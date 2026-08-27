# Linux zentrale Benutzerverwaltung 
> Erstellt von Lilas Dahdal und Alexander Wöhrer
### Projektidee
Eine zentrale Benutzerverwaltung soll mittels Linux Debian Servern und Samba Tools erstellt werden. Es sollen 2 ADDCs eingerichtet werden, welche mit einem Debian Nextcloud server verbunden werden sollen.
test
# Inhaltsverzeichnis

* [Netzwerkplanung](#netzwerkplanung)

* [1. Einrichten des ersten Samba-Domain-Controller](#1-einrichten-des-ersten-samba-domain-controller)

  * [1.1 sudo einrichten](#11-sudo-einrichten)
  * [1.2 Netzwerkkonfiguration](#12-netzwerkkonfiguration)
  * [1.3 Hosts-Datei konfigurieren](#13-hosts-datei-konfigurieren)
  * [1.4 Samba Active Directory installieren](#14-samba-active-directory-installieren)
  * [1.5 Samba-Domäne provisionieren](#15-samba-domäne-provisionieren)
  * [1.6 Samba auf internes Netzwerk beschränken](#16-samba-auf-internes-netzwerk-beschränken)
  * [1.7 Kerberos konfigurieren](#17-kerberos-konfigurieren)
  * [1.8 Samba-Dienst starten](#18-samba-dienst-starten)
  * [1.9 DNS als Domain Controller konfigurieren](#19-dns-als-domain-controller-konfigurieren)
  * [1.10 DNS überprüfen](#110-dns-überprüfen)
  * [1.11 DNS-Einträge mit samba-tool prüfen](#111-dns-einträge-mit-samba-tool-prüfen)
  * [1.12 Active-Directory-Datenbank prüfen](#112-active-directory-datenbank-prüfen)
  * [1.13 Kerberos testen](#113-kerberos-testen)
  * [1.14 Zeit synchronisieren](#114-zeit-synchronisieren)
  * [1.15 Benutzer erstellen](#115-benutzer-erstellen)
  * [1.16 Gruppen erstellen](#116-gruppen-erstellen)
  * [1.17 Administratorrechte vergeben](#117-administratorrechte-vergeben)
  * [1.18 Organisationseinheiten](#118-organisationseinheiten)
  * [1.19 Windows 11 konfigurieren](#119-windows-11-konfigurieren)
  * [1.20 Windows-Netzwerk testen](#120-windows-netzwerk-testen)
  * [1.21 Domain Controller von Windows finden](#121-domain-controller-von-windows-finden)
  * [1.22 Windows 11 der Domäne hinzufügen](#122-windows-11-der-domäne-hinzufügen)
  * [1.23 Anmeldung mit Domänenbenutzer testen](#123-anmeldung-mit-domänenbenutzer-testen)
  * [1.24 AD-Objekte kontrollieren](#124-ad-objekte-kontrollieren)
  * [1.25 Samba-Freigaben](#125-samba-freigaben)

* [2. Einrichten eines zweiten Samba-Domain-Controllers](#2-einrichten-eines-zweiten-samba-domain-controllers)
  * [2.1 Debian auf DC2 vorbereiten](#21-debian-auf-dc2-vorbereiten)
  * [2.2 Hostname von DC2 setzen](#22-hostname-von-dc2-setzen)
  * [2.3 Netzwerk konfigurieren](#23-netzwerk-konfigurieren)
  * [2.4 Prüfen, ob DC2 DC1 per Namen findet](#24-prüfen-ob-dc2-dc1-per-namen-findet)
  * [2.5 Kerberos testen](#25-kerberos-testen)
  * [2.6 Alte Samba-Konfiguration entfernen bzw. sichern](#26-alte-samba-konfiguration-entfernen-bzw-sichern)
  * [2.7 Join](#27-join)
  * [2.8 Kerberos-Konfiguration übernehmen](#28-kerberos-konfiguration-übernehmen)
  * [2.9 Samba-AD-Dienst aktivieren](#29-samba-ad-dienst-aktivieren)
  * [2.10 Prüfen, ob DC2 wirklich ein Domain Controller ist](#210-prüfen-ob-dc2-wirklich-ein-domain-controller-ist)
  * [2.11 Replikation prüfen](#211-replikation-prüfen)
  * [2.12 Funktionstest mit Benutzer](#212-funktionstest-mit-benutzer)

* [3. Installation des Nextcloud Servers](#3-installation-des-nextcloud-servers)
  * [3.1 Netzwerk unter Debian konfigurieren](#31-netzwerk-unter-debian-konfigurieren)
  * [3.2 Apache, MariaDB, PHP und benötigte Pakete installieren](#32-apache-mariadb-php-und-benötigte-pakete-installieren)
  * [3.3 MariaDB für Nextcloud konfigurieren](#33-mariadb-für-nextcloud-konfigurieren)
  * [3.4 Nextcloud herunterladen und installieren](#34-nextcloud-herunterladen-und-installieren)
  * [3.5 Apache-Webserver für Nextcloud konfigurieren](#35-apache-webserver-für-nextcloud-konfigurieren)
  * [3.6 Nextcloud über den Browser fertig einrichten](#36-nextcloud-über-den-browser-fertig-einrichten)
  * [3.7 Nextcloud-Verzeichnis und OCC-Befehle](#37-nextcloud-verzeichnis-und-occ-befehle)
  * [3.8 LDAP-App in Nextcloud aktivieren](#38-ldap-app-in-nextcloud-aktivieren)
  * [3.9 Eigenes LDAP-Dienstkonto verwenden](#39-eigenes-ldap-dienstkonto-verwenden)
  * [3.10 LDAP-Verbindung zu Samba in Nextcloud eintragen](#310-ldap-verbindung-zu-samba-in-nextcloud-eintragen)
  * [3.11 LDAP-Verbindung unabhängig von Nextcloud testen](#311-ldap-verbindung-unabhängig-von-nextcloud-testen)
    * [3.11.1 TLS-/StartTLS-Verbindung prüfen](#3111-tls-starttls-verbindung-prüfen)
    * [3.11.2 Fehlerbehebung bei leeren Benutzer- oder Objektklassen](#3112-fehlerbehebung-bei-leeren-benutzer--oder-objektklassen)
  * [3.12 LDAP-Benutzer und Gruppen in Nextcloud filtern](#312-ldap-benutzer-und-gruppen-in-nextcloud-filtern)
    * [3.12.1 Vorhandene Samba-AD-Gruppen in Nextcloud prüfen](#3121-vorhandene-samba-ad-gruppen-in-nextcloud-prüfen)
    * [3.12.2 Anmeldung mit einem Samba-AD-Benutzer testen](#3122-anmeldung-mit-einem-samba-ad-benutzer-testen)
    * [3.12.3 DC2 für LDAP-Ausfallsicherheit prüfen](#3123-dc2-für-ldap-ausfallsicherheit-prüfen)
  * [3.13 Aktuelle LDAP-Konfiguration mit OCC prüfen](#313-aktuelle-ldap-konfiguration-mit-occ-prüfen)
  * [3.14 PHP-Speicher für Nextcloud anpassen](#314-php-speicher-für-nextcloud-anpassen)
  * [3.15 Nextcloud-Hintergrundjobs auf Cron umstellen](#315-nextcloud-hintergrundjobs-auf-cron-umstellen)
  * [3.16 HTTPS für Nextcloud einrichten](#316-https-für-nextcloud-einrichten)
    * [3.16.1 SSL-Zertifikat erstellen](#3161-ssl-zertifikat-erstellen)
    * [3.16.2 HTTPS-VirtualHost für Nextcloud konfigurieren](#3162-https-virtualhost-für-nextcloud-konfigurieren)
    * [3.16.3 HTTPS-Verbindung testen](#3163-https-verbindung-testen)
    * [3.16.4 HTTP auf HTTPS umleiten](#3164-http-auf-https-umleiten)
    * [3.16.5 HTTPS überprüfen](#3165-https-überprüfen)
  * [3.17 Nextcloud-Gesamtsystem testen](#317-nextcloud-gesamtsystem-testen)

* [4. DC-Migration (FALLS ÜBERHAUPT NÖTIG)](#4-dc-migration)
  * [4.1 Einstellungen analysieren](#41-einstellungen-analysieren)
  * [4.2 Samba-Version feststellen](#42-samba-version-feststellen)
  * [4.3 Samba-Konfiguration](#43-samba-konfiguration)
  * [4.4 Domäne feststellen](#44-domäne-feststellen)
  * [4.5 Benutzer prüfen](#45-benutzer-prüfen)
  * [4.6 Gruppen prüfen](#46-gruppen-prüfen)
  * [4.7 DNS ganz genau überprüfen](#47-dns-ganz-genau-überprüfen)
  * [4.8 Kerberos testen](#48-kerberos-testen)
  * [4.9 Replikation zwischen DC1 und DC2 prüfen](#49-replikation-zwischen-dc1-und-dc2-prüfen)
  * [4.10 FSMO-Rolle prüfen](#410-fsmo-rolle-prüfen)
  * [4.11 AD-Datenbank überprüfen](#411-ad-datenbank-überprüfen)
  * [4.12 Samba-Dienste prüfen](#412-samba-dienste-prüfen)
  * [4.13 DC3 Installation](#413-dc3-installation)
    * [4.13.1 Installation Debian Server](#4131-installation-debian-server)
    * [4.13.2 sudo einrichten](#4132-sudo-einrichten)
    * [4.13.3 Netzwerkkonfiguration](#4133-netzwerkkonfiguration)
    * [4.13.4 Hosts-Datei konfigurieren](#4134-hosts-datei-konfigurieren)
    * [4.13.5 Samba Active Directory installieren](#4135-samba-active-directory-installieren)
    * [4.13.6 Samba-Standardkonfiguration (smb.conf) sichern](#4136-samba-standardkonfiguration-smbconf-sichern)
    * [4.13.7 DNS-Client für den Join konfigurieren](#4137-dns-client-für-den-join-konfigurieren)
  * [4.14 DNS auf DC3 testen](#414-dns-auf-dc3-testen)
  * [4.15 Uhrzeit prüfen](#415-uhrzeit-prüfen)
  * [4.16 jetzt DC3 joinen](#416-jetzt-dc3-joinen)
  * [4.17 weitere Konfiguration von DC3](#417-weitere-konfiguration-von-dc3)
    * [4.17.1 Samba auf internes Netzwerk beschränken](#4171-samba-auf-internes-netzwerk-beschränken)
    * [4.17.2 Kerberos konfigurieren](#4172-kerberos-konfigurieren)
    * [4.17.3 Samba-Dienst starten](#4173-samba-dienst-starten)
  * [4.18 Replikation prüfen](#418-replikation-prüfen)
  * [4.19 Kontrollieren, ob Benutzer angekommen sind](#419-kontrollieren-ob-benutzer-angekommen-sind)
  * [4.20 Praktischer Replikationstest](#420-praktischer-replikationstest)
  * [4.21 DNS auf DC3 prüfen](#421-dns-auf-dc3-prüfen)
  * [4.22 FSMO noch einmal kontrollieren](#422-fsmo-noch-einmal-kontrollieren)
  * [4.23 DC1 demoten/außer Betrieb nehmen](#423-dc1-demotenaußer-betrieb-nehmen)

* [5. Automatisierter Benutzerimport aus Oracle](#5-automatisierter-benutzerimport-aus-oracle)
  * [5.1 Benötigte Daten aus Oracle](#51-benötigte-daten-aus-oracle)
  * [5.2 Migrationsverzeichnis vorbereiten](#52-migrationsverzeichnis-vorbereiten)
  * [5.3 Gruppen importieren](#53-gruppen-importieren)
  * [5.4 Benutzer mit Passwörtern importieren](#54-benutzer-mit-passwörtern-importieren)
  * [5.5 Gruppenmitgliedschaften importieren](#55-gruppenmitgliedschaften-importieren)
  * [5.6 Import kontrollieren](#56-import-kontrollieren)
  * [5.7 Post-Migration](#57-post-migration)

* [6. Windows-Verwaltung, Gruppenrichtlinien und Sicherheit](#6-windows-verwaltung-gruppenrichtlinien-und-sicherheit)
  * [6.1 grafische Verwaltung über Windows-11](#61-grafische-verwaltung-über-windows-11)
  * [6.2 RSAT-Verwaltung unter Windows vervollständigen](#62-rsat-verwaltung-unter-windows-vervollständigen)
  * [6.3 Gruppenwechsel und Dateizugriff testen](#63-gruppenwechsel-und-dateizugriff-testen)
    * [6.3.1 Gruppenmitgliedschaft über RSAT ändern](#631-gruppenmitgliedschaft-über-rsat-ändern)
    * [6.3.2 Neue Gruppenmitgliedschaft am Client übernehmen](#632-neue-gruppenmitgliedschaft-am-client-übernehmen)
    * [6.3.3 Dateizugriff über Samba-Freigaben kontrollieren](#633-dateizugriff-über-samba-freigaben-kontrollieren)
  * [6.4 Gruppenrichtlinienverwaltung mit RSAT](#64-gruppenrichtlinienverwaltung-mit-rsat)
    * [6.4.1 Gruppenrichtlinienverwaltung öffnen](#641-gruppenrichtlinienverwaltung-öffnen)
    * [6.4.2 Aufbau einer Gruppenrichtlinie](#642-aufbau-einer-gruppenrichtlinie)
  * [6.5 SYSVOL und Fehlerbehebung](#65-sysvol-und-fehlerbehebung)
    * [6.5.1 Aufgabe von SYSVOL](#651-aufgabe-von-sysvol)
    * [6.5.2 Aufgetretenes SYSVOL-Problem](#652-aufgetretenes-sysvol-problem)
    * [6.5.3 SYSVOL vom Windows-Client prüfen](#653-sysvol-vom-windows-client-prüfen)
    * [6.5.4 SYSVOL auf dem Samba-DC prüfen](#654-sysvol-auf-dem-samba-dc-prüfen)
    * [6.5.5 SYSVOL-ACLs mit samba-tool reparieren](#655-sysvol-acls-mit-samba-tool-reparieren)
    * [6.5.6 Wichtiger Hinweis bei mehreren Samba-DCs](#656-wichtiger-hinweis-bei-mehreren-samba-dcs)
  * [6.6 Gruppenrichtlinie erstellen und verknüpfen](#66-gruppenrichtlinie-erstellen-und-verknüpfen)
    * [6.6.1 GPO erstellen](#661-gpo-erstellen)
    * [6.6.2 GPO auf dem Windows-Client aktualisieren](#662-gpo-auf-dem-windows-client-aktualisieren)
  * [6.7 AppLocker über Gruppenrichtlinien konfigurieren](#67-applocker-über-gruppenrichtlinien-konfigurieren)
    * [6.7.1 AppLocker in der GPO finden](#671-applocker-in-der-gpo-finden)
    * [6.7.2 Ausführbare Regeln](#672-ausführbare-regeln)
    * [6.7.3 Standardregeln erstellen](#673-standardregeln-erstellen)
    * [6.7.4 Ausnahmen richtig verwenden](#674-ausnahmen-richtig-verwenden)
    * [6.7.5 Application-Identity-Dienst](#675-application-identity-dienst)
  * [6.8 AppLocker-Komplikationen und Fehlerbehebung](#68-applocker-komplikationen-und-fehlerbehebung)
    * [6.8.1 PowerShell normal bzw. als Administrator](#681-powershell-normal-bzw-als-administrator)
    * [6.8.2 Fehlerhafte Ausnahmen in den Standardregeln](#682-fehlerhafte-ausnahmen-in-den-standardregeln)
     * [6.8.3 AppLocker-Regeln korrigieren](#683-applocker-regeln-korrigieren)
  * [6.9 Gruppenrichtlinien und AppLocker testen](#69-gruppenrichtlinien-und-applocker-testen)
    * [6.9.1 Richtlinien aktualisieren](#691-richtlinien-aktualisieren)
    * [6.9.2 Angewendete Gruppenrichtlinien prüfen](#692-angewendete-gruppenrichtlinien-prüfen)
    * [6.9.3 Benutzergruppen kontrollieren](#693-benutzergruppen-kontrollieren)


## Netzwerkplanung 

**Domainname:** `fruehwald.homenet.com`   
**NetBIOS-Domäne:** `FRUEHWALD`  
**Debian Domain Controller:**
* Hostname: `dc1`
* IP: `192.168.10.10`   

für die Einrichtung eines zweiten Domain Controller:
* Hostname: `dc2`
* IP: `192.168.10.11`

für die Einrichtung eines dritten Domain Controller (FALLS ÜBERHAUPT NÖTIG):
* Hostname: `dc3`
* IP: `192.168.10.12`

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

## 1. Einrichten des ersten Samba-Domain-Controller

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

### 1.1. sudo einrichten

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
### 1.2. Netzwerkkonfiguration

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
### 1.3. Hosts-Datei konfigurieren

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
### 1.4. Samba Active Directory installieren

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
### 1.5. Samba-Domäne provisionieren

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

### 1.6. Samba auf internes Netzwerk beschränken
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
### 1.7. Kerberos konfigurieren
Die von Samba erzeugte Kerberos-Konfiguration wird übernommen
``` Bash
sudo cp -f /var/lib/samba/private/krb5.conf /etc/krb5.conf
```
Kontrolle
``` Bash
cat /etc/krb5.conf
```
Wichtig: Realm sollte `FRUEHWALD.HOMENET.COM` sein
### 1.8. Samba-Dienst starten
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

### 1.9. DNS als Domain Controller konfigurieren 
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
nameserver 192.168.10.10 
search fruehwald.homenet.com
```
früher war nameserver: 127.0.0.1

=> alle verwendeten DC-IPs sollten eingetragen werden
Kontrolle
``` Bash
cat /etc/resolv.conf
```

### 1.10. DNS überprüfen
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

### 1.11. DNS-Einträge mit samba-tool prüfen
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

### 1.12. Active-Directory-Datenbank prüfen
``` Bash
sudo samba-tool dbcheck
```
Erwartete Ausgabe
``` Bash
Checked 282 objects (0 errors)
```

### 1.13. Kerberos testen
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

### 1.14. Zeit synchronisieren
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
Zeitzone sollte `Europe/Vienna` sein

### 1.15. Benutzer erstellen
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

### 1.16. Gruppen erstellen
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

### 1.17. Administratorrechte vergeben
normaler Benutzer zum Mitglied der Gruppe `Domain Admins` machen
``` Bash
sudo samba-tool group addmembers "Domain Admins" max.mueller
```

### 1.18. Organisationseinheiten
Anstatt alle Benutzer unter Users zu lassen, könnten OUs erstellt werden.  
Beispiel
``` YAML
fruehwald.homenet.com
│
├── OU=Mitarbeiter
│   ├── OU=Aerzte
│   ├── OU=XYZ
│   └── OU=IT
│
├── OU=Computer
│   ├── OU=Clients
│   ├── OU=XYZ
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

### 1.19. Windows 11 konfigurieren
Windows-Client verwendet:
``` Bash
IP-Adresse:    192.168.10.21
Subnetzmaske:  255.255.255.0
DNS-Server:    192.168.10.10
```
Der interne Netzwerkadapter besizt kein Gateway.   

### 1.20. Windows-Netzwerk testen
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

### 1.21. Domain Controller von Windows finden
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

### 1.22. Windows 11 der Domäne hinzufügen
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

### 1.23. Anmeldung mit Domänenbenutzer testen

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

### 1.24. AD-Objekte kontrollieren
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

### 1.25. Samba-Freigaben
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

## 2. Einrichten eines zweiten Samba-Domain-Controllers
### 2.1 Debian auf DC2 vorbereiten
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

### 2.2 Hostname von DC2 setzen
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
192.168.10.11   dc2.fruehwald.homenet.com dc2
192.168.10.10   dc1.fruehwald.homenet.com dc1
```
eventuell vorhandener Eintrag `127.0.1.1 dc2` sollte entfernt werden

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

### 2.3 Netzwerk konfigurieren
Datei öffnen
```Bash
sudo nano /etc/network/interfaces
```
* Beispiel:
``` YAML
allow-hotplug enp0s3
iface enp0s3 inet dhcp

iface enp0s3 inet6 auto

auto enp0s8 => bekommt statische IP
iface enp0s8 inet static
    address 192.168.10.11/24
```
Notiz: allow-hotplug enp0s3 => bleibt über DHCP konfiguriert

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
### 2.4 Prüfen, ob DC2 DC1 per Namen findet
```Bash
ping dc1.fruehwald.homenet.com
```
Danach
```Bash
nslookup dc1.fruehwald.homenet.com
```

### 2.5 Kerberos testen
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
### 2.6 Alte Samba-Konfiguration entfernen bzw. sichern
Falls `/etc/samba/smb.conf` existiert
```Bash
sudo mv /etc/samba/smb.conf /etc/samba/smb.conf.backup
```
Wichtig, weil DC2 beim Join seine passende Domain-Controller-Konfiguration bekommt

### 2.7 Join
Auf DC2
```Bash
sudo samba-tool domain join fruehwald.homenet.com DC -U Administrator
```
Dann Passwort eingeben

Hier ist das `DC` wichtig: Es bedeutet `Join der bestehenden Domäne als Domain Controller`. 

### 2.8 Kerberos-Konfiguration übernehmen
Nach erfolgreichem Join 
```Bash
sudo cp /var/lib/samba/private/krb5.conf /etc/krb5.conf
```
### 2.9 Samba-AD-Dienst aktivieren
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
### 2.10 Prüfen, ob DC2 wirklich ein Domain Controller ist
```Bash
sudo testparm
```
Erwartete (WICHTIG) Ausgabe
```Bash
server role = active directory domain controller
```
### 2.11 Replikation prüfen
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

### 2.12 Funktionstest mit Benutzer
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



## 3. Installation des Nextcloud Servers
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

### 3.1 Netzwerk unter Debian konfigurieren 
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


### 3.2 Apache, MariaDB, PHP und benötigte Pakete installieren
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

### 3.3 MariaDB für Nextcloud konfigurieren
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

### 3.4 Nextcloud herunterladen und installieren
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

### 3.5 Apache-Webserver für Nextcloud konfigurieren
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

### 3.6 Nextcloud über den Browser fertig einrichten
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

Nach Abschluss der Installation kann der Nextcloud-Status über `occ` geprüft werden.

### 3.7 Nextcloud-Verzeichnis und OCC-Befehle
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

### 3.8 LDAP-App in Nextcloud aktivieren
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

### 3.9 Eigenes LDAP-Dienstkonto verwenden
Für die LDAP-Anbindung sollte Nextcloud nicht dauerhaft das Domänen-Administratorkonto verwenden. Stattdessen wurde ein eigenes LDAP-Dienstkonto angelegt.

Verwendetes Konto
```text
CN=nextcloud.ldap,CN=Users,DC=fruehwald,DC=homenet,DC=com
```

Dieses Konto wird ausschließlich dafür verwendet, dass Nextcloud Benutzer und Gruppen aus dem Samba Active Directory abfragen kann.  
Dadurch muss das Administrator-Konto nicht in der Nextcloud-LDAP-Konfiguration hinterlegt werden.

### 3.10 LDAP-Verbindung zu Samba in Nextcloud eintragen
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

### 3.11 LDAP-Verbindung unabhängig von Nextcloud testen
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

#### 3.11.1 TLS-/StartTLS-Verbindung prüfen
Da die LDAP-Verbindung zunächst trotz erreichbarem LDAP-Port Probleme verursachte, wurde auch der TLS-/StartTLS-Handshake geprüft.
```bash
openssl s_client -connect dc1.fruehwald.homenet.com:389 -starttls ldap -showcerts
```

Dabei ist wichtig, dass der verwendete Hostname zum Zertifikat des Domain Controllers passt.

Die LDAP-Anbindung konnte anschließend erfolgreich hergestellt werden.

#### 3.11.2 Fehlerbehebung bei leeren Benutzer- oder Objektklassen
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

### 3.12 LDAP-Benutzer und Gruppen in Nextcloud filtern
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

### 3.12.1 Vorhandene Samba-AD-Gruppen in Nextcloud prüfen
In Nextcloud
1. Mit dem Nextcloud-Administratorkonto anmelden.
2. `Verwaltungseinstellungen` öffnen.
3. `LDAP/AD-Integration` auswählen.
4. Die bestehende LDAP-Konfiguration öffnen.
5. Zum Bereich `Gruppen` wechseln.
6. Prüfen, ob vorhandene Samba-AD-Gruppen gefunden werden.

Damit wird geprüft, ob Nextcloud neben den Benutzern auch die Gruppenstruktur des Samba Active Directory korrekt auslesen kann.

#### 3.12.2 Anmeldung mit einem Samba-AD-Benutzer testen
1. Vom lokalen Nextcloud-Administratorkonto abmelden.
2. Einen im Samba Active Directory vorhandenen Domänenbenutzer verwenden.
3. Das Passwort dieses AD-Benutzers eingeben.
4. Anmeldung durchführen.

Die Anmeldung mit einem Samba-AD-Benutzer war erfolgreich. Damit ist bestätigt, dass Nextcloud die Benutzerkonten aus dem Samba Active Directory verwendet.

#### 3.12.3 DC2 für LDAP-Ausfallsicherheit prüfen
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

### 3.13 Aktuelle LDAP-Konfiguration mit OCC prüfen
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

### 3.14 PHP-Speicher für Nextcloud anpassen
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

### 3.15 Nextcloud-Hintergrundjobs auf Cron umstellen
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

### 3.16 HTTPS für Nextcloud einrichten
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

#### 3.16.1 SSL-Zertifikat erstellen
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

#### 3.16.2 HTTPS-VirtualHost für Nextcloud konfigurieren
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

#### 3.16.3 HTTPS-Verbindung testen
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

#### 3.16.4 HTTP auf HTTPS umleiten
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

#### 3.16.5 HTTPS überprüfen
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


### 3.17 Nextcloud-Gesamtsystem testen
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

## 4. DC-Migration (FALLS ÜBERHAUPT NÖTIG)
### 4.1 Einstellungen analysieren
Hostname
```bash
hostname
hostname -f
```
IP-Adresse
```bash
ip addr
ip -br addr
```
Routing/Gateway
```bash
ip route
```
DNS-Konfiguration
```bash
cat /etc/resolv.conf
```
Falls `systemd-resolved` eingesetzt wird
```bash
resolvectl status
```
Damit hat man schon:
```
Hostname
FQDN
IP-Adresse
Gateway
DNS-Server
```

### 4.2 Samba-Version feststellen
```bash
samba --version
samba-tool --version
```
=> wichtig für die Migration, insbesondere wenn eine ältere Samba-Version verwendet wird.

### 4.3 Samba-Konfiguration
```Bash
sudo testparm
```
etwas in dieser Richtung sollte gesehen werden
```
server role = active directory domain controller
realm = FRUEHWALD.HOMENET.COM
workgroup = FRUEHWALD
```
Samba-Konfigurationsdatei
```Bash
sudo cat /etc/samba/smb.conf
```

### 4.4 Domäne feststellen
```Bash
sudo samba-tool domain level show
```
liefer beispielsweeise
```Bash
Domain function level
Forest function level
Lowest function level of a DC
```
Domaininformationen 
```Bash
sudo samba-tool domain info 192.168.10.12
```
### 4.5 Benutzer prüfen
```Bash
sudo samba-tool user list
```
bestimmte Benutzerobjekte
```Bash
sudo samba-tool user show <Benutzername>
```

### 4.6 Gruppen prüfen 
```Bash
sudo samba-tool group list
```
Mitglieder einer bestimmten Gruppe
```Bash
sudo samba-tool group listmembers "<Gruppenname>"
```

### 4.7 DNS ganz genau überprüfen
Wichtig für die Aufstellung von DC3!!!

```Bash
nslookup dc3.fruehwald.homenet.com
```
Besonders wichtig für Active Directory
```Bash
dig _ldap._tcp.fruehwald.homenet.com SRV
dig _kerberos._tcp.fruehwald.homenet.com SRV
```
=> Damit überprüft man, ob die Domain Controller über die AD-DNS.Service-Records gefunden werden.

### 4.8 Kerberos testen 
```Bash
kinit Administrator
```
Passwort eingeben und dann
```Bash
klist
```
Man sollte ein gültiges Kerberos-Ticket sehen, ungefähr so:
```Bash
Default principal: Administrator@FRUEHWALD.HOMENET.COM
```
=> Falls Fehler auftreten sollten, **DC3 noch nicht joinen**

### 4.9 Replikation zwischen DC1 und DC2 prüfen
Wichtigster Test!

Auf DC1 und DC2
```Bash
sudo samba-tool drs showrepl
```
=> zeigt den Status der AD-Replikation eines Domain Controllers

testen, ob DC1 DRS mit DC2 sprechen kann (auf DC1 und DC2) 
```Bash
sudo samba-tool drs bind <vollständiger Hostname>
```

### 4.10 FSMO-Rolle prüfen
Wichtig vor dem Entfernen alter DCs!
```Bash
sudo samba-tool fsmo show
```
=> Besitzer der FSMO-Rollen anzeigen. Falls beispielsweise DC1 diese Rolle besitzt, muss vor dem Löschen den Rolle an einem verbleibenden DC übertragen werden. (Auf DC3 z.B.)

### 4.11 AD-Datenbank überprüfen 
```Bash
sudo samba-tool dbcheck
```
=> DB auf Fehler überprüfen 
Hier zunächst ohne Reperaturoptionen arbeiten

### 4.12 Samba-Dienste prüfen
Je nach Installation
```Bash
sudo systemctl status samba-ad-dc
sudo systemctl status samba
```
=> Wen es ein AD DC ist, sollte man einen laufenden Samba-AD-DC-Dienst sehen 

### 4.13 DC3 Installation
Zunächst richtet man DC3 als normalen Debian-Server ein.  
Vor dem Join sollte DC3 selbst nicht als DNS verwendet werden!

#### 4.13.1 Installation Debian Server

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
dc3
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
dc3
dc3.fruehwald.homenet.com
```

#### 4.13.2 sudo einrichten
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
#### 4.13.3 Netzwerkkonfiguration
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
    address 192.168.10.12/24
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
enp0s8   UP   192.168.10.12/24
```
#### 4.13.4 Hosts-Datei konfigurieren

Datei öffnen
```Bash
sudo nano /etc/hosts
```
wichtige Einträge:
```Bash
127.0.0.1       localhost
192.168.10.12   dc3.fruehwald.homenet.com dc3
```
eventuell vorhandener Eintrag `127.0.1.1 dc3` sollte entfernt werden

Kontrolle
```Bash
hostname -f
```
Erwartete Ausgabe
```Bash
dc3.fruehwald.homenet.com
```
#### 4.13.5 Samba Active Directory installieren
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

#### 4.13.6 Samba-Standardkonfiguration (smb.conf) sichern
zuerst kontrollieren, ob die Datei vorhanden ist
```Bash
ls -l /etc/samba/smb.conf
```

falls sie existiert
```Bash
sudo mv /etc/samba/smb.conf /etc/samba/smb.conf.bak
```
#### 4.13.7 DNS-Client für den Join konfigurieren
Vor dem Join darf DC3 nicht sich selbst und auch keine anderen DNS als DN für die AD-Domäne verwenden

Kontrolle
```Bash
cat /etc/resolv.conf
```

Da beim Debian-System dhcpcd die Datei automatisch überschreibt 
```Bash
sudo nano /etc/dhcpcd.conf
```
ganz unten ergänzen
```Bash
nohook resolv.conf
```
Konfiguration neu einlesen
```Bash
sudo dhcpcd -n enp0s3
```
Dann
```Bash
sudo nano /etc/resolv.conf
```
Inhalt
```Bash
search fruehwald.homenet.com
nameserver 192.168.10.10
nameserver 192.168.10.11
```
Dadurch verwendet DC3 vor dem join die anderen DCs als AD-DNS-Server   
Kontrolle
```Bash
cat /etc/resolv.conf
```
### 4.14 DNS auf DC3 testen 
noch bevor Samba gejoint wird

Netzwerkverbindung prüfen
```Bash
ping dc1.fruehwald.homenet.com
ping dc2.fruehwald.homenet.com
```
bzw.
```Bash
ping -c 4 192.168.10.10
ping -c 4 192.168.10.11
```
Danach Namensauflösung
```Bash
host dc1.fruehwald.homenet.com
host dc2.fruehwald.homenet.com
```
erwartete Ausgabe
```Bash
dc1.fruehwald.homenet.com => 192.168.10.10
dc2.fruehwald.homenet.com => 192.168.10.11
```
LDAP-Service-Records prüfen
```Bash
dig _ldap._tcp.fruehwald.homenet.com SRV
```
Dabei solltn DC1/DC2 auf Port `389` gefunden werden

Kerberos-Service-Records prüfen
```Bash
dig _kerberos._tcp.fruehwald.homenet.com SRV
```
Dabei solltn DC1/DC2 auf Port `88` gefunden werden

Zusätzlich den für die Domain-Controller-Suche wichtigen DC-Locator prüfen 
```Bash
host -t SRV _ldap._tcp.dc._msdcs.fruehwald.homenet.com
```
Dabei solltn DC1/DC2 auf Port `389` gefunden werden

Wenn unklar ist, welchen DNS-Server DC3 verwendet 
```Bash
dig dc1.fruehwald.homenet.com
```
Bei `SERVER:` sollte einer der internen AD-DNS-Server erscheinen   
`SERVER: 192.168.10.10#53`

direkte Prüfung von DC1 als DNS
```Bash
dig @192.168.10.10 _ldap._tcp.dc._msdcs.fruehwald.homenet.com SRV
```

### 4.15 Uhrzeit prüfen
Kerberos reagiert empfindlich auf größere Zeitabweichungen.
```Bash
timedatectl
```
auf DC1 und DC2
```Bash
date
```

### 4.16 jetzt DC3 joinen
DC3 wird mit der vorhandenen Domäne gejoint
```Bash
sudo samba-tool domain join fruehwald.homenet.com DC -U "FRUEHWALD\Administrator"
```
Dann Passwort eingeben
Dadurch gehören alle drei zu selben Domäne

### 4.17 weitere Konfiguration von DC3
#### 4.17.1 Samba auf internes Netzwerk beschränken
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
        netbios name = DC3
        realm = FRUEHWALD.HOMENET.COM
        server role = active directory domain controller
        workgroup = FRUEHWALD
```
Konfiguration prüfen
``` Bash
sudo testparm -s
```
Wichtig: `Server role: ROLE_ACTIVE_DIRECTORY_DC`

#### 4.17.2 Kerberos konfigurieren
Die von Samba erzeugte Kerberos-Konfiguration wird übernommen
``` Bash
sudo cp -f /var/lib/samba/private/krb5.conf /etc/krb5.conf
```
Kontrolle
``` Bash
cat /etc/krb5.conf
```
Wichtig: Realm sollte `FRUEHWALD.HOMENET.COM` sein

#### 4.17.3 Samba-Dienst starten
Nach dem erfolgreichen Join zunächst die Samba-Konfiguration prüfen
``` Bash
sudo testparm -s
```
Wichtig ist `server role = ROLE_ACTIVE_DIRECTORY_DC`

prüfen, ob klassische Samba-Dienste separat laufen
``` Bash
sudo systemctl status smbd nmbd winbind --no-pager
```
=> sollten auf dem AD-DC nicht separat laufen

Falls ja, dann
``` Bash
sudo systemctl disable --now smbd nmbd winbind
```
vom versehentlichen Starten sperren
``` Bash
sudo systemctl mask smbd nmbd winbind
```
Danach sicherstellen, dass `samba-ad-dc` selbst nicht maskiert ist 
``` Bash
sudo systemctl unmask samba-ad-dc
```
Dann AD-DC-Dienst aktivieren und starten
``` Bash
sudo systemctl enable --now samba-ad-dc
```
Kontrolle
``` Bash
sudo systemctl is-active samba-ad-dc
```
Erwartete Ausgabe
``` Bash
active
```
Zusätzlich
``` Bash
sudo systemctl status samba-ad-dc --no-pager
```
Replikation prüfen
``` Bash
sudo samba-tool drs showrepl
```
Wichtige erwartete Ausgaben sind
``` Bash
Last attempt ... was successful
0 consecutive failure(s)
```

### 4.18 Replikation prüfen
auf DC3
```Bash
sudo samba-tool drs showrepl
```
Dann 
```Bash
sudo samba-tool drs bind dc1.fruehwald.homenet.com
sudo samba-tool drs bind dc2.fruehwald.homenet.com
```
Dann auf DC1
```Bash
sudo samba-tool drs bind dc3.fruehwald.homenet.com
```
Dann auf DC2
```Bash
sudo samba-tool drs bind dc3.fruehwald.homenet.com
```

### 4.19 Kontrollieren, ob Benutzer angekommen sind
Auf DC1
```Bash
sudo samba-tool user list
```
Auf DC3
```Bash
sudo samba-tool user list
```
auch bei Gruppen 
=> sollte dieselben sein 
Auf DC1
```Bash
sudo samba-tool group list
```
Auf DC3
```Bash
sudo samba-tool group list
```

### 4.20 Praktischer Replikationstest
Auf DC1 einen temporären Testbenutzer anlegen 
```Bash
sudo samba-tool user create migrationstest
```
Dann auf DC3
```Bash
sudo samba-tool user list | grep migrationstest
```
=> wenn der Testbenutzer erscheint, dann hat die AD-Replikation funktioniert

Dann auf DC3 löschen 
```Bash
sudo samba-tool user delete migrationstest
```
auf DC1 kontrollieren
```Bash
sudo samba-tool user list
```
bzw.
```Bash
sudo samba-tool user list | grep migrationstest => sollte keine Ausgabe kommen
```
=> wenn der gelöschte Benutzer auch auf DC1 nicht mehr angezeigt wird, dann funktioniert die Replikation in beiden Richtungen

### 4.21 DNS auf DC3 prüfen
nach dem Join
```Bash
nslookup dc3.fruehwald.homenet.com
```
und
```Bash
dig _ldap._tcp.fruehwald.homenet.com SRV
```
=> DC3 sollte als Domain Controller auffindbar sein

### 4.22 FSMO noch einmal kontrollieren
```Bash
sudo samba-tool fsmo show
```
=> Kontrollieren, ob diese Rolle verhanden ist und wer diese Rolle trägt

Vorher muss sie auf einen verbleibenden DC übertragen werden durch.   
Auf DC3
```Bash
sudo samba-tool fsmo transfer --role=all -U "FRUEHWALD\Administrator"
```
=> erst machen, nachdem DC3 mehrere Test erfolgreich bestanden hat

### 4.23 DC1 demoten/außer Betrieb nehmen
Vor dem Löschen alles nochmals kontrollieren!!!  
wichtigste Tests
```Bash
sudo samba-tool drs bind dc3.fruehwald.homenet.com
sudo samba-tool fsmo show
```
Auf DC1
```Bash
sudo samba-tool domain demote --server=dc3.fruehwald.homenet.com -U "FRUEHWALD\Administrator"
```
`--server=dc3.fruehwald.homenet.com` legt DC3 als Partner-DC für die Demotion fest.  
Der Befehl wird auf DC1 ausgeführt. DC1 demotet sich selbst, während die
notwendigen Änderungen über den verbleibenden DC3 in der Domäne eingetragen werden.

Dann wieder alles testen!!!

und erst dann auch DC2 entfernen

## 5. Automatisierter Benutzerimport aus Oracle

Die bestehenden Benutzer befinden sich aktuell in einer Oracle-Datenbank. Die benötigten Benutzerinformationen können aus dieser Datenbank abgefragt und als CSV-Dateien bereitgestellt werden.

Ablauf ist:

```text
Oracle-Datenbank -> CSV-Export -> Debian-Server -> Python-Importskripte -> samba-tool -> Samba Active Directory
```

### 5.1 Benötigte Daten aus Oracle

Dient nur als Bsp:
| Information | Active-Directory-Attribut |
|---|---|
| Benutzername | `sAMAccountName` |
| Vorname | `givenName` |
| Nachname | `sn` |
| Anzeigename | `displayName` |
| E-Mail-Adresse | `mail` |
| Benutzername der Anmeldung | `userPrincipalName` |
| Passwort | Passwort des AD-Benutzers |
| Gruppe | Gruppenmitgliedschaft |

Möglicher Aufbau der CSV
```text
SamAccountName,GivenName,Surname,DisplayName,Mail,UserPrincipalName,Password
```

### 5.2 Migrationsverzeichnis vorbereiten

Auf Debian ein eigenes Verzeichnis für die Migration erstellen:

```bash
mkdir -p /root/migration
```

Zugriffsrechte einschränken:

```bash
chmod 700 /root/migration
```

Die exportierten CSV-Dateien dort ablegen, bspw.:

```text
/root/migration/users.csv
/root/migration/groups.csv
/root/migration/memberships.csv
```

Da sich in `users.csv` auch Passwörter befinden können, sollten die Rechte zusätzlich eingeschränkt werden:

```bash
chmod 600 /root/migration/users.csv
```

Nur `root` sollte auf diese Datei zugreifen können.

### 5.3 Gruppen importieren

Gruppen sollten vor den Gruppenmitgliedschaften erstellt werden.

Neue Datei erstellen:

```bash
nano /root/migration/import_groups.py
```

Inhalt:

```python
import csv
import subprocess

CSV_DATEI = "/root/migration/groups.csv"

with open(CSV_DATEI, newline="", encoding="utf-8-sig") as datei:
    reader = csv.DictReader(datei)

    for gruppe in reader:
        name = gruppe["SamAccountName"].strip()

        if not name:
            continue

        print(f"Erstelle Gruppe: {name}")

        ergebnis = subprocess.run([
            "samba-tool",
            "group",
            "add",
            name
        ])

        if ergebnis.returncode != 0:
            print(f"Gruppe konnte nicht erstellt werden: {name}")
```

Skript starten:

```bash
python3 /root/migration/import_groups.py
```

Anschließend kontrollieren:

```bash
samba-tool group list
```


### 5.4 Benutzer mit Passwörtern importieren

Die Benutzer können anschließend aus der Oracle-CSV erstellt werden.

Dabei wird das bereits vorhandene Passwort direkt aus der CSV verwendet.

Neue Datei:

```bash
nano /root/migration/import_users.py
```

Beispiel:

```python
import csv
import subprocess

CSV_DATEI = "/root/migration/users.csv"

with open(CSV_DATEI, newline="", encoding="utf-8-sig") as datei:
    reader = csv.DictReader(datei)

    for user in reader:
        username = user["SamAccountName"].strip()
        passwort = user["Password"]

        if not username or not passwort:
            print("Benutzername oder Passwort fehlt - Datensatz übersprungen")
            continue

        print(f"Erstelle Benutzer: {username}")

        ergebnis = subprocess.run([
            "samba-tool",
            "user",
            "create",
            username,
            passwort
        ])

        if ergebnis.returncode != 0:
            print(f"Benutzer konnte nicht erstellt werden: {username}")
```

Skript starten:

```bash
python3 /root/migration/import_users.py
```

Anschließend kontrollieren:

```bash
samba-tool user list
```

Bestimmten Benutzer prüfen:

```bash
samba-tool user show <Benutzername>
```

### 5.5 Gruppenmitgliedschaften importieren

Nachdem Benutzer und Gruppen vorhanden sind, können die Gruppenmitgliedschaften übernommen werden.

Reihenfolge:

```text
1. Gruppen erstellen
2. Benutzer erstellen
3. Gruppenmitgliedschaften hinzufügen
```

Neue Datei:

```bash
nano /root/migration/import_memberships.py
```

Inhalt:

```python
import csv
import subprocess

CSV_DATEI = "/root/migration/memberships.csv"

with open(CSV_DATEI, newline="", encoding="utf-8-sig") as datei:
    reader = csv.DictReader(datei)

    for eintrag in reader:
        gruppe = eintrag["Group"].strip()
        mitglied = eintrag["Member"].strip()

        if not gruppe or not mitglied:
            continue

        print(f"Füge {mitglied} zu {gruppe} hinzu")

        ergebnis = subprocess.run([
            "samba-tool",
            "group",
            "addmembers",
            gruppe,
            mitglied
        ])

        if ergebnis.returncode != 0:
            print(f"Mitgliedschaft konnte nicht erstellt werden: {mitglied} -> {gruppe}")
```

Skript starten:

```bash
python3 /root/migration/import_memberships.py
```

Gruppenmitgliedschaft kontrollieren:

```bash
samba-tool group listmembers "<Gruppenname>"
```

### 5.6 Import kontrollieren

Nach dem Import müssen die übernommenen Daten kontrolliert werden.

Benutzer:

```bash
samba-tool user list
```

Bestimmter Benutzer:

```bash
samba-tool user show <Benutzername>
```

Gruppen:

```bash
samba-tool group list
```

Gruppenmitglieder:

```bash
samba-tool group listmembers "<Gruppenname>"
```

### 5.7 Post-Migration

Nach erfolgreicher Migration und Kontrolle sollte die Datei aus Sicherheitsgründen komplett gelöscht werden:

```bash
rm /root/migration/users.csv
```

## 6. Windows-Verwaltung, Gruppenrichtlinien und Sicherheit
Linux Debian UI kann problemlos nachinstalliert werden

Kurze Prüfungen vor der Installation
```Bash
hostname
ip a
ip route
cat /etc/resolv.conf
sudo systemctl status samba-ad-dc
```
Notiz: sudo systemctl status samba-ad-dc => sollte "active (running)" sein

```Bash
sudo apt update
sudo apt install task-xfce-desktop
```
Danach neu starten 
```Bash
sudo reboot
```

Die grafische Verwaltung des Active Directory kann über ein Windows-11-System erfolgen. Samba unterstützt dafür die `Microsoft RSAT-Tools (Remote Server Administration Tools)`. Damit stehen grafische Verwaltungsprogramme wie Active Directory-Benutzer und -Computer, der DNS-Manager, Active Directory-Standorte und -Dienste sowie die Gruppenrichtlinienverwaltung zur Verfügung.

### 6.1 grafische Verwaltung über Windows-11
1. Bei Windows Domain-Admin einlogen
2. Dann auf das Windows-Symbol 
3. `optionale Features` suchen 
4. auf `Features anzeigen` klicken 
5. Bei der Suchleiste `RSAT` eingeben und die gewünschten Features makieren. Falls die RSAT-Features nicht angezeigt werden, dann auf `Verfügbare Features anzeigen` klicken und nochmals nach `RSAT` suchen

Nach der erfolgreichen Installation neustarten und dann `Windows + R` => `dsa.msc` eingeben und <kbd>Enter</kbd>


### 6.2 RSAT-Verwaltung unter Windows vervollständigen

Nach der Installation der RSAT-Komponenten kann das Samba Active Directory von einem Windows-11-Domänenclient grafisch verwaltet werden.

Wichtige RSAT-Werkzeuge sind:

| Werkzeug | Startbefehl | Aufgabe |
|---|---|---|
| Active Directory-Benutzer und -Computer | `dsa.msc` | Benutzer, Gruppen, Computer und OUs verwalten |
| Gruppenrichtlinienverwaltung | `gpmc.msc` | Gruppenrichtlinien erstellen, bearbeiten und verknüpfen |
| DNS-Manager | `dnsmgmt.msc` | AD-DNS-Zonen und Einträge verwalten |
| Active Directory-Standorte und -Dienste | `dssite.msc` | Domain Controller und AD-Standorte verwalten |

Benötigt werden aber administrative Rechte im Active Directory.

### 6.3 Gruppenwechsel und Dateizugriff testen
#### 6.3.1 Gruppenmitgliedschaft über RSAT ändern

`Active Directory-Benutzer und -Computer` öffnen:

```text
Windows + R
dsa.msc
```

Danach:

1. Den gewünschten Benutzer öffnen.
2. Zum Reiter `Mitglied von` wechseln.
3. Gruppe hinzufügen oder entfernen.
4. Änderung übernehmen.

Alternativ kann die Gruppenmitgliedschaft direkt von Samba-Domain-Controller kontrolliert werden mittels Kommandozeile. Dort kann dann ein Benutzer von einer Gruppe entfernt oder hinzugefügt werden.

#### 6.3.2 Neue Gruppenmitgliedschaft am Client übernehmen

Eine Änderung der AD-Gruppenmitgliedschaft wird nicht immer sofort in einer bereits bestehenden Windows-Anmeldesitzung wirksam. Grund dafür ist, dass Windows bei der Anmeldung ein Zugriffstoken erstellt, in dem die Gruppen des Benutzers enthalten sind.

Deshalb nach einem Gruppenwechsel:

1. Benutzer vollständig abmelden.
2. Benutzer erneut anmelden.
3. Gruppen erneut kontrollieren.

Kontrolle:

```cmd
whoami /groups
```

Zusätzlich kann Kerberos-Ticketcache kontrolliert werden:

```cmd
klist
```

Falls notwendig:

```cmd
klist purge
```

Danach erneut anmelden bzw. auf die Ressource zugreifen.

`gpupdate /force` aktualisiert Gruppenrichtlinien, ersetzt aber nicht in jedem Fall das Benutzer-Zugriffstoken. Bei geänderten Gruppenmitgliedschaften ist eine vollständige Ab- und Neuanmeldung daher der zuverlässigste Test.

#### 6.3.3 Dateizugriff über Samba-Freigaben kontrollieren

Die Berechtigung wird weiterhin in `/etc/samba/smb.conf` über die AD-Gruppen gesteuert, zum Beispiel:

```ini
[Verwaltung]
    path = /srv/samba/verwaltung
    browseable = yes
    read only = yes
    valid users = @Verwaltung @IT
    write list = @Verwaltung @IT
```
So muss nicht jeder Benutzer einzelne Rechte bekommen, wenn alles über die Gruppen organisiert ist.


### 6.4 Gruppenrichtlinienverwaltung mit RSAT

#### 6.4.1 Gruppenrichtlinienverwaltung öffnen

Auf dem Windows-Domänenclient:

```text
Windows + R
gpmc.msc
```

Danach Struktur folgen:

```text
Gesamtstruktur
 Domänen
     fruehwald.homenet.com
         Default Domain Policy
         weitere Gruppenrichtlinienobjekte
```
Dort können Gruppenrichtlinienobjekte, kurz `GPOs`, erstellt, bearbeitet und mit der Domäne oder einer OU verknüpft werden.

#### 6.4.2 Aufbau einer Gruppenrichtlinie

Eine GPO besteht aus zwei Teilen:

1. einem Objekt im Active Directory
2. den zugehörigen Richtliniendateien im `SYSVOL`

Dadurch reicht es nicht aus, dass nur das GPO-Objekt im Active Directory existiert. Auch der zugehörige Inhalt im SYSVOL muss erreichbar sein und die richtigen Berechtigungen besitzen. Der Zusammenhang war dann nämlich bei unserer SYSVOL-Fehlerbehebung wichtig.


### 6.5 SYSVOL und Fehlerbehebung

Samba repliziert die Dateien im SYSVOL nicht automatisch über DRS zwischen den Domain Controllern. Bei mehreren Samba-DCs muss deshalb zusätzlich eine SYSVOL-Synchronisierung eingerichtet werden.

#### 6.5.1 Aufgabe von SYSVOL

`SYSVOL` ist eine spezielle Freigabe eines Active-Directory-Domain-Controllers.

Darin liegen unter anderem:

- Gruppenrichtliniendateien
- Anmelde- bzw. Logon-Skripte
- Dateien, die von Domänenclients für Gruppenrichtlinien benötigt werden

Unter Samba befindet sich das Verzeichnis normalerweise unter:

```text
/var/lib/samba/sysvol
```

Über das Netzwerk wird es als Freigabe bereitgestellt:

```text
\\fruehwald.homenet.com\SYSVOL
```

bzw. direkt über einen Domain Controller:

```text
\\dc1\SYSVOL
```

Die GPO-Dateien liegen unterhalb der Domäne beispielsweise in:

```text
/var/lib/samba/sysvol/fruehwald.homenet.com/Policies/
```

Die einzelnen GPOs besitzen dort Ordner mit einer GUID als Namen.

#### 6.5.2 Aufgetretenes SYSVOL-Problem

Während der Arbeit mit den Gruppenrichtlinien funktionierte der Zugriff bzw. die Bearbeitung der GPOs nicht mehr korrekt.

Das Problem lag nicht an der grundlegenden AD-Domäne selbst. Die Benutzer, Gruppen und der Domain Controller waren weiterhin vorhanden.

Der Fehler betraf den `SYSVOL`-Bereich und dessen Berechtigungen bzw. ACLs.

Dadurch konnte Windows die für Gruppenrichtlinien notwendigen Dateien nicht mehr so verwenden, wie es das Active Directory erwartete.

Typische Auswirkungen davon sind:

- GPO lässt sich in der Gruppenrichtlinienverwaltung nicht korrekt bearbeiten.
- Windows meldet Zugriffs- oder Berechtigungsprobleme.
- `gpupdate /force` kann Fehler liefern.
- Das GPO-Objekt ist im AD sichtbar, die dazugehörigen Dateien im SYSVOL können aber nicht korrekt gelesen werden.

> **Wichtig:** Bei Samba-AD dürfen die SYSVOL-Berechtigungen nicht beliebig mit `chmod`, `chown` oder pauschalen Rechten wie `777` verändert werden. Samba verwendet für SYSVOL Windows-kompatible NT-ACLs. Werden diese falsch verändert, können Gruppenrichtlinien beschädigt bzw. unbrauchbar werden.

#### 6.5.3 SYSVOL vom Windows-Client prüfen

Zuerst wurde geprüft, ob der SYSVOL-Pfad vom Windows-Domänenclient erreichbar ist.

Im Explorer:

```text
\\fruehwald.homenet.com\SYSVOL
```

Alternativ:

```text
\\dc1\SYSVOL
```

Zusätzlich kann in einer Eingabeaufforderung getestet werden:

```cmd
dir \\fruehwald.homenet.com\SYSVOL
```

Der Client muss den Domänenordner und dessen Inhalte lesen können.

#### 6.5.4 SYSVOL auf dem Samba-DC prüfen

Auf dem Domain Controller kann zuerst kontrolliert werden, ob das Verzeichnis vorhanden ist:

```bash
sudo ls -la /var/lib/samba/sysvol
```

Domänenverzeichnis kontrollieren:

```bash
sudo ls -la /var/lib/samba/sysvol/fruehwald.homenet.com
```

Policies kontrollieren:

```bash
sudo ls -la /var/lib/samba/sysvol/fruehwald.homenet.com/Policies
```

Anschließend die von Samba erwarteten SYSVOL-ACLs prüfen:

```bash
sudo samba-tool ntacl sysvolcheck
```

Wenn hier Fehler ausgegeben werden, stimmen die SYSVOL-ACLs nicht mit dem von Samba erwarteten Zustand überein.

#### 6.5.5 SYSVOL-ACLs mit samba-tool reparieren

Für die Reparatur sollte nicht versucht werden, die Berechtigungen per Hand nachzubauen.

Stattdessen besitzt Samba einen eigenen Befehl:

```bash
sudo samba-tool ntacl sysvolreset
```

Danach erneut prüfen:

```bash
sudo samba-tool ntacl sysvolcheck
```

Anschließend Samba neu starten:

```bash
sudo systemctl restart samba-ad-dc
```

Status kontrollieren:

```bash
sudo systemctl status samba-ad-dc --no-pager
```

Erwarteter Zustand:

```text
Active: active (running)
```

Danach nochmals vom Windows-Client aufrufen:

```text
\\fruehwald.homenet.com\SYSVOL
```

und die Gruppenrichtlinien aktualisieren:

```cmd
gpupdate /force
```

Damit wurde der SYSVOL-Bereich wieder in einen von Samba erwarteten Berechtigungszustand gebracht.

#### 6.5.6 Wichtiger Hinweis bei mehreren Samba-DCs

Bei mehreren Samba-Domain-Controllern muss zwischen zwei Arten von Daten unterschieden werden:

- AD-Objekte werden über die normale AD-/DRS-Replikation repliziert.
- Die Dateien im SYSVOL müssen ebenfalls auf allen verwendeten Domain Controllern konsistent vorhanden sein.

Die normale Prüfung:

```bash
sudo samba-tool drs showrepl
```

beweist daher in erster Linie, dass die AD-Datenbank repliziert wird. Sie ist alleine kein Beweis dafür, dass sämtliche SYSVOL-Dateien auf allen DCs identisch sind.

Deshalb bei GPO-Problemen zusätzlich immer den SYSVOL-Bestand und die SYSVOL-Berechtigungen auf den beteiligten DCs kontrollieren.

---

### 6.6 Gruppenrichtlinie erstellen und verknüpfen

#### 6.6.1 GPO erstellen

In der Gruppenrichtlinienverwaltung:

1. `gpmc.msc` öffnen.
2. Die gewünschte Domäne oder OU auswählen.
3. Rechtsklick.
4. `Gruppenrichtlinienobjekt hier erstellen und verknüpfen` auswählen.
5. Einen eindeutigen Namen vergeben.
6. GPO anschließend über `Bearbeiten` öffnen.

Dadurch wird sowohl das GPO im Active Directory als auch der zugehörige Richtlinienbereich im SYSVOL verwendet.

#### 6.6.2 GPO auf dem Windows-Client aktualisieren

Nach Änderungen an einer Gruppenrichtlinie:

```cmd
gpupdate /force
```

Danach je nach Richtlinie:

- Benutzer ab- und wieder anmelden oder
- Windows neu starten.

Dadurch wird verhindert, dass beim Testen noch eine alte Richtlinienversion verwendet wird.

---

### 6.7 AppLocker über Gruppenrichtlinien konfigurieren

AppLocker wurde verwendet, um die Ausführung von Programmen über Gruppenrichtlinien einzuschränken.

#### 6.7.1 AppLocker in der GPO finden

Im Gruppenrichtlinienverwaltungs-Editor:

```text
Computerkonfiguration
    Richtlinien
        Windows-Einstellungen
            Sicherheitseinstellungen
                Anwendungssteuerungsrichtlinien
                    AppLocker
```

Unter AppLocker befinden sich verschiedene Regeltypen.

Für normale `.exe`-Programme ist wichtig:

```text
Ausführbare Regeln
```

Nicht mit `Ausgeführte Regeln` verwechseln. Der richtige Bereich heißt in der deutschen Oberfläche `Ausführbare Regeln`.

#### 6.7.2 Ausführbare Regeln

In `Ausführbare Regeln` können Regeln erstellt werden, die festlegen, welche Programme ausgeführt werden dürfen.

Regeln können sich unter anderem beziehen auf:

- Pfad
- Herausgeber
- Datei-Hash
- Benutzer oder Gruppen

Für die Tests wurden die Regeln innerhalb einer Gruppenrichtlinie verwaltet.

#### 6.7.3 Standardregeln erstellen

Vor eigenen Einschränkungen sollten die AppLocker-Standardregeln erstellt werden.

Dazu:

1. Rechtsklick auf `Ausführbare Regeln`.
2. `Standardregeln erstellen`.

Typischerweise werden Regeln erzeugt, die sinngemäß Folgendes erlauben:

```text
Alle Dateien im Windows-Ordner
Alle Dateien im Programme-Ordner
Alle Dateien für lokale Administratoren
```

Diese Standardregeln sind wichtig, weil AppLocker nach dem Aktivieren einer Regelsammlung ansonsten auch notwendige Windows-Programme blockieren kann.

#### 6.7.4 Ausnahmen richtig verwenden

Eine `Ausnahme` bedeutet, dass ein bestimmter Pfad bzw. eine bestimmte Datei von einer allgemeineren Regel ausgenommen wird.

Beispiel:

```text
Zulassen:
%WINDIR%\*

Ausnahme:
bestimmte ausführbare Datei
```

Die Ausnahme darf nur an der Regel gesetzt werden, bei der sie tatsächlich benötigt wird.

In der AppLocker-Übersicht zeigt die Spalte `Ausnahme` mit `Ja`, dass die jeweilige Regel mindestens eine Ausnahme enthält.

Während der Fehlerbehebung wurde genau diese Spalte kontrolliert.

> **Wichtig:** Es ist nicht richtig, dieselben Ausnahmen einfach in sämtliche Standardregeln einzutragen. Dadurch können sich Regeln anders auswirken als beabsichtigt und auch administrative Programme blockieren.

Die Administrator-Standardregel, die Administratoren grundsätzlich die Ausführung von Dateien erlaubt, sollte nicht versehentlich mit denselben einschränkenden Ausnahmen versehen werden.

#### 6.7.5 Application-Identity-Dienst

AppLocker verwendet den Windows-Dienst `Anwendungsidentität` bzw. `Application Identity`.

Der Dienst kann auf dem Windows-Client kontrolliert werden:

```powershell
Get-Service AppIDSvc
```

Der zugehörige Dienstname lautet:

```text
AppIDSvc
```

Für eine tatsächlich erzwungene AppLocker-Konfiguration muss sichergestellt werden, dass der Dienst verfügbar ist und die Richtlinie vom Client übernommen wurde.

---

### 6.8 AppLocker-Komplikationen und Fehlerbehebung

#### 6.8.1 PowerShell normal bzw. als Administrator

Beim Test mit einem Domänenbenutzer trat ein auffälliger Unterschied auf:

- PowerShell ließ sich in der normalen Benutzersitzung öffnen.
- Beim Versuch, PowerShell mit erhöhten Rechten bzw. `Als Administrator ausführen` zu starten, funktionierte der Start nicht wie erwartet.

Damit war klar, dass nicht PowerShell selbst beschädigt war. Das Verhalten hing mit den angewendeten AppLocker-Regeln bzw. deren Ausnahmen zusammen.

#### 6.8.2 Fehlerhafte Ausnahmen in den Standardregeln

Bei der Kontrolle von:

```text
AppLocker
    Ausführbare Regeln
```

zeigte sich, dass Ausnahmen an mehreren Regeln gesetzt worden waren.

Dadurch war die Konfiguration zu breit geworden.

Insbesondere muss beachtet werden:

- Eine allgemeine Zulassungsregel kann durch ihre Ausnahme eingeschränkt werden.
- Werden Ausnahmen auch in einer Administrator-Zulassungsregel gesetzt, kann das Auswirkungen auf Programme haben, die mit administrativem Token gestartet werden.
- Deshalb dürfen Ausnahmen nicht blind auf alle Standardregeln kopiert werden.

Genau diese falsche Verteilung der AppLocker-Ausnahmen war die Ursache für die späteren AppLocker-Komplikationen.

#### 6.8.3 AppLocker-Regeln korrigieren

Die AppLocker-Regeln wurden deshalb einzeln geöffnet und kontrolliert.

Vorgehensweise:

1. `gpmc.msc` öffnen.
2. Betroffene GPO bearbeiten.
3. Zu `AppLocker → Ausführbare Regeln` wechseln.
4. Jede Standardregel einzeln öffnen.
5. Reiter bzw. Bereich `Ausnahmen` kontrollieren.
6. Nicht benötigte Ausnahmen entfernen.
7. Nur bei der tatsächlich vorgesehenen Regel die benötigte Ausnahme beibehalten.
8. Prüfen, dass die Administrator-Zulassungsregel nicht versehentlich durch eine ungewollte Ausnahme eingeschränkt wird.

In der Übersicht darf `Ausnahme: Ja` somit nur bei einer Regel erscheinen, wenn diese Regel tatsächlich bewusst eine Ausnahme besitzen soll.

Anschließend auf dem Client:

```cmd
gpupdate /force
```

Danach den Benutzer vollständig abmelden bzw. den Rechner neu starten und erneut testen.

> **Ergebnis der Fehleranalyse:** Nicht AppLocker selbst war defekt. Die Regelstruktur war falsch konfiguriert. Durch die Korrektur der Ausnahmen konnte verhindert werden, dass eine eigentlich allgemeine Zulassungsregel unbeabsichtigt eingeschränkt wird.

---

### 6.9 Gruppenrichtlinien und AppLocker testen

#### 6.9.1 Richtlinien aktualisieren

Nach jeder Änderung:

```cmd
gpupdate /force
```

Wenn Windows meldet, dass für die Übernahme eine Abmeldung oder ein Neustart notwendig ist, sollte dies durchgeführt werden.

#### 6.9.2 Angewendete Gruppenrichtlinien prüfen

Kontrollieren, welche Richtlinien auf den Benutzer bzw. Computer angewendet wurden:

```cmd
gpresult /r
```

Ausführlicher Bericht:

```cmd
gpresult /h C:\gpresult.html
```

Danach:

```text
C:\gpresult.html
```

im Browser öffnen.

Damit lässt sich unterscheiden, ob:

- die GPO überhaupt angewendet wurde oder
- die GPO vorhanden ist, aber die enthaltene AppLocker-Regel falsch konfiguriert ist.

#### 6.9.3 Benutzergruppen kontrollieren

Da Gruppenmitgliedschaften ebenfalls Einfluss auf Berechtigungen und möglicherweise die GPO-Zielgruppe haben, zusätzlich kontrollieren:

```cmd
whoami
```

und:

```cmd
whoami /groups
```

Wenn kurz zuvor eine Gruppenmitgliedschaft geändert wurde, den Benutzer vorher vollständig ab- und wieder anmelden.