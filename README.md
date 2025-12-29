# 🛠 Generador de índices para Repositorios en GitHub

Mantener un repositorio bien documentado es clave, pero actualizar manualmente el índice de archivos puede convertirse en una tarea repetitiva y propensa a errores, especialmente en proyectos que crecen rápido.

En este artículo explico una solución automatizada basada en Python + GitHub Actions que genera y mantiene actualizado un índice global del proyecto dentro del README.md, sin intervención manual.

<img width="821" height="813" alt="Captura de pantalla 2025-12-30 003214" src="https://github.com/user-attachments/assets/36c209be-6f03-4a1b-95e8-66e627b9bb57" />


## 🎯 ¿Qué hace esta automatización?

El objetivo principal es:  
🔹Escanear el repositorio de forma controlada  
🔹Generar un índice en formato Markdown  
🔹Insertarlo automáticamente en el README  
🔹Mantenerlo actualizado en cada push a main  

Todo esto respetando:  
🔹Profundidad máxima  
🔹Exclusión de carpetas y archivos irrelevantes  
🔹Tipos de archivos permitidos  
🔹Compatibilidad con rutas y enlaces web  


## ⚙️ Configuración Inicial

Para utilizar esta automatización, solo necesitas crear dos elementos clave en tu repositorio:

### 1️⃣ Script en Python
🔹Crea un directorio llamado scripts/ y dentro añade el archivo:
``` python
scripts/
└── generate_readme.py
```
Este script es el encargado de: 
🔹Escanear el proyecto  
🔹Construir el índice en Markdown  
🔹Insertarlo entre los marcadores:  
``` python
<!-- INDEX_START -->
<!-- INDEX_END -->
```
dentro del README.md

⚠️ Importante: Asegúrate de que tu README contiene estos marcadores para que el índice se actualice correctamente.

### 2️⃣ Workflow de GitHub Actions

Crea la siguiente estructura:
``` python
.github/
└── workflows/
    └── update-readme.yml
```

Este archivo define el flujo automático que:  
🔹Se ejecuta al hacer push a main  
🔹Descarga el repositorio  
🔹Ejecuta el script generate_readme.py  
🔹Commitea y sube los cambios del README automáticamente  


``` yml
name: 🛠 Auto Update README

on:
  push:
    branches:
      - main

permissions:
  contents: write

jobs:
  update-readme:
    runs-on: ubuntu-latest

    steps:
      - name: 📦 Checkout repository
        uses: actions/checkout@v4

      - name: 🐍 Run Python script
        run: python scripts/generate_readme.py

      - name: 📤 Commit changes
        run: |
          git config user.name "github-actions"
          git config user.email "github-actions@github.com"
          git add README.md
          git commit -m "🤖 Auto-update project index" || exit 0
          git push
```


## 📂 Validación Inteligente de Archivos y Carpetas

El script implementa filtros claros:  
🔹Evita carpetas ocultas o internas  
🔹Excluye el README.md raíz para prevenir autorreferencias  
🔹Permite solo extensiones definidas explícitamente  
🔹Esto es clave para evitar ruido en proyectos grandes.  


## 🔍 Escaneo Recursivo del Repositorio

La función principal realiza un escaneo recursivo:  
🔹Recorre carpetas de forma ordenada  
🔹Respeta el nivel de profundidad  
🔹Genera rutas relativas compatibles con GitHub  

Usa iconos visuales:

📁 Carpetas 
📄 Archivos 

Ejemplo de salida generada:
``` python
- 📁 **[scripts](scripts)**
  - 📄 [generate_readme.py](scripts/generate_readme.py)
```


## 🧾 Generación del Markdown

Una vez recopilada la estructura, el script:  
🔹Aplica indentación según profundidad  
🔹Construye una lista Markdown jerárquica  
🔹Inserta el contenido entre los marcadores del README  

Si el índice ya existe, se reemplaza automáticamente; si no, se añade al final del archivo. 


## 🤖 Automatización con GitHub Actions 

El proceso se completa con un workflow de GitHub Actions que: 
🔹Se ejecuta en cada push a main  
🔹Descarga el repositorio  
🔹Ejecuta el script en Python  
🔹Commitea automáticamente los cambios en el README  
🔹Todo esto sin intervención humana.  


## ✅ Beneficios Clave

🔹 README siempre actualizado   
🔹 Documentación automática y consistente    
🔹 Ahorro de tiempo en proyectos activos    
🔹 Ideal para repositorios técnicos y educativos    
🔹Escalable y fácilmente configurable  

