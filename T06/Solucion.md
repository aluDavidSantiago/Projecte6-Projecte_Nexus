# Proyecto Nexus — PoC PKI Corporativa y Firma Digital (Grupo 8)

**Autores**:

*   **Administrador (Servidor Ubuntu)**: *S. Moreno* (simulado por S. Hernández)
*   **Cliente (Windows 11)**: *S. Hernández*

**Entorno**: VirtualBox (NAT + Host‑Only)

***

## 1. Objetivo

Demostrar una **PKI corporativa propia** (CA interna) que permita:

*   Emitir **certificados de usuario** para empleados.
*   Instalar los certificados en **Windows 11**.
*   **Firmar digitalmente** un PDF con **Adobe Acrobat Reader** y **validar** la firma.

***

## 2. Arquitectura y direccionamiento

*   **Servidor CA (Ubuntu Server)**
    *   Nombre (FQDN): `ca.nexus8.test`
    *   IP Host-only (Grupo A, nº 17): `192.168.2.17/24` (sin GW ni DNS)
    *   IP NAT: DHCP (10.0.2.x)

*   **Cliente (Windows 11)**
    *   IP Host-only (Grupo A, nº 10): `192.168.2.10/24` (sin GW ni DNS)
    *   IP NAT: DHCP (10.0.2.x)

**VirtualBox (ambas VMs)**

*   Adaptador 1: **NAT**
*   Adaptador 2: **Red solo‑anfitrión** (Host‑Only)

Justificación: en WiFi, el modo Puente puede fallar. Host‑Only + NAT es estable y cumple los requisitos de la práctica.

***

## 3. Fase 1 — Preparación de VMs y Red

### 3.1. Cliente (Windows 11) — VM

*   2 vCPU, 4 GB RAM (recomendado 6–8 GB), 40 GB disco dinámico.
*   **BIOS/Legacy** (EFI desactivado).
*   Red:
    *   Adaptador 1: NAT
    *   Adaptador 2: Host‑Only

**Snapshot inicial**: `Cliente-Inicial`.

### 3.2. Servidor (Ubuntu Server) — VM

*   2 vCPU, 2 GB RAM, 20 GB disco dinámico.
*   **BIOS/Legacy** (EFI desactivado).
*   Red:
    *   Adaptador 1: NAT
    *   Adaptador 2: Host‑Only

**Snapshot inicial**: `Servidor-Inicial`.

**\[Captura 1]** Ajustes de red en VirtualBox (NAT + Host‑Only) para ambas VMs.

***

## 4. Fase 2 — Instalación y configuración del Servidor CA (Ubuntu)

### 4.1. Instalación de Ubuntu Server

*   Idioma: Español.
*   Teclado: Español.
*   Interfaz NAT en **DHCP**.
*   Interfaz Host‑Only **estática**:
        IP: 192.168.2.17/24
        Gateway: (vacío)
        DNS: (vacío)
*   Hostname: **ca.nexus8.test**
*   Instalar **OpenSSH Server**.

**\[Captura 2]** Pantalla de login mostrando `ca.nexus8.test`.  
**Comprobaciones**:

```bash
hostname            # ca.nexus8.test
ip a                # Ver 10.0.2.x (NAT) y 192.168.2.17/24 (Host-Only)
ping -c 3 google.com
```

### 4.2. Actualización y SSH

```bash
sudo apt update && sudo apt upgrade -y
sudo systemctl enable --now ssh
sudo systemctl status ssh   # Active (running)
```

**\[Captura 3]** `systemctl status ssh` en activo.

***

## 5. Fase 3 — Configuración de OpenSSL y creación de la CA

### 5.1. Estructura de la CA

```bash
sudo mkdir -p /etc/ssl/CA/{certs,crl,newcerts,private}
sudo touch /etc/ssl/CA/index.txt
echo "C001" | sudo tee /etc/ssl/CA/serial
sudo chmod 700 /etc/ssl/CA/private
```

