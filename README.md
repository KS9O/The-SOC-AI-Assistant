# The SOC AI Assistant [v0.4]

The SOC Assistant is a high-performance desktop application designed to bridge the gap between raw security data and actionable intelligence. Built with a Tauri/Rust core and a React/TypeScript frontend, it provides SOC analysts with a local, secure environment to manage, triage, and analyze security incidents from AlienVault/LevelBlue ecosystems.

The model used within the test was the qwen3.6-27b dense model, running on an RTX 3090ti, currently running at Q4_K_M quantization, with a context window of 100k fully offloaded to the GPU. The application has custom AI prompting to help assist the AI on how to think and report. 

## Mission Statement: The Analyst Sidekick

This application was not created to replace the human element of security operations. Instead, it is built to empower the analyst. A sophisticated triage requires human intuition, experience, and the ability to correlate complex variables that an algorithm may miss.

The SOC Assistant acts as a digital companion. As the analyst performs their deep-dive triage, they hand off findings to the Assistant. The tool keeps the investigation organized, maintains a persistent triage queue, and leverages local AI to formulate professional reports, allowing the analyst to stay focused on the "how" and "why" of an attack rather than the manual labor of formatting.

![alt text](<assets/Dashboard overview.png>)

---

## Technical Breakdown: Authentication & Handshaking

One of the most complex challenges in SOC workflows is maintaining a secure connection to the security portal without manual re-authentication. The SOC Assistant uses a proprietary "Handshake" mechanism:

### 1. The Local Listener
The application initializes a secure, low-overhead HTTP listener on `127.0.0.1:12124`. This listener acts as a localized bridge between the browser and the desktop app.

### 2. Session Capturing
The "SOC Sidekick" browser extension monitors the active portal tab. Upon user request, it captures:
*   **Active JWT/Identity Tokens:** Scraped from `localStorage` and `sessionStorage`.
*   **XSRF-TOKEN:** Extracted directly from active cookies.
*   **Environment Domain:** Dynamically detected to support any AlienVault sub-domain.

### 3. Secure Handover
The extension encrypts this session state and pushes it to the local listener. The Assistant then adopts these tokens to perform background API requests on behalf of the analyst, ensuring that all data extraction is authenticated and authorized.

---

## Log Ingestion & Forensic Parsing

The Assistant features a dual-path ingestion engine designed to handle the high volume of a live SOC environment.

### [Path A] Direct URL Extraction (Sentinel Engine)
Analysts can provide a direct URL to an Alarm or an Event. The "Sentinel" engine:
1.  Analyzes the URL to determine the entity type (Alarm vs. Event).
2.  Dynamically constructs API paths (e.g., `/api/2.0/security/events/{uuid}`).
3.  Performs a multi-path discovery to locate the raw JSON logs across various platform architectures (MSSP vs. Dedicated).

### [Path B] Triage Queue Ingestion
The Triage Queue allows for asynchronous investigation. An analyst can send multiple related events to the queue while browsing the portal. These logs are stored in a persistent local state, where they can be expanded, reviewed, and selectively appended to the "AI Workbench" for cross-correlation.

---

## The SOC Sidekick: Browser Extension Workflow

The extension is the primary point of interaction during the "Active Triage" phase. It provides a native-feel integration into the browser:

*   **Native Context Menu:** Right-click any Alarm or Event ID link in the portal and select `[IN] Send to Triage Queue`. The log data is fetched and queued in the app instantly without the analyst needing to leave the browser tab.
*   **The Popup Interface:** Styled with the application's signature dark-theme aesthetic, the popup allows for:
    *   `[SYNC]` Instant session synchronization.
    *   `[WB]` Loading a specific incident directly into the primary AI Workbench.
    *   `[PIN]` The Evidence Snipper: Detects highlighted text on any webpage and pins it to the Evidence Vault as a forensic artifact.

![alt text](<assets/SOC Sidekick Extension.png>)
---

## Forensic Analysis & Reporting

![alt text](<assets/Local AI Models.png>)

Once the analyst has gathered their findings in the Assistant, the local AI engine (compatible with Ollama and OpenAI-v1 APIs) takes over the heavy lifting of documentation:

*   **Template-Driven:** Uses Tier 3 Incident Response templates (Executive, Phishing, Lateral Movement, etc.).
*   **Privacy-First:** All log analysis is processed locally. Sensitive data never leaves the analyst's machine.
*   **Automated IOC Harvesting:** The system automatically extracts IPs, Hashes, and Domains from the generated report and organizes them into a dedicated Indicators tab for easy export to blocklists or watchlists.

![alt text](<assets/Investigation 1 of 2.png>)
![alt text](<assets/investigation 2 of 2.png>)
---

**Designed for professionals. Built for speed. Focused on security.**

**Designed and Built by Kyle Sopt**
