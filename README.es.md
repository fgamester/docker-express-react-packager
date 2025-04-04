[Read it in English](README.md)

# Docker Express React Packager

Hola a todos👋🏼👋🏼👋🏼  

Hace unos días quise repasar un poco de Docker, esa mágica herramienta que nos permite empaquetar nuestro proyectos y facilitar bastante la forma de compartirlos sin que las demás personas tengan que instalar incontables dependencias o perdiendo tiempo configurando entornos. El problema es que los proyectos empaquetados en estado de desarrollo pesan demasiado y a veces requieren de muchos ajustes, entonces pensé en desarrollar un servidor simple en express que me permita empaquetar proyectos de React ya construidos.

Solo menciono React por como funciona, una página estática que renderiza contenido según la ruta y otras condicionales, entonces los ajustes del servidor son bastante fáciles para el conocimiento que manejo actualmente.

### Tecnologías utilizadas

- **Express**: Un framework de Node.js, utilizado para configurar el servidor. Es todo lo que necesito para este proyecto.

## ¿Quieres probarlo?

Esta es básicamente una herramienta, así que la mejor forma de utilizarla es clonando el repositorio. También necesitarás:

- **Node.js** (para levantar el servidor)  
- **Docker** (solo si planeas empaquetar tu proyecto)  

Necesitarás proporcionar un proyecto en React ya construido en una carpeta "dist" en el directorio raíz del proyecto. Más información sobre esto más abajo...

Si lo que necesitas es empaquetar un proyecto, ve aún más abajo de este README.

### Obtén el repositorio para tu espacio de trabajo local

Primero que nada necesitarás clonar el repositorio:
```bash
$ git clone https://github.com/fgamester/docker-express-react-packager.git
```
Luego ejecutar el comando de instalación de dependencias:
```bash
$ npm install
```

#### Proporciona un proyecto

Como mencioné antes, necesitarás un proyecto de React ya construido dentro de una carpeta `dist`.  

Primero crea la carpeta `dist` en el directorio raíz o copialo desde tu mismo proyecto, pero asegurándote de que quede en el directorio principal. Algo como esto:

```
este-proyecto/  
├── dist/  
│   ├── index.html
│   ├── styles.css
│   ├── app.js
│   └── assets/
│       ├── image.png
│       └── logo.png
├── node_modules/
├── .dockerignore
├── .gitignore
├── Dockerfile
├── package-lock.json
├── package.json
├── README.es.md
├── README.md
└── server.js
```

Tus archivos reales pueden variar, solo asegúrate que estén dentro de `dist`.

Adicionalmente en el archivo `server.js` puedes cambiar el puerto expuesto en caso de que puedas necesitarlo, está establecido en el puerto 3000 por defecto. Si lo cambiaste RECUERDA cambiarlo en el `Dockerfile` también (solo en caso de que lo quieras para empaquetar).

`server.js`:
```javascript
1. const port = 3000; // <--- CAMBIALO AQUÍ
```

`Dockerfile`:
```docker
5. EXPOSE 3000 # <--- CAMBIALO AQUÍ
```

#### Listo para ser ejecutado

Finalmente, levanta el servidor con el siguiente comando:
```bash
$ node server.js
```

El proyecto estará corriendo en el puerto `3000` por defecto (si es que no lo cambiaste), así que deberás escribir [`localhost:3000`](http://localhost:3000) en tu navegador o hacer clic en el enlace señalado.

### Dockerizar

Si necesitas un proyecto empaquetado para tus clientes, colegas de trabajo, estudiantes, amigos, etc., así es como deberás hacerlo.

Asumiré que ya cuentas con Docker instalado, abre una terminal donde la ruta apunte hacia la carpeta raíz del proyecto, ahí deberás ejecutar el siguiente comando (en caso de que no sepas mucho sobre Docker, te recomiendo leer la descripción del comando más abajo):

```bash
$ docker build -t <nombre-personalizado>:<etiqueta-personalizada> .
```

Después de esto la terminal mostrará unos detalles del proceso, solo espera a que termine.

#### Descripción del comando

`-t`: significa `--tag` (etiqueta), nos permite escribir un nombre y opcionalmente una etiqueta para la imagen de docker que estamos construyendo.  
`custom-name`: si usas `-t` tendrás que proporcionar un nombre para tu imagen, usa uno que sea fácil de recordar.  
`custom-tag`: esto es opcional, las etiquetas de una imagen de Docker usualmente son utilizadas para distinguir versiones de esta. Si estás seguro que será la única versión de tu imagen, puedes omitirlo, pero asegúrate de eliminar el símbolo `:` después del nombre de la imagen.  
`.`: este punto es usado para especificar en donde se encuantra `Dockerfile`, relativo a la ruta actual de tu terminal.  

#### Usa tu imagen

Ahora está todo listo para crear un contenedor con nuestra imagen. Abre un nuevo terminal, no importa en que ruta apunte, ahora trabajaremos directamente con Docker CLI.

Ejecuta el siguiente comando:
```bash
$ docker run --name <nombre-personalizado> -p <3000:3000> -d <nombre-imagen>
```
`--name`: nos permite especificar un `nombre-personalizado` para nuestro contenedor, de preferencia uno fácil de recordar, pero es esencialmente opcional.

`-p`: esto es requerido, ya que necesitamos especificar un puerto que permita comunicarnos con nuestro contenedor.

`3000:3000`: El primero es el puerto por el cuál podremos conectarnos desde FUERA del contenedor, es relativamente opcional (en cuanto a que número usar), solo debes asegurarte que sea uno que se encuentre disponible (no utilizado actualmente por otras aplicaciones). El segundo es el que especificamos antes de construir la imagen, si no cambiaste nada entonces es el puerto `3000`.

`-d`: Opcional, corre el container en modo `detach` (segundo plano). En este modo la única forma de detener el contenedor es mediante la terminal o la interfaz de Docker. No usar este modo provocará que el contenedor se detenga al cerrar la terminal.

`nombre-imagen`: El nombre de la imagen que especificamos al crearla.

Después de eso y si no tuvimos ningún problema, la terminal nos responderá con un ID generado para el contenedor. Adicionalmente el comando `run` iniciará inmediatamente nuestro contenedor.

Y eso sería todo, ahora puedes visitar [`localhost:3000`](http://localhost:3000) o cualquiera haya sido el puerto que configuraste para ver tu página. 

Cuando quieras detener el servicio, solo utiliza el comando `stop`:
```bash
$ docker stop <nombre-contenedor|id-conetenedor>
```

Y cuando quieras volver a ejecutar el contenedor deberás utilizar el comando `start`:
```bash
$ docker start <nombre-contenedor|id-contenedor>
```

>*Puedes usar tanto el nombre como el ID, recomiendo utilizar el nombre si especificaste uno.*