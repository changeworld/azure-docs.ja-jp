<properties linkid="develop-php-table-service" urlDisplayName="Table Service" pageTitle="How to use table storage (PHP) | Microsoft Azure" metaKeywords="Azure Table service PHP, Azure creating table, Azure deleting table, Azure insert table, Azure query table" description="Learn how to use the Table service from PHP to create and delete a table, and insert, delete, and query the table." metaCanonical="" services="storage" documentationCenter="PHP" title="How to use the Table service from PHP" authors="" solutions="" manager="" editor="" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author="" />

# PHP からテーブル サービスを使用する方法

このガイドでは、Azure テーブル サービスを使用して一般的なシナリオを実行する方法について説明します。サンプルは PHP で記述され、[Azure SDK for PHP][Azure SDK for PHP] を利用しています。紹介するシナリオは、**テーブルの作成と削除、テーブルのエンティティの挿入、削除、および照会**などです。Azure テーブル サービスの詳細については、「[次のステップ][次のステップ]」を参照してください。

## 目次

-   [テーブル サービスとは][テーブル サービスとは]
-   [概念][概念]
-   [Azure のストレージ アカウントの作成][Azure のストレージ アカウントの作成]
-   [PHP アプリケーションの作成][PHP アプリケーションの作成]
-   [テーブル サービスにアクセスするようにアプリケーションを構成する][テーブル サービスにアクセスするようにアプリケーションを構成する]
-   [Azure のストレージ接続文字列の設定][Azure のストレージ接続文字列の設定]
-   [方法: テーブルの作成][方法: テーブルの作成]
-   [方法: エンティティをテーブルに追加する][方法: エンティティをテーブルに追加する]
-   [方法: 単一のエンティティを取得する][方法: 単一のエンティティを取得する]
-   [方法: パーティション内のすべてのエンティティを取得する][方法: パーティション内のすべてのエンティティを取得する]
-   [方法: パーティション内のエンティティのサブセットを取得する][方法: パーティション内のエンティティのサブセットを取得する]
-   [方法: エンティティ プロパティのサブセットを取得する][方法: エンティティ プロパティのサブセットを取得する]
-   [方法: エンティティを更新する][方法: エンティティを更新する]
-   [方法: バッチ テーブル処理][方法: バッチ テーブル処理]
-   [方法: テーブルを削除する][方法: テーブルを削除する]
-   [次のステップ][次のステップ]

[WACOM.INCLUDE [howto-table-storage](../includes/howto-table-storage.md)]

## <span id="CreateAccount"></span></a>Azure のストレージ アカウントの作成

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <span id="CreateApplication"></span></a>PHP アプリケーションの作成

Azure テーブル サービスにアクセスする PHP アプリケーションを作成するための要件は、コード内から Azure SDK for PHP のクラスを参照することのみです。アプリケーションの作成には、メモ帳などの任意の開発ツールを使用できます。

このガイドで使用するテーブル サービス機能は、PHP アプリケーション内からローカルで呼び出すことも、Azure の Web ロール、worker ロール、または Web サイト上で実行されるコード内で呼び出すこともできます。

## <span id="GetClientLibrary"></span></a>Azure クライアント ライブラリの入手

[WACOM.INCLUDE [get-client-libraries](../includes/get-client-libraries.md)]

## <span id="ConfigureStorage"></span></a>テーブル サービスにアクセスするようにアプリケーションを構成する

Azure テーブル サービス API を使用するには、次の要件があります。

1.  [require\_once][require\_once] ステートメントを使用してオートローダー ファイルを参照する
2.  使用する可能性のあるクラスを参照する

次の例では、オートローダー ファイルをインクルードし、**ServicesBuilder** クラスを参照する方法を示しています。

> [WACOM.NOTE]
> この例 (およびこの記事のその他の例) では、Composer を使用して Azure 向け PHP クライアント ライブラリがインストールされていることを前提としています。ライブラリを手動でまたは PEAR パッケージとしてインストールした場合は、`WindowsAzure.php` オートローダー ファイルを参照する必要があります。

    require_once 'vendor\autoload.php';
    use WindowsAzure\Common\ServicesBuilder;

