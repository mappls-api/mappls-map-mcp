[<img src="https://about.mappls.com/images/mappls-b-logo.svg" height="60"/>](https://www.mapmyindia.com/api)

# Mappls MCP Server

**Location Intelligence for AI Assistants**

Powered by India's most comprehensive mapping and location intelligence platform — Mappls.

The Mappls MCP Server connects an AI assistant to MapmyIndia's location data over the Model Context Protocol (MCP). It gives the assistant **six tools that each answer a driver's question in one call**: finding places, planning drives, finding stops along the way, estimating a trip's time, cost and hazards, looking up one place, and saying where the car is. Users can ask in English, Hindi or Hinglish, and the answers come back as short, conversational replies.

---

## [Documentation History]()

| Version | Release Date | Notes |
|---|---|---|
| [v1.3.0](README.md) | Latest | Documentation dated 10 November 2026 |

---

## [Table of Contents]()

- [Overview](README.md)
- [MCP vs LLM: Understanding the Difference](EXPLAINER.md)
- [Getting Started](GETTING-STARTED.md)

### MCP vs LLM
- [The Basics: LLM, MCP, Client and Server](EXPLAINER.md#what-is-an-llm)
- [How a Query Flows Through the System](EXPLAINER.md#how-a-query-flows-through-the-system)
- [What an MCP Server Is Not](EXPLAINER.md#what-an-mcp-server-is-not)
- [Common Questions](EXPLAINER.md#common-questions)

### Connecting a Client
- [Connection Details](GETTING-STARTED.md#connection-details)
- [Quick Verification (curl)](GETTING-STARTED.md#quick-verification-curl)
- [Session Management](GETTING-STARTED.md#session-management)
- [Troubleshooting](GETTING-STARTED.md#troubleshooting)
- [Client Documentation](GETTING-STARTED.md#client-documentation)
- [Client Setup](GETTING-STARTED.md#client-setup): [Claude Desktop](GETTING-STARTED.md#claude-desktop) · [Claude Code](GETTING-STARTED.md#claude-code-cli) · [Cursor](GETTING-STARTED.md#cursor) · [Google Antigravity](GETTING-STARTED.md#google-antigravity) · [Any other client](GETTING-STARTED.md#any-other-mcp-client)

### MCP Architecture
- [Architectural Diagram: Mappls Map MCP for Automotive](ARCHITECTURE-AUTOMOTIVE.md)
- [Architectural Diagram: Mappls Map MCP for Generic](ARCHITECTURE-GENERIC.md)

### Tool Reference
- [mappls_find_places](TOOLS.md#mappls_find_places)
- [mappls_plan_route](TOOLS.md#mappls_plan_route)
- [mappls_find_along_route](TOOLS.md#mappls_find_along_route)
- [mappls_trip_info](TOOLS.md#mappls_trip_info)
- [mappls_place_lookup](TOOLS.md#mappls_place_lookup)
- [mappls_where_am_i](TOOLS.md#mappls_where_am_i)
- [Which Tool for Which Request](TOOLS.md#which-tool-for-which-request)

### Use Cases
- [Category 1: Nearby Places](USE-CASES.md#category-1-nearby-places)
- [Category 2: Brands and Chains](USE-CASES.md#category-2-brands-and-chains)
- [Category 3: Filtered Search by Amenity or Attribute](USE-CASES.md#category-3-filtered-search-by-amenity-or-attribute)
- [Category 4: Place Details](USE-CASES.md#category-4-place-details)
- [Category 5: Finding and Identifying a Place](USE-CASES.md#category-5-finding-and-identifying-a-place)
- [Category 6: Driving and Walking Directions](USE-CASES.md#category-6-driving-and-walking-directions)
- [Category 7: Stops Along the Route](USE-CASES.md#category-7-stops-along-the-route)
- [Category 8: Trip Cost and Travel Time](USE-CASES.md#category-8-trip-cost-and-travel-time)
- [Category 9: Road Safety and Hazards](USE-CASES.md#category-9-road-safety-and-hazards)
- [Category 10: Hindi, Hinglish and Typo-Tolerant Queries](USE-CASES.md#category-10-hindi-hinglish-and-typo-tolerant-queries)
- [Category 11: Multi-Part and Comparison Questions](USE-CASES.md#category-11-multi-part-and-comparison-questions)
- [Category 12: Knowing Where You Are](USE-CASES.md#category-12-knowing-where-you-are)
- [Category 13: Honest Answers When Data Is Missing](USE-CASES.md#category-13-honest-answers-when-data-is-missing)

---
## License & AI Usage Policy

This repository is governed by the [Mappls MCP Server License](../../LICENSE.md):
* **Sample Configurations & Examples:** Licensed under the [MIT License](../../LICENSE.md#3-sample-code-and-quickstarts).
* **MCP Server Software & Schemas:** Proprietary to CE Info Systems Limited (“MapmyIndia”). Free for local evaluation and testing; **requires an active commercial subscription and credentials for production deployment**.
* **AI Training & Scraping Restrictions:** Strict prohibitions apply against caching responses in vector databases, distilling outputs, or training/fine-tuning AI models on Mappls geospatial data. See [Section 2 of LICENSE.md](../../LICENSE.md#2-ai-model-training-caching-distillation--anti-scraping-restrictions).

For commercial licensing and enterprise API keys, contact [contact@mappls.com](mailto:contact@mappls.com).

---

For any queries and support, please contact:

[<img src="https://about.mappls.com/images/mappls-logo.svg" height="40"/>](https://about.mappls.com/api/)

Need support? [Contact us](https://about.mappls.com/contact/)

<div align="center">© Copyright 2026 CE Info Systems Ltd. All Rights Reserved.</div>

<div align="center">
  <a href="https://about.mappls.com/api/terms-&-conditions">Terms & Conditions</a> |
  <a href="https://about.mappls.com/about/privacy-policy">Privacy Policy</a>
</div>
