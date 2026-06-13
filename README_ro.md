# Multiverse Signal Lab 🌌

**Un detector software pentru urme posibile ale coliziunilor cosmice în lumina fosilă a Universului.**

---

## Ce este asta, pe scurt?

Imaginează-ți că Universul nostru este o bulă de săpun.

Teoria inflației eterne spune că ar putea exista **milioane de astfel de bule** — universuri paralele — care s-au format în același timp cu al nostru. Dacă două bule s-au lovit la începutul timpului, coliziunea ar fi lăsat o **urmă circulară slabă** în cerul cosmic.

Această urmă ar putea fi vizibilă în **CMB** — lumina cea mai veche din Univers, emisă la 380.000 de ani după Big Bang. O poți privi ca pe o fotografie a Universului în copilărie.

**Noi am construit un program care caută aceste urme în acea fotografie.**

---

## Ce este CMB?

CMB înseamnă **Cosmic Microwave Background** — Fundalul Cosmic de Microunde.

Când Universul era tânăr și fierbinte, era plin de plasmă (ca interiorul unui Soare). La un moment dat s-a răcit suficient cât să devină transparent, iar toată energia acumulată a fost eliberată ca lumină. Acea lumină **mai există și azi** — ajunge la noi din toate direcțiile cerului, puțin răcită, sub forma unor microunde invizibile ochiului uman.

Satelitul **Planck** al Agenției Spațiale Europene a fotografiat această lumină cu o precizie extremă. Harta arată astfel:

- Pe fundal: zgomot cosmic natural (variații mici de temperatură)
- Eventual ascunse în acel zgomot: urme ale unor evenimente exotice de acum 13,8 miliarde de ani

Noi căutăm acele urme.

---

## Ce cautăm exact?

### Coliziune de bule cosmice 🫧

Dacă Universul nostru s-a lovit de un alt univers-bulă, urma ar arăta ca un **disc circular** în harta CMB — o zonă puțin mai caldă sau mai rece decât restul, cu un contur bine definit la margine.

```
Hartă normală CMB:      Hartă cu urmă de coliziune:
  ~ ~ ~ ~ ~ ~ ~             ~ ~ ~ ~ ~ ~ ~
  ~ ~ ~ ~ ~ ~ ~             ~ ~(  disc  )~ ~
  ~ ~ ~ ~ ~ ~ ~             ~ ~(  cald  )~ ~
  ~ ~ ~ ~ ~ ~ ~             ~ ~(       )~ ~
  ~ ~ ~ ~ ~ ~ ~             ~ ~ ~ ~ ~ ~ ~
```

### String cosmic 🧵

Un string cosmic este o **cicatrice liniară în spațiu-timp** — un defect din primele fracțiuni de secundă ale Universului, cam cum crăpăturile apar în gheața care îngheață rapid. Ar lăsa o linie dreaptă de discontinuitate în temperatura CMB.

---

## Cum funcționează programul?

În 5 pași simpli:

### Pas 1 — Generează o hartă de test 🗺️

Nu folosim direct date reale Planck (acelea sunt mari și complicate). În schimb, **simulăm** o hartă realistă:

