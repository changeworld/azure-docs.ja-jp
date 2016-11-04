---
title: ロジック アプリに Azure Blob Storage コネクタを追加する | Microsoft Docs
description: Azure Blob Storage コネクタと REST API パラメーターの概要
services: ''
documentationcenter: ''
author: MandiOhlinger
manager: erikre
editor: ''
tags: connectors

ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/25/2016
ms.author: mandia

---
# Azure Blob Storage コネクタの概要
Azure Blob Storage は、大量の非構造化データを格納するためのサービスです。Azure Blob Storage での BLOB のアップロード、更新、取得、削除など、多様なアクションを 実行できます。

Azure Blob Storage では、次の操作を実行できます。

* 新しいプロジェクトをアップロードするか、最近更新されたファイルを取得して、ワークフローを構築します。
* ファイルのメタデータの取得、ファイルの削除、ファイルのコピーなどのアクションを使用します。たとえば、Azure Web サイトでツールが更新されると (トリガー)、Blob Storage でファイルを更新します (アクション)。

このトピックでは、ロジック アプリ内で Blob Storage コネクタを使用する方法を説明し、アクションの一覧を示します。

> [!NOTE]
> 本記事は、一般公開された Logic Apps の一般公開 (GA) を対象としています。
> 
> 

Logic Apps の詳細については、「[Logic Apps とは](../app-service-logic/app-service-logic-what-are-logic-apps.md)」および[ロジック アプリの作成](../app-service-logic/app-service-logic-create-a-logic-app.md)に関するページをご覧ください。

## Azure Blob Storage に接続する
ロジック アプリから任意のサービスにアクセスできるようにするには、まず、そのサービスへの "*接続*" を作成します。接続により、ロジック アプリと別のサービスとの接続が実現します。たとえば、ストレージ アカウントに接続するには、最初に Blob Storage の "*接続*" を作成します。接続を作成するには、接続対象のサービスへのアクセスに通常使用する資格情報を入力します。そのため、Azure Storage の場合は、ストレージ アカウントの資格情報を入力して接続を作成します。

#### 接続の作成
> [!INCLUDE [Azure Blob Storage への接続を作成する](../../includes/connectors-create-api-azureblobstorage.md)]
> 
> 

## トリガーを使用する
このコネクタにはトリガーがありません。定期実行のトリガー、HTTP Webhook トリガー、他のコネクタで使用可能なトリガーなど、他のトリガーを使用してロジック アプリを起動します。[ロジック アプリの作成](../app-service-logic/app-service-logic-create-a-logic-app.md)に関する記事に例が記載されています。

## アクションを使用する
アクションとは、ロジック アプリで定義されたワークフローによって実行される操作です。

1. プラス記号を選択します。**[アクションの追加]**、**[条件の追加]**、**[More (その他)]** のいずれかのオプションという複数の選択肢があります。
   
    ![](./media/connectors-create-api-azureblobstorage/add-action.png)
2. **[アクションの追加]** を選択します。
3. テキスト ボックスに「blob」と入力して、使用可能なすべてのアクションの一覧を取得します。
   
    ![](./media/connectors-create-api-azureblobstorage/actions.png)
