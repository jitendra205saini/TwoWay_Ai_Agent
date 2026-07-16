# Two-Way AI CRM — Sir ko explain karne ka script

**Total time:** ~18 min bolne ka + ~10 min Q&A
**Saath me kholo:** `TWOWAY.md` (diagrams ke liye)

> **Structure ka logic:** Component-by-component mat samjhao ("ye Postgres hai, ye Redis hai") — usme sir bore ho jaayenge aur picture nahi banegi.
> **Ek lead ki kahani** sunao — form bharne se le kar 5 PM demo tak. Har step pe tech apne aap aa jaayega.
> Aur **end me unse decision maango.** Manager ko "maine sab decide kar liya" se zyada "in 6 cheezon pe aapka call chahiye" achha lagta hai.

---

## 🎬 OPENING — 1 min

**Bolo:**

> "Sir, main aapko poora system ek lead ki journey ke through dikhata hoon. Maan lo Sharma Industries ne aaj subah 10 baje Facebook pe hamara ad dekha aur form bhar diya. Main aapko step by step batata hoon ki us form se le kar shaam 5 baje ke demo tak, system kya-kya karta hai.
>
> Ek line me — **system ka kaam ye hai ki hot lead 2 minute me engage ho jaaye, 2 din baad nahi.** Aaj lead aati hai, kisi ko pata chalta hai, koi call karta hai — usme ghante lag jaate hain. Tab tak lead thandi ho chuki hoti hai. Ye system wo gap band karta hai.
>
> 4 phase hain journey ke: **capture → samajhna → sahi banda dhoondhna → baat karna.** Chaliye shuru karte hain."

**Dikhao:** `TWOWAY.md` §1 ka bada architecture diagram — par sirf 5 second. Bolo: *"Ye poora picture hai, abhi confuse mat hoiye — main ek-ek hissa alag se dikhata hoon."* Phir scroll kar do.

> 💡 **Tip:** Bada diagram shuru me dikhana zaroori hai (context milta hai) par uspe rukna mat. Log poora diagram padhne lagenge aur tumhari baat sunna band kar denge.

---

## STEP 1 — Lead aayi (2 min)

**Bolo:**

> "Sharma Industries ne form bhara. Facebook turant hamare system ko batata hai — isko webhook kehte hain, matlab Facebook khud hamare darwaze pe dastak deta hai, humein baar-baar poochhna nahi padta.
>
> Hum 6 cheezein capture karte hain: **Naam, Phone, Email, Company, Source** (Facebook/Instagram/Organic) **aur Campaign** — matlab kaunsi service chahiye.
>
> Yahan ek chhota par important design decision hai. Facebook ko jawab hum **3 second ke andar** de dete hain — 'mil gaya, dhanyavaad' — aur asli kaam uske baad background me karte hain. **Agar hum Facebook ko wait karayenge to wo dobara-dobara bhejega, aur baar-baar fail hone pe hamara connection hi band kar dega.** Isliye pehle 'haan mil gaya' bolo, phir kaam karo."

**Dikhao:** §1 diagram ka ① SOURCES + ② INGESTION wala hissa.

**Sir poochh sakte hain:**

| Sawal | Jawab |
|---|---|
| *"Duplicate lead aa gayi to?"* | "Wahi lead 2 minute me dobara aayi — same phone, same email — to hum drop kar dete hain. Aur company ka naam thoda alag ho ('Sharma Industries' vs 'Sharma Industries Pvt Ltd') to `pg_trgm` naam ki Postgres feature use karte hain jo naam ka match karti hai. **Iske liye AI ki zaroorat nahi — ye database query hai.** AI lagayenge to paisa aur time dono waste." |
| *"Website form bhi jodenge?"* | "Haan. Har source ke liye alag darwaza, par uske andar sab ek hi rasta pakadte hain. Naya source jodna ek din ka kaam hai." |

---

## STEP 2 — AI samajhta hai: Hot ya Cold? (4 min) ⭐

