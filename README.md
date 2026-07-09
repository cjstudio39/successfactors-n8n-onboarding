# SAP SuccessFactors → n8n: Onboarding Automation Workflow

Connect **SAP SuccessFactors Onboarding** to the outside world — **without BTP, CPI, or any middleware license**. When a candidate completes their document signature, this n8n workflow instantly:

- 📧 Emails the candidate a personalized survey (pre-filled Google Form link)
- ✈️ Alerts your HR Telegram group that the record is now waiting in **Manage Pending Hire (MPH)**

Built with **Integration Center** (already included in your SF subscription) and a self-hosted **n8n** instance.

▶️ **Watch the full walkthrough:** [YouTube video](YOUR_YOUTUBE_LINK)
📄 **Guide + download page:** [cjstudio.site/sapsf-to-n8n](https://cjstudio.site/sapsf-to-n8n/)

## How it works

```
SAP SuccessFactors                          n8n (self-hosted)
┌─────────────────┐    ICS outbound    ┌─────────┐   ┌──────────────────┐
│ Candidate signs │ ───integration───▶ │ Webhook │──▶│ 📧 Survey email  │
│ onboarding docs │    (POST + Basic   │ (Basic  │   ├──────────────────┤
│ → record lands  │     Auth)          │  Auth)  │──▶│ ✈️ Telegram alert │
│ in MPH          │                    └─────────┘   └──────────────────┘
└─────────────────┘
```

## Repo contents

`SFONB_Survey_template.json` — the n8n workflow, import-ready. All credentials and endpoints are placeholders.

## Setup (5 steps)

1. **Import** — n8n → Workflows → Import from File → `SFONB_Survey_template.json`. It imports inactive; keep it that way until configured.
2. **Webhook** — open the Webhook node, keep or rename the `sf-onboarding-survey` path, create a Basic Auth credential (any username/password), attach it, then activate and copy the **production** webhook URL.
3. **Credentials** — attach your own SMTP/Gmail credential on the *Send email* node (set `<YOUR_SENDER_EMAIL>`); create a Telegram bot via [@BotFather](https://t.me/botfather), attach the token, and set `<YOUR_TELEGRAM_CHAT_ID>` (add the bot to your HR group, then read the chat ID from `https://api.telegram.org/bot<TOKEN>/getUpdates`).
4. **Google Form** — in the *Edit Fields* node, replace `<YOUR_GOOGLE_FORM_ID>` and `<GOOGLE_FORM_EMAIL_ENTRY_ID>` with your own form's values (use the form's *Get pre-filled link* to find the `entry.XXXXXXXXX` ID).
5. **SuccessFactors** — in Integration Center, create an outbound integration triggered on the onboarding document-signed event. Map **exactly** these field names: `Email`, `FirstName`, `LastName` (the Code node reads `body.Email`, etc.). Destination = your n8n production webhook URL, with the Basic Auth from step 2. The trigger and mapping are shown in the [video](YOUR_YOUTUBE_LINK).

## Test without SF

```bash
curl -X POST https://YOUR-N8N-HOST/webhook/sf-onboarding-survey \
  -u your_user:your_password \
  -H "Content-Type: application/json" \
  -d '{"Email":"jane.doe@example.com","FirstName":"Jane","LastName":"Doe"}'
```

## ⚠️ Demo, not production

This is a working demo. A real client deployment also needs webhook payload validation, error handling and retries, monitoring, and a data privacy review — deliberately out of scope here.

## About

I'm **CJ Chew** — independent HR tech advisor in Malaysia, 8+ years of SAP SuccessFactors implementation (EC, RCM, ONB, LMS). I help businesses connect their HR systems without expensive middleware — vendor-neutral, from enterprise SF landscapes to SME setups.

🌐 [cjstudio.site](https://cjstudio.site) · 📩 cjchew@cjstudio.site · 💼 [LinkedIn](https://www.linkedin.com/in/cjchew9319)

## License

MIT — use it, modify it, ship it. Attribution appreciated.
