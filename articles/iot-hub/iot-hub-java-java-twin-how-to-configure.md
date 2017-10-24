---
title: "Azure IoT Hub デバイス ツインのプロパティの使用 (Java) | Microsoft Docs"
description: "Azure IoT Hub デバイス ツインを使用してデバイスを構成する方法。 Java 用 Azure IoT device SDK を使用して、シミュレートされたデバイス アプリを実装し、Java 用 Azure IoT service SDK を使用して、デバイス ツインを使用するデバイス構成を変更するサービス アプリを実装します。"
services: iot-hub
documentationcenter: java
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-hub
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2017
ms.author: dobett
ms.openlocfilehash: 3881e3791c97baf2922722f01f9e6e6ea55ed2e2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="use-desired-properties-to-configure-devices"></a>必要なプロパティを使用してデバイスを構成する

[!INCLUDE [iot-hub-selector-twin-how-to-configure](../../includes/iot-hub-selector-twin-how-to-configure.md)]

このチュートリアルの最後には、次の 2 つの Java コンソール アプリが完成します。

* **simulated-device**: 必要な構成の更新を待機し、シミュレートされた構成の更新プロセスの状態を報告する、シミュレートされたデバイス アプリです。
* **set-configuration**: デバイス上に必要な構成を設定し、構成更新プロセスに対してクエリを実行する、バックエンド アプリです。

> [!NOTE]
> デバイス アプリケーションとバックエンド アプリの両方の作成に利用できる Azure IoT SDK に関する情報は、「[Azure IoT SDKs (Azure IoT SDK)][lnk-hub-sdks]」の記事で取り上げています。
> 
> 

このチュートリアルを完了するには、以下が必要です。

