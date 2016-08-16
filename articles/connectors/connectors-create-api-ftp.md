<properties
pageTitle="ロジック アプリで FTP コネクタを使用する方法 | Microsoft Azure"
description="Azure App Service を使用してロジック アプリを作成します。FTP サーバーに接続して、ファイルを管理します。FTP サーバーのファイルのアップロード、更新、取得、削除など、多様なアクションを実行できます。"
services="logic-apps"	
documentationCenter=".net,nodejs,java" 	
authors="msftman"	
manager="erikre"	
editor=""
tags="connectors" />

<tags
ms.service="logic-apps"
ms.devlang="multiple"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="07/22/2016"
ms.author="deonhe"/>

# FTP コネクタの使用

FTP コネクタを使用して、FTP サーバー上のファイルを監視、管理、作成します。

[任意のコネクタ](./apis-list.md)を使用するには、まずロジック アプリを作成する必要があります。ロジック アプリの作成方法については、[こちら](../app-service-logic/app-service-logic-create-a-logic-app.md)をご覧ください。

## FTP に接続する

ロジック アプリから任意のサービスにアクセスできるようにするには、まず、そのサービスへの "*接続*" を作成する必要があります。[接続](./connectors-overview.md)により、ロジック アプリと別のサービスとの接続が実現します。

### FTP への接続を作成する

>[AZURE.INCLUDE [FTP への接続を作成する手順](../../includes/connectors-create-api-ftp.md)]

## FTP トリガーを使用する

トリガーとは、ロジック アプリで定義されたワークフローの開始に使用できるイベントです。[トリガーの詳細についてはこちらをご覧ください](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts)。

>[AZURE.IMPORTANT]FTP コネクタを使用するには、インターネットからアクセスできる、パッシブ モードで動作するように構成されている FTP サーバーが必要です。また、FTP コネクタは、**暗黙的 FTPS (FTP over SSL) に対応していません**。FTP コネクタは、明示的 FTPS (FTP over SSL) のみをサポートしています。

この例では、FTP サーバー上でファイルが追加または変更されたときに、**[FTP - When a file is added or modified (FTP - ファイルが追加または変更されたとき)]** トリガーによりロジック アプリ ワークフローを開始する方法について説明します。エンタープライズの例では、このトリガーを使用して、FTP フォルダーに顧客からの注文を表す新しいファイルがあるかどうかを監視できます。次に、**[Get file content (ファイルの内容を取得する)]** などの FTP コネクタ アクションを使用して注文の内容を取得し、後続の処理や注文データベースへの格納を行うことができます。

1. Logic Apps デザイナーの検索ボックスに「*ftp*」と入力し、**[FTP - When a file is added or modified (FTP - ファイルが追加または変更されたとき)]** トリガーを選択します。![FTP trigger image 1](./media/connectors-create-api-ftp/ftp-trigger-1.png)**[When a file is added or modified (ファイルが追加または変更されたとき)]** コントロールが開きます。![FTP trigger image 2](./media/connectors-create-api-ftp/ftp-trigger-2.png)
- コントロールの右側にある **[...]** を選択します。これによりフォルダー ピッカー コントロールが開きます。![FTP trigger image 3](./media/connectors-create-api-ftp/ftp-trigger-3.png)
- **[>]** (右矢印) を選択し、新しいファイルや変更されたファイルを監視するフォルダーを参照します。そのフォルダーを選択します。フォルダーが **[フォルダー]** コントロールに表示されます。![FTP trigger image 4](./media/connectors-create-api-ftp/ftp-trigger-4.png)


これで、特定の FTP フォルダーでファイルが変更または作成されたときにワークフローの他のトリガーおよびアクションの実行を開始するトリガーがロジック アプリに構成されました。

>[AZURE.NOTE]ロジック アプリを機能させるには、少なくとも 1 つのトリガーとアクションが含まれている必要があります。アクションを追加するには、次のセクションの手順に従います。



## FTP アクションを使用する

