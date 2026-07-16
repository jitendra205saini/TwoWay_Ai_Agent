# Two-Way AI CRM — Sir ko explain karne ka script

**Company:** NotifyTechAi — Bulk WhatsApp Business API & SMS
**Time:** ~18 min bolna + ~10 min Q&A
**Saath me kholo:** `TWOWAY.md` (diagrams)

> **Structure ka logic:** Component-by-component mat samjhao ("ye Postgres hai, ye Redis hai") — sir bore ho jaayenge aur picture nahi banegi.
> **Ek lead ki kahani** sunao — form bharne se le kar 5 PM demo tak. Tech apne aap aa jaayega.
> **End me decision maango.** Manager ko "maine sab decide kar liya" se zyada "in cheezon pe aapka call chahiye" achha lagta hai.

---

## 🎬 OPENING — 1.5 min

> **Ye opening tumhara sabse strong card hai. Isse mat gawao.**

**Bolo:**

> "Sir, shuru karne se pehle ek baat jo poore design ko badalti hai.
>
> **Hum ek WhatsApp Business API company hain. Aur hum apni leads ko WhatsApp pe handle karne wale hain.**
>
> Matlab **ye system sirf ek internal tool nahi hai — ye hamara live case study hai.** Har demo me hum bol sakte hain: *'jo AI abhi aapse WhatsApp pe baat kar raha tha — wahi hum aapko bech rahe hain.'*
>
> Aur ek aur faayda — **hamari sales team ab apne hi product ki dikkatein khud jhelegi.** Jo bug customer ko dikhta hai, wo hume pehle dikhega."

**Ruko. Phir:**

> "Ab main aapko poora system ek lead ki journey se dikhata hoon. Maan lo ek company ne aaj subah 10 baje hamara Facebook ad dekha, form bhara — 'mujhe 5 lakh message per month bhejne hain'. Main batata hoon ki us form se le kar shaam 5 baje ke demo tak system kya karta hai.
>
> **Ek line me maqsad — hot lead 2 minute me engage ho, 2 din baad nahi.**"

**Dikhao:** `TWOWAY.md` §1 ka bada diagram — **sirf 5 second.** Hara block dikhao: *"ye poora hara hissa hamara apna product hai."* Phir scroll kar do.

> 💡 Bada diagram context ke liye zaroori hai, par uspe rukna mat. Log usko padhne lagenge aur tumhari baat sunna band kar denge.

---

## STEP 1 — Lead aayi (2 min)

**Bolo:**

> "Lead ne form bhara. Facebook turant hume batata hai — webhook se, matlab Facebook khud dastak deta hai, hume baar-baar poochhna nahi padta.
>
> 6 cheezein capture karte hain: **Naam, Phone, Email, Company, Source** (Facebook/Instagram/Organic) **aur Campaign** — matlab kya chahiye: WhatsApp API, Bulk SMS, ya RCS.
>
> Ek chhota par important decision — Facebook ko jawab **3 second me** de dete hain, 'mil gaya', aur asli kaam background me. **Wait karayenge to wo baar-baar bhejega, aur baar-baar fail hone pe connection hi band kar dega.**"

**Sir poochh sakte hain:**

| Sawal | Jawab |
|---|---|
| *"Duplicate lead?"* | "Same phone/email 2 min me dobara aayi → drop. Company naam thoda alag ho ('Sharma Industries' vs 'Sharma Industries Pvt Ltd') to `pg_trgm` naam ki Postgres feature match kar leti hai. **Iske liye AI ki zaroorat nahi — ye database query hai.** AI lagayenge to paisa aur time dono waste." |
| *"Website form bhi?"* | "Haan. Har source ka alag darwaza, andar sab ek rasta. Naya source ek din ka kaam." |

---

## STEP 2 — Hot ya Cold? (4 min) ⭐

> **Presentation ka sabse important step. Yahan dikhega ki tumne apne business ke hisaab se socha hai, template copy nahi kiya. Time do.**

