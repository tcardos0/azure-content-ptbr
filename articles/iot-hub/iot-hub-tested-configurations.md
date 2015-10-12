<properties
	pageTitle="Compatibilidade de hardware e plataformas de sistema operacional de IoT Azure | Microsoft Azure"
	description="Compatibilidade de plataformas de SO e hardware"
	services="iot-hub"
	documentationCenter="na"
	authors="hegate"
	manager="jamesosb"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="na"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="tbd"
     ms.date="09/29/2015"
     ms.author="hegate"/>

# Compatibilidade de plataformas de SO e hardware

Este documento descreve a compatibilidade do dispositivo SDKs com diferentes plataformas de sistema operacional, bem como as configurações específicas de dispositivo incluídas no [Microsoft Azure Certified para o programa de IoT](#certified). Se você já tiver um dispositivo, examine a lista de dispositivos incluídos no programa para localizar informações específicas de dispositivo em compatibilidade. Se não tiver certeza de qual dispositivo usar, veja a seção de compatibilidade da [Plataforma de sistema operacional e bibliotecas](#platforms).


## Plataformas de SO

As bibliotecas do Azure IoT foram testadas nas seguintes plataformas de sistema operacional:


|Plataformas dos sistemas operacionais Linux/Unix | Versão|
|:---------------|:------------:|
|Debian Linux| 7\.5|
|Fedora Linux|20|
|Raspbian Linux| 3\.18 |
|Ubuntu Linux| 14\.04 |
|Yocto Linux|2,1 |

|Plataformas do sistema operacional Windows | Versão|
|:---------------|:------------:|
|Área de trabalho do Windows| 7,8,10 |
|Windows IoT Core| 10 |
|Windows Server| 2012 R2|

|Outras plataformas | Versão|
|:---------------|:------------:|
|sistema operacional mbed| 2,0 |
|TI-RTOS | 2\. x |



## Bibliotecas C

O [SDK do dispositivo IoT do Microsoft Azure](c/device/readme.md) foi testado nas seguintes configurações:

|Plataforma do SO| Versão|Protocolos|
|:---------|:----------:|:----------:|
|Debian Linux| 7\.5 | HTTPS, AMQP, MQTT |
|Fedora Linux| 20 | HTTPS, AMQP, MQTT |
|sistema operacional mbed| 2,0 | HTTPS, AMQP |
|TI-RTOS| 2\. x | HTTPS |
|Ubuntu Linux| 14\.04 | HTTPS, AMQP, MQTT |
|Área de trabalho do Windows| 7,8,10 | HTTPS, AMPQ, MQTT |
|Yocto Linux|2,1 | HTTPS, AMQP|



## Bibliotecas do Node. js
O [SDK do dispositivo IoT do Microsoft Azure para Node.js](node/device/readme.md) foi testado nas seguintes configurações:


|Tempo de execução| Versão|Protocolos|
|:---------|:----------:|:----:|
|Node.js| 4\.1.0 | HTTPS|



## Bibliotecas Java
O [SDK do dispositivo IoT do Microsoft Azure para Java](java/device/readme.md) foi testado nas seguintes configurações:

|Tempo de execução| Versão|Protocolos|
|:---------|:----------:|----|
|Java SE (Windows)| 1\.7 | HTTPS, AMQP |
|Java SE (Linux)| 1\.8 | HTTPS, AMQP|

O [SDK do serviço IoT do Microsoft Azure para Java](java/service/readme.md) foi testado nas seguintes configurações:

|Tempo de execução| Versão|Protocolos|
|:---------|:----------:|:-----|
|Java SE| 1\.8 | HTTPS, AMQP |


## CSharp
O [SDK do serviço IoT do Microsoft Azure para .NET](csharp/readme.md) foi testado nas seguintes configurações:

|Plataforma do SO| Versão|Protocolos|
|:---------|:----------:|:----------:|
|Área de trabalho do Windows| 7,8,10 | HTTPS, AMPQ|
|Windows IoT Core|10 | HTTPS|

O código de agente gerenciado requer o Microsoft .NET Framework 4.5


## Certificado para IoT do Microsoft Azure

O certificado para o IoT do Microsoft Azure é o parceiro do programa que conecta o ecossistema IoT mais amplo ao Microsoft Azure de forma que os desenvolvedores e arquitetos entendem os cenários de compatibilidade. Especificamente, fornece uma lista de combinações de dispositivo/SO para ajudá-lo a se familiarizar rapidamente com um projeto IoT – se você estiver em uma prova de conceito ou fase piloto de piloto. Com o certificado do dispositivo e combinações de sistema operacional, seu projeto IoT pode começar rapidamente, com menos trabalho e personalização necessária para garantir que os dispositivos sejam compatíveis com o Azure IoT Suite e o Azure Hub IoT.


## Certificado para dispositivos de IoT

O certificado para dispositivos IoT testaram a compatibilidade com os SDKs do Azure IoT e estão pronto para serem usados em seu aplicativo IoT. Especificamente, podemos identificar a compatibilidade com base na plataforma do sistema operacional e o do código de idioma.

#### Lista de dispositivos

 Cada dispositivo foi certificado para trabalhar com o SDK no sistema operacional e linguagem escolhidos pelo fabricante do dispositivo. Por exemplo, BeagleBone Black funciona em Debian usando nossa linguagem C, Javascript e Java. Isso significa que os desenvolvedores poderão criar aplicativos em qualquer uma dessas linguagens e combinações de sistema operacional nos dispositivos específicos.

## Próximas etapas

Saiba mais sobre como desenvolver soluções usando certificados para dispositivos IoT [aqui](http://azure.com/iotdev).

|Dispositivo| SO testado |Linguagem|
|:---------|:----------|:----------|
|Raspberry Pi 2| Raspbian | C, Javascript, Java |
|Raspberry Pi 2| Windows 10 IoT básico| C, Javascript, C#|
|BeagleBone Black| Debian |C, Javascript, Java|
|BeagleBone Green|Debian |C, Javascript, Java|
|TI CC3200 | TI-RTOS 2.x|C|
|Intel Edison |Yocto |C, Javascript|
|Minnowboard Max |Windows 7,8, 10 |C#|
|Seta Dragonboard |Windows 10 IoT básico | C#|
|Freescale FRDM K64 |mbedOS | C|

<!---HONumber=Oct15_HO1-->