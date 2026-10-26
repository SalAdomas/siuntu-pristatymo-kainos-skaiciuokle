# Siuntų pristatymo kainos skaičiuoklė

Kursinio darbo I dalis: projektavimo dokumentas

## 1. Problema ir idėja

**Sistema vienu sakiniu:** C++ konsolinė programa, kuri pagal siuntos svorį, matmenis, pristatymo zoną ir skubumą apskaičiuoja pristatymo kainą bei pateikia jos išklotinę.

**Problema ir dabartinis procesas:** Modeliuojama nedidelė pristatymo įmonė, kurios darbuotojas kainą nustato pagal tarifų lentelę ir skaičiuotuvą. Rankiniu būdu galima supainioti svorio intervalus, pamiršti matmenų priemoką arba skubumą skaičiuoti nuo netinkamos sumos.

**Nauda:** Darbuotojas gauna vienodai apskaičiuotą kainą ir mato kiekvieną jos dalį. Netinkama siunta atmetama nurodant priežastį.

**Naudotojai:** Vienos modeliuojamos pristatymo įmonės darbuotojai, kurie įveda siuntos duomenis, apskaičiuoja kainą ir gali išsaugoti skaičiavimą.

**Prielaidos:** Tarifai ir dydžio ribos yra projekto prielaidos, ne realių vežėjų kainos. Skaičiuojamas vienos siuntos pristatymas iš Lietuvos. Zoną pasirenka naudotojas; adresai ir valstybės netikrinami. Kainos galutinės, atskiras PVM skaičiavimas nenumatomas.

## 2. Apimtis

| Funkcija | Ką naudotojas galės atlikti | Pagrindinis modulis ar pagalbinė funkcija |
|---|---|---|
| Siuntos duomenų įvedimas | Įvesti svorį, tris matmenis, zoną ir skubumą | Pagalbinė funkcija |
| Pristatymo kainos skaičiavimas | Gauti kainos išklotinę arba atmetimo priežastį | **Pagrindinis modulis** |
| Skaičiavimo išsaugojimas | Patvirtinus išsaugoti siuntos duomenis ir kainos išklotinę | Pagalbinė funkcija |
| Skaičiavimų istorija | Peržiūrėti anksčiau išsaugotus skaičiavimus | Pagalbinė funkcija |

**Į kursinio darbo apimtį neįeina:** Vežėjų API, mokėjimai, siuntų sekimas, etikečių spausdinimas, maršrutų planavimas ir grafinė sąsaja. Šiame pradiniame projekte numatomas vienas tarifų rinkinys, o paskyrų ir kelių įmonių duomenų valdymas neprojektuojamas. Apimtį bus galima tikslinti vėlesniuose etapuose.

## 3. Pagrindinis modulis

**Pavadinimas ir atsakomybė:** Pristatymo kainodaros modulis: patikrinti siuntos duomenis, parinkti tarifą, apskaičiuoti priemokas ir grąžinti kainos išklotinę. Modulis neskaito konsolės ir nerašo failų.

**Logika, kurią reikės projektuoti ir testuoti:** Svorio intervalo parinkimas, matmenų ribos, padidintos siuntos nustatymas, priemokų taikymo eilė ir apvalinimas.

**Įvestis:** Svoris sveikais gramais, ilgis, plotis ir aukštis sveikais centimetrais, zona LT arba ES, skubumas „taip“ arba „ne“ ir tarifų rinkinys. Pavyzdys: 2 500 g, 30 × 20 × 10 cm, LT, neskubu.

**Išvestis:** Svorio tarifas, zonos priemoka, padidintos siuntos priemoka, skubumo priemoka ir galutinė suma arba klaidos paaiškinimas. Pavyzdžio rezultatas: 5,99 € + 0,00 € + 0,00 € + 0,00 € = **5,99 €**.

**Veikimo eiga:** Naudotojas įveda siuntą → modulis patikrina duomenis → parenka svorio tarifą → apskaičiuoja priemokas → grąžina rezultatą. Meniu parodo išklotinę; patvirtinus atskira duomenų saugojimo funkcija išsaugo skaičiavimą.

### Taisyklės arba sprendimo žingsniai