### 5.2. Configuración de `openssl.cnf`

Editar:

```bash
sudo nano /etc/ssl/openssl.cnf
```

Añadir/normalizar el bloque (sin duplicados):

```ini
[ ca ]
default_ca = CA_default

####################################################################
[ CA_default ]

dir               = /etc/ssl/CA
certs             = $dir/certs
crl_dir           = $dir/crl
new_certs_dir     = $dir/newcerts
database          = $dir/index.txt
serial            = $dir/serial

certificate       = $dir/cacert.pem
private_key       = $dir/private/cakey.pem

crlnumber         = $dir/crlnumber
crl               = $dir/crl.pem

default_days      = 365
default_crl_days  = 30
default_md        = sha256

x509_extensions   = usr_cert
name_opt          = ca_default
cert_opt          = ca_default

policy            = policy_match
```

### 5.3. Crear clave privada y certificado raíz (CA)

```bash
sudo openssl req -new -x509 -days 3650 \
  -keyout /etc/ssl/CA/private/cakey.pem \
  -out /etc/ssl/CA/cacert.pem
```

**Valores recomendados**:

*   C: `ES`
*   ST/L: `Barcelona`
*   O: `Nexus 8`
*   OU: `CA`
*   **CN: `ca.nexus8.test`** (crítico)
*   Email: `admin@nexus8.test`
*   Pass CA: `nexusCA123` (memorizada para firmar)

**\[Captura 4]** `ls -l /etc/ssl/CA` mostrando `cacert.pem`, `index.txt`, `serial`, carpetas, y `private/`.

***

## 6. Fase 4 — Certificado de Usuario (Cliente)

### 6.1. Clave y CSR (solicitud)

```bash
openssl req -new -keyout userkey.pem -out userreq.csr
```

**Valores**:

*   C: `ES`, ST/L: `Barcelona`
*   O: `Nexus 8`, OU: `Users`
*   **CN**: `Santiago Hernandez`
*   Email: `hernandez@nexus8.test`
*   Pass clave usuario (PFX): **`123456`**

### 6.2. Firmar el certificado con la CA

```bash
sudo openssl ca -in userreq.csr -out usercert.pem
# Pass de la CA: nexusCA123
# Responder 'y' a la firma
```

### 6.3. Exportar a PKCS#12 (para Windows)

```bash
openssl pkcs12 -export -out CertUser.pfx -inkey userkey.pem -in usercert.pem
# Contraseña exportación: 123456
```

**\[Captura 5]** `ls -l user*` y `ls -l *.pfx`.

***

## 7. Fase 5 — Distribución de Certificados

### 7.1. Vía SCP (básico)

Permisos temporales:

```bash
chmod 777 /etc/ssl/CA/cacert.pem
chmod 777 CertUser.pfx
```

Desde **Windows (PowerShell)**:

```powershell
scp usuari@192.168.2.17:/etc/ssl/CA/cacert.pem C:\Users\cliente\Desktop\
scp usuari@192.168.2.17:/home/usuari/CertUser.pfx C:\Users\cliente\Desktop\
```

**\[Captura 6]** Archivos en escritorio: `cacert.pem` y `CertUser.pfx`.

> **Opcional (realista)**: Portal web simple con Apache/Nginx y enlaces a ambos ficheros.

***

## 8. Fase 6 — Configuración del Cliente (Windows 11)

### 8.1. IP estática (Host‑Only)

    Adaptador: VirtualBox Host‑Only Ethernet Adapter
    IPv4 manual:
      IP: 192.168.2.10
      Máscara: 255.255.255.0
      Puerta de enlace: (vacío)
      DNS: (vacío)

**Comprobación**:

```powershell
ping 192.168.2.17
ipconfig
```

### 8.2. Resolver el FQDN del servidor

