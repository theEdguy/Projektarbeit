---
marp: true
theme: default
paginate: true
header: "RAG-Lernassistent für Vorlesungsmaterialien"
footer: "Technisches Konzept | Prototype"
---

# RAG-Lernassistent
## Vom Vorlesungsskript zum aktiven Prüfungstraining

**Technisches Konzept für einen Prototype**

Markdown-Ingestion | Hybrides Retrieval | OpenClaw | Anki

---

# 1. Was ist RAG?
## Retrieval-Augmented Generation

Ein Sprachmodell erzeugt seine Antwort nicht nur aus dem Trainingswissen. Vor der Antwort werden passende Textstellen aus einer Datenquelle gesucht und als Kontext übergeben.

```text
Frage
   |
Suche in Vorlesungsdaten
   |
Passende Textstellen
   |
RAG-LLM erzeugt Antwort
```

- Das RAG-System speichert und durchsucht die Vorlesungsdaten.
- Das RAG-LLM verarbeitet die gefundenen Textstellen.
- Das Modell kann grundsätzlich ausgetauscht werden.
- Auf der RAG-Seite wird dafür ein Embedding-Modell benötigt.

### Warum RAG?

- Vorlesungsspezifische Inhalte können verwendet werden.
- Aktuelle freigegebene Materialien können nachträglich eingelesen werden.
- Antworten können mit Quellen aus den Vorlesungsfolien verbunden werden.

### Verschiedene RAG-Ansätze

Es gibt verschiedene RAG-Ansätze. Sie unterscheiden sich unter anderem bei der Aufteilung der Dokumente, der Suche und der Verarbeitung der gefundenen Textstellen.

- Einfache RAG-Systeme verwenden eine einzelne semantische Suche.
- Hybride RAG-Systeme kombinieren semantische und lexikalische Suche.
- Erweiterte RAG-Systeme können zusätzliche Schritte zur Prüfung oder Auswahl der Ergebnisse verwenden.

Der Prototype verwendet eine hybride Suche.

### Nutzen für das Lernen

- Studierende erhalten Erklärungen zu den verwendeten Unterlagen.
- Fragen können auf einzelne Kapitel oder Folien bezogen werden.
- Aufgaben und Lernkarten können aus den Vorlesungsdaten erzeugt werden.

---

# 2. Der Problemraum
## Warum ein LLM den Kontext verlieren kann

### Technische Grenzen großer Sprachmodelle

- **„Large“ bedeutet Breite:** LLMs verfügen über sehr viele Parameter und wurden mit einer großen Vielfalt an Trainingsdaten auf Allgemeinwissen optimiert.
- **Generalist statt Vorlesungsspezialist:** Ein LLM kennt viele Themen, aber nicht automatisch die Nomenklatur, Definitionen und Struktur genau dieser Hochschule oder dieses Kurses.
- **Fester Wissensstand:** Das Trainingswissen endet an einem bestimmten Zeitpunkt. Neue Forschung, Semesteränderungen und aktuelle Folien sind ohne externe Quellen nicht verfügbar.
- **Große Kontexte sind nicht automatisch gute Kontexte:** Ein komplettes Semester an Folien kann wichtige Details zwischen zu vielen Informationen verbergen.

**Konsequenz:** Für präzise Antworten braucht ein LLM gezielt ausgewählte, aktuelle und vorlesungsspezifische Belege.

---

# 3. Der technische Lösungspfad
## Technischer Ablauf

```text
Alle Materialien
   |
Chunking: ca. 500 Zeichen mit Überlappung
   |
Embeddings und semantische Suche
   |
Lexikalische Suche: Begriffe und Formeln
   |
Kontext für das LLM
```

- **Chunking:** Der Text wird meist in Abschnitte mit etwa 500 Zeichen geteilt. Eine Überlappung zwischen den Abschnitten reduziert Informationsverlust an den Grenzen.
- **Embeddings:** Ein SLM oder LLM wandelt Text und Suchanfrage in Vektoren um. Dadurch können inhaltlich ähnliche Textstellen gefunden werden.
- **Semantische Suche:** Die Vektoren werden verglichen. Die Suche berücksichtigt damit die Bedeutung einer Frage.
- **Lexikalische Suche:** Eine zusätzliche Suche findet exakte Fachbegriffe, Abkürzungen und Formeln.
- **Hybride Suche:** Die Ergebnisse beider Suchen werden zusammengeführt. Das LLM erhält dadurch mit wenigen Suchschritten den passenden Kontext.
- **Modell:** Das Sprachmodell ist austauschbar. Mistral kann als kostenfrei nutzbare Option verwendet werden. Auch andere Modelle sind möglich, wenn das benötigte Embedding-Modell auf der RAG-Seite verfügbar ist. Bei Anforderungen an den Datenschutz kann ein Modell mit Hosting in Europa gewählt werden.

