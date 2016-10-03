<properties
 pageTitle="IoT ゲートウェイの内側で管理されたデバイスを有効にする | Microsoft Azure"
 description="Gateway SDK を使用して作成した IoT Gateway と、IoT Hub によって管理されるデバイスの使用に関するガイダンスを示します。"
 services="iot-hub"
 documentationCenter=""
 authors="chipalost"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="04/29/2016"
 ms.author="cstreet"/>
 
# IoT ゲートウェイの内側で管理されたデバイスを有効にする

## 基本的なデバイスの分離

多くの場合、組織では IoT ゲートウェイを使用して、IoT ソリューション全体のセキュリティを強化します。一部のデバイスはデータをクラウドに送信する必要がありますが、インターネット上の脅威から自身を保護することはできません。外部との通信をゲートウェイを通じて行うことで、これらのデバイスを外部の脅威から保護できます。

ゲートウェイは、セキュリティで保護された環境とオープンなインターネットの間の境界上に配置されます。デバイスはゲートウェイと通信し、ゲートウェイはクラウド上の正しい宛先にメッセージを渡します。ゲートウェイは外部の脅威に対して強化されているので、未承認の要求はブロックされ、承認済みの着信トラフィックは許可されて正しいデバイスに転送されます。

![][1]

ゲートウェイの内側に自身を保護するデバイスを配置して、セキュリティをさらに強化することもできます。この場合は、すべてのデバイスの OS を更新する代わりに、ゲートウェイの OS に最新の脆弱性に対する修正プログラムを適用した状態を維持するだけで済みます。

## 分離とインテリジェンス

強化されたルーターは、単にデバイスを分離するゲートウェイとしては十分に機能します。ただし、多くの IoT ソリューションでは、ゲートウェイにデバイスを分離する以上のインテリジェンスが求められます。たとえば、クラウドからデバイスを管理する必要があるとします。ソリューション内のクラウド管理の部分には、デバイス管理の標準プロトコルである LWM2M を使用できます。ただし、デバイスは非 TCP/IP 対応プロトコルを使用してテレメトリを送信します。さらに、デバイスで生成される大量のデータのうち、アップロードする必要があるのはフィルター処理された一部のテレメトリのみです。ビルドするソリューションには、異なる 2 つのデータ ストリームに対応した IoT ゲートウェイを組み込むことができます。ゲートウェイでは、以下を行う必要があります。

-   **テレメトリ**を認識し、フィルター処理し、ゲートウェイを介してクラウドにアップロードします。ゲートウェイは、デバイスとクラウドの間でデータを転送するだけのシンプルなルーターではなくなります。

-   デバイスとクラウドの間で、単に **LWM2M デバイス管理データ**を交換します。ゲートウェイは、着信データを認識する必要はなく、デバイスとクラウド間のデータの受け渡しが確実に行われることだけを確保します。

このシナリオを次の図に示します。

![][2]

## ソリューション: Azure IoT デバイス管理と Gateway SDK 

パブリック プレビュー リリースの [Azure IoT デバイス管理][lnk-device-management]とベータ リリースの [Azure IoT Gateway SDK] により、このシナリオが実現します。ゲートウェイは、次のように各データ ストリームを処理します。

-   **テレメトリ**: テレメトリ データを認識し、フィルター処理し、クラウドに送信するパイプラインを Gateway SDK を使用して作成できます。Gateway SDK には、開発者に代わってこのパイプラインの一部を実装するコードが用意されています。SDK のアーキテクチャの詳細については、[IoT Gateway SDK の概要に関する記事][lnk-gateway-get-started]のチュートリアルを参照してください。

-   **デバイス管理**: Azure デバイス管理には、管理コマンドをデバイスに発行するクラウド インターフェイスのほか、デバイス上でも実行する LWM2M クライアントが用意されています。
    
    デバイスと IoT Hub 間の LWM2M データの交換を処理する必要はないため、ゲートウェイに特別なロジックは必要ありません。多くの最新オペレーティング システムが備えているインターネット接続の共有機能をゲートウェイで有効にすることで、LWM2M データ交換を有効にできます。Gateway SDK ではさまざまなオペレーティング システムがサポートされているため、このシナリオに適したオペレーティング システムを選択できます。サポートされている多くのオペレーティング システムのうちの 2 つ、[Windows 10] と [Ubuntu] でインターネット接続の共有を有効にする手順を参照してください。

次の図に、[Azure IoT デバイス管理][lnk-device-management]と [Azure IoT Gateway SDK] を使用したシナリオのアーキテクチャの概要を示します。

![][3]

## 次のステップ

Gateway SDK の使用方法については、次のチュートリアルを参照してください。

- [IoT Gateway SDK - Get started using Linux][lnk-gateway-get-started] \(IoT Gateway SDK – Linux の使用)
- [IoT Gateway SDK – send device-to-cloud messages with a simulated device using Linux][lnk-gateway-simulated] \(IoT Gateway SDK – Linux を使用してシミュレートされたデバイスから D2C メッセージを送信する)

IoT Hub を使用したデバイス管理の詳細については、「[Azure IoT Hub デバイス管理 (DM) クライアント ライブラリの概要][lnk-library-c]」をご覧ください。

IoT Hub の機能を詳しく調べるには、次のリンクを使用してください。

- [ソリューションの設計][lnk-design]
- [開発者ガイド][lnk-devguide]
- [Gateway SDK を使用したデバイスのシミュレーション][lnk-gateway]
- [Azure ポータルを使用した IoT Hub の管理][lnk-portal]

<!-- Images and links -->
[1]: media/iot-hub-gateway-device-management/overview.png
[2]: media/iot-hub-gateway-device-management/manage.png
[Azure IoT Gateway SDK]: https://github.com/Azure/azure-iot-gateway-sdk/
[Windows 10]: http://windows.microsoft.com/ja-JP/windows/using-internet-connection-sharing#1TC=windows-7
[Ubuntu]: https://help.ubuntu.com/community/Internet/ConnectionSharing
[3]: media/iot-hub-gateway-device-management/manage_2.png
[lnk-gateway-get-started]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-gateway-simulated]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-device-management]: iot-hub-device-management-overview.md

[lnk-tutorial-twin]: iot-hub-device-management-device-twin.md
[lnk-tutorial-queries]: iot-hub-device-management-device-query.md
[lnk-tutorial-jobs]: iot-hub-device-management-device-jobs.md
[lnk-dm-gateway]: iot-hub-gateway-device-management.md
[lnk-library-c]: iot-hub-device-management-library.md

[lnk-design]: iot-hub-guidance.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-portal]: iot-hub-manage-through-portal.md

<!---HONumber=AcomDC_0713_2016-->