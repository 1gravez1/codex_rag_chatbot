# \[🇬🇧 English](README.md) | 🇸🇮 Slovenščina

# 🚀 Codex RAG Chatbot

AI klepetalni pomočnik (RAG – Retrieval Augmented Generation) za podjetje **Codex d.o.o.**, ki uporabnikom omogoča hitro pridobivanje informacij o izdelkih, tehničnih specifikacijah, distributerjih in kontaktnih podatkih na podlagi uradnega prodajnega kataloga podjetja.

\---

## 📋 Pregled projekta

Codex uporablja obsežen 143-stranski večjezični katalog izdelkov (slovenščina, angleščina, nemščina, hrvaščina in srbščina). Iskanje informacij v takšnem dokumentu je za uporabnike pogosto zamudno, zato je bil razvit RAG chatbot, ki:

* razume vprašanja v naravnem jeziku,
* poišče relevantne informacije v katalogu,
* odgovarja v jeziku uporabnika,
* zmanjšuje potrebo po ročnem iskanju po dokumentaciji.

\---

## 🎯 Cilji

* Omogočiti hitro iskanje informacij po katalogu.
* Zmanjšati čas iskanja tehničnih specifikacij in kontaktnih podatkov.
* Zagotoviti odgovore na podlagi uradnih podatkov podjetja.
* Preprečiti halucinacije modela z uporabo vektorskega iskanja.

\---

## 🏗️ Arhitektura sistema

Projekt je sestavljen iz dveh ločenih n8n workflow-ov.

### Workflow A – Indeksiranje kataloga

```text
Manual Trigger
      │
      ▼
Google Drive (Download Markdown File)
      │
      ▼
Default Data Loader
      │
      ▼
Text Splitter
      │
      ▼
OpenAI Embeddings
      │
      ▼
Pinecone Vector Store (Insert)
```

Ta workflow se izvede ob posodobitvi kataloga in poskrbi za pripravo podatkov za semantično iskanje.

### Workflow B – Chatbot

```text
Chat Trigger
      │
      ▼
AI Agent
 ├─ GPT-4o mini
 ├─ Window Buffer Memory
 └─ Pinecone Vector Store Tool
          │
          ▼
   Relevant Context
```

Chatbot uporablja vektorsko iskanje za pridobivanje relevantnih informacij iz Pinecone baze in nato generira odgovor z uporabo OpenAI modela.

\---

## 📥 Priprava podatkov

Originalni PDF katalog je bil pretvorjen v Markdown format z uporabo CloudConvert.

Postopek priprave podatkov:

1. PDF katalog se pretvori v Markdown.
2. Markdown datoteka se shrani v Google Drive.
3. n8n workflow prenese datoteko.
4. Besedilo se razdeli na manjše segmente (chunks).
5. Za vsak segment se ustvarijo embeddings.
6. Embeddings se shranijo v Pinecone.

Takšen pristop omogoča učinkovito semantično iskanje po celotni vsebini kataloga.

\---

## 🧠 Indeksiranje v Pinecone

### Text Splitter

* Recursive Character Text Splitter
* Chunk size: \~1000 znakov
* Overlap: \~200 znakov

### Embeddings

Model:

```text
text-embedding-3-small
```

### Pinecone Index

```text
codexchatbot
```

Vsak chunk vsebuje:

* besedilo
* source
* blobType
* loc.lines.from
* loc.lines.to

\---

## 🤖 AI Agent

### Chat Model

```text
GPT-4o mini
```

Model je bil izbran zaradi dobrega razmerja med:

* kakovostjo odgovorov,
* hitrostjo odziva,
* stroški izvajanja.

### Memory

```text
Window Buffer Memory
```

Omogoča ohranjanje konteksta med pogovorom.

### Retrieval Tool

```text
codex\_catalog\_search
```

Pinecone Vector Store Tool skrbi za pridobivanje relevantnih informacij iz kataloga.

\---

## 📜 Ključna pravila agenta

* Za vsa vsebinska vprašanja mora uporabiti `codex\_catalog\_search`.
* Odgovarja v jeziku uporabnika.
* Če informacije ni mogoče najti, uporabnika usmeri na `info@codex.si`.
* Zavrne vprašanja, ki niso povezana s katalogom ali podjetjem Codex.

\---

## 💬 Chat vmesnik

**Naslov:**

```text
Codex pomočnik
```

**Začetno sporočilo:**

Kratek pozdrav in predstavitev področij, pri katerih lahko chatbot pomaga.

\---

## 🛠️ Tehnološki sklad

|Komponenta|Orodje|
|-|-|
|Avtomatizacija / orkestracija|n8n|
|Pretvorba PDF → Markdown|CloudConvert|
|Shranjevanje datotek|Google Drive|
|Embeddings|OpenAI text-embedding-3-small|
|Vektorska baza|Pinecone|
|Chat model|GPT-4o mini|
|Memory|Window Buffer Memory|

\---

## 📁 Struktura repozitorija

```text
codex-rag-chatbot/
├── README.md/
├── workflows/
│   ├── rag-upload-pinecone.json
│   ├── chatbot.json
├── data/
│   ├── CODEX\_2010\_katalog\_NET\_links.md
├── prompts/
│   ├── system\_prompt\_en.md
│   └── system\_prompt\_sl.md
│   └── tool\_description.md
```

\---

## 💡 Arhitekturne odločitve

### Markdown kot vmesni format

Pretvorba PDF dokumenta v Markdown poenostavi nadaljnjo obdelavo in izboljša kakovost segmentacije besedila.

### Chunking z overlapom

Overlap med segmenti zmanjšuje izgubo konteksta na mejah posameznih chunkov.

### Ločena workflow-a

Indeksiranje in chatbot sta ločena procesa, kar omogoča enostavnejše vzdrževanje in boljšo skalabilnost.

### Obvezna uporaba retrieval orodja

Agent mora za vse vsebinske odgovore uporabiti Pinecone iskanje, s čimer se zmanjša možnost halucinacij in poveča zanesljivost odgovorov.

\---

## 🔐 Varnost

Vsi API ključi in poverilnice so shranjeni izključno v n8n Credentials.

Repozitorij ne vsebuje:

* OpenAI API ključev,
* Pinecone API ključev,
* Google Drive poverilnic,
* drugih občutljivih podatkov.

\---

## 📌 Status projekta

Projekt je bil razvit kot praktična implementacija RAG sistema za uporabo nad realnim prodajnim katalogom podjetja Codex d.o.o. in predstavlja primer integracije n8n, OpenAI in Pinecone v produkcijsko uporaben AI pomočnik.

