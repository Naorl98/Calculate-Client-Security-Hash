# Calculate Client Security Hash (UiPath REFramework)

Automated solution for UiPath Academy’s **Advanced RPA Developer – Assignment 1**.  
The robot logs into ACME System 1, extracts every **WI5 (Open)** work-item, computes a SHA-1 security hash from the client’s details, updates the work-item with the hash, and marks the item as **Completed**.

---

## Table of Contents
1. [Key Features](#key-features)  
2. [Prerequisites](#prerequisites)  
3. [Getting Started](#getting-started)  
4. [Configuration](#configuration)  
5. [Project Structure](#project-structure)  
6. [Process Flow](#process-flow)  
7. [Troubleshooting](#troubleshooting)  
8. [Contributing](#contributing)  
9. [License](#license)

---

## Key Features
- **Robust REFramework** implementation with logging, retry, and exception handling  
- Dynamic scraping of ACME work-items (WI5 only)  
- SHA-1 hash creation via `sha1-online.com`  
- Automatic status & comment update for each processed item  
- Config-driven (URLs, credentials, timeouts) via `Config.xlsx`  
- Screenshots captured on exception for fast debugging  

---

## Prerequisites
| Software | Version | Notes |
|----------|---------|-------|
| **UiPath Studio / Studio X** | 23.x or later | Community or Enterprise edition |
| **Chrome / Edge** | Latest | Used by the robot to navigate ACME & SHA-1 websites |
| **UiPath Extensions** | Browser extension installed | Prompted automatically the first time you run Studio |

---

## Getting Started
```bash
# 1) Clone the repository
git clone https://github.com/Naorl98/Calculate-Client-Security-Hash.git
cd Calculate-Client-Security-Hash

# 2) Open Main.xaml in UiPath Studio

# 3) Update usernames / passwords in Orchestrator assets
#    OR edit the 'Config.xlsx' → Settings sheet
#
# 4) Click 'Run' (or publish & run from UiPath Assistant)
```

> **Tip:** The default branch is `main`.  
> If Studio creates `master` locally, rename it or push with:
> ```bash
> git push -u origin master
> ```

---

## Configuration
| Sheet | Key | Description |
|-------|-----|-------------|
| **Settings** | `System1_URL` | ACME test site URL |
| | `SHA1_URL` | SHA-1 online calculator URL |
| | `System1_Credential` | Name of Orchestrator asset containing login creds |
| **Constants** | `MaxRetryNumber` | Global transaction retry count |
| **Assets** | — | Leave blank if you use Orchestrator assets |

All values are read at runtime by the **Init** state.

---

## Project Structure
```
📂 Calculate-Client-Security-Hash
│
├── Main.xaml
├── project.json
├── Config.xlsx
│
├── AddComments.xaml
├── CopyFromDetails.xaml
├── CreateHash.xaml
├── LoginToAcme.xaml
├── NavigateToWorkItems.xaml
├── NavigateUpdateWork.xaml
│
├── .screenshots/          # captured on error (for debugging)
├── .local/ .objects/ …    # UiPath internal folders
└── .settings / .tmh …     # project metadata
```

---

## Process Flow
1. **Init**  
   - Reads `Config.xlsx`, opens the browser, and logs in to ACME.
2. **Get Transaction Data**  
   - Navigates to *Work Items* → filters `WI5 / Open`.  
   - Returns the next unprocessed work-item.
3. **Process Transaction**  
   - Opens item details → extracts `ClientID`, `ClientName`, `ClientCountry`.  
   - Builds the string `"{ClientID}-{ClientName}-{ClientCountry}"`.  
   - Calculates the SHA-1 hash on **sha1-online.com**.  
   - Updates the work-item status to **Completed** and adds the hash as a comment.
4. **End**  
   - Logs out, closes the browser, and writes final results to the output panel.

All states inherit global error-handling from the REFramework.  
Unhandled exceptions trigger a screenshot, log entry, and (optionally) an e-mail before safe termination.

---

## Troubleshooting
| Symptom | Cause | Fix |
|---------|-------|-----|
| `error: src refspec main does not match any` when pushing | Local branch is named `master`, remote expects `main` | Push `master` or rename: `git branch -m master main` |
| Robot stops at login page | Wrong credentials or selector change | Verify Orchestrator asset / selectors in `LoginToAcme.xaml` |
| Hash not inserted | SHA1-online UI changed | Update selectors in `CreateHash.xaml` |
| Evaluator score stays 0 | You reset ACME data during assessment | Do **NOT** reset data while the assignment is pending |

---

## Contributing
Pull requests are welcome!  
Please fork the repo and open a PR with clear commit messages.  
For major changes, open an issue first to discuss what you would like to change.

---

## License
This project is released under the **MIT License** – see the `LICENSE` file for details.
