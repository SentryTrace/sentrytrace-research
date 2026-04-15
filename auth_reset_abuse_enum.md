# 🔐 Authentication Abuse Chain : User Enumeration & Reset Flooding

## 📌 Context

During a targeted assessment of a web application's authentication layer, a chain of weaknesses was identified in the password reset mechanism.

The findings demonstrate how small logic flaws in authentication flows can be combined into a scalable abuse vector.

---

## ⚙️ Vulnerability Chain

### 1. User Enumeration

The authentication system returns distinguishable responses based on account existence.

#### Observation:

- Valid account → success response
- Invalid account → error response

#### Impact:

- Allows attackers to build a list of valid users
- Enables targeted attacks

---

### 2. Reset Endpoint Abuse

The password reset endpoint can be triggered repeatedly without restriction.

#### Behavior:

- No rate limiting detected
- No CAPTCHA / challenge mechanism
- No anomaly detection

---

### 3. Lack of Protective Controls

No defensive layers were observed:

- No IP throttling
- No request limiting
- No behavioral detection

---

## 🔍 Technical Illustration (Anonymized)

### Endpoint Pattern

POST /api/auth/forgot-password

---

### Sample Request

```json
{
  "email": "target@example.com"
}


⸻

Differential Responses

Valid Account

{
  "ok": true
}

Invalid Account

{
  "error": "Invalid identifier"
}

➡️ This discrepancy enables user enumeration.

⸻

🔁 Abuse Flow

[ Attacker ]
     ↓
Enumerate valid emails
     ↓
Select target accounts
     ↓
Trigger repeated reset requests
     ↓
[ Victim Inbox Flooded ]


⸻

⚡ Proof of Concept (Simplified)

for i in {1..15}
do
  curl -X POST https://target/api/auth/forgot-password \
  -H "Content-Type: application/json" \
  -d '{"email":"target@example.com"}'
done


⸻

Result:
	•	Multiple reset emails triggered
	•	No blocking observed
	•	No rate limiting activated

⸻

📊 Impact Assessment

Technical Impact
	•	Authentication logic weakness
	•	API abuse vector
	•	Lack of anti-automation controls

⸻

Business Impact
	•	User experience degradation
	•	Email infrastructure abuse
	•	Targeted harassment potential
	•	Reputation risk

⸻

🛡️ Mitigation Recommendations
	•	Normalize authentication responses
	•	Implement rate limiting (per IP + per account)
	•	Introduce CAPTCHA / challenge after threshold
	•	Monitor anomalous patterns (burst requests)

⸻

 🔐 Key Insight

Authentication endpoints are often treated as low-risk, but:

Small response differences + missing rate limits = scalable attack surface

⸻

🧾 Disclosure Notes
	•	Target has been fully anonymized
	•	Testing performed with minimal requests
	•	No real user data exposed
	•	No exploitation beyond validation

⸻

⚙️ SentryTrace Perspective

This pattern is detectable through:
	•	Response diffing
	•	Auth endpoint fuzzing
	•	Behavioral analysis of reset flows

⸻

🧠 Author

SentryTrace Research
Focus: API security, authentication abuse, offensive automation

Curious how others approach detection of these patterns in modern APIs.
