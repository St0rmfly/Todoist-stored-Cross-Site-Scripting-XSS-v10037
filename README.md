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
javascript
<script>alert('XSS BY Jessy');</script>

**Step 3: Intercept the Upload Request**

Using Burp Suite or browser DevTools, intercept a file upload request:
<img width="782" height="379" alt="1" src="https://github.com/user-attachments/assets/53d47443-b6b2-4406-8789-c8da5dd81a91" />


**Step 4: Inject the Payload**

<img width="785" height="375" alt="2" src="https://github.com/user-attachments/assets/549adede-b951-4155-a295-25e74a04e2a7" />

Modify the request parameters to include your XSS payload. The injection point is in the task description or file metadata field:



**Step 5: Verify the Stored Payload**

<img width="947" height="506" alt="3" src="https://github.com/user-attachments/assets/137b132c-719f-4d7a-9f6f-29d33d125fd1" />

After uploading, navigate to the task or project containing your payload. The malicious script is now stored on Todoist's servers.



**Step 6: Trigger the XSS**

<img width="949" height="509" alt="4" src="https://github.com/user-attachments/assets/6e352e02-f110-44cb-8f06-f6d8d6d052dc" />


When any user (including yourself) views the affected content:

    The server sends the stored HTML/JavaScript to the browser

    The browser renders the page and executes the malicious script

    The XSS payload triggers, displaying the popup

    In a real attack, the script could now:

        Send cookies to an attacker-controlled server

        Modify the page content

        Perform actions on behalf of the user


**Step 7: Confirmation**

<img width="944" height="472" alt="5" src="https://github.com/user-attachments/assets/6f3b040c-617a-4852-8917-ecc8a878e275" />


The payload successfully executes, confirmed by the popup from d1ysz50cxb9zwl.cloudfront.net, which is Todoist's CDN domain.




*Mitigation Recommendations*
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


