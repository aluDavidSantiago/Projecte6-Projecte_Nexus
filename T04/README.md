
# T04: Duel de Titans — Apache vs Nginx  
## Breu descripció

En aquesta pràctica comparareu el rendiment dels servidors web **Apache** i **Nginx** sota diferents nivells de càrrega. Utilitzareu eines de benchmarking per mesurar com responen davant trànsit lleuger i situacions d’estrès.

---

## Introducció

Fins ara heu configurat dos servidors web diferents:  
- **Apache**, clàssic i robust  
- **Nginx**, lleuger i ràpid  

Tots dos serveixen el mateix contingut web del client *Nexus*, però… **responen igual sota pressió?**

En aquesta pràctica adoptareu el rol d’**Auditors de Sistemes**, sotmetent els servidors a proves d’estrès des de la màquina client **Zorin OS** per determinar quin gestiona millor les connexions.

---

## Descripció de l’activitat

- Cada alumne ha de tenir la seva màquina virtual amb Apache i Nginx instal·lats.  
- Cal millorar el contingut web del site *nexus* perquè sembli més professional (podeu usar IA per generar-lo).  
- Un alumne farà les proves sobre **Apache** i l’altre sobre **Nginx**.  
- A Zorin OS instal·leu les utilitats necessàries:

```bash
sudo apt install apache2-utils
```

---

## Proves

### Prova de càrrega lleugera

Simula trànsit normal:  
- **10 usuaris concurrents**  
- **1000 peticions totals**

Comanda:

```bash
ab -n 1000 -c 10 http://IP_SERVIDOR/
```

Anoteu per cada servidor:

- Time taken for tests  
- Transfer Rate  
- Requests per second  
- Time per request (mean)  
- Completed requests  
- Failed requests  

---

### Prova d’estrès

Simula un pic de trànsit o un atac lleu:  
- **100 usuaris concurrents**  
- **10.000 peticions**

Comanda:

```bash
ab -n 10000 -c 100 http://IP_SERVIDOR/
```

>  *Si algun servidor falla (errors, timeouts…), també s’ha d’anotar.*

---

## Taula comparativa (en Markdown)

| **Mètrica**               | **Apache — prova lleugera** | **Nginx — prova lleugera** | **Apache — prova d’estrès** | **Nginx — prova d’estrès** |
|---------------------------|------------------------------|------------------------------|------------------------------|------------------------------|
| Time taken for test       |                              |                              |                              |                              |
| Transfer rate             |                              |                              |                              |                              |
| Requests per second (RPS) |                              |                              |                              |                              |
| Time per request (mean)   |                              |                              |                              |                              |
| Completed requests        |                              |                              |                              |                              |
| Failed requests           |                              |                              |                              |                              |

---

## Material de suport

J.D. Muñoz. *El comando ab*. Servicios de Red e Internet. 2017.  
https://serviciosgs.readthedocs.io/es/latest/rendimiento/ab.html

---
