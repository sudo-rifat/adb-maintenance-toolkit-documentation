# ADB Maintenance Toolkit: সম্পূর্ণ ব্যবহার গাইড

**ভার্সন:** 1.0.0
**সাপোর্টেড প্ল্যাটফর্ম:** Linux (Ubuntu), Android Termux (Windows সাপোর্ট ভবিষ্যতে আসবে)
**দরকারি জিনিস:** Python 3.7+ এবং ADB

---

## সূচিপত্র

1. এই Tool কী এবং কী করে
2. শুরু করার আগে যা লাগবে
3. Initial Setup
4. প্রতিবার কীভাবে ব্যবহার করবেন
5. মেনুর প্রতিটি অপশনের ব্যাখ্যা
6. Tool Update করা
7. Uninstall করা
8. Error হলে কীভাবে সমাধান করবেন
9. সচরাচর জিজ্ঞাসা (FAQ)
10. Safety: এই Tool কী কখনো করবে না

---

## ১. এই Tool কী এবং কী করে

**ADB Maintenance Toolkit** একটি ছোট Terminal Tool। এটি আপনার Android ফোনের তথ্য দেখায় এবং নিরাপদভাবে কিছু Maintenance কাজ করে।

**কী কী দেখা যায়:**
- ব্যাটারির অবস্থা, তাপমাত্রা ও পরিসংখ্যান
- RAM ও Storage কতটুকু ব্যবহৃত হচ্ছে
- Download, DCIM, Pictures, Movies, Music ফোল্ডার কত জায়গা নিচ্ছে
- Android Version, Model, Manufacturer
- Installed, Third-party ও System Apps-এর তালিকা

**কী কী করা যায়:**
- Android-এর নিজস্ব পদ্ধতিতে Cache পরিষ্কার (Cache Maintenance)
- অস্থায়ী (Temporary) ফাইল দেখে নিজে বেছে মুছে ফেলা
- ফোন Reboot করা

**বিশেষ সুবিধা:**
- কোনো অতিরিক্ত Python Package Install করতে হয় না
- Linux ও Termux-এ একই Code চলে
- আপনার অনুমতি ছাড়া কোনো ফাইল মোছে না
- Serial Number ডিফল্টভাবে লুকানো থাকে

---

## ২. শুরু করার আগে যা লাগবে

| জিনিস | বিস্তারিত |
|---|---|
| Android ফোন | USB Debugging চালু করা থাকতে হবে |
| Python 3.7 বা নতুন | Ubuntu-তে সাধারণত আগে থেকেই থাকে |
| ADB | Android SDK Platform-Tools-এর অংশ |
| USB তার | Linux কম্পিউটার থেকে ফোন কন্ট্রোল করলে |
| Wi-Fi | Wireless debugging ব্যবহার করলে (দুই ডিভাইস একই নেটওয়ার্কে) |

**Python আছে কিনা দেখতে:**
```
python3 --version
```
`Python 3.7` বা তার বেশি দেখালে ঠিক আছে।

---

## ৩. Initial Setup

Setup শুধু **একবারই** করতে হয়। নিচের ধাপগুলো পরপর অনুসরণ করুন।

### ধাপ ৩.১: ফোনে Developer Options চালু করা (সবার জন্য)

1. **Settings** > **About phone** এ যান।
2. **Build number** এ টানা **৭ বার** ট্যাপ করুন।
3. "You are now a developer" বার্তা এলে ঠিক আছে। (পিন/পাসওয়ার্ড চাইলে দিন।)
4. **Settings** > **System** > **Developer options** এ যান।
5. **USB debugging** চালু করুন।

> **নোট:** কিছু ফোনে (Xiaomi, Samsung ইত্যাদি) "About phone" এর ভেতরে "Software information" এ Build number থাকে। মেনুর নাম ফোনভেদে সামান্য আলাদা হতে পারে।

### ধাপ ৩.২: যে ডিভাইস থেকে কন্ট্রোল করবেন সেখানে ADB ও Python Install করা

ADB, Python ও এই Tool Install করতে হয় **যে ডিভাইস থেকে আপনি কন্ট্রোল করবেন** সেখানে (আপনার PC, অথবা Termux চলে এমন Android ফোন)। যে ফোনটি কন্ট্রোল হবে, সেখানে শুধু Developer options ও (Wireless হলে) Wireless debugging চালু থাকলেই চলে।

**কে কাকে কন্ট্রোল করতে পারে:**

