<properties
	pageTitle="IoT Hub の概要 | Microsoft Azure"
	description="このチュートリアルに従って、C# で Azure IoT Hub を使用します。"
	services="iot-hub"
	documentationCenter=".net"
	authors="fsautomata"
	manager="kevinmil"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="csharp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="tbd"
     ms.date="09/29/2015"
     ms.author="elioda"/>

# IoT Hub の概要

## はじめに

Azure IoT Hub は、何百万もの IoT デバイスとアプリケーション バック エンドの間に信頼性のある保護された双方向通信を確立できる、完全に管理されたサービスです。IoT プロジェクトが直面する最も大きな課題の 1 つは、アプリケーション バック エンドにデバイスを確実かつ安全に接続する方法です。このシナリオを簡素化するために、Azure IoT Hub は、信頼性に優れた非常にスケール性の高いデバイスとクラウド間のメッセージングを提供し、デバイスごとのセキュリティ資格情報とアクセス制御を使用してセキュリティで保護された通信を可能にし、最も一般的な言語とプラットフォームのデバイス ライブラリを含みます。

このチュートリアルでは、Azure ポータルを使用して IoT Hub を作成する方法を示します。また、IoT Hub でデバイス ID を作成し、デバイスからクラウドへのメッセージを送信して、クラウド バックエンドからこれらのメッセージを受信するシミュレーション対象デバイスを作成する方法も示します。

このチュートリアルの最後には、次の 3 つの Windows コンソール アプリケーションが作成されていることになります。

* **CreateDeviceIdentity**。デバイス ID と関連付けられているセキュリティ キーを作成し、シミュレーション対象デバイスを接続します。
* **ReadDeviceToCloudMessages**。デバイスからクラウドへのメッセージを読み取り、その内容を表示します。
* **SimulatedDevice**。以前に作成したデバイス ID で IoT Hub に接続して、1 秒ごとにデバイスからクラウドへのメッセージを送信します。

> [AZURE.NOTE]IoT Hub には、Azure IoT デバイス SDK を介した多数のデバイス プラットフォームや言語 (C、Java、Javascript など) に対する SDK サポートがあります。このチュートリアルのコード (一般的には Azure IoT Hub) にデバイスを接続するための詳しい手順については、「[Azure IoT デベロッパー センター]」を参照してください。Java および Node 用の Azure IoT サービス SDK は、近日リリース予定です。

このチュートリアルを完了するには、以下が必要になります。

+ Microsoft Visual Studio 2015、

+ アクティブな Azure アカウント<br/>アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、「Azure の無料試用版サイト」(http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fja-JP%2Fdevelop%2Fiot%2Ftutorials%2Fgetstarted%2F target=”\_blank”) を参照してください。

## IoT Hub の作成

1. [Azure プレビュー ポータル]にログオンします。

2. ジャンプバーで、**[新規]**、**[モノのインターネット]**、**[IoT Hub]** の順にクリックします。

   	![][1]

3. **[IoT Hub の新規作成]** ブレードで、IoT Hub の必要な構成を指定します。

   	![][2]

    * **[名前]** ボックスに、IoT Hub を識別するための名前を入力します。**名前**が検証されると、緑色のチェック マークが **[名前]** ボックスに表示されます。
    * 必要に応じて、**[価格とスケール レベル]** を変更します。このチュートリアルでは特定のレベルは必要ありません。
    * **[リソース グループ]** ボックスで、新しいリソース グループを作成するか、既存のものを選択します。詳細については、[リソース グループを使用した Azure リソースの管理](resource-group-portal.md)に関するページを参照してください。
    * **[場所]** を使用して、IoT Hub のホストの地理的場所を指定します。  


4. 新しい IoT Hub オプションを構成したら、**[作成]** をクリックします。IoT Hub が作成されるまで数分かかる場合があります。状態を確認するには、スタートボードで進行状況を監視してください。または、通知セクションから進行状況を監視できます。

    ![][3]


5. IoT Hub が正常に作成されたら、新しい IoT Hub のブレードを開き、URI をメモして、上部の**キー** アイコンを選択します。

   	![][4]

