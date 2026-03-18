
# Guía técnica de implementación: Migración a Nginx con multidominio, HTTPS forzado y HTTP/2

Autor: Santiago Hernández\
Entorno: Práctica local en VirtualBox\
Fecha de ejecución: 05/03/2026

***

## 1. Objetivo

Replicar la infraestructura levantada previamente con Apache utilizando **Nginx** en **Ubuntu Server**, con:

*   Multidominio (dos server blocks independientes).
*   Páginas de error 404 personalizadas.
*   **HTTPS** con certificados autofirmados por dominio.
*   Redirección forzada **HTTP → HTTPS**.
*   **HTTP/2** activado y verificado.
*   Comprobaciones desde cliente Windows y desde el propio servidor.

La guía está escrita en primera persona y refleja exactamente **lo que se configuró y funcionó** en el entorno de práctica, paso a paso.

***

## 2. Arquitectura y topología de red

### 2.1 Máquinas utilizadas

*   **Servidor**: Ubuntu Server (Nginx, certificados, vhosts).
*   **Cliente**: Windows 11 (navegador para pruebas y edición de `hosts`).

> Opcionalmente, también se podría usar un cliente Linux Desktop, pero en esta práctica usé Windows 11.

### 2.2 Adaptadores de red en VirtualBox

**Servidor (Ubuntu Server)**

*   Adaptador 1: **NAT** → acceso a Internet para `apt update/upgrade` e instalación de paquetes.
*   Adaptador 2: **Sólo-anfitrión (Host‑Only)** → red local entre anfitrión y VMs para SSH y acceso desde el cliente.

**Cliente (Windows 11)**

*   Adaptador 1: **Sólo‑anfitrión (Host‑Only)** → acceso al servidor por la red local de laboratorio.

### 2.3 Direccionamiento IP

*   Red Host‑Only de VirtualBox: `192.168.56.0/24`.
*   **Servidor Ubuntu**: `192.168.56.117` (asignada por DHCP del adaptador Host‑Only).
*   **Cliente Windows**: `192.168.56.128` (asignada por DHCP del adaptador Host‑Only).
*   El anfitrión (PC real) suele ser `192.168.56.1`.

### 2.4 Comprobaciones de conectividad

Desde Windows (anfitrión o VM cliente):

```
ping 192.168.56.117
```

Desde Ubuntu:

```
ping 192.168.56.128
```
***

## 3. Preparación del entorno

### 3.1 Acceso por SSH desde el anfitrión

Desde Windows:

```
ssh <usuario>@192.168.56.117
```

> Nota: si Windows no tiene habilitado el cliente SSH, se activa en “Características opcionales de Windows”.

### 3.2 Desinstalación/paro de Apache

En mi caso, **Apache no estaba instalado**. Dejo los comandos que habría usado en caso de estar presente:

```
sudo systemctl stop apache2
sudo systemctl disable apache2
sudo apt remove apache2 -y
sudo apt autoremove -y
```

### 3.3 Instalación y arranque de Nginx

```
sudo apt update
sudo apt upgrade -y
sudo apt install nginx -y
```
<img src="IMG/1.png" alt="1" width="600" height="auto">


Verificación:

```
systemctl status nginx
```
<img src="IMG/2.png" alt="2" width="600" height="auto">

```
sudo ss -tulnp | grep nginx
```

<img src="IMG/3.png" alt="3" width="600" height="auto">

`http://192.168.56.117`

<img src="IMG/4.png" alt="4" width="600" height="auto">

***

## 4. Multidominio con Server Blocks

### 4.1 Estructura de contenidos

Se crearon las dos raíces web:

```
sudo mkdir -p /var/www/nexus
sudo mkdir -p /var/www/academia
```
<img src="IMG/5.png" alt="5" width="600" height="auto">

Archivos de índice iniciales:

```
echo "<h1>Web Nexus con Nginx</h1>" | sudo tee /var/www/nexus/index.html
echo "<h1>Web Academia con Nginx</h1>" | sudo tee /var/www/academia/index.html
```

<img src="IMG/6.png" alt="6" width="600" height="auto">

Permisos:

```
sudo chown -R www-data:www-data /var/www/nexus
sudo chown -R www-data:www-data /var/www/academia
```

<img src="IMG/7.png" alt="7" width="600" height="auto">

### 4.2 Creación de server blocks

Archivo: `/etc/nginx/sites-available/nexus.local`

<img src="IMG/8.png" alt="8" width="600" height="auto">

Archivo: `/etc/nginx/sites-available/academia.local`

<img src="IMG/9.png" alt="9" width="600" height="auto">

Activación y limpieza del default:

```
sudo ln -s /etc/nginx/sites-available/nexus.local /etc/nginx/sites-enabled/
sudo ln -s /etc/nginx/sites-available/academia.local /etc/nginx/sites-enabled/
```

<img src="IMG/10.png" alt="10" width="600" height="auto">

```
sudo rm /etc/nginx/sites-enabled/default
sudo nginx -t
```

<img src="IMG/11.png" alt="11" width="600" height="auto">

```
sudo systemctl restart nginx
```