| যা থেকে কন্ট্রোল করছেন | যে ফোন কন্ট্রোল হচ্ছে | পদ্ধতি |
|---|---|---|
| Ubuntu/Linux PC | আপনার Android ফোন | USB অথবা Wireless |
| অন্য একটি Android ফোন (Termux) | আপনার Android ফোন | Wireless |
| একই ফোনের Termux | সেই ফোনই | Wireless |

> **নোট:** Wireless পদ্ধতিতে দুটি ডিভাইসই **একই Wi-Fi (বা একই Hotspot) নেটওয়ার্কে** থাকতে হবে। PC-ও একই Wi-Fi-তে থাকলে ফোনের সাথে Wireless Debugging দিয়ে Connect করা যায়।

#### Ubuntu/Linux-এ

**১) ADB Install করুন:**
```
sudo apt update
sudo apt install adb
```

**২) ADB Install হয়েছে কিনা যাচাই করুন:**
```
adb version
```
"Android Debug Bridge version ..." লেখা এলে সফল।

#### Termux-এ

**১) Termux Install করুন।**
Play Store-এর Termux পুরোনো। **F-Droid** থেকে Termux নামান: https://f-droid.org/packages/com.termux/

**২) প্রয়োজনীয় Package Install করুন:**
```
pkg update
pkg install python android-tools
```
মাঝে `Y` চাপতে বললে `Y` লিখে Enter দিন।

**৩) ফোনের Storage-এ Termux-কে অনুমতি দিন (ZIP ফাইল পড়ার জন্য):**
```
termux-setup-storage
```
পপআপে **Allow** চাপুন।

### ধাপ ৩.৩: Tool Install করা

**১) ZIP ফাইল (`adb-maintenance-toolkit.zip`) ডাউনলোড করুন।**

**২) Unzip করুন।**

*Ubuntu:* (ফাইল Downloads ফোল্ডারে থাকলে)
```
cd ~/Downloads
python3 -m zipfile -e adb-maintenance-toolkit.zip ~/
```

*Termux:* (ফাইল ফোনের Download ফোল্ডারে থাকলে)
```
python3 -m zipfile -e ~/storage/downloads/adb-maintenance-toolkit.zip ~/
```

**৩) Tool-এর ফোল্ডারে ঢুকুন:**
```
cd ~/adb-maintenance-toolkit
```

### ধাপ ৩.৪: ফোনের সাথে Connect করা

#### পদ্ধতি A: USB তার দিয়ে (Linux PC থেকে)

**১) ফোন USB তার দিয়ে কম্পিউটারে লাগান।**
ফোনের স্ক্রিনে **"Allow USB debugging?"** পপআপ আসবে। **"Always allow from this computer"** টিক দিয়ে **Allow** চাপুন।

**২) Tool চালু করুন:**
```
python3 -m adbtk
```
ফোন চিনতে পারলে Tool নিজেই ডিভাইসটি বেছে নেবে। মেনুর উপরে ফোনের Model দেখাবে।

`device` ছাড়া অন্য কিছু (`unauthorized` বা `offline`) দেখালে "Error সমাধান" অংশ দেখুন।

#### পদ্ধতি B: Wireless Debugging দিয়ে (PC বা অন্য ফোন বা একই ফোন থেকে)

> **প্রয়োজন:** যে ফোন কন্ট্রোল হবে সেটি Android 11 বা নতুন হতে হবে, এবং দুই ডিভাইস একই Wi-Fi নেটওয়ার্কে থাকতে হবে।

এই পদ্ধতিতে **প্রথমবার** দুটি কাজ করতে হয়: **Pair** (পরিচয় করানো) এবং **Connect** (সংযোগ)। দুটোই Tool-এর মেনু থেকেই হয়, আলাদা কোনো Command লিখতে হয় না।

**১) যে ফোন কন্ট্রোল হবে, সেখানে Wireless Debugging চালু করুন:**
**Settings** > **Developer options** > **Wireless debugging** চালু করুন।

**২) যে ডিভাইস থেকে কন্ট্রোল করবেন, সেখানে Tool চালু করুন:**
```
python3 -m adbtk
```
> **নোট:** Command হলো `adbtk` (`adbkit` নয়)। বানান মিলিয়ে লিখুন।

**৩) মেনু থেকে `1` লিখে Enter দিন (Device Connection)।**
তিনটি অপশন দেখাবে:
```
1) List / select device
2) Wireless connect (IP:PORT)
3) Wireless pair (IP:PORT + pairing code)
0) Back
```

**৪) প্রথমে Pair করুন (শুধু প্রথমবার লাগে):**

