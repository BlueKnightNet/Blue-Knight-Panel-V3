# Blue Knight Gate — Replit Edition

پنل VLESS / VMess / Trojan / XHTTP روی Replit — **ایمپورت کن، Publish کن؛ متغیری لازم نیست.**

A VLESS / VMess / Trojan / XHTTP panel for Replit — **import, publish, done; no variables needed.**

## فارسی

### راه‌اندازی
1. توی Replit: **Create App → Import from GitHub** → این ریپو (یا فورک خودت).
2. **Publish** رو بزن (Autoscale یا Reserved VM). یه آدرس `https://<name>.replit.app` می‌گیری.
3. برو به `https://<name>.replit.app/knight` و رمز پنل رو بساز.
4. اگه `replit.app` توی ایران فیلتره، یه **Cloudflare Worker** بساز (پایین‌تر).
5. پنل رو از آدرس Worker باز کن (`https://<worker>.workers.dev/knight`)؛ لینک‌ها خودکار روی آدرس Worker میرن.
6. کانفیگ‌ها یا لینک ساب رو کپی کن و توی کلاینتت وارد کن.

پورت، دامنه و TLS خودکار تنظیم میشن: همه‌ی لینک‌ها پورت `443` با TLS هستن.

### Cloudflare Worker (وقتی replit.app فیلتره)
1. **dash.cloudflare.com → Workers & Pages → Create → Worker** → Deploy.
2. **Edit code**، کد زیر رو جایگزین کن، `ORIGIN` رو آدرس `replit.app` خودت بذار و **Deploy** کن.
3. پنل رو با `https://<worker>.workers.dev/knight` باز کن و کانفیگ‌ها رو دوباره کپی کن.

```js
const ORIGIN = "YOUR-APP.replit.app";
export default {
  async fetch(request) {
    const url = new URL(request.url);
    const relayHost = url.hostname;
    url.hostname = ORIGIN; url.protocol = "https:"; url.port = "";
    const headers = new Headers(request.headers);
    headers.set("Host", ORIGIN);
    headers.set("X-Forwarded-Host", relayHost);
    return fetch(new Request(url, { method: request.method, headers, body: request.body, redirect: "manual" }));
  },
};
```

پنل آدرس Worker رو از `X-Forwarded-Host` می‌فهمه و ذخیره می‌کنه. اگه خواستی دستی تعیین کنی، Secret به اسم `DOMAIN` با مقدار آدرس Worker بذار (اولویت با `DOMAIN` هست).

به‌صورت زنده تست شده: VLESS-WS-TLS و VMess-WS-TLS از طریق Worker کار می‌کنن.

### مصرف ترافیک
تب **Usage** توی پنل مصرف هر کانفیگ (VLESS WS، VMess WS، Trojan WS، XHTTP) رو جدا نشون میده: آپلود، دانلود و جمع، برای امروز، این ماه (از ساعت 00:00 UTC روز اول ماه، مثل Replit) و کل زمان، همراه سرعت لحظه‌ای.
- این عدد **تخمینیه**؛ عدد واقعی صفحه‌ی **Account → Usage** توی Replit هست.
- شمارنده توی پوشه‌ی داده ذخیره میشه: بعد از ری‌استارت می‌مونه، ولی با هر **Publish** صفر میشه. با فیلد **Sync** عدد ماه رو با Replit یکی کن (Secret لازم نیست).
- اگه Secret `TRAFFIC_LIMIT_GB` رو بذاری (مثلاً `100`)، نوار پیشرفت و مقدار باقی‌مونده نشون داده میشه و توی ۸۰٪ و ۱۰۰٪ هشدار میده.
- دکمه‌ی **Reset counter** همه‌ی شمارنده‌ها رو صفر می‌کنه (فقط بعد از ورود به پنل).

### کدوم لینک؟
- **VLESS-WS-TLS** و **VMess-WS-TLS**: همه‌ی کلاینت‌ها (v2rayNG، v2rayN، Hiddify، NekoBox، sing-box، Clash Meta).
- **Trojan-WS-TLS**: بیشتر کلاینت‌ها.
- **VLESS-XHTTP-TLS**: فقط کلاینت‌های با هسته‌ی **Xray** (v2rayNG، v2rayN، Hiddify با هسته‌ی Xray).
- لینک ساب یه `token` خصوصی داره؛ پخشش نکن.

### مشکل داری؟
- **آدرس `.replit.dev` کار نمی‌کنه:** اون آدرس خصوصی محیط ویرایشه. حتماً **Publish** کن و از `.replit.app` یا Worker استفاده کن.
- **فقط کانفیگ‌های TLS (پورت 443):** روی Replit فقط 443 عمومیه؛ کانفیگ بدون TLS هیچ‌وقت وصل نمیشه.
- **تست:** از **Real delay** یا تست URL استفاده کن، نه TCP ping.
- **IP تمیز:** فقط address رو با یه IP تمیز کلادفلر (از اسکنر) عوض کن؛ `sni` و `host` روی آدرس Worker بمونن.
- **بعد از هر Publish رمز و کانفیگ‌ها عوض میشن:** فایل‌های اپ Publish‌شده روی Replit موندگار نیستن. برای ثابت موندن، توی **Publishing → Secrets** این‌ها رو بذار: `UUID`، `PANEL_PASSWORD`، `SUB_TOKEN`.

