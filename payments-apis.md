Payment Collection APIs

These APIs let you accept money from users via cards, bank transfers, USSD, and mobile money — the bread and butter of any commerce or fintech product in Africa.

1. Paystack
Website: paystack.com | Docs: paystack.com/docs
Sandbox: Available — free to test
Primary Markets: Nigeria, Ghana, Kenya, South Africa
What It Does
Paystack is the gold standard for developer-friendly payment APIs in West Africa. Founded in 2016 by Shola Akinlade and Ezra Olubi, it was acquired by Stripe in 2020 for over $200 million,  the largest startup acquisition in Nigerian history at the time. More than 300,000 merchants use Paystack to collect payments, and its API has powered a generation of Nigerian startups.
The core offering is a clean REST API for accepting payments via cards, bank transfers, USSD, QR codes, and mobile money. Its checkout modal is plug-and-play, you can go from zero to accepting payments in under an hour.
Key Endpoints to Know
httpPOST /transaction/initialize   → Generate a payment link
GET  /transaction/verify/:ref  → Verify a transaction
POST /transfer                 → Send money to a bank account
GET  /bank                     → List supported banks
Sample: Initialize a Transaction (Node.js)
javascriptconst axios = require('axios');

const initializePayment = async () => {
  const response = await axios.post(
    'https://api.paystack.co/transaction/initialize',
    {
      email: 'customer@example.com',
      amount: 5000, // amount in kobo (₦50.00)
      currency: 'NGN',
    },
    {
      headers: {
        Authorization: `Bearer ${process.env.PAYSTACK_SECRET_KEY}`,
        'Content-Type': 'application/json',
      },
    }
  );

  return response.data.data.authorization_url; // redirect user here
};

Pro Tip: Always verify transactions server-side using the reference returned after payment and ever trust a client-side success callback alone.

Why Junior Devs Love It - Arguably the best documentation of any African payment API
Active developer community on Slack and GitHub
Sandbox with test cards and bank transfer simulation built in
Webhooks are well-structured and easy to implement
Settlement is T+1 (next business day) in Nigeria — fast for SMEs

Pricing (Nigeria) - 1.5% per transaction + ₦100 for transactions above ₦2,500
No monthly fees, no setup costs

2. Flutterwave
Website: flutterwave.com | Docs: developer.flutterwave.com
Sandbox: Available
Primary Markets: 34+ African countries + international
What It Does
If Paystack optimized for simplicity in Nigeria, Flutterwave built infrastructure for cross-border complexity. 
Co-founded by Olugbenga Agboola and Iyinoluwa Aboyeji in 2016, Flutterwave has processed over 1 billion transactions worth more than $40 billion across more than 30 countries. 
It holds a $3 billion valuation and has secured 34 US Money Transmitter Licences, positioning it as a truly global operator.
Flutterwave supports the widest range of payment methods on the continent: cards, bank transfers, USSD, mobile money (MTN, Airtel, Vodafone), and even cryptocurrency. 
In January 2026, it acquired open banking startup Mono, adding financial data access, identity verification, and account-to-account payments to its already powerful stack.

Key Features - Rave Inline: A drop-in checkout UI
Payment Links: No-code payment pages
Recurring Payments: Subscription billing support
Transfers API: Programmatic disbursements to 30+ African countries
Virtual Accounts: Assign dedicated bank accounts to customers

Sample: Create a Payment Link (Python)
pythonimport requests

url = "https://api.flutterwave.com/v3/payments"
headers = {
    "Authorization": f"Bearer {FLUTTERWAVE_SECRET_KEY}",
    "Content-Type": "application/json"
}

payload = {
    "tx_ref": "order_12345",
    "amount": "2500",
    "currency": "NGN",
    "redirect_url": "https://yourapp.com/payment-callback",
    "customer": {
        "email": "user@example.com",
        "name": "Ade Balogun"
    },
    "customizations": {
        "title": "My Store",
        "logo": "https://yourapp.com/logo.png"
    }
}

response = requests.post(url, json=payload, headers=headers)
print(response.json()["data"]["link"])  # redirect user to this URL

When to Choose Flutterwave Over Paystack - Your users are spread across multiple African countries, not just Nigeria
You need mobile money support in East or Francophone West Africa.
You're building a marketplace with complex disbursement flows.
You need international card acceptance (Visa/Mastercard globally).

3. KoraPay
Website: korapay.com | Docs: docs.korapay.com
Sandbox: Available
Primary Markets: Nigeria, Kenya, Ghana, and expanding

What It Does - KoraPay is a newer entrant (founded 2020) built explicitly with a developer-first philosophy. It provides API-based financial infrastructure for collections, payouts, and cross-border payments. 
Where the bigger players can sometimes feel like they're built for enterprise, KoraPay has earned a reputation among startups for clean APIs, responsive support, and straightforward onboarding.
It supports bank transfers, card payments, and mobile money, and is particularly popular among fintechs, e-commerce platforms, and lending companies that want to embed payments without the complexity overhead.

Sample: Collect a Payment (JavaScript)
javascriptconst response = await fetch('https://api.korapay.com/merchant/api/v1/charges/initialize', {
  method: 'POST',
  headers: {
    'Authorization': `Bearer ${process.env.KORA_SECRET_KEY}`,
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    amount: 3000,
    currency: 'NGN',
    reference: 'kora_ref_001',
    customer: {
      email: 'buyer@example.com'
    },
    notification_url: 'https://yourapp.com/webhooks/kora'
  })
});

const data = await response.json();
console.log(data.data.checkout_url); // send user here

When to pick KoraPay: If you're a startup that values quick, uncomplicated onboarding and want developer support that actually responds to your tickets.