1. কন্ট্রোল হওয়া ফোনে **Wireless debugging** পেজে **Pair device with pairing code** এ চাপুন।
2. একটি পপআপ আসবে, যেখানে **Wi-Fi pairing code** (৬ সংখ্যা) এবং **IP address & Port** লেখা থাকবে। পপআপটি **বন্ধ করবেন না**।
3. Tool-এ `3` লিখে Enter দিন।
4. IP ও Port চাইলে পপআপে দেখানো মানটি `IP:PORT` আকারে লিখুন। যেমন: `192.168.0.5:37123`
5. ৬ সংখ্যার Pairing code চাইলে পপআপের কোডটি লিখুন।
6. "Successfully paired" লেখা এলে প্রাথমিক সংযোগ সফল।

> **টিপ:** একই ফোনে Termux ব্যবহার করলে পপআপ খোলা রেখে Split-screen বা Floating Window-তে Termux চালান, নইলে পপআপ বন্ধ হয়ে Pairing ব্যর্থ হয়।

**৫) এবার Connect করুন:**

1. Pairing-এর পপআপ বন্ধ করে **Wireless debugging**-এর মূল পেজে ফিরুন।
2. সেখানে একদম উপরে **IP address & Port** লেখা থাকে। এই Port-টি Pairing-এর Port থেকে **আলাদা**।
3. Tool-এ মেনু ১ এ ফিরে `2` লিখে Enter দিন (Wireless connect)।
4. মূল পেজে দেখানো IP ও Port লিখুন। যেমন: `192.168.0.5:41567`
5. সংযোগ হলে Tool নিজেই ডিভাইসটি বেছে নেবে এবং মেনুর উপরে ফোনের নাম দেখাবে।

> **গুরুত্বপূর্ণ:** Pairing **একবারই** করতে হয়। পরের বার থেকে শুধু ধাপ ৫ (Connect) করলেই চলে। তবে Connect-এর Port প্রতিবার Wireless debugging বন্ধ-চালু, Wi-Fi বদল বা ফোন Reboot করলে **বদলে যেতে পারে**। তাই প্রতিবার মূল পেজ থেকে নতুন IP:PORT দেখে নিন।

### ধাপ ৩.৫: Setup ঠিক হয়েছে কিনা যাচাই (ঐচ্ছিক)

ফোন ছাড়াই Tool ঠিকমতো চলছে কিনা দেখতে:
```
python3 -m unittest discover -s tests -v
```
শেষে `OK` লেখা এলে সব ঠিক।

---

## ৪. প্রতিবার কীভাবে ব্যবহার করবেন

প্রথমবার Setup হয়ে গেলে প্রতিবার শুধু এই ৩টি ধাপ।

**১) ফোন Connect করুন**
- *USB (Linux):* USB তার লাগান এবং ফোন Unlock রাখুন।
- *Wireless (PC বা অন্য ফোন বা Termux):* দুই ডিভাইস একই Wi-Fi-তে রাখুন এবং ফোনে Wireless debugging চালু করুন। Tool চালিয়ে মেনু `1` > `2` দিয়ে মূল পেজের বর্তমান IP:PORT দিন। Pairing আবার লাগবে না।

**২) Tool-এর ফোল্ডারে গিয়ে চালান**
```
cd ~/adb-maintenance-toolkit
python3 -m adbtk
```

**৩) মেনু থেকে নম্বর লিখে Enter চাপুন**
যেমন ব্যাটারির তথ্য দেখতে `4` লিখে Enter দিন। কাজ শেষে "Press Enter to continue" এ Enter চাপলে মেনুতে ফিরবেন।

**বের হতে:** `22` লিখে Enter দিন (অথবা `Ctrl + C`)।

### Command Options

| Command | কাজ |
|---|---|
| `python3 -m adbtk` | সাধারণভাবে চালু (Serial লুকানো) |
| `python3 -m adbtk --show-serial` | পুরো Serial Number দেখায় |
| `python3 -m adbtk --version` | Tool-এর Version দেখায় |
| `python3 -m adbtk --help` | সাহায্য দেখায় |

> **টিপ (Privacy):** স্ক্রিনশট বা Screen Share করার আগে `--show-serial` ব্যবহার করবেন না। ডিফল্ট মোডে Serial এমন দেখাবে: `AB********56`।

---

## ৫. মেনুর প্রতিটি অপশনের ব্যাখ্যা

### Connection

| নম্বর | অপশন | কাজ |
|---|---|---|
| 1 | Device Connection | Device তালিকা/বাছাই, Wireless Connect ও Pair |

### Storage

