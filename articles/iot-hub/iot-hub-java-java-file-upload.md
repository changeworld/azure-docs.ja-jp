---
title: Java を使用してデバイスから Azure IoT Hub にファイルをアップロードする | Microsoft Docs
description: Java 用 Azure IoT device SDK を使用してデバイスからクラウドにファイルをアップロードする方法。 アップロードしたファイルは Azure Storage Blob コンテナーに格納されます。
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 07/18/2021
ms.custom:
- amqp
- mqtt
- devx-track-java
ms.openlocfilehash: f375c8cf49d3bdc230e589f6a816eb9615468946
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2021
ms.locfileid: "129458948"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-java"></a>IoT Hub を使用してデバイスからクラウドにファイルをアップロードする (Java)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

このチュートリアルでは、Java を使った IoT Hub のファイル アップロードの機能を使用する方法を示します。 ファイルのアップロード プロセスの概要については、「[IoT Hub を使用したファイルのアップロード](iot-hub-devguide-file-upload.md)」を参照してください。

[デバイスから IoT ハブにテレメトリを送信する方法](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-java)のクイックスタートと [IoT Hub で cloud-to-device メッセージを送信する方法](iot-hub-java-java-c2d.md)のチュートリアルには、IoT Hub のデバイスからクラウドへのメッセージングと cloud-to-device メッセージの基本的な機能が示されています。 「[IoT Hub を使用してメッセージ ルーティングを構成する](tutorial-routing.md)」チュートリアルでは、Azure Blob Storage にデバイスからクラウドへのメッセージを確実に格納する方法を説明しています。 ただし、一部のシナリオでは、デバイスから送信されるデータを、IoT Hub が受け取る、クラウドからデバイスへの比較的小さなメッセージにマッピングすることは簡単ではありません。 次に例を示します。

* イメージを含む大きなファイル
* ビデオ
* 高頻度でサンプリングされる振動データ
* 何らかの形式の前処理済みデータ

