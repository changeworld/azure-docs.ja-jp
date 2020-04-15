---
title: チュートリアル - 変更フィードを使用したエンドツーエンドの Async Java SQL API アプリケーションのサンプル
description: このチュートリアルでは、変更フィードを使用してコンテナーの具体化されたビューを維持しながら、Azure Cosmos DB コンテナーにドキュメントを挿入する単純な Java SQL API アプリケーションについて説明します。
author: anfeldma
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: tutorial
ms.date: 04/01/2020
ms.author: anfeldma
ms.openlocfilehash: 5eab523dde2a13a85b0c8ff5bcbb3ecb5912e78e
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/02/2020
ms.locfileid: "80587219"
---
# <a name="tutorial---an-end-to-end-async-java-sql-api-application-sample-with-change-feed"></a>チュートリアル - 変更フィードを使用したエンドツーエンドの Async Java SQL API アプリケーションのサンプル

このチュートリアル ガイドでは、変更フィードを使用してコンテナーの具体化されたビューを維持しながら、Azure Cosmos DB コンテナーにドキュメントを挿入する単純な Java SQL API アプリケーションについて説明します。

## <a name="prerequisites"></a>前提条件

* パーソナル コンピューター

* Azure Cosmos DB アカウントの URI とキー

* Maven

* Java 8

## <a name="background"></a>バックグラウンド

Azure Cosmos DB の変更フィードには、ドキュメントの挿入に応答してアクションをトリガーするイベントドリブン インターフェイスが用意されています。 これには多くの用途があります。 たとえば、読み取りと書き込みの両方の負荷が高いアプリケーションでは、変更フィードは主に、ドキュメントを取り込んでいるときに、コンテナーの**具体化されたビュー**をリアルタイムで作成するために使用されます。 具体化されたビューのコンテナーは、同じデータを保持しますが、効率的な読み取りのためにパーティション分割されているため、アプリケーションの読み取りと書き込みの両方を効率的に行うことができます。

変更フィードのイベントを管理する作業は、主に、SDK に組み込まれている変更フィード プロセッサ ライブラリによって行われます。 このライブラリは、必要に応じて変更フィードのイベントを複数のワーカー間に配布するのに十分な性能を備えています。 変更フィード ライブラリにコールバックを提供するだけで実行できます。

この簡単な例では、1 つのワーカーが、具体化されたビューからドキュメントを作成および削除する変更フィード プロセッサ ライブラリを示します。

## <a name="setup"></a>セットアップ

まだ行っていない場合は、アプリのサンプル リポジトリを複製します。

```bash
git clone https://github.com/Azure-Samples/azure-cosmos-java-sql-app-example.git
```

> このクイック スタートでは、Java SDK 4.0 または Java SDK 3.7.0 を使用して作業することを選択できます。 **Java SDK 3.7.0 を使用する場合は、ターミナルに「```git checkout SDK3.7.0```** 」と入力します。 それ以外の場合は、```master``` ブランチにとどまります。それにより、既定で Java SDK 4.0 が使用されます。

リポジトリ ディレクトリでターミナルを開きます。 次を実行してアプリをビルドします。

```bash
mvn clean package
```

## <a name="walkthrough"></a>チュートリアル

1. 最初のチェックとして、Azure Cosmos DB アカウントを持っている必要があります。 ブラウザーで **Azure portal** を開き、Azure Cosmos DB アカウントに移動します。左側のペインで**データ エクスプローラー**に移動します。

    ![Azure Cosmos DB アカウント](media/create-sql-api-java-changefeed/cosmos_account_empty.JPG)

1. 次のコマンドを使用して、ターミナルでアプリを実行します。

    ```bash
    mvn exec:java -Dexec.mainClass="com.azure.cosmos.workedappexample.SampleGroceryStore" -DACCOUNT_HOST="your-account-uri" -DACCOUNT_KEY="your-account-key" -Dexec.cleanupDaemonThreads=false
    ```