| নম্বর | অপশন | কাজ |
|---|---|---|
| 2 | Storage Analysis | সব Partition-এর বিবরণ এবং সবচেয়ে বড় ১০টি ফোল্ডার |
| 3 | Storage Usage | /data ও /sdcard কতটুকু ভরেছে (Bar Chart সহ) |

### Battery

| নম্বর | অপশন | কাজ |
|---|---|---|
| 4 | Battery Information | চার্জ, স্বাস্থ্য, তাপমাত্রা, ভোল্টেজ, চার্জিং অবস্থা |
| 5 | Battery Statistics | ব্যাটারি ব্যবহারের সারাংশ (সব ফোনে নাও থাকতে পারে) |

### Memory

| নম্বর | অপশন | কাজ |
|---|---|---|
| 6 | RAM Information | মোট, ব্যবহৃত ও খালি RAM এবং Swap |

### Apps (শুধু দেখা যায়, কিছু মোছে না)

| নম্বর | অপশন | কাজ |
|---|---|---|
| 7 | Installed Apps | সব অ্যাপের Package নাম |
| 8 | Third-party Apps | আপনার Install করা অ্যাপ |
| 9 | System Apps | সিস্টেমের অ্যাপ |

অ্যাপ তালিকায় নাম দিয়ে Filter করা যায়। যেমন `whatsapp` লিখলে শুধু সংশ্লিষ্ট অ্যাপ দেখাবে।

### Device Info

| নম্বর | অপশন | কাজ |
|---|---|---|
| 10 | Android Version | Android ও API Level, Security Patch |
| 11 | Device Model | Model ও Codename |
| 12 | Manufacturer | নির্মাতা, Brand, Hardware |

### Storage Folders

| নম্বর | অপশন | কাজ |
|---|---|---|
| 13 | Internal Storage | মোট, ব্যবহৃত ও খালি জায়গা |
| 14 | Download Folder Usage | Download ফোল্ডারের সাইজ ও ফাইল সংখ্যা |
| 15 | DCIM Folder Usage | ক্যামেরা ফোল্ডার |
| 16 | Pictures Folder Usage | Pictures ফোল্ডার |
| 17 | Movies Folder Usage | Movies ফোল্ডার |
| 18 | Music Folder Usage | Music ফোল্ডার |

বড় ফোল্ডারের ক্ষেত্রে হিসাব করতে কয়েক সেকেন্ড থেকে কয়েক মিনিট লাগতে পারে। অপেক্ষা করুন।

### Maintenance (সতর্কভাবে ব্যবহার করুন)

#### 19: Cache Maintenance
- Android-এর নিজস্ব `pm trim-caches` পদ্ধতিতে অ্যাপের Cache পরিষ্কার করতে বলে।
- আপনার ছবি, ভিডিও, ডকুমেন্ট বা অ্যাপের ডেটা **মোছে না**।
- শুরুর আগে `y` লিখে নিশ্চিত করতে হয়।
- শেষে কত জায়গা খালি হলো দেখায়।
- কিছু ফোনে এই সুবিধা নিষিদ্ধ থাকে। তখন Error দেখাবে এবং Settings থেকে করার পরামর্শ দেবে।

#### 20: Temporary File Cleanup
এটি সবচেয়ে সতর্কতার অপশন। কাজের ধারা:

1. Tool অস্থায়ী ফাইল খুঁজবে (`*.tmp`, `*.temp`, `*.crdownload`, `*.part`, `*.partial` এবং `/data/local/tmp`)।
2. ফাইলের **তালিকা ও সাইজ** দেখাবে। **কিছুই মোছা হয় না।**
3. আপনি মুছতে চাওয়া ফাইলের নম্বর লিখবেন। যেমন `1,3,5-8`, অথবা সব নিতে `all`। কিছু না মুছতে শুধু Enter চাপুন।
4. Tool কয়টি ফাইল ও মোট কত সাইজ মুছবে তা দেখাবে।
5. নিশ্চিত হলে শব্দটি টাইপ করতে হবে: `yes`
6. তবেই শুধু ওই ফাইলগুলো মুছবে। **মোছা ফাইল ফেরত পাওয়া যায় না।**

> **সতর্কতা:** `/data/local/tmp`-এর ফাইল আপনি বা কোনো Developer Tool ইচ্ছাকৃতভাবে রেখে থাকতে পারেন। তালিকা ভালো করে দেখে তারপর বাছুন। আর কোনো `.part` ফাইল আসলে চলমান ডাউনলোডের অংশ হতে পারে। ডাউনলোড চলাকালীন মুছবেন না।

### Power

| নম্বর | অপশন | কাজ |
|---|---|---|
| 21 | Device Reboot | Normal / Recovery / Bootloader Reboot |

