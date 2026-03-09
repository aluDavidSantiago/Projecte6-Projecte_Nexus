Aquí tienes lo que me pediste: **una guía definitiva, limpia, coherente, actualizada y en perfecto español**, escrita como si la hubieras redactado tú mismo.  
La he preparado en **Markdown**, con pasos claros, sin contradicciones y usando **las versiones correctas** que Canvas LMS exige hoy en día (Ruby 3.4.x, Node 20+, Yarn 1.22+, PostgreSQL, Redis, etc.).

---

# 📘 **Guía Definitiva: Cómo instalar Canvas LMS en Linux (Zorin/Ubuntu 22.04)**

Canvas LMS es una plataforma educativa de código abierto similar a Moodle, pero más moderna y orientada a instituciones que necesitan un entorno robusto y escalable. Esta guía explica cómo instalar Canvas LMS desde cero en Zorin OS (basado en Ubuntu 22.04), asegurando compatibilidad total entre versiones.

---

## 🧩 **1. Requisitos previos**

### **Hardware recomendado**
- 8 GB RAM  
- 4 CPU  
- 40 GB de disco  
- Conexión a Internet estable  

### **Software**
- Zorin OS 17 / Ubuntu 22.04  
- Acceso a un usuario con permisos sudo  

---

## 🔧 **2. Actualizar el sistema**

```bash
sudo apt update && sudo apt upgrade -y
sudo reboot
```

---

## 👤 **3. Crear usuario para Canvas**

```bash
sudo adduser canvas
sudo usermod -aG sudo canvas
```

Iniciar sesión como ese usuario:

```bash
su - canvas
```

---

## 📦 **4. Instalar dependencias del sistema**

```bash
sudo apt install -y git curl gnupg2 build-essential \
  software-properties-common libxml2-dev libxslt1-dev \
  libcurl4-openssl-dev libffi-dev libreadline-dev zlib1g-dev \
  libsqlite3-dev sqlite3 libpq-dev postgresql postgresql-contrib \
  redis-server imagemagick libmagickwand-dev nginx
```

---

## 🐘 **5. Configurar PostgreSQL**

```bash
sudo -u postgres createuser canvas --superuser
sudo -u postgres createdb canvas_production --owner=canvas
sudo -u postgres psql
```

Dentro de PostgreSQL:

```sql
ALTER USER canvas WITH PASSWORD 'tu_password_segura';
\q
```

---

## 💎 **6. Instalar Ruby 3.4.x con rbenv (versión requerida por Canvas)**

```bash
git clone https://github.com/rbenv/rbenv.git ~/.rbenv
echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bashrc
echo 'eval "$(rbenv init - bash)"' >> ~/.bashrc
source ~/.bashrc

git clone https://github.com/rbenv/ruby-build.git ~/.rbenv/plugins/ruby-build
rbenv install 3.4.1
rbenv global 3.4.1
gem install bundler
```

---

## 🟩 **7. Instalar Node.js 20 LTS (requerido por Canvas)**

```bash
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
sudo apt install -y nodejs
```

Verificar:

```bash
node -v
```

Debe mostrar algo como:

```
v20.x.x
```

---

## 🧶 **8. Instalar Yarn (versión correcta, no cmdtest)**

```bash
curl -fsSL https://dl.yarnpkg.com/debian/pubkey.gpg | gpg --dearmor | sudo tee /usr/share/keyrings/yarn.gpg >/dev/null
echo "deb [signed-by=/usr/share/keyrings/yarn.gpg] https://dl.yarnpkg.com/debian/ stable main" | sudo tee /etc/apt/sources.list.d/yarn.list
sudo apt update
sudo apt install --no-install-recommends yarn
```

Verificar:

```bash
yarn -v
```

---

## 📥 **9. Descargar Canvas LMS**

```bash
cd ~
git clone https://github.com/instructure/canvas-lms.git
cd canvas-lms
git checkout stable
```

---

## ⚙️ **10. Configurar archivos de Canvas**

```bash
cp config/database.yml.example config/database.yml
cp config/domain.yml.example config/domain.yml
cp config/security.yml.example config/security.yml
```

### **Editar `config/database.yml`**

```yaml
production:
  adapter: postgresql
  encoding: utf8
  database: canvas_production
  host: localhost
  username: canvas
  password: tu_password_segura
```

### **Editar `config/domain.yml`**

Si tu servidor tiene la IP **192.168.56.129**:

```yaml
production:
  domain: "192.168.56.129"
  ssl: false
```

---

## 📚 **11. Instalar dependencias Ruby y JS**

```bash
bundle install
yarn install --pure-lockfile
```

---

## 🧱 **12. Inicializar la base de datos**

```bash
RAILS_ENV=production bundle exec rake db:initial_setup
```

---

## 🎨 **13. Compilar los assets (tarda bastante)**

```bash
RAILS_ENV=production bundle exec rake canvas:compile_assets
```

---

## 🐆 **14. Crear servicio systemd para Canvas**

Crear archivo:

```bash
sudo nano /etc/systemd/system/canvas.service
```

Contenido:

```ini
[Unit]
Description=Canvas LMS
After=network.target

[Service]
Type=simple
User=canvas
WorkingDirectory=/home/canvas/canvas-lms
Environment=RAILS_ENV=production
ExecStart=/home/canvas/.rbenv/shims/bundle exec puma -C config/puma.rb
Restart=always

[Install]
WantedBy=multi-user.target
```

Activar:

```bash
sudo systemctl daemon-reload
sudo systemctl enable canvas
sudo systemctl start canvas
```

---

## 🌐 **15. Configurar Nginx como proxy**

```bash
sudo nano /etc/nginx/sites-available/canvas
```

Contenido:

```nginx
server {
    listen 80;
    server_name 192.168.56.129;

    location / {
        proxy_pass http://127.0.0.1:3000;
        proxy_set_header Host $host;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

Activar:

```bash
sudo ln -s /etc/nginx/sites-available/canvas /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl restart nginx
```

---

## 👨‍🏫 **16. Crear usuario administrador**

```bash
RAILS_ENV=production bundle exec rake canvas:initial_admin
```

---

## 🎉 **17. Acceder a Canvas**

En tu navegador:

```
http://192.168.56.129
```

Inicia sesión con el usuario administrador que creaste.

---

# ✅ **Guía completada**

Esta guía está diseñada para evitar todos los errores comunes:

- Conflictos entre versiones de Ruby  
- Node.js demasiado antiguo  
- Yarn incorrecto (cmdtest)  
- Configuración de dominio mal definida  
- Dependencias faltantes  
- Servicios sin systemd  
- Nginx sin proxy  

Si quieres, puedo prepararte también:

- Una guía para actualizar Canvas  
- Una guía para instalarlo con Docker  
- Una guía para producción con HTTPS y certificados  

Solo dímelo.
