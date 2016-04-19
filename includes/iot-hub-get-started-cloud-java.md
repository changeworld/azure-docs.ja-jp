## デバイス ID の作成

このセクションでは、IoT ハブの ID レジストリに新しいデバイス ID を作成する Java コンソール アプリケーションを作成します。IoT Hub に接続するデバイスは、あらかじめデバイス ID レジストリに登録されている必要があります。詳細については、「[IoT Hub 開発者ガイド][lnk-devguide-identity]」の「**デバイス ID レジストリ**」を参照してください。このコンソール アプリケーションを実行すると、デバイスからクラウドへのメッセージを IoT Hub に送信するときにそのデバイスを識別する一意の ID とキーが生成されます。

1. 「iot-java-get-started」という名前の空のフォルダーを新規作成します。コマンド プロンプトで次のコマンドを実行し、「iot-java-get-started」フォルダーで「**create-device-identity**」という名前の Maven プロジェクトを新規作成します。これは 1 つの長いコマンドです。

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=create-device-identity -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. コマンド プロンプトで、新しい「create-device-identity」フォルダーに移動します。

3. テキスト エディターを使用し、create-device-identity フォルダーで pom.xml ファイルを開き、次の依存関係を **dependencies** ノードに追加します。それにより、アプリケーションで iothub-service-sdk パッケージを利用できるようになります。

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

7. 次のクラスレベル変数を **App** クラスに追加し、**{yourhubname}** と **{yourhubkey}** を先にメモした値に置換します。

    ```
    private static final String connectionString = "HostName={yourhubname}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={yourhubkey}";
    private static final String deviceId = "javadevice";
    
    ```
    
8. **main** メソッドのシグネチャを変更し、下の例外を追加します。

    ```
    public static void main( String[] args ) throws IOException, URISyntaxException, Exception
    ```
    
9. **main** メソッドの本文として次のコードを追加します。このコードにより「*javadevice*」という名前のデバイスが IoT Hub ID レジストリに作成されます (未作成の場合)。その後、デバイス ID とキーが表示されますが、これは後で必要になります。

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

11. Maven を利用して **create-device-identity** アプリケーションを構築するには、create-device-identity フォルダーで、コマンド プロンプトで次のコマンドを実行します。

    ```
    mvn clean package -DskipTests
    ```

12. Maven を利用して **create-device-identity** アプリケーションを実行するには、create-device-identity フォルダーで、コマンド プロンプトで次のコマンドを実行します。

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

13. **デバイス ID** と**デバイス キー**をメモします。後でデバイスとして IoT Hub に接続するアプリケーションを作成するときに必要になります。

> [AZURE.NOTE] IoT Hub の ID レジストリには、ハブに対するアクセスの安全性を確保するためのデバイス ID だけが格納されます。セキュリティ資格情報として使用するキーとデバイス ID、そして個々のデバイスについてアクセスを無効にすることのできる有効/無効フラグが格納されます。その他デバイス固有のメタデータをアプリケーションで保存する必要がある場合は、アプリケーション固有のストアを使用する必要があります。詳細については、「[IoT Hub 開発者ガイド][lnk-devguide-identity]」を参照してください。

## デバイスからクラウドへのメッセージの受信

このセクションでは、IoT Hub からのデバイスからクラウドへのメッセージを読み込む Java コンソール アプリケーションを作成します。Iot Hub は、デバイスからクラウドへのメッセージを読み取るための、[Event Hubs][lnk-event-hubs-overview] と互換性のあるエンドポイントを公開します。わかりやすくするために、このチュートリアルで作成するリーダーは基本的なものであり、高スループットのデプロイメントには適していません。「[デバイスからクラウドへのメッセージの処理][lnk-processd2c-tutorial]」チュートリアルでは、デバイスからクラウドへの大規模なメッセージを処理する方法を紹介しています。「[Event Hubs の使用][lnk-eventhubs-tutorial]」チュートリアルでは、Event Hubs からのメッセージを処理する方法について詳しく説明しています。これは IoT Hub のイベント ハブと互換性のあるエンドポイントに適用されます。

> [AZURE.NOTE] 常に、Event Hubs と互換性のあるエンドポイントは、デバイスからクラウドへのメッセージを読み取るために AMQPS プロトコルを使用します。

1. コマンド プロンプトで次のコマンドを実行して、「*デバイス ID の作成*」セクションで作成した iot-java-get-started フォルダーで **read-d2c-messages** という名前の Maven プロジェクトを新規作成します。これは 1 つの長いコマンドです。

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=read-d2c-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. コマンド プロンプトで、新しい「read-d2c-messages」フォルダーに移動します。

3. テキスト エディターを使用して、read-d2c-messages フォルダーで pom.xml ファイルを開き、次の依存関係を **dependencies** ノードに追加します。これで、アプリケーションの eventhubs-client パッケージを利用し、Event Hubs と互換性のあるエンドポイントから読み込めるようにできます。

    ```
    <dependency>
      <groupId>com.microsoft.eventhubs.client</groupId>
      <artifactId>eventhubs-client</artifactId>
      <version>1.0</version>
    </dependency>
    ```

4. pom.xml ファイルを保存して閉じます。

5. テキスト エディターを使用し、read-d2c-messages\\src\\main\\java\\com\\mycompany\\app\\App.java ファイルを開きます。

