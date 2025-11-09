# 🌍 RestCountries JMeter API Testing Project

This repository contains a **complete Apache JMeter API testing project** built using the free [RestCountries API](https://restcountries.com/).  
It demonstrates all major **JMeter concepts** — including **functional testing**, **parameterization**, **correlation**, **assertions**, and **performance validation** — all in a single, easy-to-run project.

---

## 🚀 Project Overview

The **RestCountries API** provides information about countries such as their name, code, region, capital, and more.  
This project uses the API to simulate real-world API testing workflows and validate data consistency across endpoints.

**Main API Endpoints Tested:**

| Endpoint | Description |
|-----------|--------------|
| `GET /v3.1/name/{countryName}` | Fetch country details by name |
| `GET /v3.1/alpha/{countryCode}` | Fetch country details by ISO 2-letter code |
| `GET /v3.1/all?fields=name,cca2,region,subregion,capital` | Fetch all countries with selected fields |

---

## 🧠 JMeter Concepts Covered

✅ HTTP Samplers  
✅ User Defined Variables  
✅ CSV Data Set Config (Data-driven testing)  
✅ Correlation using JSON Extractor  
✅ JSR223 (Groovy) Assertions  
✅ Response & Duration Assertions  
✅ Modular Thread Groups  
✅ Header Management  
✅ Listeners (Results Tree & Summary Report)

---

## 📁 Project Structure

restcountries-jmeter-testing/
│
├── restcountries_test_plan.jmx       # Main JMeter test plan
├── countries_data.csv                # CSV file with country name & code
│
├── /results/                         # (optional) stores reports
│   └── summary_report.jtl
│
└── /screenshots/                     # (optional) UI snapshots
├── view_results_tree.png
├── summary_report.png

---

## ⚙️ Setup Instructions

### 1️⃣ Prerequisites
- Apache JMeter **5.6+**
- Java **8 or higher**
- Internet connection (for calling API)
- Optional: JMeter Plugins Manager (for advanced assertions)

---

### 2️⃣ Clone the Repository

git clone https://github.com/<your-username>/restcountries-jmeter-testing.git
cd restcountries-jmeter-testing


⸻

3️⃣ Open in JMeter
	1.	Launch JMeter GUI
	2.	Open restcountries_test_plan.jmx
	3.	Ensure the CSV Data Set Config path points to countries_data.csv
	4.	Hit Start ▶️

⸻

🧩 Test Plan Structure

1. User Defined Variables

Variable	Example Value	Description
basePath	https://restcountries.com/v3.1	Base URL for API endpoints
thinkTimeMs	1000	Think time between requests


⸻

2. CSV Data Set Config

Column	Example Value
countryName	India
countryCode	IN

This allows data-driven testing for multiple countries.

⸻

3. HTTP Header Manager

Header	Value
Accept	application/json

Ensures the API returns JSON responses.

⸻

4. Thread Group – Functional

(a) GET Country By Name
	•	Method: GET
	•	Path: ${basePath}/name/${countryName}
	•	Extract: Country Code (cca2) using JSON Extractor
	•	JSON Path: $.[?(@.name.common=="${countryName}")].cca2
	•	Variable: extractedCode

(b) GET Country By Code
	•	Method: GET
	•	Path: ${basePath}/alpha/${__trim(${extractedCode})}
	•	Assertions:
	•	Response Assertion → JSON $.cca2 == ${extractedCode}
	•	Duration Assertion → Max: 2000 ms

(c) GET All Countries
	•	Method: GET
	•	Path: ${basePath}/all?fields=name,cca2,region,subregion,capital
	•	Assertions:
	•	JSON Assertion → $[0].name.common exists
	•	JSR223 Assertion (Groovy):

import groovy.json.JsonSlurper
def json = new groovy.json.JsonSlurper().parseText(prev.getResponseDataAsString())
int count = json.size()
if (count <= 150) {
    AssertionResult.setFailure(true)
    AssertionResult.setFailureMessage("Expected >150 countries, got ${count}")
}

⸻

5. Listeners
	•	View Results Tree → Detailed request & response view
	•	Summary Report → Performance metrics (min, max, avg time, throughput)

⸻

🧩 Correlation Explained

The test dynamically extracts the country code from the first request (GET by name) and passes it to the second request (GET by code).
This ensures you’re testing real API chaining — where data from one API response drives the next call.

Example Flow:

GET /v3.1/name/India → returns "cca2": "IN"
→ Next Request: GET /v3.1/alpha/IN


⸻

📊 Assertions Implemented

Assertion Type	Purpose
Response Assertion	Validates JSON field values
JSON Assertion	Ensures key fields exist
Duration Assertion	Checks API performance
JSR223 Assertion	Custom logic for response size


⸻

📈 Expected Results

Request	Expected Response Code	Assertions
GET Country by Name	200	JSON field cca2 extracted
GET Country by Code	200	Code matches extracted one
GET All Countries	200	JSON size > 150; response < 2000ms


🔍 Example Output

Summary Report View:

Label	#Samples	Avg (ms)	Min	Max	Error %
GET Country By Name	3	400	300	600	0%
GET Country By Code	3	350	280	420	0%
GET All Countries	1	1100	1050	1150	0%


⸻

🧰 Tools & Versions
	•	Apache JMeter 5.6.2
	•	Groovy (JSR223)
	•	RestCountries API (no rate limits)

⸻

⭐ If you found this helpful, don’t forget to star the repository!

---