- Adăugăm zgomot cosmic real (bazat pe fizica CMB)
- Adăugăm zgomot de instrument (ca la orice aparat de măsură)
- Opțional: injectăm artificial un semnal cunoscut (o „urmă" pe care noi am pus-o acolo)

De ce? Ca să putem **testa** dacă detectorul funcționează corect.

### Pas 2 — Curăță harta (Prewhitening) 🧹

Harta CMB nu are zgomot uniform — unele frecvențe sunt mult mai zgomotoase decât altele (la scări mari, CMB are mult mai multă putere decât la scări mici).

Prewhitening înseamnă: **egalăm puterea pe toate frecvențele**, ca să nu fim păcăliți de structuri mari care nu au legătură cu semnalul căutat.

E ca și cum ai fotografia un peisaj pe ceață și ai aplica un filtru care elimină ceața uniform, fără să șteargă detaliile.

### Pas 3 — Aplică filtrul potrivit (Bandpass + Disc SNR) 🔍

Dacă căutăm un disc de 8 grade pe cer, nu are sens să ne uităm la structuri de 0.1 grade sau de 60 de grade. Aplicăm un **filtru de bandă** — păstrăm doar frecvențele spațiale relevante pentru dimensiunea discului căutat.

Apoi calculăm **SNR** (Signal-to-Noise Ratio — Raport Semnal/Zgomot):

```
SNR = media temperaturii în interiorul discului
      ─────────────────────────────────────────
      abaterea standard în inelul din jurul discului
```

Dacă SNR > 3.0, avem un candidat suspect.

### Pas 4 — Verifică de 3 ori că nu e fals 🧪

Acesta e pasul cel mai important. Un SNR mare poate fi:
- O urmă reală de coliziune cosmică ✨
- Zgomot care s-a aliniat întâmplător 😅
- Praf galactic 🌫️
- Un bug în cod 🐛

Verificăm cu trei teste:

**Test pozitiv:** Injectăm artificial un semnal puternic și verificăm că programul îl găsește. Dacă nu îl găsește → avem bug.

**Test nul:** Rulăm detectorul pe hărți fără niciun semnal. Dacă găsește „candidați" → avem prea multe false pozitive.

**Campanie de recuperare:** Testăm la zeci de amplitudini și raze diferite, și construim o curbă de sensitivitate — cât de slab poate fi un semnal ca să-l mai detectăm?

### Pas 5 — Raportează cinstit 📊

Programul generează automat:
- Grafice de tip heatmap (câte semnale am detectat per amplitudine și rază)
- Curbe de sensitivitate (limita noastră de detecție)
- Un raport Markdown cu toate rezultatele

---

## De ce e greu să găsești aceste urme?

Problema fundamentală: **zgomotul CMB este uriaș față de semnalul căutat**.

La rezoluția curentă (nside=128, beam 7.3'):
- Zgomotul CMB: ~115 µK (microkelvin)
- Un semnal tipic de coliziune: 5–50 µK

E ca și cum ai încerca să auzi un șoaptă în mijlocul unui concert rock.

Filtrul nostru în 3 pași reduce zgomotul de confuzie cu un factor ~10×. Dar pentru a ajunge la sensitivitate reală (3–10 µK), avem nevoie de **date de la mai multe frecvențe** care să poată separa semnalul CMB de semnalul exotic. Asta e planul pentru versiunea viitoare.

---

## Cum rulezi programul?

```bash
# 1. Instalează dependențele
pip install healpy numpy pyyaml typer rich pydantic matplotlib pytest

# 2. Creează un config
python pipeline.py init --quick --mechanism bubble

# 3. Rulează tot pipeline-ul
python pipeline.py run pipeline.yaml

# 4. Sau rulează un demo complet cu o singură comandă
python pipeline.py demo --quick
```

Asta va genera automat:
- O hartă CMB simulată
- Rezultatele scanului
- Graficele de validare
- Un raport complet

---

## Structura proiectului

```
multiverse-signal-lab/
│
├── pipeline.py          ← Punctul de intrare — comenzile principale
├── pipeline.yaml        ← Setările (rezoluție, zgomot, ce căutăm)
│
├── src/
│   ├── config/          ← Validarea setărilor (nu poți pune valori greșite)
│   ├── detection/       ← Filtrul de detecție (matematica principală)
│   └── steps/           ← Pașii pipeline-ului în ordine
│
├── scripts/
│   ├── gen_realistic_maps.py   ← Simulatorul de hărți CMB
│   ├── injection_recovery.py   ← Testele de recuperare a semnalelor
│   └── plot_recovery.py        ← Generatorul de grafice
│
├── tests/               ← 43 de teste automate (verifică că nimic nu e stricat)
├── docs/prereg.md       ← Ce testăm și ce ar dovedi că am greșit
│
├── Dockerfile           ← Rulează în container izolat
├── Makefile             ← Comenzi rapide: make run, make test
└── README.md            ← Documentația principală
```

---

## Ce am descoperit până acum?

**Nimic spectacular — și asta e un rezultat corect.**

La configurația curentă (date simulate, nside=128), pragul de detecție este ~300 µK. Semnalele teoretice de coliziune sunt de 5–50 µK. Deci **nu avem cum să le vedem încă** cu această configurație.

Dar știm exact **de ce** nu le vedem, știm **ce trebuie schimbat** (date multi-frecvență, rezoluție mai mare), și avem **infrastructura completă** pentru a face asta.

Aceasta este știința corectă: nu pretindem că am găsit ceva când nu am găsit.

---

## De ce contează asta?

Chiar dacă nu găsim nimic, pipeline-ul demonstrează ceva important:

> Putem construi un detector reproductibil, validat și cinstit pentru semnale extrem de slabe în date cosmologice.

Același tip de arhitectură se poate folosi pentru:
- Detectarea anomaliilor în imagini satelitare
- Monitorizarea infrastructurii cu senzori IoT
- Detectarea defectelor în rețele electrice
- Orice domeniu unde cauți un semnal slab în mult zgomot

---

## Terminologie rapidă

| Termen | Ce înseamnă simplu |
|---|---|
| CMB | Lumina fosilă a Universului, emisă la 380.000 ani după Big Bang |
| HEALPix | Sistem de împărțire a sferei cerești în pixeli egali |
| nside | Rezoluția hărții (nside=128 → ~200.000 pixeli pe tot cerul) |
| µK | Microkelvin — o milionime de grad Kelvin; unitatea de măsură a variațiilor CMB |
| SNR | Signal-to-Noise Ratio — cât de clar se vede semnalul față de zgomot |
| Prewhitening | Egalarea puterii pe frecvențe — ca un egalizator audio |
| Bandpass filter | Lasă să treacă doar frecvențele relevante pentru dimensiunea căutată |
| Injection-recovery | Testul: punem un semnal cunoscut, verificăm că îl găsim |
| False positive | „Detecție" care de fapt e zgomot — cel mai mare pericol în știință |
| p-value | Probabilitatea ca un rezultat să fie pur întâmplător |

---

## Resurse dacă vrei să înveți mai mult

- **CMB și cosmologie:** [ESA Planck Mission](https://www.esa.int/Science_Exploration/Space_Science/Planck)
- **Bubble collisions:** Căutare: *"CMB bubble collision signatures"* pe arXiv.org
- **HEALPix:** [healpix.sourceforge.io](https://healpix.sourceforge.io)
- **Cod sursă:** github.com/yourusername/multiverse-signal-lab

---

## Citat

Dacă folosești acest software într-un proiect sau lucrare:

```
Multiverse Signal Lab v0.3.0
A reproducible pipeline for exotic CMB anomaly searches
https://github.com/yourusername/multiverse-signal-lab
```

---

## Licență

MIT — poți folosi, modifica și distribui liber, cu atribuire.

---

*Nu am descoperit multiversul. Am construit un instrument care ar putea, cândva, să caute urme ale lui. Diferența contează.*