Editar `C:\Windows\System32\drivers\etc\hosts` (Bloc de notas como admin) y añadir:

    192.168.2.17   ca.nexus8.test

**Comprobación**:

```powershell
ping ca.nexus8.test
```

### 8.3. Instalar Adobe Reader (winget)

```powershell
winget install Adobe.Acrobat.Reader.64-bit --accept-source-agreements --accept-package-agreements
```

### 8.4. Instalar **certificado raíz** de la CA

*   `certmgr.msc` → **Entidades emisoras de certificados raíz de confianza → Certificados**
*   Botón derecho → **Importar** → `cacert.pem`
*   **Colocar en este almacén**: *raíz de confianza*.

**\[Captura 7]** CA `ca.nexus8.test` visible en el almacén raíz.

### 8.5. Instalar **certificado personal** (PFX)

*   `certmgr.msc` → **Personal → Certificados**
*   Botón derecho → Importar → `CertUser.pfx` → Pass: `123456`
*   Debe mostrar:
    *   Sujeto: **Santiago Hernandez**
    *   Emisor: **ca.nexus8.test**
    *   **Tiene clave privada** (icono llave)

**\[Captura 8]** Certificado personal instalado.

***

## 9. Fase 7 — Firma Digital de un PDF con Adobe Reader

### 9.1. PDF de prueba

*   Crear `Nexus-POC.pdf` (Word → Exportar a PDF, o Imprimir a PDF).

### 9.2. Firma digital (con certificado)

*   Abrir PDF en **Adobe Acrobat Reader**.
*   **Todas las herramientas → Certificados → Firmar digitalmente**.
*   Dibujar área de firma.
*   Elegir el certificado **Santiago Hernandez (emitido por ca.nexus8.test)**.
*   Introducir pass: `123456`.
*   Guardar como `Nexus-POC-SIGNED.pdf`.

### 9.3. Verificación

*   **Panel de firmas** → Comprobar:
    *   Integridad: **sin modificaciones desde la firma.**
    *   Emisor: **ca.nexus8.test**.
    *   Estado: **válida** o **validez desconocida** (por almacén interno de Adobe).
    *   *Aviso de capas*: puede aparecer, **no afecta**.

**\[Captura 9]** Panel de firmas mostrando la firma aplicada.

> Nota: si deseas que Adobe marque **“firma válida”** sin “identidad desconocida”, importa `cacert.pem` en **Adobe → Edición → Preferencias → Firmas → Identidades y certificados de confianza → Más… → Certificados de confianza → Importar → “Usar este certificado como raíz de confianza”**. **No es obligatorio para la práctica.**

***

## 10. Fase 8 — Entregables

Subir al repositorio (p. ej., `tarea-pki/`):

*   `memoria.md` (esta guía + **tus capturas** en cada fase).
*   `Nexus-POC-SIGNED.pdf` (PDF firmado).
*   **Certificado raíz** en formato **.cer (DER)** (además del `.pem` si quieres).

### 10.1. Convertir `cacert.pem` a `.cer` (DER) para Windows

```bash
openssl x509 -in /etc/ssl/CA/cacert.pem -outform der -out cacert.cer
```

Copia `cacert.cer` al repositorio.

**\[Captura 10]** Confirmación de `cacert.cer` generado.

**Árbol final sugerido:**

    tarea-pki/
    ├─ memoria.md
    ├─ cacert.cer
    └─ Nexus-POC-SIGNED.pdf

***

## 11. Resolución de problemas (Troubleshooting)

