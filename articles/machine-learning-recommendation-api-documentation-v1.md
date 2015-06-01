<properties 
	pageTitle="Azure Machine Learning Recommendations API ドキュメント" 
	description="Azure Machine Learning Recommendations API ドキュメント" 
	services="machine-learning" 
	documentationCenter="" 
	authors="oranms" 
	manager="paulettm" 
	editor="cgronlun"/>
<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/07/2015" 
	ms.author="v-ahgumn"/>


#Azure Machine Learning Recommendations API ドキュメント

バージョン: 1.0<br>
最新バージョンは[こちら](machine-learning-recommendation-api-documentation.md)でご確認ください

このドキュメントでは、Azure ML の Recommendations API ついて記載しています。


[AZURE.INCLUDE [machine-learning-free-trial](../includes/machine-learning-free-trial.md)]

##1.概要
このドキュメントは、API リファレンスです。最初に、ドキュメント『Azure ML Recommendations – クイック スタート』をお読みください。

Azure ML の Recommendations API は、8 つのグループに分けられます。

1.	<ins>基本モデル</ins> – モデルで基本的な操作が可能な API (例: モデルの作成、更新、削除)。
2.	<ins>高度なモデル</ins> – モデルの高度なデータ分析が取得できる API。
3.	<ins>ビジネス ルールのモデル</ins> – モデルの推奨結果に関するビジネス ルールを管理できる API。
4.	<ins>カタログ</ins> – モデルのカタログに関する基本操作を実行できる API。カタログには、使用状況データ項目に関するメタ データ情報が含まれています。
5.	<ins>使用状況データ</ins> – モデルの使用状況データに関する基本操作を実行できる API。基本フォームの使用状況データは、<userId> と <itemId> のペアを含む行で構成されます。
6.	<ins>ビルド</ins> – モデルのビルドを起動し、このビルドに関連する基本操作を実行できる API。モデルのビルドは、有効な使用状況データが存在する場合に起動できます。
7.	<ins>推奨</ins> – モデルのビルドが終了すると、これらの API を使用して推奨を使用できます。
8.	<ins>通知</ins> – API の操作に関する問題が発生した場合に、通知を受け取ることができます (例: データ取得を使用して使用状況データをレポートし、ほとんどのイベント処理が失敗している場合、エラー通知が発生します)。


##2.制限事項

* サブスクリプションごとのモデルの最大数: 10
* カタログが保持できる項目の最大数: 100,000
* 保持される使用状況ポイントの最大数は 5,000,000 です。新しいデータがアップロードまたは報告されると、最も古いデータが削除されます。
* POST で送信できるデータ (例: カタログ データのインポート、使用データのインポート) の最大サイズは 200 MB です。
* アクティブでない推奨モデル ビルドに対する 1 秒あたりのトランザクション数は約 2 TPS です。アクティブな推奨モデル ビルドのみが 20 TPS を保持できます。

##3.API の概要

###3.1.認証
認証に関する Microsoft Azure Marketplace のガイドラインに従ってください。Marketplace は、Basic または OAuth のいずれかの認証方法をサポートします。

