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

---

Would you also like me to create a small sample `.env.example` and starter code for the Node.js or Laravel side if you want to plug the passkey directly into a project? 🚀  
I can quickly generate that too! 