> **Ye sabse important step hai presentation ka.** Yahan sir ko lagega ki tumne sochke banaya hai, copy nahi kiya. Time do isko.

**Bolo:**

> "Ab AI ko decide karna hai ki ye lead hot hai ya cold. Yahan maine ek deliberate design choice li hai, aur main chahta hoon aap isse agree karein.
>
> **Maine AI ko score calculate karne nahi diya.**
>
> Kaam do hisso me baanta hai:
>
> **AI ka kaam — sirf padhna aur samajhna.** Lead ne likha *'Hume 200 users ka plan chahiye, budget approx 4 lakh, kal call kar sakte ho?'* — AI isse padh kar batata hai: budget mention hua = haan, urgency = high, team size = 200. **Ye language ka kaam hai, AI isme achha hai.**
>
> **Number nikalna — plain code ka kaam.** Un signals ko le kar ek simple formula score banata hai, 0 se 100. **Ye arithmetic hai, aur AI isme kharab hai — wo number bana deta hai.**"

**Ruko. Phir teen wajah do — ye teenon manager ki bhasha hai:**

> "Teen wajah se ye important hai, sir:
>
> **Ek — aap ise tune kar sakte ho.** Threshold galat nikla, to main ek line badal dunga. AI se score karwate to poora prompt dobara likhna padta, aur pakka nahi hota ki theek hua ya nahi.
>
> **Do — main iska test likh sakta hoon.** Code ka test hota hai. AI ke mood ka nahi.
>
> **Teen — aap poochhoge 'ye lead 78 kyun hai?' to main aapko wajah dikha sakta hoon.** Har factor ka breakdown save hota hai — budget se 30, urgency se 25, corporate email se 10. **AI se karwate to jawab hota 'model ko aisa laga.' Wo jawab nahi hai.**"

**Dikhao:** `TWOWAY.md` §3 ka scoring flow diagram. Laal box = AI, hara box = code. Ungli se dikhao: *"laal sirf padhta hai, hara faisla karta hai."*

**Phir company check:**

> "Saath me ek aur cheez check hoti hai — **ye company nayi hai ya hamari purani customer hai?** Pehle email domain match karte hain, phir company ka naam.
>
> **Aur yahan ek business sawal hai jo meeting me nahi aaya tha, sir.** Agar Sharma Industries **already hamari customer hai**, to kya usko round-robin me daal ke kisi naye BDA ko dena chahiye? Mera kehna hai **nahi** — wo upsell hai, aur usko uske existing account manager ke paas jaana chahiye. **Purane customer ko bot cold-pitch kare, ye bura dikhta hai.** Ispe aapka call chahiye."

**Sir poochh sakte hain:**

| Sawal | Jawab |
|---|---|
| *"To AI ka kaam hi kya reh gaya?"* | "AI wo kaam kar raha hai jo pehle insaan karta tha — free text padh ke samajhna. Wo asli kaam hai. Main sirf calculator ka kaam usse nahi karwa raha, kyunki wo usme kharab hai." |
| *"Score galat nikla to?"* | "To wo ek tuning ka kaam hai, ek constant badalna. Aur mera plan hai pehle 2 hafte score nikalne ke saath-saath asli outcome bhi track karna — phir number khud bata dega ki threshold kahan hona chahiye." |
| *"Cold lead ka kya?"* | "Nurture list me. **Koi agent nahi, koi bot nahi.** Cold lead pe bot bhejna paisa bhi waste karta hai aur brand bhi." |

---

## STEP 3 — Sahi BDA dhoondhna (3 min) ⚠️

> **Yahan tumhe sir ki likhi hui requirement pe polite disagree karna hai.** Direct bolo, par respect ke saath — aur alternative ready rakho.

**Bolo:**

