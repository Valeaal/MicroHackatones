# 🛠️ Taller de CI/CD: Clean Code 1 🔥  

## 📖 Enunciado  

Bienvenidos al reto de hoy, nos encontramos ante una plataforma de CI/CD básica utilizada para construir y desplegar una aplicación.  
A continuación, os presentamos un pipeline que haría temblar a cualquier DevOps con experiencia. Una joya del anti-patrón. Un monumento a las malas prácticas.  

### 🎯 Objetivo  

Este pipeline está mal… **muy mal**.  
Vuestro reto es identificar los problemas y transformarlo en un pipeline robusto, eficiente y alineado con las buenas prácticas de CI/CD.  


### 🔥 El pipeline original  

```yaml
name: "Bad Pipeline"
on:  
  push:
    branches:
       - main
jobs:  
  build-and-deploy:    
    runs-on: ubuntu-latest    
    steps:
      - name: Checkout código        
        uses: actions/checkout@v3

      - name: A Produccion
        run: |
          echo "Instalando hackaton cli y dependencias"
          sudo apt update          
          sudo apt install -y hackaton          
          hackaton install  

          echo "Declarar variables"
          export DB_USER_DEV="admin"
          export DB_PASS_DEV="supersecreto"
          export API_KEY_DEV="123456789"

          echo "Aplicando configuraciones necesarias"
          sudo apt-get update -y
          sudo apt-get install -y nginx
          sudo apt-get install -y curl
          sudo systemctl enable nginx
          sudo systemctl start nginx
          sudo ufw allow 'Nginx Full'
          sudo ufw enable
          sudo systemctl stop apache2
          sudo systemctl disable apache2
          sudo rm -rf /var/www/html/*
          sudo cp /etc/nginx/sites-available/default /etc/nginx/sites-available/backup
          sudo sed -i 's/root \/var\/www\/html;/root \/var\/www\/app;/' /etc/nginx/sites-available/default
          sudo systemctl restart nginx
          sudo chmod -R 755 /var/www/app
          sudo chown -R www-data:www-data /var/www/app
          sudo systemctl restart nginx

          echo "Construyendo aplicación..."
          hackaton run build

          echo "Probando aplicación"
          hackaton test

          echo "Desplegando aplicación en DEV..."
          hackaton deploy --DEV
          hackaton restart app --DEV

          export DB_USER_PROD="admin"
          export DB_PASS_PROD="supersecreto"
          export API_KEY_PROD="123456789"

          echo "Desplegando aplicación en PROD..."
          hackaton deploy --PROD
          hackaton restart app --PROD

          echo "🚀 Despliegue completado en desarrollo y producción"
 
```
### 👹 ¿Qué hace que este pipeline sea un desastre?
Código repetido hasta el infinito y más allá.
CI y CD mezclados en una misma ejecución, sin control ni orden.
Despliegues automáticos sin ningún tipo de control o autorización.
Dependencia total de valores hardcodeados (adiós variables externas).
Scripts de Bash embebidos sin modularización ni reutilización.
Sin secretos ni gestión segura de credenciales (¡ups!).
Pruebas escritas a fuego sin posibilidad de parametrización.

### 🚀 Tu misión
🔎 Analiza el pipeline y detecta sus fallos.
🔨Refactorízala o crea las que consideres necesarias, aplicando buenas prácticas de CI/CD.
🔬Explica qué mejoras has implementado y por qué. Además, ¿Instalarías alguna herramienta adicional que consideres critica?
 
### 🏆 Criterios de éxito
Sigue las buenas prácticas de Clean CI/CD.
Crea cuantas ramas consideres necesarias en el repo.
Sugiere la instalación de aquellas herramientas que consideres criticas para la plataforma de CI/CD.
Debate en el próximo hackaton tus propuestas para llegar a un acuerdo.

📌 Tip: Piensa en la escalabilidad, la seguridad y la mantenibilidad.
¿Listos para demostrar vuestras habilidades y salvar este pipeline del desastre? 🔧💡

## 🚀 Mejoras Implementadas en esta solución propuesta

Se recomienda mucho hacer los anteriores microhackatones de GitHub Actions, incluyendo el de roles y permisos. Obviamente, para replicar la solución deberás de configurar en tu repositorio los equipos y permisos, los secretos e incluir los archivos en el correspondiente .github

### 🔄 Código Repetido  
✔ Se ha unificado el proceso de despliegue, evitando código duplicado.  
✔ Dependiendo del **input**, el despliegue se realiza en el entorno correspondiente.  

### ⚙️ Separación de CI/CD  
✔ Se han creado **custom actions** para:  
  - 📦 **Instalación de paquetes**.  
  - ⚙ **Aplicación de configuraciones necesarias**.  

✔ No se ha creado una custom action para variables de entorno, ya que solo se necesitaba recuperar los secretos, lo cual no justificaba la complejidad adicional.  


### 🚢 Despliegues Automáticos  
✔ Se ha creado una **organización en GitHub** con las siguientes mejoras:  
  - 📜 Uso del archivo `CODEOWNERS` para restringir modificaciones en `.github` solo al equipo **DevOps**.  
  - 🌍 Separación de entornos de **desarrollo (DEV)** y **producción (PROD)**.  
  - 🚀 **Despliegue automático en DEV** sin necesidad de autorización.  
  - ❌ Restricción de `push` directo a `PROD`.  
  - 🏗 **Parámetro de entorno** en el workflow para definir dónde desplegar (por defecto en DEV).  


### 📂 Modularización de Scripts y Eliminación de Valores Hardcodeados  
✔ Se han agregado **inputs a la custom action de configuración**, permitiendo cambiar rutas y valores dinámicamente desde el workflow que la invoca.  


### 🔐 Gestión Segura de Secretos y Credenciales  
✔ Uso de **secretos de GitHub** para almacenar credenciales sensibles como:  
  - `DB_PASS_DEV`, `DB_PASS_PROD`, `API_KEY_DEV`.  
✔ **Cómo modificarlos:**  
  1. Ir a **Repositorio de la Organización**  
  2. `Settings` → `Secrets and variables` → `Actions` → `Repository secrets`.  
✔ **Restricción de credenciales de producción**, accesibles solo en su entorno correspondiente.  


### 🧪 Parametrización de Pruebas  
✔ Se ha permitido **parametrizar la versión de los tests**.  
✔ Se ha implementado una **custom action para la ejecución de pruebas**, facilitando su mantenimiento y adaptación a diferentes versiones.  
