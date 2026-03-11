# **T02: Missió Apache — Desplegament Multidomini i Segur**

## **Breu descripció**

Les prediccions dels assessors de la incubadora s’han confirmat! Ja teniu un primer client amb un encàrrec relacionat amb la infraestructura web.

Nexus és una nova empresa de formació a Mataró, que ha contactat amb nosaltres per al desplegament i gestió de la seva infraestructura web. En aquesta fase inicial, l'objectiu és establir una base sòlida i segura per als seus serveis corporatius abans de migrar definitivament al núvol.

Els requisits del client són clars i directes:

- Allotjar **dos portals web diferenciats** (un per l'agència de disseny i un altre per a l'acadèmia de formació) en un únic servidor per optimitzar recursos.
- Garantir la **màxima seguretat** en les comunicacions mitjançant xifratge **SSL/TLS**.
- Assegurar un **rendiment òptim** utilitzant protocols de transferència moderns.
- Configurar el servidor web **Apache sobre Ubuntu Server** amb rigor professional.

---

## **Descripció de l’encàrrec**

Abans del desplegament en un VPS, el projecte es desenvoluparà en una màquina virtual local. Un cop superades les proves de funcionament i amb el vistiplau del client, es desplegarà a l’entorn d’Internet.

Les accions a realitzar consisteixen en la instal·lació, configuració de dominis virtuals i securització avançada d'un servidor web Apache. Tot el procés s’ha de documentar en un informe tècnic.

---

## **Tasques específiques a realitzar**

### **1. Instal·lació i Configuració Base**

- Instal·leu el servidor web Apache sobre la màquina virtual Ubuntu Server.
- Verifiqueu el funcionament del servei utilitzant la comanda `apachectl` per comprovar l'estat.
- Assegureu-vos que l'usuari `www-data` s'ha creat correctament i verifiqueu els permisos de la carpeta `/var/www`.

---

### **2. Desplegament de VirtualHosts (Multidomini)**

- El client té dos dominis:
  - `projectenexus.test` (Site 1)
  - `academia.test` (Site 2)
- Creeu l'estructura de directoris necessària a `/var/www/` per allotjar ambdós llocs de manera organitzada.
- Configureu dos VirtualHosts a `/etc/apache2/sites-available/` fent servir com a base l'arxiu de configuració per defecte.
- Activeu els llocs amb la comanda `a2ensite` i modifiqueu l'arxiu `hosts` per simular la resolució de noms (DNS) i que els dominis responguin correctament.

---

### **3. Personalització d'Errors**

Configureu una pàgina d'error personalitzada per al codi **404 (Not Found)** per a, com a mínim, un dels VirtualHosts. El missatge ha de ser corporatiu i professional, evitant la pàgina per defecte del servidor.

---

### **4. Seguretat i Certificats (HTTPS)**

- Habiliteu el mòdul **SSL** a Apache.
- Genereu un **certificat autosignat** per als dos dominis `projectenexus.test` i `academia.test` utilitzant `openssl`.  
  - Validesa: **365 dies**  
  - Clau RSA: **2048 bits**
- Configureu els VirtualHosts segurs (port **443**) apuntant a les claus generades.
- Configureu una **redirecció forçada** perquè qualsevol petició HTTP (port 80) a `projectenexus.test` i `academia.test` es redirigeixi automàticament a HTTPS (port 443).

---

### **5. Optimització amb HTTP/2**

- Habiliteu el protocol **HTTP/2** per millorar la latència i la velocitat de càrrega de la web segura.
- Configureu la directiva `Protocols` dins dels VirtualHosts corresponents.
- Demostreu tècnicament que el protocol està actiu utilitzant la comanda `curl` o inspeccionant la xarxa amb el navegador.

---

## **Què cal lliurar**

Cal redactar una **memòria tècnica** de la instal·lació i configuració, incloent les proves de funcionament.  
Aquesta memòria ha de contenir explicacions clares i no només captures de pantalla, ja que els clients no són experts i necessiten entendre el que s’ha fet.

---

## **Material de suport**

- **UD5.AA2. El servidor Apache** — Disponible al Moodle del mòdul de Serveis de Xarxa.

