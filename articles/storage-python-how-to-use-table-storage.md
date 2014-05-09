<properties linkid="develop-python-table-service" urlDisplayName="テーブル サービス" pageTitle="テーブル ストレージの使用方法 (Python) | Microsoft Azure" metaKeywords="Azure テーブル Python, テーブルの作成 Azure, テーブルの削除 Azure, テーブルの挿入 Azure, テーブルの照会 Azure" description="Python からテーブル サービスを使用して、テーブルを作成および削除する方法、テーブルのエンティティを挿入、削除、照会する方法について説明します。" metaCanonical="" services="storage" documentationCenter="Python" title="Python からテーブル ストレージ サービスを使用する方法" authors="" solutions="" manager="" editor="" />





# Python からテーブル ストレージ サービスを使用する方法
このガイドでは、Azure テーブル ストレージ サービスを使用して一般的な
シナリオを実行する方法について説明します。サンプルは Python API を使用して記述
されています。紹介するシナリオは、**テーブルの作成と削除、
テーブルのエンティティの挿入とクエリ実行**などです。テーブルの
詳細については、「[次のステップ][]」のセクションを参照してください。

## 目次

[テーブル サービスとは][]
 [概念][]   
 [Azure ストレージ アカウントの作成][]   
 [方法: テーブルを作成する][]   
 [方法: エンティティをテーブルに追加する][]   
 [方法: エンティティを更新する][]   
 [方法: エンティティのグループを変更する][]   
 [方法: エンティティを照会する][]   
 [方法: エンティティのセットを照会する][]   
 [方法: エンティティ プロパティのサブセットを照会する][]   
 [方法: エンティティを削除する][]   
 [方法: テーブルを削除する][]   
 [次の手順][]

[WACOM.INCLUDE [howto-table-storage](../includes/howto-table-storage.md)]