**Ergebnis:** Das LLM erhält einen kleinen und passenden Kontext.

---

# 4. OCR und Textaufbereitung
## Verarbeitung von Folien

Folien können Text, Bilder, Tabellen, Formeln und Screenshots enthalten.

OCR erkennt Text in Bildern und gescannten PDF-Seiten.

Die erkannten Inhalte werden als strukturierter Text gespeichert.

Gespeichert werden zusätzlich:

- Foliennummer
- Kapitel und Überschrift
- Dateipfad
- Position im Quelldokument

**Verarbeitung:** OCR oder Markdown-Parser -> Textabschnitte -> Embeddings und Suchindex -> LLM-Kontext.

---

# 5. Vorteile für die Hochschule

### Einheitliche Datenbasis

- Vorlesungsfolien werden mit Überschriftenhierarchien, Alternativtexten und Metadaten erstellt.
- Die Hochschule stellt die geprüften Vorlesungsdaten zentral bereit.
- Dozierende entscheiden, welche Inhalte für ein Fach freigegeben werden.

### Fachweiser Zugriff über einen MCP-Prototyp

- Der Zugriff erfolgt über einen MCP-Prototyp (Model Context Protocol).
- Ein Server verbindet die MCP-Schnittstellen mit der gemeinsamen Datenbank.
- Die Datenbank bleibt gleich; die Inhalte werden nach Vorlesung und Fach getrennt.
- Für jedes Fach wird festgelegt, welche Vorlesungsdaten verwendet werden.
- Alle Studierenden eines Fachs erhalten denselben geprüften Wissensstand.
- Zugriffe können über Passwörter eingeschränkt werden.
- Quellen und verwendete Modelle können zentral kontrolliert werden.

### Einsatz in Laboren

- Studierende können in Laborveranstaltungen ein eigenes RAG-System aufbauen.
- Dabei bearbeiten sie praktische Aufgaben aus den Bereichen Datenbanken, Kommunikationsnetze und verteilte Systeme.
- Mögliche Bestandteile sind Datenaufbereitung, Chunking, Embeddings, Vektorsuche und MCP-Kommunikation.


Die Nutzung kann als Angebot bereitgestellt werden. Professoren und Studierende müssen sie nicht verwenden.

---

# 6. Gesamt-Workflow
## Ablauf und Systemschichten

```text
Markdown
   |
OCR oder Markdown-Parser
   |
Chunking und Suchindex
   |
Semantische und lexikalische Suche
   |
RRF-Fusion
   |
OpenClaw: Dialog, Aufgaben, Anki
```

### Systemschichten

`Chat-Interface` -> `OpenClaw` -> `MCP-Prototyp` -> `PostgreSQL + pgvector`

Das RAG-System liefert Textstellen mit Quellen. OpenClaw verwendet diese Textstellen für Dialog und Aufgaben.

### Vereinfachter Ablauf einer Anfrage

```text
1. OpenClaw: Frage
   |
   v
2. RAG-LLM: Suchanfrage
   |
   v
3. MCP-Prototyp: Tool-Aufruf
   |
   v
4. Datenbank: relevante Top-K-Chunks
   |                 |
   +-- Chunks -------+--> RAG-LLM: Frage + Chunks
                              |
                              v
                    Antwort mit Quellen
                              |
                              v
                         OpenClaw
```

OpenClaw nimmt die Frage entgegen und gibt sie an das RAG-LLM weiter. Das RAG-LLM fordert über den MCP-Prototyp passende Textstellen an. Die Datenbank liefert nur relevante Top-K-Chunks mit Quellen. Diese Chunks werden an das RAG-LLM zurückgegeben. Das RAG-LLM erzeugt daraus die Antwort und gibt sie an OpenClaw zurück.

---

# 7. Ingestion und Retrieval
## Datenaufnahme und Suche

### Markdown- und Text-Chunking

