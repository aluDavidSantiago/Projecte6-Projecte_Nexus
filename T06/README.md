## T06: Projecte Nexus — Implantació de PKI i Signatura Digital Corporativa

### Breu descripció
Projecte Nexus necessita garantir **integritat**, **autenticitat** i **no repudi** dels seus documents interns i contractes. Fins ara utilitzaven signatures en paper, però volen modernitzar el procés mitjançant una infraestructura pròpia de certificació digital.

L’objectiu és crear una **Prova de Concepte (PoC)** que permeti desplegar una **PKI corporativa**, emetre certificats digitals per als empleats i signar documents PDF de manera oficial sense dependre de tercers.

---

## Introducció
Després de resoldre els problemes de confidencialitat, Nexus ha detectat la necessitat d’assegurar que els documents siguin fiables i verificables. Per això, volen implementar:

- Una **Autoritat de Certificació (CA)** pròpia.
- Certificats digitals corporatius per als empleats.
- Un sistema de **signatura digital** per a documents PDF.

Aquesta PoC demostrarà que és possible gestionar tot el procés internament.

---

## Descripció de l’activitat
L’activitat es divideix en **tres fases principals** i es realitza en parelles:

- **Administrador de Nexus**: gestiona el servidor i la CA.
- **Treballador de Nexus**: actua com a client que sol·licita i utilitza el certificat.

### Fase 1: Desplegament de la CA a Ubuntu Server
- Instal·lació i configuració de l’Autoritat de Certificació.
- Generació de la clau privada i certificat arrel.
- Configuració de rutes, permisos i polítiques.

### Fase 2: Sol·licitud i Emissió de Certificats pel client
- Creació d’una CSR (Certificate Signing Request).
- Enviament i aprovació de la sol·licitud.
- Emissió del certificat client.
- Instal·lació del certificat i de la clau pública de la CA al client.

### Fase 3: Signatura Digital i Verificació (Acrobat Reader)
- Signatura d’un document PDF amb el certificat corporatiu.
- Validació de la signatura i comprovació de la cadena de confiança.

---

## Què cal lliurar

### Memòria tècnica (memoria.md)
En format **Markdown**, incloent:

- Captures de pantalla comentades del procés d’instal·lació de la CA a Ubuntu Server.
- Documentació del procediment de sol·licitud del certificat client.
- Procediment de creació del certificat client.
- Instal·lació de la clau pública de la CA al client.
- Instal·lació del certificat client.
- Procediment de signatura d’un document PDF i comprovació.
- Explicació de les diferències entre **clau pública** i **clau privada** en aquest procés.

### Evidència de la signatura
- Un **PDF signat digitalment** per un membre del grup.

### Certificat arrel
- El fitxer **.cer** de la CA (clau pública), per poder ser descarregat i validar signatures.

---

## Material de suport
- Material de l’assignatura *Seguretat Informàtica — RA3: Signatura electrònica i Certificats Digitals* (Moodle).
- Guia de l’activitat proporcionada pel professorat.

---

Si vols, puc ajudar-te a convertir aquest text en una plantilla de *memoria.md* llesta per omplir.