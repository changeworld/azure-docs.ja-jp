<properties linkid="develop-python-table-service" UrlDisplayName="テーブル サービス" pageTitle="テーブル ストレージの使用方法 (Python) - Windows Azure の機能ガイド" MetaKeywords="Azure テーブル Python, テーブルの作成 Azure, テーブルの削除 Azure, テーブルの挿入 Azure, テーブルの照会 Azure" description="Python からテーブル サービスを使用して、テーブルを作成および削除する方法、テーブルのエンティティを挿入、削除、照会する方法について説明します。" metaCanonical="" services="storage" documentationCenter="Python" title="Python からテーブル ストレージ サービスを使用する方法" authors=""  solutions="" writer="" manager="" editor=""  />





# Python からテーブル ストレージ サービスを使用する方法
このガイドでは、Windows Azure テーブル ストレージ サービスを使用して一般的な
シナリオを実行する方法について説明します。サンプルは Python API を使用して記述されて
います。紹介するシナリオは、**テーブルの作成と削除、テーブルの
エンティティの挿入とクエリ実行**などです。テーブルの
詳細については、「[次のステップ][]」のセクションを参照してください。

## 目次

[テーブル サービスとは][]   
 [概念][]   
 [Windows Azure ストレージ アカウントの作成][]   
 [テーブルの作成方法][]   
 [エンティティをテーブルに追加する方法][]   
 [エンティティを更新する方法][]   
 [エンティティのグループを変更する方法][]   
 [エンティティを照会する方法][]   
 [エンティティのセットを照会する方法][]   
 [エンティティ プロパティのサブセットを照会する方法][]   
 [エンティティを削除する方法][]   
 [テーブルを削除する方法][]   
 [次のステップ][]   

[WACOM.INCLUDE [howto-table-storage](../includes/howto-table-storage.md)]

