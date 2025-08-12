# Onboarding MCP 

A model context protocol (MCP) server provided by Fiuu that provides onboarding of submerchants easeness for our MRP using AI assistants. 

![MCP Demo](assets/mcp_demo.gif)

## 📘 Overview  

This MCP service is open to participating Merchant Requisition Partners (MRP) and Partners/Facilitators (PF) of Fiuu. When MRP/PF participates in the program, they are provisioned with a service account which is meant to be for server-to-server communication. This account has a unique email and secret key for authentication. They'll receive these details in a welcome email sent from support@fiuu.com. Features included is meant to follow the Merchant Onboarding API. For further details, can be found here: [Onboarding API Documentation](https://fiuu-onboarding.readme.io/reference/introduction) 

## 📋 Prerequisites   

### System Requirements

- **Node.js**: Version 18.0.0 or higher  
- **MCP Client**: A compatible client that supports the Model Context Protocol (MCP), such as:
  - Claude Desktop  
  - Cursor  
  - Qwen  
  - Cherry Studio  
  - Any other MCP-compatible client

### Authentication Credentials

- **User Key**: Your MRP-registered email address  
- **User Secret Key**: MRP notification key used for secure API access  

> **Note:** Authentication credentials will be provided by **Fiuu** upon successful enrollment in the **Merchant Requisition Partner (MRP)** or **Partner/Facilitator (PF)** program.  You will receive these credentials via a welcome email from: `support@fiuu.com`

 
## ⚙️ Configurations 

This guide explains how to connect to the **Fiuu Onboarding Agent** from various development tools, using either **STDIO** or **SSE** connection modes.

### Step 1: Choose Your Client

| Client                         | Recommended Mode | Instructions                                                                 |
| :----------------------------- | :--------------- | :--------------------------------------------------------------------------- |
| **Claude Desktop** | STDIO            | Go to `Settings` → `Developer` → `Edit` and update `claude_desktop_config.json`. |
| **Cursor** | STDIO or SSE            | Go to `Settings` → `Tools & Integrations ` → `Add Custom MCP` and update `mcp.json`.        |
| **Cherry Studio / Qwen / Others**| STDIO or SSE     | Refer to their specific settings documentation for adding an MCP server.     |


---
### Step 2: Select a Connection Mode

#### Option 1: STDIO Mode (Recommended)

Use this mode if your client supports connections via STDIO.

```
{ 
"mcpServers": { 
    "onboarding-mcp": { 
    "command": "npx", 
    "args": [ 
        "mcp-remote@latest", 
        "https://onboarding-mcp.fiuu.com/sse", 
        "--header", 
        "X-UserID:${USERID}", 
        "--header", 
        "X-SecretKey:${SECRETKEY}" 
    ], 
    "env":  { 
        "USERID": "<USER_EMAIL>", 
        "SECRETKEY": "<SECRET_KEY>" 
            } 
        } 
    } 
} 
```

#### Option 2: SSE Mode

Use this if your client supports SSE (Server-Sent Events) connections.
```
{
"mcpServers": {
    "sse-onboarding": {
    "url": "https://onboarding-mcp.fiuu.com/sse",
    "headers": {
        "Content-Type": "application/json",
        "X-UserID": "<USER_EMAIL>",
        "X-SecretKey": "<SECRET_KEY>"
            }
        }
    }
}
```
> **Note:** Not all clients support SSE mode. Use STDIO if SSE is not available.


## 🛠️ Tools

### 🧰 Available Toolsets

| Tool Name                   | Purpose                                                              |
|-----------------------------|----------------------------------------------------------------------|
| `create_update_application` | Creates or updates an onboarding application.                        |
| `submit_application`        | Submits an onboarding application for verification.                  |
| `create_store`              | Creates a new retail store for a specific merchant.                  |
| `create_new_channel`        | Subscribes a merchant to an additional payment channel.              |
| `list_applications`         | Lists all onboarding applications with optional filters.             |
| `get_application`           | Retrieves full details of a specific application.                    |
| `get_plan`                  | Provides a list of plans available to the merchant.                  |
| `get_detailed_plan`         | Shows detailed channel offerings under a specific plan.              |
| `get_channel_status`        | Checks the subscription status of channels in an application.        |
| `get_acknowledgement_status`| Checks the application acknowledgement status.                       |
| `get_states`                | Provides a list of states for a specified country.     