1. 次が表示されたら、Enter キーを押します。

    ```bash
    Press enter to create the grocery store inventory system...
    ```

    次に、ブラウザーで Azure portal のデータ エクスプローラーに戻ります。 次の 3 つの空のコンテナーを持つ **GroceryStoreDatabase** というデータベースが追加されていることがわかります。 

    * **InventoryContainer** - サンプル食料品店のインベントリ レコード。項目 ```id``` (UUID) でパーティション分割されています。
    * **InventoryContainer-pktype** - インベントリ レコードの具体化されたビュー。項目 ```type``` に対してクエリを実行するように最適化されています。
    * **InventoryContainer-leases** - リース コンテナーは、変更フィードに常に必要です。リースは、変更フィードの読み取りでのアプリの進行状況を追跡します。


    ![空のコンテナー](media/create-sql-api-java-changefeed/cosmos_account_resources_lease_empty.JPG)


1. ターミナルで、プロンプトが表示されます。

    ```bash
    Press enter to start creating the materialized view...
    ```

    Enter キーを押します。 これで、次のコード ブロックが実行され、別のスレッドで変更フィード プロセッサが初期化されます。 


    **Java SDK 4.0**
    ```java
    changeFeedProcessorInstance = getChangeFeedProcessor("SampleHost_1", feedContainer, leaseContainer);
    changeFeedProcessorInstance.start()
        .subscribeOn(Schedulers.elastic())
        .doOnSuccess(aVoid -> {
            isProcessorRunning.set(true);
        })
        .subscribe();

    while (!isProcessorRunning.get()); //Wait for Change Feed processor start
    ```

    **Java SDK 3.7.0**
    ```java
    changeFeedProcessorInstance = getChangeFeedProcessor("SampleHost_1", feedContainer, leaseContainer);
    changeFeedProcessorInstance.start()
        .subscribeOn(Schedulers.elastic())
        .doOnSuccess(aVoid -> {
            isProcessorRunning.set(true);
        })
        .subscribe();

    while (!isProcessorRunning.get()); //Wait for Change Feed processor start    
    ```

    ```"SampleHost_1"``` は、変更フィード プロセッサ ワーカーの名前です。 ```changeFeedProcessorInstance.start()``` が実際に変更フィード プロセッサを開始します。

    ブラウザーで Azure portal のデータ エクスプローラーに戻ります。 **InventoryContainer-leases** コンテナーで、 **[items]\(項目\)** をクリックして内容を表示します。 変更フィード プロセッサによってリース コンテナーが設定されたことがわかります。つまり、プロセッサが ```SampleHost_1``` ワーカーに、**InventoryContainer** の一部のパーティションに対するリースを割り当てました。

    ![リース](media/create-sql-api-java-changefeed/cosmos_leases.JPG)

1. ターミナルでもう一度 Enter キーを押します。 これにより、**InventoryContainer** への 10 個のドキュメントの挿入がトリガーされます。 各ドキュメントの挿入は JSON として変更フィードに表示されます。次のコールバック コードは、JSON ドキュメントを具体化されたビューにミラーリングすることによって、これらのイベントを処理します。

    **Java SDK 4.0**
    ```java
    public static ChangeFeedProcessor getChangeFeedProcessor(String hostName, CosmosAsyncContainer feedContainer, CosmosAsyncContainer leaseContainer) {
        ChangeFeedProcessorOptions cfOptions = new ChangeFeedProcessorOptions();
        cfOptions.setFeedPollDelay(Duration.ofMillis(100));
        cfOptions.setStartFromBeginning(true);
        return ChangeFeedProcessor.changeFeedProcessorBuilder()
            .setOptions(cfOptions)
            .setHostName(hostName)
            .setFeedContainer(feedContainer)
            .setLeaseContainer(leaseContainer)
            .setHandleChanges((List<JsonNode> docs) -> {
                for (JsonNode document : docs) {
                        //Duplicate each document update from the feed container into the materialized view container
                        updateInventoryTypeMaterializedView(document);
                }

            })
            .build();
    }

    private static void updateInventoryTypeMaterializedView(JsonNode document) {
        typeContainer.upsertItem(document).subscribe();
    }
    ```

    **Java SDK 3.7.0**
    ```java
    public static ChangeFeedProcessor getChangeFeedProcessor(String hostName, CosmosContainer feedContainer, CosmosContainer leaseContainer) {
        ChangeFeedProcessorOptions cfOptions = new ChangeFeedProcessorOptions();
        cfOptions.feedPollDelay(Duration.ofMillis(100));
        cfOptions.startFromBeginning(true);
        return ChangeFeedProcessor.Builder()
            .options(cfOptions)
            .hostName(hostName)
            .feedContainer(feedContainer)
            .leaseContainer(leaseContainer)
            .handleChanges((List<CosmosItemProperties> docs) -> {
                for (CosmosItemProperties document : docs) {
                        //Duplicate each document update from the feed container into the materialized view container
                        updateInventoryTypeMaterializedView(document);
                }

            })
            .build();
    }

    private static void updateInventoryTypeMaterializedView(CosmosItemProperties document) {
        typeContainer.upsertItem(document).subscribe();
    }    
    ```