## <a name="create-account"> </a>Windows Azure ストレージ アカウントの作成
[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

**注:** Python またはクライアント ライブラリをインストールする場合は、「[Python Installation Guide (Python インストール ガイド)](../python-how-to-install/)」を参照してください。


## <a name="create-table"> </a>テーブルの作成方法

**TableService** オブジェクトを使用して、テーブル サービスを操作できます。次の
コードでは、**TableService** オブジェクトを作成します。プログラムを使用して
Windows Azure のストレージにアクセスするすべての Python ファイルの先頭付近に、次のコードを追加します。

	from azure.storage import *

次のコードは、ストレージ アカウントの名前とアカウント キーを使用して、**TableService** オブジェクトを作成します。"myaccount" と "mykey" の部分は、実際のアカウントとキーに置き換えます。

	table_service = TableService(account_name='myaccount', account_key='mykey')

	table_service.create_table('tasktable')

## <a name="add-entity"> </a>エンティティをテーブルに追加する方法

エンティティを追加するには、最初に、エンティティのプロパティ名と
値を定義するディクショナリを作成します。すべてのエンティティについて、
**PartitionKey** と **RowKey** を指定する必要があることに注意してください。これらはエンティティの
一意の識別子であり、他のエンティティのプロパティよりはるかに高速に
照会できる値です。システムでは **PartitionKey** が
使用されて多くのストレージ ノードにテーブルのエンティティが自動的に配布されます。
**PartitionKey** が同じエンティティは同じノードで格納されています。**RowKey** は、
エンティティが属するパーティション内のエンティティの一意の ID
です。

エンティティをテーブルに追加するには、ディクショナリ
オブジェクトを **insert\_entity** メソッドに渡します。

	task = {'PartitionKey': 'tasksSeattle', 'RowKey': '1', 'description' : 'Take out the trash', 'priority' : 200}
	table_service.insert_entity('tasktable', task)

**Entity** クラスのインスタンスを **insert\_entity** メソッドに渡すこともできます。

	task = Entity()
	task.PartitionKey = 'tasksSeattle'
	task.RowKey = '2'
	task.description = 'Wash the car'
	task.priority = 100
	table_service.insert_entity('tasktable', task)

## <a name="update-entity"> </a>エンティティを更新する方法

次のコードは、既存のエンティティの以前のバージョンを更新された
バージョンに置き換える方法を示しています。

	task = {'description' : 'Take out the garbage', 'priority' : 250}
	table_service.update_entity('tasktable', 'tasksSeattle', '1', task)

更新されるエンティティが存在しない場合、更新操作は失敗します。
既に存在しているかどうかに関係なく
エンティティを格納するには、**insert\_or\_replace_entity** を使用します。
次の例では、最初の呼び出しで既存のエンティティを置き換えます。2 番目の呼び出しでは、指定された **PartitionKey** と **RowKey** を持つエンティティがテーブル内に存在しないため、新しいエンティティが挿入されます。

	task = {'description' : 'Take out the garbage again', 'priority' : 250}
	table_service.insert_or_replace_entity('tasktable', 'tasksSeattle', '1', task)

	task = {'description' : 'Buy detergent', 'priority' : 300}
	table_service.insert_or_replace_entity('tasktable', 'tasksSeattle', '3', task)

## <a name="change-entities"> </a>エンティティのグループを変更する方法

状況によって、複数の操作をバッチとして送信し、サーバーによる
アトミック処理を行うことが合理的である場合があります。これを実現するには、
**TableService** の **begin\_batch** メソッドを使用した後、一連の操作を
通常どおり呼び出します。バッチを送信するときは、
**commit\_batch** を呼び出します。バッチでエンティティを変更するには、すべてのエンティティが同じパーティションに含まれている必要があります。次の例では、2 つのエンティティをバッチでまとめて追加します。

	task10 = {'PartitionKey': 'tasksSeattle', 'RowKey': '10', 'description' : 'Go grocery shopping', 'priority' : 400}
	task11 = {'PartitionKey': 'tasksSeattle', 'RowKey': '11', 'description' : 'Clean the bathroom', 'priority' : 100}
	table_service.begin_batch()
	table_service.insert_entity('tasktable', task10)
	table_service.insert_entity('tasktable', task11)
	table_service.commit_batch()

## <a name="query-for-entity"> </a>エンティティを照会する方法

テーブル内のエンティティを照会するには、**get\_entity** メソッドを
使用して、**PartitionKey** と **RowKey** を渡します。

	task = table_service.get_entity('tasktable', 'tasksSeattle', '1')
	print(task.description)
	print(task.priority)

## <a name="query-set-entities"> </a>エンティティのセットを照会する方法

次の例では、**PartitionKey** に基づいて、Seattle 内のすべてのタスクを検索します。

	tasks = table_service.query_entities('tasktable', "PartitionKey eq 'tasksSeattle'")
	for task in tasks:
		print(task.description)
		print(task.priority)

## <a name="query-entity-properties"> </a>エンティティ プロパティのサブセットを照会する方法

テーブルに対するクエリでは、ごくわずかのプロパティだけをエンティティから取得できます。
*プロジェクション*と呼ばれるこの方法では、帯域幅の使用が削減され、クエリの
パフォーマンスが向上します。特に、大きなエンティティがある場合に役立ちます。**select**
パラメーターを使用して、クライアントに提供するプロパティの名前を
渡します。

次のコードのクエリは、テーブル内のエンティティの**説明**だけを
返します。

*次のスニペットはクラウド ストレージ サービスに対してのみ機能します。
これはストレージ エミュレーターではサポートされていません。*

	tasks = table_service.query_entities('tasktable', "PartitionKey eq 'tasksSeattle'", 'description')
	for task in tasks:
		print(task.description)

## <a name="delete-entity"> </a>エンティティを削除する方法

パーティション キーと行キーを使用してエンティティを削除できます。

	table_service.delete_entity('tasktable', 'tasksSeattle', '1')

## <a name="delete-table"> </a>テーブルを削除する方法

次のコードは、ストレージ アカウントからテーブルを削除します。

	table_service.delete_table('tasktable')

## <a name="next-steps"> </a>次のステップ

これで、テーブル ストレージの基本を学習できました。さらに複雑なストレージ タスクを
実行する方法については、次のリンク先を参照してください。

-   MSDN リファレンス: [Windows Azure のデータの格納とアクセス][]
-   [Windows Azure ストレージ チーム ブログ][] (このページは英語の場合があります)

  [次のステップ]: #next-steps
[テーブル サービスとは]: #what-is
[概念]: #concepts
[Windows Azure ストレージ アカウントの作成]: #create-account
[テーブルの作成方法]: #create-table
[エンティティをテーブルに追加する方法]: #add-entity
[エンティティを更新する方法]: #update-entity
[エンティティのグループを変更する方法]: #change-entities
[エンティティを照会する方法]: #query-for-entity
[エンティティのセットを照会する方法]: #query-set-entities
[エンティティ プロパティのサブセットを照会する方法]: #query-entity-properties
[エンティティを削除する方法]: #delete-entity
[テーブルを削除する方法]: #delete-table
[Windows Azure のデータの格納とアクセス]: http://msdn.microsoft.com/en-us/library/windowsazure/gg433040.aspx
[Windows Azure ストレージ チーム ブログ]: http://blogs.msdn.com/b/windowsazurestorage/ (このページは英語の場合があります)

