<properties urlDisplayName="Table Service" pageTitle="テーブル ストレージを使用する方法 (Ruby) | Microsoft Azure" metaKeywords="Azure table storage service, Azure table service Ruby, table storage Ruby" description="Azure でテーブル ストレージ サービスを使用する方法について説明します。コード サンプルは Ruby API を使用して記述されています。" metaCanonical="" services="storage" documentationCenter="Ruby" title="Ruby からテーブル サービスを使用する方法" authors="guayan" solutions="" manager="wpickett" editor="" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="ruby" ms.topic="article" ms.date="01/01/1900" ms.author="guayan" />

# Ruby からテーブル サービスを使用する方法

このガイドでは、Windows Azure テーブル サービスを使用して一般的なシナリオを
実行する方法について説明します。サンプルは Ruby API を使用して記述されて
います。紹介するシナリオは、**テーブルの作成と削除、テーブルのエンティティの挿入とクエリ実行**などです。
テーブルの
詳細については、「[次のステップ][次のステップ]」のセクションを参照してください。

## 目次

-   [テーブル サービスとは][テーブル サービスとは]
-   [概念][概念]
-   [Azure のストレージ アカウントの作成][Azure のストレージ アカウントの作成]
-   [Ruby アプリケーションの作成][Ruby アプリケーションの作成]
-   [アプリケーションのストレージへのアクセスの構成][アプリケーションのストレージへのアクセスの構成]
-   [Azure のストレージ接続文字列の設定][Azure のストレージ接続文字列の設定]
-   [方法: テーブルを作成する][方法: テーブルを作成する]
-   [方法: エンティティをテーブルに追加する][方法: エンティティをテーブルに追加する]
-   [方法: エンティティを更新する][方法: エンティティを更新する]
-   [方法: エンティティのグループを操作する][方法: エンティティのグループを操作する]
-   [方法: エンティティを照会する][方法: エンティティを照会する]
-   [方法: エンティティのセットを照会する][方法: エンティティのセットを照会する]
-   [方法: エンティティ プロパティのサブセットを照会する][方法: エンティティ プロパティのサブセットを照会する]
-   [方法: エンティティを削除する][方法: エンティティを削除する]
-   [方法: テーブルを削除する][方法: テーブルを削除する]
-   [次のステップ][次のステップ]

[WACOM.INCLUDE [howto-table-storage](../includes/howto-table-storage.md)]

## <span id="create-a-windows-azure-storage-account"></span></a> Azure のストレージ アカウントの作成

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <span id="create-a-ruby-application"></span></a>Ruby アプリケーションを作成する

Ruby アプリケーションを作成します。手順については、
「[Azure VM での Ruby on Rails Web アプリケーション][Azure VM での Ruby on Rails Web アプリケーション]」を参照してください。

## <span id="configure-your-application-to-access-storage"></span></a>アプリケーションからストレージへのアクセスの構成

Azure Storage を使用するには、Ruby azure パッケージをダウンロードして
使用する必要があります。このパッケージには、ストレージ REST サービスと通信するための便利なライブラリのセットが含まれています。

### RubyGems を使用してパッケージを取得する

1.  **PowerShell** (Windows)、**ターミナル** (Mac)、**Bash** (Unix) などのコマンド ライン インターフェイスを使用します。

2.  コマンド ウィンドウに「**gem install azure**」と入力して、gem と依存関係をインストールします。

### パッケージをインポートする

任意のテキスト エディターを使用して、ストレージを使用する Ruby ファイルの先頭に次のコードを追加します。

    require "azure"

## <span id="setup-a-windows-azure-storage-connection"></span></a>Azure のストレージ接続文字列の設定

azure モジュールは、Azure のストレージ アカウントに接続するために必要な情報として、環境変数 **AZURE\_STORAGE\_ACCOUNT** および **AZURE\_STORAGE\_ACCESS\_KEY**
 を読み取ります。これらの環境変数が設定されていない場合は、**Azure::TableService** を使用する前に、次のコードを使用してアカウント情報を指定する必要があります。

    Azure.config.storage_account_name = "<your azure storage account>"
    Azure.config.storage_access_key = "<your azure storage access key>"

