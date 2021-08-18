# Introduccion a git

## Que es git?

Git es, con diferencia, el sistema de control de versiones moderno más utilizado del mundo.

Git presenta una arquitectura distribuida. Es un DVCS (sistema de control de versiones distribuido, por sus siglas en inglés). En lugar de tener un único espacio para todo el historial de versiones del software, como sucede de manera habitual en los sistemas de control de versiones antaño populares, como CVS o Subversion (también conocido como SVN), en Git, la copia de trabajo del código de cada desarrollador es también un repositorio que puede albergar el historial completo de todos los cambios.

### Flujo de trabajo con git

En [este diagrama][git.repositorio] se muestra el flujo de trabajo de git.

![repositorios git][git.repositorio]

[git.repositorio]: img/git.intro.jpg "Logica de repositorios git"

## Comenzando con git



```bash
git config --global user.name "Daniel Oscar Zamo"
git config --global user.email "daniel.oscar.zamo@gmail.com"

git version        # Consulto version de git (si esta instalado)
git init           # Crear el repositorio en el actual directorio

git add <FILE>     # Agrego al stage
git commit -m "Primer commit de <FILE>"   # Agrego el fichero a mi repo. local
```
