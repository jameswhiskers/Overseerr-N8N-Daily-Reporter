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

-Less mode
```json


{
"output": {
  "date": "2025-10-22",
  "stalled_count":2,
  "stalled_over_date": [
    {
      "originalTitle": "Needful Things",
      "mediaType": "movie",
      "createdDate": "2025-08-16T02:41:16.000Z",
      "daysSinceRequested": 66
    },
    {
      "originalTitle": "Would You Rather",
      "mediaType": "movie",
      "createdDate": "2025-08-16T02:40:43.000Z",
      "daysSinceRequested": 66
    }
  ]
  }
}
```

- More Mode
```json
{
"output": {
  "date": "2025-10-22",
  "stalled_count": 2,
  "stalled_over_date": [
    {
      "movieId": 33,
      "tmdbId": 1428,
      "mediaType": "tv",
      "status": "approved",
      "downloadStatus": 4,
      "requestedBy": "ThatAutisticGuy",
      "originalTitle": "MythBusters",
      "createdDate": "2025-10-12T08:00:45.000Z",
      "daysSinceRequested": 9
    },
    {
      "movieId": 7,
      "tmdbId": 97051,
      "mediaType": "movie",
      "status": "approved",
      "downloadStatus": 3,
      "requestedBy": null,
      "originalTitle": "Would You Rather",
      "createdDate": "2025-08-16T02:40:43.000Z",
      "daysSinceRequested": 66
    }
    ]
  }
}