* 最新の [Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) 
* [Maven 3](https://maven.apache.org/install.html) 
* アクティブな Azure アカウント。 (アカウントがない場合は、[無料アカウント][lnk-free-trial]を数分で作成できます)。

[デバイス ツインの概要][lnk-twin-tutorial]に関するチュートリアルを行った場合は、既に IoT Hub と、**myDeviceId** というデバイス ID があります。 その場合は、「[シミュレートされたデバイス アプリを作成する]」[lnk-how-to-configure-createapp]セクションをスキップできます。

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

<a id="#create-the-simulated-device-app"></a>
## <a name="create-the-simulated-device-app"></a>シミュレートされたデバイスのアプリを作成する
このセクションでは、**myDeviceId** としてハブに接続し、必要な構成の更新を待機して、シミュレートされた構成の更新プロセスの情報を報告する、Java コンソール アプリを作成します。

1. dt-get-started という名前の空のフォルダーを作成します。

1. コマンド プロンプトで次のコマンドを実行し、dt-get-started フォルダーに **simulated-device** という名前の Maven プロジェクトを作成します。 これは、1 つの長いコマンドであることに注意してください。

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. コマンド プロンプトで、simulated-device フォルダーに移動します。

1. テキスト エディターを使用して、simulated-device フォルダー内の pom.xml ファイルを開き、次の依存関係を **dependencies** ノードに追加します。 この依存関係により、アプリの iot-service-client パッケージを使用して IoT Hub と通信できるようになります。

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.3.30</version>
    </dependency>
    ```

    > [!NOTE]
    > [Maven 検索][lnk-maven-device-search] を使用して、**iot-device-client** の最新バージョンを確認できます。

1. **dependencies** ノードの後に、次の **build** ノードを追加します。 この構成では、Java 1.8 を使用してアプリをビルドするように Maven に指示しています。

    ```xml
    <build>
      <plugins>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.3</version>
          <configuration>
            <source>1.8</source>
            <target>1.8</target>
          </configuration>
        </plugin>
      </plugins>
    </build>
    ```

1. pom.xml ファイルを保存して閉じます。

1. テキスト エディターを使用し、simulated-device\src\main\java\com\mycompany\app\App.java ソース ファイルを開きます。

1. ファイルに次の **import** ステートメントを追加します。

    ```java
    import com.microsoft.azure.sdk.iot.device.*;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.*;

    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.Scanner;
    ```

1. 次のクラスレベル変数を **App** クラスに追加します。 **{yourdeviceconnectionstring}** を「*デバイス ID の作成*」セクションで書き留めたデバイス接続文字列に置き換えます。

    ```java
    private static String deviceId = "myDeviceId";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String connString = "{yourdeviceconnectionstring}";
    private static DeviceClient client;
    private static TelemetryConfig telemetryConfig;
    ```

1. (デバッグ用の) デバイス ツイン状態イベントのコールバック ハンドラーを実装するために、次の入れ子になったクラスを **App** クラスに追加します。

    ```java
    protected static class DeviceTwinStatusCallBack implements IotHubEventCallback 
    {
        public void execute(IotHubStatusCode status, Object context) 
        {
            //System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
    }
    ```

1. 次の入れ子になったクラスを **App** クラスに追加します。

    ```java
    private static class TelemetryConfig extends Device 
    {
        private String configId = "0000";
        private String sendFrequency = "45m";

        private Boolean initialRun = true;
        
        private Property telemetryConfig = new Property("telemetryConfig", "{configId=" + configId + ", sendFrequency=" + sendFrequency + "}");

        public void InitTelemetry() throws IOException 
        {
            System.out.println("Report initial telemetry config:");
            System.out.println(this.telemetryConfig);

            this.setReportedProp(this.telemetryConfig);

            client.sendReportedProperties(this.getReportedProp());
        }

        private void UpdateReportedConfiguration() 
        {
            try {
                System.out.println("Initiating config change");
                    
                this.setReportedProp(this.telemetryConfig);
                client.sendReportedProperties(this.getReportedProp());
                    
                System.out.println("Simulating device reset");
                
                Thread.sleep(10000);
                
                System.out.println("Completing config change");
                System.out.println("Config change complete");
            } catch (Exception e) {
                System.out.println("Exception \n" + " Cause: " + e.getCause() + " \n" + e.getMessage());
            }
        }

        @Override
        public void PropertyCall(String propertyKey, Object propertyValue, Object context) 
        {
            if (propertyKey.equals("telemetryConfig")) {
                if (!(initialRun)) {
                    System.out.println("Desired property change:");
                    System.out.println(propertyKey + ":" + propertyValue);

                    telemetryConfig.setValue(propertyValue);

                    UpdateReportedConfiguration();
                } else {
                    initialRun = false;
                }
            } 
        }
    }
    ```

    > [!NOTE]
    > TelemetryConfig クラスは、[Device クラス][lnk-java-device-class]を拡張して、必要なプロパティのコールバックにアクセスします。

1. **main** メソッドのシグネチャを変更し、次の例外をスローします。

    ```java
    public static void main(String[] args) throws IOException, URISyntaxException
    ```

1. **main** メソッドに次のコードを追加して、**DeviceClient** と **TelemetryConfig** をインスタンス化します。

    ```java
    client = new DeviceClient(connString, protocol);

    telemetryConfig = new TelemetryConfig();
    ```

1. **main** メソッドに次のコードを追加して、デバイス ツイン サービスを開始します。

    ```java
    try 
    {
        System.out.println("Connecting to hub");
        client.open();
        client.startDeviceTwin(new DeviceTwinStatusCallBack(), null, telemetryConfig, null);

        telemetryConfig.InitTelemetry();

        System.out.println("Wait for desired telemetry...");
        client.subscribeToDesiredProperties(telemetryConfig.getDesiredProp());
    }
    catch (Exception e) 
    {
        System.out.println("On exception, shutting down \n" + " Cause: " + e.getCause() + " \n" + e.getMessage());
        telemetryConfig.clean();
        client.close();
        System.out.println("Shutting down...");
    }
    ```

1. **main** メソッドに次のコードを追加して、必要に応じてデバイス シミュレーターをシャット ダウンします。

    ```java
    System.out.println("Press enter to exit...");
    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();

    telemetryConfig.clean();
    client.close();
    ```

1. simulated-device\src\main\java\com\mycompany\app\App.java を保存して閉じます。

1. **simulated-device** バックエンド アプリをビルドし、エラーを修正します。 コマンド プロンプトで simulated-device フォルダーに移動し、次のコマンドを実行します。

    `mvn clean package -DskipTests`

   > [!NOTE]
   > このチュートリアルでは、同時実行の構成の更新動作はシミュレートしません。 一部の構成更新プロセスでは、更新の実行中に対象の構成に変更を加えることができますが、場合によってはキューに入れる必要があったり、エラー条件で拒否されたりすることがあります。 お使いの構成プロセスに必要な動作を考慮し、構成の変更を開始する前に適切なロジックを追加するようにしてください。
   > 
   > 

## <a name="create-the-service-app"></a>サービス アプリを作成する
このセクションでは、新しいテレメトリの構成オブジェクトを使用して、**myDeviceId** に関連付けられたデバイス ツインの "*必要なプロパティ*" を更新する、Java コンソール アプリを作成します。 その後このアプリでは、IoT Hub に格納されているデバイス ツインにクエリを実行し、デバイスの必要な構成と報告される構成の違いを示します。

1. コマンド プロンプトで次のコマンドを実行して、dt-get-started フォルダーに **set-configuration** という名前の Maven プロジェクトを作成します。 これは、1 つの長いコマンドであることに注意してください。

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=set-configuration -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. コマンド プロンプトで、set-configuration フォルダーに移動します。

1. テキスト エディターを使用して、trigger-reboot フォルダー内の pom.xml ファイルを開き、次の依存関係を **dependencies** ノードに追加します。 この依存関係により、アプリの iot-service-client パッケージを使用して IoT Hub と通信できるようになります。

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.5.22</version>
      <type>jar</type>
    </dependency>
    ```

    > [!NOTE]
    > [Maven 検索][lnk-maven-service-search] を使用して、**iot-service-client** の最新バージョンを確認できます。

1. **dependencies** ノードの後に、次の **build** ノードを追加します。 この構成では、Java 1.8 を使用してアプリをビルドするように Maven に指示しています。

    ```xml
    <build>
      <plugins>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.3</version>
          <configuration>
            <source>1.8</source>
            <target>1.8</target>
          </configuration>
        </plugin>
      </plugins>
    </build>
    ```

1. pom.xml ファイルを保存して閉じます。

1. テキスト エディターを使用し、set-configuration\src\main\java\com\mycompany\app\App.java ソース ファイルを開きます。

1. ファイルに次の **import** ステートメントを追加します。

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.*;
    import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;

    import java.io.IOException;
    import java.util.HashSet;
    import java.util.Set;
    ```

1. 次のクラスレベル変数を **App** クラスに追加します。 **{youriothubconnectionstring}** を、「*IoT Hub の作成*」セクションで書き留めた IoT Hub 接続文字列で置換します。

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";
    ```

1. シミュレートされたデバイスでデバイス ツインに対するクエリおよび更新を実行するために、次のコードを **main** メソッドに追加します。

    ```java
    DeviceTwin twinClient = DeviceTwin.createFromConnectionString(iotHubConnectionString);
    DeviceTwinDevice device = new DeviceTwinDevice(deviceId);

    String sendFrequency= "12h";

    try {
        twinClient.getTwin(device);

        // make sure a differing value exists before calling the updateTwin() method, else a null device exception will be thrown
        // this allows the program to be run multiple times for demonstration purposes
        String desiredProperties = device.desiredPropertiesToString();
        if (desiredProperties.contains("sendFrequency=" + sendFrequency))
        {
            sendFrequency = "8h";
        }
            
        Set<Pair> tags = new HashSet<Pair>();
        tags.add(new Pair("telemetryConfig", "{configId=0001, sendFrequency=" + sendFrequency + "}"));

        twinClient.getTwin(device);
        device.setDesiredProperties(tags);

        System.out.println("Config report for: " + deviceId);   
        System.out.println(device);

        twinClient.updateTwin(device);

        String reportedProperties = device.reportedPropertiesToString();
        Boolean waitFlag = true;

        while (waitFlag) {
            if (!reportedProperties.contains("sendFrequency=" + sendFrequency)) {
                Thread.sleep(10000);
            }
            else 
            {
                waitFlag = false;
            }

            twinClient.getTwin(device);
            reportedProperties = device.reportedPropertiesToString();
        }
            
        SqlQuery sqlQuery = SqlQuery.createSqlQuery("*", SqlQuery.FromType.DEVICES, "properties.reported.telemetryConfig='{configId=0001, sendFrequency=" + sendFrequency + "}'", null);

        Query twinQuery = twinClient.queryTwin(sqlQuery.getQuery(), 100);
        while (twinClient.hasNextDeviceTwin(twinQuery)) {
            DeviceTwinDevice d = twinClient.getNextDeviceTwin(twinQuery);
            System.out.println(d.getDeviceId() + " found with changed telemetryConfig");
        }

        System.out.println("Config report for: " + deviceId);
        twinClient.getTwin(device);
        System.out.println(device);

        } catch (IotHubException e) {
            System.out.println(e.getMessage());
        } catch (IOException e) {
            System.out.println(e.getMessage());
        } catch (InterruptedException e) {
            System.out.println(e.getMessage());
        }
    ```

1. set-desired-configuration\src\main\java\com\mycompany\app\App.java ファイルを保存して閉じます。

1. **set-configuration** バックエンド アプリをビルドし、エラーを修正します。 コマンド プロンプトで set-configuration フォルダーに移動し、次のコマンドを実行します。

    `mvn clean package -DskipTests`
   
    このコードでは、**myDeviceId** のデバイス ツインを取得してから、新しいテレメトリ構成オブジェクトを使って必要なプロパティを更新します。
    その後、IoT Hub に格納されたデバイス ツインのクエリを 10 秒ごとに実行して、報告された目的のテレメトリ構成を出力します。 すべてのデバイスに関する豊富なレポートを生成する方法については、[IoT Hub のクエリ言語][lnk-query]に関するページを参照してください。
   
   > [!IMPORTANT]
   > このアプリケーションでは、例示を目的として、デバイスが更新されるまで 10 秒ごとに IoT Hub にクエリを実行します。 変更を検出するためではなく、あらゆるデバイスのユーザー向けのレポートを生成するためにクエリを使用します。 ソリューションにデバイス イベントのリアルタイム通知が必要な場合は、[ツイン通知][lnk-twin-notifications]を使用します。
   > 

   > [!IMPORTANT]
   > デバイスのレポート操作とクエリの結果までには、最大 1 分間の遅延が生じます。 これは、非常に大きな規模でクエリのインフラストラクチャを動作させるためです。  1 つのデバイス ツインの一貫したビューを取得するには、**DeviceTwin** クラスで **getDeviceTwin** メソッドを使用します。
   > 
   > 

## <a name="run-the-apps"></a>アプリの実行

これで、アプリを実行する準備が整いました。

1. simulated-device フォルダーで、コマンド プロンプトから次のコマンドを実行し、IoT Hub からのデバイス ツイン呼び出しのリッスンを開始します。

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

1. set-configuration フォルダーでコマンド プロンプトから次のコマンドを実行して、シミュレートされたデバイス上のデバイス ツインのクエリと更新を IoT Hub から実行します。

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

1. シミュレートされたデバイスは、再起動 ダイレクト メソッドの次の呼び出しに応答します。

    ![デバイス ツインの呼び出しに応答する IoT Hub の Java 版のシミュレートされたデバイス アプリ][img-deviceconfigured]

## <a name="next-steps"></a>次のステップ
このチュートリアルでは、必要な構成を*必要なプロパティ*としてソリューション バックエンドから設定し、その変更を検出して、報告されるプロパティを通してその状態を報告するマルチステップ更新プロセスをシミュレートするデバイス アプリを記述しました。

詳細については、次のリソースをご覧ください。

* [IoT Hub の概要][lnk-iothub-getstarted]に関するチュートリアルでデバイスからテレメトリを送信する。
* 多数のデバイスで操作をスケジュールまたは実行するには、[ジョブのスケジュールとブロードキャスト][lnk-schedule-jobs]に関するチュートリアルを参照してください。
* [ダイレクト メソッドの使用][lnk-methods-tutorial]に関するチュートリアルで、デバイスを対話形式で制御する (ユーザー制御アプリからファンをオンにするなど)。

<!-- images -->
[img-deviceconfigured]: media/iot-hub-java-java-twin-how-to-configure/deviceconfigured.png


<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-java-device-class]: https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.sdk.iot.device._device_twin._device

[lnk-query]: iot-hub-devguide-query-language.md
[lnk-twin-notifications]: iot-hub-devguide-device-twins.md#back-end-operations
[lnk-twin-tutorial]: iot-hub-csharp-csharp-twin-getstarted.md
[lnk-schedule-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-iothub-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md
[lnk-how-to-configure-createapp]: iot-hub-csharp-csharp-twin-how-to-configure.md#create-the-simulated-device-app
