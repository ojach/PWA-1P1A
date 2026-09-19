Languages: [English](../../README.md) | [Français](README.fr.md) | [Español](README.es.md) | [Português](README.pt.md) | [日本語](README.ja.md)
# 1P1A (One Page One App) & OJapp Dynamic PWA Suite

> Verwandeln Sie eine Seite, ein Verzeichnis oder eine gesamte Website mit einem schlanken clientseitigen Skript und einem dynamisch erzeugten Data-URL-Manifest in eine eigenständige Progressive Web App (PWA).

---

## 🌟 Überblick

**1P1A (One Page One App)** ist ein clientseitiger PWA-Ansatz, der es überflüssig macht, für jede Seite oder jedes Tool eine statische `manifest.json` zu erstellen und zu pflegen.

Fügen Sie dem `<head>` der Seite ein OJapp-Skript hinzu. Es erzeugt dynamisch ein Web-App-Manifest als `data:application/manifest+json`-URL. Je nach gewähltem Modus können Besucher eine einzelne Seite, eine Verzeichnisgruppe oder eine gesamte Website mit eigenem App-Namen, eigener Beschreibung, eigenem Symbol, eigener Identität, Start-URL, eigenem Navigationsbereich und eigener Installationsdarstellung zum Startbildschirm hinzufügen.

Die Kernskripte benötigen weder Build-Schritt noch Benutzerregistrierung, externe API oder serverseitige Manifest-Erzeugung.

---

## 🚀 Hauptfunktionen

- **Kein Build-Schritt und kein Backend:** Funktioniert auf statischen Hosts wie Cloudflare Pages, GitHub Pages, Vercel und herkömmlichen Webservern.
- **Dynamische Data-URL-Manifeste:** Erzeugt das Web-App-Manifest zur Laufzeit im Browser.
- **Drei App-Strukturen:** Unterstützt Apps auf Seitenebene (1P1A), Verzeichnisebene (1G1A) und Website-Ebene (1S1A).
- **Optionale Query-Verarbeitung:** Fügt bei Aktivierung den aktuellen Query-String zu `id`, `start_url` und `scope` hinzu.
- **Benutzerdefinierte Metadaten:** Konfigurieren Sie App-Titel, Beschreibung, Symbol, Identität, Start-URL und Navigationsbereich mit Meta-Tags.
- **Installationsdarstellung:** Fügen Sie eine eigene Installationsbeschreibung und einen oder mehrere Screenshots (bis zu fünf) hinzu, ohne ein statisches Manifest zu pflegen.
- **Reine clientseitige Ausführung:** Die Free-Skripte funktionieren ohne Benutzerkonten oder externe API-Abhängigkeiten.

---

## 💻 Schnellstart & App-Strukturen

OJapp liest seine Meta-Tags beim Ausführen des Skripts. Platzieren Sie alle OJapp-Meta-Tags **vor** dem Script-Tag.

### 1. 1P1A: One Page One App

Machen Sie die aktuelle Seite – oder eine per Query konfigurierte Instanz dieser Seite – zu einer App auf dem Startbildschirm des Benutzers.

```html
<!-- Optionale Einstellungen müssen vor dem Skript stehen -->
<meta name="ojapp:query" content="true">
<meta name="ojapp:title" content="Mein benutzerdefiniertes Tool">
<meta name="ojapp:icon" content="/icon.png">

<script src="https://ojapp.app/js/ojapp_1p1a.js"></script>
```

Ohne optionale Meta-Tags werden automatisch die Seiten-URL, der Seitentitel und ein verfügbares Seitensymbol verwendet.

### 2. 1S1A: One Site One App

Machen Sie den gesamten Origin zu einer einzigen einheitlichen App.

```html
<script src="https://ojapp.app/js/ojapp_1s1a.js"></script>
```

Standardmäßig verwenden `id`, `start_url` und `scope` das Stammverzeichnis des Origins (`/`).

### 3. 1G1A: One Group One App

Verwandeln Sie ein Verzeichnis wie `/dashboard/`, `/tools/` oder `/docs/` in eine eigene App unter demselben Origin.

```html
<meta name="ojapp:id" content="/dashboard/">
<meta name="ojapp:start-url" content="/dashboard/">
<meta name="ojapp:scope" content="/dashboard/">

<script src="https://ojapp.app/js/ojapp_1s1a.js"></script>
```

Jede Verzeichnisgruppe kann eine andere ID und einen anderen Scope verwenden. Dadurch sind mehrere Gruppen-Apps unter einem Origin möglich, ohne separate Manifest-Dateien oder Build-Pipelines.

---

## 🔗 Per Query konfigurierte Apps

Aktivieren Sie die Query-Verarbeitung mit:

