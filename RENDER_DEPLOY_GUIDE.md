# 🚀 Render + UptimeRobot 24/7 Hosting Guide (Ultra-Lightweight NC Bot)

यह गाइड आपको सिखाती है कि इस बॉट को **Render.com के Free 512 MB Plan** पर कैसे होस्ट करें और **UptimeRobot** के साथ कैसे 4-5+ महीने बिना रुके 24/7 लगातार चालू रखें।

---

## 🛠️ Step 1: जरूरी फाइल्स (Files Required)

आपके पास ये फाइलें होनी चाहिए (जो प्रोजेक्ट में पहले से तैयार हैं):
1. **`bot.py`** — मेन अल्ट्रा-लाइटवेट बॉट स्क्रिप्ट (जिसमें इनबिल्ट HTTP Health Server और Daily 6 PM Purge शामिल है)।
2. **`requirements.txt`** — डिपेंडेंसी (`httpx>=0.27.0`)।
3. **`Procfile`** — `web: python bot.py`।
4. **`render.yaml`** — Render सर्विस कॉन्फ़िगरेशन।

---

## ☁️ Step 2: Render.com पर Web Service बनाना

1. [Render.com](https://render.com) पर जाएं और लॉगिन/साइन-अप करें (Free Account)।
2. ऊपर दाईं ओर **`New +`** बटन दबाएं और **`Web Service`** चुनें।
3. अपना GitHub Repository सेलेक्ट करें (या "Public Git repository" चुनकर अपना रेपो लिंक डालें)।
4. निम्नलिखित सेटिंग्स भरें:
   - **Name:** `ultra-fast-nc-bot` (या कोई भी नाम)
   - **Region:** `Singapore` या `Frankfurt` (Asia/India के लिए Singapore सबसे तेज़ है)
   - **Branch:** `main`
   - **Runtime:** `Python 3`
   - **Build Command:** `pip install -r requirements.txt`
   - **Start Command:** `python bot.py`
   - **Instance Type:** `Free` (0.1 CPU, 512 MB RAM)

---

## 🔐 Step 3: Environment Variables (ENV) डालना

Render डैशबोर्ड में नीचे स्क्रॉल करें या **Environment** टैब में जाएं और ये 3 Variables जोड़ें:

| Key | Example Value | Description |
|---|---|---|
| `OWNER_ID` | `7878403531` | आपका Telegram User ID |
| `BOT_TOKENS` | `token1,token2,token3...` | आपके 10 बॉट टोकन्स (कॉमा से अलग) |
| `PORT` | `10000` | Render का इंटरनल वेब पोर्ट |

फिर **`Create Web Service`** बटन पर क्लिक करें। 
Render 1-2 मिनट में ऐप बिल्ड करके डिप्लॉय कर देगा और आपको एक URL देगा, जैसे:
👉 `https://ultra-fast-nc-bot.onrender.com`

---

## ⏱️ Step 4: UptimeRobot से 24/7 चालू रखना (Keep-Alive)

### Render Free Plan की सच्चाई:
Render के Free Plan में अगर 15 मिनट तक कोई HTTP विज़िट नहीं आती, तो सर्वर **स्लीप (Spin down)** हो जाता है।

### UptimeRobot इसे 24/7 कैसे चलाता है:
हमारे `bot.py` में **Port 10000 पर एक सुपर-लाइटवेट HTTP Server** इनबिल्ट है। जब UptimeRobot हर 5 मिनट में पिंग करता है, तो बॉट तुरंत `200 OK` रिस्पॉन्स देता है। Render को लगता है कि वेबसाइट पर ट्रैफिक आ रहा है, इसलिए Render इसे **1 सेकंड के लिए भी स्लीप नहीं करता!**

### UptimeRobot सेटअप करने के आसान स्टेप्स:
1. [UptimeRobot.com](https://uptimerobot.com) पर जाएं (फ्री अकाउंट बनाएं)।
2. **`+ Add New Monitor`** पर क्लिक करें।
3. निम्नलिखित सेटिंग्स भरें:
   - **Monitor Type:** `HTTP(s)`
   - **Friendly Name:** `NC Bot Render`
   - **URL (or IP):** अपना Render URL पेस्ट करें (उदा: `https://ultra-fast-nc-bot.onrender.com`)
   - **Monitoring Interval:** `Every 5 minutes` (Recommended)
   - **Monitor Timeout:** `30 seconds`
4. **`Create Monitor`** बटन पर क्लिक करें।

---

## 📊 Step 5: बॉट में चेक करना

टेलीग्राम में बॉट को किसी भी ग्रुप में जोड़ें और एडमिन बनाकर ये कमांड भेजें:

- **`/nc <text>`** : 1 NC हर 2 सेकंड में नॉन-स्टॉप लगातार बदलेगा (0% फ़्लड) [Owner / Sudo Users Only]।
- **`/nc stop`** : ग्रुप में NC रोक देगा [Owner / Sudo Users Only]।
- **`/allgc <text>`** : सभी कनेक्टेड ग्रुप्स में एक साथ NC चलाएगा [Owner / Sudo Users Only]।
- **`/addsudo <user_id>`** : किसी भी यूज़र को Sudo बनाएगा (कमांड या उसके मैसेज को रिप्लाई करके) [Owner Only]।
- **`/delsudo <user_id>`** : Sudo यूज़र को हटाएगा [Owner Only]।
- **`/sudolist`** : सभी एक्टिव Sudo यूज़र्स की लिस्ट दिखाएगा [Owner Only]।
- **`! usage`** : Render की 512 MB मेमोरी में से कितना RAM खर्च हुआ, कितना डाटा और कितने Sudo यूज़र्स हैं, यह रियल टाइम में दिखाएगा! [Strictly Owner Only — Sudo या कोई अन्य यूज़र इसे नहीं देख सकता]।

### 💡 Daily 6:00 PM Purge:
हर शाम 6:00 PM IST को बॉट बिना रुके अपने सभी अनयूज़्ड कैशे और फाइल्स को ऑटो-क्लीन कर देता है, जिससे मेमोरी कभी भी 25 MB से ऊपर नहीं जाती! Sudo लिस्ट `sudo_users.txt` में सेफ रहती है।

---

## 🛡️ 5 Golden Rules: आपकी स्क्रिप्ट कभी बंद क्यों नहीं होगी?

1. **1 Render Account = 1 Bot (Best Strategy):**
   Render हर फ्री अकाउंट को 750 घंटे प्रति माह देता है (1 महीने में 720-744 घंटे होते हैं)। जब आप हर बॉट के लिए एक अलग Render अकाउंट रखते हैं, तो आपका 750 घंटे का कोटा पूरे 30-31 दिन कभी खत्म नहीं होगा!
2. **UptimeRobot Ping (Never Sleep):**
   Render का नियम है कि 15 मिनट बिना ट्रैफिक के स्लीप हो जाना। UptimeRobot हर 5 मिनट में `/` या `/healthz` पर पिंग करता है, इसलिए Render कभी स्लीप नहीं होता।
3. **512 MB Crash Prevention (Ultra-Lightweight):**
   बॉट केवल ~20 MB RAM लेता है (512 MB की लिमिट का मात्र 4%)। शाम 6 बजे ऑटो-रिफ्रेश भी होता है, इसलिए Out-of-Memory (OOM) क्रैश का 0% चांस है।
4. **Try-Except Error Immunity:**
   Telegram की तरफ से कोई भी नेटवर्क ड्रॉप या FloodWait एरर आने पर स्क्रिप्ट बंद नहीं होती, बल्कि 2 सेकंड रुककर अपने-आप रीकनेक्ट हो जाती है।
5. **Telegram Group Admin Rights:**
   जिस ग्रुप या VCB में बॉट चल रहा है, वहां बॉट के पास **"Change Group Info"** (ग्रुप की जानकारी बदलना) का अधिकार हमेशा ऑन रखें।
