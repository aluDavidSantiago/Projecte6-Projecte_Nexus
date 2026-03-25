Aquí tens **la guia definitiva**, **sense emojis**, **extremadament clara**, **pas a pas**, i **pensada perquè fins i tot una persona amb zero experiència la pugui seguir**.  
Aquesta guia està dissenyada per superar un examen: és precisa, estructurada i contempla errors i solucions.

***

# GUIA COMPLETA DE VIGILÀNCIA I AUDITORIA DE SISTEMES

**(Windows Server – Tots els passos explicats sense ambigüitats)**

***

# FASE 1 – Monitorització de Recursos del Sistema

## Objectiu

Obtenir una captura real de l’ús de CPU i RAM del servidor i interpretar si està treballant correctament.

## Passos detallats

### 1. Obrir el Task Manager

Opció A:

*   Premeu les tecles: **CTRL + SHIFT + ESC**

Opció B:

*   Clic dret a la barra inferior de Windows
*   Seleccioneu: **Task Manager**

### 2. Habilitar la vista completa

Si veieu una finestra molt simple amb pocs botons:

*   A la part inferior, feu clic a **More details**.

### 3. Obrir la pestanya de rendiment

*   A la part superior, seleccioneu **Performance**.

### 4. Què ha de sortir a la captura

*   Gràfic de la CPU amb el percentatge d’ús.
*   Gràfic de la RAM amb la memòria utilitzada i la total.

Aquesta captura és obligatòria.

## Interpretació (el que has d’escriure a l’informe)

*   **Servidor sense estrès:** CPU per sota del 40 % i RAM per sota del 50 %.
*   **Càrrega normal:** CPU entre 40 % i 70 %.
*   **Risc de saturació:** CPU per sobre del 80 % de forma contínua o RAM per sobre del 80 %.

## Errors típics i solucions

### Error: No es mostra el gràfic

Solució: seleccionar manualment “CPU” o “Memory”.

### Error: No surt la vista avançada

Solució: clicar “More details”.

***

# FASE 2 – Configuració de l’Auditoria de Seguretat

## Objectiu

Configurar Windows perquè registri tots els intents d’inici de sessió, tant correctes com incorrectes.

## Passos detallats

### 1. Obrir l’editor de polítiques locals

*   Premeu **Win + R**
*   Escriviu: **gpedit.msc**
*   Premeu Enter

Si apareix un error, significa que no tens permisos d’administrador.

### 2. Ruta exacta fins a la política

Segueix exactament aquest camí:

    Configuració de l'Equip
      → Configuració de Windows
        → Configuració de Seguretat
          → Polítiques Locals
            → Política d'Auditoria

### 3. Activar les dues polítiques clau

Obre cada una de les següents i marca “Success” i “Failure”:

1.  Audit logon events
2.  Audit account logon events

Aplica els canvis i tanca.

## Errors típics i solucions

### Error: No trobo “Política d'Auditoria”

Solució: estàs en la branca d’usuari. Revisa que estàs a “Configuració de l’Equip”.

### Error: No puc editar les polítiques

Solució: comprova que estàs amb un **usuari administrador**.

***

# FASE 3 – Simulació d’Intent d’Intrusió

## Objectiu

Generar esdeveniments reals d’inici de sessió fallit perquè quedin registrats.

## Passos detallats

### 1. Tancar la sessió actual

Start → Sign out.

### 2. Fer diversos intents d’inici de sessió erronis

*   Selecciona un usuari normal (per exemple, un usuari de magatzem).
*   Escriu una contrasenya incorrecta.
*   Fes aquest intent **3 o 4 vegades**.

Això ha de generar esdeveniments de tipus “Audit Failure”.

### 3. Iniciar sessió correctament amb l’administrador

Això generarà esdeveniments “Audit Success”.

## Errors típics i solucions

### Error: L’usuari queda bloquejat als 3 intents

Solució: desbloqueja’l des de “Local Users and Groups” o Active Directory (segons el tipus de servidor).

### Error: No es generen errors

Solució: revisa la FASE 2. Si no està activada l’auditoria de “Failure”, no apareixerà res.

***

# FASE 4 – Anàlisi Forense amb Event Viewer

## Objectiu

Localitzar els intents d’inici de sessió fallit i identificar-los amb la informació detallada.

## Passos detallats

### 1. Obrir el Visor d’Esdeveniments

*   Premeu **Win + R**
*   Escriviu **eventvwr.msc**

### 2. Ruta exacta

Aneu aquí:

    Windows Logs
      → Security

### 3. Què buscar

Has de buscar entrades amb aquesta informació:

*   Nivell: Audit Failure
*   Acció: Logon
*   Identificador: Event ID 4625

### 4. Obrir un event i revisar la informació

En fer doble clic, mira:

*   Usuari que ha fallat
*   Hora exacta
*   Motiu del fall
*   Nom d’equip
*   IP d’origen (només si l’intent és remot)

### Filtrar si hi ha molts events

A la dreta, selecciona **Filter Current Log** i a “Event ID” escriu:

    4625

Així aïlles només els intents fallits.

## Event ID obligatori per l’examen

L’identificador d’error d’inici de sessió fallit és:

**4625**

***

# FASE FINAL – Documentació a lliurar

1.  Captura de Task Manager amb CPU i RAM visibles.
2.  Captura de les polítiques d’auditoria activades (Success i Failure).
3.  Captura del Visor d’Esdeveniments mostrant un dels errors.
4.  Resposta tècnica:
    *   L’Event ID dels inicis de sessió fallits és **4625**.

***

Si vols, puc generar:

*   Una **plantilla d’informe llesta per entregar**.
*   Un **resum ultra curt per estudiar**.
*   Un **model de captures fictícies** per entendre com han de quedar.

Només demana-ho.
