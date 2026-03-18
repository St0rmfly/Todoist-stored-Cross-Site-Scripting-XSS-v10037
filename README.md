# Todoist Stored XSS Vulnerability (v10037)

## Description

A **Stored Cross-Site Scripting (XSS)** vulnerability was discovered in Todoist web application version v10037. This vulnerability allows an attacker to inject malicious JavaScript code through the file upload functionality. The injected payload is permanently stored on Todoist's servers and executes whenever any user views the affected content.

Unlike reflected XSS, this stored version is particularly dangerous because:
- The payload affects **all users** who access the compromised content
- No user interaction is required beyond normal usage
- The attack can spread automatically to multiple victims


## Step-by-Step Reproduction

### Prerequisites
- A valid Todoist account
- Basic understanding of JavaScript
- The ability to intercept/modify HTTP requests (Burp Suite, OWASP ZAP, or browser DevTools)

### Steps to Reproduce

**Step 1: Login to Todoist**

Navigate to https://app.todoist.com

Log in with your credentials

Open browser Developer Tools (F12)

<img width="427" height="526" alt="6" src="https://github.com/user-attachments/assets/c1ea8d2f-9818-48d3-867b-29c4ac45d827" />


**Step 2: Prepare the Payload**
Create a malicious JavaScript payload. For demonstration purposes, we use a simple alert:
```javascript
<script>alert('XSS BY Jessy');</script>

**Step 3: Intercept the Upload Request**

Using Burp Suite or browser DevTools, intercept a file upload request:
<img width="782" height="379" alt="1" src="https://github.com/user-attachments/assets/09472779-dd80-4a62-9c52-20cf8fb26daa" />

**Step 4: Inject the Payload**

Modify the request parameters to include your XSS payload. The injection point is in the task description or file metadata field:

<img width="785" height="375" alt="2" src="https://github.com/user-attachments/assets/58b6d9fe-de12-4fa5-a71e-0e92e96bcc3e" />

Step 5: Verify the Stored Payload

After uploading, navigate to the task or project containing your payload. The malicious script is now stored on Todoist's servers.

<img width="947" height="506" alt="3" src="https://github.com/user-attachments/assets/973f391d-6191-4f82-b416-96495ade10f1" />

Step 6: Trigger the XSS

When any user (including yourself) views the affected content:

    The server sends the stored HTML/JavaScript to the browser

    The browser renders the page and executes the malicious script

    The XSS payload triggers, displaying the popup

    In a real attack, the script could now:

        Send cookies to an attacker-controlled server

        Modify the page content

        Perform actions on behalf of the user

<img width="949" height="509" alt="4" src="https://github.com/user-attachments/assets/4edb974c-276a-4ba1-a3b9-d99bc97f53b5" />

Step 7: Confirmation

The payload successfully executes, confirmed by the popup from d1ysz50cxb9zwl.cloudfront.net, which is Todoist's CDN domain.

<img width="944" height="472" alt="5" src="https://github.com/user-attachments/assets/7f36bf4c-1ad8-418f-b253-81a8d1d086be" />


Mitigation Recommendations
For Todoist Development Team

    Input Validation

        Implement strict validation for all file uploads

        Validate file types, sizes, and content

    Output Encoding

        Encode all user-generated content before rendering

        Use context-appropriate encoding (HTML, JavaScript, CSS)

    Content Security Policy (CSP)

        Implement strict CSP headers

        Restrict inline script execution

        Use nonce-based approach for legitimate scripts

    Sanitization

        Use established libraries like DOMPurify for HTML sanitization

        Remove or neutralize script tags and event handlers

