# 🌐 CDN IP Finder for ShirOKhorshid

<div align="center">

![License](https://img.shields.io/badge/license-GPL--3.0-blue)
![Shell](https://img.shields.io/badge/language-Bash-green)
![Iran](https://img.shields.io/badge/tested%20from-Iran-red)

**[English](#english) | [فارسی](#فارسی)**

</div>

---

## English

### What is This?

This project provides bash scripts to automatically find, collect and test CDN edge IPs (Akamai, Google, Amazon CloudFront, Microsoft Azure) that are **not blocked in Iran**, so they can be used for **CDN Fronting** in [ShirOKhorshid](https://github.com/shirokhorshid/shirokhorshid-android) — a community fork of the Psiphon Android client.

---

### How CDN Fronting Works

```
[User in Iran]
      ↓  connects to Google/Akamai IP (not blocked)
[CDN Edge IP]
      ↓  CDN forwards traffic internally
[Psiphon Server]
      ↓
[Free Internet 🌍]
```

Iran's firewall **cannot block** major CDN IPs like Akamai or Google without also breaking thousands of legitimate Iranian websites and services. CDN Fronting exploits this by hiding Psiphon traffic behind these unblockable IPs.

The trick is:
- **SNI** (what the firewall sees): a safe CDN domain like `a248.e.akamai.net`
- **Host header** (encrypted, what CDN sees): the actual Psiphon server
- The CDN forwards the request internally to Psiphon

---

### Supported CDNs

| CDN | Status | Why Hard to Block |
|-----|--------|-------------------|
| **Akamai** | ✅ Usually works | Powers 30%+ of global internet |
| **Google** | ✅ Usually works | Gmail, Search — blocking = chaos |
| **Amazon CloudFront** | ⚠️ Sometimes works | AWS powers half the internet |
| **Microsoft Azure** | ⚠️ Sometimes works | Office365, Teams widely used in Iran |

---

### Scripts

#### 1. `scripts/akamai_finder.sh` — Quick Akamai IP Finder
Resolves 20+ major Akamai-hosted domains and tests IPs from **your own machine**.

- ✅ Fast (~2 minutes)
- ✅ Good for a quick initial list
- ❌ Does NOT verify from inside Iran

```bash
chmod +x scripts/akamai_finder.sh
./scripts/akamai_finder.sh
```

---

#### 2. `scripts/akamai_iran_checker.sh` — Akamai Iran Tester
Tests Akamai IPs specifically from **real Iranian nodes** via check-host.net API.

- ✅ Accurate — tested from inside Iran
- ✅ Shows which Iranian nodes can reach each IP
- ❌ Slower (~10-15 minutes for full list)

```bash
chmod +x scripts/akamai_iran_checker.sh
./scripts/akamai_iran_checker.sh
```

---

#### 3. `scripts/cdn_iran_checker.sh` — Full CDN Iran Checker ⭐ Recommended
Tests **all major CDNs** from Iranian nodes. The most complete tool.

- ✅ Tests Akamai, Google, Amazon AND Azure
- ✅ First checks which CDNs are accessible in Iran
- ✅ Collects IPs only from accessible CDNs
- ✅ Tests each IP from 4 Iranian nodes (Tehran, Isfahan, Mashhad)
- ✅ Shows response time per Iranian node
- ✅ Outputs ready-to-paste comma-separated list
- ❌ Takes 15-30 minutes for full run

```bash
chmod +x scripts/cdn_iran_checker.sh
./scripts/cdn_iran_checker.sh
```

---

### How Testing Works (check-host.net)

```
Your Machine (outside Iran)
      ↓ sends API request
check-host.net
      ↓ forwards TCP test to Iranian servers
┌──────────────────────────────────────┐
│ ir1.node.check-host.net  → Tehran   │
│ ir2.node.check-host.net  → Tehran   │
│ ir3.node.check-host.net  → Isfahan  │
│ ir4.node.check-host.net  → Mashhad  │
└──────────────────────────────────────┘
      ↓ each node tries TCP:443 to the IP
      ↓ reports back: time or error
✅ IP works in Iran  OR  ❌ IP is blocked
```

> **Note:** check-host.net Iranian nodes cover major cities and ISPs but not all of them. For complete coverage across MCI, Irancell and Rightel, community testing from inside Iran is also valuable — see [Contributing](#contributing).

---

### How to Use Results in ShirOKhorshid

1. Run `cdn_iran_checker.sh` and wait for it to finish
2. Copy the comma-separated IP list from output (or from `results/cdn_working_ips.txt`)
3. Open **ShirOKhorshid** on your Android device
4. Go to **Settings**
5. Under the **CDN Fronting** section:
   - Tap **CDN edge IPs** → paste your IP list
   - Tap **CDN SNI hostname** → enter one of:
     ```
     a248.e.akamai.net       ← for Akamai IPs
     www.googleapis.com      ← for Google IPs
     ajax.aspnetcdn.com      ← for Azure IPs
     ```
6. Set **Connection Protocol** → **CDN Fronting**
7. Hit connect!

---

### Requirements

```bash
# Ubuntu / Debian
sudo apt install curl dnsutils python3 -y

# CentOS / RHEL / Fedora
sudo yum install curl bind-utils python3 -y
```

---

### Project Structure

```
cdn-ip-finder/
├── scripts/
│   ├── akamai_finder.sh         # Quick Akamai IP finder (local test)
│   ├── akamai_iran_checker.sh   # Akamai tester via Iranian nodes
│   └── cdn_iran_checker.sh      # Full CDN checker via Iranian nodes ⭐
├── results/
│   └── .gitkeep                 # Working IPs get saved here
├── docs/
│   └── how-it-works.md          # Technical deep dive
├── README.md                    # This file
└── LICENSE                      # GPL-3.0
```

---

### Contributing

If you have tested IPs from inside Iran on specific ISPs, please open an Issue with:

- Which **ISP** you tested on (MCI / Irancell / Rightel / Shatel / etc)
- Which **IPs** worked and which didn't
- **Date** of testing (IPs rotate frequently!)
- Your **city** (blocking can differ by region)

Community data from real Iranian users is the most valuable contribution!

---

### Related Projects

- [ShirOKhorshid Android](https://github.com/shirokhorshid/shirokhorshid-android)
- [Psiphon](https://psiphon.ca)

---

### License

GPL-3.0 — same license as ShirOKhorshid and Psiphon

---
---

## فارسی

### این پروژه چیست؟

این پروژه اسکریپت‌های bash برای پیدا کردن و تست کردن آی‌پی‌های CDN (آکامای، گوگل، آمازون CloudFront، مایکروسافت Azure) ارائه می‌دهد که در ایران **فیلتر نشده‌اند** تا بتوان از آن‌ها برای **CDN Fronting** در اپلیکیشن [شیروخورشید](https://github.com/shirokhorshid/shirokhorshid-android) استفاده کرد.

---

### CDN Fronting چطور کار می‌کند؟

```
[کاربر در ایران]
      ↓  به آی‌پی گوگل/آکامای وصل می‌شود (فیلتر نیست)
[آی‌پی CDN]
      ↓  CDN ترافیک را داخلاً فوروارد می‌کند
[سرور سایفون]
      ↓
[اینترنت آزاد 🌍]
```

فایروال ایران **نمی‌تواند** آی‌پی‌های CDN‌های بزرگ مثل Akamai یا Google را بلاک کند چون بلاک کردن آن‌ها هزاران سایت و سرویس مشروع ایرانی را هم از دسترس خارج می‌کند.

ترفند اینجاست:
- **SNI** (چیزی که فایروال می‌بیند): یک دامنه CDN بی‌خطر مثل `a248.e.akamai.net`
- **Host header** (رمزگذاری‌شده، چیزی که CDN می‌بیند): سرور واقعی سایفون
- CDN درخواست را داخلاً به سایفون فوروارد می‌کند

---

### CDN‌های پشتیبانی‌شده

| CDN | وضعیت | چرا بلاک کردن سخت است |
|-----|--------|----------------------|
| **Akamai** | ✅ معمولاً کار می‌کند | بیش از ۳۰٪ اینترنت جهانی را پشتیبانی می‌کند |
| **Google** | ✅ معمولاً کار می‌کند | Gmail، Search — بلاک کردن = فاجعه |
| **Amazon CloudFront** | ⚠️ گاهی کار می‌کند | AWS نیمی از اینترنت را پشتیبانی می‌کند |
| **Microsoft Azure** | ⚠️ گاهی کار می‌کند | Office365 و Teams در ایران زیاد استفاده می‌شود |

---

### اسکریپت‌ها

#### ۱. `scripts/akamai_finder.sh` — جستجوگر سریع آی‌پی‌های Akamai
بیش از ۲۰ دامنه مشهور روی Akamai را resolve کرده و آی‌پی‌ها را از **دستگاه خودتان** تست می‌کند.

- ✅ سریع (~۲ دقیقه)
- ✅ مناسب برای یک لیست اولیه سریع
- ❌ از داخل ایران تأیید نمی‌کند

```bash
chmod +x scripts/akamai_finder.sh
./scripts/akamai_finder.sh
```

---

#### ۲. `scripts/akamai_iran_checker.sh` — تستر ایرانی Akamai
آی‌پی‌های Akamai را از **نودهای واقعی ایرانی** از طریق API سایت check-host.net تست می‌کند.

- ✅ دقیق — از داخل ایران تست می‌شود
- ✅ نشان می‌دهد کدام نودهای ایرانی به هر آی‌پی دسترسی دارند
- ❌ کندتر (~۱۰-۱۵ دقیقه برای لیست کامل)

```bash
chmod +x scripts/akamai_iran_checker.sh
./scripts/akamai_iran_checker.sh
```

---

#### ۳. `scripts/cdn_iran_checker.sh` — چکر کامل CDN برای ایران ⭐ توصیه‌شده
**همه CDN‌های اصلی** را از نودهای ایرانی تست می‌کند. کامل‌ترین ابزار.

- ✅ Akamai، Google، Amazon و Azure را تست می‌کند
- ✅ ابتدا بررسی می‌کند کدام CDN‌ها در ایران دسترس‌پذیرند
- ✅ آی‌پی‌ها را فقط از CDN‌های دسترس‌پذیر جمع‌آوری می‌کند
- ✅ هر آی‌پی را از ۴ نود ایرانی (تهران، اصفهان، مشهد) تست می‌کند
- ✅ زمان پاسخ هر نود ایرانی را نشان می‌دهد
- ✅ لیست جداشده با کاما آماده برای paste کردن خروجی می‌دهد
- ❌ برای اجرای کامل ۱۵-۳۰ دقیقه طول می‌کشد

```bash
chmod +x scripts/cdn_iran_checker.sh
./scripts/cdn_iran_checker.sh
```

---

### نحوه کار تست (check-host.net)

```
دستگاه شما (خارج از ایران)
      ↓ ارسال درخواست API
check-host.net
      ↓ ارسال تست TCP به سرورهای ایرانی
┌──────────────────────────────────────────┐
│ ir1.node.check-host.net  →  تهران       │
│ ir2.node.check-host.net  →  تهران       │
│ ir3.node.check-host.net  →  اصفهان      │
│ ir4.node.check-host.net  →  مشهد        │
└──────────────────────────────────────────┘
      ↓ هر نود TCP:443 را به آی‌پی امتحان می‌کند
      ↓ نتیجه گزارش می‌شود: زمان یا خطا
✅ آی‌پی در ایران کار می‌کند  یا  ❌ فیلتر است
```

> **نکته:** نودهای ایرانی check-host.net شهرها و اپراتورهای اصلی را پوشش می‌دهند ولی همه را نه. برای پوشش کامل MCI، ایرانسل و رایتل، تست اجتماعی از داخل ایران هم ارزشمند است.

---

### نحوه استفاده از نتایج در شیروخورشید

۱. اسکریپت `cdn_iran_checker.sh` را اجرا کنید و منتظر پایان بمانید
۲. لیست آی‌پی‌های جداشده با کاما را از خروجی کپی کنید (یا از فایل `results/cdn_working_ips.txt`)
۳. اپ **شیروخورشید** را روی گوشی اندروید باز کنید
۴. به **تنظیمات** بروید
۵. در بخش **CDN Fronting**:
   - روی **CDN edge IPs** بزنید ← لیست آی‌پی‌ها را paste کنید
   - روی **CDN SNI hostname** بزنید ← یکی را وارد کنید:
     ```
     a248.e.akamai.net       ← برای آی‌پی‌های Akamai
     www.googleapis.com      ← برای آی‌پی‌های Google
     ajax.aspnetcdn.com      ← برای آی‌پی‌های Azure
     ```
۶. **Connection Protocol** را روی **CDN Fronting** بگذارید
۷. وصل شوید!

---

### پیش‌نیازها

```bash
# اوبونتو / دبیان
sudo apt install curl dnsutils python3 -y

# CentOS / RHEL / Fedora
sudo yum install curl bind-utils python3 -y
```

---

### ساختار پروژه

```
cdn-ip-finder/
├── scripts/
│   ├── akamai_finder.sh         # جستجوگر سریع آکامای (تست محلی)
│   ├── akamai_iran_checker.sh   # تستر آکامای از طریق نودهای ایرانی
│   └── cdn_iran_checker.sh      # چکر کامل CDN از نودهای ایرانی ⭐
├── results/
│   └── .gitkeep                 # آی‌پی‌های کارکرده اینجا ذخیره می‌شوند
├── docs/
│   └── how-it-works.md          # جزئیات فنی
├── README.md                    # این فایل
└── LICENSE                      # GPL-3.0
```

---

### مشارکت

اگر از داخل ایران روی اپراتورهای مختلف آی‌پی‌ها را تست کرده‌اید، یک Issue باز کنید و اشتراک بگذارید:

- روی کدام **اپراتور** تست کردید (MCI / ایرانسل / رایتل / شاتل / ...)
- کدام **آی‌پی‌ها** کار کردند و کدام‌ها نه
- **تاریخ** تست (آی‌پی‌ها مرتباً تغییر می‌کنند!)
- **شهر** شما (فیلترینگ ممکن است بر حسب منطقه فرق داشته باشد)

داده‌های اجتماعی از کاربران واقعی ایرانی ارزشمندترین مشارکت است!

---

### پروژه‌های مرتبط

- [شیروخورشید اندروید](https://github.com/shirokhorshid/shirokhorshid-android)
- [سایفون](https://psiphon.ca)
- [گزارش‌های OONI برای ایران](https://ooni.org/countries/ir)
- [بحث‌های ایران در net4people/bbs](https://github.com/net4people/bbs)

---

### لایسنس

GPL-3.0 — مثل شیروخورشید و سایفون
