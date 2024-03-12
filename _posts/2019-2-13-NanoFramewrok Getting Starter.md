---
title: NanoFramework
author: ethelpoe
date: 2019-08-09 20:55:00 +0800
categories:
  - Blogging
  - Tutorial
tags:
  - Programing
  - Processing
pin: false
---

Ok, comencemos:

1.- Lo primero sera descargar e instalar  [Visual Studio Community](https://www.visualstudio.com/downloads) asegúrate de instalar los workloads **.NET desktop development** and **.NET Core cross-platform development y al menos  [.NET 6.0 Runtime (or .NET 6.0 SDK)](https://dotnet.microsoft.com/download) ** 

2.- instala **_nanoFramework_ extension for Visual Studio** 

3.- Restart VS

#### Cargando el firmware a la placa usando nanoFirmwareFlasher

Ahora tenemos que flashear nuestra placa utilizando [nano Firmware Flasher (nanoff)](https://github.com/nanoframework/nanoFirmwareFlasher)
necesitas saber en que puerto esta conectado tu dispositivo (puedes validar esto en tu administrador de dispositivos)

>Importante: Debes tener instalado el driver para la version de windows de tu esp32

click here: [Driver](https://www.silabs.com/developers/usb-to-uart-bridge-vcp-drivers?tab=downloads)

![[assets/Pasted image 20240304220601.png]]

```c#
using System.Threading;
using Windows.Devices.Gpio;

class Program
{
    static void Main()
    {
        // Especifica el número del pin donde está conectado el LED
        int ledPinNumber = 2;

        // Configura el pin como salida
        GpioPin ledPin = GpioController.GetDefault().OpenPin(ledPinNumber);
        ledPin.SetDriveMode(GpioPinDriveMode.Output);

        // Enciende el LED y espera por un tiempo
        ledPin.Write(GpioPinValue.High);
        Thread.Sleep(1000);

        // Apaga el LED
        ledPin.Write(GpioPinValue.Low);

        // Mantén la aplicación en ejecución (puedes cerrarla manualmente después)
        Thread.Sleep(Timeout.Infinite);
    }
}

```






---
## Referencias

[Getting Started NanoFramework](https://docs.nanoframework.net/content/getting-started-guides/getting-started-managed.html)

[Driver](https://www.silabs.com/developers/usb-to-uart-bridge-vcp-drivers?tab=downloads)