# Overseerr N8N Daily Reporter 

This n8n workflow nags Overseerr until it gives up its secrets.  
It pokes your server daily (or whenever you feel like it) to tell you what’s still pending, what’s stalled, and what’s been lounging in “processing” purgatory longer than it should.

---

## 🧠 What It Does

- Fetches your Overseerr requests and media data via the API.  
- Calculates how many are **pending**, **approved**, **available**, and **stalled**.  
- Cross-checks stalled items to see how long they’ve been hanging around.  
- Ignores new requests until they’ve aged beyond your patience (`warnAfter` days).  
- Outputs clean JSON you can feed into another workflow or notification system.  
  (e.g. Telegram, Discord, carrier pigeons — whatever n8n can automate.)

---

## ⚙️ Settings Node

Edit the **`settings`** node to tell it how to behave:

| Field | Description |
|-------|--------------|
| `endpoint` | Your Overseerr base URL (e.g. `http://192.168.1.50:5055`) |
| `key` | Overseerr API key |
| `lessDetail` | `true` = light mode using `/media`; `false` = heavy mode using `/request` |
| `warnAfter` | Number of days before a stalled request gets reported |

---

## 🧩 Key Nodes

| Node | Purpose |
|------|----------|
| **settings** | Config storage — your one-stop tweak shop |
| **Switch / Switch1** | Chooses “less” or “more” detail paths |
| **Process Overseerr Data** | Turns the Overseerr JSON mess into something sane |
| **Loop Over Items** | Goes full Groundhog Day through your stalled media |
| **movieOrTv** | Decides which API endpoint to hit |
| **GoodThingsComeToThoseWhoWait** | Rate-limits the Overseerr poking (because kindness is a virtue) |
| **CalculateDaysSince** | Figures out how long ago each request was made |
| **IfOverXDays** | Filters out the fresh ones so you don’t get spammed |
| **Aggregate / SendIT** | Smashes everything together into one neat JSON blob for your next workflow |
| **Grimm (optional)** | Your sarcastic narrator AI, who yells when you break things |

---

## 🧰 Setup / Requirements

- Copy pasta into a **fresh n8n workflow** (you have that right?),  
  then edit the `settings` node so it knows where your Overseerr server is hiding.  
  Add the API key so the poking can begin.  
- Add a **Trigger node** — use case may vary,  
  but a simple **cron job** works great.  
  Or get fancy and hook it into a bot command.  
- Choose your **Output** — personal flavour here.  
  Send the report however you like: Telegram, Discord, smoke signals.  
  Mine goes through an AI semantic notification rewriter that abuses me  
  for refusing to get faster internet.

---

## 💬 Output Example

```json
{
  "date": "2025-10-22",
  "pending_count": 2,
  "approved_count": 8,
  "available_count": 15,
  "stalled_count": 3,
  "stalled_items": [
    {
      "title": "Dune: Part Two",
      "mediaType": "movie",
      "status": "approved",
      "downloadStatus": 2,
      "requestedBy": "jamie",
      "daysSinceRequested": 67
    }
  ]
}
