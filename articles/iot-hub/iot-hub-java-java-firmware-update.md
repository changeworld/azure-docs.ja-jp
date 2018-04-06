---
title: Azure IoT Hub を使用したデバイス ファームウェアの更新 (Java/Java) | Microsoft Docs
description: Azure IoT Hub でデバイス管理を使用してデバイス ファームウェアの更新を開始する方法。 Azure IoT device SDK for Java を使用して、シミュレート対象デバイス アプリを実装し、ファームウェア更新をトリガーするサービス アプリを実装します。
services: iot-hub
documentationcenter: java
author: msebolt
manager: timlt
editor: ''
ms.assetid: 70b84258-bc9f-43b1-b7cf-de1bb715f2cf
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/11/2017
ms.author: v-masebo
ms.openlocfilehash: 19d9cc6722d65d8bcd76d73fda0f24a598479647
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2018
---
# <a name="use-device-management-to-initiate-a-device-firmware-update-javajava"></a>デバイス管理を使用してデバイス ファームウェアの更新を開始する (Java/Java)
[!INCLUDE [iot-hub-selector-firmware-update](../../includes/iot-hub-selector-firmware-update.md)]

[デバイス管理の開始][lnk-dm-getstarted]に関するチュートリアルでは、[デバイス ツイン][lnk-devtwin]と[ダイレクト メソッド][lnk-c2dmethod] プリミティブを使用してリモートでデバイスを再起動する方法を説明しました。 このチュートリアルでは、同じ IoT Hub プリミティブを使用して、エンド ツー エンドでシミュレートされたファームウェア更新を実行する方法を示します。  このパターンは、[Raspberry Pi デバイスの実装サンプル][lnk-rpi-implementation]のファームウェア更新の実装で使用されます。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

このチュートリアルでは、次の操作方法について説明します。

* シミュレート対象デバイス アプリで **firmwareUpdate** ダイレクト メソッドを IoT ハブ経由で呼び出す Java コンソール アプリを作成します。
* デバイスをシミュレートし、**firmwareUpdate** ダイレクト メソッドを実装する Java コンソール アプリを作成します。 このメソッドは、ファームウェア イメージをダウンロードするまで待機し、ファームウェア イメージをダウンロードし、最後にファームウェア イメージを適用する、多段階のプロセスを開始します。 更新の各段階中、デバイスは、報告されるプロパティを使用して進捗状況を報告します。

このチュートリアルの最後には、次の 2 つの Java コンソール アプリが完成します。

**firmware-update**。シミュレートされたデバイス上のダイレクト メソッドを呼び出し、応答を表示し、報告されたプロパティ更新を定期的に表示します。

**simulated-device**。以前に作成したデバイス ID を使用して IoT ハブに接続し、firmwareUpdate ダイレクト メソッド呼び出しを受け取り、ファームウェア更新シミュレーションを介して実行します。

このチュートリアルを完了するには、以下が必要です。