6. ファイルに次の **import** ステートメントを追加します。

    ```
    import java.io.IOException;
    import com.microsoft.eventhubs.client.Constants;
    import com.microsoft.eventhubs.client.EventHubClient;
    import com.microsoft.eventhubs.client.EventHubEnqueueTimeFilter;
    import com.microsoft.eventhubs.client.EventHubException;
    import com.microsoft.eventhubs.client.EventHubMessage;
    import com.microsoft.eventhubs.client.EventHubReceiver;
    import com.microsoft.eventhubs.client.ConnectionStringBuilder;
    ```

7. 次のクラスレベル変数を **App** クラスに追加します。

    ```
    private static EventHubClient client;
    private static long now = System.currentTimeMillis();
    ```

8. 次の入れ子になったクラスを **App** クラス内に追加します。このアプリケーションでは、**MessageReceiver** を実行し、Event Hub の 2 つのパーティションからメッセージを読み込む 2 つのスレッドが作成されます。

    ```
    private static class MessageReceiver implements Runnable
    {
        public volatile boolean stopThread = false;
        private String partitionId;
    }
    ```

9. **MessageReceiver** クラスに次のコンストラクターを追加します。

    ```
    public MessageReceiver(String partitionId) {
        this.partitionId = partitionId;
    }
    ```

10. **MessageReceiver** クラスに次の **run** メソッドを追加します。このメソッドでは、Event Hub パーティションから読み込む **EventHubReceiver** インスタンスが作成されます。これは **stopThread** が true になるまでループを続け、メッセージの詳細を出力します。

    ```
    public void run() {
      try {
        EventHubReceiver receiver = client.getConsumerGroup(null).createReceiver(partitionId, new EventHubEnqueueTimeFilter(now), Constants.DefaultAmqpCredits);
        System.out.println("** Created receiver on partition " + partitionId);
        while (!stopThread) {
          EventHubMessage message = EventHubMessage.parseAmqpMessage(receiver.receive(5000));
          if(message != null) {
            System.out.println("Received: (" + message.getOffset() + " | "
                + message.getSequence() + " | " + message.getEnqueuedTimestamp()
                + ") => " + message.getDataAsString());
          }
        }
        receiver.close();
      }
      catch(EventHubException e) {
        System.out.println("Exception: " + e.getMessage());
      }
    }
    ```

    > [AZURE.NOTE] 受信側が実行開始後、IoT Hub に送信されたメッセージのみを読み込むように、このメソッドは受信側の構築時にフィルターを称します。現在の一連のメッセージを確認できるので、テスト環境ではこれは便利ですが、運用環境の場合、コードですべてのメッセージが処理されることを確認する必要があります。詳細については、「[IoT Hub のデバイスからクラウドへのメッセージを処理する方法][lnk-processd2c-tutorial]」チュートリアルを参照してください。

11. **main** メソッドのシグネチャを変更し、下の例外を追加します。

    ```
    public static void main( String[] args ) throws IOException
    ```

12. **App** クラスの **main** メソッドに次のコードを追加します。このコードでは、IoT Hub でイベント ハブと互換性のあるエンドポイントに接続する **EventHubClient** インスタンスが作成されます。それから、2 つのパーティションから読み取る 2 つのスレッドが作成されます。**{youriothubkey}**、**{youreventhubcompatiblenamespace}**、**{youreventhubcompatiblename}** を先にメモした値に置き換えます。**{youreventhubcompatiblenamespace}** プレースホルダーの値は、**Event Hub-compatible endpoint** から取得され、**xxxxnamespace** の形式になります (つまり、ポータルのイベント ハブと互換性のあるエンドポイントの ****sb://** プレフィックスと **.servicebus.windows.net** サフィックスを削除します)。

    ```
    String policyName = "iothubowner";
    String policyKey = "{youriothubkey}";
    String namespace = "{youreventhubcompatiblenamespace}";
    String name = "{youreventhubcompatiblename}";
    try {
      ConnectionStringBuilder csb = new ConnectionStringBuilder(policyName, policyKey, namespace);
      client = EventHubClient.create(csb.getConnectionString(), name);
    }
    catch(EventHubException e) {
        System.out.println("Exception: " + e.getMessage());
    }
    
    MessageReceiver mr0 = new MessageReceiver("0");
    MessageReceiver mr1 = new MessageReceiver("1");
    Thread t0 = new Thread(mr0);
    Thread t1 = new Thread(mr1);
    t0.start(); t1.start();

    System.out.println("Press ENTER to exit.");
    System.in.read();
    mr0.stopThread = true;
    mr1.stopThread = true;
    client.close();
    ```

    > [AZURE.NOTE] このコードでは、F1 (free) レベルで IoT hub を作成したと想定しています。無料の IoT Hub は、2 つのパーティションがあります。名前は「0」と「1」です。他の価格レベルのいずれかを使用して IoT Hub を作成した場合、コードを調整し、パーティションごとに **MessageReceiver** を作成する必要があります。

13. App.java ファイルを保存して閉じます。

14. Maven を使用して **read-d2c-messages** アプリケーションを構築するには、read-d2c-messages フォルダーで、コマンド プロンプトで次のコマンドを実行します。

    ```
    mvn clean package -DskipTests
    ```



<!-- Links -->

[lnk-eventhubs-tutorial]: ../articles/event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: ../articles/iot-hub/iot-hub-devguide.md#identityregistry
[lnk-event-hubs-overview]: ../articles/event-hubs/event-hubs-overview.md
[lnk-processd2c-tutorial]: ../articles/iot-hub/iot-hub-csharp-csharp-process-d2c.md

<!---HONumber=AcomDC_0413_2016-->