これらのファイルは通常、[Azure Data Factory](../data-factory/introduction.md) や [Hadoop](../hdinsight/index.yml) スタックなどのツールを使用してクラウドでバッチ処理されます。 ただし、デバイスからファイルをアップロードする必要がある場合でも、IoT Hub のセキュリティと信頼性を利用できます。 このサンプルではその方法を示します。 また、GitHub の [https://github.com/Azure/azure-iot-sdk-java/tree/main/device/iot-device-samples/file-upload-sample/src/main/java/samples/com/microsoft/azure/sdk/iot](https://github.com/Azure/azure-iot-sdk-java/tree/main/device/iot-device-samples/file-upload-sample/src/main/java/samples/com/microsoft/azure/sdk/iot) には 2 つのサンプルがあります。

> [!NOTE]
> IoT Hub は、Azure IoT Device SDK を介して多数のデバイス プラットフォームと言語 (C、.NET、Javascript など) をサポートしています。 Azure IoT Hub にデバイスを接続するための詳しい手順については、[Azure IoT デベロッパー センター](https://azure.microsoft.com/develop/iot)を参照してください。

[!INCLUDE [iot-hub-include-x509-ca-signed-file-upload-support-note](../../includes/iot-hub-include-x509-ca-signed-file-upload-support-note.md)]

## <a name="prerequisites"></a>前提条件

* [Java SE Development Kit 8](/java/azure/jdk/)。 JDK 8 のダウンロードを利用するには、「**長期サポート**」の「**Java 8**」を選択します。

* [Maven 3](https://maven.apache.org/download.cgi)

* アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料 Mobile Apps を入手できます。 (アカウントがない場合は、[無料アカウント](https://azure.microsoft.com/pricing/free-trial/) を数分で作成できます)。

* ポート 8883 がファイアウォールで開放されていることを確認してください。 この記事のデバイス サンプルでは、ポート 8883 を介して通信する MQTT プロトコルを使用しています。 このポートは、企業や教育用のネットワーク環境によってはブロックされている場合があります。 この問題の詳細と対処方法については、「[IoT Hub への接続 (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)」を参照してください。

## <a name="create-an-iot-hub"></a>IoT Hub の作成

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>IoT ハブに新しいデバイスを登録する

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-include-associate-storage.md)]

## <a name="create-a-project-using-maven"></a>Maven を使ってプロジェクトを作成する

プロジェクトのディレクトリを作成し、そのディレクトリでシェルを起動します。 コマンド ラインで、以下を実行します

```cmd/sh
mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=my-app -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

これにより、*artifactId* と同じ名前のディレクトリと標準のプロジェクト構造が生成されます。

```
  my-app
  |-- pom.xml
   -- src
      -- main
         -- java
            -- com
               -- mycompany
                  -- app
                     --App.Java
```

テキスト エディターを使用して、pom.xml ファイルを以下と置き換えます。

```xml

<?xml version="1.0" encoding="UTF-8"?>

<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <groupId>com.mycompany.app</groupId>
  <artifactId>my-app</artifactId>
  <version>1.0-SNAPSHOT</version>

  <name>my-app</name>
  <!-- FIXME change it to the project's website -->
  <url>http://www.example.com</url>

  <properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.source>1.7</maven.compiler.source>
    <maven.compiler.target>1.7</maven.compiler.target>
  </properties>

  <dependencies>
      <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.30.1</version>
    </dependency>
    <dependency>
      <groupId>org.slf4j</groupId>
      <artifactId>slf4j-log4j12</artifactId>
      <version>1.7.29</version>
    </dependency>    
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.11</version>
      <scope>test</scope>
    </dependency>
  </dependencies>

  <build>
    <pluginManagement><!-- lock down plugins versions to avoid using Maven defaults (may be moved to parent pom) -->
      <plugins>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId>
            <version>3.3</version>
            <configuration>
              <source>1.7</source>
              <target>1.7</target>
            </configuration>
        </plugin>
        <plugin>
          <artifactId>maven-shade-plugin</artifactId>
          <version>2.4</version>
          <executions>
              <execution>
                  <phase>package</phase>
                  <goals>
                    <goal>shade</goal>
                  </goals>
                  <configuration>
                      <filters>
                          <filter>
                              <artifact>*:*</artifact>
                              <excludes>
                                  <exclude>META-INF/*.SF</exclude>
                                  <exclude>META-INF/*.RSA</exclude>
                              </excludes>
                          </filter>
                      </filters>
                      <shadedArtifactAttached>true</shadedArtifactAttached>
                      <shadedClassifierName>with-deps</shadedClassifierName>
                  </configuration>
              </execution>
          </executions>
        </plugin>
      </plugins>
    </pluginManagement>
  </build>
</project>

```

## <a name="upload-a-file-from-a-device-app"></a>デバイス アプリからのファイルのアップロード

アップロードするファイルを、プロジェクト ツリー内の `my-app` フォルダーにコピーします。 テキスト エディターを使用して、App.java を次のコードに置き換えます。 示されたデバイス接続文字列とファイル名を指定します。 デバイス接続文字列は、デバイスの登録時にコピーしたものです。

```java
package com.mycompany.app;

import com.azure.storage.blob.BlobClient;
import com.azure.storage.blob.BlobClientBuilder;
import com.microsoft.azure.sdk.iot.deps.serializer.FileUploadCompletionNotification;
import com.microsoft.azure.sdk.iot.deps.serializer.FileUploadSasUriRequest;
import com.microsoft.azure.sdk.iot.deps.serializer.FileUploadSasUriResponse;
import com.microsoft.azure.sdk.iot.device.DeviceClient;
import com.microsoft.azure.sdk.iot.device.IotHubClientProtocol;

import java.io.BufferedInputStream;
import java.io.File;
import java.io.FileInputStream;
import java.io.IOException;
import java.net.URISyntaxException;
import java.util.Scanner;

public class App 
{
    /**
     * Upload a single file to blobs using IoT Hub.
     *
     */
    public static void main(String[] args)throws IOException, URISyntaxException
    {
        String connString = "Your device connection string here";
        String fullFileName = "Path of the file to upload";

        System.out.println("Starting...");
        System.out.println("Beginning setup.");

        // File upload will always use HTTPS, DeviceClient will use this protocol only
        //   for the other services like Telemetry, Device Method and Device Twin.
        IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;

        System.out.println("Successfully read input parameters.");

        DeviceClient client = new DeviceClient(connString, protocol);

        System.out.println("Successfully created an IoT Hub client.");

        try
        {
            File file = new File(fullFileName);
            if (file.isDirectory())
            {
                throw new IllegalArgumentException(fullFileName + " is a directory, please provide a single file name, or use the FileUploadSample to upload directories.");
            }

            System.out.println("Retrieving SAS URI from IoT Hub...");
            FileUploadSasUriResponse sasUriResponse = client.getFileUploadSasUri(new FileUploadSasUriRequest(file.getName()));

            System.out.println("Successfully got SAS URI from IoT Hub");
            System.out.println("Correlation Id: " + sasUriResponse.getCorrelationId());
            System.out.println("Container name: " + sasUriResponse.getContainerName());
            System.out.println("Blob name: " + sasUriResponse.getBlobName());
            System.out.println("Blob Uri: " + sasUriResponse.getBlobUri());

            System.out.println("Using the Azure Storage SDK to upload file to Azure Storage...");

            try
            {
                BlobClient blobClient =
                    new BlobClientBuilder()
                        .endpoint(sasUriResponse.getBlobUri().toString())
                        .buildClient();

                blobClient.uploadFromFile(fullFileName);
            }
            catch (Exception e)
            {
                System.out.println("Exception encountered while uploading file to blob: " + e.getMessage());

                System.out.println("Failed to upload file to Azure Storage.");

                System.out.println("Notifying IoT Hub that the SAS URI can be freed and that the file upload failed.");

                // Note that this is done even when the file upload fails. IoT Hub has a fixed number of SAS URIs allowed active
                // at any given time. Once you are done with the file upload, you should free your SAS URI so that other
                // SAS URIs can be generated. If a SAS URI is not freed through this API, then it will free itself eventually
                // based on how long SAS URIs are configured to live on your IoT Hub.
                FileUploadCompletionNotification completionNotification = new FileUploadCompletionNotification(sasUriResponse.getCorrelationId(), false);
                client.completeFileUpload(completionNotification);

                System.out.println("Notified IoT Hub that the SAS URI can be freed and that the file upload was a failure.");

                client.closeNow();
                return;
            }

            System.out.println("Successfully uploaded file to Azure Storage.");

            System.out.println("Notifying IoT Hub that the SAS URI can be freed and that the file upload was a success.");
            FileUploadCompletionNotification completionNotification = new FileUploadCompletionNotification(sasUriResponse.getCorrelationId(), true);
            client.completeFileUpload(completionNotification);
            System.out.println("Successfully notified IoT Hub that the SAS URI can be freed, and that the file upload was a success");
        }
        catch (Exception e)
        {
            System.out.println("On exception, shutting down \n" + " Cause: " + e.getCause() + " \nERROR: " +  e.getMessage());
            System.out.println("Shutting down...");
            client.closeNow();
        }

        System.out.println("Press any key to exit...");

        Scanner scanner = new Scanner(System.in);
        scanner.nextLine();
        System.out.println("Shutting down...");
        client.closeNow();
    }
}
```

## <a name="build-and-run-the-application"></a>アプリケーションのビルドと実行

`my-app` フォルダーのコマンド プロンプトで、次のコマンドを実行します。

```cmd/sh
mvn clean package -DskipTests
```

ビルドが完了したら、次のコマンドを実行してアプリケーションを実行します。

```cmd/sh
mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
```

ポータルを使用して、構成したストレージ コンテナーにアップロードされたファイルを表示できます。

![アップロードされたファイル](media/iot-hub-java-java-upload/uploaded-file.png)

## <a name="receive-a-file-upload-notification"></a>ファイル アップロードの通知の受信

このセクションでは、IoT Hub からファイル アップロードの通知メッセージを受信する Java コンソール アプリケーションを作成します。

1. プロジェクトのディレクトリを作成し、そのディレクトリでシェルを起動します。 コマンド ラインで、以下を実行します

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=my-app -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
    ```

2. コマンド プロンプトで、新しい `my-app` フォルダーに移動します。

3. テキスト エディターを使用して、`my-app` フォルダー内の `pom.xml` ファイルを以下と置き換えます。 サービス クライアントの依存関係を追加することにより、アプリケーションの **iothub-java-service-client** パッケージを使用して、IoT Hub サービスと通信できます。

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>

    <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
      <modelVersion>4.0.0</modelVersion>

      <groupId>com.mycompany.app</groupId>
      <artifactId>my-app</artifactId>
      <version>1.0-SNAPSHOT</version>

      <name>my-app</name>
      <!-- FIXME change it to the project's website -->
      <url>http://www.example.com</url>

      <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <maven.compiler.source>1.7</maven.compiler.source>
        <maven.compiler.target>1.7</maven.compiler.target>
      </properties>

      <dependencies>
          <dependency>
          <groupId>com.microsoft.azure.sdk.iot</groupId>
          <artifactId>iot-device-client</artifactId>
          <version>1.30.1</version>
        </dependency>
        <dependency>
          <groupId>com.microsoft.azure.sdk.iot</groupId>
          <artifactId>iot-service-client</artifactId>
          <version>1.7.23</version>
        </dependency>
        <dependency>
          <groupId>org.slf4j</groupId>
          <artifactId>slf4j-log4j12</artifactId>
          <version>1.7.29</version>
        </dependency>    
        <dependency>
          <groupId>junit</groupId>
          <artifactId>junit</artifactId>
          <version>4.11</version>
          <scope>test</scope>
        </dependency>
      </dependencies>

      <build>
        <pluginManagement><!-- lock down plugins versions to avoid using Maven defaults (may be moved to parent pom) -->
          <plugins>
            <plugin>
              <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.3</version>
                <configuration>
                  <source>1.7</source>
                  <target>1.7</target>
                </configuration>
            </plugin>
            <plugin>
              <artifactId>maven-shade-plugin</artifactId>
              <version>2.4</version>
              <executions>
                  <execution>
                      <phase>package</phase>
                      <goals>
                        <goal>shade</goal>
                      </goals>
                      <configuration>
                          <filters>
                              <filter>
                                  <artifact>*:*</artifact>
                                  <excludes>
                                      <exclude>META-INF/*.SF</exclude>
                                      <exclude>META-INF/*.RSA</exclude>
                                  </excludes>
                              </filter>
                          </filters>
                          <shadedArtifactAttached>true</shadedArtifactAttached>
                          <shadedClassifierName>with-deps</shadedClassifierName>
                      </configuration>
                  </execution>
              </executions>
            </plugin>
          </plugins>
        </pluginManagement>
      </build>
    </project>
    ```

    > [!NOTE]
    > [Maven 検索](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22)を使用して、**iot-service-client** の最新バージョンを確認できます。

4. `pom.xml` ファイルを保存して閉じます。

5. IoT Hub サービスの接続文字列を取得します。
    [!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

6. テキスト エディターを使用して、`my-app\src\main\java\com\mycompany\app\App.java` ファイルを開き、コードを以下に置き換えます。

    ```java
    package com.mycompany.app;

    import com.microsoft.azure.sdk.iot.service.*;
    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.concurrent.ExecutorService;
    import java.util.concurrent.Executors;


    public class App 
    {
        private static final String connectionString = "{Your service connection string here}";
        private static final IotHubServiceClientProtocol protocol = IotHubServiceClientProtocol.AMQPS;

        public static void main(String[] args) throws Exception
        {
            ServiceClient sc = ServiceClient.createFromConnectionString(connectionString, protocol);

            FileUploadNotificationReceiver receiver = sc.getFileUploadNotificationReceiver();
            receiver.open();
            FileUploadNotification fileUploadNotification = receiver.receive(2000);
    
            if (fileUploadNotification != null)
            {
                System.out.println("File Upload notification received");
                System.out.println("Device Id : " + fileUploadNotification.getDeviceId());
                System.out.println("Blob Uri: " + fileUploadNotification.getBlobUri());
                System.out.println("Blob Name: " + fileUploadNotification.getBlobName());
                System.out.println("Last Updated : " + fileUploadNotification.getLastUpdatedTimeDate());
                System.out.println("Blob Size (Bytes): " + fileUploadNotification.getBlobSizeInBytes());
                System.out.println("Enqueued Time: " + fileUploadNotification.getEnqueuedTimeUtcDate());
            }
            else
            {
                System.out.println("No file upload notification");
            }
    
            receiver.close();
        }
    
    }
    ```


7. `my-app\src\main\java\com\mycompany\app\App.java` ファイルを保存し、閉じます。

8. 次のコマンドを使用して、アプリをビルドし、エラーを確認します。
    ```cmd/sh
    mvn clean package -DskipTests
    ```
## <a name="run-the-application"></a>アプリケーションの実行

これで、アプリケーションを実行する準備が整いました。

`my-app` フォルダーのコマンド プロンプトで、次のコマンドを実行します。

```cmd/sh
mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
```
次のスクリーン ショットは、**read-file-upload-notification** アプリからの出力を示しています。

![read-file-upload-notification アプリからの出力](media/iot-hub-java-java-upload/read-file-upload-notification.png)

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、IoT Hub のファイル アップロード機能を使用して、デバイスからのファイルのアップロードを簡素化する方法を学習しました。 次の記事で IoT Hub の機能やシナリオをさらに詳しく調べることができます。

* [プログラムによる IoT Hub の作成](iot-hub-rm-template-powershell.md)

* [C SDK の概要](iot-hub-device-sdk-c-intro.md)

* [Azure IoT SDK](iot-hub-devguide-sdks.md)

IoT Hub の機能を詳しく調べるには、次のリンクを使用してください。

* [IoT Edge を使用したデバイスのシミュレーション](../iot-edge/quickstart-linux.md)