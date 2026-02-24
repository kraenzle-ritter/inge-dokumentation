# Troubleshooting

## SOAP-Fehler

### Error Fetching http headers

**Symptom:**
```
SoapFault exception: [HTTP] Error Fetching http headers
```

**Ursachen:**
1. **Timeout** - DIMAG braucht zu lange für die Verarbeitung (große Dateien)
2. **Server nicht erreichbar** - DIMAG-Server down oder Netzwerkproblem
3. **Firewall** - Verbindung wird blockiert

**Lösung:**

1. Socket-Timeout in `.env` erhöhen:
```env
SOAP_SOCKET_TIMEOUT=900  # 15 Minuten für große Dateien
```

2. Nach Änderung Cache leeren:
```bash
php artisan config:cache
```

3. PHP-Limits prüfen:
```bash
php -i | grep -E "max_execution_time|memory_limit|default_socket_timeout"
```

### SOAP-Debugging

Für detaillierte SOAP-Debug-Informationen:

```bash
php artisan inge:sync --archive=opfikon --aid=opfikon-media-445 -vv
```

Zeigt:
- Dauer der SOAP-Anfrage
- Fault Code und String
- Letzten SOAP Request (XML)
- Letzte SOAP Response
- Raw Response von DIMAG

Logs findest du in:
```
storage/logs/laravel.log
```

---

## Ingest-Fehler

### AID existiert in DIMAG aber nicht in INGE

```bash
php artisan inge:sync --archive=opfikon --aid=opfikon-media-445
```

Dies ruft `finalize()` auf und erstellt den Eintrag in INGE.

### Status in INGE ist falsch

```bash
# Status manuell setzen
php artisan inge:sync --archive=opfikon --aid=opfikon-media-445 --status=20
```

### Mehrere AIDs korrigieren

```bash
# Alle AIDs mit DIMAG abgleichen (Vorsicht: kann lange dauern)
php artisan inge:sync --archive=opfikon --all

# Vorschau zuerst
php artisan inge:sync --archive=opfikon --all --dry-run
```

---

## Datenbank

### File-Status prüfen

```bash
php artisan tinker
```

```php
use App\Models\File;
File::where('aid', 'opfikon-media-445')->first();
```

### Import-History prüfen

```php
use App\Models\Import;
Import::where('file_id', 123)->orderBy('created_at', 'desc')->get();
```

---

## Konfiguration

### Wichtige Umgebungsvariablen

| Variable | Beschreibung | Default |
|----------|--------------|---------|
| `SOAP_SOCKET_TIMEOUT` | SOAP-Timeout in Sekunden | 300 |
| `SOAP_IGNORE_CERTIFICATE` | SSL-Zertifikat ignorieren | false |
| `LOG_CHANNEL` | Log-Kanal | stack |

### Config Cache leeren

Nach Änderungen an `.env` oder `config/`:

```bash
php artisan config:cache
php artisan cache:clear
```

---

## Performance

### Große Dateien (>100 MB)

Bei großen Dateien kann der SOAP-Import mehrere Minuten dauern:

1. `SOAP_SOCKET_TIMEOUT` auf mindestens 900 (15 Min) setzen
2. PHP `max_execution_time` erhöhen
3. Webserver-Timeout (nginx/Apache) anpassen

### Typische Verarbeitungszeiten

| Dateigröße | Ungefähre Zeit |
|------------|----------------|
| < 10 MB | < 30 Sekunden |
| 10-50 MB | 30-120 Sekunden |
| 50-200 MB | 2-10 Minuten |
| > 200 MB | 10+ Minuten |

---

## Logs

### Wichtige Log-Einträge

```bash
# Letzte SOAP-Fehler
grep -i "soap" storage/logs/laravel.log | tail -50

# Letzte Ingest-Aktivitäten
grep -i "ingest\|import" storage/logs/laravel.log | tail -50

# Fehler der letzten Stunde
grep "$(date '+%Y-%m-%d %H')" storage/logs/laravel.log | grep -i error
```

### Log-Level anpassen

In `.env`:
```env
LOG_LEVEL=debug  # Für mehr Details
```
