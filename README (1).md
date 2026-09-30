# 🌫️ Uzbekistan Air Quality Monitor

**Python + API + Power BI** yordamida O'zbekistonning yirik shaharlaridagi havo sifatini (AQI, PM2.5) va shamol tezligini kuzatuvchi interaktiv dashboard.

![Dashboard](images/dashboard.png)

---

## 📌 Loyiha haqida

Ushbu loyiha ochiq API orqali havo sifati ma'lumotlarini soatlik ravishda yig'adi, Python yordamida tozalaydi va Power BI'da vizuallashtiradi. Dashboard foydalanuvchiga quyidagi savollarga tez javob topishga yordam beradi:

- Hozir qaysi shaharda havo eng ifloslangan, qaysi birida eng toza?
- So'nggi kunlarda AQI qanday o'zgargan?
- Kun davomida PM2.5 darajasi qaysi soatlarda ko'tariladi?
- Shamol tezligi va PM2.5 o'rtasida bog'liqlik bormi?

### 🏙️ Kuzatiladigan shaharlar

| Shahar | Oxirgi AQI |
|---|---|
| Toshkent | 53 |
| Nukus | 48 |
| Samarqand | 45 |
| Namangan | 44 |
| Buxoro | 40 |

> Ma'lumotlar 24.09.2026 – 27.09.2026 oralig'ini qamrab oladi. Oxirgi yangilanish: **27/09/2026**.

---

## 📊 Dashboard tarkibi

### KPI kartalari
| Karta | Tavsif |
|---|---|
| **Oxirgi AQI** | Eng so'nggi o'lchangan AQI qiymati (46) |
| **AQI o'zgarishi** | Oldingi o'lchovga nisbatan farq (−3.00, yashil = yaxshilanish) |
| **Oxirgi PM2.5** | Eng so'nggi PM2.5 konsentratsiyasi, µg/m³ (5) |
| **Eng iflos shahar** | AQI eng yuqori bo'lgan shahar (Toshkent) |
| **Eng toza shahar** | AQI eng past bo'lgan shahar (Buxoro) |

### Vizuallar
1. **Soatlik / Kunlik grafik** — PM2.5 dinamikasini ko'rsatuvchi area chart. Tugmalar orqali soatlik va kunlik ko'rinish o'rtasida almashish mumkin (bookmark + button).
2. **Oxirgi AQI by city** — shaharlar bo'yicha oxirgi AQI qiymatlari (bar chart, kamayish tartibida).
3. **Kunlik jadval** — har kun uchun o'rtacha AQI va o'rtacha shamol tezligi.
4. **Ortacha shamol va PM2.5 (scatter)** — shamol tezligi (X o'qi) va PM2.5 (Y o'qi) o'rtasidagi bog'liqlik, shaharlar rang bilan ajratilgan.

### Filtr
- **Shahar** slicer — barcha vizuallarni bitta yoki bir nechta shahar bo'yicha filtrlaydi.

---

## 🔍 Asosiy xulosalar

- **Toshkent** kuzatuv davrida eng yuqori AQI ko'rsatkichiga ega (53), **Buxoro** esa eng toza (40).
- Umumiy o'rtacha AQI — **46.09**, o'rtacha shamol tezligi — **8.81 km/soat**.
- 27-sentabrda o'rtacha AQI 42.24 gacha tushgan — kuzatuv davridagi eng yaxshi kun.
- Scatter grafikda shamol kuchaygan sari PM2.5 kamayish tendensiyasi ko'rinadi: yuqori PM2.5 qiymatlari (20–27) asosan past shamol tezligida (0–7 km/soat), ayniqsa Toshkentda kuzatiladi.
- Soatlik grafikda PM2.5 kechasi va erta tongda ko'tarilib, kunduzi pasayadi.

---

## 🛠️ Texnologiyalar

| Qatlam | Vosita |
|---|---|
| Ma'lumot manbai | Air Quality REST API |
| Ma'lumot yig'ish va tozalash | Python (`requests`, `pandas`) |
| Saqlash | CSV / Excel |
| Modellashtirish va vizualizatsiya | Power BI Desktop, DAX, Power Query |

