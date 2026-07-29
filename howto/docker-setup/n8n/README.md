# n8n Docker Setup

Dieses Repository enthält eine `docker-compose.yml`, um [n8n](https://n8n.io/) (Workflow-Automatisierungstool) lokal per Docker zu betreiben.

## Voraussetzungen

- [Docker](https://docs.docker.com/get-docker/) und [Docker Compose](https://docs.docker.com/compose/install/) müssen installiert sein
- Ein Linux-Host wird empfohlen, da `network_mode: host` verwendet wird (auf macOS/Windows funktioniert dieser Modus nicht zuverlässig)

## Setup

1. **Repository/Dateien herunterladen** und in ein beliebiges Verzeichnis legen.

2. **`docker-compose.yml` anpassen:**

   Folgende Werte müssen vor dem ersten Start angepasst werden:

   | Variable/Pfad | Beschreibung |
   |---|---|
   | `N8N_USER_MANAGEMENT_MAIN_USER_PASSWORD` | Passwort für den n8n-Hauptbenutzer |
   | `N8N_USER_MANAGEMENT_MAIN_USER_EMAIL` | E-Mail-Adresse für den n8n-Hauptbenutzer |
   | `/path/to/your/output` (Volume) | Lokaler Pfad, der im Container unter `/output` bereitgestellt wird (z. B. für Dateiexporte aus Workflows) |

   > ⚠️ **Wichtig:** Benutzername und Passwort werden nur beim **allerersten Start** aus den Umgebungsvariablen übernommen. Spätere Änderungen an diesen Variablen haben keine Wirkung mehr, sobald der Benutzer einmal angelegt wurde.

3. **Container starten:**

   ```bash
   docker compose up -d
   ```

4. **n8n aufrufen:**

   Im Browser öffnen: [http://localhost:5678](http://localhost:5678)

   Beim ersten Aufruf mit der oben konfigurierten E-Mail und dem Passwort anmelden.

## Konfigurationsdetails

- **`network_mode: host`**: Der Container nutzt das Host-Netzwerk direkt, wodurch n8n unter `localhost:5678` erreichbar ist. Dies ist z. B. hilfreich, wenn n8n auf andere Dienste zugreifen soll, die ebenfalls lokal auf dem Host laufen.
- **`N8N_RESTRICT_FILE_ACCESS_TO=/output`**: Beschränkt den Dateizugriff von n8n-Workflows (z. B. Read/Write File Nodes) auf das Verzeichnis `/output` im Container.
- **Volumes:**
  - `~/.n8n:/home/node/.n8n` – speichert alle n8n-Daten (Workflows, Credentials, Datenbank) dauerhaft auf dem Host.
  - `<dein-output-pfad>:/output` – Verzeichnis für Dateien, mit denen Workflows arbeiten sollen.
- **`extra_hosts`**: Ermöglicht dem Container, über `host.docker.internal` auf den Host zuzugreifen (nützlich z. B. für lokal laufende APIs oder Datenbanken).

## Container stoppen

```bash
docker compose down
```

Die Daten in `~/.n8n` bleiben dabei erhalten, solange das Volume nicht gelöscht wird.

## Daten zurücksetzen

Falls ein kompletter Neustart (inkl. neuem Hauptbenutzer) gewünscht ist, das lokale `~/.n8n`-Verzeichnis löschen bzw. sichern und neu anlegen lassen:

```bash
rm -rf ~/.n8n
docker compose up -d
```

⚠️ Dadurch gehen alle gespeicherten Workflows und Credentials verloren, falls kein Backup vorhanden ist.
