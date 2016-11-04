## シミュレーション対象デバイス アプリの作成
このセクションでは、デバイスからクラウドへのメッセージを IoT ハブに送信するデバイスをシミュレートする Java コンソール アプリを作成します。

1. コマンド プロンプトで次のコマンドを実行し、「*デバイス ID の作成*」セクションで作成した「iot-java-get-started」フォルダーで「**simulated-device**」という名前の Maven プロジェクトを新規作成します。これは 1 つの長いコマンドです。
   
    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```
2. コマンド プロンプトで、新しい「simulated-device」フォルダーに移動します。
3. テキスト エディターを使用し、simulated-device フォルダーで pom.xml ファイルを開き、次の依存関係を **dependencies** ノードに追加します。これにより、アプリケーションの iothub-java-client パッケージを使用し、IoT Hub と通信し、Java オブジェクトを JSON にシリアル化できます。
   
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
7. 次のクラスレベルの変数を **App** クラスに追加し、**{youriothubname}** を IoT Hub 名に、**{yourdeviceid}** と **{yourdevicekey}** を「*デバイス ID の作成*」セクションで生成されたデバイス値に置換します。
   
    ```
    private static String connString = "HostName={youriothubname}.azure-devices.net;DeviceId={yourdeviceid};SharedAccessKey={yourdevicekey}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.AMQPS;
    private static boolean stopThread = false;
    ```
   
    このサンプル アプリケーションでは、**DeviceClient** オブジェクトをインスタンス化するとき、**protocol** 変数が使用されます。HTTPS プロトコルと AMQPS プロトコルのいずれかを利用して IoT Hub と通信できます。
8. 次の入れ子になった **TelemetryDataPoint** クラスを **App** クラス内に追加し、デバイスで IoT Hub に送信する製品利用統計情報データを指定します。
   
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
9. 次の入れ子になった **EventCallback** クラスを **App** クラス内に指定し、シミュレーションされたデバイスからのメッセージを処理するときに IoT Hub が返す肯定応答ステータスを表示します。このメソッドにより、メッセージが処理されたときのアプリケーションのメイン スレッドも通知されます。
   
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
10. 次の入れ子になった **MessageSender** クラスを **App** クラス内に追加します。このクラスの **run** メソッドは、IoT ハブに送信するサンプル製品利用統計情報データを生成し、肯定応答を待ってから次のメッセージを送信します。
    
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
11. 「デバイスからクラウドへの」メッセージを IoT Hub に送信するスレッドを作成する次のコードで **main** メソッドを置換します。
    
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
13. Maven を利用して **simulated-device** アプリケーションを構築するには、simulated-device フォルダーで、コマンド プロンプトで次のコマンドを実行します。
    
    ```
    mvn clean package -DskipTests
    ```

> [!NOTE]
> わかりやすくするために、このチュートリアルでは再試行ポリシーは実装しません。運用環境のコードでは、[一時的な障害処理][lnk-transient-faults]に関する MSDN の記事で推奨されているように、再試行ポリシー (指数関数的バックオフなど) を実装することをお勧めします。
> 
> 

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

<!---HONumber=AcomDC_0323_2016-->