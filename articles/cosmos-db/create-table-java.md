---
title: 'クイックスタート: Table API (Java) - Azure Cosmos DB | Microsoft Docs'
description: このクイックスタートでは、Azure Cosmos DB Table API を使って Azure Portal と Java でアプリケーションを作成する方法を示します
services: cosmos-db
documentationcenter: ''
author: arramac
manager: kfile
ms.assetid: 66327041-4d5e-4ce6-a394-fee107c18e59
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: quickstart
ms.date: 11/20/2017
ms.author: arramac
ms.openlocfilehash: fb544e11ac637201fb3fb032daeb395d5f0da839
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2018
---
# <a name="quickstart-build-a-table-api-app-with-java-and-azure-cosmos-db"></a>クイックスタート: Java と Azure Cosmos DB で Table API アプリを構築する

このクイックスタートでは、GitHub から例を複製することで、Java と Azure Cosmos DB の [Table API](table-introduction.md) を使ってアプリを作成する方法を示します。 このクイックスタートでは、Web ベースの Azure Portal で Azure Cosmos DB アカウントを作成する方法、およびデータ エクスプローラーを使用してテーブルとエンティティを作成する方法も説明します。

Azure Cosmos DB は、Microsoft のグローバルに配布されるマルチモデル データベース サービスです。 Azure Cosmos DB の中核をなすグローバル配布と水平方向のスケール機能を活用して、ドキュメント、キー/値、およびグラフ データベースをすばやく作成および照会できます。 

## <a name="prerequisites"></a>前提条件

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

加えて次の作業を行います。 

* [Java Development Kit (JDK) 1.7 以降](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
    * Ubuntu で `apt-get install default-jdk` を実行して JDK をインストールします。
    * 必ず、JDK のインストール先フォルダーを指すように JAVA_HOME 環境変数を設定してください。
* [Maven](http://maven.apache.org/) バイナリ アーカイブの[ダウンロード](http://maven.apache.org/download.cgi)と[インストール](http://maven.apache.org/install.html)
    * Ubuntu で `apt-get install maven` を実行して Maven をインストールします。
* [Git](https://www.git-scm.com/)
    * Ubuntu で `sudo apt-get install git` を実行して Git をインストールします。

## <a name="create-a-database-account"></a>データベース アカウントの作成

> [!IMPORTANT] 
> 新しいテーブル API アカウントを作成して一般公開のテーブル API SDK を操作する必要があります。 プレビュー期間中に作成されたテーブル API アカウントは、一般公開の SDK ではサポートされません。
>

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="add-a-table"></a>テーブルの追加

[!INCLUDE [cosmos-db-create-table](../../includes/cosmos-db-create-table.md)]

## <a name="add-sample-data"></a>サンプル データの追加

これで、データ エクスプローラーを使用して、新しいテーブルにデータを追加できます。

1. データ エクスプローラーで、**sample-table** を展開し、**[エンティティ]**、**[エンティティの追加]** の順にクリックします。

   ![Azure Portal のデータ エクスプローラーで新しいエンティティを作成する](./media/create-table-dotnet/azure-cosmosdb-data-explorer-new-document.png)
2. PartitionKey 値ボックスと RowKey 値ボックスにデータを追加し、**[エンティティの追加]** をクリックします。

   ![新しいエンティティのパーティション キーと行キーを設定する](./media/create-table-dotnet/azure-cosmosdb-data-explorer-new-entity.png)
  
    これで、テーブルにさらにエンティティを追加したり、エンティティを編集したり、データ エクスプローラーでデータをクエリしたりできます。 データ エクスプローラーでは、スループットを拡張し、ストアド プロシージャ、ユーザー定義関数、およびトリガーをテーブルに追加することもできます。

## <a name="clone-the-sample-application"></a>サンプル アプリケーションの複製

github で Table アプリの複製を作成し、接続文字列を設定して実行します。 プログラムでデータを処理することが非常に簡単であることがわかります。 

1. git bash などの git ターミナル ウィンドウを開き、`cd` コマンドを使用して、サンプル アプリをインストールするフォルダーに変更します。 

    ```bash
    cd "C:\git-samples"
    ```

2. 次のコマンドを実行して、サンプル レポジトリを複製します。 このコマンドは、コンピューター上にサンプル アプリのコピーを作成します。 

    ```bash
    git clone https://github.com/Azure-Samples/storage-table-java-getting-started.git 
    ```

## <a name="update-your-connection-string"></a>接続文字列を更新する

ここで Azure Portal に戻り、接続文字列情報を取得し、アプリにコピーします。 これでアプリが、ホストされているデータベースと通信できます。 

1. [Azure Portal](http://portal.azure.com/) で **[接続文字列]** をクリックします。 

   ![[接続文字列] ウィンドウに表示されている必要な接続文字列情報をコピーします。](./media/create-table-java/connection-string.png)

2. 右側にある [コピー] ボタンを使って、プライマリ接続文字列をコピーします。

3. C:\git-samples\storage-table-java-getting-started\src\main\resources フォルダーで config.properties を開きます。 

5. 1 行目をコメント アウトし、2 行目をコメント解除します。 最初の 2 行は次のようになります。

    ```
    #StorageConnectionString = UseDevelopmentStorage=true
    StorageConnectionString = DefaultEndpointsProtocol=https;AccountName=[ACCOUNTNAME];AccountKey=[ACCOUNTKEY]
    ```

6. ポータルのプライマリ接続文字列を、2 行目の StorageConnectionString に貼り付けます。 

    > [!IMPORTANT]
    > エンドポイントで documents.azure.com を使用している場合は、プレビュー アカウントを持っていることになるため、[新しいテーブル API](#create-a-database-account) アカウントを作成して、一般公開のテーブル API SDK を操作する必要があります。
    >

7. config.properties ファイルを保存します。

これで、Azure Cosmos DB と通信するために必要なすべての情報でアプリを更新しました。 

## <a name="run-the-app"></a>アプリの実行

1. git ターミナル ウィンドウで、storage-table-java-getting-started フォルダーに `cd` で移動します。

    ```git
    cd "C:\git-samples\storage-table-java-getting-started"
    ```

2. git ターミナル ウィンドウで、次のコマンドを実行して Java アプリケーションを起動します。

    ```git
    mvn compile exec:java 
    ```

    コンソール ウィンドウに、Azure Cosmos DB の新しいテーブル データベースに追加されるテーブル データが表示されます。

    これで、データ エクスプローラーに戻って、この新しいデータの表示、クエリ、変更、操作を行うことができます。 

## <a name="review-slas-in-the-azure-portal"></a>Azure Portal での SLA の確認

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>次の手順

このクイック スタートでは、Azure Cosmos DB アカウントを作成し、データ エクスプローラーを使用してテーブルを作成し、アプリを実行する方法を説明しました。  これで、Table API を使用して、データをクエリできます。  

> [!div class="nextstepaction"]
> [テーブル データを Table API にインポートする](table-import.md)
