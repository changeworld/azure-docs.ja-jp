<properties
	pageTitle="Python から Table ストレージを使用する方法 | Microsoft Azure"
	description="Python から Table サービスを使用して、テーブルを作成、削除する方法、テーブルのエンティティを挿入、照会する方法について説明します。"
	services="storage"
	documentationCenter="python"
	authors="emgerner-msft"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="python"
	ms.topic="article"
	ms.date="12/11/2015"
	ms.author="emgerner"/>


# Python から Table ストレージを使用する方法

[AZURE.INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]

## 概要

このガイドでは、Azure Table ストレージ サービスを使用して一般的なシナリオを実行する方法について説明します。サンプルは Python で記述され、[Python Azure Storage パッケージ][]を使用しています。紹介するシナリオは、テーブルのエンティティの挿入とクエリ実行のほか、テーブルの作成と削除などがあります。

[AZURE.INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

[AZURE.NOTE]Python または [Python Azure パッケージ][]をインストールする場合は、「[Python インストール ガイド](../python-how-to-install.md)」をご覧ください。


## テーブルの作成

**TableService** オブジェクトを使用して、テーブル サービスを操作できます。次のコードでは、**TableService** オブジェクトを作成します。プログラムを使用して Azure Storage にアクセスするすべての Python ファイルの先頭付近に、コードを追加します。

	from azure.storage.table import TableService, Entity

次のコードでは、ストレージ アカウント名とアカウント キーを使用して **TableService** オブジェクトを作成します。'myaccount' と 'mykey' の部分は、実際のアカウントとキーに置き換えてください。

	table_service = TableService(account_name='myaccount', account_key='mykey')

	table_service.create_table('tasktable')

## エンティティをテーブルに追加する

エンティティを追加するには、最初に、エンティティのプロパティ名と値を定義するディクショナリを作成します。すべてのエンティティについて、**PartitionKey** と **RowKey** を指定する必要があることに注意してください。これらは、エンティティの一意の識別子になります。これらの値は、その他のプロパティをクエリするよりもはるかに高速にクエリできます。システムでは **PartitionKey** が使用されて多くのストレージ ノードにテーブル エンティティが自動的に配布されます。**PartitionKey** が同じエンティティは同じノードに格納されます。**RowKey** は、エンティティが属するパーティション内のエンティティの一意の ID です。

エンティティをテーブルに追加するには、ディクショナリ オブジェクトを **insert\_entity** メソッドに渡します。

	task = {'PartitionKey': 'tasksSeattle', 'RowKey': '1', 'description' : 'Take out the trash', 'priority' : 200}
	table_service.insert_entity('tasktable', task)

**Entity** クラスのインスタンスを **insert\_entity** メソッドに渡すこともできます。

	task = Entity()
	task.PartitionKey = 'tasksSeattle'
	task.RowKey = '2'
	task.description = 'Wash the car'
	task.priority = 100
	table_service.insert_entity('tasktable', task)

## エンティティを更新する

次のコードは、既存のエンティティの以前のバージョンを更新されたバージョンに置き換える方法を示しています。

	task = {'description' : 'Take out the garbage', 'priority' : 250}
	table_service.update_entity('tasktable', 'tasksSeattle', '1', task)

更新されるエンティティが存在しない場合、更新操作は失敗します。存在しているかどうかに関係なくエンティティを格納するには、**insert\_or\_replace\_entity** を使用します。次の例では、最初の呼び出しで既存のエンティティを置き換えます。2 番目の呼び出しでは、指定された **PartitionKey** と **RowKey** を持つエンティティがテーブル内に存在しないため、新しいエンティティが挿入されます。

	task = {'description' : 'Take out the garbage again', 'priority' : 250}
	table_service.insert_or_replace_entity('tasktable', 'tasksSeattle', '1', task)

	task = {'description' : 'Buy detergent', 'priority' : 300}
	table_service.insert_or_replace_entity('tasktable', 'tasksSeattle', '3', task)

## エンティティのグループを変更する

状況によって、複数の操作をバッチとして送信し、サーバーによるアトミック処理を行うことが合理的である場合があります。これを実現するには、**TableService** の **begin\_batch** メソッドを使用した後、一連の操作を通常どおり呼び出します。バッチを送信するときは、**commit\_batch** を呼び出します。バッチでエンティティを変更するには、すべてのエンティティが同じパーティションに含まれている必要があります。次の例では、2 つのエンティティをバッチでまとめて追加します。

	task10 = {'PartitionKey': 'tasksSeattle', 'RowKey': '10', 'description' : 'Go grocery shopping', 'priority' : 400}
	task11 = {'PartitionKey': 'tasksSeattle', 'RowKey': '11', 'description' : 'Clean the bathroom', 'priority' : 100}
	table_service.begin_batch()
	table_service.insert_entity('tasktable', task10)
	table_service.insert_entity('tasktable', task11)
	table_service.commit_batch()

## エンティティを照会する

テーブル内のエンティティを照会するには、**get\_entity** メソッドを使用して、**PartitionKey** と **RowKey** を渡します。

	task = table_service.get_entity('tasktable', 'tasksSeattle', '1')
	print(task.description)
	print(task.priority)

## エンティティのセットを照会する

次の例では、**PartitionKey** に基づいて、Seattle 内のすべてのタスクを検索します。

	tasks = table_service.query_entities('tasktable', "PartitionKey eq 'tasksSeattle'")
	for task in tasks:
		print(task.description)
		print(task.priority)

## エンティティ プロパティのサブセットを照会する

テーブルに対するクエリでは、ごくわずかのプロパティだけをエンティティから取得できます。*プロジェクション*と呼ばれるこの方法では、帯域幅の使用が削減され、クエリのパフォーマンスが向上します。特に、大量のエンティティがある場合に役立ちます。**select** パラメーターを使用して、クライアントに提供するプロパティの名前を渡します。

次のコードのクエリは、テーブル内のエンティティの説明だけを返します。

[AZURE.NOTE]次のスニペットは、クラウド ストレージ サービスに対してのみ機能します。これはストレージ エミュレーターではサポートされません。

	tasks = table_service.query_entities('tasktable', "PartitionKey eq 'tasksSeattle'", 'description')
	for task in tasks:
		print(task.description)

## エンティティを削除する

パーティション キーと行キーを使用してエンティティを削除できます。

	table_service.delete_entity('tasktable', 'tasksSeattle', '1')

## テーブルを削除する

次のコードは、ストレージ アカウントからテーブルを削除します。

	table_service.delete_table('tasktable')

## 次のステップ

これで、テーブル ストレージの基本を学習できました。さらに複雑なストレージ タスクについては、次のリンク先をご覧ください。

-   MSDN リファレンス [Azure Storage][] を参照してください。
-   [Azure Storage チームのブログ][]

詳細については、[Python デベロッパー センター](/develop/python/)も参照してください。

[Azure Storage チームのブログ]: http://blogs.msdn.com/b/windowsazurestorage/
[Python Azure パッケージ]: https://pypi.python.org/pypi/azure
[Python Azure Storage パッケージ]: https://pypi.python.org/pypi/azure-storage

<!---HONumber=AcomDC_1217_2015-->