###3.2.サービス URI
各 Azure ML Recommendations API のサービス ルート URI は、[こちら](https://api.datamarket.azure.com/amla/recommendations/v1/)にあります。

サービス URI はすべて、OData 仕様の要素を使用して表されます。

###3.3API バージョン
各 API 呼び出しでは、最後に apiVersion というクエリ パラメーターを 1.0 に設定する必要があります。

##4.基本モデル

###4.1.モデルの作成
"モデルの作成" 要求を作成します。

| HTTP メソッド | URI |
|:--------|:--------|
|POST |`<rootURI>/CreateModel?modelName=%27<model_name>%27&apiVersion=%271.0%27`<br><br>例:<br>`<rootURI>/CreateModel?modelName=%27MyFirstModel%27&apiVersion=%271.0%27`|

|	パラメーター名 |	有効な値 |
|:--------			|:--------								|
|	modelName |	英字 (A～Z、a～z)、数字 (0～9)、ハイフン (-)、アンダー スコア (_) のみが許可されます。<br>最大長: 20 |
|	apiVersion | 1.0 |
|||
| 要求本文 | なし |


**応答**:

HTTP ステータス コード: 200

OData XML

	feed/entry/content/properties/id – contains the model id

	<feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/CreateModel" xmlns:d="http://schemas.microsoft.com/ado/200708/dataservices" xmlns:m="http://schemas.microsoft.com/ado/200708/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	  <title type="text" />
	  <subtitle type="text">Create A New Model</subtitle>
	  <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'</id>
	  <rights type="text" />
	  <updated>2014-10-05T06:35:21Z</updated>
 	 <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'" />
	  <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">CreateANewModelEntity2</title>
    <updated>2014-10-05T06:35:21Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">a658c626-2baa-43a7-ac98-f6ee26120a12</d:Id>
        <d:Name m:type="Edm.String">MyFirstModel</d:Name>
        <d:Date m:type="Edm.String">10/5/2014 6:35:19 AM</d:Date>
        <d:Status m:type="Edm.String">Created</d:Status>
        <d:HasActiveBuild m:type="Edm.String">false</d:HasActiveBuild>
        <d:BuildId m:type="Edm.String">-1</d:BuildId>
        <d:Mpr m:type="Edm.String">0</d:Mpr>
        <d:UserName m:type="Edm.String">5-4058-ab36-1fe254f05102@dm.com</d:UserName>
        <d:Description m:type="Edm.String"></d:Description>
      </m:properties>
    </content>
	  </entry>
	</feed>

###4.2.モデルの取得
"モデルの取得" 要求を作成します。

| HTTP メソッド | URI |
|:--------|:--------|
|GET |`<rootURI>/GetModel?id=%27<model_id>%27&apiVersion=%271.0%27`<br>例:<br>`<rootURI>/GetModel?id=%271cac7b76-def4-41f1-bc81-29b806adb1de%27&apiVersion=%271.0%27`|

|	パラメーター名 |	有効な値 |
|:--------			|:--------								|
|	id |	モデルの一意識別子。 |
|	apiVersion | 1.0 |
|||
| 要求本文 | なし |

**応答**:

HTTP ステータス コード: 200

OData XML

	feed/entry/content/properties/Id – Model unique id
	feed/entry/content/properties/Name – Model name
	feed/entry/content/properties/Date – Model creation date
	feed/entry/content/properties/Status – Model status. One of the following:
    	- Created - model is created and does not contains Catalog and Usage
		- ReadyForBuild – model is created and contains Catalog and Usage
	feed/entry/content/properties/HasActiveBuild – indicate if model was built successfully
	feed/entry/content/properties/BuildId – Model active BuildId
	feed/entry/content/properties/Mpr – Model MPR (see ModelInsight for more information)
	feed/entry/content/properties/UserName – Model internal username


	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v1/GetAllModels" xmlns:d="http://schemas.microsoft.com/ado/200708/dataservices" xmlns:m="http://schemas.microsoft.com/ado/200708/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	  <title type="text" />
	  <subtitle type="text">Get A List Of All Models</subtitle>
	  <id>https://api.datamarket.azure.com/amla/recommendations/v1/GetAllModels?apiVersion='1.0'</id>
	  <rights type="text" />
	  <updated>2014-10-28T14:35:51Z</updated>
 	 <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetAllModels?apiVersion='1.0'" />
	  <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v1/GetAllModels?apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">GetAListOfAllModelsEntity</title>
    <updated>2014-10-28T14:35:51Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetAllModels?apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">68b232f2-1037-45f7-8f49-af822695ee63</d:Id>
        <d:Name m:type="Edm.String">vah-11111</d:Name>
        <d:Date m:type="Edm.String">10/28/2014 2:16:07 PM</d:Date>
        <d:Status m:type="Edm.String">ReadyForBuild</d:Status>
        <d:HasActiveBuild m:type="Edm.String">false</d:HasActiveBuild>
        <d:BuildId m:type="Edm.String">-1</d:BuildId>
        <d:Mpr m:type="Edm.String">0</d:Mpr>
		<d:UsageFileNames m:type="Edm.String">ImplicitMatrix10_Guid_small.txt, ImplicitMatrix11_Guid_small.txt</d:UsageFileNames>
        <d:CatalogId m:type="Edm.String">626babdb-cab6-43a6-82ef-4fb86345700c</d:CatalogId>
        <d:UserName m:type="Edm.String">89dc4722-03ba-4f90-8821-b1db388408b5@dm.com</d:UserName>
        <d:Description m:type="Edm.String">short description</d:Description>
        <d:CatalogFileName m:type="Edm.String">catalog10_small.txt</d:CatalogFileName>
      </m:properties>
    </content>
	  </entry>
	</feed>

###4.3.すべてのモデルの取得
現在のユーザーのすべてのモデルを取得します。

| HTTP メソッド | URI |
|:--------|:--------|
|削除 |`<rootURI>/GetAllModels?apiVersion=%271.0%27`<br>例:<br>`<rootURI>/GetAllModels?apiVersion=%271.0%27`|

|	パラメーター名 |	有効な値 |
|:--------			|:--------								|
|	apiVersion | 1.0 |
|||
| 要求本文 | なし |

**応答**:

HTTP ステータス コード: 200

OData XML

	feed/entry/content/properties/Id – Model unique id
	feed/entry/content/properties/Name – Model name
	feed/entry/content/properties/Date – Model creation date
	feed/entry/content/properties/Status – Model status. One of the following:
	•	Created - model is created and does not contains Catalog and Usage
	•	ReadyForBuild – model is created and contains Catalog and Usage
	feed/entry/content/properties/HasActiveBuild – indicate if model was built successfully
	feed/entry/content/properties/BuildId – Model active BuildId
	feed/entry/content/properties/Mpr – Model MPR (see ModelInsight for more information)
	feed/entry/content/properties/UserName – Model internal username
	feed/entry/content/properties/UsageFileNames – List of model usage files separated by comma
	feed/entry/content/properties/CatalogId – Model Catalog Id
	feed/entry/content/properties/Description – Model description
	feed/entry/content/properties/CatalogFileName – Model Catalog file name

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v1/GetAllModels" xmlns:d="http://schemas.microsoft.com/ado/200708/dataservices" xmlns:m="http://schemas.microsoft.com/ado/200708/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
		<title type="text" />
		<subtitle type="text">Get A List Of All Models</subtitle>
		<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetAllModels?apiVersion='1.0'</id>
		<rights type="text" />
		<updated>2014-10-28T14:35:51Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetAllModels?apiVersion='1.0'" />
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetAllModels?apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
			<title type="text">GetAListOfAllModelsEntity</title>
    <updated>2014-10-28T14:35:51Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetAllModels?apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Id m:type="Edm.String">68b232f2-1037-45f7-8f49-af822695ee63</d:Id>
					<d:Name m:type="Edm.String">vah-11111</d:Name>
					<d:Date m:type="Edm.String">10/28/2014 2:16:07 PM</d:Date>
					<d:Status m:type="Edm.String">ReadyForBuild</d:Status>
					<d:HasActiveBuild m:type="Edm.String">false</d:HasActiveBuild>
					<d:BuildId m:type="Edm.String">-1</d:BuildId>
					<d:Mpr m:type="Edm.String">0</d:Mpr>
					<d:UsageFileNames m:type="Edm.String">ImplicitMatrix10_Guid_small.txt, ImplicitMatrix11_Guid_small.txt</d:UsageFileNames>
					<d:CatalogId m:type="Edm.String">626babdb-cab6-43a6-82ef-4fb86345700c</d:CatalogId>
					<d:UserName m:type="Edm.String">89dc4722-03ba-4f90-8821-b1db388408b5@dm.com</d:UserName>
					<d:Description m:type="Edm.String">short description</d:Description>
					<d:CatalogFileName m:type="Edm.String">catalog10_small.txt</d:CatalogFileName>
				</m:properties>
			</content>
		</entry>
	</feed>

###4.4.モデルの更新

モデルの説明かアクティブなビルド ID を更新できます。<br>
<ins>アクティブなビルド ID</ins> - すべてのモデルのすべてのビルドには "ビルド ID" があります。アクティブな "ビルド ID" は、新しいモデルそれぞれの最初の正常なビルドです。アクティブなビルド ID があり、同じモデルに対して追加のビルドを実行する場合は、必要に応じて、既定のビルド ID として明示的に設定する必要があります。Recommendations を使用する際に、使用するビルド ID を指定しないと、既定の ID が自動的に使用されます。<br>
このメカニズムにより、推奨モデルが運用環境にあるときに、新しいモデルを構築して、それを運用環境に移行する前にテストすることができます。


| HTTP メソッド | URI |
|:--------|:--------|
|PUT |`<rootURI>/UpdateModel?id=%27<modelId>%27&apiVersion=%271.0%27`<br>例:<br>`<rootURI>/UpdateModel?id=%279559872f-7a53-4076-a3c7-19d9385c1265%27&apiVersion=%271.0%27`|

|	パラメーター名 |	有効な値 |
|:--------			|:--------								|
|	id | モデルの一意識別子。 |
|	apiVersion | 1.0 |
|||
| 要求本文 | `<ModelUpdateParams xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">`<br>`<Description>New Description</Description>`<br>`<ActiveBuildId>-1</ActiveBuildId>`<br>` </ModelUpdateParams>`<br><br>xml タグの Description と ActiveBuildId はオプションなので、Description または ActiveBuildId を設定しない場合は、タグ全体を削除してください。|

**応答**:

HTTP ステータス コード: 200

###4.5.モデルの削除
既存のモデルを ID で削除する

| HTTP メソッド | URI |
|:--------|:--------|
|削除 |`<rootURI>/DeleteModel?id=%27<model_id>%27&apiVersion=%271.0%27`<br>例:<br>`<rootURI>/DeleteModel?id=%271cac7b76-def4-41f1-bc81-29b806adb1de%27&apiVersion=%271.0%27`|

|	パラメーター名 |	有効な値 |
|:--------			|:--------								|
|	id |	モデルの一意識別子。 |
|	apiVersion | 1.0 |
|||
| 要求本文 | なし |

**応答**:

HTTP ステータス コード: 200

OData XML

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v1/DeleteModel" xmlns:d="http://schemas.microsoft.com/ado/200708/dataservices" xmlns:m="http://schemas.microsoft.com/ado/200708/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	  <title type="text" />
	  <subtitle type="text">Delete Model by Id</subtitle>
	  <id>https://api.datamarket.azure.com/amla/recommendations/v1/DeleteModel?id='1cac7b76-def4-41f1-bc81-29b806adb1de'&amp;apiVersion='1.0'</id>
	  <rights type="text" />
	  <updated>2014-10-28T10:39:33Z</updated>
 	 <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/DeleteModel?id='1cac7b76-def4-41f1-bc81-29b806adb1de'&amp;apiVersion='1.0'" />
	  <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v1/DeleteModel?id='1cac7b76-def4-41f1-bc81-29b806adb1de'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">DeleteModelByIdEntity</title>
    <updated>2014-10-28T10:39:33Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/DeleteModel?id='1cac7b76-def4-41f1-bc81-29b806adb1de'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
		<d:string m:type="Edm.String"></d:string>
      </m:properties>
    </content>
	  </entry>
	</feed>

##5.高度なモデル

###5.1.モデル データの分析
この API は、このモデルがビルドされた際の使用状況データに関する統計データを返します。

| HTTP メソッド | URI |
|:--------|:--------|
|GET |`<rootURI>/GetDataInsight?modelId=%27<model_id>%27&apiVersion=%271.0%27`<br>例:<br>`<rootURI>/GetDataInsight?modelId=%271cac7b76-def4-41f1-bc81-29b806adb1de%27&apiVersion=%271.0%27`|

|	パラメーター名 |	有効な値 |
|:--------			|:--------								|
|	modelId |	モデルの一意識別子。 |
|	apiVersion | 1.0 |
|||
| 要求本文 | なし |

**応答**:

HTTP ステータス コード: 200

OData XML

<pre>
	feed/entry/id/content/properties/key
	feed/entry/id/content/properties/value
</pre>
のコレクションとしてデータが返されます。

次の表はそれぞれが表す値である各キーを示します。<table> <tr> <th>キー</th> <th>説明</th> </tr> <tr> <td>AvgItemLength</td> <td>項目ごとの別個のユーザーの平均数</td> </tr> <tr> <td>AvgUserLength</td> <td>項目ごとの別個のユーザーの平均数</td> </tr> <tr> <td>DensificationNumberOfItems</td> <td>モデル化できない項目を削除した後の項目数</td> </tr> <tr> <td>DensificationNumberOfUsers</td> <td>モデル化できないユーザーと項目を削除した後の使用状況ポイントの数</td> </tr> <tr> <td>DensificationNumberOfRecords</td> <td>モデル化できないユーザーと項目を削除した後の使用状況ポイントの数</td> </tr> <tr> <td>MaxItemLength</td> <td>最も人気のある項目の別個のユーザー数</td> </tr> <tr> <td>MaxUserLength</td> <td>ユーザーごとの別個の項目の最大数</td> </tr> <tr> <td>MinItemLength</td> <td>項目ごとの別個のユーザーの最大数</td> </tr> <tr> <td>MinUserLength</td> <td>ユーザーごとの別個の項目の最小数</td> </tr> <tr> <td>RawNumberOfItems</td> <td>モデル化できない項目を削除した後の項目の数</td> </tr> <tr> <td>RawNumberOfUsers</td> <td>モデル化できない項目を削除する前の項目数</td> </tr> <tr> <td>RawNumberOfRecords</td> <td>排除する前の使用状況ポイントの数</td> </tr> <tr> <td>SampelingNumberOfItems</td> <td>サンプリングが有効な場合、サンプルの項目の数。それ以外の場合、無視</td> </tr> <tr> <td>SampelingNumberOfRecords</td> <td>サンプリングがオンになっている場合、ユーザー ポイント サンプルの数。それ以外の場合、無視</td> </tr> <tr> <td>SampelingNumberOfUsers</td> <td>サンプリングがオンになっている場合、ユーザー ポイント サンプルの数。それ以外の場合、無視</td> </tr> </table>

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v1/GetDataInsight" xmlns:d="http://schemas.microsoft.com/ado/200708/dataservices" xmlns:m="http://schemas.microsoft.com/ado/200708/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	<title type="text" />
	<subtitle type="text">Get data insight statistics</subtitle>
	<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'</id>
	<rights type="text" />
	<updated>2014-10-27T14:21:21Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'" />
	<entry>
	<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">GetDataInsightStatisticsEntity</title>
    <updated>2014-10-27T14:21:21Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Key m:type="Edm.String">AvgItemLength</d:Key>
        <d:Value m:type="Edm.String">18.936</d:Value>
      </m:properties>
    </content>
	</entry>
	<entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v1/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
    <title type="text">GetDataInsightStatisticsEntity</title>
    <updated>2014-10-27T14:21:21Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Key m:type="Edm.String">AvgUserLength</d:Key>
        <d:Value m:type="Edm.String">51.879</d:Value>
      </m:properties>
    </content>
    </entry>
    <entry>
	<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
    <title type="text">GetDataInsightStatisticsEntity</title>
    <updated>2014-10-27T14:21:21Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Key m:type="Edm.String">DensificationNumberOfItems</d:Key>
        <d:Value m:type="Edm.String">2,000</d:Value>
      </m:properties>
    </content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1</id>
	<title type="text">GetDataInsightStatisticsEntity</title>
	<updated>2014-10-27T14:21:21Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Key m:type="Edm.String">DensificationNumberOfRecords</d:Key>
        <d:Value m:type="Edm.String">37,872</d:Value>
      </m:properties>
    </content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1</id>
	<title type="text">GetDataInsightStatisticsEntity</title>
	<updated>2014-10-27T14:21:21Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1" />
	<content type="application/xml">
		<m:properties>
			<d:Key m:type="Edm.String">DensificationNumberOfUsers</d:Key>
			<d:Value m:type="Edm.String">730</d:Value>
      </m:properties>
    </content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1</id>
		<title type="text">GetDataInsightStatisticsEntity</title>
		<updated>2014-10-27T14:21:21Z</updated>
    	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">MaxItemLength</d:Key>
				<d:Value m:type="Edm.String">4,704</d:Value>
			</m:properties>
		</content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1</id>
		<title type="text">GetDataInsightStatisticsEntity</title>
		<updated>2014-10-27T14:21:21Z</updated>
    	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1" />
    	<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">MaxUserLength</d:Key>
				<d:Value m:type="Edm.String">190</d:Value>
			</m:properties>
    	</content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1</id>
		<title type="text">GetDataInsightStatisticsEntity</title>
		<updated>2014-10-27T14:21:21Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">MinItemLength</d:Key>
				<d:Value m:type="Edm.String">8</d:Value>
			</m:properties>
    	</content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1</id>
		<title type="text">GetDataInsightStatisticsEntity</title>
		<updated>2014-10-27T14:21:21Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">MinUserLength</d:Key>
				<d:Value m:type="Edm.String">20</d:Value>
			</m:properties>
		</content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1</id>
		<title type="text">GetDataInsightStatisticsEntity</title>
		<updated>2014-10-27T14:21:21Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">RawNumberOfItems</d:Key>
				<d:Value m:type="Edm.String">2,000</d:Value>
			</m:properties>
		</content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=10&amp;$top=1</id>
		<title type="text">GetDataInsightStatisticsEntity</title>
		<updated>2014-10-27T14:21:21Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=10&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">RawNumberOfRecords</d:Key>
				<d:Value m:type="Edm.String">72,022</d:Value>
			</m:properties>
		</content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=11&amp;$top=1</id>
		<title type="text">GetDataInsightStatisticsEntity</title>
		<updated>2014-10-27T14:21:21Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=11&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">RawNumberOfUsers</d:Key>
				<d:Value m:type="Edm.String">9,044</d:Value>
			</m:properties>
		</content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=12&amp;$top=1</id>
		<title type="text">GetDataInsightStatisticsEntity</title>
		<updated>2014-10-27T14:21:21Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=12&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">SampelingNumberOfItems</d:Key>
				<d:Value m:type="Edm.String">2,000</d:Value>
			</m:properties>
		</content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=13&amp;$top=1</id>
		<title type="text">GetDataInsightStatisticsEntity</title>
		<updated>2014-10-27T14:21:21Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=13&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">SampelingNumberOfRecords</d:Key>
				<d:Value m:type="Edm.String">72,022</d:Value>
			</m:properties>
		</content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=14&amp;$top=1</id>
		<title type="text">GetDataInsightStatisticsEntity</title>
		<updated>2014-10-27T14:21:21Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=14&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">SampelingNumberOfUsers</d:Key>
				<d:Value m:type="Edm.String">9,044</d:Value>
			</m:properties>
		</content>
    </entry>
    </feed>

###5.2.モデルの分析
この API はアクティブなビルドまたは特定のビルド (指定した場合) でモデルの分析を返します。

| HTTP メソッド | URI |
|:--------|:--------|
|GET |アクティブなビルド ID の場合:<br>`<rootURI>/GetDataInsight?modelId=%27<model_id>%27&apiVersion=%271.0%27`<br><br>例:<br>`<rootURI>/GetDataInsight?modelId=%271cac7b76-def4-41f1-bc81-29b806adb1de%27&apiVersion=%271.0%27`<br><br>特定のビルド ID の場合:<br>`<rootURI>/GetModelInsight?modelId=%27<model_id>%27&buildId=%27<build_id>%27&apiVersion=%271.0%27`|

|	パラメーター名 |	有効な値 |
|:--------			|:--------								|
|	modelId |	モデルの一意識別子。 |
|	buildId |	省略可能 – 成功したビルドを識別する数値。 |
|	apiVersion | 1.0 |
|||
| 要求本文 | なし |

**応答**:

HTTP ステータス コード: 200

OData XML

<pre>
	feed/entry/id/content/properties/key
	feed/entry/id/content/properties/value
</pre>
のコレクションとしてデータが返されます。

次の表はそれぞれが表す値である各キーを示します。<table> <tr> <th>キー</th> <th>説明</th> </tr> <tr> <td>CatalogCoverage</td> <td>使用状況パターンを使用してモデル化できるカタログの部分。残りの項目はコンテンツ ベースの機能が必要になります。</td> </tr> <tr> <td>Mpr</td> <td>モデルの平均パーセンタイル順位付け。小さい数値がより適していることを示します。</td> </tr> <tr> <td>NumberOfDimensions</td> <td>行列因子分解アルゴリズムによって使用されるディメンションの数。</td> </tr> </table>

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v1/GetModelInsight" xmlns:d="http://schemas.microsoft.com/ado/200708/dataservices" xmlns:m="http://schemas.microsoft.com/ado/200708/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	<title type="text" />
	<subtitle type="text">Get model insight statistics</subtitle>
	<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'</id>
	<rights type="text" />
	<updated>2014-10-27T14:27:11Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'" />
	<entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
		<title type="text">GetModelInsightStatisticsEntity</title>
		<updated>2014-10-27T14:27:11Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">CatalogCoverage</d:Key>
				<d:Value m:type="Edm.String">1.000</d:Value>
			</m:properties>
		</content>
	</entry>
	<entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
		<title type="text">GetModelInsightStatisticsEntity</title>
		<updated>2014-10-27T14:27:11Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">Mpr</d:Key>
				<d:Value m:type="Edm.String">0.367</d:Value>
			</m:properties>
		</content>
	</entry>
	<entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
		<title type="text">GetModelInsightStatisticsEntity</title>
		<updated>2014-10-27T14:27:11Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">NumberOfDimensions</d:Key>
				<d:Value m:type="Edm.String">20</d:Value>
			</m:properties>
		</content>
	</entry>
	</feed>

###5.3.モデルのサンプルの取得
推奨モデルのサンプルを取得します。

| HTTP メソッド | URI |
|:--------|:--------|
|GET |`<rootURI>/GetModelSample?modelId=%27<model_id>%27&apiVersion=%271.0%27`<br>例:<br>`<rootURI>/GetModelSample?modelId=%271cac7b76-def4-41f1-bc81-29b806adb1de%27&apiVersion=%271.0%27`|

|	パラメーター名 |	有効な値 |
|:--------			|:--------								|
|	modelId |	モデルの一意識別子。 |
|	apiVersion | 1.0 |
|||
| 要求本文 | なし |

**応答**:

HTTP ステータス コード: 200

OData XML<br>
応答は未加工のテキスト形式で返されます
<pre>
Level 1
---------------
655fc955-a5a3-4a26-9723-3090859cb27b, Prey: A Novel
	655fc955-a5a3-4a26-9723-3090859cb27b, Prey: A Novel Rating: 0.5215
	3f471802-f84f-44a0-99c8-6d2e7418eec1, Black Hawk Down: A Story of Modern War Rating: 0.5151
	07b10e28-9e7c-4032-90b7-10acab7f2460, Cryptonomicon Rating: 0.5148
	6afc18e4-8c2a-43d1-9021-57543d6b11d8, Imajica Rating: 0.5146
	e4cc5e69-3567-43ab-b00f-f0d8d0506870, Hit List Rating: 0.514
56b61441-0eed-46cc-a8f6-112775b81892, Life and Death in Shanghai
	56b61441-0eed-46cc-a8f6-112775b81892, Life and Death in Shanghai Rating: 0.5218
	53156702-cc0c-443d-b718-6fb74b2491d3, Son of \ Rating: 0.5212
	fb8cf7a6-8719-46ee-97d4-92f931d77a3a, Smoke and Mirrors: Short Fictions and Illusions Rating: 0.5188
	8f5fe006-79e4-4679-816b-950989d1db4b, A Place I'Ve Never Been (Contemporary American Fiction) Rating: 0.5156
	d8db4583-cc0f-49ce-bc95-b7fa3491623f, Happiness : A Novel Rating: 0.5156
50471eec-9aeb-4900-84d7-21567ab18546, If the Buddha Dated: A Handbook for Finding Love on a Spiritual Path
	cfe922a1-7ca0-4f8d-ad9d-b7cc87bfe0ef, Divine Secrets of the Ya-Ya Sisterhood: A Novel Rating: 0.5266
	ff91a483-1ce5-4b37-a6fd-5ffcf21f8745, The Poisonwood Bible: A Novel Rating: 0.5252
	973f8cbd-0846-4f6b-9d28-4dd0d7dc3a19, Pigs in Heaven Rating: 0.5244
	e2cbf7ad-0636-4117-8b30-298da6df7077, Animal Dreams Rating: 0.5227
	6c818fd3-5a09-417d-9ab4-7ffe090f0fef, Confessions of an Ugly Stepsister : A Novel Rating: 0.5222
5e97148f-defb-4d74-af2d-80f4763bf531, The Deep End of the Ocean (Oprah's Book Club)
	5e97148f-defb-4d74-af2d-80f4763bf531, The Deep End of the Ocean (Oprah's Book Club) Rating: 0.537
	5dcbac37-2946-4f2a-a0b3-bbe710f9409a, Up Island: A Novel Rating: 0.5277
	bc5b69db-733b-4346-adde-3927544258f7, Downtown Rating: 0.5275
	31fe5c63-3e5a-48d0-802b-d3b0f989a634, Have a Nice Day : A Tale of Blood and Sweatsocks Rating: 0.5252
	0adf981a-b65b-4c11-b36b-78aca2f948a2, The Perfect Storm : A True Story of Men Against the Sea Rating: 0.5238
68f97068-ae1a-4163-9e94-396b800b743d, Modoc : The True Story of the Greatest Elephant That Ever Lived
	68f97068-ae1a-4163-9e94-396b800b743d, Modoc : The True Story of the Greatest Elephant That Ever Lived Rating: 0.5379
	6724862e-e4e7-4022-9614-1468d8b902ff, Little House on the Prairie Rating: 0.5345
	cdedb837-1620-496d-94c4-6ccfed888320, Little House in the Big Woods Rating: 0.5325
	382164ba-406b-4187-b726-d7a54b9d790d, The Tao of Pooh Rating: 0.5309
	6a068d6a-bb74-4ba3-b3f2-a956c4f9d1b5, On the Banks of Plum Creek Rating: 0.5285
37ef8e74-e348-44e5-aabc-1d7f9efcb25b, Men Are from Mars Women Are from Venus: A Practical Guide for Improving Communication and Getting What You Want in Your Relationships
	37ef8e74-e348-44e5-aabc-1d7f9efcb25b, Men Are from Mars Women Are from Venus: A Practical Guide for Improving Communication and Getting What You Want in Your Relationships Rating: 0.5397
	f2be16d4-5faf-4d32-ab83-7ba74d29261e, Politically Correct Bedtime Stories: Modern Tales for Our Life and Times Rating: 0.5207
	ef732c5c-334b-4d6b-ab82-7255eb7286d0, Honor Among Thieves Rating: 0.5195
	0b209b8c-7cdd-47fd-b940-05c7ff7c60fc, The Giving Tree Rating: 0.5194
	883b360f-8b42-407f-b977-2f44ad840877, Scary Stories to Tell in the Dark : Collected from American Folklore (Scary Stories) Rating: 0.5184
ff51b67e-fa8e-4c5e-8f4d-02a928de735d, Men at Work : The Craft of Baseball
	d008dae9-c73a-40a1-9a9b-96d5cf546f36, The Gulag Archipelago 1918-1956 : An Experiment in Literary Investigation I-II Rating: 0.5416
	ff51b67e-fa8e-4c5e-8f4d-02a928de735d, Men at Work : The Craft of Baseball Rating: 0.5403
	49dec30e-0adb-411a-b186-48eaabf6f8bc, Fatherland Rating: 0.5394
	cc7964fd-d30f-478e-a425-93ddbdf094ed, Magic the Gathering: Arena Vol. 1 Rating: 0.5379
	8a1e9f36-97af-4614-bed9-24e3940a05f3, More Sniglets: Any Word That Doesn't Appear in the Dictionary but Should Rating: 0.5377
12a6d988-be21-4a09-8143-9d5f4261ba16, A Dream of Eagles
	07b10e28-9e7c-4032-90b7-10acab7f2460, Cryptonomicon Rating: 0.5417
	e4cc5e69-3567-43ab-b00f-f0d8d0506870, Hit List Rating: 0.5416
	1f1a34c4-9781-49f5-a3cc-acec3ae3c71d, The Family Rating: 0.5371
	56daeffe-7d48-43cd-8ef8-7dffd0c103d3, Kilo Class Rating: 0.5366
	b2fe511e-5cb9-4a56-b823-2801e63e6a96, Legal Tender Rating: 0.5366
df87525b-e435-4bd6-8701-4e60ad344e28, Finding Fish
	56d33036-dfda-46b9-8e2a-76cb03921bb0, The X-Files: Ground Zero Rating: 0.5417
	0780cde8-6529-4e1d-b6c6-082c1b80e596, Twelve Red Herrings Rating: 0.5416
	df87525b-e435-4bd6-8701-4e60ad344e28, Finding Fish Rating: 0.5408
	400fe331-2c35-490c-adbc-b28b4b73d56c, Shall We Tell the President? Rating: 0.5383
	f86ad7d0-5c03-42b3-aebf-13d44aec8b30, Shades of Grace Rating: 0.5358
de1f62a4-89e6-44d2-aaee-992a4bf093f1, The Map That Changed the World: William Smith and the Birth of Modern Geology
	de1f62a4-89e6-44d2-aaee-992a4bf093f1, The Map That Changed the World: William Smith and the Birth of Modern Geology Rating: 0.5422
	b303538f-e2c6-4a2c-b425-8d21e684fc3e, My Uncle Oswald Rating: 0.5385
	34b84627-48af-4a4c-96c4-b26fb3863f56, Midnight In the Garden of Good and Evil Rating: 0.5379
	306cbaa7-b1a8-4142-9d55-e11b5018a7a8, The Street Lawyer Rating: 0.5376
	e53b4baa-8c09-45c4-95c0-b6a26b98770b, Miss Smillas Feeling for Snow Rating: 0.5367

レベル 2
---------------
352aaea1-6b12-454d-a3d5-46379d9e4eb2, The Sinister Pig (Hillerman Tony)
	352aaea1-6b12-454d-a3d5-46379d9e4eb2, The Sinister Pig (Hillerman Tony) Rating: 0.5425
	74c49398-bc10-4af5-a658-a996a1201254, Children of the Storm (Peters Elizabeth) Rating: 0.5387
	9ba80080-196e-43fd-8025-391d963f77e7, The Floating Girl Rating: 0.5372
	e68f81d5-7745-4cc7-b943-fedb8fcc2ced, Killer Smile (Scottoline Lisa) Rating: 0.5353
	b2fe511e-5cb9-4a56-b823-2801e63e6a96, Legal Tender Rating: 0.5332
c65c3995-abf7-4c7b-bb3c-8eb5aa9be7a5, Lake Wobegon days
	0adf981a-b65b-4c11-b36b-78aca2f948a2, The Perfect Storm : A True Story of Men Against the Sea Rating: 0.5433
	c65c3995-abf7-4c7b-bb3c-8eb5aa9be7a5, Lake Wobegon days Rating: 0.543
	a00ae6ad-4a7f-4211-9836-75ce8834eb11, Sniglets (Snig'lit : Any Word That Doesn't Appear in the Dictionary But Should) Rating: 0.5327
	6f6e192e-0d64-49ca-9b63-f09413ea1ee6, Politically Correct Holiday Stories: For an Enlightened Yuletide Season Rating: 0.5307
	798051a8-147d-4d46-b0dc-e836325029e6, AGE OF INNOCENCE (MOVIE TIE-IN) Rating: 0.5301
73f3e25a-e996-4162-9ed8-ff3d34075650, O Pioneers! (Penguin Twentieth-Century Classics)
	cba8163f-6536-436b-8130-47b4a43c827f, Trust No One (The Official Guide to the X-Files Vol. 2) Rating: 0.5434
	5708e4cb-2492-49c0-94a8-cc413eec5d89, Small Gods (Discworld Novels (Paperback)) Rating: 0.5406
	73f3e25a-e996-4162-9ed8-ff3d34075650, O Pioneers! (Penguin Twentieth-Century Classics) Rating: 0.5403
	d885b0bd-ae4b-452d-bdf2-faa90197dbc9, The Color of Magic Rating: 0.539
	b133a9c4-4784-4db3-b100-d0d6dffb94d2, The Truth Is Out There (The Official Guide to the X-Files Vol. 1) Rating: 0.5367
271700a5-854a-4d5a-8409-6b57a5ee4de4, Fluke : Or I Know Why the Winged Whale Sings
	271700a5-854a-4d5a-8409-6b57a5ee4de4, Fluke : Or I Know Why the Winged Whale Sings Rating: 0.5445
	2de1c354-90ff-47c5-a0db-1bad7d88ef94, The Salaryman's Wife (Children of Violence Series) Rating: 0.5329
	d279416e-19c0-43f8-9ec9-a585947879ca, Zen Attitude Rating: 0.5316
	c8f854d7-3de3-4b23-8217-f4f851670fd4, Revenge of the Cootie Girls: A Robin Hudson Mystery (Robin Hudson Mysteries (Paperback)) Rating: 0.5305
	8ef4751c-7074-409e-a3ac-d49b222fc864, Where the Wild Things Are Rating: 0.5289
9ad1b620-0a7b-4543-8673-66d4c3bcb2f1, Their Eyes Were Watching God
	9ad1b620-0a7b-4543-8673-66d4c3bcb2f1, Their Eyes Were Watching God Rating: 0.5446
	da45c4d5-aba1-413b-a9bd-50df98b1e1d2, The Bean Trees Rating: 0.5389
	65ecbdd1-131c-40c3-a3d6-d86ca281377a, The God of Small Things Rating: 0.5387
	c78743bf-7947-4a0c-8db7-8a3bfe69ba70, The Stone Diaries Rating: 0.5355
	973f8cbd-0846-4f6b-9d28-4dd0d7dc3a19, Pigs in Heaven Rating: 0.5344
5f17d90a-2604-4fe8-8977-1a280b9098b1, One for the Money (Stephanie Plum Novels (Paperback))
	5f17d90a-2604-4fe8-8977-1a280b9098b1, One for the Money (Stephanie Plum Novels (Paperback)) Rating: 0.5446
	57169b2b-9a8a-486b-9aac-1ed98ce57168, Final Appeal Rating: 0.5332
	efcb1bc4-7278-4a8f-b491-befde02070d6, Moment of Truth Rating: 0.5329
	1efa91a2-993b-4c43-9f5c-3454fc12612d, Burn Factor Rating: 0.5309
	24c59962-458a-4ec8-b95d-d694e861919c, At Home in Mitford (The Mitford Years) Rating: 0.5303
4fd48c46-1a20-4c57-bc7f-a02ef123dc52, As Nature Made Him: The Boy Who Was Raised As a Girl
	4fd48c46-1a20-4c57-bc7f-a02ef123dc52, As Nature Made Him: The Boy Who Was Raised As a Girl Rating: 0.5449
	cd5f2c03-20cb-43be-a1fb-3b4233e63222, Pigs in Heaven Rating: 0.5329
	19985fdb-d07a-4a25-ae4a-97b9cb61e5d1, Love in the Time of Cholera (Penguin Great Books of the 20th Century) Rating: 0.5267
	15689d09-c711-4844-84d8-130a90237b26, Bel Canto Rating: 0.5245
	ff91a483-1ce5-4b37-a6fd-5ffcf21f8745, The Poisonwood Bible: A Novel Rating: 0.5235
98df28ec-41e7-4fca-b77f-8b0d3109085d, Star Trek Memories
	f874b5a3-5d40-4436-94ff-0fa1c090ddf5, The Sun Also Rises (A Scribner classic) Rating: 0.5451
	98df28ec-41e7-4fca-b77f-8b0d3109085d, Star Trek Memories Rating: 0.5442
	0ce0014a-9a48-4013-a08a-7f2c11877930, H.M.S. Unseen Rating: 0.5421
	15316ca6-1e38-425f-893d-691944a47000, More Scary Stories To Tell In The Dark Rating: 0.5409
	329d5682-3dc3-4206-8aa2-eef4b1032258, Letters from the Earth Rating: 0.54
5b9445d5-c072-419c-8d49-6f669bb1b0a9, Daughter of Fortune : A Novel (Oprah's Book Club (Hardcover))
	5b9445d5-c072-419c-8d49-6f669bb1b0a9, Daughter of Fortune : A Novel (Oprah's Book Club (Hardcover)) Rating: 0.5462
	ff91a483-1ce5-4b37-a6fd-5ffcf21f8745, The Poisonwood Bible: A Novel Rating: 0.5372
	604eb3bd-6026-4f51-bffd-9fb54f180400, Family Pictures: A Novel Rating: 0.5341
	8d06d01d-31cd-4678-b6b1-140a67987ce9, Songs in Ordinary Time (Oprah's Book Club (Paperback)) Rating: 0.5334
	da45c4d5-aba1-413b-a9bd-50df98b1e1d2, The Bean Trees Rating: 0.5319
d5358189-d70f-4e35-8add-34b83b4942b3, Pigs in Heaven
	d5358189-d70f-4e35-8add-34b83b4942b3, Pigs in Heaven Rating: 0.5491
	ff91a483-1ce5-4b37-a6fd-5ffcf21f8745, The Poisonwood Bible: A Novel Rating: 0.5401
	c78743bf-7947-4a0c-8db7-8a3bfe69ba70, The Stone Diaries Rating: 0.5393
	8d06d01d-31cd-4678-b6b1-140a67987ce9, Songs in Ordinary Time (Oprah's Book Club (Paperback)) Rating: 0.5382
	973f8cbd-0846-4f6b-9d28-4dd0d7dc3a19, Pigs in Heaven Rating: 0.5367

</pre>

##6.ビジネス ルールのモデル
追加できるルールには、<strong>Blocklist</strong> と <strong>Upsale</strong> の 2 つのタイプがあります。BlockList では、推奨事項の結果を返すときに除外する項目の一覧を指定できます。Upsale では項目を強制的に推奨事項の結果の一部として返すことができます。

###6.1.モデルのルールの取得

| HTTP メソッド | URI |
|:--------|:--------|
|GET |`<rootURI>/GetModelRules?modelId=%27<model_id>%27&apiVersion=%271.0%27`<br>例:<br>`<rootURI>/GetModelRules?modelId=%271cac7b76-def4-41f1-bc81-29b806adb1de%27&apiVersion=%271.0%27`|

|	パラメーター名 |	有効な値 |
|:--------			|:--------								|
|	modelId |	モデルの一意識別子。 |
|	apiVersion | 1.0 |
|||
| 要求本文 | なし |

**応答**:

HTTP ステータス コード: 200

OData XML

	feed/entry/content/properties/Id – The unique identifier of this rule
	feed/entry/content/properties/Type – The type of the rule. BlockList or Upsale
	feed/entry/content/properties/Parameter – The rule parameter

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v1/GetModelRules" xmlns:d="http://schemas.microsoft.com/ado/200708/dataservices" xmlns:m="http://schemas.microsoft.com/ado/200708/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	<title type="text" />
	<subtitle type="text">Get a list of rules for a model</subtitle>
	<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetModelRules?modelId='5e824626-50d3-469d-a824-564d38453103'&amp;apiVersion='1.0'</id>
	<rights type="text" />
	<updated>2014-11-05T12:58:57Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetModelRules?modelId='5e824626-50d3-469d-a824-564d38453103'&amp;apiVersion='1.0'" />
	<entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetModelRules?modelId='5e824626-50d3-469d-a824-564d38453103'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
		<title type="text">GetAListOfRulesForAModelEntity</title>
		<updated>2014-11-05T12:58:57Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetModelRules?modelId='5e824626-50d3-469d-a824-564d38453103'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Id m:type="Edm.String">1000043</d:Id>
				<d:Type m:type="Edm.String">BlockList</d:Type>
				<d:Parameters m:type="Edm.String">{"ItemsToExclude":["1000"]}</d:Parameters>
			</m:properties>
		</content>
	</entry>
	<entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetModelRules?modelId='5e824626-50d3-469d-a824-564d38453103'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
		<title type="text">GetAListOfRulesForAModelEntity</title>
		<updated>2014-11-05T12:58:57Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetModelRules?modelId='5e824626-50d3-469d-a824-564d38453103'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Id m:type="Edm.String">1000044</d:Id>
				<d:Type m:type="Edm.String">BlockList</d:Type>
				<d:Parameters m:type="Edm.String">{"ItemsToExclude":["1001"]}</d:Parameters>
			</m:properties>
		</content>
	</entry>
	</feed>

###6.2.ルールの追加
| HTTP メソッド | URI |
|:--------|:--------|
|POST |`<rootURI>/AddRule?apiVersion=%271.0%27`|

|	パラメーター名 |	有効な値 |
|:--------			|:--------								|
|	apiVersion | 1.0 |
|||
| 要求本文 | <ins>BlockList ルールの追加:</ins><br>`<ApiFilter xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"><ModelId>24024f7e-b45c-419e-bfa2-dfd947e0d253</ModelId><Type>BlockList</Type><Value>{"ItemsToExclude":["2406E770-769C-4189-89DE-1C9283F93A96"]}</Value></ApiFilter>`<br><br><ins>Upsale ルールの追加:</ins><br>`<ApiFilter xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"><ModelId>24024f7e-b45c-419e-bfa2-dfd947e0d253</ModelId><Type>Upsale</Type><Value>{"ItemsToUpsale":["2406E770-769C-4189-89DE-1C9283F93A96"]}</Value></ApiFilter>`|

HTTP ステータス コード: 200

OData XML

	feed/entry/content/properties/Id – The unique identifier of this rule
	feed/entry/content/properties/Type – The type of the rule. BlockList or Upsale
	feed/entry/content/properties/Parameter – The rule parameter

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v1/AddRule" xmlns:d="http://schemas.microsoft.com/ado/200708/dataservices" xmlns:m="http://schemas.microsoft.com/ado/200708/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	<title type="text" />
	<subtitle type="text">Add A Rule</subtitle>
	<id>https://api.datamarket.azure.com/amla/recommendations/v1/AddRule?apiVersion='1.0'</id>
	<rights type="text" />
	<updated>2014-11-05T11:13:28Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/AddRule?apiVersion='1.0'" />
	<entry>
		<id>https://api.datamarket.azure.com/amla/recommendations/v1/AddRule?apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
		<title type="text">AddARuleEntity</title>
		<updated>2014-11-05T11:13:28Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/AddRule?apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Id m:type="Edm.String">1000041</d:Id>
				<d:Type m:type="Edm.String">BlockList</d:Type>
				<d:Parameters m:type="Edm.String">{"ItemsToExclude":["1002"]}</d:Parameters>
			</m:properties>
		</content>
	</entry>
	</feed>

###6.3.ルールの削除
| HTTP メソッド | URI |
|:--------|:--------|
|GET |`<rootURI>/DeleteRule?modelId=%27<model_id>%27&filterId=%27<filter_Id>%27&apiVersion=%271.0%27`<br><br>例:<br>`DeleteRule?modelId=%2724024f7e-b45c-419e-bfa2-dfd947e0d253%27&filterId=%271000011%27&apiVersion=%271.0%27`|

|	パラメーター名 |	有効な値 |
|:--------			|:--------								|
|	modelId |	モデルの一意識別子。 |
|	filterId |	フィルターの一意識別子 |
|	apiVersion | 1.0 |
|||
| 要求本文 | なし |

**応答**:

HTTP ステータス コード: 200

###6.4.すべてのルールの削除

| HTTP メソッド | URI |
|:--------|:--------|
|GET |`<rootURI>/DeleteAllRules?modelId=%27<model_id>%27&apiVersion=%271.0%27`<br><br>例:<br>`DeleteAllRules?modelId=%2724024f7e-b45c-419e-bfa2-dfd947e0d253%27&apiVersion=%271.0%27`|

|	パラメーター名 |	有効な値 |
|:--------			|:--------								|
|	modelId |	モデルの一意識別子。 |
|	apiVersion | 1.0 |
|||
| 要求本文 | なし |

**応答**:

HTTP ステータス コード: 200

##7.カタログ

###7.1.カタログ データのインポート

複数の呼び出しで同じモデルに複数のカタログ ファイルがアップロードされた場合は、新しいカタログ項目のみを挿入します。既存の項目は、元の値でそのまま残ります。このメソッドを使用してカタログ データを更新できません。

| HTTP メソッド | URI |
|:--------|:--------|
|POST |`<rootURI>/ImportCatalogFile?modelId=%27<modelId>%27&filename=%27<fileName>%27&apiVersion=%271.0%27`<br><br>例:<br>`<rootURI>/ImportCatalogFile?modelId=%27a658c626-2baa-43a7-ac98-f6ee26120a12%27&filename=%27catalog10_small.txt%27&apiVersion=%271.0%27`|

|	パラメーター名 |	有効な値 |
|:--------			|:--------								|
|	modelId |	モデルの一意識別子。 |
| filename | カタログを表すテキスト形式の識別子。<br>英字 (A～Z、a～z)、数字 (0～9)、ハイフン (-)、アンダー スコア (_) のみが許可されます。<br>最大長: 50 |
|	apiVersion | 1.0 |
|||
| 要求本文 | カタログ データ。形式:<br>`<Item Id>,<Item Name>,<Item Category>[,<description>]`<br><br><table><tr><th>名前</th><th>必須</th><th>タイプ</th><th>説明</th></tr><tr><td>項目 ID</td><td>あり</td><td>英数字、最大の長さ 50</td><td>項目の一意識別子</td></tr><tr><td>項目名</td><td>あり</td><td>英数字、最大の長さ 255</td><td>、項目名</td></tr><tr><td>項目カテゴリ</td><td>あり</td><td>英数字、最大の長さ 255</td><td>この項目 (例: 料理ブック、ドラマ...) が属しているカテゴリ</td></tr><tr><td>説明</td><td>いいえ</td><td>英数字、最大の長さ 4000</td><td>この項目の説明</td></tr></table><br>最大ファイル サイズ 200 MB<br><br>例:<br><pre>2406e770-769c-4189-89de-1c9283f93a96,Clara Callan,Book<br>21bf8088-b6c0-4509-870c-e1c7ac78304a,The Forgetting Room: A Fiction (Byzantium Book),Book<br>3bb5cb44-d143-4bdd-a55c-443964bf4b23,Spadework,Book<br>552a1940-21e4-4399-82bb-594b46d7ed54,Restraint of Beasts,Book</pre> |


**応答**:

HTTP ステータス コード: 200

OData XML

	feed\entry\content\properties\LineCount – number of lines accepted
	feed\entry\content\properties\ErrorCount – number of lines that were not inserted due to an error

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v1/ImportCatalogFile" xmlns:d="http://schemas.microsoft.com/ado/200708/dataservices" xmlns:m="http://schemas.microsoft.com/ado/200708/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	<title type="text" />
	<subtitle type="text">Import catalog file</subtitle>
	<id>https://api.datamarket.azure.com/amla/recommendations/v1/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'</id>
	<rights type="text" />
	<updated>2014-10-05T06:58:04Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'" />
	<entry>
   	<id>https://api.datamarket.azure.com/amla/recommendations/v1/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
		<title type="text">ImportCatalogFileEntity2</title>
		<updated>2014-10-05T06:58:04Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:LineCount m:type="Edm.String">4</d:LineCount>
				<d:ErrorCount m:type="Edm.String">0</d:ErrorCount>
			</m:properties>
		</content>
	</entry>
	</feed>

###7.2.カタログの取得
すべてのカタログ項目を取得します。

| HTTP メソッド | URI |
|:--------|:--------|
|GET |`<rootURI>/GetCatalog?modelId=%27<model_id>%27&apiVersion=%271.0%27`<br><br>例:<br>`GetCatalog?modelId=%2724024f7e-b45c-419e-bfa2-dfd947e0d253%27&apiVersion=%271.0%27`|

|	パラメーター名 |	有効な値 |
|:--------			|:--------								|
|	modelId |	モデルの一意識別子。 |
|	apiVersion | 1.0 |
|||
| 要求本文 | なし |

**応答**:

HTTP ステータス コード: 200

OData XML

	The response includes one entry per catalog item, each entry has the following data:

	feed/entry/content/properties/ExternalId – Catalog item external Id, the one provided by the customer
	feed/entry/content/properties/InternalId – Catalog item internal Id, the one that Azure ML Recommendations has generated
	feed/entry/content/properties/Name – Catalog item name
	feed/entry/content/properties/Category – Catalog item category
	feed/entry/content/properties/Description – Catalog item description
	feed/entry/content/properties/Metadata – Catalog item metadata

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v1/GetCatalog" xmlns:d="http://schemas.microsoft.com/ado/200708/dataservices" xmlns:m="http://schemas.microsoft.com/ado/200708/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
		<title type="text" />
		<subtitle type="text">Get All Catalog Items</subtitle>
		<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'</id>
		<rights type="text" />
		<updated>2014-10-29T11:13:26Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'" />
		<entry>
        	<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
		<title type="text">AllCatalogItemsEntity</title>
		<updated>2014-10-29T11:13:26Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:ExternalId m:type="Edm.String">552A1940-21E4-4399-82BB-594B46D7ED54</d:ExternalId>
				<d:InternalId m:type="Edm.String">060db26a-e6a6-464e-bb52-436d2da82a50</d:InternalId>
				<d:Name m:type="Edm.String">Restraint of Beasts</d:Name>
				<d:Category m:type="Edm.String">Book</d:Category>
				<d:Description m:type="Edm.String"></d:Description>
				<d:Metadata m:type="Edm.String"></d:Metadata>
			</m:properties>
		</content>
	</entry>
	<entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
		<title type="text">AllCatalogItemsEntity</title>
		<updated>2014-10-29T11:13:26Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:ExternalId m:type="Edm.String">2406E770-769C-4189-89DE-1C9283F93A96</d:ExternalId>
				<d:InternalId m:type="Edm.String">209d7bfe-2eb9-4455-92a3-7c867a41a74a</d:InternalId>
				<d:Name m:type="Edm.String">Clara Callan</d:Name>
				<d:Category m:type="Edm.String">Book</d:Category>
				<d:Description m:type="Edm.String"></d:Description>
				<d:Metadata m:type="Edm.String"></d:Metadata>
			</m:properties>
		</content>
	</entry>
	<entry>
		<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
		<title type="text">AllCatalogItemsEntity</title>
		<updated>2014-10-29T11:13:26Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:ExternalId m:type="Edm.String">3BB5CB44-D143-4BDD-A55C-443964BF4B23</d:ExternalId>
				<d:InternalId m:type="Edm.String">913ff79b-059b-4d4d-8042-6fa4cc1391dd</d:InternalId>
				<d:Name m:type="Edm.String">Spadework</d:Name>
				<d:Category m:type="Edm.String">Book</d:Category>
				<d:Description m:type="Edm.String"></d:Description>
				<d:Metadata m:type="Edm.String"></d:Metadata>
			</m:properties>
		</content>
	</entry>
	<entry>
    		<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1</id>
		<title type="text">AllCatalogItemsEntity</title>
		<updated>2014-10-29T11:13:26Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:ExternalId m:type="Edm.String">21BF8088-B6C0-4509-870C-E1C7AC78304A</d:ExternalId>
				<d:InternalId m:type="Edm.String">ea65e4fa-768c-40b4-92c3-69d3e8178691</d:InternalId>
				<d:Name m:type="Edm.String">The Forgetting Room: A Fiction (Byzantium Book)</d:Name>
				<d:Category m:type="Edm.String">Book</d:Category>
				<d:Description m:type="Edm.String"></d:Description>
				<d:Metadata m:type="Edm.String"></d:Metadata>
			</m:properties>
		</content>
	</entry>
	</feed>

###7.3.トークンによるカタログ項目の取得

| HTTP メソッド | URI |
|:--------|:--------|
|GET |`<rootURI>/GetCatalogItemsByToken?modelId=%27<modelId>%27&token=%27<token>%27&apiVersion=%271.0%27`<br><br>例:<br>`GetCatalogItemsByToken?modelId=%270dbb55fa-7f11-418d-8537-8ff2d9d1d9c6%27&token=%27Cla%27&apiVersion=%271.0%27`|

|	パラメーター名 |	有効な値 |
|:--------			|:--------								|
|	modelId |	モデルの一意識別子。 |
|	token |	カタログ項目の名前のトークン。3 文字以上にする必要があります。 |
|	apiVersion | 1.0 |
|||
| 要求本文 | なし |

**応答**:

HTTP ステータス コード: 200

OData XML

	The response includes one entry per catalog item, each entry has the following data:

	feed/entry/content/properties/ExternalId – Catalog item external Id, the one provided by the customer
	feed/entry/content/properties/InternalId – Catalog item internal Id, the one that Azure ML Recommendations has generated
	feed/entry/content/properties/Name – Catalog item name
	feed/entry/content/properties/Category – Catalog item category
	feed/entry/content/properties/Description – Catalog item description
	feed/entry/content/properties/Metadata – Catalog item metadata

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v1/GetCatalogItemsByToken" xmlns:d="http://schemas.microsoft.com/ado/200708/dataservices" xmlns:m="http://schemas.microsoft.com/ado/200708/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
		<title type="text" />
		<subtitle type="text">Get Catalog items that contain a token</subtitle>
		<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetCatalogItemsByToken?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;token='Cla'&amp;apiVersion='1.0'</id>
		<rights type="text" />
		<updated>2014-10-29T11:48:19Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetCatalogItemsByToken?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;token='Cla'&amp;apiVersion='1.0'" />
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetCatalogItemsByToken?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;token='Cla'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
			<title type="text">CatalogItemsThatContainATokenEntity</title>
			<updated>2014-10-29T11:48:19Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetCatalogItemsByToken?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;token='Cla'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    		<content type="application/xml">
      			<m:properties>
					<d:Id m:type="Edm.String">2406E770-769C-4189-89DE-1C9283F93A96</d:Id>
        			<d:Name m:type="Edm.String">Clara Callan</d:Name>
        			<d:Rating m:type="Edm.Double">0</d:Rating>
        			<d:Reasoning m:type="Edm.String"></d:Reasoning>
        			<d:Metadata m:type="Edm.String"></d:Metadata>
        			<d:FormattedRating m:type="Edm.Double" m:null="true"></d:FormattedRating>
      			</m:properties>
			</content>
		</entry>
	</feed>

##8.使用状況データ
###8.1.使用状況データのインポート
####8.1.1.ファイルのアップロード
このセクションでは、ファイルを使用して使用状況データをアップロードする方法を示します。この API は使用状況データと共に何度も呼び出すことができます。すべての呼び出しですべての使用状況データが保存されます。

| HTTP メソッド | URI |
|:--------|:--------|
|POST |`<rootURI>/ImportUsageFile?modelId=%27<modelId>%27&filename=%27<fileName>%27&apiVersion=%271.0%27`<br><br>例:<br>`<rootURI>/ImportUsageFile?modelId=%27a658c626-2baa-43a7-ac98-f6ee26120a12%27&filename=%27ImplicitMatrix10_Guid_small.txt%27&apiVersion=%271.0%27`|

|	パラメーター名 |	有効な値 |
|:--------			|:--------								|
|	modelId |	モデルの一意識別子。 |
| filename | カタログを表すテキスト形式の識別子。<br>英字 (A～Z、a～z)、数字 (0～9)、ハイフン (-)、アンダー スコア (_) のみが許可されます。<br>最大長: 50 |
|	apiVersion | 1.0 |
|||
| 要求本文 | 利用状況データ。形式:<br>`<User Id>,<Item Id>[,<Time>,<Event>]`<br><br><table><tr><th>名前</th><th>必須</th><th>型</th><th>説明</th></tr><tr><td>ユーザー ID</td><td>はい</td><td>英字</td><td>ユーザーの一意識別子</td></tr><tr><td>アイテム ID</td><td>はい</td><td>英字、最大の長さ 50</td><td>項目の一意識別子</td></tr><tr><td>時間</td><td>いいえ</td><td>次の形式の日付: YYYY/MM/DDTHH:MM:SS (例. 2013/06/20T10:00:00)</td><td>データの時間</td></tr><tr><td>イベント</td><td>いいえ、指定した場合は日付も指定すること</td><td>次のいずれか:<br>• Click<br>• RecommendationClick<br>• AddShopCart<br>• RemoveShopCart<br>• Purchase</td><td></td></tr></table><br>最大ファイルサイズ 200MB<br><br>例:<br><pre>149452,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>6360,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>50321,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>71285,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>224450,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>236645,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>107951,1b3d95e2-84e4-414c-bb38-be9cf461c347</pre> |

**応答**:

HTTP ステータス コード: 200

OData XML

	Feed\entry\content\properties\LineCount – number of lines accepted
	Feed\entry\content\properties\ErrorCount – number of lines that were not inserted due to an error
	Feed\entry\content\properties\FileId – the file identifier


	<feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ImportUsageFile" xmlns:d="http://schemas.microsoft.com/ado/200708/dataservices" xmlns:m="http://schemas.microsoft.com/ado/200708/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
  	<title type="text" />
  	<subtitle type="text">Add bulk usage data (usage file)</subtitle>
  	<id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'</id>
  	<rights type="text" />
  	<updated>2014-10-05T07:21:44Z</updated>
  	<link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'" />
  	<entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">AddBulkUsageDataUsageFileEntity2</title>
    <updated>2014-10-05T07:21:44Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:LineCount m:type="Edm.String">33</d:LineCount>
        <d:ErrorCount m:type="Edm.String">0</d:ErrorCount>
        <d:FileId m:type="Edm.String">fead7c1c-db01-46c0-872f-65bcda36025d</d:FileId>
      </m:properties>
    </content>
  	</entry>
	</feed>


####8.1.2.データ取得の使用
このセクションでは、Web サイトから Azure ML の Recommendations にリアルタイムでイベントを送信する方法を示します。

| HTTP メソッド | URI |
|:--------|:--------|
|POST |`<rootURI>/AddUsageEvent?apiVersion=%271.0%27-f6ee26120a12%27&filename=%27catalog10_small.txt%27&apiVersion=%271.0%27`|

|	パラメーター名 |	有効な値 |
|:--------			|:--------								|
|	apiVersion | 1.0 |

要求本文

	Event data entry for each event you want to send. You should send for the same user or browser session the same id in the SessionId field.


	Example of Click:
	<Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  	<ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
  	<SessionId>11112222</SessionId>
  	<EventData>
    <EventData>
      <Name>Click</Name>
      <ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
    </EventData>
  	</EventData>
	</Event> 


	Example of Recommendation Click:
	<Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  	<ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
  	<SessionId>11112222</SessionId>
  	<EventData>
    <EventData>
      <Name>RecommendationClick</Name>
      <ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
    </EventData>
  	</EventData>
	</Event>


	Example of Adding to shop cart:
	<Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  	<ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
  	<SessionId>11112222</SessionId>
  	<EventData>
    <EventData>
      <Name>AddShopCart</Name>
      <ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
    </EventData>
  	</EventData>
	</Event>

	Example of Removing from shop cart:
	<Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  	<ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
  	<SessionId>11112222</SessionId>
  	<EventData>
    <EventData>
      <Name>RemoveShopCart</Name>
      <ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
    </EventData>
  	</EventData>
	</Event>

	Example of Purchase:
	<Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">   
	<ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>   
	<SessionId>11112222</SessionId>   
	<EventData>     
	<EventData>       
		<Name>Purchase</Name> 
		<PurchaseItems>
			<PurchaseItems>
				<ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>       
				<Count>3</Count>
			</PurchaseItems>
		</PurchaseItems>
	</EventData>     
	</EventData> 
	</Event>

	Example of sending 2 events “Click” and “AddShopCart:
	<Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  	<ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
  	<SessionId>11112222</SessionId>
  	<EventData>
    <EventData>
      <Name>Click</Name>
      <ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
      <ItemName>itemName</ItemName>
      <ItemDescription>item description</ItemDescription>
      <ItemCategory>category</ItemCategory>
    </EventData>
    <EventData>
      <Name>AddShopCart</Name>
      <ItemId>552A1940-21E4-4399-82BB-594B46D7ED54</ItemId>
    </EventData>
  	</EventData>
	</Event>

**応答**:
HTTP 状態コード: 200

###8.2.モデルの使用状況ファイルの一覧
すべてのモデルに関する使用状況ファイルのメタデータを取得します。

| HTTP メソッド | URI |
|:--------|:--------|
|GET |`<rootURI>/ListModelUsageFiles?forModelId=%27<model_id>%27&apiVersion=%271.0%27`<br><br>例:<br>`<rootURI>/ListModelUsageFiles?forModelId=%270dbb55fa-7f11-418d-8537-8ff2d9d1d9c6%27&apiVersion=%271.0%27`|

|	パラメーター名 |	有効な値 |
|:--------			|:--------								|
|	forModelId |	モデルの一意識別子。 |
|	apiVersion | 1.0 |
|||
| 要求本文 | なし |

**応答**:

HTTP ステータス コード: 200

OData XML

	The response includes one entry per usage file, each entry has the following data:
	feed\entry\content\properties\Id – Usage file Id
	feed\entry\content\properties\Length – Usage file Length in MB
	feed\entry\content\properties\DateModified – Date when Usage file was created
	feed\entry\content\properties\UseInModel – Is usage files used in model

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v1/ListModelUsageFiles" xmlns:d="http://schemas.microsoft.com/ado/200708/dataservices" xmlns:m="http://schemas.microsoft.com/ado/200708/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
		<title type="text" />
		<subtitle type="text">Get a list of model's usage files info</subtitle>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v1/ListModelUsageFiles?forModelId='1c1110f8-7d9f-4c64-a807-4c9c5329993a'&amp;apiVersion='1.0'</id>
		<rights type="text" />
		<updated>2014-10-30T09:40:25Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/ListModelUsageFiles?forModelId='1c1110f8-7d9f-4c64-a807-4c9c5329993a'&amp;apiVersion='1.0'" />
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v1/ListModelUsageFiles?forModelId='1c1110f8-7d9f-4c64-a807-4c9c5329993a'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
			<title type="text">GetAListOfModelsUsageFilesInfoEntity</title>
			<updated>2014-10-30T09:40:25Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/ListModelUsageFiles?forModelId='1c1110f8-7d9f-4c64-a807-4c9c5329993a'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Id m:type="Edm.String">4c067b42-e975-4cb2-8c98-a6ab80ed6d63</d:Id>
					<d:Length m:type="Edm.Double">0</d:Length>
					<d:DateModified m:type="Edm.String">10/30/2014 9:19:57 AM</d:DateModified>
					<d:UseInModel m:type="Edm.String">true</d:UseInModel>
				</m:properties>
			</content>
		</entry>
	<entry>
		<id>https://api.datamarket.azure.com/amla/recommendations/v1/ListModelUsageFiles?forModelId='1c1110f8-7d9f-4c64-a807-4c9c5329993a'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
		<title type="text">GetAListOfModelsUsageFilesInfoEntity</title>
		<updated>2014-10-30T09:40:25Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/ListModelUsageFiles?forModelId='1c1110f8-7d9f-4c64-a807-4c9c5329993a'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Id m:type="Edm.String">3126d816-4e80-4248-8339-1ebbdb9d544d</d:Id>
				<d:Length m:type="Edm.Double">0.001</d:Length>
				<d:DateModified m:type="Edm.String">10/30/2014 9:21:44 AM</d:DateModified>
				<d:UseInModel m:type="Edm.String">true</d:UseInModel>
          	</m:properties>
		</content>
	</entry>
</feed>

###8.3.使用状況の統計の取得
使用状況の統計を取得します。

| HTTP メソッド | URI |
|:--------|:--------|
|GET |`<rootURI>/GetUsageStatistics?modelId=%27<modelId>%27& startDate=%27<date>%27&endDate=%27<date>%27&eventTypes=%27<types>%27&apiVersion=%271.0%27`<br><br>例:<br>`<rootURI>/GetUsageStatistics?modelId=%271d20c34f-dca1-4eac-8e5d-f299e4e4ad66%27&startDate=%272014%2F10%2F17T00%3A00%3A00%27&endDate=%272014%2F11%2F16T00%3A00%3A00%27&eventTypes=%271%2C2%2C3%2C4%2C5%27&apiVersion=%271.0%27`|

|	パラメーター名 |	有効な値 |
|:--------			|:--------								|
| modelId |	モデルの一意識別子。 |
| startDate |	開始日。形式: yyyy/MM/ddTHH:mm:ss |
| endDate |	終了日。形式: yyyy/MM/ddTHH:mm:ss |
| eventTypes |	イベント種類のコンマ区切りの文字列。すべてのイベントを取得する場合は null 値。 |
| apiVersion | 1.0 |
|||
| 要求本文 | なし |

**応答**:

HTTP ステータス コード: 200

OData XML

	A collection of key/value that each one contains the sum of events for a specific event type grouped by hour.
	The key:
		feed\entry[i]\content\properties\Key – contains the time (grouped by hours) and the event type.
	The value:
		feed\entry[i]\content\properties\Value – total event count

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v1/GetUsageStatistics" xmlns:d="http://schemas.microsoft.com/ado/200708/dataservices" xmlns:m="http://schemas.microsoft.com/ado/200708/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
		<title type="text" />
		<subtitle type="text">Get usage statistics</subtitle>
        <id>https://api.datamarket.azure.com/amla/recommendations/v1/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'</id>
	<rights type="text" />
	<updated>2014-11-18T11:39:16Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'" />
	<entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
		<title type="text">GetUsageStatisticsEntity</title>
		<updated>2014-11-18T11:39:16Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Event m:type="Edm.String">11/9/2014 8:00:00 AM;Click</d:Event>
				<d:Value m:type="Edm.String">5</d:Value>
			</m:properties>
		</content>
	</entry>
	<entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
		<title type="text">GetUsageStatisticsEntity</title>
		<updated>2014-11-18T11:39:16Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Event m:type="Edm.String">11/9/2014 8:00:00 AM;RecommendationClick</d:Event>
				<d:Value m:type="Edm.String">10</d:Value>
          	</m:properties>
		</content>
	</entry>
	<entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
		<title type="text">GetUsageStatisticsEntity</title>
		<updated>2014-11-18T11:39:16Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
        <content type="application/xml">
			<m:properties>
				<d:Event m:type="Edm.String">11/1/2014 8:00:00 AM;RemoveShopCart</d:Event>
            	<d:Value m:type="Edm.String">10</d:Value>
			</m:properties>
        </content>
	</entry>
	<entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1</id>
		<title type="text">GetUsageStatisticsEntity</title>
		<updated>2014-11-18T11:39:16Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Event m:type="Edm.String">11/5/2014 8:00:00 AM;RemoveShopCart</d:Event>
				<d:Value m:type="Edm.String">10</d:Value>
			</m:properties>
		</content>
	</entry>
	</feed>

###8.4.使用状況ファイルのサンプルの取得
使用状況ファイルの内容の最初の 2 KB を取得します。

| HTTP メソッド | URI |
|:--------|:--------|
|GET |`<rootURI>/GetUsageFileSample?modelId=%27<modelId>%27& fileId=%27<fileId>%27&apiVersion=%271.0%27`<br><br>例:<br>`<rootURI>/GetUsageFileSample?modelId=%271c1110f8-7d9f-4c64-a807-4c9c5329993a%27&fileId=%274c067b42-e975-4cb2-8c98-a6ab80ed6d63%27&apiVersion=%271.0%27`|

|	パラメーター名 |	有効な値 |
|:--------			|:--------								|
| modelId |	モデルの一意識別子。 |
| フィールド |	モデルの使用状況ファイルの一意識別子。 |
| apiVersion | 1.0 |
|||
| 要求本文 | なし |

**応答**:

HTTP ステータス コード: 200

OData XML<br>
応答は未加工のテキスト形式で返されます。<pre> 85526,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1
210926,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1
116866,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1
177458,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1
274004,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1
123883,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1
37712,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1
152249,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1
250948,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1
235588,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1
158254,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1
271195,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1
141157,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1
171118,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1
225087,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1
</pre>

###8.5.モデルの使用状況ファイルの取得
使用状況ファイルのすべての内容を取得します。

| HTTP メソッド | URI |
|:--------|:--------|
|GET |`<rootURI>/GetModelUsageFile?mid=%27<modelId>%27& fid=%27<fileId>%27&download=%27<download_value>%27&apiVersion=%271.0%27`<br><br>例:<br>`<rootURI>/GetModelUsageFile?mid=%271c1110f8-7d9f-4c64-a807-4c9c5329993a%27&fid=%273126d816-4e80-4248-8339-1ebbdb9d544d%27&download=%271%27&apiVersion=%271.0%27`|

|	パラメーター名 |	有効な値 |
|:--------			|:--------								|
| mid |	モデルの一意識別子。 |
| fid |	モデルの使用状況ファイルの一意識別子。 |
| ダウンロード | 1 |
| apiVersion | 1.0 |
|||
| 要求本文 | なし |

**応答**:

HTTP ステータス コード: 200

OData XML<br>
応答は未加工のテキスト形式で返されます。<pre> 85526,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1
210926,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1
116866,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1
177458,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1
274004,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1
123883,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1
37712,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1
152249,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1
250948,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1
235588,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1
158254,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1
271195,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1
141157,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1
171118,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1
225087,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1
244881,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1
50547,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1
213090,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1
260655,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1
72214,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1
189334,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1
36326,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1
189336,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1
189334,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1
260655,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1
162100,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1
54946,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1
260965,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1
102758,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1
112602,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1
163925,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1
262998,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1
144717,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1
</pre>

###8.6使用状況ファイルの削除
指定されたモデルの使用状況ファイルを削除します。

| HTTP メソッド | URI |
|:--------|:--------|
|削除 |`<rootURI>/DeleteUsageFile?modelId=%27<modelId>%27&fileId=%27<fileId>%27&apiVersion=%271.0%27`<br><br>例:<br>`<rootURI>/DeleteUsageFile?modelId=%270f86d698-d0f4-4406-a684-d13d22c47a73%27&fileId=%27f2e0b09d-be5c-46b2-9ac2-c7f622e5e1a5%27&apiVersion=%271.0%27`|

| パラメーター名 |	有効な値 |
|:--------			|:--------								|
| modelId |	モデルの一意識別子。 |
| フィールド | 削除するファイルの一意識別子 |
| apiVersion | 1.0 |
|||
| 要求本文 | なし |

**応答**:

HTTP ステータス コード: 200


###8.7.すべての使用状況ファイルの削除
すべてのモデルの使用状況ファイルを削除します。

| HTTP メソッド | URI |
|:--------|:--------|
|削除 |`<rootURI>/DeleteAllUsageFiles?modelId=%27<modelId>%27&apiVersion=%271.0%27`<br><br>例:<br>`<rootURI>/DeleteAllUsageFiles?modelId=%271c1110f8-7d9f-4c64-a807-4c9c5329993a%27&apiVersion=%271.0%27`|

| パラメーター名 |	有効な値 |
|:--------			|:--------								|
| modelId |	モデルの一意識別子。 |
| apiVersion | 1.0 |
|||
| 要求本文 | なし |

**応答**:

HTTP ステータス コード: 200

##9.構築

###9.1.モデルのビルド

| HTTP メソッド | URI |
|:--------|:--------|
|POST |`<rootURI>/BuildModel?modelId=%27<modelId>%27&userDescription=%27<description>%27&apiVersion=%271.0%27`<br><br>例:<br>`<rootURI>/BuildModel?modelId=%27a658c626-2baa-43a7-ac98-f6ee26120a12%27&userDescription=%27First%20build%27&apiVersion=%271.0%27`|

|	パラメーター名 |	有効な値 |
|:--------			|:--------								|
| modelId |	モデルの一意識別子。 |
| userDescription | カタログを表すテキスト形式の識別子。空白を使用する場合は、%20 にエンコードする必要があることに注意してください上記の例をご覧ください。<br>最大長: 50 |
| apiVersion | 1.0 |
|||
| 要求本文 | 空のままの場合、ビルドは既定のビルド パラメーターで実行されます。<br><br>ビルド パラメーターを設定する場合、次のサンプルのように、パラメーターを XML として本文に入れて送信します (パラメーターの説明のビルド パラメーターの取得セクションをご覧ください)。<br>`<BuildParametersList xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"><NumberOfModelIterations>40</NumberOfModelIterations><NumberOfModelDimensions>20</NumberOfModelDimensions><UseFeaturesInModel>false</UseFeaturesInModel><AllowColdItemPlacement>false</AllowColdItemPlacement><MinItemAppearance>5</MinItemAppearance><MinUserAppearance>5</MinUserAppearance><EnableFeatureCorrelation>false</EnableFeatureCorrelation><RefreshFeatureScoreOnBuild>false</RefreshFeatureScoreOnBuild><ComputeUpd>false</ComputeUpd><EnableModelingInsights>true</EnableModelingInsights><ModelingFeatureList /><ReasoningFeatureList /></BuildParametersList>`|

**応答**:

HTTP ステータス コード: 200

OData XML

	This is an asynchronous API. You will get a Build Id as a response. To know when the build has ended, you should call the “Get Builds Status of a Model” API and locate this build Id in the response. Note that a build can take from minutes to hours depending on the size of the data.
	You cannot consume recommendations till the build ends.

	Valid build status:

	* Create – model was created
	* Queued – model build was triggered and it is queued
	* Building – the model is being build
	* Success – the build ended successfully
	* Error – the build ended with a failure
	* Cancelled – build was canceled
	* Cancelling – build is being cancelled

	Feed\entry\content\properties\Id – contains the build id

	<feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/BuildModel" xmlns:d="http://schemas.microsoft.com/ado/200708/dataservices" xmlns:m="http://schemas.microsoft.com/ado/200708/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
  	<title type="text" />
  	<subtitle type="text">Build a Model with RequestBody</subtitle>
  	<id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First build'&amp;apiVersion='1.0'</id>
  	<rights type="text" />
  	<updated>2014-10-05T08:56:34Z</updated>
  	<link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First%20build'&amp;apiVersion='1.0'" />
  	<entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First build'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">BuildAModelEntity2</title>
    <updated>2014-10-05T08:56:34Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First%20build'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">1000272</d:Id>
        <d:UserName m:type="Edm.String"></d:UserName>
        <d:ModelId m:type="Edm.String">9559872f-7a53-4076-a3c7-19d9385c1265</d:ModelId>
        <d:ModelName m:type="Edm.String">docTest</d:ModelName>
        <d:Type m:type="Edm.String">Recommendation</d:Type>
        <d:CreationTime m:type="Edm.String">2014-10-05T08:56:31.893</d:CreationTime>
        <d:Progress_BuildId m:type="Edm.String">1000272</d:Progress_BuildId>
        <d:Progress_ModelId m:type="Edm.String">9559872f-7a53-4076-a3c7-19d9385c1265</d:Progress_ModelId>
        <d:Progress_UserName m:type="Edm.String">5-4058-ab36-1fe254f05102@dm.com</d:Progress_UserName>
        <d:Progress_IsExecutionStarted m:type="Edm.String">false</d:Progress_IsExecutionStarted>
        <d:Progress_IsExecutionEnded m:type="Edm.String">false</d:Progress_IsExecutionEnded>
        <d:Progress_Percent m:type="Edm.String">0</d:Progress_Percent>
        <d:Progress_StartTime m:type="Edm.String">0001-01-01T00:00:00</d:Progress_StartTime>
        <d:Progress_EndTime m:type="Edm.String">0001-01-01T00:00:00</d:Progress_EndTime>
        <d:Progress_UpdateDateUTC m:type="Edm.String"></d:Progress_UpdateDateUTC>
        <d:Status m:type="Edm.String">Queued</d:Status>
        <d:Key1 m:type="Edm.String">UseFeaturesInModel</d:Key1>
        <d:Value1 m:type="Edm.String">False</d:Value1>
      </m:properties>
    </content>
  	</entry>
	</feed>

###9.2.モデルのビルド状態の取得
指定したモデルのビルドとそれらの状態を取得します。

| HTTP メソッド | URI |
|:--------|:--------|
|GET |`<rootURI>/GetModelBuildsStatus?modelId=%27<modelId>%27&onlyLastBuild=<bool>&apiVersion=%271.0%27`<br><br>例:<br>`<rootURI>/GetModelBuildsStatus?modelId=%279559872f-7a53-4076-a3c7-19d9385c1265%27&onlyLastBuild=true&apiVersion=%271.0%27`|


|	パラメーター名 |	有効な値 |
|:--------			|:--------								|
|	modelId |	モデルの一意識別子。 |
|	onlyLastBuild |	モデルのすべてのビルド履歴を返すか、最新のビルドの状態のみを返すかを示します。 |
|	apiVersion |	1.0 |


**応答**:

HTTP ステータス コード: 200

OData XML

	The response includes one entry per build, each entry has the following data:

	feed/entry/content/properties/UserName – the name of the user
	feed/entry/content/properties/ModelName – the name of the model
	feed/entry/content/properties/ModelId – the model unique identifier
	feed/entry/content/properties/IsDeployed – is the build deployed
	feed/entry/content/properties/BuildId – the build unique identifier
	feed/entry/content/properties/Status – build status. Can be one of the following: Error, Building, Queued, Cancelled, Cancelling, Success
	feed/entry/content/properties/StatusMessage – detailed status message (applies only to specific states)
	feed/entry/content/properties/Progress – build progress (%)
	feed/entry/content/properties/StartTime – Build start time
	feed/entry/content/properties/EndTime – Build end time
	feed/entry/content/properties/ExecutionTime – Build duration
	feed/entry/content/properties/ProgressStep – details about the current stage that a build in progress is in.

    Valid build status:
	* Create – model was created
	* Queued – model build was triggered and it is queued
	* Building – the model is being build
	* Success – the build ended successfully
	* Error – the build ended with a failure
	* Cancelled – build was canceled
	* Cancelling – build is being cancelled

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v1/GetModelBuildsStatus" xmlns:d="http://schemas.microsoft.com/ado/200708/dataservices" xmlns:m="http://schemas.microsoft.com/ado/200708/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
		<title type="text" />
		<subtitle type="text">Get builds status of a model</subtitle>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'</id>
		<rights type="text" />
		<updated>2014-11-05T17:51:10Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'" />
		<entry>
    		<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
			<title type="text">GetBuildsStatusEntity</title>
			<updated>2014-11-05T17:51:10Z</updated>
    		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:UserName m:type="Edm.String">b-434e-b2c9-84935664ff20@dm.com</d:UserName>
					<d:ModelName m:type="Edm.String">ModelName</d:ModelName>
					<d:ModelId m:type="Edm.String">1d20c34f-dca1-4eac-8e5d-f299e4e4ad66</d:ModelId>
					<d:IsDeployed m:type="Edm.String">true</d:IsDeployed>
					<d:BuildId m:type="Edm.String">1000272</d:BuildId>
					<d:Status m:type="Edm.String">Success</d:Status>
					<d:StatusMessage m:type="Edm.String"></d:StatusMessage>
					<d:Progress m:type="Edm.String">0</d:Progress>
					<d:StartTime m:type="Edm.String">2014-11-02T13:43:51</d:StartTime>
					<d:EndTime m:type="Edm.String">2014-11-02T13:45:10</d:EndTime>
					<d:ExecutionTime m:type="Edm.String">00:01:19</d:ExecutionTime>
					<d:IsExecutionStarted m:type="Edm.String">false</d:IsExecutionStarted>
					<d:ProgressStep m:type="Edm.String"></d:ProgressStep>
				</m:properties>
			</content>
		</entry>
	</feed>


###9.3.ユーザーのビルド状態の取得
ユーザーのすべてのモデルに関するビルド状態を取得します。

| HTTP メソッド | URI |
|:--------|:--------|
|GET |`<rootURI>/GetUserBuildsStatus?onlyLastBuilds=<bool>&apiVersion=%271.0%27`<br><br>例:<br>`<rootURI>/GetUserBuildsStatus?onlyLastBuilds=true&apiVersion=%271.0%27`|


|	パラメーター名 |	有効な値 |
|:--------			|:--------								|
|	onlyLastBuild |	モデルのすべてのビルド履歴を返すか、最新のビルドの状態のみを返すかを示します。 |
|	apiVersion |	1.0 |


**応答**:

HTTP ステータス コード: 200

OData XML

	The response includes one entry per build, each entry has the following data:

	feed/entry/content/properties/UserName – the name of the user
	feed/entry/content/properties/ModelName – the name of the model
	feed/entry/content/properties/ModelId – the model unique identifier
	feed/entry/content/properties/IsDeployed – is the build deployed
	feed/entry/content/properties/BuildId – the build unique identifier
	feed/entry/content/properties/Status – build status. Can be one of the following: Error, Building, Queued, Cancelled, Cancelling, Success
	feed/entry/content/properties/StatusMessage – detailed status message (applies only to specific states)
	feed/entry/content/properties/Progress – build progress (%)
	feed/entry/content/properties/StartTime – Build start time
	feed/entry/content/properties/EndTime – Build end time
	feed/entry/content/properties/ExecutionTime – Build duration
	feed/entry/content/properties/ProgressStep – details about the current stage that a build in progress is in.

    Valid build status:
	* Create – model was created
	* Queued – model build was triggered and it is queued
	* Building – the model is being build
	* Success – the build ended successfully
	* Error – the build ended with a failure
	* Cancelled – build was canceled
	* Cancelling – build is being cancelled

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v1/GetUserBuildsStatus" xmlns:d="http://schemas.microsoft.com/ado/200708/dataservices" xmlns:m="http://schemas.microsoft.com/ado/200708/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
		<title type="text" />
		<subtitle type="text">Get builds status of a user</subtitle>
		<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetUserBuildsStatus?onlyLastBuilds=False&amp;apiVersion='1.0'</id>
		<rights type="text" />
		<updated>2014-11-05T18:41:21Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetUserBuildsStatus?onlyLastBuilds=False&amp;apiVersion='1.0'" />
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetUserBuildsStatus?onlyLastBuilds=False&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
			<title type="text">GetBuildsStatusEntity</title>
			<updated>2014-11-05T18:41:21Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetUserBuildsStatus?onlyLastBuilds=False&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:UserName m:type="Edm.String">b-434e-b2c9-84935664ff20@dm.com</d:UserName>
					<d:ModelName m:type="Edm.String">ModelName</d:ModelName>
					<d:ModelId m:type="Edm.String">1d20c34f-dca1-4eac-8e5d-f299e4e4ad66</d:ModelId>
					<d:IsDeployed m:type="Edm.String">true</d:IsDeployed>
					<d:BuildId m:type="Edm.String">1000272</d:BuildId>
					<d:Status m:type="Edm.String">Success</d:Status>
					<d:StatusMessage m:type="Edm.String"></d:StatusMessage>
					<d:Progress m:type="Edm.String">0</d:Progress>
					<d:StartTime m:type="Edm.String">2014-11-02T13:43:51</d:StartTime>
					<d:EndTime m:type="Edm.String">2014-11-02T13:45:10</d:EndTime>
					<d:ExecutionTime m:type="Edm.String">00:01:19</d:ExecutionTime>
					<d:IsExecutionStarted m:type="Edm.String">false</d:IsExecutionStarted>
					<d:ProgressStep m:type="Edm.String"></d:ProgressStep>
				</m:properties>
			</content>
		</entry>
	</feed>


###9.4.ビルドの削除
ビルドを削除します。

注意: アクティブなビルドは削除できません。削除する前に、そのモデルを別のアクティブなビルドに変更する必要があります。

| HTTP メソッド | URI |
|:--------|:--------|
|削除 |`<rootURI>/DeleteBuild?buildId=%27<buildId>%27&apiVersion=%271.0%27`<br><br>例:<br>`<rootURI>/DeleteBuild?buildId=%271500068%27&apiVersion=%271.0%27`|

|	パラメーター名 |	有効な値 |
|:--------			|:--------								|
| buildId | ビルドの一意識別子。 |
| apiVersion | 1.0 |

**応答:**

HTTP ステータス コード: 200

###9.5.ビルドのキャンセル
ビルド中の状態にあるビルドをキャンセルします。

| HTTP メソッド | URI |
|:--------|:--------|
|PUT |`<rootURI>/CancelBuild?buildId=%27<buildId>%27&apiVersion=%271.0%27`<br><br>例:<br>`<rootURI>/CancelBuild?buildId=%271500076%27&apiVersion=%271.0%27`|

|	パラメーター名 |	有効な値 |
|:--------			|:--------								|
| buildId | ビルドの一意識別子。 |
| apiVersion | 1.0 |

**応答:**

HTTP ステータス コード: 200

###9.6.ビルド パラメーターの取得
ビルド パラメーターを取得します。

| HTTP メソッド | URI |
|:--------|:--------|
|GET |`<rootURI>/GetBuildParameters?buildId=%27<buildId>%27&apiVersion=%271.0%27`<br><br>例:<br>`<rootURI>/GetBuildParameters?buildId=%271500068%27&apiVersion=%271.0%27`|

|	パラメーター名 |	有効な値 |
|:--------			|:--------								|
| buildId | ビルドの一意識別子。 |
| apiVersion | 1.0 |

**応答:**

HTTP ステータス コード: 200

OData XML

それぞれに 1 つのパラメーターが含まれるキーと値のコレクション。
<pre>
The key:
	feed/entry/content/properties/Key – Build parameter name
The value:
	feed/entry/content/properties/Value – Build parameter value
</pre>

次の表はそれぞれが表す値である各キーを示します。<table> <tr> <th>キー</th> <th>説明</th> <th>タイプ</th> <th>有効な値</th> </tr> <tr> <td>NumberOfModelIterations</td> <td>全体的なコンピューティング時間とモデルの精度によって、モデルで実行されるイテレーションの数。数が大きければそれだけ精度も増しますが、コンピューティング時間は長くかかります。</td> <td>整数</td> <td>10-50</td> </tr> <tr> <td>NumberOfModelDimensions</td> <td> モデルがデータ内で検出を試みる ’機能’ の数に関連する、ディメンションの数。ディメンションの数を増やすと、結果をより詳細に微調整して、小さいクラスターにすることができます。ただし、ディメンションが多すぎると、モデルが項目間の相関関係を検出できなくなります。</td> <td>整数</td> <td>10-40</td> </tr> <tr> <td>MinItemAppearance</td> <td> 項目がモデル内に含まれていない外観の最小数を制御できます。しきい値が高ければ、モデルから取得される結果もより適したものになります。ただし、しきい値が高すぎる場合、十分な項目を使用できなくなる可能性があります。</td> <td>整数</td> <td>0 以上</td> </tr> <tr> <td>MinUserAppearance</td> <td> ユーザーがモデル内に含まれていない外観の最小数を制御できます。しきい値が高ければ、モデルから取得される結果もより適したものになります。ただし、しきい値が高すぎる場合、十分な項目や特定ユーザーの推奨を使用できなくなる可能性があります。</td> <td>整数</td> <td>0-20</td> </tr> <tr> <td>説明</td> <td>、ビルドの説明</td> <td>文字列</td> <td>任意のテキスト、最大 255 文字</td> </tr> <tr> <td>UseFeaturesInModel</td> <td>将来使用するため</td> <td></td> <td></td> </tr> <tr> <td>AllowColdItemPlacement</td> <td>将来使用するため</td> <td></td> <td></td> </tr> <tr> <td>EnableFeatureCorrelation</td> <td>将来使用するため</td> <td></td> <td></td> </tr> <tr> <td>RefreshFeatureScoreOnBuild</td> <td>将来使用するため</td> <td></td> <td></td> </tr> <tr> <td>ComputeUpd</td> <td>将来使用するため</td> <td></td> <td></td> </tr> <tr> <td>EnableModelingInsights</td> <td>将来使用するため</td> <td></td> <td></td> </tr> <tr> <td>ModelingFeatureList</td> <td>将来使用するため</td> <td></td> <td></td> </tr> <tr> <td>ReasoningFeatureList</td> <td>将来使用するため</td> <td></td> <td></td> </tr> </table>

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v1/GetBuildParameters" xmlns:d="http://schemas.microsoft.com/ado/200708/dataservices" xmlns:m="http://schemas.microsoft.com/ado/200708/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
		<title type="text" />
		<subtitle type="text">Get build parameters</subtitle>
		<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetBuildParameters?buildId='1500075'&amp;apiVersion='1.0'</id>
		<rights type="text" />
		<updated>2014-11-02T11:23:53Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetBuildParameters?buildId='1500075'&amp;apiVersion='1.0'" />
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetBuildParameters?buildId='1500075'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2014-11-02T11:23:53Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetBuildParameters?buildId='1500075'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">UseFeaturesInModel</d:Key>
					<d:Value m:type="Edm.String">False</d:Value>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetBuildParameters?buildId='1500075'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2014-11-02T11:23:53Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetBuildParameters?buildId='1500075'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">AllowColdItemPlacement</d:Key>
					<d:Value m:type="Edm.String">False</d:Value>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetBuildParameters?buildId='1500075'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
    		<title type="text">GetBuildParametersEntity</title>
    		<updated>2014-11-02T11:23:53Z</updated>
    		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetBuildParameters?buildId='1500075'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
    		<content type="application/xml">
      			<m:properties>
        			<d:Key m:type="Edm.String">EnableFeatureCorrelation</d:Key>
        			<d:Value m:type="Edm.String">False</d:Value>
      			</m:properties>
    		</content>
  		</entry>
  		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetBuildParameters?buildId='1500075'&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2014-11-02T11:23:53Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetBuildParameters?buildId='1500075'&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1" />
			<content type="application/xml">
          		<m:properties>
        			<d:Key m:type="Edm.String">RefreshFeatureScoreOnBuild</d:Key>
        			<d:Value m:type="Edm.String">False</d:Value>
      			</m:properties>
    		</content>
  		</entry>
      	<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetBuildParameters?buildId='1500075'&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1</id>
        	<title type="text">GetBuildParametersEntity</title>
        	<updated>2014-11-02T11:23:53Z</updated>
        	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetBuildParameters?buildId='1500075'&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1" />
        	<content type="application/xml">
          		<m:properties>
            		<d:Key m:type="Edm.String">ComputeUpd</d:Key>
            		<d:Value m:type="Edm.String">False</d:Value>
          		</m:properties>
        	</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetBuildParameters?buildId='1500075'&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
    		<updated>2014-11-02T11:23:53Z</updated>
    		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetBuildParameters?buildId='1500075'&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">EnableModelingInsights</d:Key>
					<d:Value m:type="Edm.String">True</d:Value>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetBuildParameters?buildId='1500075'&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2014-11-02T11:23:53Z</updated>
        	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetBuildParameters?buildId='1500075'&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1" />
        	<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">NumberOfModelIterations</d:Key>
					<d:Value m:type="Edm.String">40</d:Value>
				</m:properties>
        	</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetBuildParameters?buildId='1500075'&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
        	<updated>2014-11-02T11:23:53Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetBuildParameters?buildId='1500075'&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">NumberOfModelDimensions</d:Key>
            		<d:Value m:type="Edm.String">20</d:Value>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetBuildParameters?buildId='1500075'&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2014-11-02T11:23:53Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetBuildParameters?buildId='1500075'&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">MinItemAppearance</d:Key>
					<d:Value m:type="Edm.String">5</d:Value>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetBuildParameters?buildId='1500075'&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2014-11-02T11:23:53Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetBuildParameters?buildId='1500075'&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">MinUserAppearance</d:Key>
					<d:Value m:type="Edm.String">5</d:Value>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetBuildParameters?buildId='1500075'&amp;apiVersion='1.0'&amp;$skip=10&amp;$top=1</id>
        	<title type="text">GetBuildParametersEntity</title>
        	<updated>2014-11-02T11:23:53Z</updated>
        	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetBuildParameters?buildId='1500075'&amp;apiVersion='1.0'&amp;$skip=10&amp;$top=1" />
        	<content type="application/xml">
          		<m:properties>
            		<d:Key m:type="Edm.String">ModelingFeatureList</d:Key>
            		<d:Value m:type="Edm.String"></d:Value>
          		</m:properties>
        	</content>
      	</entry>
      	<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetBuildParameters?buildId='1500075'&amp;apiVersion='1.0'&amp;$skip=11&amp;$top=1</id>
        	<title type="text">GetBuildParametersEntity</title>
        	<updated>2014-11-02T11:23:53Z</updated>
        	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetBuildParameters?buildId='1500075'&amp;apiVersion='1.0'&amp;$skip=11&amp;$top=1" />
        	<content type="application/xml">
          		<m:properties>
            		<d:Key m:type="Edm.String">ReasoningFeatureList</d:Key>
            		<d:Value m:type="Edm.String"></d:Value>
          		</m:properties>
        	</content>
      	</entry>
      	<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetBuildParameters?buildId='1500075'&amp;apiVersion='1.0'&amp;$skip=12&amp;$top=1</id>
        	<title type="text">GetBuildParametersEntity</title>
        	<updated>2014-11-02T11:23:53Z</updated>
        	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetBuildParameters?buildId='1500075'&amp;apiVersion='1.0'&amp;$skip=12&amp;$top=1" />
        	<content type="application/xml">
          		<m:properties>
            		<d:Key m:type="Edm.String">Description</d:Key>
            		<d:Value m:type="Edm.String"></d:Value>
          		</m:properties>
        	</content>
      	</entry>
	</feed>

##10.推奨
###10.1.推奨の取得

| HTTP メソッド | URI |
|:--------|:--------|
|GET |`<rootURI>/ItemRecommend?modelId=%27<modelId>%27&itemIds=%27<itemId>%27&numberOfResults=<int>&includeMetadata=<bool>&apiVersion=%271.0%27`<br><br>例:<br>`<rootURI>/ItemRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&itemIds=%271003%27&numberOfResults=10&includeMetadata=false&apiVersion=%271.0%27`|

|	パラメーター名 |	有効な値 |
|:--------			|:--------								|
| modelId | モデルの一意識別子。 |
| itemIds | <br> の推奨項目のコンマ区切りの一覧。最大の長さ: 200 |
| numberOfResults | 必要な結果の数。 |
| includeMetatadata | 将来的に利用 (常に false) |
| apiVersion | 1.0 |

**応答:**

HTTP ステータス コード: 200

OData XML

    The response includes one entry per recommended item, each entry has the following data:
		* Feed\entry\content\properties\Id – The recommended item id
		* Feed\entry\content\properties\Name – The name of the item 
		* Feed\entry\content\properties\Rating – The rating of the recommendation, higher number means higher confidence
		* Feed\entry\content\properties\Reasoning – the recommendation reasoning

    The example response below includes 10 recommended items:

	<feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ItemRecommend" xmlns:d="http://schemas.microsoft.com/ado/200708/dataservices" xmlns:m="http://schemas.microsoft.com/ado/200708/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
  	<title type="text" />
 	 <subtitle type="text">Get Recommendation</subtitle>
 	 <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'</id>
  	<rights type="text" />
  	<updated>2014-10-05T12:28:48Z</updated>
  	<link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'" />
  	<entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">159</d:Id>
        <d:Name m:type="Edm.String">159</d:Name>
        <d:Rating m:type="Edm.Double">0.543343480387708</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '159'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">52</d:Id>
        <d:Name m:type="Edm.String">52</d:Name>
        <d:Rating m:type="Edm.Double">0.539588900748721</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '52'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">35</d:Id>
        <d:Name m:type="Edm.String">35</d:Name>
        <d:Rating m:type="Edm.Double">0.53842946443853</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '35'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">124</d:Id>
        <d:Name m:type="Edm.String">124</d:Name>
        <d:Rating m:type="Edm.Double">0.536712832792886</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '124'</d:Reasoning>
      </m:properties>
    </content>
  	</entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">120</d:Id>
        <d:Name m:type="Edm.String">120</d:Name>
        <d:Rating m:type="Edm.Double">0.533673023762878</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '120'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">96</d:Id>
        <d:Name m:type="Edm.String">96</d:Name>
        <d:Rating m:type="Edm.Double">0.532544826370521</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '96'</d:Reasoning>
      </m:properties>
    </content>
  	</entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">69</d:Id>
        <d:Name m:type="Edm.String">69</d:Name>
        <d:Rating m:type="Edm.Double">0.531678607847759</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '69'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">172</d:Id>
        <d:Name m:type="Edm.String">172</d:Name>
        <d:Rating m:type="Edm.Double">0.530957821375951</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '172'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">155</d:Id>
        <d:Name m:type="Edm.String">155</d:Name>
        <d:Rating m:type="Edm.Double">0.529093541481333</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '155'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">32</d:Id>
        <d:Name m:type="Edm.String">32</d:Name>
        <d:Rating m:type="Edm.Double">0.528917978168322</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '32'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
	</feed>

##11.通知
Azure ML Recommendations は、システムで永続的なエラーが発生した際に、通知を作成します。通知には 3 つの種類があります。
1.	ビルドの失敗 - この通知は、すべてのビルドの失敗により生成されます。
2.	データ取得プロセスの失敗 - この通知は、1 つのモデルの使用状況イベント処理で、最後の 5 分間に 100 を超えるエラーが発生した場合に生成されます。
3.	推奨の使用の失敗 - この通知は、1 つのモデルの推奨要求処理で、最後の 5 分間に 100 を超えるエラーが発生した場合に生成されます。


###11.1.通知の取得
すべてのモデル、または 1 つのモデルのすべての通知を取得します。

| HTTP メソッド | URI |
|:--------|:--------|
|GET |`<rootURI>/GetNotifications?modelId=%27<model_id>%27&apiVersion=%271.0%27`<br><br>すべてのモデルのすべての通知を取得する場合:<br>`<rootURI>/GetNotifications?apiVersion=%271.0%27`<br><br>特定のモデルの通知を取得する例:<br>`<rootURI>/GetNotifications?modelId=%27967136e8-f868-4258-9331-10d567f87fae%27&apiVersion=%271.0%277`|


|	パラメーター名 |	有効な値 |
|:--------			|:--------								|
| modelId | オプション パラメーターであり、省略すると、すべてのモデルのすべての通知が取得されます。<br>有効な値: モデルの一意識別子。|
| apiVersion | 1.0 |
|||
| 要求本文 | なし |

**応答:**

HTTP ステータス コード: 200

OData XML

    The response includes one entry per notification, each entry has the following data::
		* feed\entry\content\properties\UserName – The internal user name identification
		* feed\entry\content\properties\ModelId – The model id
		* feed\entry\content\properties\Message – The notification message
		* feed\entry\content\properties\DateCreated – the date that this notification was created in UTC format
		* feed\entry\content\properties\NotificationType – the notification types, values: BuildFailure, RecommendationFailure, DataAquisitionFailure

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v1/GetNotifications" xmlns:d="http://schemas.microsoft.com/ado/200708/dataservices" xmlns:m="http://schemas.microsoft.com/ado/200708/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
		<title type="text" />
		<subtitle type="text">Get a list of Notifications for a user</subtitle>
		<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetNotifications?modelId='967136e8-f868-4258-9331-10d567f87fae'&amp;apiVersion='1.0'</id>
		<rights type="text" />
		<updated>2014-11-04T13:24:23Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetNotifications?modelId='967136e8-f868-4258-9331-10d567f87fae'&amp;apiVersion='1.0'" />
		<entry>
				<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetNotifications?modelId='967136e8-f868-4258-9331-10d567f87fae'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
			<title type="text">GetAListOfNotificationsForAUserEntity</title>
			<updated>2014-11-04T13:24:23Z</updated>
    		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetNotifications?modelId='967136e8-f868-4258-9331-10d567f87fae'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:UserName m:type="Edm.String">515506bc-3693-4dce-a5e2-81cb3e8efb56@dm.com</d:UserName>
					<d:ModelId m:type="Edm.String">967136e8-f868-4258-9331-10d567f87fae</d:ModelId>
					<d:Message m:type="Edm.String">Build failed for user</d:Message>
					<d:DateCreated m:type="Edm.String">2014-11-04T13:23:14.383</d:DateCreated>
					<d:NotificationType m:type="Edm.String">BuildFailure</d:NotificationType>
				</m:properties>
			</content>
		</entry>
	</feed>

###11.2.モデル通知の削除
モデルのすべての読み取り通知を削除します。

| HTTP メソッド | URI |
|:--------|:--------|
|削除 |`<rootURI>/DeleteModelNotifications?modelId=%<model_id>%27&apiVersion=%271.0%27`<br><br>例:<br>`<rootURI>/DeleteModelNotifications?modelId=%27967136e8-f868-4258-9331-10d567f87fae%27&apiVersion=%271.0%27`|


|	パラメーター名 |	有効な値 |
|:--------			|:--------								|
| modelId | モデルの一意識別子。 |
| apiVersion | 1.0 |
|||
| 要求本文 | なし |

**応答**:

HTTP ステータス コード: 200

###11.3.ユーザー通知の削除
すべてのモデルのすべての通知を削除します。

| HTTP メソッド | URI |
|:--------|:--------|
|削除 |`<rootURI>/DeleteUserNotifications?apiVersion=%271.0%27`|


|	パラメーター名 |	有効な値 |
|:--------			|:--------								|
| apiVersion | 1.0 |
|||
| 要求本文 | なし |

**応答**:

HTTP ステータス コード: 200



<!--HONumber=54-->