> "Sir, ab lead hot nikli, to kisi BDA ko deni hai. Yahan mujhe ek cheez clarify karni hai jo meeting ke notes me thi.
>
> **Notes me heading likhi thi 'Round-Robin', par andar likha tha 'best-performing agent ko do'. Ye do alag cheezein hain, aur thoda ulti bhi hain.**
>
> Round-robin matlab sabko barabar, bari-bari se. Performance-based matlab jo achha kar raha hai use zyada.
>
> Ab agar hum **poora performance-based** kar dein, to ek problem aayegi — aur wo do-teen mahine baad dikhegi, abhi nahi:
>
> **Top agent ko zyada lead milegi → uske zyada demo honge → uske numbers aur behtar → aur zyada lead milegi.**
>
> **Aur naya BDA?** Uski koi history nahi hai. To score kam. To lead milti hi nahi. To history banti hi nahi. **System ne chupchaap decide kar liya ki naya banda kabhi safal nahi hoga.**
>
> **Aur sabse khatarnak baat — ye data jaisa dikhta hai. Isliye koi sawal nahi uthayega.**"

**Ruko. Phir solution:**

> "Mera proposal ye hai — **dono chahiye, aur dono ho sakte hain:**
>
> **Ek — pehle capacity dekho, skill baad me.** Jo best agent hai par uske paas already 40 leads pending hain — wo 41st lead ke liye best nahi hai. Har BDA ka ek cap hoga. **Sirf ye ek cheez 80% faayda de deti hai.**
>
> **Do — 85% lead performance ke hisaab se, 15% lead plain rotation se.** Wo 15% naye BDA ko asli lead deta hai, taaki uska asli record ban sake. Ye chhota sa insurance hai.
>
> **Teen — performance pichhle 1 se 6 mahine ka, par recent zyada count karega.** Taaki pichhle quarter ka star jo ab dheela pad gaya hai, wo apne aap adjust ho jaaye."

**Dikhao:** §4 ka routing flowchart. Hara box (15% exploration) pe ungli rakho.

**Phir — ye zaroor bolo:**

> "Aur ek important baat, sir. **Performance-based routing Phase 1 me technically ho hi nahi sakta.** Ye priority ka faisla nahi hai — **data ka issue hai.** Performance score nikalne ke liye mujhe outcome data chahiye: kisne kitne demo book kiye, kitne deal bane. **Wo data tab banega jab system chal chuka hoga.**
>
> To Phase 1 me plain round-robin + capacity cap. **Do mahine ka data jama hoga, phir Phase 2 me performance layer chadhega.** Isse pehle karenge to hum andaaze pe score bana rahe honge."

**Sir poochh sakte hain:**

| Sawal | Jawab |
|---|---|
| *"Mujhe to poora performance-based chahiye."* | "Bilkul kar sakte hain sir — 15% wala exploration hata dunga, ek line ka change hai. Par **do mahine baad ye sawal aayega ki naye hire ko lead kyun nahi mil rahi.** Main bas chahta hoon ye faisla soch ke ho, apne aap na ho jaye. **15% se conversion pe asar na ke barabar hai, par pipeline bach jaati hai.**" |
| *"Sab BDA busy hain to?"* | "Manager queue me jaati hai — aapke paas. **Hot lead kabhi drop nahi hoti**, ye rule hai." |
| *"WIP cap kitna?"* | "Config hai, per-agent set kar sakte hain. 25 se shuru karke data dekh ke adjust karenge." |

---

## STEP 4 — AI khud lead se baat karta hai (4 min) ⭐⭐

> **Ye project ka dil hai, aur yahin sabse bada blocker bhi hai. Dono bolo.**

**Bolo:**

> "Ab sabse important hissa. Lead assign hote hi **AI khud WhatsApp pe baat shuru karta hai** — notification nahi, alert nahi. **Asli do-tarfa baat.** Requirement details poochhta hai, client ko engage karta hai, aur demo slot book karne ki koshish karta hai."

**Phir — ye ab bolna zaroori hai, baad me pata chalna mehenga padega:**