```html
<meta name="ojapp:query" content="true">
```

Beispiel:

```text
/timer/?time=5&mode=down&seconds=on&icon=blue
```

OJapp übernimmt den aktuellen Query-String in folgende Werte des erzeugten Manifests:

- `id`
- `start_url`
- `scope`

So kann die URL den ausgewählten Zustand einer App speichern, während `id` konfigurierte Instanzen unterscheidet und `start_url` diesen Zustand beim Start wiederherstellt.

Browser verarbeiten Manifest-Felder entsprechend ihrer jeweiligen Plattformimplementierung. Insbesondere kann ein Browser die Query im verarbeiteten `scope` normalisieren oder entfernen. OJapp schreibt die konfigurierte Query dennoch in alle drei Felder und überlässt dem Browser die Normalisierung.

Wenn Ihre Seite die Query nach dem ersten Laden erstellt oder ändert, navigieren Sie zur endgültigen Query-URL oder laden Sie sie neu, bevor Sie den Benutzer zum Hinzufügen zum Startbildschirm auffordern. Eine Änderung nur über die History API aktualisiert möglicherweise nicht die URL, die vom iOS-Ablauf **Zum Home-Bildschirm** erfasst wird.

---

## 🖼️ Installationsdarstellung

OJapp kann die Beschreibung und Screenshots anpassen, die in unterstützten Installationsoberflächen des Browsers angezeigt werden.

### Installationsbeschreibung

Verwenden Sie `ojapp:description`, wenn die Installationsoberfläche einen anderen Text als die Suchbeschreibung der Seite benötigt.

```html
<meta
  name="ojapp:description"
  content="Installieren Sie dieses Tool für schnellen Zugriff über Ihren Startbildschirm."
>
```

OJapp verwendet folgende Priorität:

1. `ojapp:description`
2. Standardmäßiges `<meta name="description">`
3. Keine Manifest-`description`, wenn keines von beiden vorhanden ist

So bleiben suchorientierte Seitentexte und installationsorientierte Hinweise voneinander getrennt.

### Ein Screenshot

Verwenden Sie für ein einzelnes Bild den ursprünglichen Tag ohne Nummer.

```html
<meta
  name="ojapp:screenshot"
  content="/images/install.png"
>
```

### Mehrere Screenshots

Mit nummerierten Tags können Sie bis zu fünf Bilder hinzufügen.

```html
<meta name="ojapp:screenshot-1" content="/images/install-1.png">
<meta name="ojapp:screenshot-2" content="/images/install-2.png">
<meta name="ojapp:screenshot-3" content="/images/install-3.png">
```

Sobald mindestens ein nummerierter Screenshot-Tag vorhanden ist, hat der nummerierte Satz Vorrang und der unnummerierte Tag `ojapp:screenshot` wird ignoriert.

Alle Screenshots im Satz sollten dasselbe Seitenverhältnis verwenden. Ein **quadratisches Bild im Format 1:1** wird empfohlen, da es sowohl in Desktop- als auch in mobilen Installationsoberflächen gut sichtbar bleibt. OJapp weist die ausgewählten Screenshots auf dem Desktop `wide` und unter Android `narrow` zu; das endgültige Layout wird vom Browser gesteuert.

---

## 🛠️ Metadatenreferenz

| Meta-Tag | Gilt für | Beschreibung | Standard / Fallback |
| :--- | :--- | :--- | :--- |
| `ojapp:title` | 1P1A / 1S1A | Legt den App-Namen fest | 1P1A: Seiten-`<title>`; 1S1A: Hostname |
| `ojapp:description` | 1P1A / 1S1A | Legt die Installationsbeschreibung im Manifest fest | Standard-Meta-Beschreibung der Seite; andernfalls weggelassen |
| `ojapp:icon` | 1P1A / 1S1A | Legt die URL des App-Symbols fest | Verfügbares Seitensymbol, danach OJapp-Standardsymbol |
| `ojapp:screenshot` | 1P1A / 1S1A | Legt einen Installations-Screenshot fest | Weggelassen |
| `ojapp:screenshot-1` bis `ojapp:screenshot-5` | 1P1A / 1S1A | Legt bis zu fünf Installations-Screenshots fest; der nummerierte Satz hat Vorrang | Weggelassen |
| `ojapp:query` | 1P1A / 1S1A | Auf `"true"` setzen, um die aktuelle Query in `id`, `start_url` und `scope` aufzunehmen | Deaktiviert; Query entfernt |
| `ojapp:id` | 1S1A / 1G1A | Legt die App-Identität im Manifest fest | Origin-Stammverzeichnis |
| `ojapp:start-url` | 1S1A / 1G1A | Legt die vom Startbildschirm geöffnete URL fest | Origin-Stammverzeichnis |
| `ojapp:scope` | 1S1A / 1G1A | Legt den Navigationsbereich fest | Origin-Stammverzeichnis |
| `ojapp:exclude` | 1P1A / 1S1A | Auf `"true"` setzen, um OJapp auf der Seite zu deaktivieren | Deaktiviert |