アクションとは、ロジック アプリで定義されたワークフローによって実行される操作です。[アクションの詳細についてはこちらをご覧ください](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts)。

トリガーを追加した後は、次の手順に従って、トリガーによって検出された新しいファイルまたは変更されたファイルの内容を取得するアクションを追加します。

1. **[+ 新しいステップ]** を選択して、FTP サーバー上のファイルの内容を取得するアクションを追加します。
- **[アクションの追加]** のリンクを選択します。![FTP action image 1](./media/connectors-create-api-ftp/ftp-action-1.png)
- 「*FTP*」と入力して、FTP に関連するすべてのアクションを検索します。
- FTP フォルダーで新しいファイルまたは変更されたファイルが検出されたときに実行するアクションとして **[FTP - Get file content (FTP - ファイルの内容を取得する)]** を選択します。![FTP action image 2](./media/connectors-create-api-ftp/ftp-action-2.png)**[Get file content (ファイルの内容を取得する)]** コントロールが開きます。**注**: ロジック アプリによる FTP サーバー アカウントへのアクセスをまだ承認していない場合は、承認を求められます。![FTP action image 3](./media/connectors-create-api-ftp/ftp-action-3.png)
- **[ファイル]** コントロール (**[ファイル]*** の下にある空白) を選択します。ここでは、FTP サーバーで検出された新しいファイルまたは変更されたファイルの各種プロパティを使用することができます。
- **[File content (ファイルの内容)]** オプションを選択します。![FTP action image 4](./media/connectors-create-api-ftp/ftp-action-4.png)
-  コントロールが更新され、FTP サーバー上の新しいファイルまたは変更されたファイルについて、**[FTP - Get file content (FTP - ファイルの内容を取得する)]** アクションによって "*ファイルの内容*" が取得されることが示されます。![FTP action image 5](./media/connectors-create-api-ftp/ftp-action-5.png)
- 作業内容を保存し、ファイルを FTP フォルダーに追加して、ワークフローをテストします。

これで、FTP サーバー上のフォルダーを監視し、FTP フォルダーで新しいファイルまたは変更されたファイルを検出したときにワークフローを開始するトリガーがロジック アプリに構成されました。

このロジック アプリには、新しいファイルまたは変更されたファイルの内容を取得するアクションも構成されています。

