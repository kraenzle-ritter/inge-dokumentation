# INGE Commands & Troubleshooting

## Übersicht

| Befehl | Beschreibung |
|--------|--------------|
| `inge:sync` | INGE-Datenbank mit DIMAG synchronisieren |
| `inge:add-archive` | Neues Archiv anlegen oder aktualisieren |
| `inge:add-user` | Neuen Benutzer anlegen oder aktualisieren |
| `inge:set-access-token` | API-Token für Benutzer erstellen |
| `inge:status` | Status-Abfrage durchführen |
| `loadxml:test-file` | LoadXML-Testdatei für Archiv erstellen |

---

## inge:sync

Synchronisiert die INGE-Datenbank mit DIMAG. Kann einzelne AIDs oder alle AIDs eines Archivs synchronisieren.

### Verwendung

```bash
# Hilfe anzeigen
php artisan inge:sync --archive=opfikon

# Einzelne AID synchronisieren (ruft finalize() auf)
php artisan inge:sync --archive=opfikon --aid=opfikon-media-445

# AID manuell auf Status setzen (ohne DIMAG-Abfrage)
php artisan inge:sync --archive=opfikon --aid=opfikon-media-445 --status=20

# Alle AIDs mit DIMAG synchronisieren
php artisan inge:sync --archive=opfikon --all

# Fehlende AIDs aus DIMAG importieren
php artisan inge:sync --archive=opfikon --import-missing

# Vorschau ohne Änderungen
php artisan inge:sync --archive=opfikon --all --dry-run

# Verbose Output für Debugging
php artisan inge:sync --archive=opfikon --aid=opfikon-media-445 -vv
```

### Optionen

| Option | Beschreibung |
|--------|--------------|
| `--archive=<slug>` | **Pflicht.** Archiv-Slug (z.B. opfikon) |
| `--aid=<aid>` | Einzelne AID synchronisieren |
| `--status=<code>` | Status manuell setzen (mit --aid) |
| `--all` | Alle AIDs des Archivs synchronisieren |
| `--import-missing` | AIDs aus DIMAG importieren die nicht in INGE sind |
| `--since=<Y-m-d>` | Für --import-missing: nur AIDs seit Datum |
| `--dry-run` | Zeigt was passieren würde ohne Änderungen |
| `-v` / `-vv` | Verbose / Very Verbose Output |

### Was passiert bei --aid?

1. Prüft ob AID in DIMAG existiert (`exportDoc`)
2. Ruft `finalize($aid)` auf → setzt Status auf 20 in DIMAG
3. Speichert in `files`-Tabelle
4. Erstellt `imports`-Eintrag (Audit-Log)

### Status-Codes

| Code | Bedeutung |
|------|-----------|
| 10 | In Bearbeitung |
| 20 | Abgeschlossen |
| 30 | Zu löschen |
| 40 | Primärdaten gelöscht |
| 50 | Vollständig gelöscht |

---

## inge:add-archive

Erstellt ein neues Archiv oder aktualisiert ein bestehendes.

### Verwendung

```bash
php artisan inge:add-archive \
  --slug=opfikon \
  --name="Gemeindearchiv Opfikon" \
  --ais_url="https://opfikon.anton.ji.ktzh.ch" \
  --soap_url="https://dimaggem.ji.ktzh.ch/soap/webservice_3_5_0.php" \
  --soap_username="username" \
  --soap_password="password" \
  --sftp_address="dimaggem.ji.ktzh.ch" \
  --sftp_port=22 \
  --sftp_username="sftp_user" \
  --sftp_password="sftp_pass" \
  --sftp_path="/Import" \
  --import_successful_url="https://opfikon.anton.ji.ktzh.ch/api/ingest/successful/[aid]" \
  --import_failed_url="https://opfikon.anton.ji.ktzh.ch/api/ingest/failed/[aid]" \
  --parent_aid="opfikon" \
  --sip_url="https://opfikon.anton.ji.ktzh.ch/api/sip" \
  --api_token="your-api-token"
```

---

## inge:add-user

Erstellt einen neuen Benutzer oder aktualisiert einen bestehenden (identifiziert via E-Mail).

### Verwendung

```bash
php artisan inge:add-user \
  --name="Max Mustermann" \
  --email="max@example.com" \
  --password="sicheresPasswort123" \
  --role=admin \
  --archive-slug=opfikon
```

### Optionen

| Option | Beschreibung |
|--------|--------------|
| `--name` | Name des Benutzers |
| `--email` | **Pflicht.** E-Mail (dient als Identifier) |
| `--password` | **Pflicht.** Passwort (min. 8 Zeichen) |
| `--role` | Rolle: `superadmin`, `admin`, `user` |
| `--archive-slug` | Archiv-Zuordnung |

---

## inge:set-access-token

Erstellt einen neuen API-Token für einen Benutzer (für Sanctum-Authentifizierung).

### Verwendung

```bash
php artisan inge:set-access-token --email=max@example.com

# Mit Token-Name
php artisan inge:set-access-token --email=max@example.com --token-name="AIS Integration"
```

---

## inge:status

Führt eine Status-Abfrage für einen Benutzer durch.

### Verwendung

```bash
php artisan inge:status --user-id=1
```

---

## loadxml:test-file

Erstellt eine LoadXML-Testdatei für ein Archiv.

### Verwendung

```bash
php artisan loadxml:test-file --archive=opfikon
```