これらの値を取得するには、次の手順を実行します。

1.  [Azure の管理ポータル][Azure の管理ポータル]にログインします。

2.  使用するストレージ アカウントを表示します。

3.  ナビゲーション ウィンドウの下部にある **[キーの管理]** をクリックします。

4.  ポップアップ ダイアログに、ストレージ アカウント名、プライマリ アクセス キー、およびセカンダリ アクセス キーが表示されます。アクセス キーは、プライマリとセカンダリのどちらでも選択できます。

## <span id="how-to-create-a-table"></span></a>テーブルの作成方法

**Azure::TableService** オブジェクトを使用すると、テーブルとエンティティを操作できます。テーブルを作成するには、**create\_table()** メソッドを使用します。次の例では、テーブルを作成し、既に存在している場合はエラーを出力します。

    azure_table_service = Azure::TableService.new
    begin
      azure_table_service.create_table("testtable")
    rescue
      puts $!
    end

## <span id="how-to-add-an-entity-to-a-table"></span></a>エンティティをテーブルに追加する方法

エンティティを追加するには、エンティティのプロパティを定義するハッシュ オブジェクトを最初に作成します。すべてのエンティティについて、**PartitionKey** と **RowKey** を指定する必要があることに注意してください。これらはエンティティの一意の識別子であり、他のエンティティのプロパティよりはるかに高速に照会できる値です。Azure ストレージ サービスでは、テーブルのエンティティを多数のストレージ ノードに自動的に配布するために **PartitionKey** を使用します。**PartitionKey** が同じエンティティは同じノードに格納されます。**RowKey** は、エンティティが属するパーティション内のエンティティの一意の ID です。

    entity = { "content" => "test entity", 
      :PartitionKey => "test-partition-key", :RowKey => "1" }
    azure_table_service.insert_entity("testtable", entity)

## <span id="how-to-update-an-entity"></span></a>方法: エンティティを更新する

既存のエンティティを更新するには、複数のメソッドがあります。

-   **update\_entity():** 既存のエンティティを置換することで更新します。
-   **merge\_entity():** 新しいプロパティ値を既存のエンティティにマージすることで既存のエンティティを更新します。
-   **insert\_or\_merge\_entity():** 既存のエンティティを置換することで更新します。エンティティが存在しない場合は、新しいエンティティが挿入されます。
-   **insert\_or\_replace\_entity():** 新しいプロパティ値を既存のエンティティにマージすることで既存のエンティティを更新します。エンティティが存在しない場合は、新しいエンティティが挿入されます。

次の例に、**update\_entity()** を使用してエンティティを更新する方法を示します。

    entity = { "content" => "test entity with updated content", 
      :PartitionKey => "test-partition-key", :RowKey => "1" }
    azure_table_service.update_entity("testtable", entity)

**update\_entity()** と **merge\_entity()** では、更新されるエンティティが存在しないと更新操作は失敗します。したがって、既に存在しているかどうかに関係なくエンティティを格納するには、代わりに **insert\_or\_replace\_entity()** または **insert\_or\_merge\_entity()** を使用する必要があります。

## <span id="how-to-work-with-groups-of-entities"></span></a>方法: エンティティのグループを操作する

状況によって、複数の操作をバッチとして送信し、サーバーによるアトミック処理を行うことが合理的である場合があります。このためには、まず **Batch** オブジェクトを作成し、次に **TableService** の **execute\_batch()** メソッドを使用します。次の例では、RowKey が 2 および 3 である 2 つのエンティティをバッチで送信する方法を示します。これは、同じ PartitionKey を持つエンティティでのみ機能することに注意してください。

    azure_table_service = Azure::TableService.new
    batch = Azure::Storage::Table::Batch.new("testtable", 
      "test-partition-key") do
      insert "2", { "content" => "new content 2" }
      insert "3", { "content" => "new content 3" }
    end
    results = azure_table_service.execute_batch(batch)

## <span id="how-to-query-for-an-entity"></span></a>方法: エンティティを照会する

テーブル内のエンティティを照会するには、**get\_entity()** メソッドを使用して、テーブル名、**PartitionKey**、および **RowKey** を渡します。

    result = azure_table_service.get_entity("testtable", "test-partition-key", 
      "1")