- `yes` টাইপ করে নিশ্চিত করতে হয়।
- **Recovery ও Bootloader** শুধু অভিজ্ঞদের জন্য। না জানলে শুধু **1 (Normal)** ব্যবহার করুন।
- Reboot-এর পর ফোন আবার চালু হওয়া পর্যন্ত অপেক্ষা করুন। Termux Wireless-এ পোর্ট বদলে যেতে পারে, তাই নতুন করে Connect করতে হতে পারে।

### Exit

| নম্বর | অপশন | কাজ |
|---|---|---|
| 22 | Exit | Tool বন্ধ করে |

---

## ৬. Tool Update করা

নতুন Version এলে:

1. পুরোনো ফোল্ডারটি নাম বদলে রেখে দিন (ব্যাকআপ হিসেবে):
```
mv ~/adb-maintenance-toolkit ~/adb-maintenance-toolkit-old
```
2. নতুন ZIP ফাইল **৩.৪** ধাপের মতো Unzip করুন।
3. ঠিকঠাক চললে পুরোনো ব্যাকআপ ফোল্ডার মুছে ফেলতে পারেন।

---

## ৭. Uninstall করা

### ৭.১: শুধু Tool মুছতে

> **সতর্কতা:** নিচের Command সাবধানে লিখুন। `rm -rf` এর পরে ঠিক এই পাথটুকুই থাকবে, আর কিছু নয়।

```
rm -rf ~/adb-maintenance-toolkit
```

Tool-এর কোনো ফাইল অন্য কোথাও ছড়িয়ে ছিটিয়ে থাকে না, কোনো Config বা Registry-ও নেই। ফোল্ডার মুছলেই পুরোপুরি Uninstall।

### ৭.২: ADB-ও মুছতে চাইলে (ঐচ্ছিক)

*Ubuntu:*
```
sudo apt remove adb
```

*Termux:*
```
pkg uninstall android-tools
```

### ৭.৩: ফোনে নিরাপত্তার জন্য (প্রস্তাবিত)

কাজ শেষে ফোনে এগুলো বন্ধ করে রাখা ভালো:
- **Settings** > **Developer options** > **USB debugging** বন্ধ করুন
- **Wireless debugging** বন্ধ করুন
- **Revoke USB debugging authorizations** চাপলে আগের সব কম্পিউটারের অনুমতি বাতিল হয়

---

## ৮. Error হলে কীভাবে সমাধান করবেন

প্রতিটি সমস্যার পাশে **লক্ষণ**, **কারণ** ও **সমাধান** দেওয়া আছে।

---

### ৮.১: "ADB was not found on this system"

**কারণ:** ADB Install করা নেই, অথবা PATH-এ নেই।

**সমাধান:**
- Ubuntu: `sudo apt install adb`
- Termux: `pkg install android-tools`
- যাচাই করুন: `adb version`

ADB কোনো বিশেষ জায়গায় থাকলে Tool-কে সেটি চেনাতে পারেন:
```
ADBTK_ADB=/path/to/adb python3 -m adbtk
```

---

### ৮.২: "No Android device found"

**কারণ:** কোনো ফোন সংযুক্ত নেই বা চেনা যাচ্ছে না।

**সমাধান (পরপর চেষ্টা করুন):**
1. USB তার পরীক্ষা করুন। কিছু তার শুধু চার্জের জন্য, ডেটা ট্রান্সফার করে না। অন্য তার চেষ্টা করুন।
2. ফোন Unlock করুন।
3. USB-র ধরন বদলান। ফোনের নোটিফিকেশনে "USB charging this device" চাপুন এবং **File transfer / MTP** বেছে নিন।
4. Developer options-এ **USB debugging** চালু আছে কিনা দেখুন।
5. ADB Server রিস্টার্ট করুন:
```
adb kill-server
adb start-server
adb devices
```
6. তবুও না হলে Linux-এর ক্ষেত্রে ৮.৫ দেখুন।

---

### ৮.৩: Device-এর অবস্থা `unauthorized`

**কারণ:** ফোনে "Allow USB debugging?" পপআপে অনুমতি দেওয়া হয়নি।

**সমাধান:**
1. ফোনের স্ক্রিন Unlock করুন। পপআপ এলে **Allow** চাপুন।
2. পপআপ না এলে তার খুলে আবার লাগান।
3. তাতেও না হলে: Developer options > **Revoke USB debugging authorizations** চাপুন, তারপর তার খুলে আবার লাগান।
4. ADB রিস্টার্ট করুন: `adb kill-server` তারপর `adb devices`।

