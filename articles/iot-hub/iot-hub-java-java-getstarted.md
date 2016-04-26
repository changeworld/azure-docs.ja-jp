<properties
	pageTitle="Java で Azure IoT Hub を使用する方法 | Microsoft Azure"
	description="Java で Azure IoT Hub を使用する方法についてわかりやすく説明します。Azure IoT Hub と Java、Microsoft Azure IoT SDK を使用して IoT ソリューションを実装します。"
	services="iot-hub"
	documentationCenter="java"
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="java"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="03/22/2016"
     ms.author="dobett"/>

# Azure IoT Hub for Java の使用

[AZURE.INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

## はじめに

Azure IoT Hub は、何百万もの IoT (モノのインターネット) デバイスとソリューション バック エンド間で、セキュリティで保護された信頼性のある双方向通信を実現する、完全に管理されたサービスです。IoT プロジェクトが直面する最も大きな課題の 1 つは、ソリューション バックエンドにデバイスを確実かつ安全に接続する方法です。この課題に対応するために、IoT Hub は次の機能を備えています。

- 非常にスケール性が高く信頼性に優れた、デバイスとクラウド間の双方向メッセージングを提供します。
- デバイスごとのセキュリティ資格情報とアクセス制御を使用して、セキュリティで保護された通信を可能します。
- 最も一般的な言語とプラットフォームのデバイスのライブラリが含まれます。

このチュートリアルでは、次の操作方法について説明します。

- Azure ポータルを使用して IoT Hub を作成する。
- IoT Hub におけるデバイス ID を作成する。
- クラウド バックエンドにテレメトリを送信するシミュレーション対象デバイスを作成する。

このチュートリアルの最後には、次の 3 つの Java コンソール アプリケーションが作成されています。

* **create-device-identity**。デバイス ID と関連付けられているセキュリティ キーを作成し、シミュレーション対象デバイスを接続します。
* **read-d2c-messages**。シミュレーション対象デバイスから送信されたテレメトリを表示します。
* **simulated-device**。以前に作成したデバイス ID で IoT ハブに接続し、AMQPS プロトコルを使用して 1 秒ごとにテレメトリ メッセージを送信します。

> [AZURE.NOTE] デバイス上で動作するアプリケーションの作成とソリューションのバックエンドで動作するアプリケーションの作成に利用できる各種 SDK に関する情報は、「[IoT Hub SDK][lnk-hub-sdks]」の記事で取り上げています。

このチュートリアルで行う作業には次のものが必要となります。

+ Java SE 8。<br/> 「[Prepare your development environment (開発環境を準備する)][lnk-dev-setup]」には、このチュートリアルのために Java をインストールする方法があります。Windows と Linux の両方が対象となっています。

+ Maven 3。<br/> 「[Prepare your development environment (開発環境を準備する)][lnk-dev-setup]」には、このチュートリアルのために Maven をインストールする方法があります。Windows と Linux の両方が対象となっています。

+ アクティブな Azure アカウント。<br/>アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、[Azure の無料試用版サイト][lnk-free-trial]を参照してください。

[AZURE.INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

最後の手順として、[IoT Hub] ブレードの **[設定]** をクリックし、**[設定]** ブレードの **[メッセージング]** をクリックします。**[メッセージング]** ブレードで、**[イベント ハブと互換性のある名前]** と **[イベント ハブと互換性のあるエンドポイント]** をメモします。この値は **read-d2c-messages** アプリケーションを作成するときに必要になります。

![][6]

これで IoT Hub が作成され、このチュートリアルの残りを完了するために必要な IoT Hub ホスト名、IoT Hub 接続文字列、Event Hub 対応名、Event Hub 対応エンドポイントが与えられました。

[AZURE.INCLUDE [iot-hub-get-started-cloud-java](../../includes/iot-hub-get-started-cloud-java.md)]


[AZURE.INCLUDE [iot-hub-get-started-device-java](../../includes/iot-hub-get-started-device-java.md)]

## アプリケーションの実行

これで、アプリケーションを実行する準備が整いました。

1. read-d2c フォルダーで、コマンド プロンプトで次のコマンドを実行し、IoT Hub の監視を開始します。

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App" 
    ```

    ![][7]

2. simulated-device フォルダーで、コマンド プロンプトで次のコマンドを実行し、IoT Hub へのテレメトリ データの送信を開始します。

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App" 
    ```

    ![][8]

3. [Azure ポータル][lnk-portal]の **[使用量]** タイルには、Hub に送信されたメッセージ数が表示されます。

    ![][43]

## 次のステップ

このチュートリアルでは、ポータルで新しい IoT Hub を構成し、ハブの ID レジストリにデバイス ID を作成しました。シミュレーション対象デバイス アプリでデバイスからクラウドへのメッセージをハブに送信できるようにするために、このデバイス ID を使用しました。また、ハブで受け取ったメッセージを表示するアプリを作成しました。IoT Hub の機能や、他の IoT のシナリオを次のチュートリアルでさらに詳しく説明しています。

- 「[IoT Hub を使用したクラウドからデバイスへのメッセージの送信][lnk-c2d-tutorial]」には、デバイスにメッセージを送信し、IoT Hub によって生成される配信フィードバックを処理する方法が示されています。
- 「[デバイスからクラウドへのメッセージの処理][lnk-process-d2c-tutorial]」には、デバイスから送信されるテレメトリおよび対話型メッセージを確実に処理する方法が示されています。
- 「[デバイスからのファイルのアップロード][lnk-upload-tutorial]」では、デバイスからのファイル アップロードを容易にするためにクラウドからデバイスへのメッセージを活用したパターンについて説明しています。

<!-- Images. -->
[6]: ./media/iot-hub-java-java-getstarted/create-iot-hub6.png
[7]: ./media/iot-hub-java-java-getstarted/runapp1.png
[8]: ./media/iot-hub-java-java-getstarted/runapp2.png
[43]: ./media/iot-hub-csharp-csharp-getstarted/usage.png

<!-- Links -->
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/java/device/doc/devbox_setup.md
[lnk-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md
[lnk-upload-tutorial]: iot-hub-csharp-csharp-file-upload.md

[lnk-hub-sdks]: iot-hub-sdks-summary.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

<!---HONumber=AcomDC_0420_2016-->