> "**Sir, yahan ek platform ki rukawat hai jo mujhe abhi aapko batani chahiye.**
>
> Official WhatsApp API ka ek hard rule hai: **jisne aapko pichhle 24 ghante me message nahi kiya, usse aap apni marzi ka message bhej hi nahi sakte.**
>
> Ab hamari lead ne kya kiya? **Form bhara. Humein message nahi kiya.**
>
> Matlab — **hamare AI ka pehla message AI likh hi nahi sakta.** Wo ek pre-approved template hoga jo Meta se approve karana padega. **Aur us approval me din lagte hain, aur reject bhi hota hai.**
>
> AI asli baat tab shuru karta hai **jab lead uss template ka reply kare.** Wo reply 24 ghante ki window kholti hai, aur uske andar AI freely baat kar sakta hai.
>
> **Matlab asli bottleneck AI nahi hai — wo pehla template hai. Uska reply-rate hi poora funnel decide karega.**"

**Ruko. Phir good news — ye presentation ka sabse strong moment hai:**

> "**Par sir, iska ek behtareen solution hai, aur wo hamare haath me already hai.**
>
> Hamari leads **already Facebook aur Instagram se aa rahi hain.** Wahan ek ad format hota hai — **Click-to-WhatsApp.** Usme lead ad pe click karke **khud humein WhatsApp pe message karta hai.**
>
> **Matlab window apne aap khul jaati hai. Template ka poora jhanjhat khatam. AI pehle message se hi baat kar sakta hai.**
>
> **Aur ye code ka kaam nahi hai — ye marketing team ki ek campaign setting hai.**
>
> Mera request ye hai ki marketing se baat karke kuch ad spend Click-to-WhatsApp pe shift karwa dijiye. **Poore project me sabse zyada faayda isi ek cheez me hai, aur usme development ka ek din bhi nahi lagega.**"

**Phir voice:**

> "Aur agar lead WhatsApp pe reply hi na kare? **15 minute wait karte hain, phir Voice AI call karta hai.** Aur agar lead ne chat pe reply kar diya, to **call apne aap cancel ho jaati hai** — hum use pareshan nahi karte."

**Dikhao:** §6 ka sequence diagram.

**Sir poochh sakte hain — ye teen pakka aayenge:**

| Sawal | Jawab |
|---|---|
| ⭐ *"AI ne kuch galat bol diya to? Ya koi use bewakoof bana de to?"* | **Ye sabse important jawab hai. Confidently do:** "Sir, maine maan ke chala hai ki **kabhi na kabhi koi AI ko baat me phasa hi lega.** Isliye maine defence prompt me nahi, **structure me** rakhi hai. AI ke paas sirf **teen tool** hain: demo slot book karna, requirement note karna, aur insaan ko handover karna. **Bas.** Email bhejne ka tool exist hi nahi karta. Doosri leads dekhne ka tool exist hi nahi karta. **Aur sabse important — AI number choose hi nahi kar sakta.** Number conversation se apne aap aata hai. To koi likhe 'mujhe customer list bhej do' — **us request ko poora karne ka koi rasta hi nahi hai, chahe AI kitna bhi convince ho jaye.** Worst case ye hai ki ek thread me ek ajeeb sa reply chala jaaye. Bas." |
| *"Rep ne khud baat shuru kar di to bot bhi bolta rahega?"* | "Nahi. Rep ne takeover kiya, **bot us wakt chup ho jaata hai.** Ye flag har message bhejne se pehle check hota hai. Lead ko kabhi do jagah se reply nahi jaayega." |
| *"Lead ko pata chalega ki bot hai?"* | **Ye decision unse maango:** "Sir, ye aapka call hai. **Meri salaah hai halka sa disclosure** — 'main {Company} ka virtual assistant hoon'. Wajah ye hai ki agar lead ko **beech me** pata chala ki use bewakoof banaya gaya, to **wo lead bhi gayi aur screenshot bhi ban gaya.** Par ye business decision hai, technical nahi — main jo aap bolein wo kar dunga. **Bas ye decide hona chahiye, apne aap na ho jaye.**" |