この後のコード例では、`require_once` ステートメントが常に記述されていますが、コード例の実行に必要なクラスのみ参照されます。

## <span id="ConnectionString"></span></a>Azure のストレージ接続文字列の設定

Azure テーブル サービス クライアントをインスタンス化するには、まず有効な接続文字列が必要です。テーブル サービスの接続文字列の形式は次のとおりです。

ライブ サービスにアクセスする場合:

    DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]

エミュレーター ストレージにアクセスする場合:

    UseDevelopmentStorage=true

いずれの Azure サービス クライアントを作成するにも、**ServicesBuilder** クラスを使用する必要があります。そのための方法は次のとおりです。

-   接続文字列を直接渡す
-   **CloudConfigurationManager (CCM)** を使用して複数の外部ソースに対して接続文字列を確認する

    -   既定では 1 つの外部ソース (環境変数) のみサポートされています。
    -   **ConnectionStringSource** クラスを継承して新しいソースを追加できます。

ここで概説している例では、接続文字列を直接渡します。

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;

    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

## <span id="CreateTable"></span></a>方法: テーブルの作成

**TableRestProxy** オブジェクトの **createTable** メソッドを使用してテーブルを作成できます。テーブルの作成時、テーブル サービスのタイムアウトを設定できます (テーブル サービスのタイムアウトの詳細については、「[テーブル サービス操作のサーバー タイムアウトの設定][テーブル サービス操作のサーバー タイムアウトの設定]」を参照)。

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    try {
        // Create table.
        $tableRestProxy->createTable("mytable");
    }
    catch(ServiceException $e){
        $code = $e->getCode();
        $error_message = $e->getMessage();
        // Handle exception based on error codes and messages.
        // Error codes and messages can be found here: 
        // http://msdn.microsoft.com/ja-jp/library/windowsazure/dd179438.aspx
    }

テーブル名の制限については、「[テーブル サービス データ モデルについて][テーブル サービス データ モデルについて]」を参照してください。

## <span id="AddEntity"></span></a>方法: エンティティをテーブルに追加する

エンティティをテーブルに追加するには、新しい **Entity** オブジェクトを作成し、**TableRestProxy-\>insertEntity** に渡します。エンティティの作成時には `PartitionKey` と `RowKey` を指定する必要があることに注意してください。これらにはエンティティの一意の識別子であり、他のエンティティのプロパティよりはるかに高速に照会できる値です。システムでは `PartitionKey` が使用されて多くのストレージ ノードにテーブルのエンティティが自動的に配布されます。`PartitionKey` が同じエンティティは同じノードで格納されています (同じノードで格納されている複数のエンティティに対する処理は、異なるノードにまたがって格納されているエンティティに対する処理よりもパフォーマンスは高くなります)。`RowKey` は特定のパーティション内のエンティティの一意の ID です。

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;
    use WindowsAzure\Table\Models\Entity;
    use WindowsAzure\Table\Models\EdmType;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    $entity = new Entity();
    $entity->setPartitionKey("tasksSeattle");
    $entity->setRowKey("1");
    $entity->addProperty("Description", null, "Take out the trash.");
    $entity->addProperty("DueDate", 
                         EdmType::DATETIME, 
                         new DateTime("2012-11-05T08:15:00-08:00"));
    $entity->addProperty("Location", EdmType::STRING, "Home");

    try{
        $tableRestProxy->insertEntity("mytable", $entity);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/ja-jp/library/windowsazure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
    }

テーブルのプロパティと型については、「[テーブル サービス データ モデルについて][テーブル サービス データ モデルについて]」を参照してください。

