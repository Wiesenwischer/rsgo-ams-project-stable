# Release Notes 4.0 (Deutsch)

> Hauptversion **4.0.0** · Änderungen seit Version **3.0.2**

4.0 ist eine **große Hauptversion**. Kern ist eine **komplett neu entwickelte Terminplanung** im Stil von Microsoft Project, dazu eine durchgängig modernisierte Oberfläche, neue Web-Module (Auswertungen und Kapazitätsplanung, Zusammenarbeit, Terminplan-Import), eine neu aufgebaute **Ressourcen- und Ist-Stunden-Führung**, ein neues **Administrationsportal** und ein neues Installations-/Rollout-Verfahren über **ReadyStackGo**.

> 📝 **Für Administratoren:** Vor dem Update bitte den Abschnitt „Upgrade auf 4.0 — Hinweise für Administratoren“ am Ende dieser Seite lesen. Er enthält Pflichtschritte nach dem Update und Voraussetzungen an den ams.erp-Stand.

---

## ⭐ Highlights auf einen Blick

| | Highlight |
|---|---|
| 1 | **Neue Terminplanung** mit echter Vorgänger-/Nachfolger-Berechnung, MS-Project-Einschränkungen und Ziehen im Gantt wie in MS Project |
| 2 | **Projektkalender mit Arbeitszeiten** (Arbeitstage, Zeitfenster, Feiertage, ERP-Sync) |
| 3 | **Modernisierte Oberfläche** mit durchgängiger Sprache (DE/EN) |
| 4 | **ERP-Integration** direkt in der Gantt (Bezüge, Stücklisten, Sync-Status) |
| 5 | **Neue Web-Auswertungen** mit integrierter Kapazitätsplanung, Farbsteuerung, Benutzergruppen und PDF-Export |
| 6 | **Ressourcen und Ist-Stunden**: mehrere Arbeitsplätze je Aufgabe, Kapazitätsablösung nach Projektstruktur, Ist-Stunden-Aufbereitung im Administrationsportal |
| 7 | **Angebote und Aufträge** als Terminplanbezüge in einen Plan einfügen |
| 8 | **Terminplan-Import aus dem ERP** als Teil von ams.project |
| 9 | **Neuer Installationsmechanismus über ReadyStackGo** (geführter, zentraler Rollout) |
| 10 | **Neues Administrationsportal** (Web) für Konfiguration, Benutzerrechte, Wartungsmodus, Fehler-Warteschlange, Ist-Stunden und Client-Verteilung |

---

## Neue Terminplanung (MS-Project-orientiert)

Die Terminberechnung wurde von Grund auf neu gebaut (Critical-Path-Method, Vorwärts-/Rückwärtsrechnung):

- **Vorgänger-/Nachfolger-Beziehungen** in allen vier Typen: **Ende–Anfang (FS)**, **Anfang–Anfang (SS)**, **Ende–Ende (FF)**, **Anfang–Ende (SF)**, jeweils mit **Zeitabstand (Lag/Lead)**. Verschiebt sich ein Vorgang, ziehen abhängige Vorgänge automatisch korrekt nach.
- **Einschränkungen wie in MS Project**: „So früh wie möglich" (ASAP), „So spät wie möglich" (ALAP), „Frühestens/Spätestens anfangen am" (SNET/SNLT), „Frühestens/Spätestens enden am" (FNET/FNLT), „Muss anfangen/enden am" (MSO/MFO).
- **Konflikte werden ehrlich angezeigt** statt stillschweigend „geglättet": Ist ein Termin nicht einhaltbar, entsteht – wie in MS Project – **negativer Puffer** mit Warnhinweis, statt die Vorgaben unbemerkt zu verbiegen. Endet eine harte Einschränkung erst nach dem Projektende, nennt die Konfliktliste das als eigene Meldung „Terminplan nicht erfüllbar (Projektende überschritten)" mit beiden Daten.
- **Neue Warn- und Meldungs-Spalte (Indikatoren)** in der Gantt: eine eigene Spalte zeigt pro Vorgang auf einen Blick alle Hinweise – Termin-Warnungen (nicht einhaltbare Einschränkungen, negativer Puffer, Vorgänger-/Nachfolger-Konflikte, auf Arbeitstage verschobene Termine) ebenso wie ERP-Sync-Hinweise (Termin-Abweichung bzw. -Überschreitung). Jedes Symbol hat einen erklärenden Tooltip.
- **Sammelvorgänge (Summary Tasks)** fassen ihre Unteraufgaben zusammen: Anfang, Ende und Dauer werden automatisch hochgerollt, der Balken reicht vom Anfang bis zum Ende der Teilvorgänge. Nur „Muss anfangen am"/„Muss enden am" legt den jeweiligen Rand fest; ein manuell terminierter Sammelvorgang behält beide Ränder seines eigenen Balkens. Teilvorgänge außerhalb dieses Rahmens werden als Konflikt gemeldet. Einen Termin, den ein Sammelvorgang vorgibt, übernehmen seine Teilvorgänge als Obergrenze: Sie bleiben an ihrem frühesten Termin, ein verpasster Termin erscheint als negativer Puffer.
- **Automatisch oder manuell** terminierte Vorgänge: Manuell festgelegte Vorgänge behalten ihre Termine, nehmen aber weiterhin an der Konfliktprüfung teil. Ihre Termine rasten auf Arbeitsbeginn und Arbeitsende des Kalenders ein. Manuell terminierte Unteraufgaben bleiben unter einem Sammelvorgang unverändert – unabhängig davon, ob der Sammelvorgang automatisch oder manuell terminiert ist.
- **Meilensteine** mit korrektem Zeitbezug: Sie liegen einheitlich am Tagesende und verhalten sich an arbeitsfreien Tagen sauber; ein neuer Meilenstein wird auf das Ende der bisherigen Spanne gesetzt. Das Umschalten eines ERP-Vorgangs auf „Meilenstein" verschiebt seinen ERP-Liefertermin nicht. Vorgänge mit Unteraufgaben können nicht als Meilenstein markiert werden.
- **Ziehen im Gantt wie in MS Project**: Ein automatisch geplanter Vorgang bleibt beim Ziehen automatisch geplant. Er erhält „Anfang nicht früher als" mit dem Ablegetag, behält seine Dauer und folgt weiter seinen Vorgängern; ein gezogener Sammelvorgang nimmt seine Teilvorgänge mit. Mit gedrückter **Alt**-Taste wird der Vorgang dort festgelegt, wo Sie ihn ablegen, und manuell geplant. Manuelle Vorgänge, Meilensteine, „So spät wie möglich" und Vorgänge mit einer anderen Termin-Einschränkung werden immer festgelegt; ihre Einschränkung bleibt gespeichert. Dasselbe gilt in der Multiprojektauswertung (Gantt und Zeitachse).
- **Dauer per Ziehen ändern** berücksichtigt den Kalender (Wochenenden/Feiertage); die im Tooltip angezeigte Dauer entspricht der gespeicherten.
- **Große Terminpläne** werden schnell neu berechnet – bei 5.000 Vorgängen in rund 0,2 Sekunden je Änderung, auch bei sehr tief verschachtelten Plänen.

> 📝 **Hinweis**: Da 4.0 eine neue Terminplanungs-Engine einführt, werden bestehende Projekte beim Update auf die neue Engine umgestellt. Nach dem Update ist im Administrationsportal einmalig die **Revision für alle Projekte** auszuführen (siehe Abschnitt „Upgrade auf 4.0 — Hinweise für Administratoren“).

## Projektkalender mit Arbeitszeiten

