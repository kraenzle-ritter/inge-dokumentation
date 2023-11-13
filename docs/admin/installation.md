# Installation

## Systemvoraussetzungen

Inge ist eine [Laravel (Version 10)](https://laravel.com/docs/10.x/deployment) Applikation. 

- PHP >= 8.1 (mit entsprechenden Extnsions)
- [MySQL](https://www.mysql.com/de/) >=5.7.8 or Maria DB >= 10.2.7
- [composer](https://getcomposer.org)
- email (e.g. sendmail)

## Installation

```bash 
composer install
```

```bash
php artisan migrate
```

## Einrichtung

### Archiv
- slug: Eindeutiges Kürzel für ein Archiv  
- Name: Vollständiger Name des Archivs  
- AIS URL: Link zum Archiv  

- SOAP URL: Link zum DIMAG Repository  
- SOAP Username: DIMAG User  
- SOAP Passwort: DIMAG Passwort  

- SFTP Adresse
- SFTP Port  
- SFTP Username
- SFTP Passwort
- SFTP Pfad

- DIMAG Parent AID (pid)

- SIP Base URL: Ablage der SIP Dateien durch das AIS

- URL für Rückmeldung bei erfolgreichem Import
- URL für Rückmeldung bei gescheitertem Import

### User
- User muss zu einem Archiv gehören
- User Bearer Token für Authentifizierung des AIS

### .env 
- zweiter User für Löschen
