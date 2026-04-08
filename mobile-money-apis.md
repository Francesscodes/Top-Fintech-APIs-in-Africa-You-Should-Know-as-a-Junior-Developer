Mobile Money APIs

Mobile money is how hundreds of millions of Africans transact daily. 

If you're building for East Africa in particular, this is the single most important integration you need.

 M-Pesa Daraja (Safaricom)
Website: developer.safaricom.co.ke
Sandbox: Available — register free
Primary Market: Kenya (also Tanzania, Ghana, Ethiopia via MPESA Africa)

What It Does
M-Pesa is the mobile money platform in Africa. Launched by Safaricom in 2007, it now serves over 60 million customers in Kenya and Ethiopia alone and processes more than 100 million transactions daily, peaking at 6,000 transactions per second. Daraja (Swahili for "bridge") is the developer API layer that lets you connect your application to this massive ecosystem.
In November 2025, Safaricom launched Daraja 3.0, a full cloud-native rebuild of the platform with AI-powered fraud detection, improved uptime, self-service developer tools, and a capacity ceiling of 12,000 TPS. The platform now has over 105,000 registered developers and more than 66,000 active integrations.
If you're building anything in Kenya, e-commerce, logistics, healthcare, savings not integrating M-Pesa is not an option.

Key APIs in Daraja

STK Push (Lipa na M-Pesa Online) API - Prompt customer's phone to enter PIN and pay
C2B (Customer to Business) - Register validation and confirmation URLs for paybill payments
B2C (Business to Customer)- Send money to a user's M-Pesa number
B2B (Business to Business) - Pay another business's paybill
Account Balance - Query your M-Pesa account balance
Transaction Status - Check the status of any transaction

Sample: STK Push (Node.js)

javascriptconst axios = require('axios');

// Step 1: Get OAuth token
const getToken = async () => {
  const credentials = Buffer.from(
    `${process.env.CONSUMER_KEY}:${process.env.CONSUMER_SECRET}`
  ).toString('base64');

  const res = await axios.get(
    'https://sandbox.safaricom.co.ke/oauth/v1/generate?grant_type=client_credentials',
    { headers: { Authorization: `Basic ${credentials}` } }
  );
  return res.data.access_token;
};

// Step 2: Initiate STK Push
const stkPush = async (phone, amount) => {
  const token = await getToken();
  const timestamp = new Date().toISOString().replace(/[^0-9]/g, '').slice(0, 14);
  const password = Buffer.from(
    `${process.env.BUSINESS_SHORTCODE}${process.env.PASSKEY}${timestamp}`
  ).toString('base64');

  const res = await axios.post(
    'https://sandbox.safaricom.co.ke/mpesa/stkpush/v1/processrequest',
    {
      BusinessShortCode: process.env.BUSINESS_SHORTCODE,
      Password: password,
      Timestamp: timestamp,
      TransactionType: 'CustomerPayBillOnline',
      Amount: amount,
      PartyA: phone,       // Customer's phone: 254XXXXXXXXX
      PartyB: process.env.BUSINESS_SHORTCODE,
      PhoneNumber: phone,
      CallBackURL: 'https://yourapp.com/mpesa/callback',
      AccountReference: 'Order123',
      TransactionDesc: 'Payment for goods',
    },
    { headers: { Authorization: `Bearer ${token}` } }
  );

  return res.data; // CheckoutRequestID to track with
};

Important: Phone numbers must be in the format 254XXXXXXXXX (Kenyan country code, no +). Always handle the CallBackURL asynchronously 
M-Pesa sends payment confirmation to your server after the transaction completes.

Gotchas for Junior Devs

The sandbox uses test phone numbers from Safaricom's docs — don't use your own real number in sandbox
Going live requires a Safaricom business account (Paybill or Till Number) and a formal review process
Callback URLs must be publicly accessible HTTPS endpoints — use tools like ngrok during local development