**Bolo:**

> "Ab AI ko decide karna hai — hot ya cold. Yahan maine ek deliberate choice li hai aur chahta hoon aap isse agree karein.
>
> **Maine AI ko score calculate karne nahi diya.**
>
> Kaam do hisso me:
>
> **AI ka kaam — sirf padhna.** Lead ne likha *'5 lakh message per month bhejne hain, DLT already registered hai, is hafte start karna hai'* — AI padh ke batata hai: volume = 5 lakh, DLT = haan, urgency = high. **Ye language ka kaam hai, AI isme achha hai.**
>
> **Number nikalna — plain code ka kaam.** Simple formula, 0 se 100. **Ye arithmetic hai, AI isme kharab hai — wo number bana deta hai.**"

**Ruko. Teen wajah — teenon manager ki bhasha hain:**

> "**Ek — aap ise tune kar sakte ho.** Threshold galat nikla to main ek line badal dunga. AI se karwate to poora prompt dobara, aur pakka nahi ki theek hua.
>
> **Do — iska test likh sakta hoon.** Code ka test hota hai. AI ke mood ka nahi.
>
> **Teen — aap poochhoge 'ye lead 78 kyun hai?' to main wajah dikha sakta hoon.** Har factor ka breakdown save hota hai. **AI se karwate to jawab hota 'model ko aisa laga.' Wo jawab nahi hai.**"

**Dikhao:** §3 ka scoring diagram. Laal = AI, hara = code. Ungli rakho: *"laal sirf padhta hai, hara faisla karta hai."*

**Phir — ye table dikhao, ye sabse zyada impress karega:**

> "Aur sir, **hamare business ke liye hot lead ka matlab kya hai — ye maine generic nahi rakha:**
>
> | Signal | Weight | Kyun |
> |---|---|---|
> | **Monthly volume** | Sabse zyada | Ye hamara asli revenue driver hai. '10 lakh msg/month' vs 'thoda try karna hai' — zameen aasmaan. |
> | **DLT already registered** | High | **Matlab serious hai aur onboarding fast hoga.** Hamare business ka sabse achha buying signal. |
> | **Industry** | High | E-commerce, EdTech, FinTech, Logistics = heavy senders. |
> | **Campaign** | Medium | WhatsApp API > Bulk SMS > 'bas puchh rahe the'. |
> | **Corporate email** | Medium | gmail se aaya to chhota player ho sakta hai. |
>
> Ye signals hamare business ke hain. **Koi generic CRM template nahi hai.**"

**Phir company check:**

> "Saath me check hota hai — **ye company nayi hai ya hamari purani customer?** Pehle email domain, phir naam.
>
> **Aur yahan ek business sawal hai jo meeting me nahi aaya.** Agar company **already hamari customer hai**, to kya usko round-robin me daal ke kisi naye BDA ko dena chahiye? **Mera kehna hai nahi** — wo upsell hai, uske existing account manager ko jaani chahiye. **Purane customer ko bot cold-pitch kare, ye bura dikhta hai.** Aapka call."

**Sir poochh sakte hain:**

| Sawal | Jawab |
|---|---|
| *"To AI ka kaam kya reh gaya?"* | "AI wo kaam kar raha hai jo pehle insaan karta tha — free text padh ke samajhna. **Wo asli kaam hai.** Main sirf calculator ka kaam usse nahi karwa raha, kyunki wo usme kharab hai." |
| *"Score galat nikla to?"* | "Tuning ka kaam, ek constant. Aur plan hai pehle 2 hafte score ke saath asli outcome bhi track karna — **phir number khud bata dega ki threshold kahan ho.**" |
| *"Cold lead ka kya?"* | "Nurture list. **Koi agent nahi, koi bot nahi.** Cold lead pe bot bhejna paisa bhi waste karta hai aur brand bhi." |

---

## STEP 3 — Sahi BDA (3 min) ⚠️