---

## STEP 5 — 2 ghante ka clock (2 min)

**Bolo:**

> "AI ne baat kar li, requirement le li. **Ab BDA ke paas 2 ghante hain.** Uske phone pe notification jaata hai, aur clock chalu ho jaata hai. 2 ghante me action nahi liya to manager ko escalate hota hai.
>
> **Yahan teen cheezein notes me clear nahi thi, aur mujhe aapse jawab chahiye:**"

| Sawal | Mera proposal |
|---|---|
| **"2 ghante kab se?"** | "AI ke kaam khatam karne se — assign hone se nahi. **Par ek catch hai:** jo lead beech me hi ghayab ho jaye, uska AI kabhi 'complete' nahi bolega, matlab uska clock kabhi shuru hi nahi hoga aur **wo lead hamesha ke liye latki rahegi.** Iske liye alag timer laga raha hoon." |
| **"Raat 11 baje aayi lead ka clock chalega?"** | "**Meri salaah — nahi.** Business hours ke hisaab se chale. 11 baje raat wali lead ka SLA agli subah 11 baje. **Warna har raat ki lead subah tak breach ho jaayegi, escalation channel me sirf shor bhar jaayega, aur log use mute kar denge — aur tab poora feature bekaar ho jaayega.**" |
| **"Breach pe kya ho?"** | "Manager ko escalate + reassign ka **suggestion**. **Auto-reassign nahi.** Lead chupchaap kisi aur ke paas chali jaaye aur pehle BDA ko pata bhi na chale — usse team ka trust tootega." |

---

## STEP 6 — Demo (1 min)

**Bolo:**

> "Aur poori journey ka maqsad yahi hai — **5 baje ka demo slot book ho jaaye.** AI khud fixed slots me se offer karta hai aur book kar deta hai. Phase 2 me ise asli calendar se jodenge.
>
> **Aur pehli baar hum ye numbers dekh payenge:** Facebook ki lead ka demo conversion kitna hai vs Instagram ka. Kaunsa campaign kaam kar raha hai. Kaunsa BDA achha convert kar raha hai. **Aaj ye data kahin nahi hai.**"

---

## 🗺️ PHASE 1 / PHASE 2 (2 min)

**Bolo:**

> "**Phase 1 ka goal ek line me — lead aaye, score ho, sahi BDA ko jaye, AI usse WhatsApp pe asli baat kare, aur BDA ke paas live clock ke saath pahunche.** Voice nahi, performance routing nahi.
>
> **Phase 2 — Voice AI, aur performance-based routing.**"

**Phir do decisions ki wajah batao — ye important hai:**

> "**Performance routing Phase 2 me isliye hai kyunki uska data Phase 1 ke bina exist hi nahi karta** — ye maine priority ke hisaab se nahi, majboori se rakha hai.
>
> **Aur Voice Phase 2 me isliye hai kyunki uska ek legal sawal khula hai.** Agar hum India me automated calls kar rahe hain to **TRAI aur DND ke rules lagte hain** — consent, registration, aur recording ka apna consent. **Ye technical sawal nahi hai, legal hai. Aur iska jawab Phase 2 shuru karne se pehle chahiye, kyunki ho sakta hai ye scope hi badal de.** Kya main legal team se baat kar loon?"

**Aur ye do salaah zaroor do — ye tumhe senior dikhati hain:**

