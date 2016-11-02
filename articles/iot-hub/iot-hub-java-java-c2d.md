<properties
	pageTitle="IoT Hub を使用したクラウドからデバイスへのメッセージの送信 | Microsoft Azure"
	description="このチュートリアルに従って、Java を使用して Azure IoT Hub でクラウドからデバイスへのメッセージを送信する方法を学習します。"
	services="iot-hub"
	documentationCenter="java"
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="java"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/13/2016"
     ms.author="dobett"/>

# チュートリアル: IoT Hub と Java でクラウドからデバイスへのメッセージを送信する方法

[AZURE.INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## はじめに

Azure IoT Hub は、何百万もの IoT デバイスとアプリケーション バックエンドの間に信頼性のある保護された双方向通信を確立するのに役立つ、完全に管理されたサービスです。「[IoT Hub の概要]」チュートリアルには、IoT Hub の作成方法、IoT Hub でデバイス ID をプロビジョニングする方法、およびデバイスからクラウドへのメッセージを送信するシミュレーション済みデバイスをコード化する方法が示されています。

このチュートリアルは、[IoT Hub の概要]に関するページのチュートリアルに基づいて作成されており、次の方法について説明します。

- アプリケーションのクラウド バックエンドから IoT Hub を介して単一のデバイスにクラウドからデバイスへのメッセージを送信する。
- クラウドからデバイスへのメッセージをデバイスで受信する。
- IoT Hub からデバイスに送信されたメッセージの配信確認 (*フィードバック*) を、アプリケーションのクラウド バックエンドから要求する。

クラウドからデバイスへのメッセージの詳細については、「[IoT Hub 開発者ガイド][IoT Hub Developer Guide - C2D]」をご覧ください。

このチュートリアルの最後に、次の 2 つの Java コンソール アプリケーションを実行します。

* **simulated-device**。[IoT Hub の概要]に関するページで作成されたアプリケーションの修正バージョン。これは、IoT Hub に接続し、クラウドからデバイスへのメッセージを受け取ります。
* **send-c2d-messages**。これは、クラウドからデバイスへのメッセージを IoT Hub を介してシミュレーション済みデバイスに送信し、その配信確認を受け取ります。

> [AZURE.NOTE] IoT Hub には、Azure IoT device SDK を介した多数のデバイス プラットフォームや言語 (C、Java、Javascript など) に対する SDK サポートがあります。このチュートリアルのコード (一般的には Azure IoT Hub) にデバイスを接続するための詳しい手順については、[Azure IoT デベロッパー センター]のページを参照してください。

このチュートリアルを完了するには、以下が必要です。

+ Java SE 8。<br/> 「[Prepare your development environment (開発環境を準備する)][lnk-dev-setup]」には、このチュートリアルのために Java をインストールする方法があります。Windows と Linux の両方が対象となっています。

+ Maven 3。<br/> 「[Prepare your development environment (開発環境を準備する)][lnk-dev-setup]」には、このチュートリアルのために Maven をインストールする方法があります。Windows と Linux の両方が対象となっています。

+ アクティブな Azure アカウント。アカウントがない場合は、無料試用版アカウントを数分で作成することができます。詳細については、[Azure の無料試用版][lnk-free-trial]サイトを参照してください。

## シミュレーション済みデバイスでメッセージを受信する

このセクションでは、[IoT Hub の概要]に関するページで作成したシミュレーション済みデバイス アプリケーションを、クラウドからデバイスへのメッセージを IoT Hub から受信するように変更します。

1. テキスト エディターを使用し、simulated-device\\src\\main\\java\\com\\mycompany\\app\\App.java ファイルを開きます。

2. 次の **MessageCallback** クラスを、**App** クラス内のネストされたクラスとして追加します。デバイスが IoT Hub からメッセージを受信すると、**execute** メソッドが呼び出されます。この例では、デバイスはメッセージが完了したことを常にハブに通知します。

    ```
    private static class MessageCallback implements
    com.microsoft.azure.iothub.MessageCallback {
      public IotHubMessageResult execute(Message msg, Object context) {
        System.out.println("Received message from hub: "
          + new String(msg.getBytes(), Message.DEFAULT_IOTHUB_MESSAGE_CHARSET));

        return IotHubMessageResult.COMPLETE;
      }
    }
    ```

3. **main** メソッドを次のとおり変更して、**MessageCallback** インスタンスを作成し、**setMessageCallback** メソッドを呼び出してから、クライアントを開くようにします。

    ```
    client = new DeviceClient(connString, protocol);

    MessageCallback callback = new MessageCallback();
    client.setMessageCallback(callback, null);
    client.open();
    ```

    > [AZURE.NOTE] トランスポートとして AMQP の代わりに HTTP/1 を使用した場合、**DeviceClient** インスタンスが IoT Hub からのメッセージをチェックする頻度は低くなります (25 分に 1 回未満)。AMQP と HTTP/1 のサポートの相違点と、IoT Hub スロットルの詳細については、「[Azure IoT Hub 開発者ガイド][IoT Hub Developer Guide - C2D]」を参照してください。

## C2D メッセージを送信する

このセクションでは、クラウドからデバイスへのメッセージを、シミュレーション済みデバイス アプリに送信する Java コンソール アプリを作成します。[IoT Hub の概要]のチュートリアルで追加したデバイスのデバイス ID が必要です。また、IoT Hub の接続文字列も必要です ([Azure Portal] で確認できます)。

1. コマンド プロンプトで次のコマンドを使用して、**send-c2d-messages** という Maven プロジェクトを作成します。これは、1 つの長いコマンドであることに注意してください。

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=send-c2d-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. コマンド プロンプトで、新しい send-c2d-messages フォルダーに移動します。

3. テキスト エディターを使用して、send-c2d-messages フォルダー内の pom.xml ファイルを開き、次の依存関係を **dependencies** ノードに追加します。依存関係を追加することにより、アプリケーションの **iothub-java-service-client** パッケージを使用して、IoT Hub サービスと通信できます。

    ```
    <dependency>
      <groupId>com.microsoft.azure.iothub-java-client</groupId>
      <artifactId>iothub-java-service-client</artifactId>
      <version>1.0.7</version>
    </dependency>
    ```

4. pom.xml ファイルを保存して閉じます。

5. テキスト エディターを使用し、send-c2d-messages\\src\\main\\java\\com\\mycompany\\app\\App.java ファイルを開きます。

6. ファイルに次の **import** ステートメントを追加します。

    ```
    import com.microsoft.azure.iot.service.sdk.*;
    import java.io.IOException;
    import java.net.URISyntaxException;
    ```

7. 次のクラス レベルの変数を **App** クラスに追加し、**{yourhubconnectionstring}** と **{yourdeviceid}** を先にメモした値に置き換えます。

    ```
    private static final String connectionString = "{yourhubconnectionstring}";
    private static final String deviceId = "{yourdeviceid}";
    private static final IotHubServiceClientProtocol protocol = IotHubServiceClientProtocol.AMQPS;
    ```
    
8. **main** メソッドを次のコードに置き換えて、IoT Hub に接続し、デバイスにメッセージを送信して、デバイスがメッセージを受信して処理したことを示す受信確認を待機するようにします。

    ```
    public static void main(String[] args) throws IOException,
        URISyntaxException, Exception {
      ServiceClient serviceClient = ServiceClient.createFromConnectionString(
        connectionString, protocol);
      
      if (serviceClient != null) {
        serviceClient.open();
        FeedbackReceiver feedbackReceiver = serviceClient
          .getFeedbackReceiver(deviceId);
        if (feedbackReceiver != null) feedbackReceiver.open();

        Message messageToSend = new Message("Cloud to device message.");
        messageToSend.setDeliveryAcknowledgement(DeliveryAcknowledgement.Full);

        serviceClient.send(deviceId, messageToSend);
        System.out.println("Message sent to device");

        FeedbackBatch feedbackBatch = feedbackReceiver.receive(10000);
        if (feedbackBatch != null) {
          System.out.println("Message feedback received, feedback time: "
            + feedbackBatch.getEnqueuedTimeUtc().toString());
        }

        if (feedbackReceiver != null) feedbackReceiver.close();
        serviceClient.close();
      }
    }
    ```

    > [AZURE.NOTE] わかりやすくするために、このチュートリアルでは再試行ポリシーは実装しません。運用環境のコードでは、MSDN の記事「[Transient Fault Handling (一時的な障害の処理)]」で推奨されているように、再試行ポリシー (指数関数的バックオフなど) を実装することをお勧めします。

## アプリケーションの実行

これで、アプリケーションを実行する準備が整いました。

1. simulated-device フォルダーからコマンド プロンプトで次のコマンドを実行し、IoT Hub へのテレメトリの送信を開始して、IoT Hub から送信される (クラウドからデバイスへの) メッセージをリッスンします。

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App" 
    ```

    ![シミュレーション済みデバイス アプリを実行する][img-simulated-device]

2. send-c2d-messages フォルダーからコマンド プロンプトで次のコマンドを実行し、クラウドからデバイスへのメッセージを送信して、フィードバックの配信確認を待機します。

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![クラウドからデバイスへのメッセージを送信するコマンドを実行する][img-send-command]

## 次のステップ

このチュートリアルでは、クラウドからデバイスへのメッセージを送受信する方法を学習しました。

IoT Hub を使用する完全なエンド ツー エンド ソリューションの例については、[Azure IoT Suite] に関するドキュメントをご覧ください。

IoT Hub を使用したソリューションの開発に関する詳細については、[IoT Hub 開発者ガイド]をご覧ください。


<!-- Images -->
[img-simulated-device]: media/iot-hub-java-java-c2d/receivec2d.png
[img-send-command]: media/iot-hub-java-java-c2d/sendc2d.png
<!-- Links -->

[IoT Hub の概要]: iot-hub-java-java-getstarted.md
[IoT Hub Developer Guide - C2D]: iot-hub-devguide.md#c2d
[IoT Hub 開発者ガイド]: iot-hub-devguide.md
[Azure IoT デベロッパー センター]: http://www.azure.com/develop/iot
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/java-devbox-setup.md
[Transient Fault Handling (一時的な障害の処理)]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Azure portal]: https://portal.azure.com
[Azure IoT Suite]: https://azure.microsoft.com/documentation/suites/iot-suite/

<!---HONumber=AcomDC_0914_2016-->