Alle benutzerdefinierten Werte für `id`, `start_url` und `scope` müssen zum selben Origin wie die aktuelle Seite aufgelöst werden.

---

## 📱 Plattformverhalten

### iOS / iPadOS (Safari)

Per Query konfigurierte Startbildschirm-Einträge wurden auf dem iPhone verifiziert. Mehrere Einträge, die von derselben Seite erstellt wurden, können unterschiedliche Startzustände, App-Namen und Symbole beibehalten.

Das installierte Symbol kann auch von `apple-touch-icon` abhängen. Seiten, die dynamisch ein Symbol auswählen, sollten es daher vor der Ausführung des OJapp-Skripts festlegen.

### Android (Chrome)

OJapp übernimmt die Query in die erzeugten Werte `id`, `start_url` und `scope`. Chrome kann den verarbeiteten Navigationsbereich gemäß der Web-App-Manifest-Spezifikation normalisieren. Die Identität mehrerer querybasierter Installationen kann je nach Browser- und Plattformversion variieren. Testen Sie das gewünschte Verhalten daher auf dem vorgesehenen Android-Gerät.

---

## 🧭 Auswahl eines Modus

| Modus | App-Einheit | Standardidentität / Start / Scope | Typischer Einsatz |
| :--- | :--- | :--- | :--- |
| **1P1A** | Aktuelle Seite | Aktueller Seitenpfad | Tools, Produkte, Artikel, Profile |
| **1G1A** | Verzeichnisgruppe | Expliziter Verzeichnispfad | Dashboards, Dokumentation, gruppierte Tools |
| **1S1A** | Gesamte Website | Origin-Stammverzeichnis `/` | Konventionelle websiteweite PWA |

---

## OJapp FREE

OJapp FREE bietet eine einfache Möglichkeit, die PWA-Designmuster 1P1A (One Page. One App.) und 1S1A (One Site. One App.) umzusetzen.

Für die normale Nutzung wird OJapp FREE über die offiziellen gehosteten Skripte bereitgestellt:

https://ojapp.app/

Die produktive OJapp-FREE-Laufzeit wird in diesem Repository nicht als JavaScript-Datei verteilt.

Referenzquellcode kann in diesem Repository als `.txt`-Dateien zum Lernen, Forschen, Ändern und für unabhängige Implementierungen bereitgestellt werden.

Wenn Sie OJapp FREE einfach verwenden möchten, nutzen Sie bitte das in der Dokumentation beschriebene offizielle gehostete Skript.

---

## Lizenz

Sofern nicht anders angegeben, steht der in diesem Repository enthaltene Quellcode unter der MIT-Lizenz.

Details finden Sie in der Datei `LICENSE`.

Die MIT-Lizenz dieses Repositorys gilt nur für Quellcode, der tatsächlich in diesem Repository veröffentlicht wurde.

Die unter `ojapp.app` bereitgestellte gehostete OJapp-FREE-Laufzeit und OJapp PRO sind von diesem Repository getrennt und nicht durch dessen MIT-Lizenz abgedeckt.

OJapp PRO ist proprietäre kommerzielle Software.

---

## 1P1A und UDA sind Designkonzepte

**1P1A (One Page. One App.)** und **UDA (User Defined App)** sind PWA-Designkonzepte, keine Produkte.

1P1A beschreibt einen Designansatz, bei dem die Anwendungsgrenze auf Seitenebene statt für die gesamte Website definiert wird.

UDA erweitert diese Idee, indem Benutzer eine App über einen bestimmten URL-Zustand, etwa Query-Parameter, definieren können.

OJapp ist eine Umsetzung und ein Dienst, der auf diesen Konzepten aufbaut.

Die MIT-Lizenz dieses Repositorys gilt nur für den hier veröffentlichten Quellcode. Sie gilt nicht für die Konzepte 1P1A oder UDA selbst.

---

## 🤝 Community & Feedback

Fragen, Testergebnisse, Sonderfälle und Funktionswünsche sind in diesem Repository willkommen.
Referenzimplementierungen können von der aktuellen Produktivversion von OJapp FREE abweichen und sollen nicht jede Aktualisierung der offiziellen gehosteten Laufzeit nachvollziehen.

- **Website / Dienstprogramme:** [OJapp 1P1A](https://ojapp.app/one-page-one-app/en/)
- **Entwickler:** OJapp / Ojach
