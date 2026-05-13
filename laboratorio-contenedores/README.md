# Laboratorio 2: Introducción práctica a contenedores con Docker



## Índice del laboratorio

- [Parte 1: Verificación de instalación de Docker](./parte1-verificacion.md)
- [Parte 2: Primer contenedor](./parte2-comandos-basicos.md)
- [Parte 3: Imágenes y contenedores](./parte3-imagenes-y-contenedores.md)
- [Parte 4: Administración básica de contenedores](./parte3-imagenes-y-contenedores.md#administración-de-contenedores)
- [Parte 5: Crear una aplicación sencilla](./parte4-dockerfile.md)
- [Parte 6: Construir una imagen con Dockerfile](./parte4-dockerfile.md#parte-5-construcción-de-una-imagen-con-dockerfile)
- [Parte 7: Publicación de puertos](./parte5-puertos.md)
- [Parte 8: Logs e inspección de contenedores](./parte5-puertos.md#parte-8-logs-e-inspección-de-contenedores)
- [Parte 9: Variables de entorno](./parte5-puertos.md#parte-9-variables-de-entorno)
- [Parte 10: Persistencia con volúmenes](./parte6-volumenes.md)
- [Parte 11: Bind mounts](./parte6-volumenes.md#parte-11-bind-mounts)
- [Parte 12: Redes de Docker](./parte7-redes.md)
- [Parte 13: Comunicación entre servicios](./parte7-redes.md#parte-13-comunicación-entre-servicios)
- [Parte 14: Limpieza del ambiente](./parte8-limpieza.md)
- [Parte 15: Reflexión final](#reflexión-final)

---

## Descripción general

En este laboratorio se trabajó con Docker para comprender el uso básico de contenedores. Se inició verificando la instalación de Docker y ejecutando un primer contenedor con `hello-world`. Luego se trabajó con imágenes, contenedores interactivos, administración de contenedores, construcción de una imagen personalizada con Dockerfile, publicación de puertos, logs, variables de entorno, volúmenes, bind mounts, redes de Docker y comunicación entre servicios.

También se creó una aplicación sencilla con Flask, la cual fue ejecutada primero localmente y luego dentro de un contenedor. Posteriormente, se probó el acceso desde el navegador mediante publicación de puertos y se configuró su comportamiento usando variables de entorno.

---

## Estructura del proyecto

```text
laboratorio-contenedores/
├── README.md
├── parte1-verificacion.md
├── parte2-comandos-basicos.md
├── parte3-imagenes-y-contenedores.md
├── parte4-dockerfile.md
├── parte5-puertos.md
├── parte6-volumenes.md
├── parte7-redes.md
├── parte8-limpieza.md
└── app/
    ├── Dockerfile
    ├── app.py
    └── requirements.txt
```

---

## Aplicación desarrollada

La aplicación desarrollada utiliza Flask y tiene dos rutas principales:

```text
/
```

Muestra un mensaje en formato HTML.

```text
/info
```

Devuelve información básica de la aplicación en formato JSON.

La aplicación también permite modificar el mensaje principal mediante la variable de entorno `MENSAJE`.

---

## Reflexión final

### 1. ¿Qué es un contenedor?

Un contenedor es un entorno aislado donde se puede ejecutar una aplicación junto con sus dependencias, librerías y configuración necesaria. A diferencia de una máquina virtual, no necesita incluir un sistema operativo completo, sino que comparte el kernel del sistema anfitrión.

En este laboratorio lo pude ver al ejecutar contenedores basados en imágenes como `hello-world`, `ubuntu`, `nginx`, `redis` y la imagen personalizada `laboratorio-flask:1.0`. Cada contenedor tenía su propio entorno de ejecución, pero todos corrían sobre el mismo sistema host.

---

### 2. ¿Qué problema resuelve Docker?

Docker resuelve el problema de tener que configurar manualmente el ambiente de ejecución de una aplicación en cada computadora o servidor. Con Docker se puede empaquetar una aplicación junto con sus dependencias dentro de una imagen, y luego ejecutar esa imagen como contenedor en distintos entornos.

Esto ayuda a evitar problemas como “en mi máquina sí funciona”, porque el contenedor mantiene un ambiente más controlado y reproducible. En el laboratorio, por ejemplo, la aplicación Flask pudo ejecutarse dentro de un contenedor sin depender directamente del entorno Python instalado en el sistema anfitrión.

---

### 3. ¿Qué diferencia hay entre una imagen y un contenedor?

Una imagen es una plantilla estática. Contiene lo necesario para crear contenedores, como el sistema de archivos base, dependencias, archivos de la aplicación y comandos de ejecución.

Un contenedor es una instancia creada a partir de una imagen. Puede estar ejecutándose o detenido. Por ejemplo, `laboratorio-flask:1.0` fue la imagen creada con el Dockerfile, mientras que `app-lab`, `app-puertos`, `app-env` o `app-logs` fueron contenedores creados a partir de esa imagen.

---

### 4. ¿Qué diferencia hay entre un contenedor y una máquina virtual?

Una máquina virtual virtualiza hardware y normalmente incluye un sistema operativo completo. Esto la hace más pesada, porque cada VM necesita su propio sistema operativo invitado.

Un contenedor virtualiza a nivel del sistema operativo. Comparte el kernel con el host y solo incluye lo necesario para ejecutar la aplicación. Por eso los contenedores suelen ser más ligeros, rápidos de iniciar y fáciles de mover entre entornos.

En el laboratorio, el contenedor de Ubuntu parecía un sistema Linux porque tenía carpetas como `/bin`, `/etc`, `/usr` y `/var`, pero no era una máquina virtual completa.

---

### 5. ¿Qué aprendió sobre puertos?

Aprendí que una aplicación puede estar escuchando en un puerto dentro del contenedor, pero eso no significa que sea accesible automáticamente desde la máquina anfitriona. Para acceder desde el navegador del host, se debe publicar el puerto usando la opción `-p`.

Por ejemplo, con:

```bash
-p 5000:5000
```

el puerto 5000 del host se conectó con el puerto 5000 del contenedor. También se probó:

```bash
-p 8080:5000
```

donde el host usó el puerto 8080, pero la aplicación dentro del contenedor siguió escuchando en el puerto 5000.

---

### 6. ¿Qué aprendió sobre volúmenes?

Aprendí que los volúmenes permiten guardar datos fuera del ciclo de vida del contenedor. Esto es importante porque los datos creados dentro de un contenedor pueden perderse cuando el contenedor se elimina.

En la práctica se creó el volumen `datos-lab`, se guardó un archivo dentro de `/datos`, se eliminó el primer contenedor y luego se creó otro contenedor usando el mismo volumen. El archivo seguía existiendo, lo que demostró que el dato estaba guardado en el volumen y no dependía del contenedor original.

---

### 7. ¿Qué aprendió sobre redes?

Aprendí que las redes de Docker permiten que varios contenedores se comuniquen entre sí. Al crear redes personalizadas como `red-lab` y `red-app`, los contenedores conectados a la misma red pudieron comunicarse usando sus nombres.

Por ejemplo, desde un contenedor Ubuntu se pudo acceder al servidor Nginx usando:

```bash
curl http://servidor-web
```

También se probó la comunicación con Redis usando:

```bash
redis-cli -h redis-lab
```

Esto demostró que Docker puede resolver nombres de contenedores dentro de una misma red personalizada.

---

### 8. ¿En qué casos usaría Docker en un proyecto de software?

Usaría Docker cuando necesite que una aplicación se ejecute de forma consistente en diferentes ambientes, como desarrollo, pruebas y producción. También lo usaría cuando un proyecto tenga varias dependencias o servicios, como una aplicación web, una base de datos y un sistema de caché.

Por ejemplo, en un proyecto real podría usar Docker para ejecutar una aplicación Flask junto con Redis o una base de datos en contenedores separados. También lo usaría para facilitar que otros compañeros puedan ejecutar el proyecto sin tener que instalar manualmente todas las dependencias en su máquina.

---

### 9. ¿Qué parte del laboratorio le pareció más útil?

La parte más útil fue construir una imagen personalizada con el Dockerfile y luego ejecutar la aplicación Flask dentro de un contenedor. Esa parte conectó varios conceptos importantes: imagen base, instalación de dependencias, copia de archivos, exposición de puertos y comando de ejecución.

También me pareció muy útil la parte de puertos, porque permitió ver claramente cómo una aplicación dentro de un contenedor puede accederse desde el navegador de la máquina anfitriona.

---

### 10. ¿Qué parte le pareció más confusa?

La parte más confusa al inicio fue entender la diferencia entre ejecutar algo dentro del contenedor y ejecutarlo en la terminal del host. Por ejemplo, al entrar a un contenedor Ubuntu con `docker run -it ubuntu bash`, el prompt cambia y los comandos se ejecutan dentro del contenedor, no directamente en la máquina anfitriona.

También fue un poco confuso al principio el uso de volúmenes y bind mounts, porque ambos usan la opción `-v`, pero no funcionan exactamente igual. Después de probar `datos-lab:/datos` y `"$(pwd)":/app`, quedó más claro que el volumen es administrado por Docker, mientras que el bind mount comparte una carpeta específica del host.

---

## Conclusión

En conclusión, este laboratorio permitió comprender de forma práctica los conceptos básicos de Docker. Se trabajó con imágenes, contenedores, puertos, variables de entorno, volúmenes, bind mounts y redes. Además, se creó una aplicación Flask y se ejecutó dentro de un contenedor personalizado.

La práctica ayudó a ver que Docker no solo sirve para ejecutar aplicaciones, sino también para organizar ambientes de desarrollo, separar servicios y mantener proyectos más reproducibles. También quedó claro que es importante limpiar el ambiente de trabajo para evitar acumular contenedores, imágenes o recursos que ya no se utilizan.