---

## 🔄 Ma'lumot oqimi (Pipeline)

```
 API  ──►  Python skript  ──►  CSV fayl  ──►  Power BI (Power Query)  ──►  Dashboard
          (yig'ish, tozalash)                  (model, DAX o'lchovlar)
```

1. Python skript har bir shahar koordinatalari bo'yicha API'ga so'rov yuboradi.
2. Javobdan AQI, PM2.5, shamol tezligi va vaqt ajratib olinadi.
3. Ma'lumotlar `pandas` yordamida tozalanadi va CSV faylga yoziladi.
4. Power BI CSV faylni o'qiydi; **Refresh** tugmasi bosilganda dashboard yangilanadi.

---

## 📁 Loyiha tuzilmasi

```
uzbekistan-air-quality/
├── data/
│   └── air_quality.csv          # Yig'ilgan ma'lumotlar
├── scripts/
│   └── fetch_air_quality.py     # API'dan ma'lumot yig'uvchi skript
├── powerbi/
│   └── air_quality_dashboard.pbix
├── images/
│   └── dashboard.png
├── requirements.txt
└── README.md
```

---

## 🚀 Ishga tushirish

### 1. Repozitoriyni klonlash
```bash
git clone https://github.com/<username>/uzbekistan-air-quality.git
cd uzbekistan-air-quality
```

### 2. Kutubxonalarni o'rnatish
```bash
pip install -r requirements.txt
```

### 3. Ma'lumotlarni yig'ish
```bash
python scripts/fetch_air_quality.py
```

### 4. Dashboardni ochish
- `powerbi/air_quality_dashboard.pbix` faylini Power BI Desktop'da oching.
- Kerak bo'lsa, **Transform data → Data source settings** orqali CSV fayl yo'lini yangilang.
- **Refresh** tugmasini bosing.

---

## 📐 DAX o'lchovlaridan namunalar

```dax
Oxirgi AQI =
VAR LastTime = MAX ( AirQuality[DateTime] )
RETURN
    CALCULATE ( AVERAGE ( AirQuality[AQI] ), AirQuality[DateTime] = LastTime )

AQI ozgarishi =
VAR LastTime = MAX ( AirQuality[DateTime] )
VAR PrevTime =
    CALCULATE ( MAX ( AirQuality[DateTime] ), AirQuality[DateTime] < LastTime )
RETURN
    [Oxirgi AQI]
        - CALCULATE ( AVERAGE ( AirQuality[AQI] ), AirQuality[DateTime] = PrevTime )

Eng iflos shahar =
FIRSTNONBLANK (
    TOPN ( 1, VALUES ( AirQuality[City] ), [Oxirgi AQI], DESC ),
    1
)

Eng toza shahar =
FIRSTNONBLANK (
    TOPN ( 1, VALUES ( AirQuality[City] ), [Oxirgi AQI], ASC ),
    1
)
```

---

## 🌡️ AQI shkalasi (ma'lumot uchun)

| AQI | Holat |
|---|---|
| 0–50 | 🟢 Yaxshi |
| 51–100 | 🟡 O'rtacha |
| 101–150 | 🟠 Sezgir guruhlar uchun zararli |
| 151–200 | 🔴 Zararli |
| 201–300 | 🟣 Juda zararli |
| 300+ | 🟤 Xavfli |

---

## 🧭 Kelajakdagi rejalar

- [ ] Ma'lumot yig'ishni avtomatlashtirish (Task Scheduler / cron)
- [ ] Ko'proq shaharlarni qo'shish (Andijon, Farg'ona, Qarshi va boshqalar)
- [ ] Xarita vizuali qo'shish
- [ ] AQI shkalasi bo'yicha rangli ogohlantirishlar
- [ ] Power BI Service'ga publish qilish va avtomatik yangilanish

---

## 👤 Muallif

**Ismingiz Familiyangiz**
- GitHub: [@username](https://github.com/username)
- LinkedIn: [linkedin.com/in/username](https://linkedin.com/in/username)

---

⭐ Loyiha foydali bo'lsa, yulduzcha qo'yishni unutmang!
