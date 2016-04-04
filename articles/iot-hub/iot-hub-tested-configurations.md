<properties
	pageTitle="OS プラットフォームの互換性 | Microsoft Azure"
	description="IoT デバイス SDK と OS プラットフォームとの互換性をまとめてあります。"
	services="iot-hub"
	documentationCenter=""
	authors="hegate"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="na"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="02/28/2016"
     ms.author="hegate"/>

# OS プラットフォームとデバイス SDK との互換性

このドキュメントでは、SDK とさまざまな OS プラットフォームと の互換性について説明します。使用するデバイスがわからない場合、この記事の [OS プラットフォームやライブラリ](#os-platforms)の対応状況のセクションを参照してください。

## Microsoft Azure Certified for IoT プログラム

既にデバイスがある場合、[Microsoft Azure Certified for IoT プログラム][lnk-certified]に含まれるデバイスの一覧を参照し、対応状況に関するデバイス固有の情報を確認してください。Microsoft Azure Certified for IoT は、開発者やアーキテクトが対応状況のシナリオを理解できるように、より広範な IoT エコシステムを Microsoft Azure に接続するパートナー プログラムです。具体的には、IoT プロジェクト (概念実証フェーズまたはパイロット フェーズ) をすぐに始めるために役立つ、OS とデバイスの信頼できる組み合わせの一覧を提供しています。

## OS プラットフォーム

Azure IoT ライブラリは、次のオペレーティング システム プラットフォームでテストされています。


|Linux/Unix OS プラットフォーム | バージョン|
|:---------------|:------------:|
|Debian Linux| 7\.5|
|Fedora Linux|20|
|Raspbian Linux| 3\.18 |
|Ubuntu Linux| 14\.04 |
|Yocto Linux|2\.1 |

|Windows OS プラットフォーム | バージョン|
|:---------------|:------------:|
|Windows デスクトップ| 10 |
|Windows IoT Core| 10 |
|Windows Server| 2012 R2|

|その他のプラットフォーム | バージョン|
|:---------------|:------------:|
|mbed | 2\.0 |
|TI-RTOS | 2\.x |



## C ライブラリ

[C 用 Microsoft Azure IoT デバイス SDK](https://github.com/Azure/azure-iot-sdks/blob/master/c/readme.md) は、次の構成でテストされています。

|OS プラットフォーム| バージョン|プロトコル|
|:---------|:----------:|:----------:|
|Debian Linux| 7\.5 | HTTPS、AMQP、MQTT、AMQP over WebSockets |
|Fedora Linux| 20 | HTTPS、AMQP、MQTT、AMQP over WebSockets |
|mbed OS| 2\.0 | HTTPS、AMQP |
|TI-RTOS| 2\.x | HTTPS |
|Ubuntu Linux| 14\.04 | HTTPS、AMQP、MQTT、AMQP over WebSockets |
|Windows デスクトップ| 10 | HTTPS、AMQP、MQTT、AMQP over WebSockets |
|Yocto Linux|2\.1 | HTTPS、AMQP|



## Node.js ライブラリ

[Node.js 用 Microsoft Azure IoT デバイス SDK](https://github.com/Azure/azure-iot-sdks/blob/master/node/device/readme.md) は、次の構成でテストされています。


|ランタイム| バージョン|プロトコル|
|:---------|:----------:|:----:|
|Node.js| 4\.1.0 | HTTPS、AMQP、MQTT、AMQP over WebSockets |



## Java ライブラリ

[Java 用 Microsoft Azure IoT デバイス SDK](https://github.com/Azure/azure-iot-sdks/blob/master/java/device/readme.md) は、次の構成でテストされています。

|ランタイム| バージョン|プロトコル|
|:---------|:----------:|----|
|Java SE (Windows)| 1\.8 | HTTPS、AMQP、MQTT |
|Java SE (Linux)| 1\.8 | HTTPS、AMQP、MQTT|

Java 用 Microsoft Azure IoT サービス SDK は、次の構成でテストされています。

|ランタイム| バージョン|プロトコル|
|:---------|:----------:|:-----|
|Java SE| 1\.8 | HTTPS、AMQP、MQTT |


## CSharp

[.NET 用 Microsoft Azure IoT デバイス SDK](https://github.com/Azure/azure-iot-sdks/blob/master/csharp/device/readme.md) は、次の構成でテストされています。

|OS プラットフォーム| バージョン|プロトコル|
|:---------|:----------:|:----------:|
|Windows デスクトップ| 10 | HTTPS、AMQP、MQTT、AMQP over WebSockets |
|Windows IoT Core|10 | HTTPS |

管理対象エージェントのコードには、Microsoft .NET Framework 4.5 が必要です。


## 次のステップ

- [Microsoft Azure Certified for IoT][lnk-certified] プログラムの詳細については、こちらを参照してください。
- Certified for IoT デバイスを使用したソリューションの開発に関する詳細については、[こちら](http://azure.com/iotdev)を参照してください。


[lnk-iot-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-certified]: iot-hub-certified-devices-linux-c.md

<!---HONumber=AcomDC_0323_2016-->