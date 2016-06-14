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
     ms.date="06/06/2016"
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

## デバイス ID の作成

このセクションでは、IoT ハブの ID レジストリに新しいデバイス ID を作成する Java コンソール アプリケーションを作成します。IoT Hub に接続するデバイスは、あらかじめデバイス ID レジストリに登録されている必要があります。詳細については、「[IoT Hub 開発者ガイド][lnk-devguide-identity]」の「**デバイス ID レジストリ**」を参照してください。このコンソール アプリケーションを実行すると、デバイスからクラウドへのメッセージを IoT Hub に送信するときにそのデバイスを識別する一意の ID とキーが生成されます。

1. 「iot-java-get-started」という名前の空のフォルダーを新規作成します。コマンド プロンプトで次のコマンドを実行し、iot-java-get-started フォルダーに **create-device-identity** という名前の Maven プロジェクトを新しく作成します。これは 1 つの長いコマンドです。

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=create-device-identity -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. コマンド プロンプトで、新しい「create-device-identity」フォルダーに移動します。

3. テキスト エディターを使用し、create-device-identity フォルダー内の pom.xml ファイルを開き、次の依存関係を **dependencies** ノードに追加します。それにより、アプリケーションで iothub-service-sdk パッケージを利用できるようになります。

    ```
    <dependency>
      <groupId>com.microsoft.azure.iothub-java-client</groupId>
      <artifactId>iothub-java-service-client</artifactId>
      <version>1.0.2</version>
    </dependency>
    ```
    
4. pom.xml ファイルを保存して閉じます。

5. テキスト エディターを使用し、create-device-identity\\src\\main\\java\\com\\mycompany\\app\\App.java ファイルを開きます。

6. ファイルに次の **import** ステートメントを追加します。

    ```
    import com.microsoft.azure.iot.service.exceptions.IotHubException;
    import com.microsoft.azure.iot.service.sdk.Device;
    import com.microsoft.azure.iot.service.sdk.RegistryManager;

    import java.io.IOException;
    import java.net.URISyntaxException;
    ```

7. 次のクラスレベルの変数を **App** クラスに追加し、**{yourhubname}** と **{yourhubkey}** を先にメモした値に置き換えます。

    ```
    private static final String connectionString = "HostName={yourhubname}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={yourhubkey}";
    private static final String deviceId = "javadevice";
    
    ```
    
8. **main** メソッドのシグネチャを変更し、下の例外を追加します。

    ```
    public static void main( String[] args ) throws IOException, URISyntaxException, Exception
    ```
    
9. **main** メソッドの本文として次のコードを追加します。このコードにより、*javadevice* という名前のデバイスが IoT Hub ID レジストリにない場合は作成されます。その後、デバイス ID とキーが表示されますが、これは後で必要になります。

    ```
    RegistryManager registryManager = RegistryManager.createFromConnectionString(connectionString);

    Device device = Device.createFromId(deviceId, null, null);
    try {
      device = registryManager.addDevice(device);
    } catch (IotHubException iote) {
      try {
        device = registryManager.getDevice(deviceId);
      } catch (IotHubException iotf) {
        iotf.printStackTrace();
      }
    }
    System.out.println("Device id: " + device.getDeviceId());
    System.out.println("Device key: " + device.getPrimaryKey());
    ```

10. App.java ファイルを保存して閉じます。

11. Maven を利用して **create-device-identity** アプリケーションをビルドするには、コマンド プロンプトで create-device-identity フォルダーに移動し、次のコマンドを実行します。

    ```
    mvn clean package -DskipTests
    ```

12. Maven を利用して **create-device-identity** アプリケーションを実行するには、コマンド プロンプトで create-device-identity フォルダーに移動し、次のコマンドを実行します。

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

13. **デバイス ID** と**デバイス キー**をメモします。後でデバイスとして IoT Hub に接続するアプリケーションを作成するときに必要になります。