- Der Text wird in Abschnitte mit etwa 500 Zeichen und Überlappung geteilt.
- Überschriften werden als Metadaten übernommen.
- Jeder Chunk trägt Foliennummer, Kapitelpfad und Quelle.
- Formeln und Fachbegriffe bleiben im passenden Textabschnitt.

### Suche

- **Semantisch:** Embeddings werden in pgvector gespeichert und über HNSW gesucht.
- **Lexikalisch:** PostgreSQL sucht über einen Trigrammindex nach exakten Begriffen.
- **Kombination:** Reciprocal Rank Fusion (RRF) führt beide Ergebnislisten zusammen.

$$RRF(d) = \sum_{m \in M} \frac{1}{k + rank_m(d)}, \quad k=60$$

---

# 8. Lerninteraktion
## Dialog mit OpenClaw

OpenClaw verarbeitet die gefundenen Textstellen und führt einen Dialog mit dem Studierenden:

- Inhalte werden in einzelnen Abschnitten erklärt.
- Zwischenfragen prüfen das Verständnis.
- Antworten verweisen auf die verwendeten Chunks.
- Der Dialog kann an den bisherigen Lernstand angepasst werden.

### Antwortgrundlage

Antworten sollen nur aus den abgerufenen Vorlesungsdaten erzeugt werden.

---

# 9. Aufgaben und Anki
## Generierung aus Vorlesungsdaten

Aus den abgerufenen Textstellen können erzeugt werden:

- Multiple-Choice-Fragen.
- Freitextaufgaben.
- Frage-Antwort-Karten für Anki.

### Anki-Artefakt

```text
Frage;Antwort;Quelle
"Was bedeutet ...?";"Definition ...";"Folie 42 / Kapitel 3"
```

Das RAG-System liefert die Textstellen. OpenClaw erzeugt daraus Aufgaben und Karten.

---

# 10. MCP-Prototyp
## Tools für Datenaufnahme und Suche

### Model Context Protocol über Stdio

Der Prototyp stellt die Verbindung zwischen dem KI-Client und dem RAG-System her.

| Tool | Eingabe | Ergebnis |
|---|---|---|
| `rag_ingest` | `source_path`, `project_id` | Indexierte Markdown-Chunks |
| `rag_query` | `query_text`, `retrieval_mode` | Top-K-Chunks mit Quellen |

### Technische Angaben

- Validierung über typisierte Dataclasses.
- Einheitliche JSON-RPC-Fehler: `timeout`, `validation_error`.
- Persistenz in PostgreSQL 16: `document_versions`, `document_chunks`.
- Ein MCP-Prototyp verbindet die Tools mit der gemeinsamen Datenbank.
- Die Inhalte werden über eine Fach- oder Vorlesungskennung getrennt.
- Ein Passwort kann den Zugriff auf einzelne Fächer einschränken.

---

# 11. Mögliche Erweiterung
## Persönlicher Wissensgraph

Ein persönlicher Wissensgraph ist nicht Teil des Prototypen. Er kann bei ausreichender Zeit und verfügbaren Ressourcen als Erweiterung untersucht werden.

- Für jeden Studierenden wird gespeichert, welche Teile der Unterlagen bearbeitet wurden.
- Der Graph kann festhalten, welche Zusammenhänge der Studierende verstanden und selbst erklärt hat.
- OpenClaw kann den Lernprozess über seine Memory-Funktion begleiten.
- Im sokratischen Dialog benennt der Studierende Beziehungen zwischen Konzepten.
- OpenClaw kann daraus eine Graphstruktur erzeugen oder den dafür benötigten Code erstellen.
- Bei späteren Gesprächen kann der Graph erneut abgefragt werden, um den Lernstand einzuschätzen.
- Das RAG-System liefert weiterhin die fachlichen Informationen zu den Themen.
- Mögliche Technologien sind Memgraph oder Neo4j mit Cypher.

### Forschungsfrage

**Wie kann ein persönlicher Wissensgraph den Lernstand eines Studierenden aus Dialogen und Vorlesungsdaten abbilden?**

---

# Fazit
## Prototype und Zuständigkeiten

**RAG** sucht passende Textstellen aus den Vorlesungsdaten.

**OpenClaw** verwendet die Textstellen für Dialog, Aufgaben und Anki-Karten.

**MCP** stellt die Verbindung zwischen Anwendung und RAG-System bereit.

Der Prototype besteht aus Datenaufbereitung, hybrider Suche und Lernfunktionen.