1. コードの実行には 5 から 10 秒かかります。 次に、Azure portal のデータ エクスプローラーに戻り、 **[InventoryContainer] > [items]\(項目\)** に移動します。 項目がインベントリ コンテナーに挿入されていることがわかります。パーティション キー (```id```) に注意してください。

    ![フィード コンテナー](media/create-sql-api-java-changefeed/cosmos_items.JPG)

1. 次に、データ エクスプローラーで、 **[InventoryContainer-pktype] > [items]\(項目\)** に移動します。 これは具体化されたビューです。このコンテナー内の項目は、変更フィードによってプログラムで挿入されたため、**InventoryContainer** をミラーリングしています。 パーティション キー (```type```) に注意してください。 したがって、この具体化されたビューは、```type``` をフィルター処理するクエリ用に最適化されています。これは、```id``` でパーティション分割されている **InventoryContainer** では効率が悪くなります。

    ![具体化されたビュー](media/create-sql-api-java-changefeed/cosmos_materializedview2.JPG)

1. 1 つの ```upsertItem()``` 呼び出しだけを使用して、**InventoryContainer** と **InventoryContainer-pktype** の両方からドキュメントを削除します。 まず、Azure portal のデータ エクスプローラーを見てみましょう。 ```/type == "plums"``` のドキュメント (下で赤で囲まれている) を削除します。

    ![具体化されたビュー](media/create-sql-api-java-changefeed/cosmos_materializedview-emph-todelete.JPG)

    もう一度 Enter キーを押して、コード例の ```deleteDocument()``` 関数を呼び出します。 以下に示すこの関数は、ドキュメントの新しいバージョンを ```/ttl == 5``` で upsert します。これにより、ドキュメントの Time-To-Live (TTL) は 5 秒に設定されます。 
    
    **Java SDK 4.0**
    ```java
    public static void deleteDocument() {

        String jsonString =    "{\"id\" : \"" + idToDelete + "\""
                + ","
                + "\"brand\" : \"Jerry's\""
                + ","
                + "\"type\" : \"plums\""
                + ","
                + "\"quantity\" : \"50\""
                + ","
                + "\"ttl\" : 5"
                + "}";

        ObjectMapper mapper = new ObjectMapper();
        JsonNode document = null;

        try {
            document = mapper.readTree(jsonString);
        } catch (Exception e) {
            e.printStackTrace();
        }

        feedContainer.upsertItem(document,new CosmosItemRequestOptions()).block();
    }    
    ```

    **Java SDK 3.7.0**
    ```java
    public static void deleteDocument() {

        String jsonString =    "{\"id\" : \"" + idToDelete + "\""
                + ","
                + "\"brand\" : \"Jerry's\""
                + ","
                + "\"type\" : \"plums\""
                + ","
                + "\"quantity\" : \"50\""
                + ","
                + "\"ttl\" : 5"
                + "}";

        ObjectMapper mapper = new ObjectMapper();
        JsonNode document = null;

        try {
            document = mapper.readTree(jsonString);
        } catch (Exception e) {
            e.printStackTrace();
        }

        feedContainer.upsertItem(document,new CosmosItemRequestOptions()).block();
    }    
    ```

    変更フィード ```feedPollDelay``` は 100ms に設定されています。そのため、変更フィードは、この更新にほぼ瞬時に応答し、上に示した ```updateInventoryTypeMaterializedView()``` を呼び出します。 最後の関数呼び出しは、TTL 5 秒の新しいドキュメントを **InventoryContainer-pktype** に upsert します。

    その結果、約 5 秒後にドキュメントの有効期限が切れて、両方のコンテナーから削除されます。

    変更フィードがイベントを発行するのは、項目の挿入または更新に対してのみで、項目の削除は対象にならないため、この手順が必要になります。

1. もう一度 Enter キーを押して、プログラムを終了し、そのリソースをクリーンアップします。
