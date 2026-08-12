# Projekt LDAP und SAMBA
> Erstellt von Lilas Dahdal und Alexander Wöhrer
### Projektidee
Installation und Konfiguratiom eines Samba Active Directory Domain Controllers unter Debian 13.6 mit Windows-10/11-Pro-Client.
Einrichtung von zwei Samba-Active-Directory-Domain-Controller mit der gleichen Domäne. Danach Einrichtung von NextCloud Server.

# Inhaltsverzeichnis

- [Netzwerkplanung](#netzwerkplanung)
- [Ablauf](#ablauf)
  - [1. Installation Debian Server](#1-installation-debian-server)
  - [2. sudo einrichten](#2-sudo-einrichten)
  - [3. Netzwerkkonfiguration](#3-netzwerkkonfiguration)
  - [4. Hosts-Datei konfigurieren](#4-hosts-datei-konfigurieren)
  - [5. Samba Active Directory installieren](#5-samba-active-directory-installieren)
  - [6. Samba-Domäne provisionieren](#6-samba-domäne-provisionieren)
  - [7. Samba auf internes Netzwerk beschränken](#7-samba-auf-internes-netzwerk-beschränken)
  - [8. Kerberos konfigurieren](#8-kerberos-konfigurieren)
  - [9. Samba-Dienst starten](#9-samba-dienst-starten)
  - [10. DNS als Domain Controller konfigurieren](#10-dns-als-domain-controller-konfigurieren)
  - [11. DNS überprüfen](#11-dns-überprüfen)
  - [12. DNS-Einträge mit samba-tool prüfen](#12-dns-einträge-mit-samba-tool-prüfen)
  - [13. Active-Directory-Datenbank prüfen](#13-active-directory-datenbank-prüfen)
  - [14. Kerberos testen](#14-kerberos-testen)
  - [15. Zeit synchronisieren](#15-zeit-synchronisieren)
  - [16. Benutzer erstellen](#16-benutzer-erstellen)
  - [17. Gruppen erstellen](#17-gruppen-erstellen)
  - [18. Administratorrechte vergeben](#18-administratorrechte-vergeben)
  - [19. Organisationseinheiten](#19-organisationseinheiten)
  - [20. Windows 11 konfigurieren](#20-windows-11-konfigurieren)
  - [21. Windows-Netzwerk testen](#21-windows-netzwerk-testen)
  - [22. Domain Controller von Windows finden](#22-domain-controller-von-windows-finden)
  - [23. Windows 11 der Domäne hinzufügen](#23-windows-11-der-domäne-hinzufügen)
  - [24. Anmeldung mit Domänenbenutzer testen](#24-anmeldung-mit-domänenbenutzer-testen)
  - [25. AD-Objekte kontrollieren](#25-ad-objekte-kontrollieren)
  - [26. Samba-Freigaben](#26-samba-freigaben)
  - [27. Einrichten eines zweiten Samba-Domain-Controllers](#27-einrichten-eines-zweiten-samba-domain-controllers)
    - [27.01 Debian auf DC2 vorbereiten](#2701-debian-auf-dc2-vorbereiten)
    - [27.02 Hostname von DC2 setzen](#2702-hostname-von-dc2-setzen)
    - [27.03 Netzwerk konfigurieren](#2703-netzwerk-konfigurieren)
    - [27.04 Prüfen, ob DC2 DC1 per Namen findet](#2704-prüfen-ob-dc2-dc1-per-namen-findet)
    - [27.05 Kerberos testen](#2705-kerberos-testen)
    - [27.06 Alte Samba-Konfiguration entfernen bzw. sichern](#2706-alte-samba-konfiguration-entfernen-bzw-sichern)
    - [27.07 Join](#2707-join)
    - [27.08 Kerberos-Konfiguration übernehmen](#2708-kerberos-konfiguration-übernehmen)
    - [27.09 Samba-AD-Dienst aktivieren](#2709-samba-ad-dienst-aktivieren)
    - [27.10 Prüfen, ob DC2 wirklich ein Domain Controller ist](#2710-prüfen-ob-dc2-wirklich-ein-domain-controller-ist)
    - [27.11 Replikation prüfen](#2711-replikation-prüfen)
    - [27.12 Funktionstest mit Benutzer](#2712-funktionstest-mit-benutzer)
  - [28. Installation Nextcloud Server](#28-installation-nextcloud-server)
    - [28.01 Netzwerk unter Debian konfigurieren](#2801-netzwerk-unter-debian-konfigurieren)
    - [28.02 Apache, MariaDB, PHP und benötigte Pakete installieren](#2802-apache-mariadb-php-und-benötigte-pakete-installieren)
    - [28.03 MariaDB für Nextcloud konfigurieren](#2803-mariadb-für-nextcloud-konfigurieren)
    - [28.04 Nextcloud herunterladen und installieren](#2804-nextcloud-herunterladen-und-installieren)
    - [28.05 Apache-Webserver für Nextcloud konfigurieren](#2805-apache-webserver-für-nextcloud-konfigurieren)
    - [28.06 Nextcloud über den Browser fertig einrichten](#2806-nextcloud-über-den-browser-fertig-einrichten)
    - [28.07 Nextcloud-Verzeichnis und OCC-Befehle](#2807-nextcloud-verzeichnis-und-occ-befehle)
    - [28.08 LDAP-App in Nextcloud aktivieren](#2808-ldap-app-in-nextcloud-aktivieren)
    - [28.09 Eigenes LDAP-Dienstkonto verwenden](#2809-eigenes-ldap-dienstkonto-verwenden)
    - [28.10 LDAP-Verbindung zu Samba in Nextcloud eintragen](#2810-ldap-verbindung-zu-samba-in-nextcloud-eintragen)
    - [28.11 LDAP-Verbindung unabhängig von Nextcloud testen](#2811-ldap-verbindung-unabhängig-von-nextcloud-testen)
      - [28.11.01 TLS-/StartTLS-Verbindung prüfen](#281101-tls-starttls-verbindung-prüfen)
      - [28.11.02 Fehlerbehebung bei leeren Benutzer- oder Objektklassen](#281102-fehlerbehebung-bei-leeren-benutzer--oder-objektklassen)
    - [28.12 LDAP-Benutzer und Gruppen in Nextcloud filtern](#2812-ldap-benutzer-und-gruppen-in-nextcloud-filtern)
      - [28.12.01 Vorhandene Samba-AD-Gruppen in Nextcloud prüfen](#281201-vorhandene-samba-ad-gruppen-in-nextcloud-prüfen)
      - [28.12.02 Anmeldung mit einem Samba-AD-Benutzer testen](#281202-anmeldung-mit-einem-samba-ad-benutzer-testen)
      - [28.12.03 DC2 für LDAP-Ausfallsicherheit prüfen](#281203-dc2-für-ldap-ausfallsicherheit-prüfen)
    - [28.13 Aktuelle LDAP-Konfiguration mit OCC prüfen](#2813-aktuelle-ldap-konfiguration-mit-occ-prüfen)
    - [28.14 PHP-Speicher für Nextcloud anpassen](#2814-php-speicher-für-nextcloud-anpassen)
    - [28.15 Nextcloud-Hintergrundjobs auf Cron umstellen](#2815-nextcloud-hintergrundjobs-auf-cron-umstellen)
    - [28.16 HTTPS für Nextcloud einrichten](#2816-https-für-nextcloud-einrichten)
      - [28.16.01 SSL-Zertifikat erstellen](#281601-ssl-zertifikat-erstellen)
      - [28.16.02 HTTPS-VirtualHost für Nextcloud konfigurieren](#281602-https-virtualhost-für-nextcloud-konfigurieren)
      - [28.16.03 HTTPS-Verbindung testen](#281603-https-verbindung-testen)
      - [28.16.04 HTTP auf HTTPS umleiten](#281604-http-auf-https-umleiten)
      - [28.16.05 HTTPS überprüfen](#281605-https-überprüfen)
    - [28.17 Nextcloud-Gesamtsystem testen](#2817-nextcloud-gesamtsystem-testen)
- [Command-Erklärungen](#command-erklärungen)

## Netzwerkplanung 

**Domainname:** `fruehwald.homenet.com`   
**NetBIOS-Domäne:** `FRUEHWALD`  
**Debian Domain Controller:**
* Hostname: `dc1'
* IP: `192.168.10.10`   

für die Einrichtung eines zweiten Domain Controller:
* Hostname: `dc2`
* IP: `192.168.10.11`

**Windows 11**
* IP: `192.168.10.21'
* DNS: `192.168.10.10`

**VirtualBox**   
Adapter 1
* NAT
* dient für Internetzugriff

Adapter 2
* Internes Netzwerk
* Name: `fruehwald'
* dient für die Kommunkation zwischen Domain Controllern, Windows und weiteren Servern


## Ablauf 
### 1. Installation Debian Server

Als Betriebssystem wird Debian verwendet mit der Version 13.6.

Während der Installation kann `Graphical install` verwendet werden. Auf dem eigentlichen Server wird jedoch keine grafische Desktop-Oberfläche installiert.

Softwareauswahl:
```
[ ] Debian desktop environment
[ ] GNOME
[ ] KDE
[ ] Xfce
[x] SSH server
[x] standard system utilities
```
Hostname (erste Domain)
```Bash 
dc1
```
Domain
```Bash 
fruehwald.homenet.com
```
Hostname prüfen
```Bash 
hostname
hostname -f
```
erwartete Ausgabe
```Bash 
dc1
dc1.fruehwald.homenet.com
```

### 2. sudo einrichten

Bei der Debian-Installation war `sudo` zunächst nicht installiert.

als Root anmelden
```Bash
su -
```
Paketliste aktualisieren
```Bash
apt update
```
`sudo` installieren
```Bash
apt install sudo
```
Benutzer zur Gruppe `sudo` hinzufügen
```Bash
usermod -aG sudo lildah
```
Danach vollständig abmelden und wieder anmelden.

Kontrolle
```Bash
groups
sudo whoami
```
Erwartete Ausgabe
```Bash
root
```
### 3. Netzwerkkonfiguration

Datei öffnen
```Bash
sudo nano /etc/network/interfaces
```
* Beispiel:
``` YAML
allow-hotplug enp0s3 => bleibt über DHCP konfiguriert
iface enp0s3 inet dhcp

iface enp0s3 inet6 auto

auto enp0s8 => bekommt statische IP
iface enp0s8 inet static
    address 192.168.10.10/24
```
Bearbeitungsmodus verlassen: <kbd>STRG+O</kbd> --> <kbd>Enter</kbd> --> <kbd>STRG+X</kbd>

Adapter aktivieren 
```Bash
sudo ifup enp0s8
```
Kontrolle
```Bash
ip -br a
```
erwartete Ausgabe
```Bash
enp0s3   UP   10.0.2.15/24
enp0s8   UP   192.168.10.10/24
```
### 4. Hosts-Datei konfigurieren

Datei öffnen
```Bash
sudo nano /etc/hosts
```
wichtige Einträge:
```Bash
127.0.0.1       localhost
192.168.10.10   dc1.fruehwald.homenet.com dc1
```
eventuell vorhandener Eintrag `127.0.1.1 dc1` sollte entfernt werden

Kontrolle
```Bash
hostname -f
```
Erwartete Ausgabe
```Bash
dc1.fruehwald.homenet.com
```
### 5. Samba Active Directory installieren

Paketliste aktualisieren
```Bash
sudo apt update
```
Benötigte Pakete installieren 
```Bash
sudo apt install samba-ad-dc smbclient krb5-user dnsutils ldb-tools
```

Diese Pakete enthalten:
* Samba
* Samba Active Directory Domain Controller
* LDAP-Funktionen
* Kerberos
* Samba Internal DNS
* SMB Client
* DNS-Werkzeuge

Einstellungen während Installation
``` YAML
Realm:
FRUEHWALD.HOMENET.COM

Kerberos-Server:
dc1.fruehwald.homenet.com

Administrative Kerberos-Server:
dc1.fruehwald.homenet.com
```
### 6. Samba-Domäne provisionieren

Vorhandene Samba-Konfiguration sichern
```Bash
sudo mv /etc/samba/smb.conf /etc/samba/smb.conf.bak
```
Neue Aktive-Directory-Domäne erstellen:
```Bash
sudo samba-tool domain provision --use-rfc2307 --interactive
```
Einstellungen
``` YAML
Realm:
FRUEHWALD.HOMENET.COM

Domain:
FRUEHWALD

Server Role:
dc

DNS Backend:
SAMBA_INTERNAL

DNS Forwarder:
10.0.2.3
```
Danach Passwort für Samba-AD-Administrator festlegen
Konto lautet: `FRUEHWALD\Administrator`

Die Provisionierung erstellt:
* LDAP-/AD-Datenbank
* Kerberos-Datenbank
* DNS-Zonen
* Administrator-Konto
* Kerberos-Konto krbtgt
* SYSVOL
* NETLOGON
* Gruppen und AD-Struktur

### 7. Samba auf internes Netzwerk beschränken
Da der Domain Controller zwei Netzwerkadapter besitzt, soll Samba ausschließlich das interne Netzwerk verwenden.

Datei öffnen
```Bash
sudo nano /etc/samba/smb.conf
```

* Beispiel:
``` YAML
[global]
        dns forwarder = 10.0.2.3
        interfaces = lo enp0s8
        bind interfaces only = yes
        netbios name = DC1
        realm = FRUEHWALD.HOMENET.COM
        server role = active directory domain controller
        workgroup = FRUEHWALD
```
Konfiguration prüfen
``` Bash
sudo testparm -s
```
Wichtig: `Server role: ROLE_ACTIVE_DIRECTORY_DC`
### 8. Kerberos konfigurieren
Die von Samba erzeugte Kerberos-Konfiguration wird übernommen
``` Bash
sudo cp -f /var/lib/samba/private/krb5.conf /etc/krb5.conf
```
Kontrolle
``` Bash
cat /etc/krb5.conf
```
Wichtig: Realm sollte `FRUEHWALD.HOMENET.COM` sein
### 9. Samba-Dienst starten
Samba neu starten
``` Bash
sudo systemctl restart samba-ad-dc
```
Status prüfen
``` Bash
sudo systemctl status samba-ad-dc --no-pager
```
Erwartete Ausgabe
``` Bash
Active: active (running)
```
WICHTIG: Auf einem Active Directory Domain Controller wird hauptsächlich der Dienst `samba-ad-dc` verwendet.
Daher ist es nicht notwendig, wie bei einem klassischem Samba-Fileserver `systemctl restart smbd` und `systemctl restart nmbd` separat zu verwenden.

### 10. DNS als Domain Controller konfigurieren 
Der Domain Controller soll für seine eigene AD-Domäne den Samba-DNS verwenden.

`dhcpcd` kann bei Debian die `/etc/resolv.conf` automatisch verändern
``` Bash
sudo nano /etc/dhcpcd.conf
```
Zeile ergänzen 
``` Bash
nohook resolv.conf
```
NAT-Adpater neu starten
``` Bash
sudo ifdown enp0s3
sudo ifup enp0s3
```
DNS-Konfiguration öffnen
``` Bash
sudo nano /etc/resolv.conf
```
Inhalt
``` Bash
nameserver 127.0.0.1
search fruehwald.homenet.com
```
Kontrolle
``` Bash
cat /etc/resolv.conf
```

### 11. DNS überprüfen
Hosteintrag
``` Bash
host -t A dc1.fruehwald.homenet.com 127.0.0.1
```
Erwartete Ausgabe
``` Bash
dc1.fruehwald.homenet.com has address 192.168.10.10
```
LDAP-SRV-Eintrag
``` Bash
host -t SRV _ldap._tcp.fruehwald.homenet.com 127.0.0.1
```
Erwartet wird ein Eintrag für
``` Bash
dc1.fruehwald.homenet.com
Port 389
```
Kerberos-SRV-Eintrag
``` Bash
host -t SRV _kerberos._udp.fruehwald.homenet.com 127.0.0.1
```
Erwartet wird ein Eintrag für
``` Bash
dc1.fruehwald.homenet.com
Port 88
```
### 12. DNS-Einträge mit samba-tool prüfen
Ein A-Eintrag ist ein DNS-Eintrag, der einen Namen auf eine IPv4-Adresse abbildet.

A-Record anzeigen 
``` Bash
sudo samba-tool dns query 127.0.0.1 fruehwald.homenet.com dc1 A -U Administrator
```
Der richtige Eintrag lautet
``` Bash
A: 192.168.10.10
```
Während des Prüfen wurde herausgestellt, dass für `dc1.fruehwald.homenet.com` zwei Adressen registriert wurde:
``` Bash
dc1.fruehwald.homenet.com
  * 192.168.10.10 internes Domänennetz 
  * 10.0.2.15 VirtualBox-NAT <-falsch
```
D.h. der falsche DNS-A-Eintrag der NAT-Adresse wurde aus dem Samba-AD-DNS gelöscht.
``` Bash
sudo samba-tool dns delete 127.0.0.1 fruehwald.homenet.com dc1 A 10.0.2.15 -U Administrator
```
### 13. Active-Directory-Datenbank prüfen
``` Bash
sudo samba-tool dbcheck
```
Erwartete Ausgabe
``` Bash
Checked 282 objects (0 errors)
```
### 14. Kerberos testen
Ticket anfordern
``` Bash
kinit Administrator
```
Dann
``` Bash
klist
```
Erwartete Ausgabe
``` Bash
Administrator@FRUEHWALD.HOMENET.COM
```
### 15. Zeit synchronisieren
Kerberos benötigt eine möglichst genaue Zeit zwischen Domain Controller und Clients.

Status prüfen
``` Bash
timedatectl
```
Zeitsynchronisierung aktivieren
``` Bash
sudo timedatectl set-ntp true
```
NTP-Status anzeigen
``` Bash
timedatectl timesync-status
```
Zeitzone sollte `Europa/Vienna` sein

### 16. Benutzer erstellen
``` Bash
sudo samba-tool user create lilas.dahdal
sudo samba-tool user create alexander.woehrer
```
Jeweils ein Passwort vergeben

Benutzer anzeigen
``` Bash
sudo samba-tool user list
```
Benutzerinformationen anzeigen
``` Bash
sudo samba-tool user show lilas.dahdal
sudo samba-tool user show alexander.woehrer
```
Passwort ändern
``` Bash
sudo samba-tool user setpassword lilas.dahdal
sudo samba-tool user setpassword alexander.woehrer
```
### 17. Gruppen erstellen
``` Bash
sudo samba-tool group add Aerzte
sudo samba-tool group add IT
```
Benutzer zu Gruppen hinzufügen
``` Bash
sudo samba-tool group addmembers Aerzte lilas.dahdal
sudo samba-tool group addmembers IT alexander.woehrer
```
Gruppe anzeigen
``` Bash
sudo samba-tool group list
```
Mitglieder der Gruppe anzeigen
``` Bash
sudo samba-tool group listmembers Aerzte
sudo samba-tool group listmembers IT
```
Gruppe löschem
``` Bash
sudo samba-tool group delete IT
```
=> nur die Gruppe und die Mitgliedschaft wird gelöscht; Die Benutzerkonten bleiben bestehen
### 18. Administratorrechte vergeben
normaler Benutzer zum Mitglied der Gruppe `Domain Admins` machen
``` Bash
sudo samba-tool group addmembers "Domain Admins" max.mueller
```

### 19. Organisationseinheiten
Anstatt alle Benutzer unter Users zu lassen, könnten OUs erstellt werden.  
Beispiel
``` YAML
fruehwald.homenet.com
│
├── OU=Mitarbeiter
│   ├── OU=Aerzte
│   └── OU=IT
│
├── OU=Computer
│   ├── OU=Clients
│   └── OU=Server
│
└── OU=Gruppen
```
OU erstellen
``` Bash
sudo samba-tool ou create "OU=Mitarbeiter,DC=fruehwald,DC=homenet,DC=com"
```
Untergeordenete OU
``` Bash
sudo samba-tool ou create "OU=IT,OU=Mitarbeiter,DC=fruehwald,DC=homenet,DC=com"
```
OUs anzeigen
``` Bash
sudo samba-tool ou list
```
### 20. Windows 11 konfigurieren
Windows-Client verwendet:
``` Bash
IP-Adresse:    192.168.10.21
Subnetzmaske:  255.255.255.0
DNS-Server:    192.168.10.10
```
Der interne Netzwerkadapter besizt kein Gateway.   

### 21. Windows-Netzwerk testen
IP-Konfiguration
``` Bash
ipconfig /all
```
Verbindung zum Domain Controller
``` Bash
ping 192.168.10.10
```
DNS
``` Bash
nslookup dc1.fruehwald.homenet.com
```
Erwartete Adresse
``` Bash
192.168.10.10
```
SRV-Einträge in Windows über PowerShell prüfen
``` Bash
Resolve-DnsName -Name _ldap._tcp.fruehwald.homenet.com -Type SRV -Server 192.168.10.10
```
Erwartete Ausgabe
``` Bash
NameTarget: dc1.fruehwald.homenet.com
Port: 389
```

### 22. Domain Controller von Windows finden
``` Bash
nltest /dsgetdc:fruehwald.homenet.com
```
erwartete Ausgabe
``` Bash
Domänencontroller: \\dc1.fruehwald.homenet.com
Adresse:           \\192.168.10.10
Domänenname:        fruehwald.homenet.com
```
folgende Dienste wurde erkannt
``` Bash
PDC
GC
LDAP
KDC
TIMESERV
DNS_DC
```
### 23. Windows 11 der Domäne hinzufügen
Unter Windows:
1. System
2. Info
3. Erweitere Systemeinstellungen
4. Reiter Computername
5. Ändern
6. Domäne auswählen
7. `fruehwald.homenet.com` eintragen

Für den Join wurde verwendet:
`FRUEHWALD\Administrator`
mit dem Samba-Administrator-Passwort

Nach erfolgreichem Beitritt erscheint die Meldung
``` Bash
Willkommen in der Domäne fruehwald.homenet.com
```
Danach Windows neu starten

### 24. Anmeldung mit Domänenbenutzer testen

Nach dem Neustart die Benutzer anmelden
``` Bash
FRUEHWALD\lilas.dahdal
FRUEHWALD\alexander.woehrer
```
Prüfen
``` Bash
whoami
```
erwartete Ausgabe
``` Bash
fruehwald\lilas.dahdal
fruehwald\alexander.woehrer
```
Anmeldeserver prüfen
``` Bash
echo %LOGONSERVER%
```
erwartete Ausgabe
``` Bash
\\DC1
```
Gruppen des angemeldeten Benutzers prüfen
``` Bash
whoami /groups
```

### 25. AD-Objekte kontrollieren
Benutzer
``` Bash
sudo samba-tool user list
```
Gruppen
``` Bash
sudo samba-tool group list
```
Computer
``` Bash
sudo samba-tool computer list
```
Domain-Informationen
``` Bash
sudo samba-tool domain info 127.0.0.1
```
Für neue Debian-domäne sollte folgendes erscheinen
``` Bash
Forest          : fruehwald.homenet.com
Domain          : fruehwald.homenet.com
Netbios domain  : FRUEHWALD
DC name         : dc1.fruehwald.homenet.com
```

### 26. Samba-Freigaben
Verzeichnisse anlegen (fürs Testen)
``` Bash
sudo mkdir -p /srv/samba/patientendaten
sudo mkdir -p /srv/samba/verwaltung
```
Freigaben definieren
``` Bash
sudo nano /etc/samba/smb.conf
```
Beispiel
``` YAML
[Verwaltung]
    path = /srv/samba/verwaltung
    browseable = yes
    read only = yes
    valid users = @Verwaltung @IT
    write list = @Verwaltung @IT
```
Zugriff verweigern mit `invalid users = @IT`  

Samba-Konfiguration prüfen
``` Bash
sudo testparm -s
```
erwartete Ausgabe
``` Bash
Loaded services file OK.
```
Da der Server ein Active Directory Domain Controller ist, wird verwendet
``` Bash
sudo systemctl restart samba-ad-dc
```

### 27. Einrichten eines zweiten Samba-Domain-Controllers
### 27.01 Debian auf DC2 vorbereiten
Debian aktualisieren
``` Bash
sudo apt update
sudo apt upgrade -y
```
Samba-Pakete installieren
``` Bash
sudo apt install samba smbclient winbind krb5-user dnsutils -y
```
``` YAML
Realm:
FRUEHWALD.HOMENET.COM
``` 

### 27.02 Hostname von DC2 setzen
``` Bash
sudo hostnamectl set-hostname dc2
```
Datei öffnen
```Bash
sudo nano /etc/hosts
```
wichtige Einträge:
```Bash
127.0.0.1       localhost
192.168.10.10   dc1.fruehwald.homenet.com dc1
```
eventuell vorhandener Eintrag `127.0.1.1 dc1` sollte entfernt werden

Kontrollieren
``` Bash
hostname
hostname -f
```
erwartete Ausgabe
``` Bash
dc2
dc2.fruehwald.homenet.com
```

### 27.03 Netzwerk konfigurieren
Datei öffnen
```Bash
sudo nano /etc/network/interfaces
```
* Beispiel:
``` YAML
allow-hotplug enp0s3 => bleibt über DHCP konfiguriert
iface enp0s3 inet dhcp

iface enp0s3 inet6 auto

auto enp0s8 => bekommt statische IP
iface enp0s8 inet static
    address 192.168.10.11/24
```
Bearbeitungsmodus verlassen: <kbd>STRG+O</kbd> --> <kbd>Enter</kbd> --> <kbd>STRG+X</kbd>

Adapter aktivieren 
```Bash
sudo ifup enp0s8
```
Kontrolle
```Bash
ip -br a
```
erwartete Ausgabe
```Bash
enp0s3   UP   10.0.2.15/24
enp0s8   UP   192.168.10.11/24
```
testen, ob DC1 erreichbar ist
```Bash
ping 192.168.10.10
```
DNS setzen 
```Bash
sudo nano /etc/resolv.conf
```
Beispiel
```Bash
nameserver 192.168.10.10
search fruehwald.homenet.com
```
DNS prüfen
```Bash
cat /etc/resolv.conf
```
erwartete Ausgabe
```Bash
nameserver 192.168.10.10
search fruehwald.homenet.com
```
### 27.04 Prüfen, ob DC2 DC1 per Namen findet
```Bash
ping dc1.fruehwald.homenet.com
```
Danach
```Bash
nslookup dc1.fruehwald.homenet.com
```

### 27.05 Kerberos testen
```Bash
kinit Administrator
```
Dann Domain-Administratorpasswort eingeben  
Danach
```Bash
klist
```
Erwartete Ausgabe
```Bash
Administrator@FRUEHWALD.HOMENET.COM
```
### 27.06 Alte Samba-Konfiguration entfernen bzw. sichern
Falls `/etc/samba/smb.conf` existiert
```Bash
sudo mv /etc/samba/smb.conf /etc/samba/smb.conf.backup
```
Wichtig, weil DC2 beim Join seine passende Domain-Controller-Konfiguration bekommt

### 27.07 Join
Auf DC2
```Bash
sudo samba-tool domain join fruehwald.homenet.com DC -U Administrator
```
Dann Passwort eingeben

Hier ist das `DC` wichtig: Es bedeutet `Join der bestehenden Domäne als Domain Controller`. 

### 27.08 Kerberos-Konfiguration übernehmen
Nach erfolgreichem Join 
```Bash
sudo cp /var/lib/samba/private/krb5.conf /etc/krb5.conf
```
### 27.09 Samba-AD-Dienst aktivieren
normale Samba-Dienste deaktivieren
```Bash
sudo systemctl disable --now smbd
sudo systemctl disable --now nmbd
sudo systemctl disable --now winbind
```
Dann
```Bash
sudo systemctl enable --now samba-ad-dc
```
Prüfen
```Bash
sudo systemctl status samba-ad-dc
```
Erwartete Ausgabe
```Bash
active (running)
```
### 27.10 Prüfen, ob DC2 wirklich ein Domain Controller ist
```Bash
sudo testparm
```
Erwartete (WICHTIG) Ausgabe
```Bash
server role = active directory domain controller
```
### 27.11 Replikation prüfen
Prüfen, ob DC2 Daten von DC1 repliziert
```Bash
sudo samba-tool drs showrepl
```
auf DC2 testen
```Bash
sudo samba-tool drs bind dc1.fruehwald.homenet.com
```
auf DC1 testen
```Bash
sudo samba-tool drs bind dc2.fruehwald.homenet.com
```
Dadurch testet man beide Richtungen

### 27.12 Funktionstest mit Benutzer
auf DC1 sollte ein User (oder mehrere) vorhanden sein

Danach auf DC2
```Bash
sudo samba-tool user list
```
Erwartete Ausgabe
```Bash
lilas.dahdal
alexander.woehrer
```

### 28. Installation Nextcloud Server 

Für Nextcloud muss eine eigene Debian-Server eingerichtet werden. Der Nextcloud-Server befindet sich im gleichen internen Netzwerk wie die beiden Samba-Domain-Controller und kann dadurch direkt mit dem Samba Active Directory kommunizieren.

Verwendete Systeme
```Bash
DC1:        192.168.10.10
DC2:        192.168.10.11
Nextcloud:  192.168.10.30
Domäne:     fruehwald.homenet.com
```
Hostname festlegen
```bash
sudo hostnamectl set-hostname nextcloud
```
Hostname prüfen
```bash
hostnamectl
hostname -f
```

### 28.01 Netzwerk unter Debian konfigurieren

Die Netzwerkkonfiguration erfolgt über die unter Debian vorhandene Netzwerkkonfiguration.

Zuerst die vorhandenen Netzwerkschnittstellen prüfen
```bash
ip a
```

Die Nextcloud-VM verwendet im internen Netzwerk eine feste IP-Adresse:
```text
IP-Adresse: 192.168.10.30/24
DNS-Server: 192.168.10.10 und 192.168.10.11
DNS-Suchdomäne: fruehwald.homenet.com
```

Wichtig ist, dass als DNS-Server die beiden Samba-Domain-Controller verwendet werden. Dadurch kann Nextcloud die interne Samba-Domäne und die LDAP-Dienste korrekt auflösen.

Nach der Konfiguration Netzwerk prüfen
```bash
ip a
ip route
```
Verbindung zu beiden Domain Controllern testen
```bash
ping -c 4 192.168.10.10
ping -c 4 192.168.10.11
```
DNS-Auflösung prüfen
```bash
host dc1.fruehwald.homenet.com
host dc2.fruehwald.homenet.com
host -t SRV _ldap._tcp.fruehwald.homenet.com
```
Damit ist sichergestellt, dass der Nextcloud-Server die Samba-Domäne und deren LDAP-Dienst im internen Netzwerk erreichen kann.


### 28.02 Apache, MariaDB, PHP und benötigte Pakete installieren

Paketlisten aktualisieren
```bash
sudo apt update
sudo apt upgrade -y
```
Apache und MariaDB installieren
```bash
sudo apt install apache2 mariadb-server -y
```
PHP und die für Nextcloud benötigten PHP-Module installieren
```bash
sudo apt install libapache2-mod-php php php-gd php-mysql php-curl \
php-mbstring php-intl php-gmp php-bcmath php-xml php-imagick \
php-zip php-ldap unzip wget -y
```
Apache prüfen
```bash
sudo systemctl status apache2
```
MariaDB prüfen
```bash
sudo systemctl status mariadb
```
Falls ein Dienst noch nicht aktiviert ist
```bash
sudo systemctl enable --now apache2
sudo systemctl enable --now mariadb
```

### 28.03 MariaDB für Nextcloud konfigurieren
MariaDB absichern
```bash
sudo mysql_secure_installation
```
Danach MariaDB öffnen
```bash
sudo mysql
```
Eigene Datenbank für Nextcloud erstellen
```sql
CREATE DATABASE nextcloud CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci;
```
Datenbankbenutzer erstellen
```sql
CREATE USER 'nextcloud'@'localhost' IDENTIFIED BY '<SICHERES_PASSWORT>';
```
Dem Benutzer alle Rechte auf der Nextcloud-Datenbank geben
```sql
GRANT ALL PRIVILEGES ON nextcloud.* TO 'nextcloud'@'localhost';
FLUSH PRIVILEGES;
```
MariaDB verlassen
```sql
EXIT;
```

Für die spätere Webinstallation werden damit folgende Werte verwendet:
```text
Datenbankname: nextcloud
Datenbankbenutzer: nextcloud
Datenbankpasswort: <SICHERES_PASSWORT>
Datenbankhost: localhost
```

### 28.04 Nextcloud herunterladen und installieren

In ein temporäres Verzeichnis wechseln
```bash
cd /tmp
```
Nextcloud herunterladen
```bash
wget https://download.nextcloud.com/server/releases/latest.zip
```
Archiv entpacken
```bash
unzip latest.zip
```
Entpackten Nextcloud-Ordner in das Apache-Webverzeichnis verschieben
```bash
sudo mv nextcloud /var/www/
```
Danach befindet sich die Nextcloud-Installation unter
```text
/var/www/nextcloud
```
Besitzer und Gruppe für Apache setzen
```bash
sudo chown -R www-data:www-data /var/www/nextcloud
```
Berechtigungen setzen
```bash
sudo find /var/www/nextcloud -type d -exec chmod 750 {} \;
sudo find /var/www/nextcloud -type f -exec chmod 640 {} \;
```

### 28.05 Apache-Webserver für Nextcloud konfigurieren

Neue Apache-Konfigurationsdatei erstellen
```bash
sudo nano /etc/apache2/sites-available/nextcloud.conf
```
Folgende Grundkonfiguration eintragen
```apache
<VirtualHost *:80>
    ServerName nextcloud.fruehwald.homenet.com
    DocumentRoot /var/www/nextcloud

    <Directory /var/www/nextcloud/>
        Require all granted
        AllowOverride All
        Options FollowSymLinks MultiViews
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/nextcloud_error.log
    CustomLog ${APACHE_LOG_DIR}/nextcloud_access.log combined
</VirtualHost>
```

Bearbeitungsmodus verlassen: <kbd>STRG+O</kbd> --> <kbd>Enter</kbd> --> <kbd>STRG+X</kbd>

Nextcloud-Seite aktivieren
```bash
sudo a2ensite nextcloud.conf
```
Benötigte Apache-Module aktivieren
```bash
sudo a2enmod rewrite headers env dir mime setenvif
```
Optional die Standardseite deaktivieren
```bash
sudo a2dissite 000-default.conf
```
Apache-Konfiguration prüfen
```bash
sudo apache2ctl configtest
```
Erwartete Ausgabe
```text
Syntax OK
```
Apache neu laden
```bash
sudo systemctl reload apache2
```
Status prüfen
```bash
sudo systemctl status apache2
```

### 28.06 Nextcloud über den Browser fertig einrichten

Die Weboberfläche zunächst über die interne IP-Adresse aufrufen
```text
http://192.168.10.30
```

Im Webinstaller ein lokales Nextcloud-Administratorkonto erstellen.

Bei der Datenbankkonfiguration die zuvor erstellten MariaDB-Werte verwenden
```text
Datenbankbenutzer: nextcloud
Datenbankpasswort: <SICHERES_PASSWORT>
Datenbankname: nextcloud
Datenbankhost: localhost
```

Verwendeter Datenpfad
```text
/var/www/nextcloud/data
```

Nach Abschluss der Installation kann der Nextcloud-Status über `occ` geprüft werden. ??

### 28.07 Nextcloud-Verzeichnis und OCC-Befehle

In das Nextcloud-Verzeichnis wechseln
```bash
cd /var/www/nextcloud
```
Prüfen, ob man sich im richtigen Verzeichnis befindet
```bash
pwd
```
Erwartete Ausgabe
```text
/var/www/nextcloud
```
Nextcloud-Status prüfen
```bash
sudo -E -u www-data php occ status
```
Installierte bzw. verfügbare Apps anzeigen
```bash
sudo -E -u www-data php occ app:list
```

`occ` ist die Kommandozeilenschnittstelle von Nextcloud. Die Befehle werden deshalb normalerweise direkt im Nextcloud-Verzeichnis ausgeführt.

### 28.08 LDAP-App in Nextcloud aktivieren

Damit Benutzer und Gruppen aus dem Samba Active Directory in Nextcloud verwendet werden können, muss die LDAP-App aktiviert sein.
```bash
cd /var/www/nextcloud
```
LDAP-App aktivieren
```bash
sudo -E -u www-data php occ app:enable user_ldap
```
Prüfen, ob die App aktiviert ist
```bash
sudo -E -u www-data php occ app:list | grep user_ldap
```

Danach erscheint in den Nextcloud-Verwaltungseinstellungen der Bereich `LDAP/AD-Integration`.

### 28.09 Eigenes LDAP-Dienstkonto verwenden

Für die LDAP-Anbindung sollte Nextcloud nicht dauerhaft das Domänen-Administratorkonto verwenden. Stattdessen wurde ein eigenes LDAP-Dienstkonto angelegt.

Verwendetes Konto
```text
CN=nextcloud.ldap,CN=Users,DC=fruehwald,DC=homenet,DC=com
```

Dieses Konto wird ausschließlich dafür verwendet, dass Nextcloud Benutzer und Gruppen aus dem Samba Active Directory abfragen kann.  
Dadurch muss das Administrator-Konto nicht in der Nextcloud-LDAP-Konfiguration hinterlegt werden.

### 28.10 LDAP-Verbindung zu Samba in Nextcloud eintragen

In Nextcloud
1. Mit dem lokalen Nextcloud-Administratorkonto anmelden.
2. Rechts oben das Profilbild öffnen.
3. `Verwaltungseinstellungen` öffnen.
4. `LDAP/AD-Integration` auswählen.
5. Eine neue LDAP-Konfiguration anlegen.

Für DC1 werden grundsätzlich folgende Werte verwendet
```text
Host: dc1.fruehwald.homenet.com
Port: 389
Benutzer-DN: CN=nextcloud.ldap,CN=Users,DC=fruehwald,DC=homenet,DC=com
Passwort: <PASSWORT_DES_LDAP_DIENSTKONTOS>
Base-DN: DC=fruehwald,DC=homenet,DC=com
```

Der Benutzer-DN beschreibt das Samba-AD-Konto, das Nextcloud für LDAP-Abfragen verwendet. Die Base-DN legt fest, ab welchem Punkt im Active Directory nach Benutzern und Gruppen gesucht wird.

Nach dem Eintragen müssen die Zugangsdaten gespeichert und anschließend die Verbindung getestet werden.

### 28.11 LDAP-Verbindung unabhängig von Nextcloud testen

Während der Einrichtung trat zwischenzeitlich die Fehlermeldung auf.
```text
Could not connect to LDAP
```

Um zu prüfen, ob der Fehler bei Nextcloud oder bereits bei Netzwerk bzw. LDAP liegt, wurde die Verbindung direkt vom Nextcloud-Server getestet.

Benötigte Werkzeuge installieren
```bash
sudo apt install ldap-utils netcat-openbsd openssl -y
```
DC1 per Netzwerk testen
```bash
ping -c 4 dc1.fruehwald.homenet.com
```
DNS prüfen
```bash
host dc1.fruehwald.homenet.com
host -t SRV _ldap._tcp.fruehwald.homenet.com
```
LDAP-Port 389 prüfen
```bash
nc -vz dc1.fruehwald.homenet.com 389
```
Eine erfolgreiche Ausgabe sieht beispielsweise so aus
```text
Connection to dc1.fruehwald.homenet.com 389 port [tcp/ldap] succeeded!
```

Direkte LDAP-Abfrage mit dem LDAP-Dienstkonto durchführen
```bash
ldapsearch -x \
  -H ldap://dc1.fruehwald.homenet.com:389 \
  -D "CN=nextcloud.ldap,CN=Users,DC=fruehwald,DC=homenet,DC=com" \
  -W \
  -b "DC=fruehwald,DC=homenet,DC=com" \
  "(objectClass=user)" sAMAccountName
```

Bedeutung der wichtigsten Optionen
- `-x` verwendet eine einfache LDAP-Anmeldung.
- `-H` gibt den LDAP-Server an.
- `-D` gibt den Benutzer bzw. Bind-DN an.
- `-W` fragt das Passwort erst nach dem Start des Befehls ab.
- `-b` setzt die Base-DN für die Suche.

Wenn Benutzer zurückgegeben werden, funktionieren DNS, Netzwerk, LDAP-Port, Bind-DN, Passwort und Base-DN grundsätzlich korrekt.

#### 28.11.01 TLS-/StartTLS-Verbindung prüfen

Da die LDAP-Verbindung zunächst trotz erreichbarem LDAP-Port Probleme verursachte, wurde auch der TLS-/StartTLS-Handshake geprüft.
```bash
openssl s_client -connect dc1.fruehwald.homenet.com:389 -starttls ldap -showcerts
```

Dabei ist wichtig, dass der verwendete Hostname zum Zertifikat des Domain Controllers passt.

Die LDAP-Anbindung konnte anschließend erfolgreich hergestellt werden.

#### 28.11.02 Fehlerbehebung bei leeren Benutzer- oder Objektklassen

Wenn in Nextcloud keine Benutzer oder Objektklassen angezeigt werden, sollte die Konfiguration in dieser Reihenfolge geprüft werden
1. LDAP-Host und Port eintragen.
2. Benutzer-DN und Passwort des LDAP-Dienstkontos eintragen.
3. Zugangsdaten speichern.
4. Base-DN `DC=fruehwald,DC=homenet,DC=com` verwenden.
5. Verbindung testen.
6. Erst danach Benutzer- und Gruppenfilter konfigurieren.

Auf einem Domain Controller kann zusätzlich geprüft werden, ob ein Benutzer im Active Directory vorhanden ist
```bash
sudo samba-tool user show lilas.dahdal
```

Nachdem die LDAP-Verbindung korrekt hergestellt war, wurden die AD-Benutzer in Nextcloud erkannt.

### 28.12 LDAP-Benutzer und Gruppen in Nextcloud filtern

Sobald die LDAP-Verbindung erfolgreich hergestellt wurde, kann festgelegt werden, welche Benutzer und Gruppen aus dem Samba Active Directory in Nextcloud sichtbar sein sollen.

Verwendete Basis
```text
DC=fruehwald,DC=homenet,DC=com
```

Die Benutzer und Gruppen werden `nicht erneut in Nextcloud erstellt`. Nextcloud liest die bereits im Samba Active Directory vorhandenen Konten über LDAP aus.  
Die zentrale Benutzerverwaltung bleibt damit im Samba Active Directory. Nextcloud verwendet LDAP für die Suche, Zuordnung und Anmeldung der vorhandenen AD-Benutzer.

Benutzerfilter und Login-Filter erfüllen dabei unterschiedliche Aufgaben:
- Der **Benutzerfilter** bestimmt, welche LDAP-Benutzer Nextcloud grundsätzlich kennt bzw. importiert.
- Der **Login-Filter** bestimmt, mit welchen Attributen sich diese Benutzer anmelden können, zum Beispiel über `sAMAccountName`.

### 28.12.01 Vorhandene Samba-AD-Gruppen in Nextcloud prüfen

In Nextcloud
1. Mit dem Nextcloud-Administratorkonto anmelden.
2. `Verwaltungseinstellungen` öffnen.
3. `LDAP/AD-Integration` auswählen.
4. Die bestehende LDAP-Konfiguration öffnen.
5. Zum Bereich `Gruppen` wechseln.
6. Prüfen, ob vorhandene Samba-AD-Gruppen gefunden werden.

Damit wird geprüft, ob Nextcloud neben den Benutzern auch die Gruppenstruktur des Samba Active Directory korrekt auslesen kann.

#### 28.12.02 Anmeldung mit einem Samba-AD-Benutzer testen

1. Vom lokalen Nextcloud-Administratorkonto abmelden.
2. Einen im Samba Active Directory vorhandenen Domänenbenutzer verwenden.
3. Das Passwort dieses AD-Benutzers eingeben.
4. Anmeldung durchführen.

Die Anmeldung mit einem Samba-AD-Benutzer war erfolgreich. Damit ist bestätigt, dass Nextcloud die Benutzerkonten aus dem Samba Active Directory verwendet.

#### 28.12.03 DC2 für LDAP-Ausfallsicherheit prüfen

Da DC1 und DC2 dieselben Active-Directory-Daten replizieren, wurde ebenfalls geprüft, ob DC2 vom Nextcloud-Server erreichbar ist
```bash
ping -c 4 dc2.fruehwald.homenet.com
```

LDAP-Port prüfen
```bash
nc -vz dc2.fruehwald.homenet.com 389
```

TLS-/StartTLS-Handshake prüfen
```bash
openssl s_client -connect dc2.fruehwald.homenet.com:389 -starttls ldap -showcerts
```

Damit wurde bestätigt, dass auch der zweite Domain Controller grundsätzlich für LDAP-Abfragen erreichbar ist.

### 28.13 Aktuelle LDAP-Konfiguration mit OCC prüfen

Die konfigurierte LDAP-Verbindung kann direkt über Nextcloud geprüft werden
```bash
cd /var/www/nextcloud
```

Vorhandene LDAP-Konfigurationen anzeigen
```bash
sudo -E -u www-data php occ ldap:show-config
```

Eine bestimmte Konfiguration anzeigen, in unserem Fall `s01`
```bash
sudo -E -u www-data php occ ldap:show-config s01
```

Damit können unter anderem LDAP-Host, Base-DN, Filter und weitere LDAP-Einstellungen kontrolliert werden.

### 28.14 PHP-Speicher für Nextcloud anpassen

Für Nextcloud wurde der PHP-Speicher erhöht, damit ausreichend Arbeitsspeicher für PHP-Prozesse zur Verfügung steht.

Zuerst die verwendete PHP-Version prüfen
```bash
php -v
```

Die PHP-Konfiguration für Apache öffnen. Der genaue Versionsordner hängt von der installierten PHP-Version ab
```bash
sudo nano /etc/php/<PHP-VERSION>/apache2/php.ini
```

Dort den Wert `memory_limit` anpassen, beispielsweise
```ini
memory_limit = 512M
```

Anschließend Apache neu starten
```bash
sudo systemctl restart apache2
```

Für OCC/CLI-Befehle existiert zusätzlich eine eigene PHP-Konfiguration
```bash
sudo nano /etc/php/<PHP-VERSION>/cli/php.ini
```

Der CLI-Wert kann separat gesetzt werden.

### 28.15 Nextcloud-Hintergrundjobs auf Cron umstellen

Nextcloud benötigt regelmäßig ausgeführte Hintergrundjobs für Wartungs- und Aufräumarbeiten. Dafür wurde Cron verwendet.

Cron-Dienst prüfen
```bash
sudo systemctl status cron
```

Falls notwendig aktivieren
```bash
sudo systemctl enable --now cron
```

Cronjob für den Webserver-Benutzer bearbeiten
```bash
sudo crontab -u www-data -e
```

Folgende Zeile eintragen
```cron
*/5 * * * * php -f /var/www/nextcloud/cron.php
```

Damit wird der Nextcloud-Cronjob alle fünf Minuten durch den Benutzer `www-data` ausgeführt.

In den Nextcloud-Verwaltungseinstellungen unter `Grundeinstellungen → Hintergrund-Aufgaben` anschließend `Cron` auswählen.

Der Cronjob wurde auf unserem Debian-Nextcloud-Server bereits eingerichtet.

### 28.16 HTTPS für Nextcloud einrichten

Nach der funktionierenden Nextcloud- und LDAP-Einrichtung wird die Verbindung zusätzlich mit HTTPS abgesichert.

HTTPS sorgt dafür, dass die Kommunikation zwischen Browser und Nextcloud-Server verschlüsselt übertragen wird. Dadurch können beispielsweise Anmeldedaten nicht mehr unverschlüsselt über das Netzwerk übertragen werden.

Apache-SSL-Modul aktivieren
```bash
sudo a2enmod ssl
```
Anschließend Apache neu laden
```bash
sudo systemctl reload apache2
```
Der verwendete DNS-Name des Nextcloud-Servers lautet
```text
nextcloud.fruehwald.homenet.com
```
Dieser Name sollte auch für das Zertifikat verwendet werden, damit Zertifikat und aufgerufener Hostname übereinstimmen.

#### 28.16.01 SSL-Zertifikat erstellen

Da sich der Nextcloud-Server in unserem internen Testnetz befindet, kann für die Testumgebung ein eigenes Zertifikat erstellt werden.

Zuerst einen Ordner für das Zertifikat anlegen
```bash
sudo mkdir -p /etc/apache2/ssl
```
Anschließend Zertifikat und privaten Schlüssel erstellen
```bash
sudo openssl req -x509 -nodes -days 365 \
-newkey rsa:2048 \
-keyout /etc/apache2/ssl/nextcloud.key \
-out /etc/apache2/ssl/nextcloud.crt
```

Bei der Erstellung werden verschiedene Informationen abgefragt.

Besonders wichtig ist der Hostname des Servers. Als Common Name wird verwendet
```text
nextcloud.fruehwald.homenet.com
```

Danach befinden sich die benötigten Dateien unter
```text
/etc/apache2/ssl/nextcloud.crt
/etc/apache2/ssl/nextcloud.key
```

Dabei handelt es sich um:
* `nextcloud.crt` → SSL-Zertifikat
* `nextcloud.key` → privater Schlüssel des Servers

Der private Schlüssel sollte nur vom Server selbst verwendet und nicht weitergegeben werden.

#### 28.16.02 HTTPS-VirtualHost für Nextcloud konfigurieren

Die bestehende Apache-Konfiguration für Nextcloud öffnen
```bash
sudo nano /etc/apache2/sites-available/nextcloud.conf
```

Zusätzlich zum bisherigen HTTP-VirtualHost wird ein VirtualHost für HTTPS auf Port `443` eingerichtet
```apache
<VirtualHost *:443>
    ServerName nextcloud.fruehwald.homenet.com
    DocumentRoot /var/www/nextcloud

    <Directory /var/www/nextcloud/>
        Require all granted
        AllowOverride All
        Options FollowSymLinks MultiViews
    </Directory>

    SSLEngine on
    SSLCertificateFile /etc/apache2/ssl/nextcloud.crt
    SSLCertificateKeyFile /etc/apache2/ssl/nextcloud.key

    ErrorLog ${APACHE_LOG_DIR}/nextcloud_ssl_error.log
    CustomLog ${APACHE_LOG_DIR}/nextcloud_ssl_access.log combined
</VirtualHost>
```

Damit verwendet Apache für Verbindungen auf Port `443` das zuvor erstellte Zertifikat.

Apache-Konfiguration anschließend prüfen
```bash
sudo apache2ctl configtest
```
Erwartete Ausgabe
```text
Syntax OK
```
Danach Apache neu starten
```bash
sudo systemctl restart apache2
```
Status kontrollieren
```bash
sudo systemctl status apache2
```

#### 28.16.03 HTTPS-Verbindung testen

Anschließend kann Nextcloud über HTTPS aufgerufen werden
```text
https://nextcloud.fruehwald.homenet.com
```
Alternativ kann die Verbindung zunächst auch über die interne IP-Adresse getestet werden
```text
https://192.168.10.30
```

Da in unserem internen Testnetz ein selbst erstelltes Zertifikat verwendet wird, kann der Browser zunächst eine Zertifikatswarnung anzeigen.  
Das bedeutet nicht automatisch, dass HTTPS nicht funktioniert. Der Browser kennt lediglich die Zertifizierungsstelle des selbst erstellten Zertifikats nicht.  
Nach dem Aufruf wird die Verbindung trotzdem über HTTPS verschlüsselt.

#### 28.16.04 HTTP auf HTTPS umleiten

Damit Benutzer nicht versehentlich weiterhin die unverschlüsselte HTTP-Verbindung verwenden, kann der HTTP-VirtualHost auf HTTPS umgeleitet werden.

Der VirtualHost für Port `80` kann beispielsweise folgendermaßen angepasst werden
```apache
<VirtualHost *:80>
    ServerName nextcloud.fruehwald.homenet.com

    Redirect permanent / https://nextcloud.fruehwald.homenet.com/
</VirtualHost>
```
Danach die Apache-Konfiguration erneut prüfen
```bash
sudo apache2ctl configtest
```
Apache neu laden
```bash
sudo systemctl reload apache2
```
Wird anschließend folgende Adresse geöffnet `http://nextcloud.fruehwald.homenet.com` wird der Browser automatisch auf `https://nextcloud.fruehwald.homenet.com` weitergeleitet.

#### 28.16.05 HTTPS überprüfen

Zur Kontrolle kann geprüft werden, ob Apache auf Port `443` lauscht
```bash
sudo ss -tulpn | grep :443
```
Zusätzlich kann die Verbindung direkt vom Server getestet werden
```bash
curl -k https://nextcloud.fruehwald.homenet.com
```

Mit `-k` wird bei diesem Test akzeptiert, dass es sich um ein selbst erstelltes Zertifikat handelt.

Damit ist HTTPS für den Nextcloud-Server grundsätzlich eingerichtet und die Kommunikation zwischen Browser und Nextcloud wird verschlüsselt übertragen.


### 28.17 Nextcloud-Gesamtsystem testen

Nach Abschluss der Einrichtung wurde das gesamte System nochmals getestet.

Folgende Funktionen wurden überprüft:

- Nextcloud-Weboberfläche ist erreichbar.
- HTTPS-Verbindung funktioniert.
- Apache läuft fehlerfrei.
- MariaDB läuft fehlerfrei.
- Nextcloud kann DC1 erreichen.
- Nextcloud kann DC2 erreichen.
- LDAP-Verbindung zum Samba Active Directory funktioniert.
- Samba-AD-Benutzer werden in Nextcloud erkannt.
- Anmeldung mit einem Domänenbenutzer funktioniert.
- Samba-AD-Gruppen werden von Nextcloud erkannt.
- PHP-Speicher wurde angepasst.
- Nextcloud-Hintergrundjobs werden über Cron ausgeführt.

Damit ist die grundlegende Integration von Debian, Samba Active Directory und Nextcloud erfolgreich eingerichtet.

## Command-Erklärungen
ctl in der Linux-Umgebung bezieht sich auf systemctl, ein Command, das zur Verwaltung vom systemd-Dienste verwendet wird. Es ermöglicht Benutzern Dienste zu steuern, deren Status zu überprüfen und Systeme zu starten oder herunterzufahren. Es erleichtert dadurch die Steuerung von Diensten und deren Verwaltungen.  
systemd-Dienste wiederrum werden über Unit-Dateien verwaltet und können mit systemctl gestartet, gestoppt, aktiviert, deaktiviert und überwacht werden.  
Unit-Dateien enthalten Informationen die für die Verwaltunng von Diensten, Sockets, geräten und anderen Komponenten in einem Linux-System erforderlich sind.

cat wird Verwendet um den Inhalt von Dateien anzuzeigen.

nano wird verwendet um den Inhalt von Dateien zu bearbeiten.

`/etc/network/interfaces` ist unter Debian eine Konfigurationsdatei für Netzwerkschnittstellen. Dort kann festgelegt werden, welche Netzwerkadapter verwendet werden und ob diese ihre IP-Adresse automatisch über DHCP erhalten oder eine feste IP-Adresse verwenden.   
In diesem Projekt wird diese Datei verwendet, um den internen Netzwerkadapter der Debian-Server mit einer statischen IP-Adresse zu konfigurieren. Dadurch behalten beispielsweise DC1, DC2 und der Nextcloud-Server dauerhaft ihre festgelegten IP-Adressen und können zuverlässig miteinander kommunizieren.
* Beispiel:
```bash
sudo nano /etc/network/interfaces
``` 
