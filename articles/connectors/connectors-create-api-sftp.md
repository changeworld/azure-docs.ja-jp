---
title: ロジック アプリで SFTP コネクタを使用する方法 | Microsoft Docs
description: Azure App Service を使用してロジック アプリを作成します。SFTP API に接続してファイルを送受信します。ファイルの作成、更新、取得、削除など、さまざまな操作を実行できます。
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: ''
tags: connectors

ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/20/2016
ms.author: deonhe

---
# SFTP コネクタの使用
SFTP コネクタを使用すると、SFTP アカウントにアクセスしてファイルを送受信できます。ファイルの作成、更新、取得、削除など、さまざまな操作を実行できます。

[任意のコネクタ](apis-list.md)を使用するには、まずロジック アプリを作成する必要があります。ロジック アプリの作成方法については、[こちら](../app-service-logic/app-service-logic-create-a-logic-app.md)をご覧ください。

## SFTP への接続
ロジック アプリから任意のサービスにアクセスできるようにするには、まず、そのサービスへの "接続" を作成する必要があります。[接続](connectors-overview.md)により、ロジック アプリと別のサービスとの接続が実現します。

### SFTP への接続を作成する
> [!INCLUDE [SFTP への接続を作成する手順](../../includes/connectors-create-api-sftp.md)]
> 
> 

## SFTP トリガーの使用
トリガーとは、ロジック アプリで定義されたワークフローの開始に使用できるイベントです。トリガーの詳細については、[こちら](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts)をご覧ください。

この例では、**[SFTP - When a file is added or modified (SFTP - ファイルの追加または変更時)]** トリガーを使用して、SFTP サーバー上でファイルが追加または変更されたときにロジック アプリ ワークフローを開始する方法について説明します。また、新しいファイルまたは変更されたファイルの内容をチェックする際の条件を追加し、ファイルの内容が、内容を使用する前にファイルを抽出する必要があることを示している場合にファイルの抽出を決定する方法についても説明します。最後に、ファイルの内容を抽出し、抽出した内容を SFTP サーバー上のフォルダーに配置するアクションを追加する方法を説明します。

企業での使用例として、このトリガーを使用して、SFTP フォルダーに顧客からの注文を表す新しいファイルがあるかどうかを監視できます。その後、**Get file content (ファイルの内容を取得する)** などの SFTP コネクタ アクションを使用して注文の内容を取得し、後続の処理や注文データベースへの格納を行うことができます。

> [!INCLUDE [SFTP トリガーを作成する手順](../../includes/connectors-create-api-sftp-trigger.md)]
> 
> 

## Add a condition
> [!INCLUDE [条件を追加する手順](../../includes/connectors-create-api-sftp-condition.md)]
> 
> 

## SFTP アクションの使用
アクションとは、ロジック アプリで定義されたワークフローによって実行される操作です。アクションの詳細については、[こちら](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts)をご覧ください。

> [!INCLUDE [SFTP アクションを作成する手順](../../includes/connectors-create-api-sftp-action.md)]
> 
> 

## 技術的な詳細
ここでは、この接続でサポートされるトリガー、アクション、応答について詳しく説明します。

## SFTP トリガー
SFTP には次のトリガーがあります。