> **Yahan sir ki likhi requirement pe polite disagree karna hai. Direct bolo, par wajah pehle — disagreement baad me.**

**Bolo:**

> "Sir, lead hot nikli, ab kisi BDA ko deni hai. Yahan meeting ke notes me ek cheez clarify karni hai.
>
> **Heading likhi thi 'Round-Robin', par andar likha tha 'best-performing agent ko do'. Ye do alag cheezein hain, aur thoda ulti bhi.**
>
> Round-robin = sabko barabar, bari-bari. Performance-based = jo achha kar raha hai use zyada.
>
> Agar **poora performance-based** karein, to ek problem aayegi — **do-teen mahine baad dikhegi, abhi nahi:**
>
> **Top agent ko zyada lead → zyada demo → numbers behtar → aur zyada lead.**
>
> **Aur naya BDA?** History nahi → score kam → lead milti hi nahi → **history banti hi nahi.** **System ne chupchaap decide kar liya ki naya banda kabhi safal nahi hoga.**
>
> **Aur sabse khatarnak — ye data jaisa dikhta hai. Isliye koi sawal nahi uthayega.**"

**Ruko. Solution:**

> "Mera proposal — **dono chahiye aur dono ho sakte hain:**
>
> **Ek — pehle capacity, skill baad me.** Best agent jiske paas 40 lead pending hain, wo 41st ke liye best nahi hai. Har BDA ka cap. **Sirf ye ek cheez 80% faayda de deti hai.**
>
> **Do — 85% performance se, 15% plain rotation se.** Wo 15% naye BDA ko asli lead deta hai taaki asli record bane. Chhota insurance.
>
> **Teen — performance 1 se 6 mahine ka, par recent zyada count karega.** Pichhle quarter ka star jo ab dheela hai, apne aap adjust ho jaata hai."

**Dikhao:** §4 flowchart. Hara box (15%) pe ungli.

**Ye zaroor bolo:**

> "Aur sir, **performance routing Phase 1 me technically ho hi nahi sakta.** Ye priority ka faisla nahi — **data ka issue hai.** Score ke liye outcome data chahiye: kisne kitne demo book kiye, kitne deal bane. **Wo data tab banega jab system chal chuka hoga.**
>
> To Phase 1 me plain round-robin + capacity cap. **Do mahine ka data, phir Phase 2 me performance chadhega.** Isse pehle karenge to hum **andaaze pe score bana rahe honge.**"

**Sir poochh sakte hain:**

| Sawal | Jawab |
|---|---|
| *"Mujhe poora performance-based chahiye."* | "Kar sakta hoon sir — 15% wala hata dunga, ek line ka change. Par **do mahine baad ye sawal aayega ki naye hire ko lead kyun nahi mil rahi.** Main bas chahta hoon ye faisla **soch ke ho, apne aap na ho jaye.** 15% se conversion pe asar na ke barabar hai, par pipeline bach jaati hai." |
| *"Sab BDA busy hain to?"* | "Manager queue me — aapke paas. **Hot lead kabhi drop nahi hoti.**" |
| *"WIP cap kitna?"* | "Config hai. 25 se shuru, data dekh ke adjust." |

---

## STEP 4 — AI khud lead se baat karta hai (4 min) ⭐⭐

> **Project ka dil. Aur yahin tumhara sabse bada advantage hai.**

**Bolo:**

> "Ab sabse important hissa. Lead assign hote hi **AI khud WhatsApp pe baat shuru karta hai** — notification nahi, alert nahi. **Asli do-tarfa baat.** Requirement poochhta hai, engage karta hai, demo slot book karta hai.
>
> **Aur ye hamare apne platform pe chalega — kisi Twilio ya vendor pe nahi.**"

**Ruko yahan. Ye line important hai:**

> "**Ye sirf paisa bachane ki baat nahi hai, sir.** Agar hum apni sales kisi aur ke WhatsApp API pe chalayein — to hum apne hi product pe bharosa nahi dikha rahe. **Aur hamara competitor hamare sales data se paisa kama raha hoga.**"

