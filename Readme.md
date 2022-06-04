# Tabellen, Formulare und Skripten für die ADFC Radfahrschule (RFSA)

## Links

Die Dateien liegen derzeit im Ordner AG Radfahrschule Anmeldungen / Anfänger Frontend-Formulare. Dort die Tabelle "RFS_1A Backend" und das Formular "RFS_1A Frontend".

Das Formular kann unter https://forms.gle/Uipy5wbWnch6jHg58 ausgefüllt werden. Die Backend-Tabelle dazu kann unter
https://docs.google.com/spreadsheets/d/163EnaAw4A0_BP6zuo51VSyz02MNHTfI3_t8MyQ2JUP4/edit?usp=sharing
angesehen werden.

## Neuerungen

Das Formular hat keinen Limiter und kein Skript mehr. Die Email-Verifikation wird von der Backend-Tabelle aus gesteuert. Die Kurse im Formular speisen sich aus einem Sheet der Backend-Tabelle. Das Formular wird nach jeder Buchung auf den aktuellen Stand der freien Kursplätze gebracht. Von der Tabelle aus kann das Formular auch über einen Update-Knopf aktualisiert werden, wenn z.B. Kurse hinzugefügt oder gelöscht werden.

## Die Backend-Tabelle

Die Backend-Tabelle hat die 3 Sheets Email-Verifikation, Buchungen, Kurse.

### Notizen

Für die Buchungen- und Kursetabelle gilt: Steht in der Spalte A einer Tabelle eine Notiz, wird diese Zeile ignoriert. Buchungen mit einer ungültigen IBAN bekommen z.B. eine Notiz "IBAN ungültig".
Kurse, die stattgefunden haben, können mit "Abgeschlossen" o.ä. notiert werden. Stornierte Kurse können als "Storniert" notiert werden, usw. Damit soll das Löschen von Zeilen unnötig gemacht werden, damit die Historie einsehbar bleibt.

### Kurse

Im Kurse-Sheet stehen der Name des Kurses, Uhrzeit und Tage (1 bis maximal 8), die Zahl der Kursplätze und der Restplätze.

### Buchungen

Hierhin schreibt das Formular für die Buchung die Daten. Vom Skript werden noch weitere Felder für die Anmeldebestätigung und die Email-Verifikation hinzugefügt. Für die Abbuchung werden weitere Felder hinzukommen.

### Email-Verifikation

Hierhin schreibt das Formular für die Email-Verifikation die Daten. Wenn in diesem Sheet eine Email steht, gilt sie als verifiziert und das Ausfüllen des Verifikations-Formulars wird bei Folgebuchungen nicht wieder verlangt.

## Das Skript

### Einstiegspunkte

Über Skripteditor/Bearbeiten/Trigger des aktuellen Projekts ist ein Trigger "Aus Tabelle - Bei Formularübermittlung" gesetzt, der die Funktion dispatch aufruft.

Über die "onOpen"-Funktion wird im Backend ein Menü-Eintrag ADFC-RFSA erzeugt, mit den Menüpunkten "Anmeldebestätigung senden" und "Update". Wie bei der RFS wird derzeit die Anmeldebestätigung NICHT automatisch verschickt. Vielmehr wählt man eine Zeile im Buchungen-Sheet und sendet dann die Anmeldebestätigung für diesen Kurs.

Update kann man aufrufen, wenn man in Buchungen oder Kurse irgendwelche Änderungen durchgeführt hat, wie z.B. Stornierungen oder neue Kurse. Damit wird für alle Kurse die Anzahl der freien Zimmer gleich der Anzahl verfügbarer Zimmer minus die Anzahl gebuchter Zimmer gesetzt, und das Formular upgedatet (geupdatet?). Der Aufrufer wird über jede Änderung informiert, um Überraschungen vorzubeugen. Vor allem wird auch vor Überbuchungen gewarnt (Zahl freier Zimmer < 0), die durch Manipulation der Tabellen entstehen könnten.

### Sortieren

Die Tabellen können beliebig sortiert werden, um z.B. in Buchungen alle Buchungen für einen Kurs oder alle Buchungen einer Person zusammenhängend zu sehen. Die Zeile 1 mit den Headern ist fixiert und sollte das auch bleiben (Ansicht/Fixieren/1 Zeile).

### Erste Mail

Nach Abschicken des Formulars erhält der Teilnehmer unter der von ihm angegebenen Email-Adresse eine erste Mail. In dieser steht, was er gebucht hat, ob er vorgemerkt ist, und er wird ggfs. zur Verifikation der Email aufgefordert, falls die Email-Adresse noch unbekannt ist.

### Zweite Mail / Anmeldebestätigung

Diese wird durch den Knopf "Anmeldebestätigung senden" verschickt, und enthält den Kurs und den Preis mit der Androhung der baldigen Abbuchung.

### Löschen

Bitte lieber nicht. Lieber in der ersten Spalte eine Notiz setzen.

### Änderungswünsche

Je eher, desto weniger Zeitdruck, desto lieber.

### Fake-IBAN

DE91100000000123456789 ist eine syntaktisch gültige IBAN für Testzwecke.

### Technische Details

Das Skript ist in Typescript programmiert (Endung .ts). Durch "flask push" wird es von der Datei Code.ts zur Google Apps Skript-Datei Code.gs übersetzt. In der ersten Zeile von Code.gs steht deshalb

```
// Compiled using ts2gas 3.6.4 (TypeScript 4.1.3)
```

### Setup Visual Studio Code / Typescript

See https://developers.google.com/apps-script/guides/typescript.
Installieren: node.js, visual studio code.
Dann:

```
    npm install -g @google/clasp
    npm install -S @types/google-apps-script
    clasp clone <scriptid>  # im Projektverzeichnis, scriptid = id des skripts, daß der SkriptEditor aufmacht.
```

Danach existiert Datei .clasp.json mit der scriptid.
In VSCode das Projektverzeichnis öffnen. Terminal öffnen, clasp push -w starten.
