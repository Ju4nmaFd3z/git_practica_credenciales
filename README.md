# 🧰 Práctica Git – Tokens y credenciales (Solo Consola)

> **Autor:** *Juan Manuel Fernández Rodríguez*  
> **Asignatura:** Sistemas de Gestión de Versiones – Git y GitHub  
> **Fecha:** 10-16/10/2025  

---

## 📌 Objetivos de la práctica

1. Aprender a **trabajar únicamente con la consola (Git Bash)**.  
2. **Generar un token clásico** en GitHub con los permisos mínimos necesarios.  
3. Configurar el repositorio local para **autenticación local cacheada**, sin depender de Windows Credential Manager.  
4. Documentar todo el proceso con **capturas, comandos y justificaciones técnicas**.

---

## 🧩 Parte 1 – Creación del repositorio local y remoto

### 🔹 Pasos realizados

```bash
# Crear carpeta y repo local
cd ~/Documentos
mkdir git_practica_credenciales
cd git_practica_credenciales
git init

# Crear estructura de carpetas
mkdir docs
mkdir docs/images
touch README.md
```

Después, en GitHub creamos el repositorio remoto
`https://github.com/Ju4nmaFd3z/git_practica_credenciales.git`

Y lo enlazamos con el local:

```bash
git remote add origin https://github.com/Ju4nmaFd3z/git_practica_credenciales.git
git branch -M main
```

![Creación del repositorio en GitHub](imagenes/01_repo_github.png)

---

## 🔐 Parte 2 – Generación del token clásico en GitHub

### 🔹 Ruta para generarlo

`Settings → Developer settings → Personal access tokens → Tokens (classic) → Generate new token (classic)`

### 🔹 Configuración elegida

* **Nombre:** `token-git-practica-credenciales`
* **Expiración:** `90 días`
* **Scopes:**

  * `repo`
  * Y dentro deberá estar marcada la casilla `public_repo` para que sea un repo público.

### 🔹 Justificación de permisos

El scope `repo` nos permite *leer y escribir* en repositorios a los que ya tengo acceso, lo mínimo necesario para realizar operaciones `git push` y `git pull`.
No seleccionamos permisos de administración (`admin:*`) ni de automatización (`workflow`, `packages`), siguiendo el principio de **mínimos privilegios**.

![Generación del token clásico en GitHub 1](imagenes/02_01_token.png)
![Generación del token clásico en GitHub 2](imagenes/02_02_token.png)
![Generación del token clásico en GitHub 3](imagenes/02_03_token.png)

---

## 💾 Parte 3 – Almacenamiento seguro del token

Para evitar perderlo, he guardado mi token en el **Google Drive** de mi cuenta `jferrod0510@g.educaand.es` en un archivo cifrado con GPG:

```bash
echo "ghp_miToken" > github_token_drive.txt
gpg -c github_token_drive.txt
shred -u github_token_drive.txt
```

Subimos el archivo `github_token_drive.txt.gpg` a Drive con permisos restringidos.
De este modo, el token queda **seguro y recuperable** sin estar accesible públicamente.

---

## ⚙️ Parte 4 – Configurar el repositorio para autenticación local

El objetivo es **deshabilitar Windows Credential Manager solo en este repositorio**
y que Git recuerde el token localmente para no volver a pedirlo (Así nos quitamos de tener que estar iniciando sesión cada x tiempo).

### 🔹 Comandos utilizados

```bash
# Desactivar credenciales globales de Windows solo para este repo
git config --local credential.helper "store --file .git/credentials"

# Comprobar configuración
git config --local --get credential.helper
```

Esto hace que Git guarde las credenciales (una vez introducidas) en `.git/credentials`
en lugar de usar el gestor de credenciales de Windows.

![Configuración local del helper en Git Bash](imagenes/04_config_local.png)

---

## 🔁 Parte 5 – Cachear el token (solo una vez)

Hacemos el primer `push` para que Git solicite las credenciales y las guarde:

```bash
echo "# Práctica Git - Tokens y credenciales" > README.md
git add .
git commit -m "Primer commit con configuración local de credenciales"
git push -u origin main
```

Iniciamos sesión

Después de esto, Git crea automáticamente el archivo `.git/credentials`:

```
https://Ju4nmaFd3z:gho_xxxxxxxxxxxxxxxxxxxxxxxxxx@github.com
```

A partir de ese momento, no vuelve a solicitar autenticación.

![Archivo .git/credentials generado automáticamente](imagenes/05_git_credentials.png)

---

## 🧠 Parte 6 – Explicación técnica y razonamiento

### 🔹 ¿Por qué trabajamos por línea de comandos?

En entornos profesionales (especialmente Linux y servidores sin GUI), no hay sistemas gráficos ni gestores de credenciales de Windows.
Por eso se aprende a **autenticarse y configurar Git desde consola**, garantizando independencia del sistema operativo.

### 🔹 Uso de `git config --local` vs `--global`

| Comando               | Dónde guarda la configuración | Afecta a otros repos |
| --------------------- | ----------------------------- | -------------------- |
| `git config --global` | `~/.gitconfig`                | ✅ Sí                 |
| `git config --local`  | `.git/config` (en este repo)  | ❌ No                 |

➡️ En este ejercicio he usado `--local` porque **solo** este módulo debe trabajar con autenticación local cacheada, sin alterar el resto de repositorios que usan Windows Credential Manager (De esta forma no afecta a los repos de mis otras asignaturas).

![git config --list mostrando configuración local](imagenes/06_git_list.png)

## 🧾 Parte 7 – Resultado final

✅ Repositorio local sincronizado con el remoto usando **token clásico**.
✅ Autenticación **solo local** y **cacheada**.
✅ Documentación completa y justificada para detallar lo que hemos hecho y el por qué.
✅ Capturas añadidas al repositorio dentro de `docs/images/`.

---

## 🗂️ Estructura final del repositorio

```
git_practica_credenciales/
├── README.md
├── .git/
│   └── credentials
└── imagenes/
    ├── 01_repo_github.png
    ├── 02_01_token.png
    ├── 02_02_token.png
    ├── 02_03_token.png
    ├── 04_config_local.png
    ├── 05_git_credentials.png
    └── 06_git_list.png
```
