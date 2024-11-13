---
layout: post
title: "Solucion Posibles Errores ESP32"
author: "Francisco Macias"
date: 2024-11-03 20:55:00 +0800
subtitle: ""
categories: 
   - debug, 
   - Iot,
   - Windows
tags:
   - ESP32
   - IoT
   - Tutorial
description: ""
---

## Descripción General
Este tutorial tiene como objetivo guiarte en la solucion de problemas comunes al cargar scripts a nuestro esp32 desde al interfaz de arduino.

## Review a lo basico
1. Asegúrate de que Serial.begin() está configurado en tu código.
2. Verifica que el Monitor Serial está configurado con el puerto y velocidad correctos.
3. Reinicia el ESP32 después de subir el código.
4. Si sigue sin funcionar, intenta cargar un ejemplo básico (como el "Blink") para confirmar que el ESP32 está funcionando.

otras cosas que puedes validar:

**Cable USB**: Asegúrate de que el cable esté correctamente conectado y que sea uno de datos, no solo de carga.

**Puerto USB**: Cambia a un puerto USB diferente en tu computadora para descartar un problema físico.

## Liberar el Puerto COM
A veces, el puerto COM queda bloqueado por otro programa (como el Serial Monitor). Para liberarlo:

Cierra cualquier programa que pueda estar utilizando el puerto, como:

- Arduino IDE
- Monitor Serial de Visual Studio Code
- etc
  
Si estás en Windows:
Abre el Administrador de Dispositivos.
Ve a Puertos (COM & LPT) y verifica que el ESP32 esté listado.
Si no aparece, desconecta y vuelve a conectar el ESP32.

## Pin BOOT Presionado
Si mantuviste presionado el botón BOOT después de subir el código, el ESP32 no ejecutará el programa correctamente.

Suelta el botón BOOT y reinicia el ESP32.

## Forzar el Modo de Carga (Flash Mode)
Si el ESP32 sigue sin responder:

1. Mantén presionado el botón BOOT en el ESP32.
2. Mientras lo mantienes presionado, haz clic en Subir en el Arduino IDE.
3. Suelta el botón BOOT cuando veas el mensaje Connecting.... en el Arduino IDE.

## No se si ya lo dije: Reiniciar el ESP32
El ESP32 puede quedar en un estado inestable si algo salió mal durante la programación. Para reiniciarlo:

1. Presiona el botón de RESET en el ESP32.
2. Si sigue sin responder, mantén presionado el botón de BOOT mientras conectas el ESP32, luego intenta subir el código nuevamente.

## Error en el Puerto COM
El puerto COM seleccionado en el Monitor Serial no es el correcto.

Ve a Herramientas > Puerto en el Arduino IDE y asegúrate de que el puerto seleccionado sea el mismo que usaste para subir el código.

## Reiniciar la Computadora
No creerias a cuantos ingenieros a salvado esto.

Un paso sencillo pero a veces efectivo es reiniciar tu computadora. Esto libera recursos o procesos que podrían estar bloqueando el puerto.

## Actualizar `esptool.py`
Si todo lo demás falla, actualiza esptool:
`pip install --upgrade esptool`

1. Abre la terminal de comandos:
   - Presiona Windows + R, escribe cmd y presiona Enter.

   - Alternativamente, busca "Símbolo del sistema" o "Command Prompt" en el menú de inicio.

2. Asegúrate de que Python y pip estén instalados:
   
`python --version`

Si ves la versión de Python, significa que está instalado.

1. Verifica que pip esté instalado escribiendo:

`pip --version`

4. Ejecuta el comando:

`pip install --upgrade esptool`

Si obtienes un error de permisos, agrega --user:

`pip install --upgrade esptool --user`

Si no tienes Python o pip instalados:

Descarga Python desde [python.org](https://www.python.org/downloads/).

Durante la instalación, selecciona la opción "Add Python to PATH" para poder usarlo desde la terminal.
Después de instalar esptool, intenta nuevamente subir tu código al ESP32.
 
## Firmware Corrupto
Puede que el firmware del ESP32 esté corrupto después de varios intentos fallidos de subir código.

#### En Windows
Abrir la terminal de comandos:

Presiona `Windows + R`, escribe cmd y presiona Enter.
Alternativamente, busca "Símbolo del sistema" o "Command Prompt" en el menú de inicio.
Navegar al directorio de Python (si es necesario):

Asegúrate de que `esptool.py` esté disponible en tu PATH.
Si no está configurado, navega al directorio donde se instaló `esptool.py`. Esto suele ser algo como:

`C:\Users\<tu_usuario>\AppData\Local\Programs\Python\PythonXX\Scripts`

O usa el comando completo, por ejemplo:

`python -m esptool erase_flash`

#### Conectar el ESP32:

Conecta el ESP32 a tu computadora con un cable USB.
Ejecutar el comando:

Una vez que estás en el directorio correcto (o si `esptool.py` está en tu PATH), ejecuta:

`esptool.py --port COMX erase_flash`

Reemplaza COMX con el puerto COM donde está conectado tu ESP32 (por ejemplo, COM3).


### Notas Importantes
Si no tienes `esptool`, instálalo primero:

`pip install esptool`

Después de ejecutar el comando erase_flash, vuelve a subir tu código al ESP32 desde el Arduino IDE. Esto debería resolver problemas relacionados con firmware corrupto o configuraciones previas incorrectas.

---

## Referencias



