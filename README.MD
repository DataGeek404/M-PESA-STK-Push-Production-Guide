# M-PESA STK Push Production Guide

A complete guide to obtaining your **M-PESA STK Push (Lipa Na M-PESA Online) Production Passkey** and going live with your application using Safaricom's Daraja API.

---

## 📦 Repository Purpose
This repository provides step-by-step instructions for:

- Setting up your Safaricom M-PESA Organization Portal
- Completing the Go Live process on the Daraja API Portal
- Receiving your Production Passkey
- Troubleshooting common issues

---

## 🚀 How to Get Your Production Passkey

### ✅ Step 1: Set Up Access to the M-PESA Organization Portal

1. **Request Access**:  
   Send an email to [M-PESABusiness@safaricom.co.ke](mailto:M-PESABusiness@safaricom.co.ke) requesting access to the M-PESA Organization Portal.

2. **Create an Operator**:  
   Once you receive your login credentials:
   - Log into the portal
   - Create a new operator with the role of **Business Administrator** or **Business Manager**.  
   This operator will be used during the Go Live process.

---
   
### ✅ Step 2: Initiate the Go Live Process on the Daraja Portal

1. **Register on Daraja**:  
   If you haven't already, create an account on the [Daraja Portal](https://developer.safaricom.co.ke/).

2. **Start Go Live Process**:  
   Navigate to the **Go Live** section and fill in the required details:

   - **Verification Type**: Select **Shortcode**.
   - **Organization Shortcode**: Enter your Paybill or Till number.
   - **Organization Name**: Provide your registered organization name.
   - **M-PESA Username**: Use the username created from the Organization Portal.

3. **Select Product**:  
   Choose **Lipa Na M-PESA Production** (also referred to as **M-PESA Express**).

4. **OTP Verification**:  
   An OTP will be sent to the phone number associated with your M-PESA account. Enter it to proceed.

5. **Submit Application**:  
   Complete and submit the Go Live application.

   > ✅ Upon successful submission, you will receive an email containing the production URLs.

---

### ✅ Step 3: Receive Your Production Passkey

- After approval, Safaricom will send the **Lipa Na M-PESA Online production passkey** to your registered email address.
- Approval usually happens **within 24 hours** after successful Go Live application.

---

## 🛠️ Troubleshooting

| Issue | Solution |
| :--- | :--- |
| **Delayed Passkey** | Contact Safaricom at [apifeedback@safaricom.co.ke](mailto:apifeedback@safaricom.co.ke) to follow up. |
| **Testing Without Production Credentials** | Use **Sandbox** credentials provided on the Daraja Portal for development and testing. These include a default passkey and shortcode for simulation. |

---

## 📚 Additional Resources