**Phir 24h window — par confidence ke saath:**

> "Ab WhatsApp ka ek rule hai jo sabko jhelna padta hai: **jisne aapko 24 ghante me message nahi kiya, use apni marzi ka message nahi bhej sakte.** Pehla message ek approved template hona chahiye.
>
> **Par sir — ye hamare liye utna bada problem nahi hai jitna kisi normal company ke liye hota.**
>
> Normal company ko Meta verification karana padta hai — **hamara already hai.** BSP dhoondhna padta hai — **hum khud BSP hain.** Template kisi aur ke through submit karna padta hai — **wo pipeline hamare andar hai.** Template reject hua to samajh nahi aata kyun — **hum ye roz karte hain, team jaanti hai.**
>
> **Matlab jo cheez normal company ke liye 3 hafte ka blocker hai, wo hamare liye 3 din ka kaam hai.** Ye hamara structural advantage hai."

**Phir do rehte — dono tumhare paas hain:**

> "Aur do cheezein aur hain jo hamare paas already hain:
>
> **Ek — Click-to-WhatsApp ads.** Hamari leads already Facebook/Instagram se aa rahi hain. CTWA ad me **lead khud hume message karta hai** — matlab window apne aap khul jaati hai, **template ka jhanjhat first contact pe khatam.** Aur **ye code nahi hai — marketing ki campaign setting hai.**
>
> **Do — RCS.** Ye hamara secret weapon hai. **RCS pe 24 ghante ka wo rule nahi lagta.** To hum cold lead ko RCS se pehla touch de sakte hain — sasta, bina window ki dikkat ke — aur jo reply kare use WhatsApp pe le aayein.
>
> **Ye hamare competitors kar hi nahi sakte, kyunki unke paas RCS nahi hai. Hamare paas hai.**"

**Phir voice:**

> "Aur lead WhatsApp pe reply hi na kare to? **15 minute wait, phir Voice AI call karta hai.** Aur agar lead ne chat pe reply kar diya to **call apne aap cancel** — hum use pareshan nahi karte."

**Dikhao:** §6 sequence diagram.

**Sir poochh sakte hain — ye teen pakka aayenge:**

| Sawal | Jawab |
|---|---|
| ⭐ *"AI ne galat bol diya to? Koi use bewakoof bana de to?"* | **Sabse important jawab. Confidently:** "Sir, maine maan ke chala hai ki **kabhi na kabhi koi AI ko baat me phasa hi lega.** Isliye defence prompt me nahi, **structure me** rakhi hai. AI ke paas sirf **teen tool** hain: demo book karna, requirement note karna, insaan ko handover karna. **Bas.** Email bhejne ka tool **exist hi nahi karta.** Doosri leads dekhne ka tool **exist hi nahi karta.** **Aur sabse important — AI number choose hi nahi kar sakta**, number conversation se apne aap aata hai. To koi likhe 'mujhe customer list bhej do' — **us request ko poora karne ka koi rasta hi nahi hai, chahe AI kitna bhi convince ho jaye.** Worst case: ek thread me ek ajeeb reply. Bas." |
| *"Rep ne khud baat shuru kar di to bot bhi bolta rahega?"* | "Nahi. Rep ne takeover kiya, **bot us wakt chup.** Ye flag har message bhejne se pehle check hota hai. **Lead ko kabhi do jagah se reply nahi jaayega.**" |
| *"Lead ko pata chalega ki bot hai?"* | **Decision maango:** "Aapka call sir. **Meri salaah — halka disclosure**, 'main NotifyTechAi ka virtual assistant hoon'. Wajah — agar lead ko **beech me** pata chala ki bewakoof banaya gaya, to **wo lead bhi gayi aur screenshot bhi ban gaya.** Ye business decision hai, technical nahi. **Bas ye decide hona chahiye, apne aap na ho jaye.**" |