*   **El `openssl ca` falla por `serial`** → Asegúrate de usar `C001` (no empieces por `0`).
*   **Permisos** → `/etc/ssl/CA/private` debe ser `700`.
*   **CN incorrecto** → La CA debe tener **CN = ca.nexus8.test**; el usuario, su nombre.
*   **Windows no importa `.pem` en raíz** → Usar el asistente y apuntar a *raíz de confianza*; si no, convertir a `.cer` (DER).
*   **Adobe: identidad desconocida** → Importar la CA en Adobe (opcional).
*   **Bridge no funciona en WiFi** → Usar **Host‑Only + NAT** como en esta guía.
*   **NAT sin Internet** → Revisar Adaptador 1 en NAT y `ping 8.8.8.8`.
*   **El cliente no resuelve el FQDN** → Revisar `hosts` en Windows.

***

## 12. Diferencias Clave: **Clave Pública vs Clave Privada**

*   **Clave Privada**
    *   Secreta. Solo la posee el titular (CA o usuario).
    *   **Firma** documentos (o emite certificados en el caso de la CA).
    *   Su exposición compromete la identidad.

*   **Clave Pública**
    *   Compartible.
    *   **Verifica** firmas.
    *   En la CA es el **certificado raíz** (se distribuye para confiar en la CA).
    *   En el usuario, va dentro del certificado personal emitido por la CA.

En este proceso:

*   La **CA** usa su **clave privada** para **firmar** el certificado del usuario.
*   El **cliente** usa su **clave privada** para **firmar** el PDF.
*   **Acrobat/Windows** usan las **claves públicas** (certificados) para **verificar** las firmas.

***

## 13. Evidencias (capturas que añadí)

1.  **VirtualBox**: NAT + Host‑Only en ambas VMs.
2.  **Login Ubuntu** `ca.nexus8.test`.
3.  `systemctl status ssh` activo.
4.  Estructura `/etc/ssl/CA` con `cacert.pem` y `private/`.
5.  Archivos `userkey.pem`, `userreq.csr`, `usercert.pem`, `CertUser.pfx`.
6.  Escritorio Windows con `cacert.pem` y `CertUser.pfx`.
7.  CA instalada en **raíz de confianza** (certmgr.msc).
8.  Certificado personal en **Personal → Certificados**.
9.  Panel de firmas de Adobe en `Nexus-POC-SIGNED.pdf`.
10. `cacert.cer` generado (DER).

> Inserta tus capturas como:
>
> ```markdown
> ./img/capturaX.png
> ```

***

## 14. Conclusión

Se ha desplegado una **PKI corporativa** con **CA interna** en Ubuntu, se ha emitido un certificado de usuario, instalado en Windows 11 y usado para **firmar digitalmente** PDFs con **Adobe Acrobat Reader**.  
La solución cumple los objetivos de **integridad, autenticidad y no repudio** en un entorno corporativo controlado.

***

### Anexo A — Comandos rápidos (cheatsheet)

```bash
# Estructura CA
sudo mkdir -p /etc/ssl/CA/{certs,crl,newcerts,private}
sudo touch /etc/ssl/CA/index.txt
echo "C001" | sudo tee /etc/ssl/CA/serial
sudo chmod 700 /etc/ssl/CA/private

# CA raíz
sudo openssl req -new -x509 -days 3650 \
  -keyout /etc/ssl/CA/private/cakey.pem \
  -out /etc/ssl/CA/cacert.pem

# Usuario
openssl req -new -keyout userkey.pem -out userreq.csr
sudo openssl ca -in userreq.csr -out usercert.pem
openssl pkcs12 -export -out CertUser.pfx -inkey userkey.pem -in usercert.pem

# Copia a Windows (PowerShell)
scp usuari@192.168.2.17:/etc/ssl/CA/cacert.pem C:\Users\cliente\Desktop\
scp usuari@192.168.2.17:/home/usuari/CertUser.pfx C:\Users\cliente\Desktop\

# Convertir a DER (.cer)
openssl x509 -in /etc/ssl/CA/cacert.pem -outform der -out cacert.cer
```

***

¿Quieres que te entregue este `memoria.md` como archivo y una **plantilla de carpeta** con `/img` ya lista para que pegues tus capturas? Puedo generarlo y pasártelo.