* 最新の [Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) 
* [Maven 3](https://maven.apache.org/install.html) 
* アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料 Mobile Apps を入手できます。 (アカウントがない場合は、[無料アカウント][lnk-free-trial]を数分で作成できます)。

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>ダイレクト メソッドを使用して、デバイス上でリモート ファームウェア更新を開始する
このセクションでは、デバイスでリモート ファームウェア更新を開始する Java コンソール アプリを作成します。 アプリケーションは、ダイレクト メソッドを使用して更新を開始し、デバイス ツイン クエリを使用してアクティブなファームウェア更新の状態を定期的に取得します。

1. fw-get-started という名前の空のフォルダーを作成します。

1. コマンド プロンプトで次のコマンドを使用して、fw-get-started フォルダー内に **firmware-update** という名前の Maven プロジェクトを作成します。 これは、1 つの長いコマンドであることに注意してください。

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=firmware-update -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. コマンド プロンプトで、firmware-update フォルダーに移動します。

1. テキスト エディターを使用して、firmware-update フォルダーにある pom.xml ファイルを開き、次の依存関係を **dependencies** ノードに追加します。 この依存関係により、アプリの iot-service-client パッケージを使用して IoT Hub と通信できるようになります。

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.5.22</version>
      <type>jar</type>
    </dependency>
    ```

    > [!NOTE]
    > [Maven 検索](http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22)を使用して、**iot-service-client** の最新バージョンを確認できます。

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

1. テキスト エディターを使用して、firmware-update\src\main\java\com\mycompany\app\App.java ソース ファイルを開きます。

1. ファイルに次の **import** ステートメントを追加します。

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceMethod;
    import com.microsoft.azure.sdk.iot.service.devicetwin.MethodResult;
    import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceTwin;
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceTwinDevice;

    import java.io.IOException;
    import java.util.concurrent.TimeUnit;
    ```

1. 次のクラスレベル変数を **App** クラスに追加します。 **{youriothubconnectionstring}** を、「*IoT Hub の作成*」セクションで書き留めた IoT Hub 接続文字列で置換します。

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    private static final String methodName = "firmwareUpdate";
    private static final Long responseTimeout = TimeUnit.SECONDS.toSeconds(30);
    private static final Long connectTimeout = TimeUnit.SECONDS.toSeconds(5);
    ```

1. デバイス ツインから報告されたプロパティを読み取るメソッドを実装するには、**App** クラスに以下を追加します。

    ```java
    public static void ShowReportedProperties() 
    {
        try 
        {
          DeviceTwin deviceTwins = DeviceTwin.createFromConnectionString(iotHubConnectionString);
          DeviceTwinDevice twinDevice = new DeviceTwinDevice(deviceId);
          
          Boolean firmwareUpdated = false;
          Integer timeoutCycle = 0;
    
          while (!firmwareUpdated)
          {
            if (timeoutCycle > 5)
            {
              System.out.println("Operation timed out");
              break;
            }
    
            Thread.sleep(1000);
              
            deviceTwins.getTwin(twinDevice);
    
            String reportedProperties = twinDevice.reportedPropertiesToString();
              
            if(reportedProperties.contains("status=waiting"))
            {
              System.out.println("Waiting on device...");
            }
            else if(reportedProperties.contains("status=downloadComplete"))
            {
              System.out.println("Download complete, applying firmware...");
            }
            else if (reportedProperties.contains("status=applyComplete"))
            {
              System.out.println("Firmware applied");
              System.out.println("Get reported properties from device twin");
              System.out.println(twinDevice.reportedPropertiesToString());
              firmwareUpdated = true;
            }
            else
            {
              timeoutCycle++;
            }
          }
        } catch (Exception ex) {
            System.out.println("Exception reading reported properties: " + ex.getMessage());
        }
    }
    ```

1. **main** メソッドのシグネチャを変更し、次の例外をスローします。

    ```java
    public static void main( String[] args ) throws IOException
    ```

1. シミュレートされたデバイスで firmwareUpdate ダイレクト メソッドを呼び出すには、**main** メソッドに次のコードを追加します。

    ```java
    DeviceMethod methodClient = DeviceMethod.createFromConnectionString(iotHubConnectionString);

    try
    {
      String payload = "https://someurl";
      
      System.out.println("Invoked firmware update on device.");
      System.out.println("Sent URL: " + payload);
      
      MethodResult result = methodClient.invoke(deviceId, methodName, responseTimeout, connectTimeout, payload);

      if(result == null)
      {
        throw new IOException("Invoke direct method reboot returns null");
      }

      System.out.println("Status for device:   " + result.getStatus());
      System.out.println("Message from device: " + result.getPayload());
    }
    catch (IotHubException e)
    {
      System.out.println(e.getMessage());
    }
    ```

1. シミュレートされたデバイスから報告されたプロパティをポーリングするには、**main** メソッドに次のコードを追加します。

    ```java
    ShowReportedProperties();
    ```

1. アプリを停止できるようにするには、**main** メソッドに次のコードを追加します。

    ```java
    System.out.println("Press ENTER to exit.");
    System.in.read();
    System.out.println("Shutting down sample...");
    ```

1. firmware-update\src\main\java\com\mycompany\app\App.java ファイルを保存して閉じます。

1. **firmware-update** バックエンド アプリをビルドし、エラーを修正します。 コマンド プロンプトで firmware-update フォルダーに移動し、次のコマンドを実行します。

    `mvn clean package -DskipTests`

## <a name="simulate-a-device-to-handle-direct-method-calls"></a>ダイレクト メソッド呼び出しを処理するデバイスのシミュレート
このセクションでは、firmwareUpdate ダイレクト メソッドを受け取ることができる Java コンソール シミュレート対象デバイス アプリを作成します。 このアプリは、状態と通信する reportedProperties を使用して、ファームウェア更新をシミュレートする複数状態プロセスを実行します。

1. コマンド プロンプトで次のコマンドを使用して、fw-get-started フォルダーに **simulated-device** という名前の Maven プロジェクトを作成します。 これは、1 つの長いコマンドであることに注意してください。

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. コマンド プロンプトで、simulated-device フォルダーに移動します。

1. テキスト エディターを使用して、firmware-update フォルダーにある pom.xml ファイルを開き、次の依存関係を **dependencies** ノードに追加します。 この依存関係により、アプリの iot-service-client パッケージを使用して IoT Hub と通信できるようになります。

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.3.32</version>
      <type>jar</type>
    </dependency>
    ```

    > [!NOTE]
    > [Maven 検索](http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22)を使用して、**iot-device-client** の最新バージョンを確認できます。

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
    import java.time.LocalDateTime;
    import java.util.Scanner;
    import java.util.Set;
    import java.util.HashSet;
    import java.util.HashMap;
    ```

1. 次のクラスレベル変数を **App** クラスに追加します。 **{yourdeviceconnectionstring}** を「*デバイス ID の作成*」セクションで書き留めたデバイス接続文字列で置き換えます。

    ```java
    private static final int METHOD_SUCCESS = 200;
    private static final int METHOD_NOT_DEFINED = 404;

    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String connString = "{yourdeviceconnectionstring";
    private static DeviceClient client;

    private static String downloadURL = "unknown";
    ```

1. ダイレクト メソッド機能を実装するには、以下の入れ子になったクラスを **App** クラスに追加することによってコールバックを指定します。

    ```java
    protected static class DirectMethodStatusCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context)
      {
        System.out.println("IoT Hub responded to device method operation with status " + status.name());
      }
    }
    protected static class DirectMethodCallback implements com.microsoft.azure.sdk.iot.device.DeviceTwin.DeviceMethodCallback
    {
      @Override
      public DeviceMethodData call(String methodName, Object methodData, Object context)
      {
        DeviceMethodData deviceMethodData;
        switch (methodName)
        {
          case "firmwareUpdate" :
          {
            System.out.println("Response to method '" + methodName + "' sent successfully");
    
            downloadURL = new String((byte[])methodData);
    
            int status = METHOD_SUCCESS;
            System.out.println("Starting firmware update");
            deviceMethodData = new DeviceMethodData(status, "Started firmware update");
            FirmwareUpdateThread firmwareUpdateThread = new FirmwareUpdateThread();
            Thread t = new Thread(firmwareUpdateThread);
            t.start();
            break;
          }
          default:
          {
            int status = METHOD_NOT_DEFINED;
            deviceMethodData = new DeviceMethodData(status, "Not defined direct method " + methodName);
          }
        }
        return deviceMethodData;
      }
    }
    ```

1. デバイス ツイン機能を実装するには、以下の入れ子になったクラスを **App** クラスに追加することによってコールバックを指定します。

    ```java
    protected static class DeviceTwinStatusCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context)
      {
        System.out.println("IoT Hub responded to device twin operation with status " + status.name());
      }
    }
    protected static class PropertyCallback implements PropertyCallBack<String, String>
    {
      public void PropertyCall(String propertyKey, String propertyValue, Object context)
      {
        System.out.println("PropertyKey:     " + propertyKey);
        System.out.println("PropertyKvalue:  " + propertyKey);
      }
    }
    ```

1. ファームウェア更新を実装するには、以下の入れ子になったクラスを **App** クラスに追加します。

    ```java
    protected static class FirmwareUpdateThread implements Runnable {
      public void run() {
        try {      
          HashMap initialUpdate = new HashMap();
          Property sentProperty = new Property("firmwareUpdate", initialUpdate);
          Set<Property> sentPackage = new HashSet<Property>();
          
          System.out.println("Downloading from " + downloadURL);
    
          initialUpdate.put("status", "waiting");
          initialUpdate.put("fwPackageUri", downloadURL);
          initialUpdate.put("startedWaitingTime", LocalDateTime.now().toString());   
          sentPackage.add(sentProperty);
    
          client.sendReportedProperties(sentPackage);
    
          Thread.sleep(5000);
    
          System.out.println("Download complete");
    
          HashMap downloadUpdate = new HashMap();
          downloadUpdate.put("status","downloadComplete");
          downloadUpdate.put("downloadCompleteTime", LocalDateTime.now().toString());
          downloadUpdate.put("startedApplyingImage", LocalDateTime.now().toString());
          sentProperty.setValue(downloadUpdate);
    
          client.sendReportedProperties(sentPackage);
    
          Thread.sleep(5000);
    
          System.out.println("Apply complete");
    
          HashMap applyUpdate = new HashMap();
          applyUpdate.put("status","applyComplete");
          applyUpdate.put("lastFirmwareUpdate", LocalDateTime.now().toString());
          sentProperty.setValue(applyUpdate);
    
          client.sendReportedProperties(sentPackage);
    
          Thread.sleep(5000);
    
          HashMap resetUpdate = new HashMap();
          applyUpdate.put("status","reset");
          sentProperty.setValue(resetUpdate);
    
          client.sendReportedProperties(sentPackage);
        }
        catch (Exception ex) {
          System.out.println("Exception in reboot thread: " + ex.getMessage());
        }
      }
    }
    ```

1. **main** メソッドのシグネチャを変更し、次の例外をスローします。

    ```java
    public static void main(String[] args) throws IOException, URISyntaxException
    ```

1. ダイレクト メソッドとデバイス ツイン ルーチンを開始するには、以下のコードを **main** メソッドに追加します。

    ```java
    client = new DeviceClient(connString, protocol);

    try
    {
      client.open();
      client.subscribeToDeviceMethod(new DirectMethodCallback(), null, new DirectMethodStatusCallback(), null);
      client.startDeviceTwin(new DeviceTwinStatusCallback(), null, new PropertyCallback(), null);
      System.out.println("Client connected to IoT Hub.  Waiting for firmwareUpdate direct method.");
    }
    catch (Exception e)
    {
      System.out.println("On exception, shutting down \n" + " Cause: " + e.getCause() + " \n" +  e.getMessage());
      client.close();
      System.out.println("Shutting down...");
    }
    ```

1. アプリを停止できるようにするには、以下のコードを **main** メソッドの末尾に追加します。

    ```java
    System.out.println("Press any key to exit...");
    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();
    scanner.close();
    client.close();
    System.out.println("Shutting down...");
    ```

1. simulated-device\src\main\java\com\mycompany\app\App.java を保存して閉じます。

1. **simulated-device** アプリをビルドし、エラーを修正します。 コマンド プロンプトで simulated-device フォルダーに移動し、次のコマンドを実行します。

    `mvn clean package -DskipTests`

## <a name="run-the-apps"></a>アプリの実行
これで、アプリを実行する準備が整いました。

1. **simulated-device** フォルダーでコマンド プロンプトから、次のコマンドを実行して、ファームウェア更新ダイレクト メソッドのリッスンを開始します。
   
    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

1. **firmware-update** フォルダーでコマンド プロンプトから、次のコマンドを実行してファームウェア更新を呼び出し、シミュレートされたデバイス上のデバイス ツインに対して IoT ハブからクエリを実行します。

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

3. シミュレートされたデバイスがダイレクト メソッドに応答するのをコンソールで確認できます。

    ![ファームウェアが正常に更新されました][img-fwupdate]

## <a name="next-steps"></a>次の手順
このチュートリアルでは、ダイレクト メソッドを使用してデバイス上でリモートのファームウェア更新を開始し、報告されるプロパティを使用してファームウェア更新の進捗状況を確認しました。

IoT ソリューションの拡張と複数のデバイスでのメソッドの呼び出しをスケジュールする方法については、「[ジョブのスケジュールとブロードキャスト][lnk-tutorial-jobs]」チュートリアルを参照してください。

<!-- images -->
[img-fwupdate]: media/iot-hub-java-java-firmware-update/firmwareUpdated.png

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: iot-hub-java-java-device-management-getstarted.md
[lnk-tutorial-jobs]: iot-hub-java-java-schedule-jobs.md

[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-rpi-implementation]: https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt_dm/pi_device