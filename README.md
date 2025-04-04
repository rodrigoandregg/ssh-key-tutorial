# Tutorial: Cómo generar una clave SSH y agregarla al ssh-agent

Nota: estos son comandos para **Linux Ubuntu**.

Este tutorial te dará una guía paso a paso sobre cómo generar una clave **SSH** y agregarla al `ssh-agent` para que la uses en conexiones **SSH**. Además, cubre los errores comunes que puedes encontrar, como problemas con la instalación o la ejecución del `ssh-agent`.

👀 También te servirá para agregar la clave a **GitHub** y facilitar la autenticación en **Git** sin necesidad de ingresar credenciales manualmente.

## **Paso 1**: verificar si ya tienes una clave **SSH** en tu sistema.

Antes de generar una nueva clave, debes verificar si ya tienes una en tu sistema usando este comando:

```
ls ~/.ssh/id_*
```

Si hay archivos como `id_rsa`, `id_ed25519`, etc., ya tienes una clave y puedes usarla.

Pero si quieres empezar desde cero puedes usar este comando para eliminar cualquier **clave SSH** que tengas:

```
rm -f ~/.ssh/id_*
```

Tanto si eliminaste las claves existentes o si simplemente no tenías ninguna, sigamos con el paso 2.

## **Paso 2**: Crear clave **SSH**.

Puedes crear tu clave con ese comando.
👀 Y No olvides cambiar `tu_email@example.com` por tu _Email_ real.

¿Cómo supiste que ya me pasó? 🤣

De todas formas, no pasa nada, el campo `-C "tu_email@example.com"` solo sirve para agregar un comentario a la clave, para identificarla por si tienes varias.

```
ssh-keygen -t ed25519 -C "tu_email@example.com"
```

> No te preocupes, el comando es así de raro y no, **nadie lo aprende de memoria**. Todos lo buscamos en _Google_ cuando lo necesitamos.

Cuando veas esto, solo dale al `Enter` para aceptar esa ruta de instalación:

```
Enter file in which to save the key (~/.ssh/id_ed25519):
```

Luego tú eliges si crear o no una **passphrase**. Si prefieres no usar una contraseña solo dale al `enter` las dos veces.

Ya deberías tener creada tu clave y la puedes encontrar el la carpeta o directorio `.ssh`. Es una carpeta oculta y no la verás si solo usas:

```
ls
```

Así que puedes usar este comando para ver el contenido de tu carpeta `.ssh`

```
ls ~/.ssh/
```

Ahí verás dos archivos, probablemente llamados `id_ed25519` y `id_ed25519.pub`

La primera es tu **clave SSH privada**, cuidado con compartirla. Y la segunda, con la extensión `.pub`, es tu **clave SSH pública**.

### En caso de que no hayas puesto tu Email, como yo:

Usa el comando este comando para abrir tu clave pública en el editor **Nano** y editarla rapidamente sin salir de tu terminal. Solo la clave pública lleva el Email entre comillas.

```
nano ~/.ssh/id_ed25519.pub
```

cuando lo hayas corregido guarda usando `Ctrl + O` y cierra el editor con el atajo `Ctrl + X`. Es raro ¿verdad? Pues que sepas que existe gente que prefiere eso a VSCode jajaja (no me golpeen).

## **Paso 3**: Verificar si el SSH-agent está instalado y ejecutándose.

> 🔐 ¿Qué es el `ssh-agent`?
>
> El `ssh-agent` es un programa que gestiona tus **claves privadas SSH** en segundo plano. Su objetivo es almacenar en memoria las **claves SSH** desbloqueadas para que no tengas que ingresar tu frase de contraseña _(passphrase)_ cada vez que uses Git o te conectes a un servidor remoto.

### Verificar instalación:

Usa este comando para saber si el `ssh-agent` está instalado en tu sistema:

```
which ssh-agent
```

En caso de que lo tengas instalado te mostrará la ruta a la carpeta donde se encuentra. por ejemplo: `/usr/elpepe/ssh-agent`

Pero si te da un error te toca instalarlo con este comando sencillo:

```
sudo apt install openssh-client
```

Sea cual sea tu caso, el siguiente paso es ver si es que el _ssh-agent_ está corriendo.

### Verificar ejecución:

Con este comando puedes saber si el `ssh-agent` se encuentra en ejecución en este momento:

```
eval "$(ssh-agent -s)"
```

Deberías ver una respuesta como esta:

```
Agent pid 1234
```

Si no es así posiblemente te saltaste el paso de instalación o deberías reiniciar tu sistema.

## **Paso 4**: Agregar tu clave **SSH** al `ssh-agent`

Si verificaste que el `ssh-agent` está en ejecución puedes agregar tu **clave SSH privada**.

> 👀 Debe ser tu calve privada, la que **no tiene la extensión `.pub`**

Usa este comando para hacerlo:

```
ssh-add ~/.ssh/id_ed25519
```

## **Paso 5**: Agregar la **clave SSH pública** a Github

Primero debes copiar la clave publica completa para pegarla en el sitio de github, en tu navegador:

```
cat ~/.ssh/id_ed25519.pub
```

> el comando `cat` sirve para visualizar archivos en la terminal.

### En tu navegador:

- Ve a tu perfil de _Github_.
- En la sección de _settings_ (ajustes)
- Busca SSH and GPG Keys
- Ahí verás un botón que dice _New SSH key_ (nueva clave SSH)
- Dale el nombre quieras.
- En el siguiente campo, llamado _key_ (clave), pega la clave pública completa que habías copiado antes.
- Haz clic en el botón _Add SSH key_ (añadir clave SSH) para guardar tu clave.

## \*\*Paso 6: Verifica la conexión con _Github_

Puedes comprobar la conexión SSH con github usando este comando:

```
ssh -T git@github.com
```

Deberías ver algo como esto:

```
Hi username! You've successfully authenticated, but GitHub does not provide shell access.
```

Si todo salió bien ¡estás listo para usar SSH con GitHub!

Si recibes el error `"Permission denied (publickey)"`: Esto significa que la **clave SSH** no se ha agregado correctamente a tu cuenta de GitHub o el `ssh-agent` no está cargando la clave correcta. Revisa que hayas copiado correctamente la clave pública y que el `ssh-agent esté ejecutándose con la clave correcta.
