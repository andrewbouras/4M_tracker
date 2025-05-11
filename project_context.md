# Project Context: Puppeteer Token Monitor & Scraper

This project consists of a Node.js script (`index.js`) that uses Puppeteer for web automation to monitor cryptocurrency token data and scrape for new tokens. It also includes a persistent browser profile (`puppeteer_profile`).

## Components:

### 1. `index.js` (Main Script)

*   **Purpose:** Automates cryptocurrency token monitoring and discovery.
*   **Core Technologies:** Node.js, Puppeteer, Puppeteer-Extra (with Stealth Plugin), Express.js.
*   **Key Functionalities:**
    *   **Token Monitoring:**
        *   Watches specified tokens on `photon-sol.tinyastro.io`.
        *   Tracks RSI (Relative Strength Index) and Market Cap.
        *   Attempts to automatically enable/fix RSI indicators on charts.
        *   Includes logic for identifying trading signals (details in script).
        *   Manages a queue for tokens if monitoring capacity is full.
        *   Blacklists problematic tokens.
    *   **Pump Token Scraping:**
        *   Periodically scrapes `solscan.io` for new tokens associated with a specific program address that end with "pump".
    *   **API Server:**
        *   Runs on `http://localhost:3000`.
        *   `POST /watch-token`: Accepts `{"mintAddress": "..."}` to add a token for monitoring.
        *   `POST /scrape-pump-tokens`: Manually triggers Solscan scraping.
    *   **Stealth & Profile:**
        *   Uses stealth plugins to reduce bot detection.
        *   Utilizes a persistent browser profile (`./puppeteer_profile`) for session continuity.
        *   Attempts to load a Phantom wallet extension from `./extensions/phantom/25.11.0_0`.
    *   **Logging:** Creates detailed logs in the `./logs` directory for errors, queue status, monitoring, scraping, signals, and blacklisted tokens.
    *   **User Interaction:**
        *   Requires an initial manual sign-in and setup on `photon-sol.tinyastro.io`.
        *   Provides a terminal-based command menu for basic operations.

### 2. `puppeteer_profile` (Directory)

*   **Purpose:** Stores persistent browser data (cookies, local storage, cache, etc.) for the Puppeteer-controlled Chrome instance.
*   **Benefit:** Helps maintain login sessions, website preferences, and can contribute to appearing less like a bot.

## How to Run:

1.  **Prerequisites:**
    *   Install Node.js and npm (from [nodejs.org](https://nodejs.org/)).

2.  **Install Dependencies:**
    *   Navigate to the project root in your terminal.
    *   If you don't have a `package.json`, create one: `npm init -y`
    *   Install required packages:
        ```bash
        npm install puppeteer puppeteer-extra puppeteer-extra-plugin-stealth express body-parser
        ```
        *(Built-in modules like `fs`, `path`, `http`, `https`, `readline` don't need separate installation).*

3.  **Phantom Extension (Optional but Recommended by script):**
    *   Ensure the Phantom wallet extension is placed at `./extensions/phantom/25.11.0_0` relative to `index.js`.
    *   If not present, the script will attempt to run without it but may have reduced functionality or increased detection risk on sites requiring wallet interaction.

4.  **Run the Script:**
    *   In the terminal, from the project root:
        ```bash
        node index.js
        ```

5.  **Initial Manual Setup (Important):**
    *   The script will launch a browser.
    *   It will prompt: `Press ENTER after you have signed in and loaded the chart with RSI indicator...`
    *   In the Puppeteer-controlled browser:
        1.  Go to `https://photon-sol.tinyastro.io`.
        2.  Sign in to your account.
        3.  (Optional, as script attempts this) Navigate to a token and ensure the RSI indicator is visible on the chart.
    *   Return to the terminal and press `ENTER`.

6.  **Interaction:**
    *   **API:** Use `curl` or Postman to send POST requests to `http://localhost:3000/watch-token` (e.g., `curl -X POST -H "Content-Type: application/json" -d '{"mintAddress":"YOUR_TOKEN_ADDRESS_HERE"}' http://localhost:3000/watch-token`).
    *   **CLI Menu:** Follow the prompts in the terminal where the script is running.

## Important Considerations:

*   **Headless Mode:** The script launches Puppeteer in headless mode (`headless: true`) in the main `run()` function, but `headless: false` when restarting the browser. For initial setup and debugging, you might want to temporarily change `headless: true` to `headless: false` in the `puppeteer.launch` options within the `run()` function in `index.js`.
*   **Selectors:** Web scraping scripts are sensitive to website structure changes. The CSS selectors used (e.g., for finding RSI, market cap, buttons) might break if `photon-sol.tinyastro.io` or `solscan.io` update their UI. These would need to be updated in `index.js`.
*   **Error Handling:** The script has extensive error handling, but monitor the logs in the `./logs` directory for any issues.
*   **Resource Usage:** Running multiple browser instances and performing continuous scraping can be resource-intensive. 