- Echte **Arbeitstage und Arbeitszeitfenster** (z. B. Mo–Fr 08:00–17:00, mehrere Fenster pro Tag für Mittagspausen) statt reiner Kalendertage.
- **Ausnahmen/Feiertage** auf einer eigenen Unterseite je Kalender; sie gelten bei der Terminplanung als arbeitsfrei.
- **ERP-synchronisierte Kalender** sind in der Oberfläche schreibgeschützt (Quelle ist das ERP) und in Karten- und Tabellenansicht mit „ERP-synchronisiert" gekennzeichnet.
- **Standard-/Pflichtkalender**: jedes Projekt bekommt einen Kalender; ohne explizite Zuweisung greift ein Standardkalender.
- Zuweisung pro Projekt unter **Einstellungen → Kalender**.
- Detaillierte Schritt-für-Schritt-Anleitung: siehe Kalender-Guide.

## Projektliste & Archivierung

- **Neue Terminplan-Auswahlseite**: zentrale Einstiegsseite, auf der Terminpläne ausgewählt und geöffnet werden – wahlweise als **Kartenansicht** oder als **sortierbare Tabellenansicht** (umschaltbar, die Wahl wird gespeichert). Nach der Anmeldung erscheint direkt diese Projektliste.
- **Suche und Filter** über Projektname, Manager, Sachbearbeiter, Geschäftsbereich und Revision sowie **Status-Tabs** (Alle / Aktiv / Archiviert) mit Anzahl-Anzeige. Die Datumsspalten („Erfasst", „Archiviert am") sortieren chronologisch.
- **Projekt archivieren**: Terminpläne lassen sich direkt aus der Projektliste archivieren. Archivierte Projekte erscheinen im Tab „Archiviert" und werden schreibgeschützt geführt. Die Archivierung läuft asynchron mit Fortschrittsanzeige in Echtzeit.

## Projekt aus Auftrag/Angebot anlegen

- **Neues Projekt direkt aus einem Auftrag oder Angebot** erzeugen: der Anlegen-Dialog ist jetzt web-basiert und übernimmt die ERP-Daten (z. B. Auftrags-/Angebotsnummer, Termin, Geschäftsbereich) als Vorbelegung; das angelegte Projekt bleibt mit dem ERP-Beleg verknüpft.

## Terminplan-Import aus dem ERP

- Der bisher getrennt betriebene **„Terminplan-Import"** ist Teil von ams.project. Die Web-Seite listet die Terminpläne des ERP mit Filter, Sortierung und Blättern, importiert einen Terminplan nach Rückfrage als **neues Projekt**, zeigt den Fortschritt live und je Terminplan Import-Status und Protokoll. Sie ist im Browser unter der Adresse von ams.project mit dem Pfad `/projectimport` erreichbar (z. B. `http://<server>:5000/projectimport`).
- Nur angemeldete Benutzer können importieren; der importierende Benutzer wird als Ersteller des Projekts geführt.
- Importierte Vorgänge sind mit ihrem ERP-Auftrag bzw. -Angebot verknüpft; Vorgänge ohne ERP-Position hängen am ganzen Auftrag. Ein Bezug auf einen Beleg, den es im ERP nicht gibt, lehnt den Import mit Nennung von Beleg und Vorgang ab.
- Der Kalender des ERP-Terminplans wird über seinen ERP-Code übernommen; ist er unbekannt, bricht der Import mit einer verständlichen Meldung ab. Fehlen einzelne ERP-Werte, nennt die Prüfung den fehlenden Wert.
- Ist die Einstellung „höchstens eine Ressource je Aufgabe" eingeschaltet, legt der Import für jede weitere Ressource einer Aufgabe eine eigene Aufgabe „Name der Aufgabe (Ressource)" mit denselben Terminen und Verknüpfungen an; das Import-Protokoll nennt sie.

## Angebote & Aufträge in den Terminplan einfügen

- **Angebote und Angebotspositionen** lassen sich über den Dialog **„Terminplanbezüge"** direkt in einen bestehenden Plan einfügen (**„Angebote zuordnen"**) – wahlweise das Angebot als Ganzes oder gezielt einzelne Positionen.
- Die eingefügten Bezüge erscheinen als **ERP-Vorgänge in der Gantt** (Belegnummer, Typ-Symbol, Beschreibung) und bleiben **live mit ams.erp synchronisiert**.
- Ebenso lassen sich **Aufträge und Auftragspositionen** zuordnen; bestehende Bezüge können einzeln oder gesammelt wieder entfernt werden.
- Die **Angebots- und Auftragssuche** arbeitet serverseitig und schränkt bei der Eingabe direkt ein, statt vorab die komplette Liste zu laden.

## ERP-Integration