---

### ৮.৪: Device-এর অবস্থা `offline`

**কারণ:** সংযোগ আটকে গেছে।

**সমাধান:**
1. তার খুলে আবার লাগান।
2. `adb kill-server` তারপর `adb devices`।
3. ফোনে USB debugging বন্ধ করে আবার চালু করুন।
4. ফোন রিস্টার্ট করুন।

---

### ৮.৫: Linux-এ `no permissions` বা `insufficient permissions for device`

**কারণ:** আপনার ইউজারের USB ডিভাইস ব্যবহারের অনুমতি নেই।

**সমাধান:**
```
sudo apt install android-sdk-platform-tools-common
sudo usermod -aG plugdev $USER
```
এরপর কম্পিউটার থেকে **Log out করে আবার Log in** করুন (বা রিস্টার্ট দিন), তারপর:
```
adb kill-server
adb devices
```

---

### ৮.৬: Wireless Pair (মেনু ১ > ৩) ব্যর্থ হচ্ছে

**সম্ভাব্য কারণ ও সমাধান:**

| সমস্যা | সমাধান |
|---|---|
| "Invalid format. Use IP:PORT." দেখাচ্ছে | IP ও Port কোলন (`:`) দিয়ে লিখুন, কোনো ফাঁকা ছাড়া। যেমন `192.168.0.5:37123` |
| Pairing পপআপ বন্ধ হয়ে গেছে | পপআপ খোলা রেখেই Tool ব্যবহার করুন। একই ফোন হলে Split-screen বা Floating Window ব্যবহার করুন |
| Pairing code ভুল বা পুরোনো | কোড সময়ের সাথে বদলায়। পপআপ বন্ধ করে নতুন করে **Pair device with pairing code** চেপে নতুন কোড নিন |
| ভুল Port দেওয়া হয়েছে | Pairing-এর Port আর Connect-এর Port **আলাদা**। Pairing-এ পপআপের Port, Connect-এ মূল পেজের Port দিন |
| দুই ডিভাইস আলাদা নেটওয়ার্কে | কন্ট্রোলকারী ডিভাইস ও ফোন একই Wi-Fi বা একই Hotspot-এ আছে কিনা দেখুন |
| Wi-Fi সংযুক্ত নয় | ফোনকে Wi-Fi-তে যুক্ত করুন। মোবাইল ডেটায় Wireless debugging কাজ করে না |
| Android 10 বা পুরোনো | Wireless debugging নেই। Android 11+ লাগবে |

Wi-Fi না থাকলে যেকোনো একটি ডিভাইসের Hotspot চালু করে অন্যটিকে তাতে যুক্ত করেও চেষ্টা করা যায়।

---

### ৮.৭: Wireless Connect (মেনু ১ > ২) ব্যর্থ হচ্ছে ("failed to connect" বা "Connection refused")

**কারণ:** Port বদলে গেছে, Wireless debugging বন্ধ হয়ে গেছে, বা ডিভাইস দুটি এক নেটওয়ার্কে নেই।

**সমাধান:**
1. ফোনে **Developer options** > **Wireless debugging** চালু আছে কিনা দেখুন।
2. Wireless debugging-এর **মূল পেজে** (Pairing পপআপে নয়) একদম উপরের **নতুন IP address & Port** দেখে নিন।
3. Tool-এ মেনু `1` > `2` দিয়ে সেই নতুন `IP:PORT` আবার দিন।
4. তাতেও না হলে দুই ডিভাইস একই Wi-Fi-তে আছে কিনা যাচাই করুন।
5. Connect কাজ না করলে একবার Wireless debugging বন্ধ করে চালু করুন এবং নতুন Port দিয়ে চেষ্টা করুন।
6. এরপরও না হলে মেনু `1` > `3` দিয়ে নতুন করে Pair করে আবার Connect করুন।

---

### ৮.৮: `No module named adbtk`

**কারণ:** আপনি ভুল ফোল্ডারে আছেন।

**সমাধান:** Tool-এর ফোল্ডারে ঢুকে তারপর চালান:
```
cd ~/adb-maintenance-toolkit
python3 -m adbtk
```
ফোল্ডারের ভেতরে `adbtk` নামের একটি সাবফোল্ডার আছে কিনা দেখতে: `ls`

---

### ৮.৯: `python3: command not found`

**সমাধান:**
- Termux: `pkg install python`
- Ubuntu: `sudo apt install python3`

---

### ৮.১০: "Python 3.7 or newer is required"

**কারণ:** Python-এর Version পুরোনো।

