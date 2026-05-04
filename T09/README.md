**T09 – Estimació temporal i planificació professional del desplegament IT**

## Autoria

*   **Santiago Hernandez**
*   **Santiago Moreno**  
    CFGM Sistemes Microinformàtics i Xarxes (SMX2)

***

## 1. Objectiu del projecte

L’objectiu d’aquest projecte és planificar de manera professional el desplegament d’una infraestructura IT per a l’empresa **FoodLogístics S.A.**, superant un model reactiu i poc estructurat.  
El focus no és únicament definir què es farà, sinó **quan, en quin ordre, amb quines dependències, amb quins riscos i amb quina distribució temporal realista**.

Aquest lliurament correspon a la tasca **T09 – Estimació temporal del projecte**, i posa èmfasi en la capacitat de planificació, anticipació i organització pròpia d’un equip IT professional.

***

## 2. Enfocament de planificació

La planificació s’ha dividit en tasques reals i coherents, identificades com **T01–T08**, basades en les necessitats del client i en l’anàlisi prèvia del projecte.

S’ha evitat una planificació lineal simplificada, apostant per:

*   identificació clara de dependències,
*   execució de tasques en paral·lel quan és viable,
*   detecció del camí crític,
*   inclusió d’una tasca transversal de documentació,
*   estimacions realistes basades en l’entorn lectiu real.

El calendari utilitzat correspon a **dies reals de classe**, no a dates teòriques, fet que garanteix coherència entre el diagrama i el desenvolupament real del projecte.

***

## 3. Tasques del projecte

Les tasques incloses al diagrama de Gantt són:

*   **T01** – Anàlisi del sector i competència (estratègica)
*   **T02** – Anàlisi de necessitats del client (bloquejant)
*   **T03** – Disseny de la solució IT (crítica)
*   **T04** – Infraestructura (xarxa i servidors) (crítica)
*   **T05** – Seguretat i sistemes de backups (crítica)
*   **T06** – Preparació de l’entorn cloud / on‑prem (tècnica)
*   **T07** – Documentació tècnica i d’usuari (transversal)
*   **T08** – Validació final amb el client (final)

***

## 4. Dependències i camí crític

El diagrama identifica clarament les dependències obligatòries entre tasques, representades tant de manera lògica com visual mitjançant fletxes:

*   T02 depèn de T01
*   T03 depèn de T02
*   T04 i T05 depenen de T03
*   T06 depèn de T04 i T05
*   T08 depèn de totes les tasques anteriors

### Camí crític del projecte

    T01 → T02 → T03 → T04 / T05 → T06 → T08

Qualsevol retard en una d’aquestes tasques impactaria directament en la data final del projecte.  
La documentació (T07) es considera **transversal** i no forma part del camí crític, ja que pot avançar-se o finalitzar-se sense bloquejar la resta del projecte.

***

## 5. Paral·lelisme i criteri professional

Les tasques **T04 (Infraestructura)** i **T05 (Seguretat i backups)** s’executen en paral·lel perquè, tot i partir del mateix disseny base, poden ser implementades per tècnics diferents sense interferències directes.

La documentació (T07) s’inicia un cop el disseny està definit, permetent:

*   reduir errors en fases posteriors,
*   garantir traçabilitat,
*   millorar la qualitat global del projecte.

Aquest enfocament reflecteix un criteri professional i realista, propi d’un projecte IT real.

***

## 6. Estimació temporal i realisme

Les duracions assignades a cada tasca tenen en compte:

*   temps de comprensió i anàlisi,
*   coordinació entre membres de l’equip,
*   proves i validacions,
*   correcció d’errors,
*   interrupcions pròpies de l’entorn lectiu.

Aquesta estimació evita una visió excessivament optimista i permet una planificació sostenible.  
L’ús d’intel·ligència artificial s’ha fet com a suport puntual, però totes les decisions han estat **analitzades i reinterpretades pel grup**, aplicant criteri propi.

***

## 7. Diagrama de Gantt

El diagrama de Gantt s’ha implementat en format **HTML amb representació visual avançada**, similar a eines empresarials de gestió de projectes.

El diagrama mostra:

*   tasques i durades,
*   paral·lelismes,
*   dependències mitjançant fletxes visuals,
*   classificació de tasques per tipus mitjançant color,
*   coherència total amb la planificació descrita en aquest document.

No es tracta només d’una representació gràfica, sinó d’una eina de control i comprensió del projecte.

***

## 8. Conclusions

Aquest lliurament demostra:

*   capacitat de planificar de manera professional,
*   comprensió real de dependències i colls d’ampolla,
*   estimacions temporals realistes,
*   ús adequat de la documentació com a element clau,
*   criteri propi en la presa de decisions.

El resultat diferencia clarament un treball tècnic superficial d’una **planificació pròpia d’un equip IT professional**.