1. **Siuntos tinkamumas.** Svoris turi būti 1–30 000 g, kiekvienas matmuo 1–100 cm, trijų matmenų suma iki 180 cm imtinai. Leistinos zonos: LT – Lietuva, ES – kitos ES valstybės. Tikrinama tokia tvarka: svoris, matmenys, zona; grąžinama pirmoji klaida. Netinkama arba nepilna įvestis neleidžia apskaičiuoti ir išsaugoti kainos.
2. **Svorio tarifas.** Tarifų sąraše parenkama pirmoji viršutinė svorio riba, ne mažesnė už siuntos svorį. Taigi 1 000 g patenka į pirmą intervalą, o 1 001 g – į antrą.
3. **Zonos ir matmenų priemokos.** Zonai LT pridedama 0,00 €, zonai ES – 4,00 €. Jei bent vienas matmuo didesnis nei 60 cm arba jų suma didesnė nei 120 cm, pridedama 2,00 €. Matmenų priemoka taikoma tik vieną kartą, net kai tenkinamos abi sąlygos. Vien lygybė su 60 cm ar 120 cm riba nelaikoma viršijimu.
4. **Skubumo priemoka ir skaičiavimo eilė.** Tarpinė suma S = svorio tarifas + zonos priemoka + matmenų priemoka. Skubiai siuntai pridedama 50 % nuo visos S; neskubiai siuntai ši priemoka lygi nuliui. Galutinė kaina = S + skubumo priemoka. Kitų mokesčių ar nuolaidų nėra.
5. **Apvalinimas.** Pinigai saugomi sveiku centų skaičiumi, naudojant C++ tipą long long. Skubumo priemoka apvalinama iki artimiausio cento, pusę cento apvalinant aukštyn. Kai S pateikta centais, 50 % priemoka apskaičiuojama sveikųjų skaičių dalyba: (S × 50 + 50) / 100. Pavyzdžiui, 799 ct × 50 % = 399,5 ct → 400 ct.

| Svoris gramais | Svorio tarifas |
|---|---:|
| 1–1 000 | 3,99 € |
| 1 001–5 000 | 5,99 € |
| 5 001–10 000 | 7,99 € |
| 10 001–30 000 | 11,99 € |

Tarifų sąrašas bus įkeliamas iš tekstinio failo. Svorio ribos turi būti teigiamos ir didėti, paskutinė riba – 30 000 g, kainos – neneigiamos. Jei failo nėra arba jis netinkamas, kainos skaičiavimas nevykdomas ir parodomas paaiškinimas.

### Scenarijai būsimiems testams

| Scenarijus | Pradinės sąlygos ir konkreti įvestis | Veiksmas | Tikslus laukiamas rezultatas |
|---|---|---|---|
| Įprastas atvejis | 2 500 g; 30 × 20 × 10 cm; LT; neskubu | Apskaičiuoti kainą | Svorio tarifas 5,99 €; visos priemokos 0,00 €; galutinė kaina **5,99 €** |
| Ribinis atvejis | 1 000 g; 60 × 40 × 20 cm; ES; skubu | Apskaičiuoti kainą | Svorio tarifas 3,99 €; zona 4,00 €; matmenys 0,00 €; tarpinė suma 7,99 €; skubumas 4,00 €; galutinė kaina **11,99 €** |
| Klaida arba neįmanomas rezultatas | 30 001 g; 30 × 20 × 10 cm; LT; neskubu | Apskaičiuoti kainą | Pranešimas „Siuntos svoris turi būti nuo 1 iki 30 000 g“; kaina negrąžinama, naujas įrašas nesukuriamas |

Ribiniame scenarijuje tikrinama, kad 1 000 g priskiriama pirmajam intervalui, 60 cm ir 120 cm ribos nesukelia matmenų priemokos, o 399,5 ct skubumo priemoka apvalinama iki 400 ct.

**Jei modulis naudoja AI:** Netaikoma. Kainą apskaičiuos tik aprašytos taisyklės.

## 4. Kokybės atributas

**Pasirinktas atributas:** Palaikomumas, keičiant svorio tarifų intervalus.

**Kodėl svarbus šiai sistemai:** Keičiant tarifus neturėtų reikėti perrašyti kainodaros algoritmo ar meniu.

**Tikrinimo scenarijus ir sąlygos:** Tarifų faile prieš 30 000 g ribą pridėti 20 000 g ribą su 9,99 € tarifu. Naujas intervalas 10 001–20 000 g; likęs 20 001–30 000 g intervalas kainuoja 11,99 €.

**Sėkmės kriterijus:** Pakanka pridėti vieną tarifo eilutę; C++ programos failai nekeičiami. Trys anksčiau aprašyti scenarijai duoda tuos pačius rezultatus. Papildomai, su 30 × 20 × 10 cm, LT ir neskubiu pristatymu: 15 000 g → 9,99 €; 20 000 g → 9,99 €; 20 001 g → 11,99 €.

**Numatytas projektavimo sprendimas:** Tarifai įkeliami į vector sąrašą; parenkama pirmoji tinkama svorio riba, nepriklausomai nuo intervalų skaičiaus. Failų nuskaitymas atskirtas nuo kainodaros.

**Kaip patikrinsiu vėlesniame etape:** Pakeisiu laikiną tarifų failo kopiją, patikrinsiu ankstesnius ir papildomus pavyzdžius bei įsitikinsiu, kad programos failai nepakeisti. Šiame etape pateikiamas tik patikrinimo planas.

**Sprendimo kaina arba ribojimas:** Reikės tikrinti tarifų failo struktūrą. Toks sprendimas leidžia keisti intervalus ir įkainius; visiškai naujai kainodaros sąlygai gali reikėti kodo pakeitimo.

## 5. Pradinė sistemos struktūra

### Paprasta schema

    Naudotojas ↔ Konsolės meniu ↔ Kainodaros modulis
                      ↕
                Duomenų modulis
                      ↕
              Tarifų ir istorijos failai