4. この例では、**[AzureBlob - Get file metadata using path (AzureBlob - パスを使用してファイルのメタデータを取得する)]** を選択します。接続が既に存在する場合は、**[...]** (表示ピッカー) ボタンを使用してファイルを選択します。
   
    ![](./media/connectors-create-api-azureblobstorage/sample-file.png)
   
    接続情報の入力を求められたら、詳細を入力して接続を作成します。これらのプロパティについては、このトピックの「[接続の作成](connectors-create-api-azureblobstorage.md#create-the-connection)」をご覧ください。
   
   > [!NOTE]
   > この例では、ファイルのメタデータを取得します。メタデータを表示するには、別のコネクタを使用して新しいファイルを作成する別のアクションを追加します。たとえば、メタデータに基づいて新しい "test" ファイルを作成する OneDrive アクションを追加します。
   > 
   > 
5. ツール バーの左上隅にある **[保存]** を選択して変更を保存します。ロジック アプリが保存され、場合によっては、自動的に有効になります。

> [!TIP]
> [Storage Explorer](http://storageexplorer.com/) は、複数のストレージ アカウントを管理するための優れたツールです。
> 
> 

## 技術的な詳細
## ストレージ BLOB アクション
| アクション | 説明 |
| --- | --- |
| [ファイルのメタデータを取得する](connectors-create-api-azureblobstorage.md#get-file-metadata) |この操作では、ファイル ID を使用してファイルのメタデータを取得します。 |
| [ファイルを更新する](connectors-create-api-azureblobstorage.md#update-file) |この操作では、ファイルを更新します。 |
| [ファイルを削除する](connectors-create-api-azureblobstorage.md#delete-file) |この操作では、ファイルを削除します。 |
| [パスを使用してファイルのメタデータを取得する](connectors-create-api-azureblobstorage.md#get-file-metadata-using-path) |この操作では、パスを使用してファイルのメタデータを取得します。 |
| [パスを使用してファイルの内容を取得する](connectors-create-api-azureblobstorage.md#get-file-content-using-path) |この操作では、パスを使用してファイルの内容を取得します。 |
| [ファイルの内容を取得する](connectors-create-api-azureblobstorage.md#get-file-content) |この操作では、ID を使用してファイルの内容を取得します。 |
| [ファイルを作成する](connectors-create-api-azureblobstorage.md#create-file) |この操作では、ファイルをアップロードします。 |
| [ファイルをコピーする](connectors-create-api-azureblobstorage.md#copy-file) |この操作では、ファイルを Azure Blob Storage にコピーします。 |
| [アーカイブをフォルダーに抽出する](connectors-create-api-azureblobstorage.md#extract-archive-to-folder) |この操作では、フォルダーにアーカイブ ファイル (例: .zip) を抽出します。 |

### アクションの詳細
このセクションでは、必須または任意の入力プロパティ、コネクタに関連付けられた対応する出力など、各アクションに関する具体的な詳細について説明します。

#### ファイルのメタデータを取得する
この操作では、ファイル ID を使用してファイルのメタデータを取得します。

| プロパティ名 | Displayname Settings | 説明 |
| --- | --- | --- |
| id* |ファイル |ファイルを選択する |

アスタリスク (*) は、そのプロパティが必須であることを意味します。

##### 出力の詳細
BlobMetadata

| プロパティ名 | データ型 |
| --- | --- |
| ID |string |
| 名前 |string |
| DisplayName |string |
| パス |string |
| LastModified |string |
| サイズ |integer |
| MediaType |string |
| IsFolder |ブール値 |
| ETag |string |
| FileLocator |string |

#### ファイルを更新する
この操作では、ファイルを更新します。

| プロパティ名 | Displayname Settings | 説明 |
| --- | --- | --- |
| id* |ファイル |ファイルを選択する |
| body* |ファイルのコンテンツ |更新するファイルの内容 |

アスタリスク (*) は、そのプロパティが必須であることを意味します。

##### 出力の詳細
BlobMetadata

| プロパティ名 | データ型 |
| --- | --- |
| ID |string |
| 名前 |string |
| DisplayName |string |
| パス |string |
| LastModified |string |
| サイズ |integer |
| MediaType |string |
| IsFolder |ブール値 |
| ETag |string |
| FileLocator |string |

#### ファイルを削除する
この操作では、ファイルを削除します。

| プロパティ名 | Displayname Settings | 説明 |
| --- | --- | --- |
| id* |ファイル |ファイルを選択する |

アスタリスク (*) は、そのプロパティが必須であることを意味します。

##### 出力の詳細
ありません。

#### パスを使用してファイルのメタデータを取得する
この操作では、パスを使用してファイルのメタデータを取得します。

| プロパティ名 | Displayname Settings | 説明 |
| --- | --- | --- |
| path* |ファイル パス |ファイルを選択する |

アスタリスク (*) は、そのプロパティが必須であることを意味します。

##### 出力の詳細
BlobMetadata

| プロパティ名 | データ型 |
| --- | --- |
| ID |string |
| 名前 |string |
| DisplayName |string |
| パス |string |
| LastModified |string |
| サイズ |integer |
| MediaType |string |
| IsFolder |ブール値 |
| ETag |string |
| FileLocator |文字列 |

#### パスを使用してファイルの内容を取得する
この操作では、パスを使用してファイルの内容を取得します。

| プロパティ名 | Displayname Settings | 説明 |
| --- | --- | --- |
| path* |ファイル パス |ファイルを選択する |

アスタリスク (*) は、そのプロパティが必須であることを意味します。

##### 出力の詳細
ありません。

#### ファイルの内容を取得する
この操作では、ID を使用してファイルの内容を取得します。

| プロパティ名 | データ型 | 説明 |
| --- | --- | --- |
| id* |string |ファイルを選択する |

アスタリスク (*) は、そのプロパティが必須であることを意味します。

##### 出力の詳細
ありません。

#### ファイルを作成する
この操作では、ファイルをアップロードします。

| プロパティ名 | Displayname Settings | 説明 |
| --- | --- | --- |
| folderPath* |フォルダー パス |フォルダーを選択する |
| name* |ファイル名 |アップロードするファイルの名前 |
| body* |ファイルのコンテンツ |アップロードするファイルの内容 |

アスタリスク (*) は、そのプロパティが必須であることを意味します。

##### 出力の詳細
BlobMetadata

| プロパティ名 | データ型 |
| --- | --- |
| ID |string |
| 名前 |string |
| DisplayName |string |
| パス |string |
| LastModified |string |
| サイズ |integer |
| MediaType |string |
| IsFolder |ブール値 |
| ETag |string |
| FileLocator |string |

#### ファイルをコピーする
この操作では、ファイルを Azure Blob Storage にコピーします。

| プロパティ名 | Displayname Settings | 説明 |
| --- | --- | --- |
| source* |Source url (コピー元 URL) |コピー元ファイルの URL を指定する |
| destination* |Destination file path (コピー先ファイル パス) |対象ファイル名を含む、コピー先ファイル パスを指定する |
| overwrite |Overwrite? (上書きを許可) |既存の抽出先ファイルを上書きするかどうかを指定する (true/false) |

アスタリスク (*) は、そのプロパティが必須であることを意味します。

##### 出力の詳細
BlobMetadata

| プロパティ名 | データ型 |
| --- | --- |
| ID |string |
| 名前 |string |
| DisplayName |string |
| パス |string |
| LastModified |string |
| サイズ |integer |
| MediaType |string |
| IsFolder |ブール値 |
| ETag |string |
| FileLocator |string |

#### アーカイブをフォルダーに抽出する
この操作では、フォルダーにアーカイブ ファイル (例: .zip) を抽出します。

| プロパティ名 | Displayname Settings | 説明 |
| --- | --- | --- |
| source* |Source archive file path (ソース アーカイブ ファイルのパス) |アーカイブ ファイルを選択する |
| destination* |Destination folder path (抽出先フォルダー パス) |抽出する内容を選択する |
| overwrite |Overwrite? (上書きを許可) |既存の抽出先ファイルを上書きするかどうかを指定する (true/false) |

アスタリスク (*) は、そのプロパティが必須であることを意味します。

##### 出力の詳細
BlobMetadata

| プロパティ名 | データ型 |
| --- | --- |
| ID |string |
| 名前 |string |
| DisplayName |string |
| パス |string |
| LastModified |string |
| サイズ |integer |
| MediaType |string |
| IsFolder |ブール値 |
| ETag |string |
| FileLocator |文字列 |

## HTTP 応答
他のアクションを呼び出すとき、特定の応答を受け取る場合があります。次の表に、これらの応答とその説明を示します。

| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| 202 |承認済み |
| 400 |正しくない要求 |
| 401 |権限がありません |
| 403 |許可されていません |
| 404 |見つかりません |
| 500 |内部サーバー エラー。不明なエラーが発生しました |
| default |操作に失敗しました。 |

## 次のステップ
[ロジック アプリを作成します](../app-service-logic/app-service-logic-create-a-logic-app.md)。[API の一覧](apis-list.md)で、Logic Apps で使用できる他のコネクタを確認してください。

<!---HONumber=AcomDC_0727_2016-->