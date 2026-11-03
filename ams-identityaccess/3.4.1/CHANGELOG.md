# Changelog – ams.identityaccess

Alle nennenswerten Änderungen an diesem Produkt werden hier dokumentiert.
Das Format orientiert sich an [Keep a Changelog](https://keepachangelog.com/de/1.1.0/),
die Versionierung folgt [Semantic Versioning](https://semver.org/lang/de/).

ams.identityaccess ist der eigenständig ausgelieferte Identity Provider für die
ams-Produkte (IdentityServer, AdminPortal, ClientHub, EmailSender).

## [3.4.1] – 2026-10-23

### Behoben
- **Die NServiceBus-Lizenz ist erneuert.** Die bisher ausgelieferte Lizenz war eine
  unbefristete Lizenz, deren Upgrade-Schutz bereits 2022 ausgelaufen war. Alle Dienste
  enthalten jetzt die erneuerte Subscription-Lizenz, gültig bis 2027-10-01.
  **Wirkung:** Die Dienste starten mit einer gültigen Lizenz; es ist keine Aktion nötig.

## [3.4.0] – 2026-10-23

### Hinzugefügt
- **Die ProjectImport-API ist im Identity Provider registriert.** Die Web-BFF leitet
  `/api/projectimport/*` an die ProjectImport-API weiter und holt dafür per Token-Exchange
  (RFC 8693) ein Token mit der Zielgruppe `api://projectimport`. Ohne passende
  API-Ressource und passenden Scope schlug dieser Austausch fehl, und die BFF beantwortete
  jede Anfrage mit einem leeren 502. Neu sind die API-Ressource `api://projectimport`, der
  Scope `api.projectimport` und dessen Freigabe für den Client der Web-BFF.
  Der Migrator trägt die Einträge bei jedem Lauf nach.
  **Wirkung:** Installationen und Updates mit diesem Stand brauchen kein manuelles
  SQL-Skript mehr, damit der Projektimport über die BFF erreichbar ist.

## [3.3.3] – 2026-09-03

### Behoben
- **Die Installation bricht nicht mehr ab, wenn das Datenbank-Login der Eigentümer der
  Datenbank ist.** Der Migrator prüfte vor dem Anlegen des Runtime-Benutzers nur, ob
  bereits ein Benutzer *mit diesem Namen* existiert. Gehört die Datenbank dem Login, ist
  es dort aber schon als `dbo` eingetragen — unter seinem eigenen Namen war also nichts zu
  finden, und SQL Server lehnte die Anlage mit
  `The login already has an account with the user name 'dbo'` (Fehler 15063) ab. Die
  Installation schlug damit reproduzierbar fehl.
  Die bestehende Zuordnung wird jetzt anhand der Login-Kennung (SID) erkannt statt anhand
  des Namens: Ist das Login als `dbo` eingetragen, werden Benutzeranlage, Rolle und
  Berechtigungen übersprungen — als Eigentümer hat es ohnehin Vollzugriff. Ist es unter
  einem anderen Benutzernamen eingetragen, wird dieser verwendet.
  **Wirkung:** Installationen und Updates auf Datenbanken, die dem Anwendungs-Login
  gehören, laufen durch.

## [3.3.2] – 2026-07-17

### Behoben
- **Der Sitzungsdauer-Fix greift jetzt auch beim normalen Login.** Die in 3.3.1
  eingeführte `refresh_expires_in`-Ausgabe war daran gebunden, dass `offline_access`
  in den angefragten Scopes auftaucht. IdentityServer4 stellt dieses Scope dort aber
  nicht bereit, sodass der **Authorization-Code-Login** den Hinweis nie erhielt und
  die BFF-Sitzung weiterhin nach ~1 Stunde ablief. `refresh_expires_in` wird jetzt für
  den **Authorization-Code-** und den **Refresh-Token-Grant** ausgegeben, abgesichert
  über die Client-Einstellung `AllowOfflineAccess`.
  **Wirkung:** Angemeldete Nutzer bleiben so lange eingeloggt, wie ihr Refresh Token
  gültig ist (Tage statt ~1 Stunde) — auch über den regulären Anmeldeweg.

## [3.3.1] – 2026-07-16

### Behoben
- **Die BFF-Sitzung überlebt jetzt die Lebensdauer des Zugriffstokens.** IdentityServer4
  meldet — anders als Keycloak — die Refresh-Token-Lebensdauer nicht im Token-Response.
  Die Web-BFF nutzt diesen Wert, um die TTL ihres serverseitigen Token-Vaults zu
  dimensionieren; ohne ihn fiel die TTL auf die kurze Access-Token-Lebensdauer zurück,
  sodass die Sitzung ~1 Stunde nach dem Login endete, obwohl das Refresh Token noch
  tagelang gültig war. Ein neuer `ICustomTokenRequestValidator` ergänzt ein
  Keycloak-kompatibles `refresh_expires_in` (aus der konfigurierten
  Refresh-Token-Lebensdauer des Clients), sobald ein Refresh Token ausgestellt wird.
  Token-Exchange-Responses bleiben unverändert.

## [3.3.0] – 2026-07-02

### Geändert
- **IdentityAccess läuft jetzt standardmäßig „self-contained" in einer einzigen
  Datenbank.** Die separate NServiceBus-Transport-Datenbank ist **optional** geworden.
  Neue Variable **`DB_TRANSPORT_NAME` (optional)**:
  - **leer lassen** → IdentityAccess nutzt seine Persistenz-Datenbank (`DB_NAME`) auch
    für Queues und Subscriptions (empfohlen für den eigenständigen Betrieb);
  - **gesetzt** → nur die gemeinsame Tabelle `dbo.SubscriptionRouting` liegt im
    Transport-Katalog — für kontextübergreifendes Pub/Sub (z. B. gemeinsam mit dem
    ams.project-Stack).
- **Einheitliche Subscription-Routing-Platzierung.** Migrator und Laufzeit adressieren
  `dbo.SubscriptionRouting` identisch; die Tabelle wird **idempotent** im richtigen
  Katalog angelegt — von IdentityAccess oder dem Platform-Stack, je nachdem, was zuerst
  läuft. Keine verwaisten Duplikat-Tabellen mehr.
- **`SERVICE_BUS_CATALOG` entfällt.** Der Subscription-Katalog wird jetzt aus
  `DB_TRANSPORT_NAME` abgeleitet (eine einzige Quelle der Wahrheit).

### Behoben
- **Kontextübergreifendes Pub/Sub im rc-Kanal repariert.** Die Host-Dienste lasen den
  Katalog aus `SERVICE_BUS_CATALOG`, der rc-Deploy lieferte aber `DB_TRANSPORT_NAME` —
  dadurch war der Katalog zur Laufzeit leer und `SubscriptionRouting` fiel je Dienst auf
  die eigene Datenbank zurück, was Pub/Sub still brach.
- **Fehlende Berechtigung ergänzt:** Der Laufzeit-Benutzer erhält jetzt zuverlässig
  `dbo.error` auf der Persistenz-Datenbank (zuvor eine latente Lücke).
- Entfernte tote/duplizierte Transport-Queue-Tabellen sowie die verwaiste Standard-Tabelle
  `IdentityAccess.SubscriptionRouting`.

### Hinweis für Betreiber
- Bestehende eigenständige Deployments bleiben unverändert (self-contained). Wer bisher
  `SERVICE_BUS_CATALOG` gesetzt hatte, stellt auf `DB_TRANSPORT_NAME` um.

## [3.2.0] – 2026-05-11

- Basisversion des als eigenständiges RSGO-Produkt ausgelieferten IdentityAccess
  (IdentityServer, AdminPortal, ClientHub, EmailSender). Frühere Historie ist in
  diesem Changelog nicht erfasst.