- [Daraja API Official Documentation](https://developer.safaricom.co.ke/daraja/apis/post/safaricom-safaricom-stkpush-request)
- [M-PESA Daraja Go Live Steps](https://woompesa.demkitech.com/integration-steps/)
- [How to Locate a Passkey on Daraja Portal (Video)](https://www.youtube.com/watch?v=lmW-admXNjg)

---

## 📢 Important Notes

- Always **keep your Production Passkey secure**. Treat it like a password.
- For each shortcode, a unique passkey is issued.
- **Sandbox and Production passkeys are different** — ensure you update your app when going live!

---

## 👨‍💻 Author

Created and maintained by **DataGeek404**.

---



- a `.env.example` file
- starter code for **Node.js (Express)**  
- starter code for **Laravel**  

---

# 📄 `.env.example`

```dotenv
# M-PESA API CREDENTIALS
MPESA_ENVIRONMENT=production
MPESA_CONSUMER_KEY=your_consumer_key_here
MPESA_CONSUMER_SECRET=your_consumer_secret_here
MPESA_SHORTCODE=your_shortcode_here
MPESA_PASSKEY=your_passkey_here
MPESA_INITIATOR_NAME=your_initiator_name_here
MPESA_INITIATOR_PASSWORD=your_initiator_password_here

# CALLBACK URLs
MPESA_CALLBACK_URL=https://yourdomain.com/callback
MPESA_TIMEOUT_URL=https://yourdomain.com/timeout
```

---
# 🚀 Node.js Starter Code (Express + Axios)

Here’s a basic Express setup for STK Push:

```javascript
// stkPush.js

require('dotenv').config();
const axios = require('axios');
const express = require('express');
const app = express();
app.use(express.json());

const baseURL = process.env.MPESA_ENVIRONMENT === 'production'
  ? 'https://api.safaricom.co.ke'
  : 'https://sandbox.safaricom.co.ke';

// Function to get OAuth Token
const getAccessToken = async () => {
  const auth = Buffer.from(
    `${process.env.MPESA_CONSUMER_KEY}:${process.env.MPESA_CONSUMER_SECRET}`
  ).toString('base64');

  const { data } = await axios.get(`${baseURL}/oauth/v1/generate?grant_type=client_credentials`, {
    headers: {
      Authorization: `Basic ${auth}`
    }
  });

  return data.access_token;
};

// STK Push Request
app.post('/stkpush', async (req, res) => {
  try {
    const accessToken = await getAccessToken();
    const timestamp = new Date()
      .toISOString()
      .replace(/[^0-9]/g, '')
      .slice(0, 14);

    const password = Buffer.from(
      `${process.env.MPESA_SHORTCODE}${process.env.MPESA_PASSKEY}${timestamp}`
    ).toString('base64');

    const stkPushPayload = {
      BusinessShortCode: process.env.MPESA_SHORTCODE,
      Password: password,
      Timestamp: timestamp,
      TransactionType: 'CustomerPayBillOnline',
      Amount: req.body.amount,
      PartyA: req.body.phone,
      PartyB: process.env.MPESA_SHORTCODE,
      PhoneNumber: req.body.phone,
      CallBackURL: process.env.MPESA_CALLBACK_URL,
      AccountReference: req.body.accountReference,
      TransactionDesc: req.body.transactionDesc
    };

    const { data } = await axios.post(`${baseURL}/mpesa/stkpush/v1/processrequest`, stkPushPayload, {
      headers: {
        Authorization: `Bearer ${accessToken}`
      }
    });

    res.status(200).json(data);
  } catch (error) {
    console.error('Error:', error.response.data);
    res.status(500).json({ error: error.response.data });
  }
});

// Start Server
const PORT = process.env.PORT || 3000;
app.listen(PORT, () => console.log(`Server running on port ${PORT}`));
```

---

# 🚀 Laravel Starter Code (Controller + Service)

Assuming you are using Laravel 10+, here’s a **controller** and **service class** approach:

---
### 1. Install Guzzle HTTP (if not yet installed)

```bash
composer require guzzlehttp/guzzle
```

---

### 2. Create a Service Class: `MpesaService.php`

```php
<?php

namespace App\Services;

use Illuminate\Support\Facades\Http;

class MpesaService
{
    protected $baseUrl;

    public function __construct()
    {
        $this->baseUrl = config('app.mpesa_environment') === 'production'
            ? 'https://api.safaricom.co.ke'
            : 'https://sandbox.safaricom.co.ke';
    }

    private function getAccessToken()
    {
        $response = Http::withBasicAuth(
            env('MPESA_CONSUMER_KEY'),
            env('MPESA_CONSUMER_SECRET')
        )->get($this->baseUrl . '/oauth/v1/generate?grant_type=client_credentials');

        return $response->json()['access_token'];
    }

    public function stkPush($amount, $phone, $accountReference, $transactionDesc)
    {
        $timestamp = now()->format('YmdHis');
        $password = base64_encode(
            env('MPESA_SHORTCODE') . env('MPESA_PASSKEY') . $timestamp
        );

        $payload = [
            'BusinessShortCode' => env('MPESA_SHORTCODE'),
            'Password' => $password,
            'Timestamp' => $timestamp,
            'TransactionType' => 'CustomerPayBillOnline',
            'Amount' => $amount,
            'PartyA' => $phone,
            'PartyB' => env('MPESA_SHORTCODE'),
            'PhoneNumber' => $phone,
            'CallBackURL' => env('MPESA_CALLBACK_URL'),
            'AccountReference' => $accountReference,
            'TransactionDesc' => $transactionDesc
        ];

        $response = Http::withToken($this->getAccessToken())
            ->post($this->baseUrl . '/mpesa/stkpush/v1/processrequest', $payload);

        return $response->json();
    }
}
```

---

### 3. Create a Controller: `MpesaController.php`

```php
<?php

namespace App\Http\Controllers;

use App\Services\MpesaService;
use Illuminate\Http\Request;

class MpesaController extends Controller
{
    protected $mpesaService;

    public function __construct(MpesaService $mpesaService)
    {
        $this->mpesaService = $mpesaService;
    }

    public function stkPush(Request $request)
    {
        $validated = $request->validate([
            'amount' => 'required|numeric',
            'phone' => 'required|string',
            'accountReference' => 'required|string',
            'transactionDesc' => 'required|string'
        ]);

        $response = $this->mpesaService->stkPush(
            $validated['amount'],
            $validated['phone'],
            $validated['accountReference'],
            $validated['transactionDesc']
        );

        return response()->json($response);
    }
}
```

---

### 4. Add a Route: `web.php` or `api.php`

```php
use App\Http\Controllers\MpesaController;

Route::post('/stkpush', [MpesaController::class, 'stkPush']);
```


---

# 📦 Postman Collection - **M-PESA STK Push**

Here’s the **collection structure**:

- **Collection Name**: `MPESA STK PUSH API`
- **Single POST request**: `/stkpush`
- **Environment Variables** used:
  - `base_url`
  - `token` (optional if you set Authorization manually)

---

# 🗂️ Collection JSON

```json
{
  "info": {
    "_postman_id": "ea6be7b2-4b51-489f-9c5c-47d88f8a5f8c",
    "name": "MPESA STK PUSH API",
    "description": "Collection to trigger M-PESA STK Push",
    "schema": "https://schema.getpostman.com/json/collection/v2.1.0/collection.json"
  },
  "item": [
    {
      "name": "STK Push Request",
      "request": {
        "method": "POST",
        "header": [
          {
            "key": "Content-Type",
            "name": "Content-Type",
            "value": "application/json",
            "type": "text"
          }
        ],
        "body": {
          "mode": "raw",
          "raw": "{\n    \"amount\": 10,\n    \"phone\": \"2547XXXXXXXX\",\n    \"accountReference\": \"TestAccount\",\n    \"transactionDesc\": \"Test STK Push\"\n}"
        },
        "url": {
          "raw": "{{base_url}}/stkpush",
          "host": [
            "{{base_url}}"
          ],
          "path": [
            "stkpush"
          ]
        }
      },
      "response": []
    }
  ]
}
```

---

# 🌍 Environment Setup for Postman

Create a new **Environment** called `MPESA API ENV` with:

| Variable      | Initial Value                  | Current Value         |
| :------------ | :------------------------------ | :-------------------- |
| `base_url`    | `http://localhost:3000` (for Node.js) or `http://localhost:8000/api` (for Laravel) | Same |
| `token`       | *(Optional if you want to manually insert Authorization Header)* | |

---

# ✅ Steps to Import into Postman

1. Open **Postman** → Click **Import** (top left).
2. Select **Raw Text** and paste the JSON above.
3. Click **Continue** → **Import**.
4. Create an environment called `MPESA API ENV` and set the `base_url`.
5. Use the collection and send a POST request 🚀

---

# 📷 Quick Preview (what it will look like):

```
POST {{base_url}}/stkpush
Headers:
  Content-Type: application/json

Body (raw JSON):
{
  "amount": 10,
  "phone": "2547XXXXXXXX",
  "accountReference": "TestAccount",
  "transactionDesc": "Test STK Push"
}
```

---

# ⚡ Notes:
- Replace `"2547XXXXXXXX"` with a real Safaricom number registered on M-PESA.
- Ensure your backend server (`localhost:3000` for Node.js OR `localhost:8000` for Laravel) is **running** when you hit this.
- If you set **Authorization Bearer Tokens**, modify the Collection and add it under Headers.

---

W