**TableRestProxy** クラスには、他にもエンティティを挿入する 2 つのメソッドが用意されています。**insertOrMergeEntity** と **insertOrReplaceEntity** です。これらのメソッドを使用するには、新しい **Entity** を作成し、いずれかのメソッドにパラメーターとして渡します。各メソッドは、渡されたエンティティが存在しない場合に、そのエンティティを挿入します。エンティティが既に存在する場合、**insertOrMergeEntity** はプロパティ値が既に存在するなら更新し、存在しないなら新しいプロパティを追加します。一方、**insertOrReplaceEntity** は既存のエンティティを完全に置き換えます。次の例は、**insertOrMergeEntity** を使用する方法を示しています。`PartitionKey` が "tasksSeattle" で `RowKey` が "1" であるエンティティがまだ存在しない場合は挿入されます。ただし、既に挿入されている場合 (前の例を参照)、`DueDate` プロパティが更新され、`Status` プロパティが追加されます。`Description` プロパティと `Location` プロパティも更新されますが、値は実際には変更されないままになります。これら後者の 2 つのプロパティは例に示しているように追加されますが、ターゲット エンティティに存在しているため、それらの既存の値は変更されないままになります。

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;
    use WindowsAzure\Table\Models\Entity;
    use WindowsAzure\Table\Models\EdmType;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    //Create new entity.
    $entity = new Entity();

    // PartitionKey and RowKey are required.
    $entity->setPartitionKey("tasksSeattle");
    $entity->setRowKey("1");

    // If entity exists, existing properties are updated with new values and
    // new properties are added. Missing properties are unchanged.
    $entity->addProperty("Description", null, "Take out the trash.");
    $entity->addProperty("DueDate", EdmType::DATETIME, new DateTime()); // Modified the DueDate field.
    $entity->addProperty("Location", EdmType::STRING, "Home");
    $entity->addProperty("Status", EdmType::STRING, "Complete"); // Added Status field.

    try {
        // Calling insertOrReplaceEntity, instead of insertOrMergeEntity as shown,
        // would simply replace the entity with PartitionKey "tasksSeattle" and RowKey "1".
        $tableRestProxy->insertOrMergeEntity("mytable", $entity);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/ja-jp/library/windowsazure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }
       

## <span id="RetrieveEntity"></span></a>方法: 単一のエンティティを取得する

