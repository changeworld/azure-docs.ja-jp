---
title: クイックスタート - Azure Web PubSub インスタンスのサービス SDK を使用してメッセージを発行する
description: サービス SDK の使用方法を示すクイックスタート
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: quickstart
ms.date: 11/01/2021
ms.openlocfilehash: 72805453fb9929c4f598cca8d930eac3b447f6c2
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132287905"
---
# <a name="quickstart-publish-messages-using-the-service-sdk-for-the-azure-web-pubsub-instance"></a>クイックスタート: Azure Web PubSub インスタンスのサービス SDK を使用してメッセージを発行する

このクイックスタートでは、サービス SDK を使用してクライアントにメッセージを発行する方法について説明します。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- このクイックスタートには、Azure CLI のバージョン 2.22.0 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

## <a name="create-a-resource-group"></a>リソース グループを作成する

[!INCLUDE [Create a resource group](includes/cli-rg-creation.md)]

## <a name="create-a-web-pubsub-instance"></a>Web PubSub インスタンスを作成する

[!INCLUDE [Create a Web PubSub instance](includes/cli-awps-creation.md)]

## <a name="get-the-connectionstring-for-future-use"></a>将来使用するために ConnectionString を取得する

[!INCLUDE [Get the connection string](includes/cli-awps-connstr.md)]

フェッチされた **ConnectionString** をコピーします。これは、このチュートリアルの後半でサービス SDK を使用するときに `<connection_string>` の値として使用されます。

## <a name="connect-to-the-instance"></a>インスタンスに接続する

[!INCLUDE [az webpubsub client](includes/cli-awps-client-connect.md)]

## <a name="publish-messages-using-service-sdk"></a>サービス SDK を使用してメッセージを発行する

次に、Azure Web PubSub SDK を使用して、接続されているクライアントにメッセージを発行します。

### <a name="prerequisites"></a>前提条件

# <a name="c"></a>[C#](#tab/csharp)

