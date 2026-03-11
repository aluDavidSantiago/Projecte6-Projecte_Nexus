
# Informe Tècnic – Protecció de la Informació per a Projecte Nexus

## 1. Justificació Teòrica  
El **xifratge** és un procés reversible que transforma informació llegible en dades inintel·ligibles utilitzant una clau secreta. Només qui disposa de la contrasenya pot recuperar el contingut original. El seu objectiu principal és garantir la **confidencialitat**.  
Una **funció hash**, en canvi, és un procés **no reversible** que genera una empremta digital única d’un fitxer. Si el fitxer es modifica mínimament, el hash resultant canvia completament. El seu propòsit és assegurar la **integritat**, no pas amagar informació.

---

## 2. Evidències Tasca 1 – Xifratge amb VeraCrypt

### 2.1 Configuració del volum xifrat  
*Captura de pantalla de la configuració del volum (AES-256, mida 100MB).*

### 2.2 Unitat muntada amb el fitxer secret  
*Captura mostrant la unitat virtual muntada i el fitxer `EXAMEN_FINAL_SEGURETAT.txt` a l’interior.*

### 2.3 Procés d’accés al fitxer  
*Captures que mostren:*
- Intent d’obrir el volum sense contrasenya (accés denegat).  
- Introducció de la contrasenya robusta.  
- Accés correcte al contingut un cop muntat.

---

## 3. Evidències Tasca 2 – Verificació d’Integritat (Hashing)

### 3.1 Hash original  
Fitxer: `nota_final_curs.txt`  
Contingut:  
```
L'alumne ha aprovat amb un 5
```

Comanda utilitzada (exemple Linux):  
```
sha256sum nota_final_curs.txt
```

*Captura mostrant el hash SHA-256 original.*

### 3.2 Hash després de modificar el fitxer  
Nou contingut:  
```
L'alumne ha aprovat amb un 9
```

Comanda:  
```
sha256sum nota_final_curs.txt
```

*Captura mostrant el nou hash, completament diferent de l’original.*

---

## 4. Conclusió  
Per a Nexus és essencial protegir la informació sensible, especialment quan es transporta en dispositius físics. L’ús de contenidors xifrats garanteix que, en cas de pèrdua o robatori, les dades continuïn protegides. Igualment important és utilitzar **contrasenyes robustes**, difícils d’endevinar i emmagatzemades de manera segura.  
D’altra banda, l’ús de funcions **hash** permet verificar que documents crítics —com actes de notes, exàmens o contractes— no han estat manipulats. La combinació de xifratge i hashing reforça la confidencialitat i la integritat de tota la informació acadèmica gestionada per Nexus.