---

## STEP 5 — 2 ghante ka clock (2 min)

**Bolo:**

> "AI ne baat kar li, requirement le li. **Ab BDA ke paas 2 ghante hain.** Notification jaata hai, clock chalu. 2 ghante me action nahi to manager ko escalate.
>
> **Teen cheezein notes me clear nahi thi, mujhe aapse jawab chahiye:**"

| Sawal | Mera proposal |
|---|---|
| **"2 ghante kab se?"** | "AI ke kaam khatam karne se — assign se nahi. **Par ek catch:** jo lead beech me ghayab ho jaye, uska AI kabhi 'complete' nahi bolega — matlab **clock kabhi shuru hi nahi hoga aur wo lead hamesha latki rahegi.** Iske liye alag timer laga raha hoon." |
| **"Raat 11 baje wali lead?"** | "**Meri salaah — clock business hours me chale.** 11 baje raat wali lead ka SLA agli subah 11. **Warna har raat ki lead subah tak breach ho jaayegi, escalation channel me sirf shor bhar jaayega, aur log use mute kar denge — aur tab poora feature bekaar.**" |
| **"Breach pe kya?"** | "Manager escalate + reassign ka **suggestion**. **Auto-reassign nahi.** Lead chupchaap kisi aur ke paas chali jaaye aur pehle BDA ko pata bhi na chale — **usse team ka trust tootega.**" |

---

## STEP 6 — Demo (1 min)

**Bolo:**

> "Poori journey ka maqsad — **5 baje ka demo book ho jaaye.** AI fixed slots offer karke book kar deta hai. Phase 2 me asli calendar.
>
> **Aur pehli baar hum ye numbers dekh payenge:** Facebook ki lead ka conversion vs Instagram ka. Kaunsa campaign chal raha hai — WhatsApp API ya Bulk SMS. Kaunsa BDA achha convert kar raha hai. **Aaj ye data kahin nahi hai.**"

---

## 🗺️ PHASE 1 / PHASE 2 (2 min)

**Bolo:**

> "**Phase 1 ek line me — lead aaye, score ho, sahi BDA ko jaye, AI usse WhatsApp pe asli baat kare, aur BDA ke paas live clock ke saath pahunche.** Voice nahi, performance routing nahi, RCS nahi.
>
> **Phase 2 — Voice AI, performance routing, aur RCS channel.**"

**Phir wajah batao:**

> "**Performance routing Phase 2 me isliye hai kyunki uska data Phase 1 ke bina exist hi nahi karta** — ye maine priority se nahi, majboori se rakha hai.
>
> **Voice Phase 2 me isliye kyunki uska ek legal sawal khula hai.** Hamare paas **DLT compliance already hai** — matlab hum is domain me naye nahi hain, ye bada advantage hai. **Par voice calling ke TRAI rules SMS se alag hain.** **Ye hamari compliance team ka 1-hafte ka sawal hai, 1-mahine ka nahi — par Phase 2 shuru karne se pehle jawab chahiye.** Main baat kar loon?"

**Ye do salaah zaroor do — ye tumhe senior dikhati hain:**

> "**Ek — main routing se pehle conversation banaunga.** Routing ek chhota database query hai, ek shaam ka kaam, **aur wo pehli baar me theek chalega.** Asli risk conversation me hai — 24 ghante ki window, timing, aur sabse bada sawal: **kya AI asli lead ke saath kaam ki baat kar bhi paata hai?** **Jo fail ho sakta hai use pehle banao.** Hafte 3 me 'perfect routing + toota conversation' bahut bura position hai.
>
> **Do — pehle do hafte shadow mode.** **AI reply likhega, par bhejega insaan.** Naapenge ki BDA kitna edit kar rahe hain. Agar 60% rewrite ho raha hai to **ye mujhe internal traffic pe pata chalna chahiye, asli client pe nahi.** Do hafte ka data, phir decide karenge. **Number decide karega, meri opinion nahi.**"