* [.NET Core 2.1 以降](https://dotnet.microsoft.com/download)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

* [Node.js 12.x 以降](https://nodejs.org)

# <a name="python"></a>[Python](#tab/python)
* [Python](https://www.python.org/)

# <a name="java"></a>[Java](#tab/java)
- [Java Development Kit (JDK)](/java/azure/jdk/) バージョン 8 以降。
- [Apache Maven](https://maven.apache.org/download.cgi)。

---

### <a name="set-up-the-project-to-publish-messages"></a>メッセージを発行するプロジェクトを設定する

# <a name="c"></a>[C#](#tab/csharp)

1. 新しいプロジェクト `publisher` を追加し、SDK パッケージ `package Azure.Messaging.WebPubSub` を追加します。

    ```bash
    mkdir publisher
    cd publisher
    dotnet new console
    dotnet add package Azure.Messaging.WebPubSub --version 1.0.0-beta.3
    ```

2. `Program.cs` ファイルを更新し、`WebPubSubServiceClient` クラスを使用してクライアントにメッセージを送信します。

    ```csharp
    using System;
    using System.Threading.Tasks;
    using Azure.Messaging.WebPubSub;

    namespace publisher
    {
        class Program
        {
            static async Task Main(string[] args)
            {
                if (args.Length != 3) {
                    Console.WriteLine("Usage: publisher <connectionString> <hub> <message>");
                    return;
                }
                var connectionString = args[0];
                var hub = args[1];
                var message = args[2];

                var service = new WebPubSubServiceClient(connectionString, hub);
                
                // Send messages to all the connected clients
                // You can also try SendToConnectionAsync to send messages to the specific connection
                await service.SendToAllAsync(message);
            }
        }
    }
    ```

    `service.SendToAllAsync()` の呼び出しでは、単純にハブ内の接続されているすべてのクライアントにメッセージが送信されます。

3. `<connection_string>` を、[前の手順](#get-the-connectionstring-for-future-use)でフェッチされた **ConnectionString** で置き換えて、次のコマンドを実行します。

    ```bash
    dotnet run "<connection_string>" "myHub1" "Hello World"
    ```

4. 前の CLI クライアントがメッセージを受信したことを確認できます。
   
    ```json
    {"type":"message","from":"server","dataType":"text","data":"Hello World"}
    ```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

1. まず、このプロジェクト用の新しいフォルダー `publisher` を作成し、必要な依存関係をインストールします。

    ```bash
    mkdir publisher
    cd publisher
    npm init -y
    npm install --save @azure/web-pubsub@1.0.0-alpha.20211102.4
    ```

2. 次に、Azure Web PubSub SDK を使用して、サービスにメッセージを発行しましょう。 下のコードを使用して `publish.js` ファイルを作成します。

    ```javascript
    const { WebPubSubServiceClient } = require('@azure/web-pubsub');

    if (process.argv.length !== 3) {
      console.log('Usage: node publish <message>');
      return 1;
    }
    const hub = "pubsub";
    let service = new WebPubSubServiceClient(process.env.WebPubSubConnectionString, hub);
    // by default it uses `application/json`, specify contentType as `text/plain` if you want plain-text
    service.sendToAll(process.argv[2], { contentType: "text/plain" });
    ```

    `sendToAll()` の呼び出しでは、単純にハブ内の接続されているすべてのクライアントにメッセージが送信されます。

3. `<connection_string>` を、[前の手順](#get-the-connectionstring-for-future-use)でフェッチされた **ConnectionString** で置き換えて、次のコマンドを実行します。

    ```bash
    export WebPubSubConnectionString="<connection-string>"
    node publish "Hello World"
    ```

4. 前の CLI クライアントがメッセージを受信したことを確認できます。
   
    ```json
    {"type":"message","from":"server","dataType":"text","data":"Hello World"}
    ```

# <a name="python"></a>[Python](#tab/python)

1. まず、このプロジェクト用の新しいフォルダー `publisher` を作成し、必要な依存関係をインストールします。
    ```bash
    mkdir publisher
    cd publisher
    # Create venv
    python -m venv env
    # Active venv
    source ./env/bin/activate

    pip install azure-messaging-webpubsubservice

    ```
2. 次に、Azure Web PubSub SDK を使用して、サービスにメッセージを発行しましょう。 下のコードを使用して `publish.py` ファイルを作成します。

    ```python
    import sys
    from azure.messaging.webpubsubservice import WebPubSubServiceClient
    
    if __name__ == '__main__':
    
        if len(sys.argv) != 4:
            print('Usage: python publish.py <connection-string> <hub-name> <message>')
            exit(1)
    
        connection_string = sys.argv[1]
        hub_name = sys.argv[2]
        message = sys.argv[3]
    
        service = WebPubSubServiceClient.from_connection_string(connection_string, hub=hub_name)
        res = service.send_to_all(message, content_type='text/plain')
        print(res)
    ```

    `service.send_to_all()` メソッドでは、ハブ内の全ての接続済みクライアントにメッセージが送信されます。

3. `<connection_string>` を、[前の手順](#get-the-connectionstring-for-future-use)でフェッチされた **ConnectionString** で置き換えて、次のコマンドを実行します。

    ```bash
    python publish.py "<connection_string>" "myHub1" "Hello World"
    ```

4. 前の CLI クライアントがメッセージを受信したことを確認できます。
   
    ```json
    {"type":"message","from":"server","dataType":"text","data":"Hello World"}
    ```

# <a name="java"></a>[Java](#tab/java)

1. まず、Maven を使用して新しいコンソール アプリ `webpubsub-quickstart-publisher` を作成し、*webpubsub-quickstart-publisher* フォルダーに移動します。
    ```console
    mvn archetype:generate --define interactiveMode=n --define groupId=com.webpubsub.quickstart --define artifactId=webpubsub-quickstart-publisher --define archetypeArtifactId=maven-archetype-quickstart --define archetypeVersion=1.4
    cd webpubsub-quickstart-publisher
    ```

2. Azure Web PubSub SDK の依存関係を `pom.xml` の `dependencies` ノードに追加します。

    ```xml
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-messaging-webpubsub</artifactId>
        <version>1.0.0-beta.2</version>
    </dependency>
    ```

3. 次に、Azure Web PubSub SDK を使用して、サービスにメッセージを発行しましょう。 */src/main/java/com/webpubsub/quickstart* ディレクトリに移動し、*App.java* ファイルをエディターで開いて、下のコードに置き換えます。

    ```java
    package com.webpubsub.quickstart;

    import com.azure.messaging.webpubsub.*;
    import com.azure.messaging.webpubsub.models.*;

    /**
    * Quickstart - Publish messages using Azure Web PubSub service SDK
    *
    */
    public class App 
    {
        public static void main( String[] args )
        {
            if (args.length != 3) {
                System.out.println("Expecting 3 arguments: <connection-string> <hub-name> <message>");
                return;
            }

            WebPubSubServiceClient service = new WebPubSubClientBuilder()
                .connectionString(args[0])
                .hub(args[1])
                .buildClient();
            service.sendToAll(args[2], WebPubSubContentType.TEXT_PLAIN);
        }
    }

    ```

    `service.sendToAll()` の呼び出しでは、単純にハブ内の接続されているすべてのクライアントにメッセージが送信されます。

4. *pom.xml* ファイルが格納されているディレクトリに移動し、次の `mvn` コマンドを使用してプロジェクトをコンパイルします。

    ```console
    mvn compile
    ```
5. 次に、パッケージをビルドします

    ```console
    mvn package
    ```
6. 次の `mvn` コマンドを実行してアプリを実行します。その際、`<connection_string>` を [前の手順](#get-the-connectionstring-for-future-use)でフェッチされた **ConnectionString** に置き換えます。

    ```console
    mvn exec:java -Dexec.mainClass="com.webpubsub.quickstart.App" -Dexec.cleanupDaemonThreads=false -Dexec.args="'<connection_string>' 'myHub1' 'Hello World'"
    ```

7. 前の CLI クライアントがメッセージを受信したことを確認できます。
   
    ```json
    {"type":"message","from":"server","dataType":"text","data":"Hello World"}
    ```

---

## <a name="next-steps"></a>次の手順

このクイックスタートでは、Web PubSub サービスに接続する方法と、接続されているクライアントにメッセージを発行する方法の基本概念について説明しています。

実際のアプリケーションでは、さまざまな言語の SDK を使用して独自のアプリケーションを構築できます。 また、サーバーレス アプリケーションを簡単に構築するための関数の拡張機能も用意しています。

[!INCLUDE [next step](includes/include-next-step.md)]