この段階で、別のアクション (たとえば、新しいファイルまたは変更されたファイルの内容を SQL データベース テーブルに挿入する [[SQL Server - insert row (SQL Server - 行を挿入する)]](./connectors-create-api-sqlazure.md#insert-row) アクション) を追加することができます。

## 技術的な詳細

ここでは、この接続でサポートされるトリガー、アクション、応答について詳しく説明します。

## FTP トリガー

FTP には、次のトリガーがあります。

|トリガー | 説明|
|--- | ---|
|[[When a file is added or modified (ファイルが追加または変更されたとき)]](connectors-create-api-ftp.md#when-a-file-is-added-or-modified)|この操作では、フォルダーでファイルが追加または変更されたときにフローをトリガーします。|


## FTP アクション

FTP には、次のアクションがあります。


|アクション|説明|
|--- | ---|
|[ファイルのメタデータを取得する](connectors-create-api-ftp.md#get-file-metadata)|この操作では、ファイルのメタデータを取得します。|
|[ファイルを更新する](connectors-create-api-ftp.md#update-file)|この操作では、ファイルを更新します。|
|[ファイルを削除する](connectors-create-api-ftp.md#delete-file)|この操作では、ファイルを削除します。|
|[パスを使用してファイルのメタデータを取得する](connectors-create-api-ftp.md#get-file-metadata-using-path)|この操作では、パスを使用してファイルのメタデータを取得します。|
|[パスを使用してファイルの内容を取得する](connectors-create-api-ftp.md#get-file-content-using-path)|この操作では、パスを使用してファイルの内容を取得します。|
|[ファイルの内容を取得する](connectors-create-api-ftp.md#get-file-content)|この操作では、ファイルの内容を取得します。|
|[ファイルを作成する](connectors-create-api-ftp.md#create-file)|この操作では、ファイルを作成します。|
|[ファイルをコピーする](connectors-create-api-ftp.md#copy-file)|この操作では、ファイルを FTP サーバーにコピーします。|
|[フォルダー内のファイルを一覧表示する](connectors-create-api-ftp.md#list-files-in-folder)|この操作では、フォルダー内のファイルとサブフォルダーの一覧を取得します。|
|[ルート フォルダー内のファイルを一覧表示する](connectors-create-api-ftp.md#list-files-in-root-folder)|この操作では、ルート フォルダー内のファイルとサブフォルダーの一覧を取得します。|
|[フォルダーを抽出する](connectors-create-api-ftp.md#extract-folder)|この操作では、フォルダーにアーカイブ ファイル (例: .zip) を抽出します。|
### アクションの詳細

ここでは、このコネクタのアクションおよびトリガーとその応答について詳しく説明します。



### ファイルのメタデータを取得する
この操作では、ファイルのメタデータを取得します。


|プロパティ名| Displayname Settings|説明|
| ---|---|---|
|id*|ファイル|ファイルを選択する|

* は、必須のプロパティを示します。

#### 出力の詳細

BlobMetadata


| プロパティ名 | データ型 |
|---|---|---|
|ID|string|
|名前|string|
|DisplayName|string|
|パス|string|
|LastModified|string|
|サイズ|integer|
|MediaType|string|
|IsFolder|ブール値|
|ETag|string|
|FileLocator|string|




### ファイルを更新する
この操作では、ファイルを更新します。


|プロパティ名| Displayname Settings|説明|
| ---|---|---|
|id*|ファイル|ファイルを選択する|
|body*|ファイルのコンテンツ|ファイルの内容|

* は、必須のプロパティを示します。

#### 出力の詳細

BlobMetadata


| プロパティ名 | データ型 |
|---|---|---|
|ID|string|
|名前|string|
|DisplayName|string|
|パス|string|
|LastModified|string|
|サイズ|integer|
|MediaType|string|
|IsFolder|ブール値|
|ETag|string|
|FileLocator|string|




### ファイルを削除する
この操作では、ファイルを削除します。


|プロパティ名| Displayname Settings|説明|
| ---|---|---|
|id*|ファイル|ファイルを選択する|

* は、必須のプロパティを示します。




### パスを使用してファイルのメタデータを取得する
この操作では、パスを使用してファイルのメタデータを取得します。


|プロパティ名| Displayname Settings|説明|
| ---|---|---|
|path*|ファイル パス|ファイルを選択する|

* は、必須のプロパティを示します。

#### 出力の詳細

BlobMetadata


| プロパティ名 | データ型 |
|---|---|---|
|ID|string|
|名前|string|
|DisplayName|string|
|パス|string|
|LastModified|string|
|サイズ|integer|
|MediaType|string|
|IsFolder|ブール値|
|ETag|string|
|FileLocator|文字列|




### パスを使用してファイルの内容を取得する
この操作では、パスを使用してファイルの内容を取得します。


|プロパティ名| Displayname Settings|説明|
| ---|---|---|
|path*|ファイル パス|ファイルを選択する|

* は、必須のプロパティを示します。




### ファイルの内容を取得する
この操作では、ファイルの内容を取得します。


|プロパティ名| Displayname Settings|説明|
| ---|---|---|
|id*|ファイル|ファイルを選択する|

* は、必須のプロパティを示します。




### ファイルを作成する
この操作では、ファイルを作成します。


|プロパティ名| Displayname Settings|説明|
| ---|---|---|
|folderPath*|フォルダー パス|フォルダーを選択する|
|name*|ファイル名|ファイルの名前|
|body*|ファイルのコンテンツ|ファイルの内容|

* は、必須のプロパティを示します。

#### 出力の詳細

BlobMetadata


| プロパティ名 | データ型 |
|---|---|---|
|ID|string|
|名前|string|
|DisplayName|string|
|パス|string|
|LastModified|string|
|サイズ|integer|
|MediaType|string|
|IsFolder|ブール値|
|ETag|string|
|FileLocator|string|




### ファイルをコピーする
この操作では、ファイルを FTP サーバーにコピーします。


|プロパティ名| Displayname Settings|説明|
| ---|---|---|
|source*|Source url (コピー元 URL)|ソース ファイルの URL|
|destination*|Destination file path (コピー先ファイル パス)|対象ファイル名を含む、コピー先ファイル パス|
|overwrite|Overwrite? (上書きを許可)|’true’ に設定すると、宛先ファイルが上書きされます|

* は、必須のプロパティを示します。

#### 出力の詳細

BlobMetadata


| プロパティ名 | データ型 |
|---|---|---|
|ID|string|
|名前|string|
|DisplayName|string|
|パス|string|
|LastModified|string|
|サイズ|integer|
|MediaType|string|
|IsFolder|ブール値|
|ETag|string|
|FileLocator|string|




### When a file is added or modified (ファイルの追加または変更時)
この操作では、フォルダーでファイルが追加または変更されたときにフローをトリガーします。


|プロパティ名| Displayname Settings|説明|
| ---|---|---|
|folderId*|フォルダー|フォルダーを選択する|

* は、必須のプロパティを示します。




### フォルダー内のファイルを一覧表示する
この操作では、フォルダー内のファイルとサブフォルダーの一覧を取得します。


|プロパティ名| Displayname Settings|説明|
| ---|---|---|
|id*|フォルダー|フォルダーを選択する|

* は、必須のプロパティを示します。



#### 出力の詳細

BlobMetadata


| プロパティ名 | データ型 |
|---|---|---|
|ID|string|
|名前|string|
|DisplayName|string|
|パス|string|
|LastModified|string|
|サイズ|integer|
|MediaType|string|
|IsFolder|ブール値|
|ETag|string|
|FileLocator|文字列|




### ルート フォルダー内のファイルを一覧表示する
この操作では、ルート フォルダー内のファイルとサブフォルダーの一覧を取得します。


この呼び出しには、パラメーターはありません

#### 出力の詳細

BlobMetadata


| プロパティ名 | データ型 |
|---|---|---|
|ID|string|
|名前|string|
|DisplayName|string|
|パス|string|
|LastModified|string|
|サイズ|integer|
|MediaType|string|
|IsFolder|ブール値|
|ETag|string|
|FileLocator|string|




### フォルダーを抽出する
この操作では、フォルダーにアーカイブ ファイル (例: .zip) を抽出します。


|プロパティ名| Displayname Settings|説明|
| ---|---|---|
|source*|Source archive file path (ソース アーカイブ ファイルのパス)|アーカイブ ファイルのパス|
|destination*|Destination folder path (抽出先フォルダー パス)|宛先フォルダーのパス|
|overwrite|Overwrite? (上書きを許可)|’true’ に設定すると、宛先ファイルが上書きされます|

* は、必須のプロパティを示します。



#### 出力の詳細

BlobMetadata


| プロパティ名 | データ型 |
|---|---|---|
|ID|string|
|名前|string|
|DisplayName|string|
|パス|string|
|LastModified|string|
|サイズ|integer|
|MediaType|string|
|IsFolder|ブール値|
|ETag|string|
|FileLocator|文字列|



## HTTP 応答

上記のアクションとトリガーは、次の HTTP 状態コードを 1 つ以上返す場合があります。

|名前|説明|
|---|---|
|200|OK|
|202|承認済み|
|400|正しくない要求|
|401|権限がありません|
|403|許可されていません|
|404|見つかりません|
|500|内部サーバー エラー。不明なエラーが発生しました。|
|default|操作に失敗しました。|







## 次のステップ
[ロジック アプリを作成します](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!----HONumber=AcomDC_0803_2016-->