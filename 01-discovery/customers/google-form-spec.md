# Google Form Spec: Bulgarian Car Enthusiasts Survey
## `/01-discovery/customers/google-form-spec.md`
### Produced by: @customer-analyst | Date: 2026-03-18
### Version: 1.0

## References
- Interview Guide: `/01-discovery/customers/interview-guide.md`

---

## Form Title
**Анкета: Как управляваш разходите за колата си?**
*(Survey: How do you manage your car expenses?)*

## Form Description
```
Здравей! Проучваме как собствениците на автомобили в България управляват разходите си.
Анкетата отнема 5-7 минути. Няма грешни отговори — интересува ни твоят реален опит.
Отговорите са анонимни.
```
*(Hey! We're researching how car owners in Bulgaria manage their expenses. The survey takes 5-7 minutes. There are no wrong answers — we're interested in your real experience. Responses are anonymous.)*

---

## Section 1: За теб и колата ти (About you and your car)
*Maps to: Interview Q1-Q2 (Warm-Up)*

### Q1. Каква кола караш? (What car do you drive?)
- **Type:** Short text
- **Required:** Yes
- **Placeholder:** *напр. BMW E46 320d, 2003 / VW Golf 7, 2015*

### Q2. От колко време имаш тази кола?
*(How long have you had this car?)*
- **Type:** Multiple choice
- **Required:** Yes
- **Options:**
  - По-малко от 6 месеца (Less than 6 months)
  - 6 месеца — 1 година (6 months — 1 year)
  - 1-3 години (1-3 years)
  - 3-5 години (3-5 years)
  - Над 5 години (Over 5 years)

### Q3. Колко коли притежаваш в момента?
*(How many cars do you currently own?)*
- **Type:** Multiple choice
- **Required:** Yes
- **Options:**
  - 1
  - 2
  - 3 или повече (3 or more)

### Q4. На колко години си?
*(How old are you?)*
- **Type:** Multiple choice
- **Required:** Yes
- **Options:**
  - 18-24
  - 25-30
  - 31-35
  - 36-40
  - Над 40

---

## Section 2: Разходи и проследяване (Expenses & Tracking)
*Maps to: Interview Q3-Q6 (Current Behavior)*

### Q5. Приблизително колко харчиш на месец за колата си (гориво, застраховки, ремонти, поддръжка — всичко)?
*(Roughly how much do you spend per month on your car — fuel, insurance, repairs, maintenance — everything?)*
- **Type:** Multiple choice
- **Required:** Yes
- **Options:**
  - Под 100 € (Under €100)
  - 100-200 €
  - 200-300 €
  - 300-500 €
  - Над 500 € (Over €500)
  - Нямам идея (I have no idea)

### Q6. Проследяваш ли по някакъв начин какво харчиш за колата?
*(Do you track what you spend on your car in any way?)*
- **Type:** Multiple choice
- **Required:** Yes
- **Options:**
  - Да, водя записки (тетрадка, бележки в телефона) — Yes, I keep notes (notebook, phone notes)
  - Да, в таблица (Excel/Google Sheets) — Yes, in a spreadsheet
  - Да, ползвам приложение (Fuelio, Drivvo или друго) — Yes, I use an app (Fuelio, Drivvo, or other)
  - Не, но съм се замислял/а — No, but I've thought about it
  - Не, никога не съм се замислял/а — No, I've never thought about it

### Q7. Кога за последно те е изненадала цената на нещо свързано с колата?
*(When was the last time a car-related cost surprised you?)*
- **Type:** Paragraph (long text)
- **Required:** No
- **Placeholder:** *напр. "Смяна на ангренажен ремък — 600 €, не очаквах толкова"*

### Q8. Когато трябва да вземеш решение за колата (ремонт, смяна на част), как решаваш?
*(When you need to make a decision about your car (repair, part replacement), how do you decide?)*
- **Type:** Checkboxes (multiple answers)
- **Required:** Yes
- **Options:**
  - Питам механика (I ask my mechanic)
  - Питам във Facebook група (I ask in a Facebook group)
  - Питам приятели (I ask friends)
  - Търся в Google / форуми (I search Google / forums)
  - Гледам видеа в YouTube (I watch YouTube videos)
  - Решавам на усет (I go with gut feeling)
  - Друго (Other) — with short text field

---

## Section 3: Проблеми и болки (Problems & Pain Points)
*Maps to: Interview Q7-Q10 (Pain Discovery)*

### Q9. Кое е най-досадното нещо свързано с притежаването на кола в България, откъм разходи?
*(What is the most annoying thing about owning a car in Bulgaria, cost-wise?)*
- **Type:** Paragraph (long text)
- **Required:** Yes
- **Placeholder:** *Кажи свободно — няма грешен отговор*

