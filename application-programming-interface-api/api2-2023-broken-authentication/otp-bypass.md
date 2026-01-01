# OTP Bypass

***

## Technique & Security Testing

***

### OTP Bypass via Response Manipulation

Celah ini terjadi karena aplikasi melakukan validasi hanya di sisi Client **(Client Side Validation).**

#### Manipulating Verification Response

1. Register an account with a mobile number and request an OTP
2. Enter an incorrect OTP and capture the request using Burpsuite
3. Intercept and modify the server response (like "verificationStatus": false, to true)
4. Forward the manipulated response
5. The system authenticates the account despite the incorrect OTP

**Impact: full account takeover without providing valid OTP**

#### Changing Error Response To Success

1. Go to the login page and enter your phone number
2. When prompted for an OTP, enter an incorrect OTP
3. Intecrept and modify the server respons (like "error": "Invalid OTP", to "success": true)
4. Forward the manipulated response
5. If the server accepts this modification, you gain access without entering a valid OTP

**Impact: Authentication bypass leading to account takeover.**

#### OTP Verification Across Multiple Account

1. Register two different accounts with separate phone numbers
2. Enter the correct OTP fo one account and intercept the request
3. Capture the server response and note the status success
4. Now, attemp to verify the second account with an incorrect OTP
5. Intercept the server response where the status is failure
6. Change the status failure to success and forward the response
7. If successful, you bypass OTP authentication

**Impact: Bypassing OTP verification for multiple accounts**

***

### OTP Bypass Using Form Resubmission In Repeater

1. Register an account using a non-existent phone number
2. Intercept the OTP request in Burpsuite&#x20;
3. Send the request to Repeater and forward it&#x20;
4. Modify the phone number in the request to your real number&#x20;
5. If the system sends the OTP to your real number, use it to register under the fake number

**Impact: Unauthorized account registration using someone else's OTP**

***

### OTP Bypass With No Rate Limiting

1. Create an account and request an OTP&#x20;
2. Enter an incorrect OTP and capture the request in Burpsuite&#x20;
3. Sent the request to Intruder and set a payload on the OTP field&#x20;
4. Set payload type as number (depend the how many digits)&#x20;
5. Start the attack&#x20;
6. If no rate limit is enforced, the correct OTP will eventually match and also check the response length which can may indicate the correct OTP

**Impact: Complete OTP bypass through bruteforce**

***

### Additional OTP Bypass Test Cases

1. Default OTP values: some application use default OTP values, so test it to check misconfiguration. like 111111 or 000000
2. OTP leakage in server response: some application leak OTPs in API responses. Intercept OTP request response and check if OTP is present
3. Checking if old OTP is still valid: some systes allow the reuse of old OTPs, test if previously used OTPs are still accepted
4. Null Boolean Payload: Change request body to null or true
5. Pre-Auth Session: Check if you already get Cookie/JWT after input e.g. phone number. Try to use it to access profile page.