## English

### Setup
1. Replit: **Create App → Import from GitHub** → this repo (or your fork).
2. Click **Publish** (Autoscale or Reserved VM). You get `https://<name>.replit.app`.
3. Open `https://<name>.replit.app/knight` and create the panel password.
4. If `replit.app` is filtered (e.g. in Iran), create a **Cloudflare Worker** (below).
5. Open the panel through the Worker (`https://<worker>.workers.dev/knight`); links switch to the Worker address automatically.
6. Copy the configs or the subscription link into your client.

Port, domain and TLS are set automatically: every link uses port `443` with TLS.

### Cloudflare Worker (when replit.app is filtered)
1. **dash.cloudflare.com → Workers & Pages → Create → Worker** → Deploy.
2. **Edit code**, paste the code above (Persian section), set `ORIGIN` to your `replit.app` address, then **Deploy**.
3. Open `https://<worker>.workers.dev/knight` and copy the configs again.

The panel learns the Worker address from `X-Forwarded-Host` and saves it. To set it by hand, add a `DOMAIN` Secret with the Worker address (`DOMAIN` always wins).

Tested live: VLESS-WS-TLS and VMess-WS-TLS work through the Worker.

### Traffic usage
The panel's **Usage** tab shows traffic per config (VLESS WS, VMess WS, Trojan WS, XHTTP): upload, download and total for today, this month (from 00:00 UTC on the 1st, like Replit's meter) and all time, plus live speed.
- It's an **estimate**; Replit's **Account → Usage** page is the real meter.
- The counter is saved in the data folder: it survives restarts but resets on every **Publish**. Use the **Sync** field to set this month's number from Replit (no Secret needed).
- Set the `TRAFFIC_LIMIT_GB` Secret (e.g. `100`) to get a progress bar with the remaining amount and warnings at 80% and 100%.
- **Reset counter** clears all counters (panel login required).

### Which link?
- **VLESS-WS-TLS** / **VMess-WS-TLS**: any client (v2rayNG, v2rayN, Hiddify, NekoBox, sing-box, Clash Meta).
- **Trojan-WS-TLS**: most clients.
- **VLESS-XHTTP-TLS**: **Xray-core** clients only (v2rayNG, v2rayN, Hiddify with the Xray core).
- Subscription URLs contain a private `token` — don't share them.

### Troubleshooting
- **The `.replit.dev` URL doesn't work:** it's the private editor URL. **Publish** and use `.replit.app` or the Worker.
- **Use only the TLS configs (port 443):** only 443 is public on Replit; non-TLS configs never connect.
- **Testing:** use **Real delay** or the URL test, not TCP ping.
- **Clean IP:** change only the address to a clean Cloudflare IP (from a scanner); keep `sni` / `host` on the Worker address.
- **Password / configs change after every Publish:** a published Replit app's files are not persistent. To keep them, add these in **Publishing → Secrets**: `UUID`, `PANEL_PASSWORD`, `SUB_TOKEN`.

<details>
<summary><b>Optional settings (not required) / تنظیمات اختیاری (لازم نیست)</b></summary>

Replit → **Publishing → Secrets** (deployment Secrets are separate from the editor's; publish again after changing them). / همه اختیاری‌ان.

| Secret | Default | Meaning |
|---|---|---|
| `DOMAIN` | `replit.app` address / Worker address you open the panel with | Force the domain used in links (bare hostname) |
| `UUID` | random, saved in the data folder | Fixed UUID so configs survive a new Publish |
| `PANEL_PASSWORD` | – | Fixed panel password (8+ characters); skips the create-password page |
| `SUB_TOKEN` | random | Fixed subscription token so subscription URLs survive a new Publish |
| `SETUP_KEY` | empty | Extra key asked for when creating the password the first time |
| `RESET_PANEL_PASSWORD` | – | `true` → publish → create a new password → **delete the secret** |
| `FORCE_HOST` | – | Force the link address (e.g. a clean IP); SNI/Host stay on `DOMAIN` |
| `LINK_SNI` / `LINK_HOST` | link domain | Separate SNI / WS Host header |
| `LINK_PORT` / `LINK_TLS` | `443` / on | Port in links / TLS on or off (not needed on Replit) |
| `LINK_FP` | `chrome` | uTLS fingerprint: `chrome` `firefox` `safari` `edge` `ios` `android` `random` `randomized` `360` `qq`, or `none` |
| `LINK_ALPN` | `http/1.1` | ALPN for WS links (keep `http/1.1`); `none` = omit |
| `ENABLE_XHTTP` | `true` | `false` = no Xray, no XHTTP link |
| `XRAY_VERSION` / `XRAY_URL` | latest | Pin the Xray-core version / custom zip URL |
| `XHTTP_MODE` | `packet-up` | `packet-up`, `stream-up`, `stream-one` or `auto` |
| `XHTTP_ALPN` | `h2` | ALPN in the XHTTP link |
| `TRAFFIC_LIMIT_GB` | none | Monthly allowance in GiB for the Usage tab (progress bar, warnings at 80% / 100%) |
| `BK_DATA_DIR` | `./bk-data` | Data folder (password, UUID, configs) |
| `PORT` | `8080` | Listening port (matches `.replit`) |

Railway still works as a fallback (same code, `RAILWAY_*` detection).

</details>
