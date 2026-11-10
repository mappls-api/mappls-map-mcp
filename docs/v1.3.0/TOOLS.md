[<img src="https://about.mappls.com/images/mappls-b-logo.svg" height="60"/>](https://www.mapmyindia.com/api)

[Home](../../README.md) · [Overview](README.md) · [Explainer](EXPLAINER.md) · [Getting Started](GETTING-STARTED.md) · [Tools](TOOLS.md) · [Use Cases](USE-CASES.md)

# Tool Reference

**The six tools, their arguments and what they return**

The six tools of the Mappls MCP Server: what each does, its arguments, and what it returns, with live examples captured from the server on 10 November 2026.

## Tools at a Glance

| Tool | It answers | The driver says |
|---|---|---|
| [`mappls_find_places`](#mappls_find_places) | Places of a kind, a brand or a name, near a spot; "which is closer" | "petrol pump near me", "pure veg restaurants with parking" |
| [`mappls_plan_route`](#mappls_plan_route) | A drive, with stops, alternatives and road preferences | "office to home via Murthal", "avoid tolls" |
| [`mappls_find_along_route`](#mappls_find_along_route) | Places on the way of a drive | "fuel station on the way to Jaipur" |
| [`mappls_trip_info`](#mappls_trip_info) | Arrival time, cost and hazards of a trip | "when will I reach?", "what will it cost?" |
| [`mappls_place_lookup`](#mappls_place_lookup) | Facts about one place: hours, contact, amenities, rating, chargers | "is the Statiq charger open?" |
| [`mappls_where_am_i`](#mappls_where_am_i) | The car's road, area, city and nearest landmark | "where am I?" |

**About the examples.** Captured on 10 November 2026 from the deployed server with the car at MapmyIndia head office, Okhla Phase 3, New Delhi (28.551574, 77.269148). Results change with place data, traffic and the time of day. The field names and shapes do not. Rows are shortened for the page.

## mappls_find_places

**Purpose:** Find places by type or name: "petrol pump", "sushi", "Amrik Sukhdev".

**Use it when** the driver wants places near a spot: a kind of place (ATM, hospital), a cuisine or dish, a brand, a named place, or a comparison ("which is closer", "how far is X"). **Do not use it** for details of one place (`mappls_place_lookup`) or for places along a drive (`mappls_find_along_route`).

| Argument | Type | Default | Meaning |
|---|---|---|---|
| `what` | string | empty | Place type, cuisine, brand or name in the driver's words. `"ATM and petrol pump"` searches both at once. Needed unless `compare` is given. |
| `near` | string | the car | A place name, an eLoc, `"lat,lng"`, `"destination"` or `"number 2"` from the last results. |
| `radius_km` | integer | about 10 | Search radius in km, maximum 50. A name search looks further (up to 50 km) and prefers the closest outlet. |
| `attributes` | list of strings | none | Must-haves, for example `["pure veg", "parking"]` or `["Type 2 plug"]`. |
| `rank` | `"nearest"` or `"fastest"` | `"nearest"` | `"fastest"` orders results by drive time and adds `eta_min` and `drive_km` to each row. |
| `compare` | list of strings | none | Used instead of `what`. Up to 5 named places, for "which is closer" or "how far is X". Returns the drive distance and minutes for each. |

**Behaviour**

- Returns up to **10 results**, nearest first (5 per kind when `what` names two kinds).
- A place written inside `what` is the search area: "restaurants in Hauz Khas" searches around Hauz Khas.
- **Attributes become Mappls filters.** Yes/no features (pure veg, parking, wifi, restroom, CCTV), EV plug type and power, and others are applied as filters. Anything that cannot be applied is listed and **said in the summary** (for example "'CCS2' was not applied as a filter"), so the list is never read out as a match. Some kinds have no filters at Mappls (petrol pump, bank, ATM, salon, mall, supermarket, police station, post office). For those, no filter is applied and the summary says so.
- Every row carries hours, phone and rating (`avgRating`, `totalReviews`) when Mappls has them. For parking, restroom, CCTV, website or reviews, the result points to `mappls_place_lookup` with `"number N"`.
- If nothing matches a name, the summary says so before showing related places.

**Returns:** `summary`, and `data.results[]` with `placeName`, `placeAddress`, `eLoc`, `distance` (metres from the search point), `hourOfOperation`, `mobileNo`, `landlineNo`, `avgRating`, `totalReviews`. EV chargers also carry `plugs`. Fastest and compare results carry `eta_min` and `drive_km`, and compare results carry `asked`. Other fields: `filters_applied`, `attributes_not_applied`, `notes`, `more`, `other_matches`.

**Example: a kind of place**

```jsonc
// call: mappls_find_places {"what": "petrol pump"}
{
  "summary": "Found 10 result(s) for 'petrol pump' near your location.",
  "data": {
    "results": [
      {"placeName": "HP Petrol Pump, Okhla Industrial Area", "placeAddress": "Okhla Industrial Estate Phase 3, New Delhi, Delhi, 110020", "eLoc": "KSNRVU", "distance": 800, "hourOfOperation": "Mon-Sun 00:00-24:00", "mobileNo": "+919811045952", "landlineNo": "+911126826650", "avgRating": 4.0, "totalReviews": 6}
    ],
    "more": "For a result's parking, restroom, CCTV, rating or website: mappls_place_lookup \"number N\"."
  }
}
```
Shortened: 1 of 10 rows.

**Example: which is closer**

```jsonc
// call: mappls_find_places {"compare": ["Blue Tokai Coffee", "Starbucks"]}
{
  "summary": "Closest by drive: Blue Tokai Coffee, AIPL Legacy, 300 metres by road, about 1 min. Then Starbucks, Nehru Place: 3.6 km by road, about 7 min.",
  "data": {
    "results": [
      {"placeName": "Blue Tokai Coffee, AIPL Legacy", "eLoc": "VSAZW3", "distance": 235, "eta_min": 1, "drive_km": 0.3, "asked": "Blue Tokai Coffee", "hourOfOperation": "Sat-Sun 08:00-20:00", "avgRating": 4.0, "totalReviews": 4},
      {"placeName": "Starbucks, Nehru Place", "eLoc": "FNOKYK", "distance": 1711, "eta_min": 7, "drive_km": 3.6, "asked": "Starbucks", "hourOfOperation": "Mon-Sun 08:00-23:15", "avgRating": 4.0, "totalReviews": 3}
    ]
  }
}
```
Shortened: address and phone fields left out.

## mappls_plan_route

**Purpose:** Plan a drive, with optional stops: "office to home via Murthal".

**Use it when** the driver wants to go somewhere: a route, a drive with stops, a choice of roads. After it, `mappls_trip_info` and `mappls_find_along_route` need no destination, because they use this trip.

| Argument | Type | Default | Meaning |
|---|---|---|---|
| `to` | string | none | The destination: a name, an eLoc, `"lat,lng"`, `"destination"` or `"number 2"`. Needed unless `route` is given. |
| `from_` | string | the car | The start, in the same forms. |
| `stops` | list of strings | none | Places to pass through, or "chai near Murthal" for a stop found near a place. |
| `profile` | `"driving"`, `"biking"`, `"trucking"` or `"walking"` | `"driving"` | The kind of vehicle or trip. |
| `include_steps` | boolean | `false` | Turn-by-turn directions in the text. Only when the user asks for them. |
| `alternatives` | boolean | `false` | Also compare up to 2 other roads (3 in all). |
| `avoid` | list of `"toll"`, `"motorway"`, `"ferry"` | none | Road types to stay off. |
| `route` | integer | none | Used instead of `to`. "Take route 2" picks one of the roads just offered, with no new search (valid 25 minutes). |
| `no_traffic` | boolean | `false` | Typical times, not live traffic. |

**Behaviour**

- Every place is resolved for you. With **more than one stop**, the stops are put in the fastest order by travel time. A stop written "X near Y" is the best match for X within 5 km of Y.
- **The summary names the place as resolved**, for example "Route to Khan Chacha, Cyber Hub Gurugram" for "Cyber Hub, Gurugram". This makes a wrong guess visible to the user.
- The trip is remembered for the session. Its id is `data.route_id`.
- Turn-by-turn steps are always in `_meta` (`mappls/route_legs`), and in the text only with `include_steps`.

**Returns:** `summary` (distance, time, arrival, main roads, tolls, and one line per alternative or stop), and `data.route_id`, `distance_km`, `duration_min`, `arrive_by`, `via[]`, `tolls`, `alternatives[]` (with `extra_min`), `legs[]` when there are stops, `notes` and `other_matches`.

**Example: a drive with alternatives**

```jsonc
// call: mappls_plan_route {"to": "Cyber Hub, Gurugram", "alternatives": true}
{
  "summary": "Route to Khan Chacha, Cyber Hub Gurugram: 28 km, about 44 min via Ho Chi Minh Marg, RTR Flyover, Delhi Gurugram Expressway. Arrive around 4:06 PM IST. Route 2: 54 min (10 min longer), 24 km via Ho Chi Minh Marg, Shri Aurobindo Marg, Mehrauli Gurugram Road. Route 3: 1 hr 2 min (18 min longer), 25 km via Guru Ravidas Marg, Mehrauli Badarpur Road, Mehrauli Gurugram Road.",
  "data": {
    "route_id": "…",
    "distance_km": 28.0,
    "duration_min": 44,
    "arrive_by": "4:06 PM IST",
    "via": ["Ho Chi Minh Marg", "RTR Flyover", "Delhi Gurugram Expressway"],
    "tolls": false,
    "alternatives": [{"route": 2, "distance_km": 24.3, "duration_min": 54, "arrive_by": "4:16 PM IST", "via": ["Ho Chi Minh Marg", "Shri Aurobindo Marg", "Mehrauli Gurugram Road"], "tolls": false, "extra_min": 10}],
    "notes": ["Route 1 is set; mappls_plan_route with route=2 takes another."]
  }
}
```
Shortened: Route 3 is left out of `data.alternatives`.

## mappls_find_along_route

**Purpose:** Find something along a drive: "fuel station on the way to Jaipur".

**Use it when** the driver wants a stop on the way: fuel, food, an ATM, a charger. It plans or reuses the route itself.

| Argument | Type | Default | Meaning |
|---|---|---|---|
| `what` | string | required | What to find, for example "fuel station" or "ATM". "Coffee or chai" searches each kind. |
| `to` | string | the trip planned last | The destination: a name, an eLoc or `"lat,lng"`. |
| `from_` | string | the car | The start, in the same forms. |
| `stops` | list of strings | none | Optional stops, in the same forms. |
| `profile` | `"driving"`, `"biking"`, `"trucking"` or `"walking"` | `"driving"` | The kind of trip. |
| `where` | `"halfway"`, `"near_origin"`, `"near_destination"` or `"whole_route"` | unset | Only if the user said which part of the route. |
| `within_km` | number | unset | Only if the user said "in the next N km". |
| `attributes` | list of strings | none | Must-haves, for example `["C2D plug"]`. Same rules as `mappls_find_places`. |

**Behaviour**

- With the car on the route and neither `where` nor `within_km` given, it searches **the next 30 km ahead of the car**. On a long route where it cannot place the car, it asks the user which part of the route they mean, instead of guessing.
- If nothing is right on the road, the corridor widens to **1,000 m** off the road, and a spelling suggestion from Mappls is tried before answering "nothing found".
- Rows are in driving order, with `km_ahead` (from the car, or from the start if there is no position) and `off_road_m`. The summary names the first.

**Returns:** `summary`, and `data.results[]` (`name`, `eLoc`, `address`, `distance`, `km_ahead`, `off_road_m`), `route_id`, `searched`, `pageInfo`, `filters_applied`, `notes` and `more`.

**Example: after planning a route, with no destination given**

```jsonc
// call: mappls_find_along_route {"what": "petrol pump"}
{
  "summary": "Found 10 result(s) for 'petrol pump' along the route. First is Bharat Petroleum Petrol Pump, in 2.1 km.",
  "data": {
    "results": [
      {"name": "Bharat Petroleum Petrol Pump", "eLoc": "BS466A", "address": "Kalkaji, New Delhi, Delhi, 110019", "distance": 1346, "km_ahead": 2.1, "off_road_m": 39},
      {"name": "Indian Oil Petrol Pump", "eLoc": "882TD5", "address": "Ground Floor, Kalkaji, Opposite Nehru Place, Bus Terminal, New Delhi, Delhi, 110019", "distance": 1387, "km_ahead": 2.1, "off_road_m": 33}
    ],
    "route_id": "…",
    "searched": "along the route",
    "pageInfo": {"pageCount": 1, "totalHits": 13, "totalPages": 2, "pageSize": 10}
  }
}
```
Shortened: 2 of 10 rows.

## mappls_trip_info

**Purpose:** Answer questions about a trip: "when will I reach", "what will it cost", "any jams or hazards".

**Use it when** the driver asks about the trip in progress or a trip they name. After `mappls_plan_route`, pass nothing and the trip planned last is used. Otherwise give `to`, and the tool plans the route itself. It reuses a route planned for the same trip in the last 10 minutes.

| Argument | Type | Default | Meaning |
|---|---|---|---|
| `route_id` | string | none | From an earlier `mappls_plan_route`. Give this, or `to`, or neither for the trip planned last. |
| `to`, `from_` | string | none, the car | A name, an eLoc or `"lat,lng"`. |
| `stops` | list of strings | none | Optional stops, in the same forms. |
| `profile` | `"driving"`, `"biking"`, `"trucking"` or `"walking"` | `"driving"` | The kind of trip. |
| `ask` | list of `"eta"`, `"cost"`, `"hazards"` | `["eta"]` | The parts to answer. "How's the drive and what will it cost" is `["eta","cost"]`. Add `"hazards"` only if the user asks about jams, accidents, safety or road conditions. |
| `vehicle_fuel_type` | `"petrol"`, `"diesel"`, `"CNG"` or `"electric"` | a guess | The driver's car, used for `"cost"`. |
| `fuel_efficiency` | number | a guess | km per litre (petrol, diesel), km per kg (CNG) or km per unit (electric). |
| `fuel_price` | number | Mappls's default | Price per litre, per kg or per unit. |

**Behaviour**

- Only the parts listed in `ask` are fetched. Cost and hazards are fetched in parallel.
- **`eta`** gives distance, drive time, arrival time, the main roads and tolls. **`cost`** gives fuel and tolls in rupees. **`hazards`** gives the reports on the route, by category.
- Fuel inputs that are not given get a default for the profile (for driving: petrol, 16 km/l). The default is **stated as a guess** in `data.notes`, never presented as measured.
- An empty hazard report is **"no data"**, not "no hazards".

**Returns:** `summary`, and `data.route_id`, `eta` (`distance_km`, `duration_min`, `arrive_by`, `via`, `tolls`), `cost` (`total_rs`, `fuel_rs`, `toll_rs`), `hazards` (`counts` by category, and `reports[]` with `category`, `description`, `address`) and `notes`.

**Example: arrival time and cost**

```jsonc
// call: mappls_trip_info {"ask": ["eta", "cost"]}
{
  "summary": "28 km, about 44 min via Ho Chi Minh Marg, RTR Flyover, Delhi Gurugram Expressway, arriving around 4:06 PM IST. Estimated trip cost about Rs 177 (fuel Rs 177, no tolls).",
  "data": {
    "route_id": "…",
    "eta": {"distance_km": 28.0, "duration_min": 44, "arrive_by": "4:06 PM IST", "via": ["Ho Chi Minh Marg", "RTR Flyover", "Delhi Gurugram Expressway"], "tolls": false},
    "cost": {"total_rs": 177, "fuel_rs": 177},
    "notes": ["Fuel type and efficiency were not supplied, so a default for profile 'driving' was used: petrol, 16 km/l. This is a guess, not a measured value. Supply your actual vehicle numbers for an accurate estimate."]
  }
}
```

**Example: hazards**

```jsonc
// call: mappls_trip_info {"ask": ["hazards"]}
{
  "summary": "32 report(s) on the route: 16 Traffic, 7 Road Condition, 9 Safety.",
  "data": {
    "route_id": "…",
    "hazards": {
      "counts": {"Traffic": {"Surveillance Camera": 12, "Traffic Monitoring Area": 4}, "Road Condition": {"Road Merging ahead": 7}, "Safety": {"Unsafe Area": 2, "Accident Prone Area": 6, "Blackspot": 1}},
      "reports": [{"category": "Traffic: Surveillance Camera", "description": "Surveillance Camera", "address": "Okhla Phase 3 Lane A,Okhla Industrial Estate Phase 3,New Delhi,Delhi,110020"}]
    }
  }
}
```
Shortened: 1 report shown.

## mappls_place_lookup

**Purpose:** Details about one place: "tell me about AIIMS", "is the Statiq charger open".

**Use it when** the driver asks about one place: whether it is open, its phone, parking, restroom, rating, or which plugs it has. It resolves the name for you.

| Argument | Type | Default | Meaning |
|---|---|---|---|
| `name` | string | required | The place's name in the driver's words, an eLoc, `"number 2"` from the last results, or `"Name (pin XXXXXX)"`. |
| `near` | string | the car | Optional bias: a place name, an eLoc or `"lat,lng"`. |
| `question` | string | none | What the driver wants to know ("is it open", "which plugs"). Only that is answered. |

**Behaviour**

- **One call answers.** The best match is taken, and a chain resolves to its **nearest outlet**. A name given as an eLoc, `"number N"` or `"Name (pin …)"` skips the search.
- **The same name elsewhere** comes back in `data.other_matches` for the assistant to judge: a famous place over a shop named after it, or the city the user named. Only a **weak match** (no candidate carries more than half the words) returns a "did you mean …?" question for the driver.
- **Every topic in the question is answered:** opening hours; contact (phone, website, email); amenities (parking, restroom, wifi, CCTV, shelter); rating ("is it good", reviews, stars); charger details (plugs, connectors, kW); and what kind of place it is. A topic the record lacks is named in `data.not_available` and in the summary ("No rating listed"), never dropped or invented. Without a `question`, the place's main facts are returned.
- A place named for the amenity, such as "Max Hospital Parking", is never told it lacks parking.

**Returns:** `summary`, and `data.name`, `address`, `eLoc`, `category`, `details` (facts by heading), `rating`, `chargers` (for EV), `distance_m`, `nearest_of` (outlets of the chain), `not_available`, `notes` and `other_matches`.

**Example: is it good, and when is it open**

```jsonc
// call: mappls_place_lookup {"name": "ICICI Bank Okhla", "question": "Is it good and what are its working hours"}
{
  "summary": "ICICI Bank: Open: Mon-Fri 09:30-15:00 ; 1st & 3rd Sat 09:30-15:00; Rating 4.0 (9 reviews).",
  "data": {
    "name": "ICICI Bank",
    "address": "254, Okhla, Phase 3, Opposite Okhla Post Office, New Delhi, Delhi, 110020",
    "eLoc": "TTO4TX",
    "category": "Bank",
    "distance_m": 21,
    "nearest_of": 2,
    "details": {"Opening Hours": {"Open": "Mon-Fri 09:30-15:00 ; 1st & 3rd Sat 09:30-15:00"}}
  }
}
```

**Example: a name that also exists elsewhere**

```jsonc
// call: mappls_place_lookup {"name": "AIIMS", "question": "is it open"}
{
  "summary": "All India Institute of Medical Sciences: Open: Mon-Fri 09:30-17:15 ; Sat 09:30-13:15 ; Sun 08:00-17:00.",
  "data": {
    "name": "All India Institute of Medical Sciences",
    "address": "Ansari Nagar, Opposite Safdarjung Hospital, Ring Road, New Delhi, Delhi, 110029",
    "eLoc": "A5E4F4",
    "category": "Medical College",
    "distance_m": 5875,
    "details": {"Opening Hours": {"Open": "Mon-Fri 09:30-17:15 ; Sat 09:30-13:15 ; Sun 08:00-17:00"}},
    "rating": "Rating 4.0 (8 reviews)",
    "other_matches": [{"said": "AIIMS", "took": {"name": "All India Institute Of Medical Sciences, New Delhi", "where": "New Delhi", "kind": "place", "km": 6}, "others": [{"name": "AIIMS Apartment", "where": "Faridabad", "kind": "area", "km": 15, "pin": "J5K9WR"}, {"name": "AIMS Golf Town", "where": "Greater Noida West", "kind": "area", "km": 18, "pin": "5VTWKW"}]}]
  }
}
```
Shortened: other fields left out.

## mappls_where_am_i

**Purpose:** "Where am I?" The road, area, city and nearest landmark as one sentence.

**Use it when** the driver asks where they are, or for the address of a point.

| Argument | Type | Default | Meaning |
|---|---|---|---|
| `location` | string | the car | `"lat,lng"` (either order is corrected), an eLoc or a place name. |

**Returns:** `summary` (one sentence), and `data.road`, `area`, `locality`, `city`, `state`, `pincode`, `landmark` and `eLoc`.

**Example**

```jsonc
// call: mappls_where_am_i {}
{
  "summary": "You're on Okhla Phase 3 Road, Okhla Industrial Estate Phase 3, New Delhi, near Centre for Genomic Application.",
  "data": {
    "road": "Okhla Phase 3 Road",
    "area": "Okhla Industrial Estate Phase 3",
    "locality": "Okhla Industrial Estate Phase 3",
    "city": "New Delhi",
    "state": "Delhi",
    "pincode": "110020",
    "landmark": "Centre for Genomic Application",
    "eLoc": "OI3W9H"
  }
}
```

---

## Which Tool for Which Request

| The user wants to... | Tool | Typical phrasing |
|---|---|---|
| Find the nearest type of place | `mappls_find_places` | "Nearest petrol pump", "gyms near me" |
| Find a branded outlet | `mappls_find_places` | "Nearest McDonald's" |
| Find places with a feature | `mappls_find_places` with `attributes` | "Pure veg restaurants with parking", "EV charger with Type 2 plug" |
| Search somewhere other than the car's position | `mappls_find_places` with `near`, or the area written inside `what` | "Sushi in Hauz Khas" |
| Sort places by drive time | `mappls_find_places` with `rank: "fastest"` | "Monuments within 10 km, sorted for a half-day trip" |
| Compare named places | `mappls_find_places` with `compare` | "Which is closer, Blue Tokai Coffee or Starbucks?" |
| Get directions | `mappls_plan_route` | "Take me to Dwarka Sector 21" |
| Plan a trip with stops, or avoid tolls | `mappls_plan_route` with `stops` or `avoid` | "Office to home via Murthal", "avoid tolls" |
| Walk, bike or drive a truck | `mappls_plan_route` with `profile` | "Walk to Lotus Temple" |
| Compare road options, or pick one | `mappls_plan_route` with `alternatives`, then `route` | "Show me other routes", "take the second route" |
| Find something on the way | `mappls_find_along_route` | "Fuel station on the way to Jaipur", "ATMs on the way" |
| Know arrival time, tolls or cost | `mappls_trip_info` with `ask: ["eta","cost"]` | "When will I reach?", "What will it cost?" |
| Know about jams, accidents, safety | `mappls_trip_info` with `ask` including `"hazards"` | "Is the route safe?" |
| Get hours, phone, rating, website, parking | `mappls_place_lookup` | "Is Dilli Haat open right now?", "Does it have parking?" |
| Know where the car is | `mappls_where_am_i` | "Where am I?" |

**A typical conversation**

| Turn | Driver says | Tool call |
|---|---|---|
| 1 | "Find a cafe" | `mappls_find_places(what="cafe")` |
| 2 | "Does the second one have parking?" | `mappls_place_lookup(name="number 2", question="does it have parking")` |
| 3 | "Take me there" | `mappls_plan_route(to="number 2")` |
| 4 | "When will I reach?" | `mappls_trip_info()` |
| 5 | "Any petrol pump on the way?" | `mappls_find_along_route(what="petrol pump")` |

---

For any queries and support, please contact:

[<img src="https://about.mappls.com/images/mappls-logo.svg" height="40"/>](https://about.mappls.com/api/)

Need support? [Contact us](https://about.mappls.com/contact/)

<div align="center">© Copyright 2026 CE Info Systems Ltd. All Rights Reserved.</div>

<div align="center">
  <a href="https://about.mappls.com/api/terms-&-conditions">Terms & Conditions</a> |
  <a href="https://about.mappls.com/about/privacy-policy">Privacy Policy</a>
</div>