**সমাধান:** Python আপডেট করুন।
- Termux: `pkg upgrade python`
- Ubuntu: `sudo apt install python3`

---

### ৮.১১: Cache Maintenance বলছে "This device refused cache trimming"

**কারণ:** আপনার ফোনের নির্মাতা ADB থেকে Cache পরিষ্কারের অনুমতি দেয়নি। এটি বেশ কিছু ফোনে হয় (বিশেষ করে Xiaomi ও কিছু Samsung)।

**সমাধান:** ফোন থেকেই করুন:
- **Settings** > **Storage** > **Cached data** (থাকলে) অথবা
- অ্যাপ-ভিত্তিক: **Settings** > **Apps** > অ্যাপ বাছুন > **Storage** > **Clear cache**

এটি Tool-এর ত্রুটি নয়, ফোনের সীমাবদ্ধতা।

---

### ৮.১২: Folder Usage-এ "Could not measure the folder"

**কারণ:** Android-এর নিরাপত্তা নিয়মে ওই ফোল্ডার পড়তে দিচ্ছে না।

**সমাধান:**
- ফোন Unlock আছে কিনা দেখুন।
- ফোনটি Android 11+ হলে Scoped Storage-এর কারণে কিছু ফোল্ডার সীমিত থাকে। সাধারণ ফোল্ডারে (Download, DCIM) সাধারণত সমস্যা হয় না।
- আবার চেষ্টা করুন।

---

### ৮.১৩: "Movies/Music/... does not exist on this device"

**কারণ:** ওই নামের ফোল্ডার ফোনে তৈরিই হয়নি। এটি স্বাভাবিক, Error নয়। কোনো ভিডিও বা গান ওই ফোল্ডারে রাখলে তৈরি হবে।

---

### ৮.১৪: Storage Analysis বা Folder Usage খুব ধীর

**কারণ:** ফোনে লক্ষ লক্ষ ফাইল থাকলে গুনতে সময় লাগে।

**সমাধান:** ধৈর্য ধরে অপেক্ষা করুন (এক-দুই মিনিট পর্যন্ত স্বাভাবিক)। "Command timed out" এলে আবার চেষ্টা করুন এবং ফোন Unlock রাখুন।

---

### ৮.১৫: Battery Statistics-এ "No summary statistics available"

**কারণ:** কিছু ফোন এই তথ্য ADB-তে দেয় না। এটি Error নয়, সেই ফোনে সুবিধাটি নেই। Battery Information (অপশন 4) ঠিকমতো কাজ করবে।

---

### ৮.১৬: Reboot-এর পর Tool কাজ করছে না

**কারণ:** Reboot-এর পর ফোন কিছুক্ষণ Disconnect থাকে।

**সমাধান:**
1. ফোন পুরোপুরি চালু হওয়ার জন্য অপেক্ষা করুন এবং Unlock করুন।
2. মেনুতে `1` (Device Connection) > `1` দিয়ে ফোন আবার বাছুন।
3. Termux Wireless হলে নতুন পোর্ট দিয়ে আবার Connect করুন।

---

### ৮.১৭: Termux-এ ZIP ফাইল পাওয়া যাচ্ছে না

**সমাধান:**
1. `termux-setup-storage` চালিয়েছেন কিনা দেখুন এবং Allow দিয়েছেন কিনা।
2. ফাইল কোথায় আছে দেখুন: `ls ~/storage/downloads/`
3. ফাইলের নাম হুবহু মিলিয়ে নিন।

---

### ৮.১৮: Blocked by safety rules

```
[X] Blocked by safety rules: ...
```

**অর্থ:** Tool-এর নিরাপত্তা ব্যবস্থা একটি ঝুঁকিপূর্ণ Command আটকে দিয়েছে। এটি **ঠিকমতো কাজ করছে**, সমস্যা নয়। এই ব্লক বন্ধ করার কোনো উপায় ইচ্ছাকৃতভাবে রাখা হয়নি।

---

### ৮.১৯: উপরের কিছুতেই সমাধান না হলে

নিচের তথ্যসহ সমস্যা জানান:
1. কোন Platform (Ubuntu, Termux)
2. `python3 --version`
3. `adb version`
4. `adb devices` এর Output (Serial Number মুছে বা লুকিয়ে দিন)
5. ঠিক কোন মেনু নম্বরে কী Error এসেছে (পুরো Error লেখাটি কপি করুন)

---

## ৯. সচরাচর জিজ্ঞাসা (FAQ)

