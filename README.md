# SCORM Completion Bypass

A technical guide demonstrating how to interact with SCORM 2004 Run-Time API (`API_1484_11`) using browser developer tools to modify `cmi.completion_status` values.

---

## Table of Contents

- [⚠️ LEGAL DISCLAIMER & WARNING](#️-legal-disclaimer--warning)
- [Compatibility](#compatibility)
- [Prep Steps](#prep-steps)
- [Essential Commands](#essential-commands)
- [Finish Up](#finish-up)
- [Troubleshooting](#troubleshooting)
- [Why This Works](#why-this-works)
- [Contributing](#contributing)
- [License](#license)
- [Final Warning](#final-warning)

---

## ⚠️ LEGAL DISCLAIMER & WARNING

**READ THIS CAREFULLY BEFORE PROCEEDING:**

### Purpose & Intended Use
This repository is provided **STRICTLY for educational, research, and authorized testing purposes only**, including:
- Testing and debugging SCORM content you personally own or develop
- Learning how SCORM APIs function in controlled environments
- Authorized security research or penetration testing with explicit permission

### No Warranty
This information is provided **"AS IS" WITHOUT WARRANTY OF ANY KIND**, express or implied, including but not limited to:
- Warranties of merchantability or fitness for a particular purpose
- Warranties of non-infringement or accuracy
- Any warranties that the information will work as described

### Limitation of Liability
**THE AUTHOR(S) AND CONTRIBUTOR(S) SHALL NOT BE LIABLE** for any damages whatsoever, including but not limited to:
- Direct, indirect, incidental, special, consequential, or punitive damages
- Loss of profits, data, employment, or business opportunities
- Legal fees, fines, or penalties
- Any other damages arising from use or misuse of this information

### Your Responsibility
By accessing or using this information, **YOU ACCEPT FULL AND SOLE RESPONSIBILITY** for:
- All actions taken and consequences resulting from use of this information
- Ensuring compliance with all applicable laws, regulations, and policies
- Obtaining all necessary permissions before testing any systems
- Any violations of terms of service, employment agreements, or organizational policies

### Legal & Ethical Risks
**UNAUTHORIZED USE MAY RESULT IN SERIOUS CONSEQUENCES**, including but not limited to:
- ❌ **Employment termination** and loss of benefits
- ❌ **Breach of contract** (employment agreements, LMS terms of service)
- ❌ **Civil liability** and lawsuits for damages
- ❌ **Criminal charges** under computer fraud, unauthorized access, or similar laws
- ❌ **Regulatory violations** in industries requiring mandatory compliance training (healthcare, finance, aviation, etc.)
- ❌ **Loss of professional licenses or certifications**
- ❌ **Academic dishonesty** violations and expulsion
- ❌ **Fraud charges** for falsifying training records

### Prohibited Uses
**DO NOT USE THIS INFORMATION TO:**
- Bypass mandatory workplace, safety, compliance, or certification training
- Violate any LMS, organizational, institutional, or employer policies
- Circumvent legitimate educational or training requirements
- Access or modify systems without explicit authorization
- Falsify training completion records
- Violate any applicable laws or regulations

### No Endorsement
This repository **DOES NOT encourage, endorse, or condone** any unauthorized, unethical, or illegal use of this information.

### Agreement to Terms
**BY CONTINUING TO READ OR USE THIS INFORMATION, YOU ACKNOWLEDGE THAT:**
1. You have read and understood this disclaimer
2. You agree to use this information only for lawful, authorized, and ethical purposes
3. You accept full responsibility for your actions
4. You release the author(s) from any and all liability

**IF YOU DO NOT AGREE TO THESE TERMS, EXIT THIS REPOSITORY IMMEDIATELY AND DO NOT USE THIS INFORMATION.**

---

## Compatibility
- **SCORM Version**: 2004 (1.0+ editions; won't work on SCORM 1.2, which uses `API` instead of `API_1484_11`).
- **Browsers**: Chrome/Chromium-based (recommended). Works on macOS, Windows, Linux, adjust launch flags as needed.
- **LMS**: Any that embeds SCORM via iframe (e.g., Moodle, Canvas, Blackboard). Bypasses CORS restrictions for console access.
- **Tested On**: macOS with Chrome/Comet; generalize for others below.

## Prep Steps

1. **Launch Browser with Security Disabled** (Bypasses CORS for iframe access):  
   Quit your browser, open a terminal/command prompt, and run the flag-enabled launch command. This creates a temporary dev sesson—**don't use for regular browsing**.  
   
   - **macOS (Chrome)**:  
   ```
   /Applications/Google\ Chrome.app/Contents/MacOS/Google\ Chrome --disable-web-security --user-data-dir="/tmp/chrome_dev_session"
   ```
    
   - **macOS (Comet, if using)**:  
   ```
   /Applications/Comet.app/Contents/MacOS/Comet --disable-web-security --user-data-dir="/tmp/comet_dev_session"
   ```
   
    - **Windows (Chrome)**:  
   ```
   "C:\Program Files\Google\Chrome\Application\chrome.exe" --disable-web-security --user-data-dir="C:\temp\chrome_dev_session"
   ```

    - **Linux (Chrome)**:  
   ```
   google-chrome --disable-web-security --user-data-dir="/tmp/chrome_dev_session"
   ```


*(Adjust paths if installed elsewhere. For other Chromium browsers like Edge/Brave, swap the executable path.)*

2. **Load the Module**:  
Log into your LMS and start *any* SCORM 2004 module (let it fully load, wait for the content iframe to initialize).

3. **Open DevTools & Target the Iframe**:  
- Press `F12` (or `Cmd + Option + I` on macOS / `Ctrl + Shift + I` on Windows/Linux).  
- Go to the **Elements** tab, press `Ctrl + F` (or `Cmd + F`), and search for `"iframe"` or the module's entry file (e.g., `"index.html"` or `"story.html"`).  
- Click the `<iframe>` element (it highlights blue). This ensures the Console targets the SCORM frame, check the top dropdown (frame selector) shows the module's name.  
- Switch to the **Console** tab.

4. **Clear Console** (Optional, reduces clutter):  
Type `clear()` and hit Enter.

## Essential Commands

**⚠️ REMINDER: Use only on systems you own or have explicit authorization to test.**

Run these *one at a time* in the targeted Console (paste, Enter). They set completion status, commit changes, and terminate the session.

1. `API_1484_11.SetValue("cmi.completion_status", "completed");`
(Should return 'true'. If not, see Troubleshooting.)

2. `API_1484_11.Commit("");`
(Should return 'true'. Saves to LMS.)

3. `API_1484_11.Terminate("");`
(Should return 'true'. Finalizes and ends the session.)

## Finish Up
- Close the tab (avoid refreshing inside the module).  
- Refresh your LMS dashboard, the module should now show as "Completed."  
- Quit the browser and relaunch normally (delete the temp session dir for cleanup, e.g., `rm -rf /tmp/chrome_dev_session`).

## Troubleshooting
- **Returns `'false'` or errors?** After the failed command, run:  
  ```
  API_1484_11.GetLastError();
  ```  
  Then:  
  ```
  API_1484_11.GetErrorString("ERROR_CODE_FROM_ABOVE");
  ```  
  (Common fixes: Ensure iframe is targeted; retry after full load. Ignore "string table" logs, they're harmless from tools like Articulate Storyline.)

- **`API_1484_11` is undefined?** The module might not expose the API yet, refresh the module (`Cmd/Ctrl + R`) and retry. Or check if it's SCORM 1.2 (use `API` instead).

- **CORS blocks console?** Double-check the launch flags; restart browser.

- **Works in preview but not LMS?** Some LMSes sandbox iframes tighter, test in incognito or a different browser.

- **For SCORM 1.2 modules**: Swap `API_1484_11` with `API`, and use `"cmi.core.lesson_status"` instead of `"cmi.completion_status"`.

## Why This Works
SCORM 2004's Run-Time API allows content to report status back to the LMS via JavaScript. This method directly manipulates the CMI data model, simulating natural completion.

## Contributing
Found tweaks for other OS/browsers/LMS configurations for **authorized testing environments**? Open a PR! Report issues if it fails on a specific setup.

**Note**: All contributions must comply with the Legal Disclaimer above. Contributions that encourage unauthorized use will be rejected.

---

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

**THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND. THE AUTHORS SHALL NOT BE LIABLE FOR ANY DAMAGES ARISING FROM USE OF THIS SOFTWARE.**

---

## Final Warning

**⚠️ CRITICAL REMINDER:**

The techniques described in this repository can have **severe legal, professional, and personal consequences** if misused. Many jurisdictions have laws against:
- Unauthorized computer access (e.g., Computer Fraud and Abuse Act in the US)
- Falsifying records or documentation
- Fraud and misrepresentation
- Bypassing security measures without authorization

**Real-world consequences have included:**
- Criminal prosecution and jail time
- Six-figure civil judgments
- Permanent employment blacklisting
- Revocation of professional licenses
- Expulsion from academic institutions

**The author(s) assume ZERO responsibility for how you use this information. You have been warned.**

Use responsibly, ethically, and legally, or don't use it at all.