<img src="IMG/12.png" alt="12" width="600" height="auto">

### 4.3 Resolución de nombres en el cliente Windows

Edición del archivo `hosts`:

Ruta: `C:\Windows\System32\drivers\etc\hosts`

Entradas añadidas:

    192.168.56.117   nexus.local
    192.168.56.117   academia.local

<img src="IMG/13.png" alt="13" width="600" height="auto">


Observación práctica: si el editor obliga a guardar como `.txt`, renombré como **Administrador**:

```
ren C:\Windows\System32\drivers\etc\hosts.txt hosts
```

<img src="IMG/14.png" alt="14" width="600" height="auto">

***

## 5. Error 404 personalizado

### 5.1 Contenido de páginas de error

Rutas:

    /var/www/nexus/errors/404.html
    /var/www/academia/errors/404.html

Ejemplo de contenido (Nexus), escrito manualmente con `nano`:



Permisos:

```
sudo mkdir -p /var/www/nexus/errors /var/www/academia/errors
```

<img src="IMG/16.png" alt="16" width="600" height="auto">

```
sudo chown -R www-data:www-data /var/www/nexus/errors
sudo chown -R www-data:www-data /var/www/academia/errors
```

<img src="IMG/19.png" alt="19" width="600" height="auto">

### 5.2 Pèrsonalizar archivo de error 404

```
sudo nano /var/www//nexus/errors/404.html
```

<img src="IMG/20.png" alt="20" width="600" height="auto">


### 5.3 Integración en Nginx

`/etc/nginx/sites-available/nexus.local`:

<img src="IMG/22.png" alt="22" width="600" height="auto">

`/etc/nginx/sites-available/academia.local`:

<img src="IMG/21.png" alt="21" width="600" height="auto">


Aplicación:

```
sudo nginx -t
sudo systemctl restart nginx
```

<img src="IMG/23.png" alt="23" width="600" height="auto">
<img src="IMG/24.png" alt="24" width="600" height="auto">


Pruebas:

*   `http://nexus.local/ruta-inexistente` → muestra 404 personalizado.
  
    <img src="IMG/26.png" alt="26" width="600" height="auto">

*   `http://academia.local/archivo-que-no-existe` → muestra 404 personalizado.
  
    <img src="IMG/27.png" alt="27" width="600" height="auto">

***

## 6. HTTPS con certificados autofirmados, redirección 80→443 y HTTP/2

### 6.1 Generación de certificados autofirmados

Se generó **un certificado por dominio**:

```
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/nexus.local.key -out /etc/ssl/certs/nexus.local.crt
```

<img src="IMG/28.png" alt="28" width="600" height="auto">

```
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/academia.local.key -out /etc/ssl/certs/academia.local.crt
```

<img src="IMG/29.png" alt="29" width="600" height="auto">

Los **Common Name (CN)** usados fueron `nexus.local` y `academia.local`.

Permisos de las claves privadas: ya quedaron como `-rw------- root:root` (equivalente a `chmod 600`).

Verificación:

```
sudo ls -l /etc/ssl/private
```

<img src="IMG/30.png" alt="30" width="600" height="auto">

### 6.2 Configuración Nginx con SSL, redirección y HTTP/2
```
sudo nano /etc/nginx/sites-available/nexus.local
```
(contenido completo final):

<img src="IMG/31.png" alt="31" width="600" height="auto">

```
sudo nano /etc/nginx/sites-available/academia.local
```
(contenido completo final):

<img src="IMG/32.png" alt="32" width="600" height="auto">

Aplicación y verificación:

```
sudo nginx -t
sudo systemctl restart nginx
```

<img src="IMG/33.png" alt="33" width="600" height="auto">


### 6.3 Comportamiento esperado en navegador

Al acceder a `http://nexus.local` o `http://academia.local`, el navegador **redirige a HTTPS** y muestra aviso de seguridad:

*   Indicador “No seguro” o “Su conexión no es privada”.
*   Código típico: `NET::ERR_CERT_AUTHORITY_INVALID`.

Esto es **correcto** con certificados autofirmados en entorno de pruebas.

<img src="IMG/36.png" alt="36" width="600" height="auto">
<img src="IMG/37.png" alt="37" width="600" height="auto">

<img src="IMG/38.png" alt="38" width="600" height="auto">
<img src="IMG/39.png" alt="39" width="600" height="auto">

### 6.4 Verificación de HTTP/2

Para que el servidor Ubuntu resuelva los nombres locales, añadí entradas al `/etc/hosts` del **servidor**:

```
sudo nano /etc/hosts
```

<img src="IMG/50.png" alt="50" width="600" height="auto">


Pruebas HTTP/2:

```
curl -I --http2 -k https://nexus.local
curl -I --http2 -k https://academia.local
```

Salida obtenida:

<img src="IMG/41.png" alt="41" width="600" height="auto">

***

## 7. Pruebas y validación

### 7.1 Desde el cliente Windows

*   `http://nexus.local` → redirección a `https://nexus.local` con aviso self‑signed.
*   `http://academia.local` → redirección a `https://academia.local` con aviso self‑signed.
*   Navegador en Herramientas de desarrollador → columna **Protocol** muestra `h2`.

