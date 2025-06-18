# CST8919 Lab 2 Building a Web App with Threat Detection using Azure Monitor and KQL

## Part 1: Deploy the Flask App to Azure
Develop a Pthyon Flask app with simple log in flask demo using VS Code.
```python
from flask import Flask, request, jsonify
import logging

app = Flask(__name__)
logging.basicConfig(level=logging.INFO)

@app.route('/')
def home():
    return "Welcome to the Flask Demo App!"

@app.route('/login', methods=['POST'])
def login():
    data = request.get_json()
    username = data.get("username")
    password = data.get("password")

    # Example: Only one valid login
    if username == "admin" and password == "secure123":
        app.logger.info(f"SUCCESSFUL LOGIN: {username}")
        return jsonify({"message": "Login successful!"}), 200
    else:
        app.logger.warning(f"FAILED LOGIN: {username}")
        return jsonify({"message": "FAILED LOGIN"}), 401

if __name__ == '__main__':
    app.run(debug=True)
```
Deploy the app using azure Web App
![image](https://github.com/user-attachments/assets/a27fb7e4-a0a8-41c8-a05c-0c37298c903c)

## Part 2: Enable Monitoring
Create a Log Analytics Workspace
![image](https://github.com/user-attachments/assets/fb2f0320-5630-4035-8ceb-5d7e20780979)
Enable:AppServiceConsoleLogs, AppServiceHTTPLogs (optional), Send to the Log Analytics workspace.
![image](https://github.com/user-attachments/assets/8f8cad81-db66-4260-8ecc-f48860e8793d)
Develop a http app using REST client to send request to Web App
```http
@baseUrl = https://flask-monitor-demo.azurewebsites.net

### Successful login attempt
POST {{baseUrl}}/login
Content-Type: application/json

{
  "username": "admin",
  "password": "secure123"
}

### Failed login attempt
POST {{baseUrl}}/login
Content-Type: application/json

{
  "username": "user",
  "password": "wrongpassword"
}

```
The log in request can be inspect in log stream
![image](https://github.com/user-attachments/assets/c4c4fbff-a9f0-47df-a2b0-119601174460)

## Part 3: Query Logs with KQL
Create a KQL query to find failed login attempts.
```query
AppServiceConsoleLogs
| where TimeGenerated > ago(10m)
| where ResultDescription contains "Failed"
| project TimeGenerated, ResultDescription
| sort by TimeGenerated desc
```
![image](https://github.com/user-attachments/assets/2e58a1cc-93d4-4ee6-86a7-4a901e4336e5)
## Part 4: Create an Alert Rule
Create an Alert rule using the KQL query created, and setup email notification if failed more than 5 attempts.
![image](https://github.com/user-attachments/assets/23188a4e-1dd7-4255-9109-a66a871e70b6)
![image](https://github.com/user-attachments/assets/6a73c225-327d-4d14-a3d3-c4ab58376cee)
![image](https://github.com/user-attachments/assets/d5e5b9e3-d864-4ae5-8fe1-86a510c21d16)
![image](https://github.com/user-attachments/assets/a59e315a-4ad9-4028-9543-a643895a9a8b)
![image](https://github.com/user-attachments/assets/0ea4782d-72f5-4a2c-9edf-96dcb09e422a)

## Youtube link
https://youtu.be/BVE80C7APQc