## <a name="create-account"> </a>Azure ストレージ アカウントの作成
[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

**注:** Python またはクライアント ライブラリをインストールする必要がある場合は、「[Python Installation Guide (Python インストール ガイド)](../python-how-to-install/).」を参照してください。


## <a name="create-table"> </a>方法: テーブルを作成する

**TableService** オブジェクトを使用して、テーブル サービスを操作できます。次の
コードでは、**TableService** オブジェクトを作成します。プログラムを使用して 
Azure ストレージにアクセスするすべての Python ファイルの先頭付近に、次のコードを追加します。

	from azure.storage import *

次のコードでは、ストレージ アカウント名とアカウント キーを使用して **TableService** オブジェクトを作成します。'myaccount' と 'mykey' の部分は、実際のアカウントとキーに置き換えてください。

	table_service = TableService(account_name='myaccount', account_key='mykey')

	table_service.create_table('tasktable')

## <a name="add-entity"> </a>方法: エンティティをテーブルに追加する

エンティティを追加するには、最初に、エンティティのプロパティ名と値を
定義するディクショナリを作成します。すべてのエンティティについて、**PartitionKey** と 
**RowKey** を指定する必要があることに注意してください。これらはエンティティの
一意の識別子であり、他のエンティティのプロパティよりはるかに高速に
照会できる値です。システムでは、テーブル エンティティを多数の
ストレージ ノードに自動的に配布するために **PartitionKey** を使用します。
**PartitionKey** が同じエンティティは同じノードに格納されます。
**RowKey** は、エンティティが属するパーティション内のエンティティの
一意の ID です。

エンティティをテーブルに追加するには、ディクショナリ オブジェクトを 
**1insert\_entity** メソッドに渡します。

	task = {'PartitionKey': 'tasksSeattle', 'RowKey': '1', 'description' : 'Take out the trash', 'priority' : 200}
	table_service.insert_entity('tasktable', task)

**Entity** クラスのインスタンスを **insert\_entity** メソッドに渡すこともできます。

	task = Entity()
	task.PartitionKey = 'tasksSeattle'
	task.RowKey = '2'
	task.description = 'Wash the car'
	task.priority = 100
	table_service.insert_entity('tasktable', task)

## <a name="update-entity"> </a>方法: エンティティを更新する

次のコードは、既存のエンティティの以前のバージョンを更新されたバージョンに
置き換える方法を示しています。

	task = {'description' : 'Take out the garbage', 'priority' : 250}
	table_service.update_entity('tasktable', 'tasksSeattle', '1', task)

更新されるエンティティが存在しない場合、更新操作は
失敗します。既に存在しているかどうかに
関係なくエンティティを格納するには、**insert\_or\_replace_entity** を使用します。
次の例では、最初の呼び出しで既存のエンティティを置き換えます。2 番目の呼び出しでは、指定された **PartitionKey** と **RowKey** を持つエンティティがテーブル内に存在しないため、新しいエンティティが挿入されます。

	task = {'description' : 'Take out the garbage again', 'priority' : 250}
	table_service.insert_or_replace_entity('tasktable', 'tasksSeattle', '1', task)

	task = {'description' : 'Buy detergent', 'priority' : 300}
	table_service.insert_or_replace_entity('tasktable', 'tasksSeattle', '3', task)

## <a name="change-entities"> </a>方法: エンティティのグループを変更する

状況によって、複数の操作をバッチとして送信し、サーバーによる
アトミック処理を行うことが合理的である場合があります。これを実現するには、
**TableService** の **begin\_batch** メソッドを使用した後、一連の
操作を通常どおり呼び出します。バッチを送信するときは、
**commit\_batch** を呼び出します。バッチでエンティティを変更するには、すべてのエンティティが同じパーティションに含まれている必要があります。次の例では、2 つのエンティティをバッチでまとめて追加します。

	task10 = {'PartitionKey': 'tasksSeattle', 'RowKey': '10', 'description' : 'Go grocery shopping', 'priority' : 400}
	task11 = {'PartitionKey': 'tasksSeattle', 'RowKey': '11', 'description' : 'Clean the bathroom', 'priority' : 100}
	table_service.begin_batch()
	table_service.insert_entity('tasktable', task10)
	table_service.insert_entity('tasktable', task11)
	table_service.commit_batch()

## <a name="query-for-entity"> </a>方法: エンティティを照会する

テーブル内のエンティティを照会するには、**get\_entity** メソッドを
使用して、**PartitionKey** と **RowKey** を渡します。

	task = table_service.get_entity('tasktable', 'tasksSeattle', '1')
	print(task.description)
	print(task.priority)

## <a name="query-set-entities"> </a>方法: エンティティのセットを照会する

次の例では、**PartitionKey** に基づいて、Seattle 内のすべてのタスクを検索します。

	tasks = table_service.query_entities('tasktable', "PartitionKey eq 'tasksSeattle'")
	for task in tasks:
		print(task.description)
		print(task.priority)

## <a name="query-entity-properties"> </a>方法: エンティティ プロパティのサブセットを照会する

テーブルに対するクエリでは、ごくわずかのプロパティだけをエンティティから取得できます。
プロジェクション**と呼ばれるこの方法では、帯域幅の使用が削減され、クエリの
パフォーマンスが向上します。特に、大量のエンティティがある場合に役立ちます。**select** 
パラメーターを使用して、クライアントに提供するプロパティの名前を
渡します。

次のコードのクエリは、テーブル内のエンティティの**説明**だけを
返します。

*次のスニペットは、クラウド ストレージ サービスに対してのみ
機能します。これはストレージ エミュレーターではサポート
されません。*

	tasks = table_service.query_entities('tasktable', "PartitionKey eq 'tasksSeattle'", 'description')
	for task in tasks:
		print(task.description)

## <a name="delete-entity"> </a>方法: エンティティを削除する

パーティション キーと行キーを使用してエンティティを削除できます。

	table_service.delete_entity('tasktable', 'tasksSeattle', '1')

## <a name="delete-table"> </a>方法: テーブルを削除する

次のコードは、ストレージ アカウントからテーブルを削除します。

	table_service.delete_table('tasktable')

## <a name="next-steps"> </a>次のステップ

これで、テーブル ストレージの基本を学習できました。さらに複雑なストレージ タスクを
実行する方法については、次のリンク先を参照してください。

- MSDN リファレンス: [Azure のデータの格納とアクセス][]
-   [Azure のストレージ チーム ブログ][]

  [次の手順]: #next-steps
  [テーブル サービスとは]: #what-is
  [概念]: #concepts
  [Azure ストレージ アカウントの作成]: #create-account
  [方法: テーブルを作成する]: #create-table
  [方法: エンティティをテーブルに追加する]: #add-entity
  [方法: エンティティを更新する]: #update-entity
  [方法: エンティティのグループを変更する]: #change-entities
  [方法: エンティティを照会する]: #query-for-entity
  [方法: エンティティのセットを照会する]: #query-set-entities
  [方法: エンティティ プロパティのサブセットを照会する]: #query-entity-properties
  [方法: エンティティを削除する]: #delete-entity
  [方法: テーブルを削除する]: #delete-table
  [Azure のデータの格納とアクセス]: http://msdn.microsoft.com/ja-jp/library/windowsazure/gg433040.aspx
  [Azure のストレージ チーム ブログ]: http://blogs.msdn.com/b/windowsazurestorage/