Meniu perduoda siuntos duomenis ir įkeltus tarifus kainodaros moduliui, o gautą rezultatą parodo naudotojui. Patvirtinus duomenų modulis išsaugo skaičiavimą.

| Sistemos dalis | Atsakomybė |
|---|---|
| Konsolės meniu | Įvesties nuskaitymas, rezultato rodymas, išsaugojimo patvirtinimas ir istorijos peržiūra |
| Kainodaros modulis | Siuntos tikrinimas, svorio tarifo parinkimas, priemokos ir apvalinimas |
| Duomenų modulis | Tarifų nuskaitymas, skaičiavimų išsaugojimas ir istorijos skaitymas |
| Duomenų struktūros | Siuntos, tarifo ir kainos išklotinės duomenys |

**Planuojamos technologijos ir pasirinkimo priežastys:** C++17, struktūros, funkcijos, vector, string ir failų srautai. Tai leidžia sukurti paprastą konsolinę programą be papildomų bibliotekų. Tarifai saugomi faile tarifai.txt, skaičiavimai – skaiciavimai.txt. Išsaugoma kainos išklotinė, todėl vėlesnis tarifų pakeitimas nekeis ankstesnių rezultatų. Skaičiavimai centais padės išvengti slankiojo kablelio paklaidų.

## 6. AI panaudojimas

### AI rengiant šį dokumentą

Naudotas OpenAI ChatGPT: projekto apimčiai, taisyklėms, scenarijų juodraščiams.

| Priemonė ir užduotis | Ką panaudojau | Ką atmečiau arba perrašiau ir kodėl | Kaip patikrinau |
|---|---|---|---|
| OpenAI ChatGPT: apimties pasirinkimas | Siuntų skaičiuoklės idėja | Pašalintas pradinis kelių įmonių valdymo planas, nes šiame dokumente apsiribota I etapo projektavimu | Sutikrinta su I etapo užduoties aprašu ir pateikimo šablonu |
| OpenAI ChatGPT: taisyklės | Konkrečios ribos, trys įvesties ir rezultato pavyzdžiai | Bendri svorio ir matmenų aprašymai pakeisti tiksliomis ribomis; tarifai pažymėti projekto prielaidomis | Pavyzdžių sumos perskaičiuotos pagal aprašytas taisykles; programos kodas ir automatiniai testai šiame etape nekuriami |

### Planuojamas AI naudojimas kuriant sistemą

**Kur ir kam naudosiu AI:** Atskirų C++ funkcijų juodraščiams, kompiliavimo klaidų paaiškinimui ir ribinių testų idėjoms.

**Kaip tikrinsiu pasiūlymus ir sugeneruotą kodą:** Lyginsiu su aprašytomis taisyklėmis, kompiliuosiu ir tikrinsiu pagal iš anksto apskaičiuotus rezultatus. Naudosiu tik kodą, kurio veikimą galėsiu paaiškinti. Rizikos – neteisingos intervalo ribos, apvalinimas ir netinkamai apdorota įvestis.

**Ar AI bus sistemos funkcionalumo dalis:** Ne. Kainodara veiks vietoje, be AI.

## 7. Tolesnių darbų planas

| Darbas | Apčiuopiamas rezultatas | Planuojama darbų seka |
|---|---|---|
| Paruošti duomenų struktūras ir tarifus | Siuntos, tarifo ir rezultato struktūros; pavyzdinis tarifų failas | 1 |
| Įgyvendinti kainodaros modulį | Įvesties tikrinimas, tarifo parinkimas ir kainos išklotinė | 2 |
| Įgyvendinti duomenų saugojimą | Veikiantis skaičiavimų išsaugojimas ir istorijos skaitymas | 3 |
| Sukurti konsolės meniu | Visas scenarijus nuo duomenų įvedimo iki rezultato | 4 |
| Patikrinti prototipą | Patikrinti trys suplanuoti scenarijai ir paruošta demonstracija | 5 |

**Būsimo prototipo veikimo scenarijus:** Įvesti 1 000 g, 60 × 40 × 20 cm, ES, skubų pristatymą. Tikėtina išklotinė: 3,99 € + 4,00 € + 0,00 € + 4,00 € = 11,99 €. Patvirtinus išsaugoti vieną įrašą ir parodyti istorijoje. Tada įvesti 30 001 g: tikėtinas pranešimas apie netinkamą svorį, naujas įrašas nesukuriamas.

| Rizika arba neaiškumas | Kaip patikrinsiu arba sumažinsiu |
|---|---|
| Neteisingos svorio ir matmenų ribos | Naudoti sveikus gramus ir centimetrus; tikrinti ribinio scenarijaus rezultatą pagal taisykles |
| Cento paklaida skubumo priemokoje | Pinigus laikyti centais; patikrinti 399,5 ct → 400 ct apvalinimą |
| Netinkamas tarifų failas arba nepavykęs įrašymas | Patikrinti failo struktūrą ir operacijos sėkmę; klaidos atveju rodyti paaiškinimą, nepranešti apie sėkmingą išsaugojimą |

## Šaltiniai, jei naudojote

- I etapo užduoties aprašas.
- I etapo pateikimo šablonas.
