<properties 
   pageTitle="Azure Data Lake Store の診断ログの表示 | Microsoft Azure" 
   description="Azure Data Lake Store の診断ログの設定方法およびアクセス方法を理解する " 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="07/11/2016"
   ms.author="nitinme"/>

# Azure Data Lake Store の診断ログへのアクセス

Data Lake Store アカウントの診断ログを有効にする方法と、アカウント用に収集されたログを表示する方法について説明します。

データにアクセスするユーザーの一覧、データにアクセスする頻度、アカウントに格納されているデータの量などの情報を提供するデータ アクセスの監査証跡を収集するには、組織で Azure Data Lake Store アカウントの診断ログを有効にします。

## 前提条件

- **Azure サブスクリプション**。[Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。
- Data Lake Store パブリック プレビューに対して、**Azure サブスクリプションを有効にする**。[手順](data-lake-store-get-started-portal.md#signup)を参照してください。
- **Azure Data Lake Store アカウント**。「[Azure ポータルで Azure Data Lake Store の使用を開始する](data-lake-store-get-started-portal.md)」の手順に従ってください。

## Data Lake Store アカウントの診断ログを有効にする

1. 新しい [Azure ポータル](https://portal.azure.com)にサインオンします。

2. Data Lake Store アカウントを開き、Data Lake Store アカウントのブレードで **[設定]**、**[診断設定]** の順にクリックします。

3. **[診断]** ブレードで、診断ログの構成を次のように変更します。

	![診断ログの有効化](./media/data-lake-store-diagnostic-logs/enable-diagnostic-logs.png "Azure App Service の Web アプリの診断ログの有効化")

	* **[状態]** を **[オン]** に設定して診断ログを有効にします。
	* 2 種類の方法でデータを格納および処理することができます。
		* **[Export to Event Hub (Event Hub へのエクスポート)]** オプションを選択して、Azure Event Hub にログ データをストリーミングします。リアルタイムで受信したログを分析するためのダウン ストリーム処理パイプラインがある場合は、ほとんどの場合、このオプションを使用します。このオプションを選択する場合、使用する Azure Event Hub の詳細を指定する必要があります。
		* **[Export to Storage Account (ストレージ アカウントへのエクスポート)]** オプションを選択して、Azure ストレージ アカウントにログを保存します。後日バッチ処理するデータをアーカイブする場合は、このオプションを使用します。このオプションを選択する場合は、ログの保存先の Azure ストレージ アカウントを指定する必要があります。
	* 監査ログ、要求ログ、またはその両方のいずれを取得するかを指定します。
	* データを保持する日数を指定します。
	* **[保存]** をクリックします。

診断設定を有効にしたら、**[診断ログ]** タブでログを確認できます。

## Data Lake Store アカウントの診断ログを表示する

1. Data Lake Store アカウントの **[設定]** ブレードで、**[診断ログ]** をクリックします。

	![診断ログの表示](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs.png "診断ログの表示")

2. **[診断ログ]** ブレードに、**監査ログ**と**要求ログ**によって分類されたログが表示されます。
	* 要求ログは、Data Lake Store アカウントで行われるすべての API 要求をキャプチャします。
	* 監査ログは、要求ログと同様ですが、Data Lake Store アカウントで実行される操作についてより詳しい内訳を提供します。たとえば、要求ログでは単一のアップロードの API 呼び出しが、監査ログでは複数の "追加" 操作になる可能性があります。

3. 各ログ エントリに対する **[ダウンロード]** リンクをクリックして、ログをダウンロードします。

## ログ データの構造について

監査ログと要求ログは JSON 形式で作成されます。このセクションでは、要求ログと監査ログの JSON 構造を確認します。

### 要求ログ

JSON 形式の要求ログのエントリの例を次に示します。各 BLOB には、ログ オブジェクトの配列を含む、**records** と呼ばれるルート オブジェクトが 1 つあります。

	{
	"records": 
	  [		
		. . . .
		,
		{
			 "time": "2016-07-07T21:02:53.456Z",
			 "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/<data_lake_store_account_name>",
			 "category": "Requests",
			 "operationName": "GETCustomerIngressEgress",
			 "resultType": "200",
			 "callerIpAddress": "::ffff:1.1.1.1",
			 "correlationId": "4a11c709-05f5-417c-a98d-6e81b3e29c58",
			 "identity": "1808bd5f-62af-45f4-89d8-03c5e81bac30",
			 "properties": {"HttpMethod":"GET","Path":"/webhdfs/v1/Samples/Outputs/Drivers.csv","RequestContentLength":0,"ClientRequestId":"3b7adbd9-3519-4f28-a61c-bd89506163b8","StartTime":"2016-07-07T21:02:52.472Z","EndTime":"2016-07-07T21:02:53.456Z"}
		}
		,
		. . . .
	  ]
	}

#### 要求ログのスキーマ

| 名前 | 型 | 説明 |
|-----------------|--------|--------------------------------------------------------------------------------|
| time | String | ログのタイムスタンプ (UTC) |
| resourceId | String | 操作が行われたリソースの ID |
| カテゴリ | String | ログのカテゴリ。**Requests** など。 |
| operationName | String | ログに記録される操作の名前。getfilestatus など。 |
| resultType | String | 操作の状態。200 など。 |
| callerIpAddress | String | 要求を行うクライアントの IP アドレス |
| correlationId | String | 関連するログ エントリのセットをグループ化するために使用できるログの ID |
| ID | オブジェクト | ログを生成した ID |
| properties | JSON | 詳細については、以下をご覧ください。 |

#### 要求ログのプロパティのスキーマ

| 名前 | 型 | 説明 |
|----------------------|--------|-----------------------------------------------------------|
| HttpMethod | String | 操作に使用される HTTP メソッド。GET など。 |
| パス | String | 操作が実行されたパス |
| RequestContentLength | int | HTTP 要求のコンテンツの長さ |
| ClientRequestId | String | この要求を一意に識別する ID |
| StartTime | String | サーバーが要求を受信した時刻 |
| EndTime | String | サーバーが応答を送信した時間 |

### 監査ログ

JSON 形式の監査ログのエントリの例を次に示します。各 BLOB には、ログ オブジェクトの配列を含む、**レコード** と呼ばれるルート オブジェクトが 1 つあります。

	{
	"records": 
	  [		
		. . . .
		,
		{
			 "time": "2016-07-08T19:08:59.359Z",
			 "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/<data_lake_store_account_name>",
			 "category": "Audit",
			 "operationName": "SeOpenStream",
			 "resultType": "0",
			 "correlationId": "381110fc03534e1cb99ec52376ceebdf;Append_BrEKAmg;25.66.9.145",
			 "identity": "A9DAFFAF-FFEE-4BB5-A4A0-1B6CBBF24355",
			 "properties": {"StreamName":"adl://<data_lake_store_account_name>.azuredatalakestore.net/logs.csv"}
		}
		,
		. . . .
	  ]
	}

#### 監査ログのスキーマ

| 名前 | 型 | 説明 |
|-----------------|--------|--------------------------------------------------------------------------------|
| time | String | ログのタイムスタンプ (UTC) |
| resourceId | String | 操作が行われたリソースの ID |
| カテゴリ | String | ログのカテゴリ。**Audit** など。 |
| operationName | String | ログに記録される操作の名前。getfilestatus など。 |
| resultType | String | 操作の状態。200 など。 |
| correlationId | String | 関連するログ エントリのセットをグループ化するために使用できるログの ID |
| ID | オブジェクト | ログを生成した ID |
| properties | JSON | 詳細については、以下をご覧ください。 |

#### 監査ログのプロパティのスキーマ

| 名前 | 型 | 説明 |
|------------|--------|------------------------------------------|
| StreamName | String | 操作が実行されたパス |

## 関連項目

- [Azure Data Lake Store の概要](data-lake-store-overview.md)
- [Data Lake Store のデータをセキュリティで保護する](data-lake-store-secure-data.md)

<!---HONumber=AcomDC_0713_2016-->