| トリガー | 説明 |
| --- | --- |
| [When a file is added or modified (ファイルの追加または変更時)](connectors-create-api-sftp.md#when-a-file-is-added-or-modified) |この操作では、フォルダーでファイルが追加または変更されたときにフローをトリガーします。 |

## SFTP アクション
SFTP には次のアクションがあります。

| アクション | 説明 |
| --- | --- |
| [ファイルのメタデータを取得する](connectors-create-api-sftp.md#get-file-metadata) |この操作では、ファイル ID を使用してファイルのメタデータを取得します。 |
| [ファイルを更新する](connectors-create-api-sftp.md#update-file) |この操作では、ファイルの内容を更新します。 |
| [ファイルを削除する](connectors-create-api-sftp.md#delete-file) |この操作では、ファイルを削除します。 |
| [パスを使用してファイルのメタデータを取得する](connectors-create-api-sftp.md#get-file-metadata-using-path) |この操作では、ファイル パスを使用してファイルのメタデータを取得します。 |
| [パスを使用してファイルの内容を取得する](connectors-create-api-sftp.md#get-file-content-using-path) |この操作では、ファイル パスを使用してファイルの内容を取得します。 |
| [ファイルの内容を取得する](connectors-create-api-sftp.md#get-file-content) |この操作では、ファイル ID を使用してファイルの内容を取得します。 |
| [ファイルを作成する](connectors-create-api-sftp.md#create-file) |この操作では、SFTP サーバーにファイルをアップロードします。 |
| [ファイルをコピーする](connectors-create-api-sftp.md#copy-file) |この操作では、SFTP サーバーにファイルをコピーします。 |
| [フォルダー内のファイルを一覧表示する](connectors-create-api-sftp.md#list-files-in-folder) |この操作では、フォルダーに含まれているファイルを取得します。 |
| [ルート フォルダー内のファイルを一覧表示する](connectors-create-api-sftp.md#list-files-in-root-folder) |この操作では、ルート フォルダー内のファイルを取得します。 |
| [フォルダーを抽出する](connectors-create-api-sftp.md#extract-folder) |この操作では、フォルダーにアーカイブ ファイル (例: .zip) を抽出します。 |

### アクションの詳細
ここでは、このコネクタのアクションおよびトリガーとその応答について詳しく説明します。

### ファイルのメタデータを取得する
この操作では、ファイル ID を使用してファイルのメタデータを取得します。

| プロパティ名 | Displayname Settings | 説明 |
| --- | --- | --- |
| id* |ファイル |ファイルを指定します |

* は、必須のプロパティを示します。

#### 出力の詳細
BlobMetadata

| プロパティ名 | データ型 |
| --- | --- | --- |
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

### ファイルを更新する
この操作では、ファイルの内容を更新します。

| プロパティ名 | Displayname Settings | 説明 |
| --- | --- | --- |
| id* |ファイル |ファイルを指定します |
| body* |ファイルのコンテンツ |更新するファイルの内容 |

* は、必須のプロパティを示します。

#### 出力の詳細
BlobMetadata

| プロパティ名 | データ型 |
| --- | --- | --- |
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

### ファイルを削除する
この操作では、ファイルを削除します。

| プロパティ名 | Displayname Settings | 説明 |
| --- | --- | --- |
| id* |ファイル |ファイルを指定します |

* は、必須のプロパティを示します。

### パスを使用してファイルのメタデータを取得する
この操作では、ファイル パスを使用してファイルのメタデータを取得します。

| プロパティ名 | Displayname Settings | 説明 |
| --- | --- | --- |
| path* |ファイル パス |ファイルの一意のパス |

* は、必須のプロパティを示します。

#### 出力の詳細
BlobMetadata

| プロパティ名 | データ型 |
| --- | --- | --- |
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

### パスを使用してファイルの内容を取得する
この操作では、ファイル パスを使用してファイルの内容を取得します。

| プロパティ名 | Displayname Settings | 説明 |
| --- | --- | --- |
| path* |ファイル パス |ファイルの一意のパス |

* は、必須のプロパティを示します。

### ファイルの内容を取得する
この操作では、ファイル ID を使用してファイルの内容を取得します。

| プロパティ名 | Displayname Settings | 説明 |
| --- | --- | --- |
| id* |ファイル |ファイルを指定します |

* は、必須のプロパティを示します。

### ファイルを作成する
この操作では、SFTP サーバーにファイルをアップロードします。

| プロパティ名 | Displayname Settings | 説明 |
| --- | --- | --- |
| folderPath* |フォルダー パス |フォルダーの一意のパス |
| name* |ファイル名 |ファイルの名前 |
| body* |ファイルのコンテンツ |作成するファイルの内容 |

* は、必須のプロパティを示します。

#### 出力の詳細
BlobMetadata

|  | プロパティ名 | データ型 |
| --- | --- | --- |
| ID |string | |
| 名前 |string | |
| DisplayName |string | |
| パス |string | |
| LastModified |string | |
| サイズ |integer | |
| MediaType |string | |
| IsFolder |ブール値 | |
| ETag |string | |
| FileLocator |string | |

### ファイルをコピーする
この操作では、SFTP サーバーにファイルをコピーします。

| プロパティ名 | Displayname Settings | 説明 |
| --- | --- | --- |
| source* |Source file path (コピー元ファイル パス) |ソース ファイルのパス |
| destination* |Destination file path (コピー先ファイル パス) |ファイル名を含む、宛先ファイルのパス |
| overwrite |Overwrite? (上書きを許可) |’true’ に設定すると、宛先ファイルが上書きされます |

* は、必須のプロパティを示します。

#### 出力の詳細
BlobMetadata

| プロパティ名 | データ型 |
| --- | --- | --- |
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

### When a file is added or modified (ファイルの追加または変更時)
この操作では、フォルダーでファイルが追加または変更されたときにフローをトリガーします。

| プロパティ名 | Displayname Settings | 説明 |
| --- | --- | --- |
| folderId* |フォルダー |フォルダーを指定します |

* は、必須のプロパティを示します。

### フォルダー内のファイルを一覧表示する
この操作では、フォルダーに含まれているファイルを取得します。

| プロパティ名 | Displayname Settings | 説明 |
| --- | --- | --- |
| id* |フォルダー |フォルダーを指定します |

* は、必須のプロパティを示します。

#### 出力の詳細
BlobMetadata

| プロパティ名 | データ型 |
| --- | --- | --- |
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

### ルート フォルダー内のファイルを一覧表示する
この操作では、ルート フォルダー内のファイルを取得します。

この呼び出しには、パラメーターはありません

#### 出力の詳細
BlobMetadata

| プロパティ名 | データ型 |
| --- | --- | --- |
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

### フォルダーを抽出する
この操作では、フォルダーにアーカイブ ファイル (例: .zip) を抽出します。

| プロパティ名 | Displayname Settings | 説明 |
| --- | --- | --- |
| source* |Source archive file path (ソース アーカイブ ファイルのパス) |アーカイブ ファイルのパス |
| destination* |Destination folder path (抽出先フォルダー パス) |宛先フォルダーのパス |
| overwrite |Overwrite? (上書きを許可) |’true’ に設定すると、宛先ファイルが上書きされます |

* は、必須のプロパティを示します。

#### 出力の詳細
BlobMetadata

| プロパティ名 | データ型 |
| --- | --- | --- |
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
上記のアクションとトリガーは、次の HTTP 状態コードを 1 つ以上返す場合があります。

| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| 202 |承認済み |
| 400 |正しくない要求 |
| 401 |権限がありません |
| 403 |許可されていません |
| 404 |見つかりません |
| 500 |内部サーバー エラー。不明なエラーが発生しました。 |
| default |操作に失敗しました。 |

## 次のステップ
[ロジック アプリを作成します](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!----HONumber=AcomDC_0803_2016-->
