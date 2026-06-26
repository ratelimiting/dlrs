---
title: "Simulador de fingerprint "
date: 2026-02-24
draft: false
tags: ["cybersecurity", "MERN", "docker", "biometrics", "cve", "exploit", "vulnerability", "opsec", "nist"]
---
Hay un error común en la ciberseguridad y en la informatica en general: la creencia de que no puedes ser infectado o comprometido con solo visitar un sitio web.

Alerta de spoiler: Sí puedes.

Construí esta plataforma para demostrar hasta cierto punto lo que un sitio web malicioso o un anuncio malicioso puede recopilar de un usuario cuando dicho usuario carga la pagina, y lo que un threat actor puede hacer con esa información.

La mayoría de los proyectos de fingerprinting se detienen en "esto es lo que veo de ti". El mío sigue hasta el CVE. Es una plataforma educativa que combina la simulación con el análisis real, y tiene dos apartados generales.

### 1. El simulador de tracking de visitantes

Por razones legales y éticas, los datos del simulador no provienen de personas reales: cada 10 segundos se genera un visitante sintético, cada uno con una identidad coherente (ciudad, ISP, zona horaria, lenguaje, IP) y su propio canvas, WebGL, audio y letra. Un pequeño porcentaje de esos visitantes son vulnerables a CVEs y/o filtraciones de bases de datos a propósito. Cada visitante pasa por el pipeline completo:

- **Comparación de similaridad** con visitantes previos.
- **Revisión de vulnerabilidades** del navegador; en caso de encontrarse, se muestra un exploit que demuestra paso a paso cómo dicha vulnerabilidad puede ser explotada.
- **Revisión de filtraciones**: se busca la IP en una base de datos de breaches y, si aparece, se muestra la información asociada al data breach donde se encontró esa IP.

La aplicación obtiene la versión del navegador y verifica si hay algún CVE asociado a él, demostrando la importancia de mantener tu software actualizado. Vale la pena aclarar que muchos navegadores hoy en día congelan (freeze) la versión que reportan en su User-Agent para reducir la superficie de fingerprinting; aun así, esa versión congelada sigue siendo útil para buscar vulnerabilidades cuando el navegador está muy desactualizado. Por eso la aplicación intenta recuperar la versión completa real a través de Client Hints (`navigator.userAgentData.getHighEntropyValues`), y cuando no lo logra te indica cómo encontrarla manualmente en `chrome://version`.

Sobre las filtraciones por IP: en la mayoría de los casos esto podría parecer inútil porque la mayoría de las personas no tienen una IP estática, pero a veces los usuarios pueden tener una IP estática sin haberla pedido o sin conocer las implicaciones de seguridad de tener una. Una IP estática actúa como un identificador persistente, haciendo que sea trivial vincular filtraciones de datos históricas directamente al usuario.

Una de las razones por las que construí este sitio fue para mostrar lo única que es la fingerprint de cada usuario. Inicialmente, hice que la aplicación comparara a cada nuevo visitante con toda la base de datos de usuarios anteriores para ver la probabilidad de que el usuario hubiera visitado el sitio antes. Sin embargo, debido a la cantidad de usuarios generados, esto causó una carga significativa en el servidor y un cuello de botella en la velocidad. Es por eso que cambié la lógica para comparar solo con el usuario más reciente, manteniendo la aplicación rápida y responsiva.

Para hacer la información más fácil de leer, creé un panel (dashboard) que muestra estadísticas generales y una tabla en tiempo real. La tabla muestra toda la información recopilada del usuario, incluyendo CVEs, una guía paso a paso sobre cómo pueden ser utilizados y búsquedas de filtraciones de datos por IP.

### 2. El escaneo real

El segundo apartado es un escaneo real del visitante. Se revisa el fingerprint del visitante y se muestra la información que la página puede recopilar de él: red, hardware, canvas, WebGL, User-Agent, etc.

Con un botón se revisa si existen vulnerabilidades del navegador del usuario por medio del API de NIST (NVD). La búsqueda distingue entre una coincidencia exacta de la versión y una coincidencia a nivel del navegador, y nada de esto se dispara ni se guarda automáticamente.

La idea es no trackear a nadie. Por eso la información de los visitantes reales no se guarda en ninguna base de datos, y por lo mismo no se hace revisión de la IP, ya que es un dato muy identificable de los usuarios. Preferí no usar ningún API externo para la revisión de la IP: puedo tener la certeza de que yo no guardaré la información, pero no puedo decir lo mismo de un API externo.

### Arquitectura

Stack: React 19 + Vite 7, Express 5, MongoDB, Docker detrás de Nginx, i18next. Utilicé Docker para contenerizar la aplicación y la hostee en un VPS.

### Míralo en vivo

Puedes ver la aplicacion en accion aquí: **[fp.dlrs.me](https://fp.dlrs.me)**