---

### 1. create_update_application

Creates or updates an onboarding application. If an `application_id` is provided, the tool updates the existing onboarding application. For information about the parameters used in this tool and their valid values, see: [Merchant Onboarding API](https://fiuu-onboarding.readme.io/reference/getting-started-with-your-api-1). Please note that this tool does not support onboarding document uploads.

**Note:** It is recommended to use the template below in your MCP client to ensure consistency and completeness when submitting application data through this tool.

**Template:**  
[`create_onboarding.csv`](assets/create_onboarding.csv)

**Examples:**
- "Read the CSV file create_onboarding.csv and create onboarding applications for all companies listed"
- "Import merchant applications from the uploaded CSV file and register them in the system"
- "Update application `<application_id>` to change the company phone number to +60123456789"
- "Update application `<application_id>` to set the website domain to shop.example.com"

---

### 2. submit_application

Allows applicants to officially submit their applications. The tool checks and confirms the information to make sure it meets the necessary standards.

**Parameters:**
- `application_id` (string, required): The ID of the application being submitted.

**Examples:**
- “Please submit Fiuu onboarding application `<application_id>`.”
- "Submit the application I just created."

---

### 3. create_store

Creates a new retail store location for a specific merchant account. For more details, see: [Create New Store API](https://fiuu-onboarding.readme.io/reference/create-new-store-api).

**Note:** It is recommended to use the template below in your MCP client to ensure consistency.

**Template:**  
[`create_new_store.csv`](assets/create_new_store.csv)

**Examples:**
- “Create new stores for the merchant `<merchant_id>` based on the data provided in the `create_new_store.csv` file.”

---

### 4. create_new_channel

Creates an ‘add-on channel’ application.

**Parameters:**
- `merchant_id` (string, required): Your MRA or merchant ID.
- `channel` (string, required): Name of the channel to subscribe.
- `rate` (string, required): Subscription rate (%) for the channel.

**Note:** Multiple channels can be requested if their rates match the merchant’s offered plan.

**Examples:**
- “Subscribe to channel Alipay with a rate of 1.50%.”
- “Create a new channel application for Visa at 2.50%.”

---

### 5. list_applications

Provides an overview of all received applications.

**Parameters:**
- `company_name` (string, optional): Filter by company name.  
- `company_registration_number` (string, optional): Filter by company registration number.  
- `merchant_id` (string, optional): Filter by merchant ID.  
- `application_status` (string, optional): Filter by status (e.g., "Draft", "Approved", "Rejected", "Submitted").

**Examples:**
- “List all onboarding applications.”
- “Find all applications that are still pending.”
- “Show me all approved applications for company `<company_name>`.”

---

### 6. get_application

Provides detailed information for a specific application.

**Parameters:**
- `application_id` (string, required): The ID of the application.

**Examples:**
- “What are the details of this application `<application_id>`?”
- “Information regarding this application `<application_id>`.”

---

### 7. get_plan

Provides a list of plans offered to a merchant.

**Examples:**
- “What plans can I subscribe to?”
- “Give me a list of plans offered.”

---

### 8. get_detailed_plan

Provides channel details under a specific plan.

**Parameters:**
- `plan_id` (string, required): ID of the plan (from `get_plan`).

**Examples:**
- “What channels can I subscribe to based on plan `<plan_id>`?”
- “Give details of this plan `<plan_id>`.”

---

### 9. get_channel_status

Provides the status of channel subscriptions for an application.

**Parameters:**
- `application_id` (string, required): ID of the application.

**Examples:**
- “What is the status of my channel subscription for this application `<application_id>`?”

---

### 10. get_acknowledgement_status

Provides the agreement status for a specific application.

**Parameters:**
- `application_id` (string, required): ID of the application.

**Examples:**
- “What is the agreement status for this application `<application_id>`?”
- “Application `<application_id>`’s acknowledgement status.”

---

### 11. get_states

Provides a list of states for a given country.

**Parameters:**
- `country_code` (string, required): ISO 2-character country code.

**Examples:**
- “Give me the states for Thailand.”
- “Malaysia’s states?”

## 💬 Support & Contact

If you find yourself stuck or needing clarification on anything related to the Onboarding MCP, don't hesitate to contact us at support@fiuu.com. We're here and happy to help. 💙