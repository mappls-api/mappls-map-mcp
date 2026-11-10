[<img src="https://about.mappls.com/images/mappls-b-logo.svg" height="60"/>](https://www.mapmyindia.com/api)

[Home](../../README.md) · [Overview](README.md) · [Explainer](EXPLAINER.md) · [Getting Started](GETTING-STARTED.md) · [Tools](TOOLS.md) · [Use Cases](USE-CASES.md)

# Use Cases

**What you can ask the Mappls MCP Server**

What the Mappls MCP Server can answer, grouped by the kind of question. Each category shows real queries and the answers the server returned. To see which tool handles a question, see [Which Tool for Which Request](TOOLS.md#which-tool-for-which-request).

The examples below are real queries and the answers the server returned, trimmed for length. Examples marked "after planning a route" assume a trip is already in progress.

**Default location:** unless a query names another place, the MCP uses the current location of the device operating the LLM. For the purpose of this document, the default location is the **MapmyIndia head office, Okhla Phase 3, New Delhi (28.551574, 77.269148)**, and every such example assumes that starting position.

## Category 1: Nearby Places

**What it covers:** everyday "what is near me" questions. The user names a type of place, and the server returns the closest matches with a distance and a locality.

**Main tool:** [`mappls_find_places`](TOOLS.md#mappls_find_places)

| Query | Answer |
|---|---|
| Are there any gyms or fitness centers near me? | Life Fitness Experience Centre is about 300 meters away in Okhla Phase 3, and Jaguar Fitness is about 700 meters away. |
| Is there a good bakery near me? | Ambrosia is nearby in Okhla Phase 3 (about 116 metres away), and Bakez by Daizy is about 200 metres away. |
| Find me a beauty salon or parlour near me | The nearest is Salon in Shyam Nagar, Okhla Industrial Estate Phase 3, about 660 metres away. There's also Ruchika Beauty Parlour in Okhla Tank around 720 metres away. |
| I am not feeling good, closest hospitals please | The closest hospital is Banarsidas Chandiwala Institute of Medical Science Hospital in Kalkaji, about 730 metres away. Shall I plan a route for you? |
| Where is the nearest petrol pump? | The nearest petrol pump is HP Petrol Pump in Okhla Industrial Area, about 722 meters away. |
| Is there a vet doctor near me who can check my dog? | Canine Care Pet Clinic is about 1.4 km away in New Friends Colony, and The Pet Clinic is roughly 1.7 km away. |
| Where is the nearest car wash? | The nearest car wash is The Detailing Mafia in Okhla Phase 3, about 160 meters away. |
| Is there a car washing centre close to the office? | Yes, there is The Detailing Mafia in Okhla Phase 3, about 258 meters away from the office. |
| Is there a place for a quick chai or coffee right as I leave the office? | There's Sonu Food Corner just 62 meters away in Okhla Phase 3, and Blue Tokai Coffee at AIPL Legacy is about 258 meters away if you're looking for specialty coffee. |

**Why it works well:** the user never has to name a category code or a coordinate. "Close to the office" and "right as I leave the office" are understood as the car's position.

## Category 2: Brands and Chains

**What it covers:** the user names a specific brand and wants the nearest outlet. Spelling mistakes in the brand name are tolerated (see also [Category 10](#category-10-hindi-hinglish-and-typo-tolerant-queries)).

**Main tool:** [`mappls_find_places`](TOOLS.md#mappls_find_places)

| Query | Answer |
|---|---|
| Where is the nearest HDFC Bank? | The nearest HDFC Bank is the Okhla Industrial Area Ph 3 Branch, about 375 meters away at 210, Okhla Industrial Area, Phase 3. |
| Where is the nearest makdonalds? | The nearest McDonald's is at New Friends Colony, about 1.2 km away in The India Mall. |
| Where is the nearest kfc chiken? | The nearest KFC is in New Friends Colony (Grandley Cinema Complex), about 1.2 km away. Would you like me to plan a route there? |
| Where is the nearest reliance freash? | The nearest is Reliance Smart in Kalkaji, located below Kalkaji Metro Station, about 950 metres away. |
| Where is the nearest appollo pharma? | The nearest Apollo Pharmacy is in Taimoor Nagar, New Friends Colony, about 2.2 km away. |
| Which bank branch is closest to me? | The closest bank branch is ICICI Bank in Okhla Phase 3, about 93 metres away. |

## Category 3: Filtered Search by Amenity or Attribute

**What it covers:** "find X that has Y". The user adds a must-have such as pure veg, home delivery, parking, a swimming pool or a plug type. The server applies it through the `attributes` field.

**Main tool:** [`mappls_find_places`](TOOLS.md#mappls_find_places)

| Query | Answer |
|---|---|
| Are there any pure veg restaurants near me? | There is Bikanervala near the NSIC Okhla Metro Station about 500 meters away, and Balaji Restaurant in Sukhdev Vihar roughly a kilometer away. |
| Are there any pure vegetarian restaurants in Hauz Khas? | Yes, there are several pure vegetarian options around Hauz Khas, including R Paratha Point in Hauz Khas, Anna South Indian Food Corner in Shahpur Jat, and Gupta Ji Ka Dhaba in Yusuf Sarai. |
| Which restaurants near me have home delivery? | Sonu Food Corner is right nearby at 110 meters, and Bikanervala is about 540 meters away. Both offer home delivery. |
| Which restaurants near me have parking? | Bikanervala in Okhla NSIC is about 540 meters away, and Biryani by Kilo in New Friends Colony is about a kilometer away. Both have parking available. |
| Are there any air-conditioned restaurants near me? | Bayleaf at The Meriton Hotel (513 m away) and Bikanervala near the NSIC Okhla Metro Station (542 m away). |
| Are there any pet friendly places near me? | There are a few pet-friendly cafes nearby, including Blue Tokai Coffee in New Friends Colony about 1.2 km away, and Horn Please Resto Cafe in Chittaranjan Park about 1.9 km away. |
| Is there a hotel with a swimming pool near me? | The closest hotel with a swimming pool is The Suryaa New Delhi in New Friends Colony, about 1.2 km away. Eros Hotel in Nehru Place is also nearby, about 1.9 km away. |
| What plug types do the EV chargers near me use? | The EV chargers near you use several plug types depending on the station, including PN3 3.3kW, IEC 3.3kW, TP1 3.68kW, and DC fast-charging plugs like C2D 30kW and C2B 60kW at the Jio BP Pulse and HP Petrol Pump stations. |
| Are there any EV chargers with a Type 2 plug near me? | There is an EESL EV Charging Station at DTC Kalkaji Terminal, about 1.4 km away, which has a Type 2 (22kW) plug. Another option nearby is the EESL station at Nehru Place, roughly 1.4 km away. |
| Does the petrol pump near me have a restroom and EV charging too? | The nearest petrol pump (HP Petrol Pump in Okhla Phase 3, about 700 metres away) features an onsite HP eCharge EV charging station. |

**How filtering works:** attributes such as "pure veg", "parking" or "Type 2 plug" are sent to Mappls as real filters. If a requested attribute cannot be applied as a filter, the answer says so, and the list is never presented as a match for it.

**Search in another area:** the user can also search away from the car by naming the area, as with Hauz Khas above, or by naming a nearby landmark.

| Query | Answer |
|---|---|
| I am in the mood for Sushi, any places in Hauz Khas? | I found a few places for sushi around Hauz Khas, including Yonaka in Shahpur Jat and Sushi Machi in Green Park. |
| It's raining really heavily, any hotels close by in CP? | There's OYO Hotel and FabHotels on the Inner Circle near Rajiv Chowk, both about 180 metres away. Slightly further in Connaught Place are Hotel Corus and Hotel Palace Heights. |

## Category 4: Place Details

**What it covers:** facts about one named place: opening hours, whether it is open today, phone numbers, ratings, websites, parking, and visiting rules.

**Main tool:** [`mappls_place_lookup`](TOOLS.md#mappls_place_lookup)

| Query | Answer |
|---|---|
| What are the timings, contact number and ratings of Humayun's Tomb? | Open Tuesday to Sunday from 09:30 AM to 04:30 PM. You can reach them at +91 11 23356080 or +91 11 24355275. It has a rating of 4.0 out of 5. |
| Is the ICICI Bank in Okhla good, and what are its working hours? | ICICI Bank: Open Monday to Friday 09:30-15:00, and 1st and 3rd Saturday 09:30-15:00. Rating 4.0 (9 reviews). |
| Is Dilli Haat open right now, and can I call ahead? | Dilli Haat INA is open today from 10:30 AM to 10:00 PM. You can call ahead at +91 11 26119055. |
| Is Red Fort open today, what's the phone number for group bookings? | Red Fort is open from 9:30 AM to 4:30 PM (closed on Mondays). You can reach them for group bookings at +91 1123277705. |
| What time is darshan at ISKCON temple? | ISKCON Temple (East of Kailash) is open daily from 4:30 AM to 1:00 PM and from 4:00 PM to 9:00 PM. |
| I need to go to Max Hospital in Saket — does it have parking? What are the visiting hours? | Max Super Speciality Hospital in Saket has parking available and is open 24/7 (Monday to Sunday). |
| Which DPS school is closest, and does it have a listed website? | The closest is Delhi Public School, Mathura Road, about 5.7 km away, and its listed website is www.dpsmathuraroad.org. |
| What is the contact number for What A Momo? | The contact numbers for What A Momo in Sukhdev Vihar are +91-9540799777 and +91-8368561204. |
| Can you give me details on the fire station near Bhikaji Cama Place? | The Bhikaji Cama Place Fire Station is located on Passport Office Road, Bhikaji Cama Place, and is open 24 hours a day, Monday through Sunday. You can reach them at +91 11 26173583. |
| What time does the National Museum open, and is it open today? | The National Museum in New Delhi is open from 10:00 AM to 6:00 PM, Tuesday through Sunday (and is closed on Mondays). |
| Is Lajpat Nagar Central Market open today? I want to go shopping. | Lajpat Nagar Central Market is open from 10:00 AM to 9:00 PM, Tuesday through Sunday (and closed on Mondays). |
| I want to visit Gurudwara Bangla Sahib — is it open 24 hours, and is there a langar listed? | Gurudwara Bangla Sahib is open 24 hours (Monday to Sunday, 00:00 to 24:00), and it features the traditional Guru Ka Langar serving free meals round the clock. |
| Find me the National Zoo — what months/seasons has different timings, and is it open today (assume Friday)? | The National Zoological Park is closed on Fridays, so it is not open today. Its timings differ by season: 9:00 AM to 4:30 PM in the summer and 9:30 AM to 4:00 PM in the winter. |
| Nearest big shopping mall — is it rated well? | The nearest shopping mall is Pacific Premium Outlets in Jasola, about 2 km away, but it has a low rating of 2 out of 5 based on limited reviews. |
| Any PVR cinema close by, can I check reviews before booking? | The closest options are PVR INOX at Epicuria, Nehru Place (about 1.7 km away) and PVR INOX at Nehru Place Market, which has a 4-star rating with 12 reviews. |

**What this shows:** the server handles the day-of-the-week logic ("open today", "closed on Mondays"), seasonal timings, multiple phone numbers, ratings with review counts, and listed websites.

## Category 5: Finding and Identifying a Place

**What it covers:** "Where is X?" questions about landmarks, localities, markets, stations and cities. The answer gives the area and, where useful, the distance from the user.

**Main tools:** [`mappls_place_lookup`](TOOLS.md#mappls_place_lookup) and [`mappls_find_places`](TOOLS.md#mappls_find_places)

| Query | Answer |
|---|---|
| Where is chandni chwok? | Chandni Chowk is located in Central-North Delhi, about 12.3 km away from your current location. |
| Where is rohini sec 3? | Rohini Sector 3 is located in North West New Delhi, approximately 23 kilometers away from your current location. |
| Where is connaught plce? | Connaught Place is a major commercial and financial hub located in central New Delhi (110001), about 10 km from your current location. |
| Where is saket disstrict centre in delhi? | Saket District Centre is located on Shaheed Pankaj Juyal Marg in Sector 6, Saket, New Delhi. |
| Where is karol bagh metero station? | Karol Bagh Metro Station is located in Karol Bagh, New Delhi (PIN 110060). It is about 13 km away from your current location. |
| Where is dilli haat? | Dilli Haat INA is located on Sri Aurobindo Marg near the INA Metro Station in New Delhi, about 6.5 km away. |
| Where is akshardham mandir? | Akshardham Temple is located near Nizamuddin Bridge, Akshardham Setu, on Pramukh Swami Maharaj Marg in New Delhi, about 6.9 km away. |
| Where is chandigah? | Chandigarh is located about 248 km away to the north. |
| Where is ambiance mall? | There are two main locations: Ambience Mall in Vasant Kunj, New Delhi (about 11 km away) and Ambience Mall in Gurugram, Haryana (about 17.5 km away). Which one would you like to head to? |
| Is there more than one Akshardham temple? Which one's in Delhi? | Yes, there are a few prominent Akshardham temples, most notably the grand one in New Delhi near the Nizamuddin Bridge, and another major one in Gandhinagar, Gujarat. |

**Ambiguity handling:** when a name matches more than one place, the server does not guess silently. It either lists the options and asks which one is meant, or takes the most likely match and also returns the others (`other_matches`, each with a pin), so the assistant can pick the right one. For example, "AIIMS" resolves to the All India Institute of Medical Sciences in New Delhi, and the lesser matches (AIIMS Apartment in Faridabad, AIMS Golf Town in Greater Noida West) come back alongside it.

## Category 6: Driving and Walking Directions

**What it covers:** "How do I get to X?" The answer gives distance, time, the main roads, and often an expected arrival time. The server also handles avoid-tolls, alternatives, walking and round trips.

**Main tool:** [`mappls_plan_route`](TOOLS.md#mappls_plan_route)

| Query | Answer |
|---|---|
| Take me to Dwarka sec 21 | I've planned a route to Dwarka Sector 21. It's about 26 kilometers and will take around 50 minutes via Ho Chi Minh Marg, RTR Flyover, and Dwarka Road. Shall we start? |
| How do I get to hummayun tomb? | It's about 6.4 km to Humayun's Tomb, and should take around 24 minutes via Modi Mill Flyover, Mathura Road, and Ashram Underpass. |
| What is the route to Select Citywalk in Saket? | About 7.0 km and around 21 minutes via Bhakti Vedant Swami Marg, Ho Chi Minh Marg, and Pt Trilok Chand Sharma Marg. You should arrive around 3:32 PM. |
| Show me the way to Pitampura by car | About 1 hour 17 minutes for the 29 km drive via Captain Gaur Marg, Ring Road, and Guru Harikishan Marg, with an estimated arrival time of 4:26 PM. |
| I'm planning a day trip to the Taj Mahal, how do I get there from here? | About 212 km and around 3 hours and 16 minutes via the Greater Noida and Yamuna Expressways. Expect tolls along the way. |
| How do I drive to Cyber Hub in Gurugram without paying tolls? | About 24 km and around 1 hour and 4 minutes via Ho Chi Minh Marg, Shri Aurobindo Marg, and Mehrauli-Gurugram Road, with no tolls along this route. |
| Take me to Cyber Hub, Gurugram, and show me other routes too | Route to Khan Chacha, Cyber Hub Gurugram: 28 km, about 44 min via Ho Chi Minh Marg, RTR Flyover, Delhi Gurugram Expressway. Arrive around 4:06 PM IST. Route 2: 54 min (10 min longer), 24 km via Ho Chi Minh Marg, Shri Aurobindo Marg, Mehrauli Gurugram Road. Route 3: 1 hr 2 min (18 min longer), 25 km via Guru Ravidas Marg, Mehrauli Badarpur Road, Mehrauli Gurugram Road. |
| I want to walk to Lotus Temple and come back to the office, what's the route? | Walking from the office to the Lotus Temple and back is about 3.8 km and takes around 47 minutes via Okhla Phase 3 Lane A, Bhakti Vedant Swami Marg, and Bahai Temple Road. |

**What this shows:** the server names the place it resolved ("Khan Chacha, Cyber Hub Gurugram" for "Cyber Hub, Gurugram") so a wrong guess is visible; disambiguation of a place name that exists in several cities (Dwarka Sector 21 resolved to Delhi), the toll-avoid option, and walking mode with a return leg. The `alternatives` and `no_traffic` options are also supported by the tool.

## Category 7: Stops Along the Route

**What it covers:** finding fuel, food, coffee or other services on the way to a destination, not only at the start or end.

**Main tool:** [`mappls_find_along_route`](TOOLS.md#mappls_find_along_route)

| Query | Answer |
|---|---|
| Are there any petrol pumps on the way to Connaught Place? | Yes, there are several petrol pumps on the way to Connaught Place. The closest is the Indian Oil Petrol Pump on Mathura Road, about 2.2 km ahead. |
| Is there a coffee shop on the way to Connaught Place where I can grab a quick cup? | You can grab coffee at Blue Tokai Coffee in AIPL Legacy, which is right at the start of your route in Okhla Phase 3. |
| On the Manali trip, where can I stop for fuel on the first stretch? | For the start of your trip, there is an HP Petrol Pump in Okhla Industrial Area (0.1 km ahead) and an Indian Oil Petrol Pump near Friends Colony (2.2 km ahead) right along Mathura Road. |
| Drive me to Red Fort — how's the traffic, any fuel cost estimate for a diesel car, and petrol stations on the way? | About 16 km and 47 minutes via Modi Mill Flyover, Mathura Road, and Ring Road. Traffic is generally smooth with no tolls, and the estimated diesel fuel cost is around ₹98. Several petrol stations are listed along the way. |

The last example also shows the follow-on behaviour: the question refers to "the Manali trip", so no destination is given and the earlier trip is reused.

## Category 8: Trip Cost and Travel Time

**What it covers:** how long a trip will take, when the user will arrive, and what it will cost in fuel and tolls.

**Main tool:** [`mappls_trip_info`](TOOLS.md#mappls_trip_info) (with `ask` set to `eta` and/or `cost`)

| Query | Answer |
|---|---|
| How much will fuel cost me to drive to Connaught Place? | Driving to Connaught Place will cost about ₹75 in fuel, assuming petrol at 16 km/l. |
| What will the total toll and fuel cost be for a trip to the Taj Mahal? | The estimated cost is ₹1,827, which includes ₹485 for 3 toll plazas (Jewar, Mathura, and Agra) and ₹1,342 for fuel (calculated using a default efficiency of 16 km/l for petrol). |
| I'm planning a weekend trip to Jaipur, how's the drive, and what will it cost? | About 292 km and 4 hours 12 minutes via the Delhi Mumbai Expressway and Jaipur Bandikui Expressway. Estimated total trip cost is ₹2,426 (₹580 for 5 tolls and about ₹1,846 for fuel). |
| When will I reach, and what will it cost? (after planning a route to Cyber Hub, Gurugram) | 28 km, about 44 min via Ho Chi Minh Marg, RTR Flyover, Delhi Gurugram Expressway, arriving around 4:06 PM IST. Estimated trip cost about Rs 177 (fuel Rs 177, no tolls). |
| Thinking of a Rishikesh trip for river rafting, how's the drive? | About 245 km and 4 hours 25 minutes via the Delhi Dehradun Expressway, Roorkee Road, and Delhi Mana Road, with tolls along the way. |
| Road trip to Manali — how many hours, and is the route safe? | About 532 km and roughly 10 hours 11 minutes via the Delhi-Dehradun Expressway and NH44. |

**Assumptions are stated:** when the user does not give a vehicle, the answer says which default it used (for example "petrol at 16 km/l"). The user can then correct it.

## Category 9: Road Safety and Hazards

**What it covers:** accident-prone areas, blackspots, police checkposts, speed or surveillance cameras and bad road conditions along a route.

**Main tool:** [`mappls_trip_info`](TOOLS.md#mappls_trip_info) (with `ask` including `hazards`)

| Query | Answer |
|---|---|
| Is the road to Connaught Place safe right now — any accident spots or checkposts I should know about? | On the way to Connaught Place, there are 2 police checkposts, 6 accident-prone areas, 2 blackspots, and 8 surveillance cameras reported along the route. Drive carefully! |
| Is the route to Connaught Place safe? Are there any bad roads? | The route has a few safety reports and bad road conditions to watch out for, including reported potholes, an accident-prone area near the start, and police checkposts along the way. |
| Is the route to Select Citywalk in Saket safe? | The 7 km route takes about 21 minutes. There are a few safety advisories along the way, including an accident-prone area near Okhla Phase 3 and Kalkaji, as well as a road safety blackspot near Jahanpanah City Forest. |
| Any jams, accidents or hazards on this route? (after planning a route) | 32 report(s) on the route: 16 Traffic, 7 Road Condition, 9 Safety. |
| Road trip to Manali — is the route safe? | The route has several reported accident-prone areas, blackspots, and speed cameras, so drive carefully especially through the hill sections. |

An empty hazard report is reported as "no data", not as "no hazards", so a silent result is never read as a safety guarantee. Hazards are only added when the user asks about safety, jams, accidents or road conditions. Ordinary route questions stay short.

## Category 10: Hindi, Hinglish and Typo-Tolerant Queries

**What it covers:** queries written in Hindi, in romanised Hindi (Hinglish), or with spelling mistakes in place and brand names. The server finds the right place, and the reply is in the user's language.

**Main tools:** [`mappls_find_places`](TOOLS.md#mappls_find_places), [`mappls_place_lookup`](TOOLS.md#mappls_place_lookup), [`mappls_plan_route`](TOOLS.md#mappls_plan_route)

| Query | Answer |
|---|---|
| Humein thoda aage ek dhaba dikha do | Aapke paas Okhla Phase 3 mein 'Malik Dhaba' hai, jo yahan se lagbhag 300 metre door hai. Aur bhi kuch food corners available hain, jaise Eatery Sall aur Maggi Corner. |
| Bacho ka school kahan hai yahan nearby? | Yahan paas mein Government Co Ed Senior Secondary School Nehru Nagar mein hai, jo kareeb 3 kilometer ki doori par hai. |
| Meri gadi ki service karani hai, kahan karwau? | Okhla Phase 3 mein paas hi kai car service centers hain, jaise ki Brite Nissan (127m door) aur Deep Motors (220m door). |
| Mujhe nagdeek ka petrl pmp dikhao | Sabse paas HP Petrol Pump hai, jo Okhla Industrial Area Phase 3 mein sirf 722 meter door hai. |

**Spelling mistakes** are handled across the other categories too. Examples: "makdonalds", "kfc chiken", "reliance freash", "appollo pharma", "hummayun tomb", "chandni chwok", "connaught plce", "karol bagh metero station", "petrl pmp".

**Intent from loose phrasing:** "Meri gadi ki service karani hai" (I need to get my car serviced) is mapped to car service centres, and "bacho ka school" (children's school) to a school. The user does not need to use a category word.

## Category 11: Multi-Part and Comparison Questions

**What it covers:** one message that asks for several things, or that compares places. The server returns one combined answer.

**Main tools:** a mix, chosen by the server. `compare` and `rank` are used for comparison and sorting.

| Query | Answer |
|---|---|
| Book me into a 5-star hotel nearby — does it have a pool, is it pet friendly, what time is check-in? | The Suryaa New Delhi is about 1.2 km away. It has a swimming pool, but it is not pet-friendly. Check-in information isn't available, but you can call them at +91 11 26835070. |
| Which is closer, Blue Tokai Coffee or Starbucks? | Closest by drive: Blue Tokai Coffee, AIPL Legacy, 300 metres by road, about 1 min. Then Starbucks, Nehru Place: 3.6 km by road, about 7 min. |
| Compare hotel amenities — does The Suryaa have the same facilities as Taj Palace? | Both hotels share most amenities like Wi-Fi, swimming pool, spa, parking, and restaurants with bar facilities, but Taj Palace also lists group-friendly amenities. |
| Find me nearby historical monuments and museums within 10km, sorted for a half-day trip | Sorted by drive time: 1. Dr. Zakir Husain Memorial, Jamia Millia Islamia (6 min, about 3 km); 2. Kos Minar, Jasola District Centre (8 min, about 4.5 km); 3. Bahlol Lodi Tomb, Chirag Delhi (10 min, about 5.7 km), and more. |
| Weekend trip to Jaipur — how's the drive, and what will it cost? | Drive time, distance, tolls and fuel are given in one reply (see [Category 8](#category-8-trip-cost-and-travel-time)). |
| Drive me to Red Fort — how's the traffic, any fuel cost estimate for a diesel car, and petrol stations on the way? | Route, traffic, diesel fuel cost and petrol stations returned together (see [Category 7](#category-7-stops-along-the-route)). |
| Road trip to Manali — how many hours, and is the route safe? | Time, distance and route hazards returned together (see [Category 9](#category-9-road-safety-and-hazards)). |

## Category 12: Knowing Where You Are

**What it covers:** the driver asks where they are, or for the address of a point. The answer is one sentence with the road, area, city and nearest landmark.

**Main tool:** [`mappls_where_am_i`](TOOLS.md#mappls_where_am_i)

| Query | Answer |
|---|---|
| Where am I? | You're on Okhla Phase 3 Road, Okhla Industrial Estate Phase 3, New Delhi, near Centre for Genomic Application. |

The structured result also carries the road, area, locality, city, state, pincode (110020 here), nearest landmark and eLoc. The tool also accepts a `"lat,lng"` pair, an eLoc or a place name, so it can describe a point other than the car's.

## Category 13: Honest Answers When Data Is Missing

**What it covers:** when the listing has no data for a field, the server says so clearly and does not invent an answer. In several cases it adds the next best step.

| Query | Answer |
|---|---|
| Does Qutub Minar have an entry fee listed? What are the visiting hours? | Qutub Minar is open Monday to Sunday from 7:00 AM to 6:00 PM. An entry fee is not currently listed in the available details. |
| Does the nearest cafe have a washroom? | The nearest cafe is Blue Tokai Coffee at AIPL Legacy (about 240 meters away), but detailed washroom availability isn't listed for it. |
| Is there a bigbasket or grofers store near me? | There are no retail Bigbasket or Grofers (Blinkit) stores right near you, only corporate offices and distribution hubs further away. |
| Are there any wheelchair accessible restaurants near me? | I couldn't find any wheelchair accessible restaurants near your current location. Would you like to search in a specific area instead? |
| Five-star hotel: what time is check-in? | Check-in information isn't available, but you can call them at +91 11 26835070 to book and check their timing. |

For hazard questions, the server words an empty result carefully: "no hazard reports... this means no data is currently reported, not a guarantee that it is clear". This stops an empty report from being read as a safety guarantee.

---

For any queries and support, please contact:

[<img src="https://about.mappls.com/images/mappls-logo.svg" height="40"/>](https://about.mappls.com/api/)

Need support? [Contact us](https://about.mappls.com/contact/)

<div align="center">© Copyright 2026 CE Info Systems Ltd. All Rights Reserved.</div>

<div align="center">
  <a href="https://about.mappls.com/api/terms-&-conditions">Terms & Conditions</a> |
  <a href="https://about.mappls.com/about/privacy-policy">Privacy Policy</a>
</div>
