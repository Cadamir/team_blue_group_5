Kurze Übersicht über Ansbile-Playbooks:

ansible.cfg:
- Config-Datei für Ansible, 
- Verknüpft eigene Ordnerstruktur, 
- Fordert sudo Rechte für Server an 

list.ini
- Liste an Endpunkten, welche Ansbile bespielt
- [an lokalen Endpunkt anpassen]
	- ansible _host= Server IP
	- ansible_user = Admin?-User auf Server

group_vars/all/vars.yml
- Variablen für die später folgenden Playbooks
- [an lokalen Endpunkt / Netz anpassen]
	- beinahltet Netz-IP, 
	- mail_hostname, 
	- mail_domain, 
	- etc.

adduser.yml / postfix.yml usw.
- einzelnen Playbooks (Über-Skripte) für die einzelnen Komponenten / Systeme
- rufen die Roles (die "echten" Skripte / Aufgaben) des benannten Playbooks auf

site.yml
- Auszuführendes Playbook, welches alle anderen startet
- Nur die Datei ausführen

roles
- beinhaltet die roles der Playbooks

Weiter in der Ordnerstruktur einer Role 

tasks
- beinhaltet die Befehle / Änderungen, welche am Server vorgenommen werden
- die main.yml wird von Ansible pro role gestartet und verweist auf die Teilskripe, falls vorhanden

handler
- beinhaltet Skripte für Steuerung der Server-Komponenten als Code, aufrufbar in den tasks
- bspw Postfix reload handler entspricht dem systemctl reload postfix / postfix reload

templates 
- beinaltet Templates bspw von config-Daten, so kann die Task kurz gehalten werden und einfach hierrauf zeigen
- kann globale Variablen nutzen 



***WICHTIG***

Was benötigt wird:
- RICHTIGE SUDO-Implementierung nehmen
	- mit Ubuntu 25.10 (unsere Version) wird sudo-rs genutzt
	- damit kommt Ansible noch nicht klar
	- deswegen Wechsel zu altem sudo.ws
	- mit sudo --version überprüfen -> sudo version 1.9.x benötigt
	- mit sudo update-alternatives --config sudo kann gewechselt werden
- Anpassung an lokales IP-Netz
- SSH-Verbindung / Port 22 muss offen sein
- Admin Rechte
- Nach ausführung des Playbooks -> sudo systemctl restart dovecot -> dann gehen Mails aus dem Netzwerkr aus

***WICHTIG***



Nützliche Befehle:

ansible-playbook [Playbook.yml // hier: site.yml] -K
- Übertragt das Playbook auf den Server

ansible-playbook site.yml -K --check
- Spielt die Übertragung nur durch / macht einen Check & zeigt Fehler an

ansible-playbook site.yml -K --syntax-check
- Überprüft die Syntax des Playbooks, lädt nichts auf den Server 

sudo doveadm mailbox list -u [User]
- es soltle "INBOX" ausgegeben werden
- User-Erstellung und Mail-Verzeichnis passt

sudo journalctl -u dovecot -f
- gibt nützliches Debugging für Dovecot

sudo tail -f /var/log/mail.log
- zeigt Zustand der versendeten Mails

