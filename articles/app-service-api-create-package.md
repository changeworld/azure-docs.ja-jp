<properties 
	pageTitle="API アプリ パッケージの作成" 
	description="API アプリ パッケージを作成する方法について説明します。" 
	services="app-service\api" 
	documentationCenter=".net"
	authors="guangyang"
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/20/2015" 
	ms.author="guayan"/>

# API アプリ パッケージの作成

## 概要

この記事では、[Azure Marketplace](http://azure.microsoft.com/marketplace/api-apps/) に発行できるように API アプリ パッケージを作成する方法について説明します。

- API アプリを作成する方法については、[Visual Studio を使用した API アプリの作成](app-service-dotnet-create-api-app.md)に関するページを参照してください。
- Azure Marketplace に API アプリ パッケージを発行する方法については、「[Azure Marketplace への API アプリ パッケージの発行](app-service-api-publish-package)」を参照してください。

## フォルダー構造

API アプリ用 Nuget パッケージ (*.nupkg* ファイル) では、*コンテンツ* フォルダーの下に次のファイルとフォルダーが含まれます。

    apiapp.json
    Metadata
        apiDefinition.swagger.json
        icons
            <icon files>
        screenshots
            <screenshot files>
        deploymentTemplates
            <template files>
        UIDefinition.json
    <other artifacts like source code, binary, etc.>

Visual Studio プロジェクトをパッケージ化すると、このフォルダー構造で *.nupkg* ファイルが作成されます。次の図のように、プロジェクト フォルダーには *apiapp.json* と *Metadata* が含まれています。

![ソリューション エクスプローラーの apiapp.json と Metadata](./media/app-service-api-create-package/metadatainse.png)

以降のセクションでは、API アプリのフォルダー構造内のファイルとフォルダーについてそれぞれ説明してから、プロジェクトのパッケージ化とデプロイの方法について説明します。

## apiapp.json

これは、API アプリのマニフェスト ファイルです。

|名前 (太字のものは必須)|型|形式|説明|
|:---------------------|:-----|:-------|:------------|
|**id**|string|[a-zA-Z0-9_.]|このパッケージの ID。名前空間内で一意である必要があり、英数字、"_"、"." のみを含めることができます。先頭には、英数字を使用する必要があります。|
|**namespace**|string|ドメイン名|**id** プロパティと共に API アプリを一意に識別する名前空間。発行者の AAD テナントのドメイン名のいずれかを指定する必要があります。|
|**version**|string|[semver](http://docs.nuget.org/Create/Versioning)|このパッケージのバージョン。ユーザーがこのパッケージの自動アップグレードを有効化すると、同一メジャー バージョン内の新しいバージョンにのみ、自動アップグレードが適用されます。|
|**gateway**|string|2015-01-14|このパッケージが使用しているゲートウェイ API のバージョン。ゲートウェイは特殊な Web アプリです。リソース グループ内の API アプリに対するすべての要求がゲートウェイを介してルーティングされます。ゲートウェイの主な機能の 1 つが認証の処理です。現時点では、利用できるゲートウェイのバージョンは 2015-01-14 のみです。今後、新しいゲートウェイのバージョンがリリースされたときに、このプロパティによって、互換性のない変更を回避し、前のゲートウェイ API を引き続き使用することができます。| 
|**title**|string||API アプリの表示名。|
|**summary**|string|最大 100 文字|API アプリの簡単な概要文。
|description|string|最大 1500 文字|API アプリの全説明文。HTML を含めることができます。使用できる要素と属性は、"h1"、"h2"、"h3"、"h4"、"h5"、"p"、"ol"、"ul"、"li"、"a[target|href]"、"br"、"strong"、"em"、"b"、"i" です。|
|**author**|string|最大 256 文字|API アプリの作成者。|
|homepage|string|URL|API アプリのホーム ページ。|
|endpoints|object||API アプリ プラットフォームが API アプリのメソッドと状態に関する情報を照会できるエンドポイントの一覧。|
|endpoints.apiDefinition|string|URL パス|GET 要求に対し Swagger 2.0 API 定義を返す API アプリによって公開された API の相対 URL (例: "/swagger/docs/v1")。設定すると、パッケージに含まれる静的 apiDefinition.swagger.json ファイルの代わりに使用されます。|
|endpoints.status|string|URL パス|GET 要求に対し API アプリのランタイム状態の情報を返す、API アプリによって公開された API の相対 URL。|
|categories|string[]|community、social、enterprise、integration、protocol、app-datasvc、other|この API アプリ パッケージが表示される Azure Marketplace カテゴリ。既定では、API アプリは常にコミュニティ カテゴリに表示されます。API アプリは承認されると、指定したカテゴリに表示されます。|
|license|object||API アプリのライセンス。|
|**license.type**|string||SPDX ライセンスの ID。MIT などです。|
|license.url|string|URL|ライセンスの全文を示す絶対 URL。|
|license.requireAcceptance|bool|true、false|インストール前にライセンスに同意する必要があるかどうか。既定値は false です。|
|links|object[]||Marketplace のページに追加されるリンクの配列。|
|**links.text**|string||リンクのテキスト。|
|**links.url**|string|URL|リンクの絶対 URL。|
|authentication|object[]||この API アプリが API 呼び出しを送信するために必要な認証の種類を示した配列。現在、API アプリ パッケージごとにサポートされる認証は 1 つだけです。|
|**authentication.type**|string|Box、DropBox、Facebook、Google、Office365、OneDrive、Quickbook、Salesforce、SharePointOnline、Twitter、Yammer|API アプリで要求される認証。現在サポートされている認証の種類は 11 個です。今後、さらに追加される予定です。| 
|authentication.scopes|string[]||認証の種類に固有のスコープの配列。|
|copyright|string|API アプリの著作権情報。
|brandColor|string|CSS と互換性のある任意の形式|UI エクスペリエンスを向上させるブランド色 (オプション)。たとえば、ロジック アプリ デザイナーは、このプロパティを使用して、カード ヘッダーの背景色を描画します。|

## metadata/apiDefinition.swagger.json

ここでは、必要に応じて静的 Swagger 2.0 JSON ファイルを指定して、API アプリの API 定義を公開できます。プラットフォームは最初に、**apiapp.json** で **endpoints.apiDefinition** プロパティが設定されているかどうかを確認します。設定されている場合は、プロパティに指定された URL から API 定義を取得します。設定されていない場合は、このファイルを探します。

- Swagger 2.0 標準については、[http://swagger.io/](http://swagger.io/) を参照してください。 <!--todo: URL を指定してください
- ロジック アプリ用に最適化するために API 定義をカスタマイズする方法については、[ドキュメントのタイトル]()を参照してください。
- 動的 API の定義については、[ドキュメントのタイトル]()を参照してください。 -->

## metadata/icons

必要に応じて、API アプリ パッケージに独自のアイコンのセットを指定できます。必要なアイコン ファイルが指定されていない場合は、次に示す既定のアイコンが使用されます。

![API アプリの既定の大きいアイコン](./media/app-service-api-create-package/api-app-default-large-icon.png)

|ファイル |幅|高さ|説明|
|:--------------------|:----|:-----|:----------|
|metadata/icons/small.png|40px|40px|独自のアイコンを使用する場合は必須です。|
|metadata/icons/medium.png|90px|90px|独自のアイコンを使用する場合は必須です。|
|metadata/icons/large.png|115px|115px|独自のアイコンを使用する場合は必須です。|
|metadata/icons/wide.png|255px|115px|独自のアイコンを使用する場合は必須です。|
|metadata/icons/hero.png|815px|290px|独自のアイコンを使用する場合は省略可能です。|

## metadata/screenshots

必要に応じて、API アプリ パッケージに最大 5 つのスクリーン ショットを指定できます。

|ファイル|幅|高さ|説明|
|:--------------------|:----|:-----|:----------|
|metadata/screenshots/*.png|533px|324px|API アプリ パッケージのスクリーン ショット。|

## metadata/deploymentTemplates

API アプリ パッケージは、デプロイ時にカスタム構成が必要になる場合があります。たとえば、[Azure Storage BLOB コネクタ](http://azure.microsoft.com/marketplace/partners/microsoft_com/azurestorageblobconnector/)には、Azure Storage BLOB コンテナー URI が必要です。必要に応じて、アクセス キーを構成することもできます。

このシナリオをサポートするには、API アプリのデプロイをカスタマイズするためにこのフォルダーに Azure リソース マネージャー (ARM) テンプレートの JSON ファイルの一覧を追加します。API アプリ プラットフォームでは、最終的なデプロイメント用のテンプレートを作成するために、システム テンプレートとカスタム ARM テンプレートをマージします。カスタム ARM テンプレート (**$system** を想定) で定義されたすべてのパラメーターは、API アプリのユーザーが値を入力できるように自動的に Azure プレビュー ポータルの **[作成]** ブレードにも表示されます。

次のサンプル ARM テンプレートでは、API アプリのデプロイ時に BLOB コンテナー URI とアクセス キーを要求する方法を示しています。

    {
      "$schema": "http://schemas.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "$system": {
          "type": "object"
        },
        "BlobConnector_ContainerUrl": {
          "type": "string"
        },
        "BlobConnector_AccessKey": {
          "type": "securestring"
        }
      },
      "resources": [
        {
          "apiVersion": "2015-04-01",
          "type": "Microsoft.Web/sites",
          "name": "[parameters('$system').siteName]",
          "location": "[parameters('$system').location]",
          "properties": {
            "siteConfig": {
              "appSettings": [
                {
                  "name": "BlobConnector_ContainerUrl",
                  "value": "[parameters('BlobConnector_ContainerUrl')]"
                },
                {
                  "name": "BlobConnector_AccessKey",
                  "value": "[parameters('BlobConnector_AccessKey')]"
                }
              ]
            }
          }
        }
      ]
    }

対応する Azure プレビュー ポータルの [作成] ブレードを下のスクリーン ショットで示します (このスクリーン ショットでは、[作成] ブレードの改良のために UIDefinition.json を使用している API アプリ パッケージを示しています)。詳細については、「[metadata/UIDefinition.json](#metadata-uidefinition-json)」を参照してください。

![カスタム ARM テンプレートの [作成] ブレードの例](./media/app-service-api-create-package/custom-arm-template-create-blade-example.png)

詳細については、「[Azure リソース マネージャー テンプレートの言語](https://msdn.microsoft.com/library/dn835138)」を参照してください。

## metadata/UIDefinition.json

API アプリのデプロイに必要なカスタム構成を指定するのにカスタム ARM テンプレートを使用する必要がある場合、Azure プレビュー ポータルの **[作成]** ブレードでは、API アプリのユーザーが値を入力できるように自動的に ARM テンプレート パラメーターが要求されます。*UIDefinition.json* ファイルを使用して、既定の値、ツールヒント、検証などを指定することにより、この **[作成]** ブレードを改良することができます。

*UIDefinition.json ファイル*を指定するには、まず次のスケルトンを使用してから、下の表に従って各パラメーターを構成します。


    {
      "parameters": {
        "<your API app package id>": {
            "<name of the ARM template parameter>": { },
            "<name of the ARM template parameter>": { }
        }
      }
    }

|名前 |型|説明|
|:---------------------|:-------|:------------|
|defaultValue|string|Azure プレビュー ポータルの [作成] ブレードにおける入力コントロールの既定値です。|
|displayName|string|Azure プレビュー ポータルの [作成] ブレードにおける入力コントロールのラベルです。これは短くする必要があります。|
|description|string|入力コントロールの説明です。|
|tooltip|string|Azure プレビュー ポータルの [作成] ブレードにおける入力コントロールのツールヒントです。これは、ユーザーがラベルの右側にある情報バブルをクリックすると表示されます。長くてわかりやすい文を指定できます。|
|constraints|オブジェクト|パラメーターと照合する制約です。|
|constraints.required|bool|パラメーターが必要かどうか。既定値は false です。|

たとえば、[Azure Storage BLOB コネクタ](http://azure.microsoft.com/marketplace/partners/microsoft_com/azurestorageblobconnector/)の *UIDefinition.json* ファイルを次に示します。

    {
      "parameters": {
        "AzureStorageBlobConnector": {
          "BlobConnector_ContainerUrl": {
            "defaultValue": "",
            "displayName": "Container/SAS URI",
            "description": "Specify the URI of the Blob Container. The URI may also include the SAS token. Example http://storageaccountname.blob.core.windows.net/containername or http://storageaccountname.blob.core.windows.net/containername?sr=c&si=mypolicy&sig=signatureblah",
            "tooltip": "Specify the URI of the Blob Container. The URI may also include the SAS token. Example http://storageaccountname.blob.core.windows.net/containername or http://storageaccountname.blob.core.windows.net/containername?sr=c&si=mypolicy&sig=signatureblah",
            "constraints": {
              "required": "true"
            }
          },
          "BlobConnector_AccessKey": {
            "defaultValue": "",
            "displayName": "Access Key",
            "description": "Specify a valid primary/secondary storage account access key. Leave this field empty if you are using SAS token for authentication.",
            "tooltip": "Specify a valid primary/secondary storage account access key. Leave this field empty if you are using SAS token for authentication.",
            "constraints": {
              "required": "false"
            }
          }
        }
      }
    }

これにより、Azure プレビュー ポータルの **[作成]** ブレードが構成され、次の **[パッケージ設定]** ブレードが表示されます。

![UIDefinition の [作成] ブレードの例](./media/app-service-api-create-package/uidefinition-create-blade-example.png)

<!--todo add when ready to document status URI
A URI to a web service Get method that returns a value that indicates the API app's current status. If you provide this URI, the portal will show the API app's current operational status along with other information about the API app, for example:  running, nearing quota, SSL certificate expiring, etc. The format of the JSON the portal expects to receive is shown below, following the end of this table. If you don't provide an endpoints.status URI, the portal shows the Azure platform status as the API app's status.
Here is an example that shows the expected format of the JSON response from the Get method that `endpoints.status` points to:
		{
		  "status":[{ 
		    "name":"Nearing Quota", 
		    "message":"One or more quotas is nearing their limit", 
		    "level":"Warning" 
		  }, { 
		    "name":"Expiring Certificate", 
		    "message":"The SSL Certificate associated with this website is about to expire", 
		    "level":"Error"
		  }, {
		    "name":"Running", 
		    "message":"The website is handling traffic", 
		    "level":"Info"
		  }]
		}
The `name` property is a short description of the status, `message` is a longer description, and `level` can be "Error", "Warning", or "Info" for normal status.
-->

## API アプリ パッケージの作成

API アプリ パッケージを作成するには

1. [Azure クロス プラットフォーム コマンド ライン インターフェイスをダウンロードしてインストールします](xplat-cli)。
2. 次のコマンドを実行して、Azure リソース マネージャー モードに切り替えます。

        azure config mode arm

3. 次のコマンドを実行して、API アプリの nuget パッケージを作成します。

        azure apiapp package create -p <package folder> -o <destination folder>

    次に例を示します。

        azure apiapp package create -p c:\ContactList\ContactList -o c:\ContactListPackage

これにより、パッケージの内容が検証され、次の点が確認されます。

- 上で説明した形式に従っているかどうか
- Metadata\\apiDefinition.swagger.json (指定した場合) に有効な Swagger 2.0 API の定義が含まれているかどうか

問題がある場合は、問題を解決して有効な API アプリ パッケージを作成できるように、問題の詳細が表示されます。

## 次のステップ

API アプリ パッケージを Azure Marketplace に発行する準備ができました。その方法については、[Azure Marketplace への API アプリ パッケージの発行](app-service-api-publish-package)のチュートリアルに従ってください。

<!--HONumber=52-->