**প্রশ্ন: এই Tool কি আমার ফোনের ডেটা মুছে ফেলতে পারে?**
উত্তর: না। ফাইল মোছার একমাত্র উপায় হলো মেনু ২০ (Temporary File Cleanup), যেখানে আপনি নিজে তালিকা দেখে বেছে নেন এবং `yes` টাইপ করেন।

**প্রশ্ন: Root লাগবে?**
উত্তর: না। Tool Root ছাড়াই চলে।

**প্রশ্ন: ইন্টারনেট লাগবে?**
উত্তর: Tool চালাতে ইন্টারনেট লাগে না। শুধু Install-এর সময় (ADB ও Package নামাতে) ইন্টারনেট লাগে। Wireless debugging-এর জন্য দুই ডিভাইসকে একই Wi-Fi বা Hotspot নেটওয়ার্কে থাকতে হয়।

**প্রশ্ন: এক ফোন থেকে আরেক ফোন, অথবা PC থেকে ফোন কন্ট্রোল করা যায়?**
উত্তর: হ্যাঁ। Wireless debugging দিয়ে যে ডিভাইস থেকে কন্ট্রোল করবেন (PC বা Termux-ওয়ালা অন্য ফোন), সেখানে ADB, Python ও Tool থাকতে হবে। কন্ট্রোল হওয়া ফোনে শুধু Wireless debugging চালু থাকলেই হবে। দুটি ডিভাইসই একই Wi-Fi-তে থাকতে হবে।

**প্রশ্ন: Tool কি আমার তথ্য কোথাও পাঠায়?**
উত্তর: না। Tool কোনো ইন্টারনেট সংযোগ করে না, কোনো তথ্য সংগ্রহ বা প্রেরণ করে না। সবকিছু আপনার ডিভাইসেই থাকে।

**প্রশ্ন: একসাথে একাধিক ফোন সংযুক্ত থাকলে?**
উত্তর: Tool তালিকা দেখাবে, আপনি নম্বর দিয়ে একটি বেছে নেবেন। পরে মেনু ১ থেকে বদলানো যায়।

**প্রশ্ন: কাজ চলাকালীন ফোন খুলে ফেললে কী হবে?**
উত্তর: প্রতিটি Command-এর আগে Tool সংযোগ যাচাই করে। না পেলে আবার ফোন বাছতে বলবে।

**প্রশ্ন: Serial Number কেন লুকানো থাকে?**
উত্তর: স্ক্রিনশট বা Screen Share-এ ভুলে ব্যক্তিগত তথ্য ফাঁস হওয়া ঠেকাতে। দরকার হলে `--show-serial` ব্যবহার করুন।

**প্রশ্ন: Windows-এ চলবে?**
উত্তর: এখনো আনুষ্ঠানিকভাবে সাপোর্টেড নয়। Code প্রস্তুত রাখা হয়েছে এবং ভবিষ্যতে সাপোর্ট আসবে।

**প্রশ্ন: ফোন ছাড়া Tool পরীক্ষা করা যায়?**
উত্তর: হ্যাঁ, একটি নকল ADB দেওয়া আছে:
```
chmod +x tests/fake_adb.py
ADBTK_ADB=$PWD/tests/fake_adb.py python3 -m adbtk
```
এতে কাল্পনিক ফোনের উদাহরণ ডেটা দেখাবে।

---

## ১০. Safety: এই Tool কী কখনো করবে না

এই Tool-এর সব `adb shell` Command একটি নিরাপত্তা-ফিল্টারের মধ্য দিয়ে যায়। নিচের কাজগুলো **সবসময় ব্লক** থাকে:

- `rm -rf /` বা `rm -rf /data/*` ধরনের Command
- `pm clear` (`pm clear --all` সহ), যা অ্যাপের ডেটা মুছে দেয়
- `pm uninstall` (অ্যাপ মুছে ফেলা)
- Disk Format বা Wipe (`mkfs`, `dd`, `format`, `wipe`)
- Root Command (`su`)
- `/sdcard/Android` ফোল্ডারের ভেতরের কিছু মোছা
- একসাথে ওয়াইল্ডকার্ড (`*`) দিয়ে ফাইল মোছা

**আরও নিশ্চয়তা:**
- স্বয়ংক্রিয়ভাবে ব্যবহারকারীর ফাইল মোছা হয় না
- Cache পরিষ্কারে শুধু Android-এর নিজস্ব `pm trim-caches` ব্যবহৃত হয়
- Temp ফাইল মোছার আগে তালিকা দেখানো হয় ও `yes` টাইপ করে নিশ্চিত করতে হয়
- Serial Number ডিফল্টভাবে লুকানো থাকে

---

*ADB Maintenance Toolkit v1.0.0*
