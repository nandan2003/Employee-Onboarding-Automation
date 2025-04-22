# Automated Employee Onboarding Workflow Documentation 🚀

Welcome to the complete documentation for our Automated Employee Onboarding project! This guide covers everything—from generating secure passwords using an Azure Function, provisioning user accounts in Microsoft Entra ID, to storing personalized documents in SharePoint/OneDrive, all orchestrated via Power Automate. 😃

---

## Table of Contents
1. Overview
2. Architecture & Components
3. Workflow Steps
    - 1. Trigger & Data Collection
    - 2. Generate Secure Password with Azure Function
    - 3. Process the HTTP Response
    - 4. Automated User Provisioning in Microsoft Entra ID
    - 5. Store User Documents
    - 6. Final Notifications
4. Conclusion

---

## Overview 🌟

This workflow automates the new hire onboarding process by:
- Generating a secure, random password via an Azure Function 🔐
- Provisioning a user account in Microsoft Entra ID 👥
- Stores user resumes in SharePoint/OneDrive 📁
- Sending final notifications to relevant parties 📧

By integrating Microsoft Power Automate with Azure and Office 365 services, the process minimizes manual intervention and ensures consistency and security.

---

## Architecture & Components ⚙️

- **Power Automate Flow:** The central workflow engine.
- **Azure Function:** A Python-based service to generate a random secure password.
- **Microsoft Entra ID Connector:** Provisions new user accounts.
- **Office 365 Connectors:** - Stores user resumes in SharePoint/OneDrive
- **SharePoint/OneDrive:** Stores document templates and final personalized documents.

---

## **Flow Architecture**

![Automated Onboarding Flow](https://github.com/nandan2003/Employee-Onboarding-Automation/blob/df56509860e4187a01f542f3f3b319bb7e7d8550/Employee%20Onboard%20Flow.png)

---

## Workflow Steps 🔄

### 1. Trigger & Data Collection

- **Trigger:**  
  The flow is initiated when a new response is received (e.g., from a Microsoft Form).
- **Data Collected:**  
  Employee details such as First Name, Last Name, Email, etc.

---

### 2. Generate Secure Password with Azure Function

An Azure Function generates the secure password. Use the following Python code:

```python
import azure.functions as func
import logging
import json
import random
import string

app = func.FunctionApp(http_auth_level=func.AuthLevel.FUNCTION)

@app.route(route="passGenerate")
def passGenerate(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Password generation function processed a request.')
    
    # Check for an optional 'length' parameter. Default length is 12 if not provided.
    try:
        length = int(req.params.get("length", 12))
    except ValueError:
        return func.HttpResponse(
            json.dumps({"error": "Invalid length provided. Please enter a number."}),
            status_code=400,
            mimetype="application/json"
        )

    # Define the characters allowed in the password and generate a random password.
    characters = string.ascii_letters + string.digits + string.punctuation
    password = ''.join(random.choice(characters) for _ in range(length))
    
    # Build a JSON formatted response.
    response_data = {"password": password}
    return func.HttpResponse(
        json.dumps(response_data),
        status_code=200,
        mimetype="application/json"
    )
```

---

## 3. Process the HTTP Response

- **Parse JSON Action:**  
  Use the Parse JSON step in Power Automate to extract the `password` field from the Azure Function's response.

- **Direct Usage:**  
  The parsed password field becomes available as dynamic content for subsequent actions. 📲

---

## 4. Automated User Provisioning in Microsoft Entra ID

Provision the new user account using the Microsoft Entra ID **"Create user"** action:

1. **Add the "Create user" Action in Power Automate.**

2. **Fill in the Required Fields:**

   - **Account Enabled:**  
     Set to `true`.
   - **Display Name:**  
   - **Mail Nickname:**  
   - **User Principal Name (UPN):**  
   - **Password:**  
   - **Given Name & Surname:**  

---

## 5. Manage User Documents (e.g., Resume)

This section focuses on managing user documents such as resumes. The process takes the resume from the submitted form and stores it in the SharePoint/OneDrive.

### 5.1. Prepare Resume Templates

- **Store:**  
  Save the resume template in SharePoint or OneDrive so that Power Automate can access and update it when needed. 📁

1. **Create the Final Resume Document:**  
   Use the **"Create file"** action to save the populated resume to a designated SharePoint or OneDrive folder.

2. **Distribution:**  
   Optionally, send the resume via email as an attachment or provide a downloadable link for HR review.
---

## 6. Final Notifications

Notify the new hire and/or relevant teams about the successful onboarding.

- **Send an Email Action:**  
  Configure the Office 365 Outlook **"Send an email (V2)"** action with the following details:

  - **Recipients:**  
    New employee's email or the HR/IT team.

  - **Subject:**  
    "Welcome to [Your Company] – Your New Account Details"

  - **Body:**  
    Include the UPN, temporary password, and a link or attachment of the personalized document.

### Example Email Body:
```plaintext
Hello [FirstName],

Welcome to [Your Company]!

Your account has been created successfully.

Username: [User Principal Name]
Temporary Password: [Generated Password]

Please change your password at first sign-in.

Attached is your personalized welcome document.

Best regards,
IT Team
```
## Conclusion 🎉

This fully automated onboarding workflow:

- Generates secure passwords using an Azure Function 🔐
- Provisions user accounts in Azure AD 👥
- Stores user resumes in SharePoint/OneDrive 📁
- Notifies all the relevant parties efficiently 📧

By leveraging Power Automate along with Azure and Office 365 services, this process significantly reduces manual work and ensures a secure, consistent onboarding experience.

Enjoy your streamlined new hire onboarding process! 😃