### Q10. Случвало ли ти се е да ти трябва пълна история на разходите по колата (напр. при продажба, за застраховка, или просто за информация)?
*(Have you ever needed a complete expense history for your car — e.g., when selling, for insurance, or just for info?)*
- **Type:** Multiple choice
- **Required:** Yes
- **Options:**
  - Да, и нямах такава — беше проблем (Yes, and I didn't have one — it was a problem)
  - Да, но се оправих някак (Yes, but I managed somehow)
  - Не, не ми е трябвало (No, I haven't needed one)

### Q11. Интересно ли ти е да знаеш как твоите разходи се сравняват с други хора със същата кола?
*(Would you be interested to know how your costs compare to other people with the same car?)*
- **Type:** Linear scale
- **Required:** Yes
- **Scale:** 1-5
- **Labels:** 1 = Изобщо не ме интересува (Not interested at all) → 5 = Много ми е интересно (Very interested)

### Q12. Документираш ли колата си — снимки на модификации, записи за извършени ремонти?
*(Do you document your car — photos of mods, records of repairs done?)*
- **Type:** Multiple choice
- **Required:** Yes
- **Options:**
  - Да, системно (снимки, записи) — Yes, systematically (photos, records)
  - Понякога, но не подредено — Sometimes, but not organized
  - Не — No

---

## Section 4: Приложения и плащане (Apps & Willingness to Pay)
*Maps to: Interview Q11-Q12 (Competitor Probing)*

### Q13. Ползвал/а ли си приложение за проследяване на разходи за кола?
*(Have you ever used an app for tracking car expenses?)*
- **Type:** Multiple choice
- **Required:** Yes
- **Options:**
  - Да, ползвам в момента (Yes, I currently use one)
  - Да, но спрях (Yes, but I stopped)
  - Не, но съм чувал/а за такива (No, but I've heard of them)
  - Не, и не знам за такива (No, and I don't know of any)

### Q14. *(Show only if Q13 = "Да, но спрях")* Защо спря да го ползваш?
*(Why did you stop using it?)*
- **Type:** Paragraph (long text)
- **Required:** No
- **Placeholder:** *напр. "Беше досадно да въвеждам всичко ръчно"*

### Q15. Колко харчиш на месец за абонаменти за приложения (Spotify, Netflix и др.)?
*(How much do you spend monthly on app subscriptions — Spotify, Netflix, etc.?)*
- **Type:** Multiple choice
- **Required:** Yes
- **Options:**
  - Нищо — не плащам за приложения (Nothing — I don't pay for apps)
  - Под 5 €/месец (Under €5/month)
  - 5-13 €/месец
  - 13-25 €/месец
  - Над 25 €/месец (Over €25/month)

---

## Section 5: Стойност и интерес (Value & Interest)
*Maps to: Interview Q13-Q15 (Value Testing)*

### Q16. Представи си, че отваряш телефона и виждаш: "Колата ти струва точно X € на месец и Y цента на километър." Полезно ли би било?
*(Imagine you open your phone and see: "Your car costs exactly X €/month and Y cents/km." Would that be useful?)*
- **Type:** Linear scale
- **Required:** Yes
- **Scale:** 1-5
- **Labels:** 1 = Не особено (Not really) → 5 = Много полезно (Very useful)

### Q17. Ако имаше приложение, което наистина ти помага с притежаването на кола (не само проследяване, а реална помощ), какво би искал/а да прави?
*(If there was an app that genuinely helped with car ownership — not just tracking, but real help — what would you want it to do?)*
- **Type:** Paragraph (long text)
- **Required:** No
- **Placeholder:** *Свободен текст — мечтай!*

---

## Section 6: Контакт (Contact — Optional)
*Maps to: Interview Q16-Q17 (Commitment & Close)*

### Q18. Работим по нещо в тази посока. Би ли пробвал/а ранна версия и дал/а честна обратна връзка?
*(We're working on something in this space. Would you try an early version and give honest feedback?)*
- **Type:** Multiple choice
- **Required:** Yes
- **Options:**
  - Да! (Yes!)
  - Може би (Maybe)
  - Не, благодаря (No, thanks)

### Q19. *(Show only if Q18 = "Да!" or "Може би")* Остави имейл или телефон, за да се свържем:
*(Leave your email or phone so we can reach out:)*
- **Type:** Short text
- **Required:** No
- **Placeholder:** *имейл или телефонен номер*

### Q20. Познаваш ли 2-3 души, които биха попълнили тази анкета? Препрати им линка!
*(Do you know 2-3 people who would fill out this survey? Forward them the link!)*
- **Type:** Info text (no input needed)
- **Display text:** *Ще ни помогнеш много ако споделиш анкетата с приятели, които карат и обичат колите си.*

---

## Form Settings

- **Collect emails:** No (keep it anonymous to maximize responses)
- **Limit to 1 response:** No (people might share from same device)
- **Show progress bar:** Yes
- **Confirmation message:** "Благодаря! Отговорите ти са много ценни за нас. Ако искаш да следиш проекта, следи [тук]."

---

## Signal Mapping

This table maps form questions back to interview assumptions for analysis:

| Assumption | Form Questions |
|---|---|
| Don't know true monthly cost | Q5 ("Нямам идея" answer), Q7 |
| Want to know (problem awareness) | Q9, Q10, Q16 |
| Not currently tracking | Q6 |
| Cost/km "aha moment" | Q16 |
| Comparison with others is compelling | Q11 |
| Would log expenses in app | Q6, Q13, Q18 |
| Would pay ~€2.99/month | Q15 (willingness to pay for apps in general) |
| Timeline/photo documentation appeals | Q12 |

---

## Export & Analysis Plan

1. **Export:** Google Forms → Google Sheets (automatic) → Download as CSV
2. **Key metrics to calculate:**
   - % who selected "Нямам идея" on Q5 (validates assumption #1)
   - % who track in any way on Q6 (validates assumption #3)
   - Average score on Q11 and Q16 (validates assumptions #4-5)
   - % who said "Да!" on Q18 (commitment signal)
   - Count of emails/phones collected on Q19 (hard commitment)
3. **Qualitative analysis:** Read Q7, Q9, Q14, Q17 responses for patterns and quotes
4. **Output:** Feed into `/01-discovery/customers/interview-notes-summary.md`