**TableRestProxy-\>getEntity** メソッドを使用して、その `PartitionKey` と `RowKey` を照会することで、1 つのエンティティを取得できます。次の例では、パーティション キー `tasksSeattle` と行キー `1` を **getEntity** メソッドに渡しています。

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    try {
        $result = $tableRestProxy->getEntity("mytable", "tasksSeattle", 1);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/ja-jp/library/windowsazure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

    $entity = $result->getEntity();

    echo $entity->getPartitionKey().":".$entity->getRowKey();

## <span id="RetEntitiesInPartition"></span></a>方法: パーティション内のすべてのエンティティを取得する

エンティティのクエリはフィルターを使用して作成します (詳細については「[テーブルおよびエンティティのクエリ][テーブルおよびエンティティのクエリ]」を参照)。パーティション内のすべてのエンティティを取得するには、フィルター "PartitionKey eq *partition\_name*" を使用します。次の例では、フィルターを **queryEntities** メソッドに渡すことで、`tasksSeattle` パーティション内のすべてのエンティティを取得する方法を示しています。

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    $filter = "PartitionKey eq 'tasksSeattle'";

    try {
        $result = $tableRestProxy->queryEntities("mytable", $filter);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/ja-jp/library/windowsazure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

    $entities = $result->getEntities();

    foreach($entities as $entity){
        echo $entity->getPartitionKey().":".$entity->getRowKey()."<br />";
    }

## <span id="RetrieveSubset"></span></a>方法: パーティション内のエンティティのサブセットを取得する

前の例で示している同じパターンを使用してパーティション内のエンティティのサブセットを取得できます。取得するエンティティのサブセットは、使用するフィルターによって決まります (詳細については、「[テーブルおよびエンティティのクエリ][テーブルおよびエンティティのクエリ]」を参照)。次の例では、フィルターを使用して、`Location` で指定した場所で、`DueDate` で指定した日付より前のエンティティをすべて取得する方法を示しています。

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    $filter = "Location eq 'Office' and DueDate lt '2012-11-5'";

    try {
        $result = $tableRestProxy->queryEntities("mytable", $filter);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/ja-jp/library/windowsazure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

    $entities = $result->getEntities();

    foreach($entities as $entity){
        echo $entity->getPartitionKey().":".$entity->getRowKey()."<br />";
    }

## <span id="RetPropertiesSubset"></span></a>方法: エンティティ プロパティのサブセットを取得する

クエリを使用してエンティティのプロパティのサブセットを取得できます。*プロジェクション*と呼ばれるこの方法では、帯域幅の使用が削減され、クエリのパフォーマンスが向上します。特に、大量のエンティティがある場合に役立ちます。取得するプロパティを指定するには、プロパティの名前を **Query-\>addSelectField** メソッドに渡します。このメソッドを複数回呼び出して、ほかのプロパティを追加できます。**TableRestProxy-&gt;queryEntities** の実行後、返されるエンティティには選択したプロパティのみ格納されています (テーブル エンティティのサブセットが返されるようにする場合は、前のクエリで示したようにフィルターを使用します)。

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;
    use WindowsAzure\Table\Models\QueryEntitiesOptions;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    $options = new QueryEntitiesOptions();
    $options->addSelectField("Description");

    try {
        $result = $tableRestProxy->queryEntities("mytable", $options);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/ja-jp/library/windowsazure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

    // All entities in the table are returned, regardless of whether 
    // they have the Description field.
    // To limit the results returned, use a filter.
    $entities = $result->getEntities();

    foreach($entities as $entity){
        $description = $entity->getProperty("Description")->getValue();
        echo $description."<br />";
    }

## <span id="UpdateEntity"></span></a>方法: エンティティを更新する

既存のエンティティは、エンティティの **Entity-\>setProperty** および **Entity-\>addProperty** メソッドを使用した後、**TableRestProxy-\>updateEntity** を呼び出すことで更新できます。次の例では、エンティティを取得してから、1 つのプロパティの変更、別のプロパティの削除、新しいプロパティの追加を行っています。プロパティを削除するには、その値を **null** に設定することに注意してください。

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;
    use WindowsAzure\Table\Models\Entity;
    use WindowsAzure\Table\Models\EdmType;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    $result = $tableRestProxy->getEntity("mytable", "tasksSeattle", 1);

    $entity = $result->getEntity();

    $entity->setPropertyValue("DueDate", new DateTime()); //Modified DueDate.

    $entity->setPropertyValue("Location", null); //Removed Location.

    $entity->addProperty("Status", EdmType::STRING, "In progress"); //Added Status.

    try {
        $tableRestProxy->updateEntity("mytable", $entity);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/ja-jp/library/windowsazure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

## <span id="DeleteEntity"></span></a>方法: エンティティを削除する

エンティティを削除するには、テーブル名、およびエンティティの `PartitionKey` と `RowKey` を **TableRestProxy-\>deleteEntity** メソッドに渡します。

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    try {
        // Delete entity.
        $tableRestProxy->deleteEntity("mytable", "tasksSeattle", "2");
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/ja-jp/library/windowsazure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

同時実行のチェック用に、削除するエンティティの Etag を設定できることに注意してください。そのためには、**DeleteEntityOptions-\>setEtag** メソッドを使用して、**DeleteEntityOptions** オブジェクトを 4 番目のパラメーターとして **deleteEntity** に渡します。

## <span id="BatchOperations"></span></a>方法: バッチ テーブル処理

**TableRestProxy-\>batch** メソッドを使用すると、1 つの要求で複数の処理を実行できます。ここで示しているパターンでは、処理を **BatchRequest** オブジェクトに追加し、**BatchRequest** オブジェクトを **TableRestProxy-\>batch** メソッドに渡しています。処理を **BatchRequest** オブジェクトに追加するには、次のいずれかのメソッドを複数回呼び出すことができます。

-   **addInsertEntity** (insertEntity 処理を追加)
-   **addUpdateEntity** (updateEntity 処理を追加)
-   **addMergeEntity** (mergeEntity 処理を追加)
-   **addInsertOrReplaceEntity** (insertOrReplaceEntity 処理を追加)
-   **addInsertOrMergeEntity** (insertOrMergeEntity 処理を追加)
-   **addDeleteEntity** (deleteEntity 処理を追加)

次の例では、1 つの要求で **insertEntity** 処理と **deleteEntity** 処理を実行する方法を示しています。

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;
    use WindowsAzure\Table\Models\Entity;
    use WindowsAzure\Table\Models\EdmType;
    use WindowsAzure\Table\Models\BatchOperations;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    // Create list of batch operation.
    $operations = new BatchOperations();

    $entity1 = new Entity();
    $entity1->setPartitionKey("tasksSeattle");
    $entity1->setRowKey("2");
    $entity1->addProperty("Description", null, "Clean roof gutters.");
    $entity1->addProperty("DueDate", 
                          EdmType::DATETIME, 
                          new DateTime("2012-11-05T08:15:00-08:00"));
    $entity1->addProperty("Location", EdmType::STRING, "Home");

    // Add operation to list of batch operations.
    $operations->addInsertEntity("mytable", $entity1);

    // Add operation to list of batch operations.
    $operations->addDeleteEntity("mytable", "tasksSeattle", "1");

    try {
        $tableRestProxy->batch($operations);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/ja-jp/library/windowsazure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

テーブル バッチ処理の詳細については、「[エンティティ グループ トランザクションの実行][エンティティ グループ トランザクションの実行]」を参照してください。

## <span id="DeleteTable"></span></a>方法: テーブルを削除する

最後に、テーブルを削除するには、テーブル名を **TableRestProxy-\>deleteTable** メソッドに渡します。

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    try {
        // Delete table.
        $tableRestProxy->deleteTable("mytable");
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/ja-jp/library/windowsazure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

## <span id="NextSteps"></span></a>次のステップ

これで、Azure テーブル サービスの基本を学習できました。さらに複雑なストレージ タスクを実行する方法については、次のリンク先を参照してください。

-   MSDN リファレンス: [Azure のデータの格納とアクセス][Azure のデータの格納とアクセス]
-   Azure のストレージ チーム ブログ: <http://blogs.msdn.com/b/windowsazurestorage/>

  [Azure SDK for PHP]: http://go.microsoft.com/fwlink/?LinkID=252473
  [次のステップ]: #NextSteps
  [テーブル サービスとは]: #what-is
  [概念]: #concepts
  [Azure のストレージ アカウントの作成]: #CreateAccount
  [PHP アプリケーションの作成]: #CreateApplication
  [テーブル サービスにアクセスするようにアプリケーションを構成する]: #ConfigureStorage
  [Azure のストレージ接続文字列の設定]: #ConnectionString
  [方法: テーブルの作成]: #CreateTable
  [方法: エンティティをテーブルに追加する]: #AddEntity
  [方法: 単一のエンティティを取得する]: #RetrieveEntity
  [方法: パーティション内のすべてのエンティティを取得する]: #RetEntitiesInPartition
  [方法: パーティション内のエンティティのサブセットを取得する]: #RetrieveSubset
  [方法: エンティティ プロパティのサブセットを取得する]: #RetPropertiesSubset
  [方法: エンティティを更新する]: #UpdateEntity
  [方法: バッチ テーブル処理]: #BatchOperations
  [方法: テーブルを削除する]: #DeleteTable
  [テーブル サービス操作のサーバー タイムアウトの設定]: http://msdn.microsoft.com/ja-jp/library/windowsazure/dd894042.aspx
  [テーブル サービス データ モデルについて]: http://msdn.microsoft.com/ja-jp/library/windowsazure/dd179338.aspx
  [テーブルおよびエンティティのクエリ]: http://msdn.microsoft.com/ja-jp/library/windowsazure/dd894031.aspx
  [エンティティ グループ トランザクションの実行]: http://msdn.microsoft.com/ja-jp/library/windowsazure/dd894038.aspx
  [Azure のデータの格納とアクセス]: http://msdn.microsoft.com/ja-jp/library/windowsazure/gg433040.aspx
