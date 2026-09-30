# 💱 Live Currency Exchange Rate Dashboard

**Python + API + Power BI** yordamida 166 ta jahon valyutasining AQSh dollariga nisbatan kurslarini kuzatuvchi interaktiv dashboard.

![Dashboard](dashboard.png)

---

## 📌 Loyiha haqida

Ushbu loyiha ochiq valyuta kurslari API'sidan ma'lumotlarni muntazam yig'adi, Python yordamida tozalaydi va Power BI'da vizuallashtiradi. Dashboard quyidagi savollarga javob beradi:

- Hozir 1 AQSh dollari necha so'm, necha yevro turadi?
- API nechta valyuta bo'yicha ma'lumot beradi?
- Qaysi valyutalar dollarga nisbatan eng "og'ir" (eng ko'p birlik talab qiladi)?
- Kurslar kundan kunga qanday o'zgarmoqda?

> Asosiy valyuta: **USD**. Barcha kurslar "1 USD = X valyuta" ko'rinishida.
> Ma'lumotlar davri: 24.09.2026 – 27.09.2026. Oxirgi yangilanish: **27/09/2026 16:12:45**.

---

## 📊 Dashboard tarkibi

### KPI kartalari
| Karta | Qiymat | Tavsif |
|---|---|---|
| **UZS Rate** | 11.83K | 1 USD ning o'zbek so'midagi kursi |
| **EUR Rate** | 0.88 | 1 USD ning yevrodagi kursi |
| **Total Currencies** | 166 | Kuzatilayotgan valyutalar soni |
| **Max Rate** | 1.59M | Eng yuqori kurs qiymati |
| **Last Refresh** | 27/09/2026 16:12:45 | Ma'lumotlar oxirgi marta yangilangan vaqt |

### Vizuallar
1. **Currency slicer** — valyuta kodlari tugmalar ko'rinishida (AED, AFN, ALL, AUD va boshqalar). Tanlangan valyuta bo'yicha butun sahifa filtrlanadi.
2. **Average of Rate by Last_Updated** — kurslarning vaqt bo'yicha o'zgarish dinamikasi (line chart).
3. **Sum of Rate by Currency** — valyutalar kurs qiymati bo'yicha saralangan (bar chart). Eng yuqorida IRR, LBP, VND, SLL, LAK, IDR, UZS.
4. **Sum of Rate by Last_Updated and Currency** — vaqt va valyuta kesimida kurslar taqsimoti (stacked area chart).
5. **Batafsil jadval** — har bir valyuta uchun oxirgi yangilanish vaqti, maksimal, o'rtacha va minimal kurs.

---

## 🔍 Asosiy xulosalar

- 1 AQSh dollari taxminan **11 830 so'm** — UZS dollarga nisbatan eng ko'p birlik talab qiladigan valyutalar qatorida.
- Eng yuqori nominal kurs **Eron riali (IRR)** da, undan keyin Livan funti (LBP) va Vyetnam dongi (VND).
- Kuzatuv davrida o'rtacha kurs ko'rsatkichi 25 sentyabrdan 27 sentyabrgacha o'sish tendensiyasini ko'rsatgan.
- Eng past kurs qiymati — **0.02**, ya'ni dollardan qimmatroq valyutalar ham kuzatuvda bor (masalan, KWD, BHD, OMR).

---

## 🛠️ Texnologiyalar

| Qatlam | Vosita |
|---|---|
| Ma'lumot manbai | Exchange Rate REST API |
| Ma'lumot yig'ish va tozalash | Python (`requests`, `pandas`) |
| Saqlash | CSV / Excel |
| Modellashtirish va vizualizatsiya | Power BI Desktop, DAX, Power Query |

---

## 🔄 Ma'lumot oqimi (Pipeline)

```
 API  ──►  Python skript  ──►  CSV fayl  ──►  Power BI (Power Query)  ──►  Dashboard
          (yig'ish, tozalash)                  (model, DAX o'lchovlar)
```

1. Python skript API'ga so'rov yuborib, barcha valyutalarning USD'ga nisbatan kurslarini oladi.
2. JSON javob `pandas` yordamida jadval ko'rinishiga keltiriladi (`Currency`, `Rate`, `Last_Updated`).
3. Har bir ishga tushirishda yangi qatorlar CSV faylga qo'shiladi — shu tariqa tarixiy ma'lumot to'planadi.
4. Power BI CSV faylni o'qiydi va **Refresh** qilinganda dashboard yangilanadi.

---

## 📐 DAX o'lchovlaridan namunalar

```dax
UZS Rate =
CALCULATE (
    AVERAGE ( Rates[Rate] ),
    Rates[Currency] = "UZS",
    Rates[Last_Updated] = MAX ( Rates[Last_Updated] )
)

EUR Rate =
CALCULATE (
    AVERAGE ( Rates[Rate] ),
    Rates[Currency] = "EUR",
    Rates[Last_Updated] = MAX ( Rates[Last_Updated] )
)

Total Currencies =
DISTINCTCOUNT ( Rates[Currency] )

Max Rate =
MAX ( Rates[Rate] )

Last Refresh =
MAX ( Rates[Last_Updated] )
```

---

## 🚀 Ishga tushirish

1. `.pbix` faylni yuklab oling va Power BI Desktop'da oching.
2. Kerak bo'lsa, **Transform data → Data source settings** orqali CSV fayl yo'lini yangilang.
3. **Refresh** tugmasini bosing.

---

## 🧭 Kelajakdagi rejalar

- [ ] Ma'lumot yig'ishni avtomatlashtirish (Task Scheduler / cron)
- [ ] Kunlik o'zgarish foizini (% change) ko'rsatish
- [ ] Asosiy valyutani tanlash imkoniyati (USD, EUR, UZS)
- [ ] Valyuta konvertori sahifasini qo'shish
- [ ] Power BI Service'ga publish qilish

---

## 👤 Muallif

**hamroali**
- GitHub: [@hamroali](https://github.com/hamroali)

⬅️ [Barcha Power BI loyihalariga qaytish](../README.md)
