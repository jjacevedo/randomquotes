# randomquotes

Tutorial 06 de Arquitectura de Software (EAFIT): aplicación Express que responde una frase aleatoria en `/`, empaquetada con Docker y desplegada en la nube.

Autor: Juan José Acevedo Otálvaro

## Ejecutar localmente

```bash
npm install
node index.js
```

La aplicación escucha en el puerto `80` (versión final del tutorial). En las dos primeras etapas usaba el puerto `3000`; el historial de commits muestra la evolución:

1. `feat: crear proyecto express`: `npm init -y`, `npm install express` y la ruta `/` con `Hello World!`.
2. `feat: generar frases aleatorias`: la ruta `/` responde una de 7 frases al azar.
3. `feat: agregar dockerfile para despliegue`: `Dockerfile` con `node:24-alpine` y puerto `80`.

## Despliegue

El enunciado indica crear una máquina virtual en GCP siguiendo los pasos A, B y C del tutorial de Laravel con Docker. Como el curso pasó a AWS, el despliegue se hizo en la instancia EC2 del grupo **Los de al fondo (arqui)** (Amazon Linux 2023, t3.micro, Docker 25), la misma del Tutorial 05.

```bash
ssh -i ~/.ssh/students.pem ec2-user@<IP>
cd ~/students/juan-acevedo
git clone https://github.com/jjacevedo/randomquotes.git
cd randomquotes
docker build -t randomquotes-juan-acevedo:1.0 .
docker run -d --name randomquotes-juan-acevedo --restart unless-stopped -p 80:80 randomquotes-juan-acevedo:1.0
```

`--restart unless-stopped` hace que el contenedor arranque solo cuando se enciende la instancia. La IP pública cambia en cada encendido; las evidencias se tomaron con `32.199.169.25`.

## Evidencias

Aplicación corriendo en la nube en `http://32.199.169.25/`, con una frase distinta en cada recarga:

![Frase 1](evidencias/01-frase-1.jpg)

![Frase 2](evidencias/02-frase-2.jpg)

![Frase 3](evidencias/03-frase-3.jpg)

Salida de terminal (15 de octubre de 2026):

```text
== Desde el Mac
Thu Oct 15 01:44:05 UTC 2026
There is beauty in simplicity. [HTTP 200]

== Desde la instancia
Thu Oct 15 01:44:07 UTC 2026
randomquotes-juan-acevedo:1.0 176MB
NAMES                       IMAGE                           STATUS          PORTS
randomquotes-juan-acevedo   randomquotes-juan-acevedo:1.0   Up 16 minutes   0.0.0.0:80->80/tcp, :::80->80/tcp, 8080/tcp
randomquotes-juan-acevedo 23.39MiB / 913.2MiB
See rejection as redirection.
Get ready to be inspired…
Alone or not you gonna walk forward.
Alone or not you gonna walk forward.
Impossible is just an opinion.
```

## Observaciones

- El `Dockerfile` declara `EXPOSE 8080` como en el enunciado, pero la aplicación escucha en el `80`. `EXPOSE` solo documenta; el puerto real lo define `-p 80:80`.
- Se agregó un `.gitignore` con `node_modules`, que el enunciado no menciona, para no versionar las dependencias.
- El puerto `80` del host es único en la instancia compartida; otro despliegue en la misma máquina debe usar un puerto distinto.