> [AZURE.NOTE] IoT Hub の ID レジストリには、ハブに対するアクセスの安全性を確保するためのデバイス ID だけが格納されます。セキュリティ資格情報として使用するキーとデバイス ID、そして個々のデバイスについてアクセスを無効にすることのできる有効/無効フラグが格納されます。その他デバイス固有のメタデータをアプリケーションで保存する必要がある場合は、アプリケーション固有のストアを使用する必要があります。詳細については、「[IoT Hub 開発者ガイド][lnk-devguide-identity]」を参照してください。

## デバイスからクラウドへのメッセージの受信

このセクションでは、IoT Hub からのデバイスからクラウドへのメッセージを読み込む Java コンソール アプリケーションを作成します。Iot Hub は、デバイスからクラウドへのメッセージを読み取るための、[イベント ハブ][lnk-event-hubs-overview]と互換性のあるエンドポイントを公開します。わかりやすくするために、このチュートリアルで作成するリーダーは基本的なものであり、高スループットのデプロイメントには適していません。[デバイスからクラウドへのメッセージの処理][lnk-process-d2c-tutorial]に関するチュートリアルでは、デバイスからクラウドへのメッセージを規模を拡大して処理する方法を紹介しています。「[Event Hubs の使用][lnk-eventhubs-tutorial]」チュートリアルでは、Event Hubs からのメッセージを処理する方法について詳しく説明しています。また、このチュートリアルは IoT Hub のイベント ハブと互換性のあるエンドポイントに当てはまります。

> [AZURE.NOTE] Event Hubs と互換性のあるエンドポイントは、常に、デバイスからクラウドへのメッセージを読み取るために AMQPS プロトコルを使用します。

1. コマンド プロンプトで次のコマンドを実行し、「*デバイス ID の作成*」セクションで作成した iot-java-get-started フォルダーに **read-d2c-messages** という名前の Maven プロジェクトを新しく作成します。これは 1 つの長いコマンドです。

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=read-d2c-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. コマンド プロンプトで、新しい「read-d2c-messages」フォルダーに移動します。

3. テキスト エディターを使用して、read-d2c-messages フォルダー内の pom.xml ファイルを開き、次の依存関係を **dependencies** ノードに追加します。これで、アプリケーションの eventhubs-client パッケージを利用して、イベント ハブと互換性のあるエンドポイントから読み込めるようにすることができます。

    ```
    <dependency> 
        <groupId>com.microsoft.azure</groupId> 
        <artifactId>azure-eventhubs</artifactId> 
        <version>0.7.1</version> 
    </dependency>
    ```

4. pom.xml ファイルを保存して閉じます。

5. テキスト エディターを使用し、read-d2c-messages\\src\\main\\java\\com\\mycompany\\app\\App.java ファイルを開きます。

6. ファイルに次の **import** ステートメントを追加します。

    ```
    import java.io.IOException;
    import com.microsoft.azure.eventhubs.*;
    import com.microsoft.azure.servicebus.*;
    
    import java.io.IOException;
    import java.nio.charset.Charset;
    import java.time.*;
    import java.util.Collection;
    import java.util.concurrent.ExecutionException;
    import java.util.function.*;
    import java.util.logging.*;
    ```