---

## ✅ CLOSING — decisions maango (2 min)

**Bolo:**

> "Sir, architecture ready hai. **Aage badhne se pehle 6 cheezon pe aapka faisla chahiye:**
>
> **1. Apne platform pe chalayein — Twilio pe nahi.** Mera strong recommendation. **Paisa bhi bachta hai, aur har demo me pitch ban jaata hai.**
>
> **2. Routing** — 'round-robin' aur 'best performer' ulte hain. Proposal: rotation floor + performance tilt + capacity cap + 15% naye logon ke liye. **Aapka call.**
>
> **3. Click-to-WhatsApp ads** — marketing se baat karwa dijiye. **Sabse bada leverage, aur wo code nahi hai.**
>
> **4. RCS ko Phase 2 me first-touch channel banayein?** **Ye hamara aisa advantage hai jo competitors ke paas hai hi nahi.**
>
> **5. Voice ka legal sawal** — DLT hamare paas hai, par voice ke rules alag hain. **Phase 2 se pehle jawab.**
>
> **6. Teen chhote sawal** — (a) 2 ghante ka clock raat me chalega? (b) beech me ghayab hone wali lead? (c) purani customer ko bot cold-pitch karega?
>
> **Do sabse zaroori — number 1 aur 3.** Number 1 poora design decide karta hai. Number 3 aaj shuru ho sakta hai aur wo mere haath me nahi hai."

---

# 📌 CHEAT SHEET — presentation se 2 min pehle padho

**Agar sirf 3 cheez yaad rahein:**

1. **"Hum apna product khud use kar rahe hain"** — Twilio nahi, apna platform. Paisa + pitch + product feedback, teenon.
2. **"AI se number nahi nikalwaya"** — AI padhta hai, code score karta hai. Tunable, testable, explainable.
3. **"Round-robin aur best-performer ulte hain"** — dono chahiye: rotation floor + performance tilt + capacity cap.

**Agar phas jao:**
> *"Sir ye point maine abhi tak poora nahi socha, kal tak likh ke bhejta hoon."*
>
> **Andaaza mat lagao.** Manager ke saamne "pata karke bataunga" **hamesha** galat jawab se behtar hai — **aur usme koi kamzori nahi dikhti.**

**Agar wo bolein "ye to bahut bada hai":**
> *"Sir, Phase 1 me sab kuch bhi ho — par asli sawal ek hi hai: **kya AI asli lead se kaam ki baat kar paata hai?** Wo mujhe do hafte ke shadow mode me pata chal jaayega. Baaki sab uske baad ka faisla hai."*

**Agar timeline poochhein:**
> **Number mat bolo jab tak khud estimate na kiya ho.** Bolo: *"Phase 1 ke 16 items list kiye hain. Aaj shaam tak har item pe estimate laga ke bhejta hoon — conversation orchestrator sabse bada hai, baaki uske aage chhote hain."*

**Agar wo poochhein "Twilio hi use kar lo, jaldi ho jayega":**
> *"Sir do wajah se nahi. Ek — hum apne hi competitor ko apni sales ka paisa denge. Do — jab client poochhega 'aapka product kaisa hai', to jawab 'pata nahi, hum to Twilio use karte hain' nahi hona chahiye. **Aur technically bhi hamara apna platform integrate karna aasaan hai — API humne hi likha hai.**"*

**Tone:**
- Jahan disagree kar rahe ho (routing) — **wajah pehle, disagreement baad me.** "Ye galat hai" nahi — "ye do mahine baad ye problem banayega, isliye ye kar raha hoon."
- Jahan nahi pata (voice legal) — **saaf bolo.** Wo confidence dikhata hai, kamzori nahi.
- Jahan blocker hai (template) — **problem ke saath solution do.** Bina solution ke problem batana **shikayat** lagti hai; solution ke saath **ownership** lagti hai.