## <span id="how-to-query-a-set-of-entities"></span></a>方法: エンティティのセットを照会する

テーブル内のエンティティのセットを照会するには、クエリ ハッシュ オブジェクトを作成し、**query\_entities()** メソッドを使用します。次の例では、同じ **PartitionKey** を持つエンティティをすべて取得します。

    query = { :filter => "PartitionKey eq 'test-partition-key'" }
    result, token = azure_table_service.query_entities("testtable", query)

**注** 結果セットが大きすぎて単一のクエリで返すことができない場合は、継続トークンが返されます。この継続トークンを使用すると、後続のページを取得できます。

## <span id="how-to-query-a-subset-of-entity-properties"></span></a>方法: エンティティ プロパティのサブセットを照会する

テーブルに対するクエリでは、ごくわずかのプロパティだけをエンティティから取得できます。"プロジェクション" と呼ばれるこの方法では、帯域幅の使用が削減され、クエリのパフォーマンスが向上します。特に、大量のエンティティがある場合に役立ちます。select 句を使用して、クライアントに渡すプロパティの名前を指定します。

    query = { :filter => "PartitionKey eq 'test-partition-key'", 
      :select => ["content"] }
    result, token = azure_table_service.query_entities("testtable", query)

## <span id="how-to-delete-an-entity"></span></a>方法: エンティティを削除する

エンティティを削除するには、**delete\_entity()** メソッドを使用します。目的のエンティティを含んでいるテーブルの名前と、エンティティの PartitionKey および RowKey を渡す必要があります。

        azure_table_service.delete_entity("testtable", "test-partition-key", "1")

## <span id="how-to-delete-a-table"></span></a>方法: テーブルを削除する

テーブルを削除するには、**delete\_table()** メソッドを使用して、削除するテーブルの名前を渡します。

        azure_table_service.delete_table("testtable")

## <span id="next-steps"></span></a> 次のステップ

これで、テーブル ストレージの基本を学習できました。さらに複雑なストレージ タスクを実行する方法については、次のリンク先を参照してください。

-   MSDN リファレンス: [Azure のデータの格納とアクセス][Azure のデータの格納とアクセス]
-   [Azure のストレージ チーム ブログ][Azure のストレージ チーム ブログ]
-   GitHub の [Azure SDK for Ruby][Azure SDK for Ruby] リポジトリ

  [次のステップ]: #next-steps
  [テーブル サービスとは]: #what-is
  [概念]: #concepts
  [Azure のストレージ アカウントの作成]: #create-a-windows-azure-storage-account
  [Ruby アプリケーションの作成]: #create-a-ruby-application
  [アプリケーションのストレージへのアクセスの構成]: #configure-your-application-to-access-storage
  [Azure のストレージ接続文字列の設定]: #setup-a-windows-azure-storage-connection
  [方法: テーブルを作成する]: #how-to-create-a-table
  [方法: エンティティをテーブルに追加する]: #how-to-add-an-entity-to-a-table
  [方法: エンティティを更新する]: #how-to-update-an-entity
  [方法: エンティティのグループを操作する]: #how-to-work-with-groups-of-entities
  [方法: エンティティを照会する]: #how-to-query-for-an-entity
  [方法: エンティティのセットを照会する]: #how-to-query-a-set-of-entities
  [方法: エンティティ プロパティのサブセットを照会する]: #how-to-query-a-subset-of-entity-properties
  [方法: エンティティを削除する]: #how-to-delete-an-entity
  [方法: テーブルを削除する]: #how-to-delete-a-table
  [Azure VM での Ruby on Rails Web アプリケーション]: /ja-jp/develop/ruby/tutorials/web-app-with-linux-vm/
  [Azure の管理ポータル]: https://manage.windowsazure.com/
  [Azure のデータの格納とアクセス]: http://msdn.microsoft.com/ja-jp/library/windowsazure/gg433040.aspx
  [Azure のストレージ チーム ブログ]: http://blogs.msdn.com/b/windowsazurestorage/
  [Azure SDK for Ruby]: http://github.com/WindowsAzure/azure-sdk-for-ruby