7. 次のクラスレベルの変数を **App** クラスに追加します。**{youriothubkey}**、**{youreventhubcompatiblenamespace}**、**{youreventhubcompatiblename}** を先にメモした値に置き換えます。**{youreventhubcompatiblenamespace}** プレースホルダーの値は、**イベント ハブと互換性のあるエンドポイント**から取得され、**xyznamespace** の形式になります (つまり、ポータルのイベント ハブと互換性のあるエンドポイントの ****sb://** プレフィックスと **.servicebus.windows.net** サフィックスを削除します)。

    ```
    private static String namespaceName = "{youreventhubcompatiblenamespace}";
    private static String eventHubName = "{youreventhubcompatiblename}";
    private static String sasKeyName = "iothubowner";
    private static String sasKey = "{youriothubkey}";
    private static long now = System.currentTimeMillis();
    ```

8. 次の **receiveMessages** メソッドを **App** クラスに追加します。このメソッドは **EventHubClient** インスタンスを作成してイベント ハブと互換性のあるエンドポイントに接続し、**PartitionReceiver** インスタンスを非同期的に作成してイベント ハブ パーティションから読み取ります。これは、アプリケーションが終了するまでループを続け、メッセージの詳細を出力します。

    ```
    private static EventHubClient receiveMessages(final String partitionId)
    {
      EventHubClient client = null;
      try {
        ConnectionStringBuilder connStr = new ConnectionStringBuilder(namespaceName, eventHubName, sasKeyName, sasKey);
        client = EventHubClient.createFromConnectionString(connStr.toString()).get();
      }
      catch(Exception e) {
        System.out.println("Failed to create client: " + e.getMessage());
        System.exit(1);
      }
      try {
        client.createReceiver( 
          EventHubClient.DEFAULT_CONSUMER_GROUP_NAME,  
          partitionId,  
          Instant.now()).thenAccept(new Consumer<PartitionReceiver>()
        {
          public void accept(PartitionReceiver receiver)
          {
            System.out.println("** Created receiver on partition " + partitionId);
            try {
              while (true) {
                Iterable<EventData> receivedEvents = receiver.receive().get();
                int batchSize = 0;
                if (receivedEvents != null)
                {
                  for(EventData receivedEvent: receivedEvents)
                  {
                    System.out.println(String.format("Offset: %s, SeqNo: %s, EnqueueTime: %s", 
                      receivedEvent.getSystemProperties().getOffset(), 
                      receivedEvent.getSystemProperties().getSequenceNumber(), 
                      receivedEvent.getSystemProperties().getEnqueuedTime()));
                    System.out.println(String.format("| Device ID: %s", receivedEvent.getProperties().get("iothub-connection-device-id")));
                    System.out.println(String.format("| Message Payload: %s", new String(receivedEvent.getBody(),
                      Charset.defaultCharset())));
                    batchSize++;
                  }
                }
                System.out.println(String.format("Partition: %s, ReceivedBatch Size: %s", partitionId,batchSize));
              }
            }
            catch (Exception e)
            {
              System.out.println("Failed to receive messages: " + e.getMessage());
            }
          }
        });
      }
      catch (Exception e)
      {
        System.out.println("Failed to create receiver: " + e.getMessage());
      }
      return client;
    }
    ```

    > [AZURE.NOTE] 受信側が実行開始後、IoT Hub に送信されたメッセージのみを読み込むように、このメソッドは受信側の構築時にフィルターを称します。現在の一連のメッセージを確認できるので、テスト環境ではこれは便利ですが、運用環境の場合、コードですべてのメッセージが処理されることを確認する必要があります。詳細については、[IoT Hub のデバイスからクラウドへのメッセージを処理する方法][lnk-process-d2c-tutorial]に関するチュートリアルを参照してください。

9. **main** メソッドのシグネチャを変更し、下の例外を追加します。

    ```
    public static void main( String[] args ) throws IOException
    ```

10. **App** クラスの **main** メソッドに次のコードを追加します。このコードは 2 つの **EventHubClient** インスタンスと **PartitionReceiver** インスタンスを作成し、メッセージの処理が完了したらアプリケーションを終了できるようにします。

    ```
    EventHubClient client0 = receiveMessages("0");
    EventHubClient client1 = receiveMessages("1");
    System.out.println("Press ENTER to exit.");
    System.in.read();
    try
    {
      client0.closeSync();
      client1.closeSync();
      System.exit(0);
    }
    catch (ServiceBusException sbe)
    {
      System.exit(1);
    }
    ```

    > [AZURE.NOTE] このコードでは、F1 (free) レベルで IoT hub を作成したと想定しています。無料の IoT Hub は、2 つのパーティションがあります。名前は「0」と「1」です。

11. App.java ファイルを保存して閉じます。

12. Maven を使用して **read-d2c-messages** アプリケーションをビルドするには、コマンド プロンプトで read-d2c-messages フォルダーに移動し、次のコマンドを実行します。

    ```
    mvn clean package -DskipTests
    ```

## シミュレーション対象デバイス アプリの作成

このセクションでは、デバイスからクラウドへのメッセージを IoT ハブに送信するデバイスをシミュレートする Java コンソール アプリを作成します。

1. コマンド プロンプトで次のコマンドを実行し、「*デバイス ID の作成*」セクションで作成した iot-java-get-started フォルダーに **simulated-device** という名前の Maven プロジェクトを新しく作成します。これは 1 つの長いコマンドです。

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. コマンド プロンプトで、新しい「simulated-device」フォルダーに移動します。

3. テキスト エディターを使用して、simulated-device フォルダー内の pom.xml ファイルを開き、次の依存関係を **dependencies** ノードに追加します。これにより、アプリケーションの iothub-java-client パッケージを使用し、IoT Hub と通信し、Java オブジェクトを JSON にシリアル化できます。

    ```
    <dependency>
      <groupId>com.microsoft.azure.iothub-java-client</groupId>
      <artifactId>iothub-java-device-client</artifactId>
      <version>1.0.2</version>
    </dependency>
    <dependency>
      <groupId>com.google.code.gson</groupId>
      <artifactId>gson</artifactId>
      <version>2.3.1</version>
    </dependency>
    ```

4. pom.xml ファイルを保存して閉じます。

5. テキスト エディターを使用し、simulated-device\\src\\main\\java\\com\\mycompany\\app\\App.java ファイルを開きます。

6. ファイルに次の **import** ステートメントを追加します。

    ```
    import com.microsoft.azure.iothub.DeviceClient;
    import com.microsoft.azure.iothub.IotHubClientProtocol;
    import com.microsoft.azure.iothub.Message;
    import com.microsoft.azure.iothub.IotHubStatusCode;
    import com.microsoft.azure.iothub.IotHubEventCallback;
    import com.microsoft.azure.iothub.IotHubMessageResult;
    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.security.InvalidKeyException;
    import java.util.Random;
    import javax.naming.SizeLimitExceededException;
    import com.google.gson.Gson;
    ```

7. 次のクラスレベルの変数を **App** クラスに追加し、**{youriothubname}** を IoT Hub 名に、**{yourdeviceid}** と **{yourdevicekey}** を「*デバイス ID の作成*」セクションで生成したデバイス値に置き換えます。

    ```
    private static String connString = "HostName={youriothubname}.azure-devices.net;DeviceId={yourdeviceid};SharedAccessKey={yourdevicekey}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.AMQPS;
    private static boolean stopThread = false;
    ```

    このサンプル アプリケーションでは、**DeviceClient** オブジェクトをインスタンス化するときに **protocol** 変数が使用されます。HTTPS プロトコルと AMQPS プロトコルのいずれかを利用して IoT Hub と通信できます。

8. 次の入れ子になった **TelemetryDataPoint** クラスを **App** クラス内に追加し、デバイスから IoT Hub に送信するテレメトリ データを指定します。

    ```
    private static class TelemetryDataPoint {
      public String deviceId;
      public double windSpeed;
        
      public String serialize() {
        Gson gson = new Gson();
        return gson.toJson(this);
      }
    }
    ```

9. 次の入れ子になった **EventCallback** クラスを **App** クラス内に追加し、シミュレートされたデバイスからのメッセージを処理するときに IoT Hub が返す受信確認ステータスを表示します。このメソッドにより、メッセージが処理されたときのアプリケーションのメイン スレッドも通知されます。

    ```
    private static class EventCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to message with status " + status.name());
      
        if (context != null) {
          synchronized (context) {
            context.notify();
          }
        }
      }
    }
    ```

10. 次の入れ子になった **MessageSender** クラスを **App** クラス内に追加します。このクラスの **run** メソッドは、IoT Hub に送信するサンプルのテレメトリ データを生成し、受信確認を待ってから次のメッセージを送信します。

    ```
    private static class MessageSender implements Runnable {
      public volatile boolean stopThread = false;

      public void run()  {
        try {
          double avgWindSpeed = 10; // m/s
          Random rand = new Random();
          DeviceClient client;
          client = new DeviceClient(connString, protocol);
          client.open();
        
          while (!stopThread) {
            double currentWindSpeed = avgWindSpeed + rand.nextDouble() * 4 - 2;
            TelemetryDataPoint telemetryDataPoint = new TelemetryDataPoint();
            telemetryDataPoint.deviceId = "myFirstDevice";
            telemetryDataPoint.windSpeed = currentWindSpeed;
      
            String msgStr = telemetryDataPoint.serialize();
            Message msg = new Message(msgStr);
            System.out.println(msgStr);
        
            Object lockobj = new Object();
            EventCallback callback = new EventCallback();
            client.sendEventAsync(msg, callback, lockobj);
    
            synchronized (lockobj) {
              lockobj.wait();
            }
            Thread.sleep(1000);
          }
          client.close();
        } catch (Exception e) {
          e.printStackTrace();
        }
      }
    }
    ```

    このメソッドは、IoT Hub が前のメッセージを確認してから 1 秒後に新しい「デバイスからクラウドへの」メッセージを送信します。このメッセージには、JSON 形式でシリアル化されたオブジェクトが、デバイス ID、ランダムに生成された番号と共に含まれ、これによって風速センサーがシミュレートされます。

11. **main** メソッドを、デバイスからクラウドへのメッセージを IoT Hub に送信するスレッドを作成する次のコードに置き換えます。

    ```
    public static void main( String[] args ) throws IOException, URISyntaxException {
    
      MessageSender ms0 = new MessageSender();
      Thread t0 = new Thread(ms0);
      t0.start(); 
    
      System.out.println("Press ENTER to exit.");
      System.in.read();
      ms0.stopThread = true;
    }
    ```

12. App.java ファイルを保存して閉じます。

13. Maven を使用して **simulated-device** アプリケーションをビルドするには、コマンド プロンプトで simulated-device フォルダーに移動し、次のコマンドを実行します。

    ```
    mvn clean package -DskipTests
    ```

> [AZURE.NOTE] わかりやすくするために、このチュートリアルでは再試行ポリシーは実装しません。運用環境のコードでは、[一時的な障害処理][lnk-transient-faults]に関する MSDN の記事で推奨されているように、再試行ポリシー (指数関数的バックオフなど) を実装することをお勧めします。

## アプリケーションの実行

これで、アプリケーションを実行する準備が整いました。

1. コマンド プロンプトを使って read-d2c フォルダーで次のコマンドを実行し、IoT Hub の最初のパーティションの監視を開始します。

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"  -Dexec.args="0"
    ```

    ![][7]

1. コマンド プロンプトを使って read-d2c フォルダーで次のコマンドを実行し、IoT Hub の 2 番目のパーティションの監視を開始します。

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"  -Dexec.args="1"
    ```

    ![][7]

2. simulated-device フォルダーで、コマンド プロンプトで次のコマンドを実行し、IoT Hub へのテレメトリ データの送信を開始します。

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App" 
    ```

    ![][8]

3. [Azure ポータル][lnk-portal]の **[使用状況]** タイルには、Hub に送信されたメッセージ数が表示されます。

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
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide.md#identityregistry
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/java/device/doc/devbox_setup.md
[lnk-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md
[lnk-upload-tutorial]: iot-hub-csharp-csharp-file-upload.md

[lnk-hub-sdks]: iot-hub-sdks-summary.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

<!---HONumber=AcomDC_0608_2016-->