> "**Ek — main routing se pehle conversation banaunga.** Routing ek chhota sa database query hai, ek shaam ka kaam, aur **wo pehli baar me hi theek chalega.** Asli risk conversation me hai — 24 ghante ki window, timing, aur sabse bada sawal: **kya AI asli lead ke saath kaam ki baat kar bhi paata hai?** **Jo cheez fail ho sakti hai use pehle banao.** Hafte teen me 'perfect routing + toota conversation' bahut bura position hai.
>
> **Do — main pehle do hafte shadow mode chalana chahta hoon.** Matlab **AI reply likhega, par bhejega insaan.** Hum naapenge ki BDA kitna edit kar rahe hain. Agar wo 60% draft rewrite kar rahe hain, to **ye baat mujhe internal traffic pe pata chalni chahiye, asli client pe nahi.** Do hafte ka data, phir number dekh ke decide karenge ki AI ko chhoda jaye ya nahi. **Number decide karega, meri opinion nahi.**"

---

## ✅ CLOSING — 6 decisions maango (2 min)

**Bolo:**

> "Sir, architecture ready hai. **Aage badhne se pehle mujhe aapse 6 cheezon pe faisla chahiye:**
>
> **1. Routing** — heading me 'round-robin' tha, andar 'best performer'. Mera proposal: rotation floor + performance tilt + capacity cap + 15% naye logon ke liye. **Aapka call.**
>
> **2. Click-to-WhatsApp ads** — **poore project ka sabse bada leverage, aur wo code nahi hai.** Marketing se baat karwa dijiye.
>
> **3. Template approval** — **aaj se shuru karna hai. Isme din lagenge aur ye poora Phase 1 block karta hai.** Kiske paas Meta business account ka access hai?
>
> **4. Voice ka legal sawal** — TRAI/DND. **Phase 2 scope karne se pehle jawab chahiye.**
>
> **5. Bot apne aap ko bot batayega ya nahi?** Business decision.
>
> **6. Teen chhote sawal** — (a) 2 ghante ka clock raat me chalega? (b) beech me ghayab hone wali lead ka kya? (c) purani customer ko bot cold-pitch karega?
>
> **Do sabse zaroori hain — number 2 aur 3.** Wo dono aaj shuru ho sakte hain aur dono mere haath me nahi hain."

---

# 📌 CHEAT SHEET — presentation se pehle 2 min me padho

**Agar sirf 3 cheez yaad rahein:**

1. **"AI se number nahi nikalwaya"** — AI padhta hai, code score karta hai. Kyunki tunable, testable, explainable.
2. **"Round-robin aur best-performer ulte hain"** — dono chahiye: rotation floor + performance tilt + capacity cap.
3. **"Click-to-WhatsApp"** — 24h window ka blocker, aur uska solution marketing ke paas hai, mere paas nahi.

**Agar phas jao:**
> *"Sir ye point maine abhi tak pura nahi socha, main kal tak likh ke bhejta hoon."*
>
> Andaaza mat lagao. Manager ke saamne "mujhe nahi pata, pata karke bataunga" **hamesha** galat jawab se behtar hai — aur usme koi kamzori nahi dikhti.

**Agar wo bolein "ye to bahut bada hai":**
> *"Sir, Phase 1 me sab kuch bhi ho — par asli sawal ek hi hai: **kya AI asli lead se kaam ki baat kar paata hai?** Wo mujhe do hafte ke shadow mode me pata chal jaayega. Baaki sab uske baad ka faisla hai."*

**Agar wo timeline poochhein:**
> Number mat bolo jab tak tumne khud estimate na kiya ho. Bolo: *"Phase 1 ke 14 items list kiye hain. Aaj shaam tak har item pe estimate laga ke bhejta hoon — Step 6 ka conversation orchestrator sabse bada hai, baaki sab uske aage chhote hain."*

**Tone:**
- Jahan disagree kar rahe ho (routing), **wajah pehle do, disagreement baad me.** "Ye galat hai" nahi — "ye do mahine baad ye problem banayega, isliye ye kar rahe hoon."
- Jahan nahi pata (legal, voice), **saaf bolo.** Wo confidence dikhata hai, kamzori nahi.
- Jahan blocker hai (template approval), **problem ke saath solution do** (Click-to-WhatsApp). Bina solution ke problem batana shikayat lagti hai; solution ke saath batana **ownership lagti hai.**