6. **iothubowner** という共有アクセス ポリシーを選択し、右のブレードにある接続文字列をコピーしてメモします。

   	![][5]

これで IoT Hub が作成され、このチュートリアルを完了するために必要な URI と接続文字列の準備ができました。

[AZURE.INCLUDE [iot-hub-get-started-cloud-csharp](../../includes/iot-hub-get-started-cloud-csharp.md)]


[AZURE.INCLUDE [iot-hub-get-started-device-csharp](../../includes/iot-hub-get-started-device-csharp.md)]

## アプリケーションの実行

これで、アプリケーションを実行する準備が整いました。

1.	Visual Studio 内でソリューションを右クリックし、**[スタートアップ プロジェクトの設定...]** を選択します。**[マルチ スタートアップ プロジェクト]** を選択してから、**ProcessDeviceToCloudMessages** と **SimulatedDevice** の両方のアプリの **[開始]** アクションを選択します。

   	![][41]

2.	**F5** キーを押すと、両方のアプリケーションが開始され、シミュレーション対象アプリによってメッセージが送信され、プロセッサ アプリで受信されたことを確認できます。

   	![][42]

## 次のステップ

このチュートリアルでは、新しい IoT Hub を設定し、ハブの ID レジストリでデバイス ID を作成し、この ID を使用して、デバイスからクラウドへのメッセージを送信するシミュレーション対象デバイスをプログラミングしました。次のチュートリアルで IoT Hub の機能やシナリオをさらに詳しく調べることができます。

- 「[IoT Hub を使用したクラウドからデバイスへのメッセージの送信]」には、デバイスにメッセージを送信し、IoT Hub によって生成される配信フィードバックを処理する方法が示されています。
- 「[デバイスからクラウドへのメッセージの処理]」には、デバイスから送信されるテレメトリおよび対話型メッセージを確実に処理する方法が示されています。
- 「[デバイスからのファイルのアップロード]」では、デバイスからのファイル アップロードを容易にするためにクラウドからデバイスへのメッセージを活用したパターンについて説明しています。

IoT Hub に関するその他の情報:

* [IoT Hub の概要]
* [IoT Hub 開発者ガイド]
* [IoT Hub のガイダンス]
* [サポートされているデバイスのプラットフォームおよび言語][Supported devices]
* [Azure IoT デベロッパー センター]

<!-- Images. -->
[1]: ./media/iot-hub-csharp-csharp-getstarted/create-iot-hub1.png
[2]: ./media/iot-hub-csharp-csharp-getstarted/create-iot-hub2.png
[3]: ./media/iot-hub-csharp-csharp-getstarted/create-iot-hub3.png
[4]: ./media/iot-hub-csharp-csharp-getstarted/create-iot-hub4.png
[5]: ./media/iot-hub-csharp-csharp-getstarted/create-iot-hub5.png

[41]: ./media/iot-hub-csharp-csharp-getstarted/run-apps1.png
[42]: ./media/iot-hub-csharp-csharp-getstarted/run-apps2.png

<!-- Links -->
[Azure プレビュー ポータル]: https://portal.azure.com/

[IoT Hub を使用したクラウドからデバイスへのメッセージの送信]: iot-hub-csharp-csharp-c2d.md
[デバイスからクラウドへのメッセージの処理]: iot-hub-csharp-csharp-process-d2c.md
[デバイスからのファイルのアップロード]: iot-hub-csharp-csharp-file-upload.md

[IoT Hub の概要]: iot-hub-what-is-iot-hub.md
[IoT Hub のガイダンス]: iot-hub-guidance.md
[IoT Hub 開発者ガイド]: iot-hub-devguide.md
[IoT Hub Supported Devices]: iot-hub-supported-devices.md
[Get started with IoT Hub]: iot-hub-csharp-csharp-getstarted.md
[Supported devices]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/tested_configurations.md
[Azure IoT デベロッパー センター]: http://www.azure.com/iotdev



 

<!---HONumber=Oct15_HO1-->