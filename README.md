# Web-Scraping-Rera

## 1. Overview
Web-Scraping-Rera is an automated Python-based data extraction tool designed to scrape real estate project details from the Maharashtra RERA (Real Estate Regulatory Authority) portal.  
The script navigates through multiple pages of search results, solves captchas using OCR, and extracts detailed project information from each project page. The collected data is saved into a CSV file for further analysis or integration with other systems.

This project is especially useful for real estate analysts, researchers, and data-driven businesses who need structured RERA data at scale.

```
Note: I've done this for the whole web site, but it takes a lot of time fetching the website, processing the captcha and formatting the csv. So I continued with a specific construction group(raheja).
```
---

## 2. Key Features
- Automated scraping of multiple paginated RERA project listings.
- Handles dynamic content rendering using Playwright (headless Chromium browser).
- Captcha solving using OCR with Tesseract and Pillow.
- Extracts structured project metadata, including:
  - Registration Number
  - Date of Registration
  - Project Status
  - Project Name
  - Project Type
  - Project Location
  - Proposed Completion Dates (Original and Revised)
  - Planning Authority and Full Name
  - Total Land Area
  - Project Address
  - District, Taluka, Village, Pin Code
  - Latitude and Longitude
  - Promoter Name, Type, and Address
  - Bank IFSC Code
  - Permissible Built-up Area
- Writes extracted data into a structured CSV file.
- Resumes scraping from the last saved progress to handle interruptions.

---

## 3. Project Structure

```
├── web_scraping_rera.ipynb      # Main notebook containing scraping logic
├── raheja_progress.txt        # Tracks progress of last scraped index
├── raheja.csv                  # Output file storing scraped data
└── README.md                   # Project documentation
```
Only the web_scraping_rera.ipynb file is needed to run the project.

## 4. Technical Approach

### Problem 1: Captcha on the RERA Website

`Solution:`
```
Inspect the web page using developer tools to locate the captcha within `<div>` tags.
Captcha is rendered as a <canvas> element.
Extract the canvas image and apply OCR to read it.
```

### Problem 2: OCR Accuracy Issues
  
`Issue:`
```
OCR struggles to distinguish between characters like 1 and I, or 0 and O.
```
`Solution:`
```
Since the likelihood of both similar-looking characters appearing together is low, run multiple attempts (3–10) and retry until OCR produces a valid captcha result.
Fewer attempts reduce time but also reduce the number of successful fetches.
```

### Problem 3: Website Blocking Bot Behavior

`Issue:`
```
Even with correct captcha input, the website may reject automated submissions due to non-human-like interaction speed.
```
`Solution:`
```
Simulate human-like behavior by introducing delays between keystrokes while entering captcha text (e.g., 100ms per character).
Use random delays to further reduce detection likelihood.
```

### Problem 4: Large Volume of Records

`Issue:`
```
The pipeline is long and prone to failure, and any error can reset the entire process.
```
`Solution:`
```
Maintain a progress tracker (raheja_progress.txt) to record the last successfully scraped index and resume from there on subsequent runs.
```

## 5. Limitations and Future Improvements
```
OCR accuracy depends on captcha image clarity and may fail occasionally.
Parallelization (multi-tab or multi-threaded scraping) is not yet implemented.
Currently configured to scrape only projects by Raheja. This can be extended to allow user-defined search keywords to scrape all builders.
Output is currently limited to CSV; support for database (SQL/NoSQL) export can be added in the future.
```
## 12. Security Recommendations for Site Owners

To protect the MahaRERA portal (or any similar public-facing platform) from automated scraping and abusive requests, it is recommended to implement an additional security layer based on **IP tracking and rate limiting**.

### Proposed Measures

1. **Track Request Frequency by IP**
   - Maintain a rolling log of incoming requests grouped by client IP address.
   - Track request counts within short time windows (e.g., per minute, per 5 minutes).

2. **Apply Rate Limiting Rules**
   - If a single IP exceeds a predefined threshold (e.g., more than 50 requests per minute), temporarily block or throttle further requests from that IP.
   - Use HTTP 429 (Too Many Requests) responses for clients that hit the limit.

3. **Implement Temporary IP Blocking**
   - For repeated violations, block the offending IP for a longer duration (e.g., 1 hour or 24 hours).
   - Store blocked IPs in a cache or firewall rule set.

4. **Add Behavior-Based Detection**
   - Detect suspicious patterns such as:
     - Very short intervals between consecutive requests
     - Accessing many paginated pages in sequence without delay
   - Flag these sessions for additional captcha verification or blocking.

5. **Integrate a Web Application Firewall (WAF)**
   - Use a WAF to automatically enforce IP-based throttling rules.
   - Combine it with existing captcha-based validation for a layered security approach.

### Benefits

- Significantly reduces the risk of large-scale automated scraping.
- Prevents server overload from abusive request patterns.
- Protects the integrity of the platform’s data and service availability.

**Note:** This recommendation is intended as a **second layer of security** to supplement captcha-based protection, not replace it.

## 6. License
```
This project is intended solely for educational and research purposes.
Ensure compliance with the MahaRERA website’s Terms of Use before deploying or redistributing this tool.
```