### 7.2 Desde el servidor Ubuntu

*   `curl -I --http2 -k https://nexus.local` → `HTTP/2 200`.
*   `curl -I --http2 -k https://academia.local` → `HTTP/2 200`.

### 7.3 Estado de Nginx y puertos

```bash
sudo systemctl status nginx
sudo ss -tulnp | grep :80
sudo ss -tulnp | grep :443
```

> \[Captura: `nginx -t` “syntax is ok / test is successful”]

***

## 8. Incidencias encontradas y resolución

1.  **Error al lanzar `sudo nginx -`**
    *   Síntoma: `bind() to 0.0.0.0:80 failed (98: Address already in use)`
    *   Causa: `sudo nginx -` intentó arrancar otro proceso encima del ya activo.
    *   Solución: `sudo systemctl restart nginx` y usar siempre `systemctl` para gestionar el servicio.

2.  **`curl: (6) Could not resolve host: nexus.local` (en el servidor)**
    *   Causa: el servidor Ubuntu no tenía resoluciones locales en `/etc/hosts`.
    *   Solución: añadir `nexus.local` y `academia.local` apuntando a `192.168.56.117` en `/etc/hosts`.

3.  **Edición del archivo `hosts` en Windows**
    *   Problema: el editor guardó como `hosts.txt`.
    *   Solución: renombrar como Administrador:\
        `ren C:\Windows\System32\drivers\etc\hosts.txt hosts`.

4.  **`chmod: cannot access '/etc/ssl/private/*.key'`**
    *   Causa: uso accidental de comillas, impidiendo la expansión del comodín `*`, y además las claves ya tenían permisos `-rw-------` (equivalente a 600).
    *   Solución: no es necesario `chmod` adicional; verificado con `sudo ls -l /etc/ssl/private`.

***

## 9. Buenas prácticas aplicadas

*   Separación clara de entornos: NAT para Internet y Host‑Only para laboratorio.
*   Server Blocks por dominio con raíz, índice y 404 dedicados.
*   Bloque `location /errors/ { internal; }` para evitar acceso directo a páginas de error.
*   Redirección 301 permanente de HTTP a HTTPS.
*   Certificados por dominio y claves privadas con permisos restrictivos.
*   Verificación de configuración con `nginx -t` antes de reiniciar.
*   Comprobaciones desde cliente y servidor, incluyendo HTTP/2 con `curl`.

***

## 10. Estado final de la solución

*   Nginx activo y escuchando en **80 y 443**.
*   Dos dominios locales: `nexus.local` y `academia.local`.
*   Redirección forzada a **HTTPS** funcionando.
*   Certificados **autofirmados** instalados por dominio.
*   Páginas 404 personalizadas operativas.
*   **HTTP/2** activado y verificado.

***

## 11. Apéndice: archivos finales

### 11.1 `/etc/nginx/sites-available/nexus.local`

```nginx
server {
    listen 80;
    server_name nexus.local;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name nexus.local;

    root /var/www/nexus;
    index index.html;

    ssl_certificate /etc/ssl/certs/nexus.local.crt;
    ssl_certificate_key /etc/ssl/private/nexus.local.key;

    error_page 404 /errors/404.html;

    location /errors/ {
        internal;
    }

    location / {
        try_files $uri $uri/ =404;
    }
}
```

### 11.2 `/etc/nginx/sites-available/academia.local`

```nginx
server {
    listen 80;
    server_name academia.local;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name academia.local;

    root /var/www/academia;
    index index.html;

    ssl_certificate /etc/ssl/certs/academia.local.crt;
    ssl_certificate_key /etc/ssl/private/academia.local.key;

    error_page 404 /errors/404.html;

    location /errors/ {
        internal;
    }

    location / {
        try_files $uri $uri/ =404;
    }
}
```

### 11.3 Entradas en `hosts`

**Windows** (`C:\Windows\System32\drivers\etc\hosts`):

    192.168.56.117   nexus.local
    192.168.56.117   academia.local

**Ubuntu Server** (`/etc/hosts`):

    192.168.56.117   nexus.local
    192.168.56.117   academia.local

***

## 12. Posibles ampliaciones futuras

*   Protección de carpeta `/private` con retorno 403 y página personalizada.
*   Compresión, cacheo y directivas de seguridad (HSTS, CSP) para producción.
*   Certificados de confianza pública con ACME/Let’s Encrypt (en entornos no locales).
*   Balanceo de carga o reverse proxy con Nginx para servicios backend.

***

## 13. Conclusión

Se ha migrado la infraestructura web a **Nginx** con **multidominio**, **errores personalizados**, **HTTPS forzado** y **HTTP/2**. La solución funciona tanto desde el cliente Windows como localmente en el servidor, y se ha documentado el procedimiento y la resolución de incidencias habituales que pueden surgir en un laboratorio con VirtualBox.

> \[Lugar para capturas: ping, SSH, página por defecto de Nginx, accesos por dominio, error 404 personalizado, aviso de privacidad por self‑signed, `nginx -t`, `curl --http2`, puertos 80/443 con `ss`].