- **ERP-Bezüge und -Termine** direkt in der Gantt sichtbar: Beschriftung, Typ-Symbole und Tooltips, auch für live angelegte ERP-Vorgänge.
- **Stücklisten-Synchronisation** mit korrekter Dauer (aus der Arbeitstage-Spanne, nicht mehr fix „1 Tag") und ohne fälschliches Meilenstein-Verhalten.
- **Sync-Status pro Aufgabe** („in Sync / nicht in Sync") und Schaltfläche **„ERP neu synchronisieren"**.
- Robustere ERP-Datum-Verteilung (zentraler Datum-Hub je Projekt).
- Ein Klick auf den Statusleisten-Zähler **„ERP-Termin(e)"** öffnet die Konfliktübersicht direkt beim Abschnitt „ERP-Termine"; ein ausgeblendeter Detailbereich wird dafür wieder eingeblendet.
- **Projektkopf ins ERP**: Beim Öffnen einer neuen Revision werden Geschäftsbereich, Projektleiter und Sachbearbeiter aus ams.project in den ERP-Projektkopf zurückgeschrieben – wie beim Speichern der Projektinformationen. Ein in ams.project leerer Wert leert dabei auch den Wert im ERP.

## Aufgaben-Detailansicht (modernisiert)

- Der Detailbereich einer Aufgabe ist neu und web-basiert, eingebettet im Desktop-Client.
- Reiter für **Vorgänger**, **Diskussion**, **Notizen (Memo)** und **Stückliste (BOM)**.
- Einschränkungen/Termine einer Aufgabe werden hier gepflegt, konsistent mit der neuen Terminplanung.
- Die Detailansicht lässt sich als **eigenes Fenster „Task-Details" ablösen**, das zuverlässig im Vordergrund bleibt. Ein hängengebliebener oder ausgeblendeter Detailbereich lässt sich jederzeit über **„Detailbereich zurücksetzen"** wiederherstellen; der Konflikte-Bereich ist scrollbar.
- **Projektleiter und Sachbearbeiter** des Projektkopfs stehen in allen Aufgaben, auch in Aufgaben, die nach dem letzten Speichern der Projektinformationen angelegt wurden.
- **Stundenbild der Zuordnungen**: Plan, Verteilt, Geleistet und **Reststunden** (Plan − Verteilt − Geleistet je Zeile). An Arbeitsplatz-Zeilen steht bei „Verteilt" „–"; wurde mehr verteilt oder gebucht als geplant, erscheinen die Reststunden negativ und rot wie im Terminplan. Die konsolidierte Karte zeigt einen gestapelten Stundenbalken, die Karte „Plan vs. Ist" zwei Balken (Plan und Geleistet). Summenzeile, Stundenbalken und die Gantt-Spalten „Planstunden"/„Reststunden" zeigen denselben Plan, ohne Stunden doppelt zu zählen, die ein Arbeitsplatz von seiner Gruppe übernimmt.
- **Block „Aus Unteraufgaben"** in der Karte „Zuweisungen": Er zeigt die in Unteraufgaben geplanten, verteilten, geleisteten und restlichen Stunden (Arbeitsplätze eingerückt), umschaltbar „nach Auftrag" / „ohne Auftrag"; die Wahl wird je Browser gemerkt. Je Arbeitsplatzgruppe schließt eine **Σ-Zeile** den gesamten Bedarf des Asts ab (Plan, Geleistet, Rest).
- **Ablösung sichtbar**: Jede Arbeitsplatz-Zeile sagt, welche Gruppen-Planung sie ablöst (z. B. „löst ab von 2 Aufgabe 1", mit der Vorgangs-ID) oder ob sie „zusätzlich" zählt. Trägt eine Unteraufgabe einen Gruppen-Satz derselben Gruppe, weist ein Hinweis an der Gruppenzeile auf die Doppelzählung hin.
- **Herkunft gebuchter Stunden**: Wechselt ein Vorgang den Auftrag, steht unter der Zeile des Arbeitsplatzes oder der Gruppe, woher gebuchte Stunden stammen, z. B. „davon 6 STD aus Auftrag 20260506" (mehrere Aufträge kommagetrennt). Plan, Verteilt, Geleistet, Reststunden und die Summenzeile ändern sich dadurch nicht. Der Hinweis erscheint, sobald der Arbeitsplan das nächste Mal rechnet.
- **Ist-Stunden je Zuordnung buchen**: Gebucht wird in der Zeile der jeweiligen Zuordnung; der Stundenvorschlag ist die Restzeit genau dieser Zuordnung, und der Buchungsdialog nennt, worauf gebucht wird. Liegt nur ein Auftrag ohne Position vor, wird auf den Auftragskopf gebucht. Die Ist-Stunden einer Aufgabe zählen alle Zuordnungen, auch eine frühere, inzwischen ersetzte; solche Zeilen sind als inaktiv gekennzeichnet.
- **„Stunden neu verteilen"** (auch im Desktop-Client): Die Aktion fragt in einem Dialog nach, bietet optional „Buchungen aus dem ERP neu einspielen" an, zeigt den Fortschritt live und meldet „fertig" erst, wenn alle Kapazitätsdaten geschrieben sind. Läuft bereits eine Neuverteilung, wird der Auftrag eingereiht („wartet – Platz n").
- **Ressource zuweisen**: Der Zuweisungs-Picker öffnet direkt in der Arbeitsplatzgruppe der Aufgabe, hat „Abbrechen", und ein zweiter Klick auf eine Kachel hebt die Auswahl auf.
- Die Schalter **„Relevant für Kapazitätsplanung"** und **„Relevant für Kostenkalkulation"** zeigen den gespeicherten Wert und ändern sich nur durch ausdrückliches Umschalten.
- Die projektweite **Konfliktübersicht** aktualisiert sich live und bleibt vollständig; die Nummer vor dem Aufgabennamen im Kopf folgt Verschiebungen und Umsortierungen.
- Tabellen und Hinweise passen auch in einen schmalen Detailbereich, ohne seitliches Scrollen oder abgeschnittene Spalten.

## Auswertungen & Kapazität

### Web-Auswertungen

- Neue **Web-Auswertungen** mit **Dashboards** und einem **Filter-Assistenten** (Konfiguration ohne SQL-Kenntnisse).
- **Interaktive Diagramme**: Hot-Edit, Drill-down, Hyperlinks, Drucken und **PDF-Export** direkt aus der Analyse-Seite. **Erweiterter PDF-Export**: einstellbare Ränder, Seitenzahlen, „an Breite anpassen" sowie optionale Aufteilung großer Diagramme auf mehrere Seiten (N × M); der Export funktioniert auch im eingebetteten Modus und exportiert alle Diagramme.
- **Kapazitätsplanung im Auswertungsmodul**: Kapazitäts-Kacheln stehen zusammen mit allen übrigen Auswertungen auf der Startseite; jede Auswertung öffnet mit einheitlichem Kopf (Typ-Symbol, Startseite und Farbsteuerung als Symbole). Kacheln aller Auswertungsarten werden im selben Dialog gepflegt (Auswertungsarten 1–5); der Zeitraum bleibt beim Wechsel der Auswertungsart erhalten, Kacheln ohne Filterangabe zeigen „Alle".
- **Kapazitätsauswertung** mit klareren Diagrammen: einstellbare Diagrammgröße und Spaltenanzahl, Auswahllisten für Auswertungsart/Gruppierung, Verfügbar- und Kapazitätsfaktor-Linien sowie deutlich hervorgehobene Überlast-Hinweise. Die Linien „Kapazität Plan" und „Kapazität PZE" sind einzeln abschaltbar (auch je Kachel); Serien ohne Wert erscheinen nicht in der Legende. Der Kapazitätsausgleich füllt gegen die gewählte Überlast-Linie.
- **Filter**: Aktive Filter erscheinen als entfernbare Chips, im Kontextmenü eines Balkens gibt es „Filter aufheben", die Filterzeile lässt sich ausblenden.
- **Darstellung**: Datum, Perioden und Zahlen folgen der Sprache der Oberfläche; Tooltips erscheinen verzögert. Im Gesamt- und Verlaufsdiagramm stehen die Werte eines Balkens im Kontextmenü, im Belastungsgebirge der angeklickte Wert im Kopf des Menüs. Die Detailtabelle zeigt die Herkunft jeder Zeile und den %-Anteil auch je Zeile.
- **Farbsteuerung**: Farbsets legen die Balken- und Linienfarben in Gantt-Auswertung und Kapazitätsplanung fest (Palette, Wert-Übersteuerungen, Bedeutungsfarben; ein Set ist mandantenweit aktiv). Festfarben je Unterlage/Position haben Vorrang und gelten wahlweise für Gantt, Kapazität oder beides; sie lassen sich auch direkt per Kontextmenü am Balken setzen. Paletten können als Textdatei exportiert und importiert werden; Farben sind innerhalb einer Grafik und über Grafiken hinweg eindeutig.
- **Benutzergruppen** für die Sichtbarkeit: Eine Kachel kann statt „Alle" oder einem Benutzer einer Gruppe gehören; Gruppen und Mitglieder werden auf einer eigenen Seite gepflegt, die Startseite zeigt nur Kacheln der eigenen Gruppen.
- **Vollständig zweisprachig** (Deutsch/Englisch) einschließlich Diagrammen, Kontextmenüs, Filter-Assistent, Konfiguration und PDF-Export; die Sprachwahl steht auf jeder Seite.
- **Schnell**: Die Kapazitätsauswertung zeichnet die erste Grafik nach rund 1,5–2 Sekunden; ein Verteilungslauf der Kapazitätsdaten ist bei großen Datenmengen nach etwa einer Minute fertig.
- **Angebotsstunden** gehen mit der Auftragswahrscheinlichkeit gewichtet in die Belastung ein (ein Angebot mit 20 % zählt 20 % seiner Stunden); verteilt werden nur offene Angebote.
- **Arbeitsplatzgruppen** werden als Gruppen erkannt und mit ihrer Bezeichnung benannt; Gruppencodes stehen nicht in der Arbeitsplatz-Auswahl.
- Eine bebilderte **Anwenderhilfe der Auswertungen** wird mit der Anwendung ausgeliefert.
- Der Bericht **„amsProject"** (Reporting Services) berücksichtigt den Projektfilter korrekt.

### Kapazitätsplanung & Ressourcenzuordnung

- **Web-Buchungsdialoge** für Iststunden, Fertigstellungsgrad und Fertigkennung. Die Buchungsliste hat eine eigene, filter- und sortierbare Spalte **Arbeitsplatzgruppe** und die Spalte **„FK"** mit dem ERP-Kürzel N / T / F (Langtext als Tooltip). Die **Fertigkennung** wird aus dem Fertigstellungsgrad vorgeschlagen, lässt sich aber auf Nicht begonnen / Teilfertig / Fertig überschreiben und wird mitgebucht; ein überschriebener Wert bleibt bei erneuter Auswahl erhalten. Aufgaben mit **reiner Gruppenzuordnung** sind buchbar: Sie erscheinen je Arbeitsplatz der Gruppe, die Gruppenkennung ist farbig hervorgehoben.
- **Mehrere Arbeitsplätze je Aufgabe**: Eine Aufgabe kann mehreren Arbeitsplätzen derselben Arbeitsplatzgruppe zugeordnet sein; jede Zuordnung hat ihre eigenen Stunden, und die Kapazitätsdaten im ERP enthalten je Zuordnung einen eigenen Satz. Arbeitsplätze anderer Gruppen sind sichtbar, aber nicht wählbar; ein Hinweis nennt die gebundene Gruppe. Der Zuordnungsdialog im Desktop-Client bietet nur Arbeitsplätze dieser Gruppe an. Eine erneute Zuweisung auf denselben Arbeitsplatz addiert die Stunden; ein Arbeitsplatzwechsel besteht aus „Zuordnung entfernen" und „neu zuordnen". Die Gantt-Spalte zeigt alle zugeordneten Arbeitsplätze.
- **Kapazitätsablösung nach Projektstruktur**: Plant eine Aufgabe oder Unteraufgabe Stunden auf einem Arbeitsplatz, lösen sie genau einen Gruppen-Satz derselben Gruppe im eigenen Ast des Projektstrukturplans ab – nicht den zeitlich frühesten Satz des Auftrags. Ein Terminverschub verschiebt damit keine Gruppenlast mehr zwischen Monaten. Die **Suchreihenfolge der Ablösung** ist je Projekt einstellbar („Position vor Nähe" = Standard, oder „Nähe vor Position"), im Desktop-Client über den gleichnamigen Menüeintrag neben „Projekt-Kalender".
- **Höchstens eine Ressource je Aufgabe (einstellbar)**: Der Administrator kann festlegen, dass eine Aufgabe nur einen Arbeitsplatz oder eine Arbeitsplatzgruppe haben darf. Dann bieten die Aufgaben-Details (auch im Desktop-Client) keine weitere Zuweisung an und erklären warum; Stunden ändern und Zuordnung entfernen bleiben möglich, und das System lehnt eine zweite Ressource auch auf anderen Wegen ab. Ab Werk ist die Einstellung ausgeschaltet.
- Zuweisungen mit **Gruppen-Ressourcen** beginnen am Starttag des Vorgangs.
- Die Karte **„Zuweisungen und Fortschritt"** im Desktop-Client nutzt die Web-Kapazitätsansicht in einem eigenen Fenster und zeigt Ressourcen-Kurzzeichen und geplantes Zeitfenster.

### Ist-Stunden

- **Ist-Stunden je Arbeitsplatz und je Auftrag**: Vorgänge, die einen Auftrag verlassen haben, zählen dort nicht mehr; doppelt gezählte Ist-Stunden werden bei der Neuverteilung bereinigt.
- **Im ERP ohne Vorgang gebuchte Ist-Stunden finden ihr Ziel**: zuerst der Vorgang, der genau diesen Arbeitsplatz plant; sonst der Gruppen-Satz der Arbeitsplatzgruppe; sonst das Budget der Gruppe. Jede Stufe sucht an der gebuchten Position, dann an der übergeordneten Position, zuletzt am Auftragskopf. Stunden wandern nie auf den Vorgang eines anderen Arbeitsplatzes; eine Buchung mit Vorgangsbezug bleibt auf ihrem Vorgang. Gruppenzeilen zeigen deshalb auch „Geleistet" aus solchen Buchungen.
- **Nicht verteilbare Buchungen** gehen nicht verloren (ohne Arbeitsplatz, mit unbekanntem Arbeitsplatz oder ohne passende Planung); sie stehen mit Grund im Ergebnis der Neuverteilung im Administrationsportal.
- **Ist-Stunden aufbereiten** im Administrationsportal (siehe dort); immer nur ein Lauf gleichzeitig, weitere Aufträge werden eingereiht und starten danach von selbst. Ein Auftrag steht nie zweimal in der Warteschlange.
- **Strukturänderungen halten die Ist-Stunden aktuell**: Wechselt ein Vorgang durch Verschieben in einen anderen Auftrag oder an eine andere Position, steht der Auftrag im Administrationsportal sofort als **„Offen"** mit dem Hinweis „Strukturänderung gemeldet" und wird nach **10 Minuten** Ruhe von selbst neu verteilt (eine Neuverteilung je Auftrag, auch nach mehreren Verschiebungen kurz hintereinander). Buchungen mit Vorgangsbezug bleiben für immer auf ihrem Vorgang; der Vorgang zeigt die Summe seiner Stunden aus allen Aufträgen.
- **Warnung vor einem Verschieben, das gebuchte Stunden zurücklässt**: Ziehen Sie im Gantt einen Vorgang unter einen Vorgang eines **anderen Auftrags**, zeigt ein Fenster die Stunden je Auftrag, die im ERP mit Vorgangsbezug gebucht sind und im alten Auftrag zurückbleiben, die betroffenen Vorgänge und den Korrekturweg (im ERP stornieren, neu buchen). **OK** verschiebt trotzdem, **Abbrechen** lässt den Vorgang stehen; ohne gebuchte Stunden erscheint keine Warnung.
- Die **Neuverteilung** belastet System und ERP-Datenbank deutlich weniger: Sie erzeugt nur noch einen Bruchteil der internen Nachrichten.

> 📝 **Hinweis für Administratoren**: Die automatische Neuverteilung nach einer Strukturänderung stellen Sie im Administrationsportal unter **Feature Flags** ein: Der Schalter `ActualHoursStructureChangeRedistribution` ist standardmäßig **eingeschaltet**; ausgeschaltet bleibt es bei der Markierung „Offen", und Sie starten die Neuverteilung von Hand. Der **Wert** des Schalters ist die Ruhezeit in Minuten (Standard 10, erlaubt 1 bis 1440); die Änderung wirkt ohne Neustart. Die Automatik gehört zum Funktionsschalter `ActualHoursRedistribution`: Ist er ausgeschaltet, startet keine Neuverteilung.

> 📝 **Hinweis für Administratoren**: Die Beschränkung auf eine Ressource je Aufgabe schalten Sie im Administrationsportal unter **Feature Flags** mit dem Schalter `SingleResourceAssignmentPerWorkItem` ein; er ist standardmäßig **ausgeschaltet**. Die Umschaltung wirkt im System nach spätestens **30 Sekunden** und in den Aufgaben-Details beim nächsten Laden der Seite bzw. beim Wechsel der Aufgabe; ein Neustart ist nicht nötig. Aufgaben, die bereits mehrere Ressourcen haben, bleiben unverändert: Ihre Stunden lassen sich korrigieren und Ressourcen entfernen, es kommt aber keine weitere hinzu. Prüfen Sie vor dem Einschalten selbst, ob es solche Aufgaben gibt.

## Zusammenarbeit

- **Diskussionen** je Aufgabe in Echtzeit.
- **Notizen (Memos)** je Aufgabe; beim Kopieren von Aufgaben optional mit übernehmbar.
- Die Diskussions- und Notiz-Bereiche nutzen das **einheitliche Design-System** und fügen sich in Look-and-Feel und Dark Mode nahtlos ein.

## Kopieren & Einfügen

- Vorgänge inkl. **Unteraufgaben** kopieren – wahlweise **nur die Unteraufgaben** ohne den Kopf-Vorgang.
- **Notizen** beim Kopieren optional mitkopieren.
- **Ressourcenzuordnungen** werden je Zuordnung mit ihren eigenen Stunden kopiert. Ist nur eine Ressource je Aufgabe erlaubt, fragt das Einfügen nach und übernimmt die erste Zuordnung je Vorgang.
- Der Dialog **„Aufgabe(n) aus Zwischenablage einfügen"** hat eine feste Größe, eine Zeile je Option und einen Meldungsbereich mit Warnungen, einem Hinweis zu „Nur Unteraufgaben einfügen" und einer Zusammenfassung, was eingefügt wird.
- Einträge ohne passenden Ziel-Vorgang werden übersprungen statt einen Fehler zu erzeugen.

## Oberfläche, Sprache & Bedienung

- **Durchgängige Sprache (Deutsch/Englisch)**: alle Module folgen der gewählten Sprache; **Sprachumschalter** im Desktop-Client (gespeichert, jederzeit änderbar). Ausnahme: einzelne Konfliktmeldungen der Aufgaben-Details (siehe Abschnitt „Bekannte Einschränkungen“).
- Modernisierte **Shell**: neue Titelleiste, Fluent-Farbpalette.
- **Dark Mode** (ab Werk ausgeschaltet, siehe Abschnitt „Bekannte Einschränkungen“): Ist er freigeschaltet, wirkt die Umschaltung auch in den eingebetteten Web-Modulen und im WPF-Menü und schaltet live um.
- Web-Module werden nahtlos im Desktop-Client gehostet.
- **Mehrprojekt-Zeitachse**: ein **Bearbeitungsmodus (F2)** verhindert versehentliches Verschieben von Terminen; Verschieben per Maus trifft die Zielposition exakt und folgt denselben Regeln wie das Ziehen im Gantt.
- Einheitliche **Wochenend-/Feiertags-Schattierung** in der Gantt.
- Neues, integriertes **Kundendoku-Portal** (Hilfe).
- **Abmelden und Benutzerwechsel** im Desktop-Client über das Benutzermenü unten in der Seitenleiste: Nach dem Abmelden wird eine erneute Anmeldung verlangt (einmalige Abfrage der Anmeldedaten), der Arbeitsbereich aktualisiert sich nach einem Benutzerwechsel von selbst.
- **`amsproject://`-Links** öffnen ams.project auch auf Arbeitsplätzen, die mit dem Client-Installer eingerichtet wurden, und Links mit Vorgangs-ID springen direkt auf den Vorgang.

## Administrationsportal (neu) *(für Administratoren)*

Neben der Anwendung selbst gibt es jetzt ein eigenes, web-basiertes **Administrationsportal** mit moderner Oberfläche (Seitenleiste, Dark Mode, Deutsch/Englisch, An-/Abmeldung mit Anzeige des angemeldeten Benutzers):

- **Zentrale Konfiguration**: systemweite Einstellungen werden zentral gepflegt und über einen **Freigabe-Schalter** aktiv geschaltet; Clients laden ihre Einstellungen beim Start automatisch vom Server – manuelle Verteilung von Konfigurationsdateien entfällt.
- **Benutzer-/Administratorrechte**: Administratoren werden im Portal verwaltet; der erste Anmeldende kann automatisch als Administrator freigeschaltet werden (inkl. Wiederherstellungs-Zugang nach Neuinstallation oder Übergabe). Das Administratorrecht bleibt auch erhalten, wenn ein Benutzer im Anmeldedienst gelöscht und neu eingeladen wird.
- **Funktionsschalter (Feature-Flags)** und **Änderungsprotokoll (Audit-Log)** direkt im Portal. Ein vorübergehender Lesefehler der Funktionsschalter schaltet keine Schalter aus; die letzten bekannten Werte bleiben gültig.
- **Wartungsmodus**: das System lässt sich – mit Ankündigung und Countdown-Schonfrist – in einen Wartungsmodus versetzen; angemeldete Clients werden rechtzeitig gewarnt.
- **Fehler-/Warteschlangen-Verwaltung**: fehlgeschlagene Hintergrund-Vorgänge werden übersichtlich angezeigt und lassen sich gezielt **erneut ausführen oder verwerfen**.
- **Revision für alle Projekte** (*Betrieb → Revisionen*): öffnet für jedes aktive, nicht archivierte Projekt eine neue Revision, mit Live-Fortschritt je Projekt.
- **Ist-Stunden** (*Betrieb → Ist-Stunden*): listet alle Kundenaufträge mit Aufbereitungsstand (durchsuchbar, blätterbar), bereitet einzelne oder „alle offenen" Aufträge neu auf, holt auf Wunsch fehlende Buchungen aus dem ERP nach und zeigt den Lauf live in einer Phasenleiste. Während ein Auftrag aufbereitet wird, stehen seine Ist-Stunden kurzzeitig auf 0.
- **Mandant.ini-Import**: bestehende Mandanteneinstellungen lassen sich per Dialog einlesen und übernehmen; eingegebene Pfade (auch UNC-Pfade) bleiben unverändert.
- **Client-Verteilung aus dem Portal**: der Desktop-Client kann direkt aus dem Portal an einen Netzwerkpfad verteilt werden – mit **Live-Fortschrittsanzeige**.
- **Abmelden** beendet die Anmeldung vollständig; danach wird eine erneute Anmeldung verlangt.

## Installation & Betrieb *(für Administratoren)*

- **Neuer Installationsmechanismus über ReadyStackGo (RSGO)**: Die gesamte Anwendung wird zentral und geführt ausgerollt – die einzelnen Dienste/Container werden anhand eines Manifests installiert und aktualisiert, ohne dass jeder Bestandteil manuell eingerichtet werden muss. Die Datenbank-Migrationen laufen dabei automatisch vor den Diensten und lassen sich gefahrlos wiederholen.
- **Ein RSGO für mehrere Zielserver**: dieselbe ReadyStackGo-Instanz kann mehrere Umgebungen/Zielserver bedienen (Remote-Umgebungen).
- **Geführter Client-Installer**: richtet den Desktop-Client beim Kunden ein (Versionsauswahl, Mandantennummer, Zielverzeichnis). Die passende Version wird **bedarfsgesteuert vom Server geladen**; Mandantennummer und Zielverzeichnis können zentral aus der Konfiguration vorbelegt werden, ein dauerhafter Hintergrunddienst ist nicht mehr nötig. Der ams.project-Client kann ein eigenes Installationsverzeichnis neben dem ERP haben (zentrale Einstellung `ams.Project.Install.Path`), die Konfiguration des Starters für `amsproject://`-Links wird automatisch gefüllt, und Installationsschritte, die Fehler ausgeben, werden als Fehler gemeldet.
- **Update-Anleitung** für Server (ReadyStackGo) und Client (Administrationsportal) auf Deutsch und Englisch, auch als Word- und PDF-Fassung.
- **Protokolle**: aussagekräftigere Start- und Verbindungsprotokolle des Clients in einem beschreibbaren Benutzerverzeichnis (Betrieb aus schreibgeschütztem Fileshare möglich); Neuberechnungen eines Terminplans über 0,5 Sekunden erscheinen als Warnung im Serverprotokoll.

## Stabilität, Verbesserungen & Fehlerbehebungen im Detail

Neben den großen Neuerungen sind in 4.0 zahlreiche Verfeinerungen und Fehlerbehebungen eingeflossen:

**Öffnen & Sitzung**
- Projekte lassen sich auch nach längerer Inaktivität zuverlässig öffnen – die Sitzung wird automatisch erneuert. Ist sie doch abgelaufen, erscheint statt einer technischen Fehlermeldung eine verständliche Seite **„Sitzung abgelaufen"** mit erneuter Anmeldung, ohne Weiterleitungs-Schleife.
- Terminpläne öffnen auch in verteilten Installationen zuverlässig.
- Benutzer mit Umlauten oder anderen Sonderzeichen im Namen (z. B. `Müller`) können sich anmelden und arbeiten.
- Benachrichtigungen und Live-Daten (Budget, Terminverletzungen, Etiketten) verbinden sich zuverlässig neu, auch wenn sich die Sitzung zwischenzeitlich erneuert.
- Ein Revisionswechsel führt nicht mehr dazu, dass ein Projekt endlos lädt und der Client mit Speichermangel abbricht.
- Nach dem Öffnen eines Projekts erscheint kein Absturzdialog mehr.

**Terminplanung**
- Datumsgebundene Meilensteine (frühestens/spätestens anfangen bzw. enden am) behalten an arbeitsfreien Tagen ihren Tag bzw. rücken korrekt auf den nächsten Arbeitstag.
- „So spät wie möglich" (ALAP) berücksichtigt den frühesten machbaren Starttermin und hält die Vorgänger-/Nachfolger-Reihenfolge ein.
- Sammelvorgänge erzeugen keine falschen „auf Arbeitstag verschoben"-Hinweise; ihre Dauer-Spalte zeigt die tatsächliche Dauer der Teilvorgänge, und ein Sammelvorgang schrumpft nicht auf einen Zeitpunkt, wenn eine Aufgabe unter ihn geschoben wird.
- Eine neue Aufgabe unter einem ERP-Auftrag mit Liefertermin in der Vergangenheit wird nicht vor den Projektstart gelegt.
- Ein geänderter Projekt-Starttermin wird auch übernommen, wenn er um Mitternacht über die Tagesgrenze fällt.
- Das erste Ziehen eines frisch angelegten Vorgangs verlängert ihn nicht.
- Vorgänge lassen sich in Projekten mit einer Gruppe „Anforderungen zur Anfrage" löschen; beim Löschen dieser Gruppe wird die Gruppe „Stücklisten" nicht mitgelöscht.
- Vorgänger-/Nachfolger-Beziehungen werden zuverlässig in der Übersicht angezeigt.

**ERP-Integration**
- Korrekte Beschriftung und Typ-Symbole auch für live angelegte ERP-Vorgänge; behobener Anzeige-Absturz bei bestimmten live angelegten ERP-Vorgängen.
- Kein Datumsversatz (−1 Tag) mehr bei ERP-Plan-Terminen.
- Geerbte Auftragsbezüge tragen keinen falschen ERP-Termin mehr.

**Auswertungen & Kapazität**
- Daten aktualisieren sich von selbst; Ladefehler werden angezeigt statt verschluckt; eine Anmelde-Schleife („HTTP 431") ist behoben, und eine durch eine hängende Sperre blockierte Verteilung läuft durch.
- Die Gantt-Auswertung öffnet nicht mit leerer Diagrammseite; die Zeitachsen-Auswertung zeigt ihre Termine auch bei „Bearbeitbar = Ja"; die Konfigurationstabelle zeigt ihre Zeilen zuverlässig.
- Die Kapazitätslinie läuft über einen Jahreswechsel nicht doppelt durchs Diagramm; Schrift, Filter-Platzhalter und Achsentitel erscheinen in der Schrift der Anwendung.
- Korrektes Datumsformat in den Export-Spalten; Überlast-Hinweise werden auch bei 0 Stunden korrekt angezeigt.
- Sechs Gantt-Spalten, die leer blieben, füllen sich.
- Die Startseite der Auswertungen übergibt die Benutzerkennung sicher als Parameter und protokolliert sie nicht im Klartext.

**Administrationsportal**
- Sprachumschaltung (DE/EN) greift zuverlässig; Symbole und Formulare werden korrekt dargestellt; Navigation im Dark Mode gut lesbar; Speichern der Konfiguration behoben.

**Oberfläche & Desktop**
- WPF-Menü folgt dem Dark Mode (neutrales Grau statt AMS-Blau).
- Das Windows-Taskleisten-Symbol wird angezeigt; die Startmeldung „Konfiguration wird geladen" ist lokalisiert; die Startkonfiguration wird bei einer Zeitüberschreitung automatisch erneut geladen.
- Die Druckansicht der Auswertungen öffnet im App-Fenster mit korrekter Projekt-Zuordnung.
- Formularfelder verlieren keine Werte, wenn sie erst durch eine Bedingung eingeblendet werden.
- Beim Verlassen eines Plans werden der aktive Plan und seine Menüs sauber geschlossen.
- Kopieren/Einfügen von Notizen: behobener Absturz beim Einfügen; wird eine Notiz geleert, verschwindet das Notiz-Symbol in der Tabelle.
- Die Stücklisten-Liste sortiert die Vorgangs-ID numerisch.
- Die Überschriften „Vorgänger"/„Nachfolger" erscheinen in der Akzentfarbe wie alle anderen Abschnittsüberschriften.

**Installation & Betrieb**
- Zahlreiche Robustheits-Verbesserungen am Client-Installer (bedarfsgesteuerter Download, Vorbelegung von Zielverzeichnis und Mandantennummer, korrekte Behandlung von UNC-Pfaden und Platzhaltern).
- Plattform-Dienst und Installer starten auch bei bestimmten Log-Level-Einstellungen stabil.
- Das Update der Datenbanken ist robuster: nachträglich hinzugefügte Pflichtfelder, wiederholtes Einspielen derselben Grunddaten, zu lange Bezeichnungen aus dem ERP, Datenbanken, deren Eigentümer das Laufzeit-Login ist, und stillgelegte Dienste-Endpunkte führen nicht mehr zum Abbruch.
- Die Kapazitätsdaten der Auswertungen werden auch auf älteren ERP-Ständen aufgebaut (dort fehlende ERP-Bausteine fallen aus der Auswertung heraus); für die Einlastung gibt es ein Diagnose-Skript. Die übrigen Anforderungen an den ERP-Stand nennt der Abschnitt „ERP-Voraussetzungen“.

## Geändert / Entfernt

- **Ziehen im Gantt**: Automatisch geplante Vorgänge bleiben beim Ziehen automatisch und erhalten „Anfang nicht früher als"; festgelegt (manuell) wird ein Vorgang nur noch mit der Alt-Taste bzw. bei manuellen Vorgängen, Meilensteinen, ALAP und Vorgängen mit anderer Termin-Einschränkung.
- **Arbeitsplatzwechsel**: besteht aus „Zuordnung entfernen" und „neu zuordnen"; eine erneute Zuweisung auf denselben Arbeitsplatz addiert die Stunden.
- **„Relevant für Kostenkalkulation"** steht für neue Vorgänge standardmäßig auf AN, auch bei reiner Gruppenzuweisung, und ändert sich nur durch ausdrückliches Umschalten.
- **Meilensteine**: Vorgänge mit Unteraufgaben können nicht mehr als Meilenstein markiert werden.
- **Kapazitätsauswertung**: Angebotsstunden werden mit der Auftragswahrscheinlichkeit gewichtet, nur offene Angebote werden verteilt; die Kapazitätsablösung zwischen Arbeitsplatzgruppe und Arbeitsplatz richtet sich nach der Projektstruktur statt nach dem Starttermin. Die angezeigte Belastung kann sich dadurch gegenüber 3.0 sichtbar ändern.
- Das Feld **„Typkurve"** in den Dialogen **„Ressource zuweisen"** und **„Sollwert ändern"** entfällt. Es wird im neuen Web-Workflow nicht mehr benötigt; der hinterlegte Typkurven-Wert eines Vorgangs bleibt erhalten und wird weiterhin verarbeitet. Die dafür nötigen Datenbankobjekte stellt das ERP bereit (siehe Abschnitt „ERP-Voraussetzungen“).
- **Entfernt**: die alte **Auswertungsart 6**, die separate **Kachel-Verwaltung** sowie veraltete Datenquellen im Hintergrund; Kachel-Daten liegen bei den übrigen Auswertungs-Konfigurationen.
- **Entfernt**: die bisher separat betriebene Import-Anwendung „Terminplan Import" – der Import läuft nur noch über die integrierte Seite und mit Anmeldung.
- **Entfernt**: direkte Schnittstellen zum Neuverteilen der Ist-Stunden; Neuverteilungen laufen über das Administrationsportal bzw. den Dialog „Stunden neu verteilen".

---

## Upgrade auf 4.0 — Hinweise für Administratoren

4.0.0 wird über **ReadyStackGo** aus dem **Stable-Kanal** installiert (Manifest-Repository `https://github.com/Wiesenwischer/rsgo-ams-project-stable`, enthält **ams.project 4.0.0** und **IdentityAccess 3.4.1**). Die Quelle ist in ReadyStackGo noch nicht vorkonfiguriert; Sie fügen sie unter **Settings → Stack Sources → Add → Git Repository** hinzu. Die Schritte im Einzelnen beschreiben die Installationsanleitung und die Update-Anleitung.

### ERP-Voraussetzungen

Prüfen Sie vor dem Update, dass Ihr **ams.erp-Stand die folgenden Datenbankobjekte selbst bereitstellt** – ams.project legt sie ab 4.0.0 nicht mehr an. Bitte stimmen Sie den nötigen ams.erp-Stand vor dem Update mit dem ams.erp-Support ab.

- die 19 Kapazitäts-Sichten `SPAV12…`, `Typkurvenverteilung` und `amsKapaData`,
- `amsKapaDataMenu` (mit den Spalten `RECORDID`, `BEZEICH80`, `LINK`), `amsKapaDashboardView`, `tvf_GetKapaDashboardParameter` sowie `ContextArbPlatz` / `ContextArbPlatzData`,
- in der Tabelle `prmd100` die Spalten `ZEIT_SOLL`, `ZEIT_IST`, `ZEIT_AZE` und `FK_APL` (fehlen sie, werden einige Kapazitäts-Sichten beim Update übersprungen).

Bestehende Datenbanken, in denen diese Objekte bereits vorhanden sind, behalten sie. Fehlen sie, scheitern u. a. die Kapazitätsauswertungen.

**Änderungen an der ERP-Datenbank beim ersten Update** (automatisch durch die Migration):

- Die Tabelle `RESD000` erhält den Primärschlüssel auf `RECORDID` (bisher `TASKID`); doppelte `RECORDID`-Werte werden dabei nach einer festen Regel bereinigt, ist Change Tracking für die Tabelle eingeschaltet, wird es dabei kurz aus- und wieder eingeschaltet. Der erste Aktualisierungslauf der Auswertungen danach ist ein vollständiger Lauf. Künftig gibt es in `RESD000` einen Satz je Zuordnung statt je Vorgang.
- Für die Aktualisierung der Auswertungen schaltet die Migration **Change Tracking** auf der Datenbank ein (Aufbewahrung 7 Tage) und auf den Tabellen `SPAD050`, `RESD000`, `PRMD030`, `PRMD100`, `ANGD430`, `TMSD800` und `ANGD300`; jede dieser Tabellen braucht dafür einen Primärschlüssel. Dazu kommen ein datenbankweiter DDL-Trigger (`capa_tgrDDLCTHeilung`), der nach dem Neuanlegen einer dieser Tabellen einen vollständigen Aktualisierungslauf auslöst, und **SQL-Agent-Jobs** für die laufende Aktualisierung. Das Admin-Login (`DB_ADMIN_USER`, `sysadmin`) legt sie an; ohne SQL-Agent werden die Jobs übersprungen. Ändert ein späteres ams.erp-Update den Primärschlüssel einer dieser Tabellen, stimmen Sie das mit dem ams.erp-Support ab.
- Die Trigger auf der Buchungstabelle `PRMD060` melden Ist-Buchungen an die ERP-Integration von ams.project.
- Bei jedem Öffnen einer neuen Revision schreibt ams.project Geschäftsbereich, Projektleiter und Sachbearbeiter in den ERP-Projektkopf (`APJD000`) zurück; ein leerer Wert in ams.project leert den ERP-Wert.

### Pflichtschritte in dieser Reihenfolge

1. **Stable-Quelle in ReadyStackGo hinzufügen** und synchronisieren (*Settings → Stack Sources → Add → Git Repository*, URL `https://github.com/Wiesenwischer/rsgo-ams-project-stable`).
2. **IdentityAccess 3.4.1 installieren bzw. aktualisieren.** 3.4.1 registriert die Schnittstelle des Terminplan-Imports beim Anmeldedienst; ohne sie ist der Import nicht nutzbar.
3. **ams.project 4.0.0 installieren bzw. aktualisieren.** Die Datenbank-Migratoren laufen automatisch vor den Diensten (siehe Abschnitt „ERP-Voraussetzungen“).
4. **Revision für alle Projekte** ausführen: **Administrationsportal → Betrieb → Revisionen → „Für alle Projekte starten"** (**Pflicht**). Erst danach stehen Projektleiter und Sachbearbeiter in allen Aufgaben, und die Kapazitätsablösung nach Projektstruktur wirkt – bis dahin gilt je Projekt die bisherige Regel nach Starttermin. Inaktive und archivierte Projekte sind nicht Teil des Laufs. **Nebenwirkung:** Jede geöffnete Revision schreibt Geschäftsbereich, Projektleiter und Sachbearbeiter in den ERP-Projektkopf zurück.
5. **Ist-Stunden aufbereiten**: Das Update setzt bereits aufbereitete Aufträge einmalig auf „Offen". Der automatische Lauf über alle Aufträge ist ab Werk **ausgeschaltet** – starten Sie im Administrationsportal unter **Betrieb → Ist-Stunden** „**Alle offenen abarbeiten**" oder schalten Sie den automatischen Lauf über die Stack-Variable `CAPACITYPLANNING_ACTUALHOURS_AUTOMATIC_REDISTRIBUTION` ein (siehe unten). Erst danach rechnen Bestandsaufträge nach den neuen Regeln (ein Kapazitätssatz je Zuordnung, Zielsuche für Buchungen ohne Vorgang, Summe je Auftrag). Die Option „Buchungen aus dem ERP neu einspielen" ist standardmäßig aus und verlängert die Laufzeit deutlich.
6. **Bericht „amsProject" (Reporting Services)**: Wer den Bericht nutzt, stellt die aktuelle Berichtsdatei im Report-Server neu bereit – bitte mit dem ams-Support abstimmen.
7. **Desktop-Client verteilen** (Administrationsportal → *Bereitstellung* → *Client-Download*). Ziehen im Gantt, die Warnung vor dem Verschieben gebuchter Stunden, das Kopieren je Zuordnung, die Gantt-Spalte mit allen Arbeitsplätzen und der neue Einfügedialog brauchen den neuen Client.

### Umstieg von 3.0.x

Läuft ams.project 3.0.x noch mit docker-compose, erfolgt der Umstieg in einem Deployment von 4.0.0 gegen die bestehenden Datenbanken, begleitet durch den ams-Support. Die Schritte beschreibt der Abschnitt „Umstieg von 3.0.x auf 4.0" der Update-Anleitung. Zusätzlich zu den Pflichtschritten oben gilt:

- **Ist-Stunden mit ERP-Nachholung**: „Alle offenen abarbeiten" mit eingeschalteter Option „Buchungen aus dem ERP neu einspielen" starten. Nur so stehen auch die Buchungen, die 3.0 noch nicht verarbeitet hatte, und die Buchungen aus dem Update-Zeitraum im Ist-Stunden-Hauptbuch. Der Lauf dauert damit deutlich länger.
- **Wartungsfenster für das ERP**: Die Migratoren ändern beim Umstieg Trigger, Schlüssel und Change Tracking von ERP-Tabellen und sperren diese dabei kurz. Planen Sie das Update in einem Wartungsfenster, in dem niemand im ERP speichert.
- **Projektkalender**: Projekte behalten den ERP-Kalender, mit dem sie geplant sind. Projekte ohne eigenen Kalender bekommen den ERP-Kalender `STANDARD`; gibt es ihn im ERP nicht, den Kalender „Demo“.
- **Neue EventStore-Volumes**: ReadyStackGo legt für den EventStore neue Volumes an; die EventStore-Daten der 3.0-Installation werden nicht übernommen. Die Revision für alle Projekte füllt den EventStore neu.

### Ab Werk ausgeschaltete Funktionen und Schalter

| Schalter | Ab Werk | Wirkung | Wo |
|---|---|---|---|
| `SingleResourceAssignmentPerWorkItem` | **Aus** | Höchstens eine Ressource je Aufgabe. Vor dem Einschalten den Bestand an Aufgaben mit mehreren Ressourcen prüfen. | Administrationsportal → Feature Flags |
| `ActualHoursStructureChangeRedistribution` | **An** (Wert 10) | Neuverteilung eines Auftrags nach einer Strukturänderung; Wert = Ruhezeit in Minuten (1–1440). | Administrationsportal → Feature Flags |
| Automatischer Lauf nach dem Update | **Aus** | Einmalige Neuverteilung der Ist-Stunden aller Aufträge nach einem Update (nur bei eingeschaltetem `ActualHoursRedistribution`). Erst einschalten, wenn ein Lauf über die Aufträge Ihrer Umgebung die erwartete Dauer hält; danach den Kapazitäts-Dienst neu starten. | Stack-Variable `CAPACITYPLANNING_ACTUALHOURS_AUTOMATIC_REDISTRIBUTION` |
| Selbstheilung der Ist-Stunden | **Aus** | Eine Buchung für einen nie aufbereiteten Auftrag löst eine vollständige Neuverteilung aus. | Stack-Variable `CAPACITYPLANNING_ACTUALHOURS_SELFHEALING` |
| ERP-Nachlauf bei der Aufbereitung | **Aus** (je Lauf) | Spielt fehlende Buchungen aus dem ERP nach. | Option im Portal bzw. im Dialog „Stunden neu verteilen" |
| `EnablePreviewFeatures` | **Aus** | Blendet im Desktop-Client die Umschaltung auf den noch unvollständigen Dark Mode ein; wirkt beim nächsten Start des Clients. | Administrationsportal → Feature Flags |

### Neue Stack-Variablen (ReadyStackGo)

| Variable | Standard | Bedeutung |
|---|---|---|
| `CAPACITYPLANNING_ACTUALHOURS_AUTOMATIC_REDISTRIBUTION` („Automatic redistribution of actual hours after an update") | `false` | siehe Tabelle oben; ein leerer Wert gilt als aus |
| `CAPACITYPLANNING_ACTUALHOURS_SELFHEALING` („Self-healing redistribution of actual hours") | `false` | siehe Tabelle oben |
| `ANALYTICS_DEMO_DATA_ENABLED`, `ANALYTICS_DEMO_DB_USER`, `ANALYTICS_DEMO_DB_PASSWORD`, `DB_DEMO_USER`, `DB_DEMO_PASSWORD` | leer / `false` | nur für Testsysteme (Demo-Daten der Auswertungen); auf Produktivsystemen **leer lassen** |

### Neue Dienste im Stack

- **Terminplan-Import** (Web-Oberfläche unter `/projectimport`).
- Ein Hintergrunddienst, der einen kommenden Web-Terminplan vorbereitet. Er hat noch keine Oberfläche, baut nach jedem Start sein Lesemodell für alle Projekte gedrosselt im Hintergrund auf und zeigt den Fortschritt in seinem Gesundheitsstatus.

### Auswirkungen auf Bestandsdaten

- Projektleiter und Sachbearbeiter werden aus den vorhandenen Aufgaben je Projekt nachgetragen; Projekte ganz ohne Wert erhalten sie mit der Revision für alle Projekte.
- „Verteilt" und „Reststunden" der Aufgaben-Details werden beim Update nachgerechnet; Hinweise zu Strukturänderung und Herkunft gebuchter Stunden erscheinen nach der nächsten Berechnung des Arbeitsplans.
- Die Auftrags-/Positionsschlüssel für den Block „Aus Unteraufgaben" werden nach dem Update einmalig im Hintergrund nachgetragen.
- Bestehende Vorgänge behalten ihren gespeicherten Kostenschalter.
- Sammelvorgänge, die durch früheres Ziehen manuell geworden sind, bleiben manuell; im Detailbereich lassen sie sich auf „Automatisch" stellen.
- **Auswertungsfilter aus 3.0**: Gespeicherte Auswertungen mit einem Filter als SQL-Text verlieren diesen Filter, denn 4.0 übernimmt nur Filter aus dem Filter-Assistenten. Die Auswertung bleibt erhalten und zeigt alle Projekte, bis der Filter im Assistenten neu angelegt ist.
- **Projektrollen und -rechte aus 3.0** werden beim Update entfernt. ams.project 4.0 hat derzeit keine Rechteverwaltung für Projekte; Administratoren werden im Administrationsportal verwaltet.
- Kunden-Auswertungen zeigen nach dem Update andere Belastungswerte (gewichtete Angebotsstunden, Ablösung nach Projektstruktur, Ist-Stunden nach Zielsuche).

---

## Bekannte Einschränkungen

- **Dark Mode**: noch nicht vollständig – u. a. das Gantt-Diagramm und einzelne WPF-Dialoge folgen dem dunklen Design noch nicht. Er ist deshalb ab Werk ausgeschaltet; die Umschaltung im Desktop-Client erscheint erst, wenn der Funktionsschalter `EnablePreviewFeatures` eingeschaltet ist.
- **Konfliktmeldungen**: Die Überschriften und Texte der Konfliktliste in den Aufgaben-Details erscheinen auch in der englischen Oberfläche auf Deutsch.
- **Reststunden in den Auswertungen**: Die Auswertungen können bei den Reststunden noch von den Aufgaben-Details abweichen; Aufgaben-Details und Terminplan rechnen bereits nach der neuen Formel (Plan − Verteilt − Geleistet).
- **Budget-Ist-Stunden**: Auf ein Budget verteilte Ist-Stunden erscheinen in den Aufgaben-Details noch nicht beim Budget.
- **Feiertage aus Feiertagskalendern**: Feiertage übernimmt 4.0 aus den Ausnahmetagen der ERP-Kalender. Regelbasierte Feiertage aus einem Feiertagskalender des ERP werden nicht übernommen und bei der Terminplanung nicht berücksichtigt.
- **Stücklisten-Gantt**: Das Ziehen im Stücklisten-Gantt berücksichtigt den Kalender noch nicht.
- **Zuordnungsdialog im Desktop-Client**: Er zeigt keine Liste aller Zuordnungen einer Aufgabe; diese steht in den Aufgaben-Details.
- **Terminplan-Import**: Es gibt noch keinen Menüeintrag im Desktop-Client und keinen Link in der Projektliste; die Seite wird über ihre Adresse geöffnet.
