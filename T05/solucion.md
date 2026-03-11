# T05 — Guía paso a paso

**Protección de datos: cifrado con VeraCrypt y verificación de integridad con SHA‑256**

***

# 1. TAREA 1 — Cifrado simétrico con VeraCrypt (Confidencialidad)

## 1.1. Preparar el entorno

1.  En el Escritorio de Windows, crear una carpeta llamada:


<!---->

    PENDRIVE_SIMULADO

2.  Instalar VeraCrypt desde su página oficial.

***

<img src="IMG/1.png" alt="2" width="600" height="auto">

## 1.2. Crear un contenedor cifrado (.hc)

Abrir VeraCrypt y seguir estos pasos:


1.  Click en **Create Volume**.
2.  Seleccionar **Create an encrypted file container** → Next.
<img src="IMG/3.png" alt="2" width="600" height="auto">
3.  Seleccionar **Standard VeraCrypt volume** → Next.
<img src="IMG/4.png" alt="4" width="600" height="auto">
4.  Click en **Select File…**  
    Guardar en:  
    Escritorio → PENDRIVE\_SIMULADO  
    Nombre del archivo:
        examen_seguro.hc
        <img src="IMG/5.png" alt="5" width="600" height="auto">
5.  Next.

**Configuración de cifrado:**

*   Encryption Algorithm: **AES**
*   Hash Algorithm: **SHA‑256**
<img src="IMG/6.png" alt="5" width="600" height="auto">
6.  Tamaño del volumen:
        100 MB
        <img src="IMG/7.png" alt="5" width="600" height="auto">

7.  Contraseña robusta (mínimo 14 caracteres).
<img src="IMG/8.png" alt="8" width="600" height="auto">
8.  Sistema de archivos: **NTFS**.
<img src="IMG/10.png" alt="8" width="600" height="auto">
9.  Click en **Format**.  
    Al finalizar aparecerá: *“Volume created successfully”*.
<img src="IMG/11.png" alt="8" width="600" height="auto">
<img src="IMG/12.png" alt="8" width="600" height="auto">
***

## 1.3. Montar el contenedor cifrado (.hc)

1.  En VeraCrypt, seleccionar una letra de unidad libre (ejemplo: **Z:**)
2.  Click en **Select File…**, elegir:
<!---->

    PENDRIVE_SIMULADO\examen_seguro.hc
3.  Click en **Mount**.
<img src="IMG/13.png" alt="8" width="600" height="auto">
4.  Introducir la contraseña.
<img src="IMG/15.png" alt="8" width="600" height="auto">
5.  Comprobar que aparece una unidad nueva (Z:) en el Explorador.
<img src="IMG/16.png" alt="8" width="600" height="auto">
***

## 1.4. Crear el archivo de examen dentro del volumen cifrado

En la unidad Z:

1.  Crear un archivo llamado:

<!---->

    EXAMEN_FINAL_SEGURETAT.txt

<img src="IMG/17.png" alt="8" width="600" height="auto">

2.  Escribir contenido de prueba (preguntas de examen).

<img src="IMG/18.png" alt="8" width="600" height="auto">

3.  Guardar y cerrar.

***

## 1.5. Demostrar que el archivo es inaccesible sin montar el volumen

1.  En VeraCrypt, click en **Dismount**.

<img src="IMG/19.png" alt="19" width="600" height="auto">

2.  Desde el Explorador, intentar abrir:

<!---->

    PENDRIVE_SIMULADO\examen_seguro.hc

Resultado esperado:

*   Windows no lo abre como carpeta.
*   Solo ofrece abrirlo con VeraCrypt (pero sin acceso al contenido).

Esto demuestra la confidencialidad.

***

# 2. TAREA 2 — Verificación de integridad con SHA‑256

## 2.1. Crear archivo original

En el Escritorio, crear:

    nota_final_curs.txt

<img src="IMG/22.png" alt="19" width="600" height="auto">

Contenido:

    L'alumne ha aprovat amb un 5

<img src="IMG/23.png" alt="19" width="600" height="auto">

***

## 2.2. Calcular hash SHA‑256 del archivo original

Abrir **PowerShell (no administrador)** y ejecutar:

```
Get-FileHash "$env:USERPROFILE\Desktop\nota_final_curs.txt" -Algorithm SHA256
```
<img src="IMG/24.png" alt="19" width="600" height="auto">

Guardar el hash mostrado.

***

## 2.3. Modificar el archivo

Editar el archivo y cambiar únicamente el número:

Antes:

    L'alumne ha aprovat amb un 5

Después:

    L'alumne ha aprovat amb un 9

<img src="IMG/25.png" alt="19" width="600" height="auto">

Guardar.

***

## 2.4. Calcular nuevamente el hash SHA‑256

Ejecutar en PowerShell:

```
Get-FileHash "$env:USERPROFILE\Desktop\nota_final_curs.txt" -Algorithm SHA256
```
<img src="IMG/26.png" alt="19" width="600" height="auto">

Comparar los dos valores:  
El hash cambia completamente incluso por un solo carácter modificado.

***
