---
title: "Simulador de fingerprint "
date: 2026-02-24
draft: false
tags: ["cybersecurity", "MERN", "docker", "biometrics", "cve", "exploit", "vulnerability", "opsec"]
---
Hay un error común en la ciberseguridad y en la informatica en general: la creencia de que no puedes ser infectado o comprometido con solo visitar un sitio web.

Alerta de spoiler: Sí puedes.

Construí este simulador de fingerprint para demostrar hasta cierto punto lo que un sitio web malicioso o un anuncio malicioso puede recopilar de un usuario cuando dicho usuario carga la pagina, y lo que un threat actor puede hacer con esa información.

Por razones legales y éticas, tuve que pasar de usar datos de visitantes reales a generar un visitante falso cada 10 segundos. Debido a que este proyecto muestra toda la información recopilada al público, mostrar estos datos de usuarios reales sería una pesadilla de privacidad. Esto es especialmente cierto considerando que busco CVEs (Vulnerabilidades y Exposiciones Comunes) asociados a los usuarios y uso las IPs para revisar filtraciones de datos (data leaks) conocidas.

### Cómo se Detectan las Vulnerabilidades y Filtraciones

La aplicación obtiene la versión del navegador y verifica si hay algún CVE asociado a él, demostrando la importancia de mantener tu software actualizado.

La IP se utiliza para buscar filtraciones de datos. En la mayoría de los casos, esto podría parecer inútil porque la mayoría de las personas no tienen una IP estática, pero a veces los usuarios pueden tener una IP estática sin haberla pedido o sin conocer las implicaciones de seguridad de tener una. Una IP estática actúa como un identificador persistente, haciendo que sea trivial vincular filtraciones de datos históricas directamente al usuario.

### Visualización de Datos

Para hacer la información más fácil de leer, creé un panel (dashboard) que muestra estadísticas generales y una tabla en tiempo real. La tabla muestra toda la información recopilada del usuario, incluyendo CVEs, una guía paso a paso sobre cómo pueden ser utilizados y búsquedas de filtraciones de datos por IP.

Una de las razones por las que construí este sitio fue para mostrar lo única que es la fingerprint de cada usuario. Inicialmente, hice que la aplicación comparara a cada nuevo visitante con toda la base de datos de usuarios anteriores para ver la probabilidad de que el usuario hubiera visitado el sitio antes. Sin embargo, debido a la cantidad de usuarios generados, esto causó una carga significativa en el servidor y un cuello de botella en la velocidad. Es por eso que cambié la lógica para comparar solo con el usuario más reciente, manteniendo la aplicación rápida y responsiva.

### Arquitectura

Construí esta aplicación usando el Stack MERN (MongoDB, Express, React y Node). Utilicé Docker para contenerizar la aplicación y la hostee en un VPS. 

Cada 10 segundos, se genera un nuevo usuario simulado y la información de su finngerprint es aleatoria. Un pequeño porcentaje de esos usuarios son vulnerables a CVEs y/o filtraciones de bases de datos.

### Míralo en vivo

Puedes ver la aplicacion en accion aquí: **[fp.dlrs.me](https://fp.dlrs.me)**