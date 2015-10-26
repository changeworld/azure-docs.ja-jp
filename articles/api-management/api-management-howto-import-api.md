<properties 
	pageTitle="API Management の重要概念" 
	description="API、成果物、ロール、グループ、その他 API Management の重要概念について説明します。" 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/10/2015" 
	ms.author="sdanie"/>

# Azure API Management で API の定義を操作と共にインポートする方法

API Management では、新しい API を作成した後で手動で API に操作を追加できるほか、1 ステップで API を操作と共にインポートすることもできます。

API と操作は、次の形式を使用してインポートできます。

-	WADL
-	Swagger

このガイドでは、1 ステップで新しい API を作成して操作をインポートする方法について説明します。手動で API を作成して操作を追加する方法については、「[Azure API Management で API を作成する方法][]」と「[Azure API Management で API に操作を追加する方法][]」を参照してください。

## <a name="import-api"> </a>API のインポート

API を作成および構成するには、パブリッシャー ポータルを使用します。パブリッシャー ポータルにアクセスするには、API Management サービスの Azure ポータルで **[管理]** をクリックします。まだ API Management サービス インスタンスを作成していない場合は、「[Azure API Management の使用][]」チュートリアルの「[API Management インスタンスの作成][]」をご覧ください。

![パブリッシャー ポータル][api-management-management-console]

左側の **[API Management]** メニューの **[API]** をクリックし、**[API のインポート]** をクリックします。

![Import API][api-management-import-apis]

**[API のインポート]** ウィンドウには、3 つのタブがあります。これらのタブは、API 仕様を指定するための 3 つの方法に対応しています。

-	**[クリップボードから]** では、専用のテキスト ボックスに API 仕様を貼り付けることができます。
-	**[ファイルから]** では、API 仕様を含むファイルを参照して選択できます。
-	**[URL から]** では、API の仕様を示す URL を指定できます。

![Import API format][api-management-import-api-clipboard]

API 仕様を指定した後は、右側のラジオ ボタンを使用して仕様の形式を指定します。次の形式がサポートされています。

-	WADL
-	Swagger

次に、**Web API URL サフィックス**を入力します。このサフィックスが、API Management サービスのベース URL に付加されます。ベース URL は、API Management サービスのインスタンスでホストされるすべての API に共通です。API Management では API がサフィックスによって識別されます。そのため、サフィックスは、特定の API Management サービス インスタンスのすべての API で一意である必要があります。

すべての値を入力したら、**[保存]** をクリックして API および関連付けられた操作を作成します。

>[AZURE.NOTE]Swagger 形式で Basic Calculator API をインポートする方法のチュートリアルについては、「[Azure API Management での最初の API の管理](api-management-get-started.md)」をご覧ください。

## <a name="export-api"> </a> API のエクスポート

新しい API をインポートする操作に加えて、パブリッシャー ポータルから API の定義をエクスポートすることもできます。そのためには、**API** の **[概要] タブ**で **[API のエクスポート]** をクリックします。

![Export API][api-management-export-api]

API は、WADL または Swagger を使用してエクスポートできます。目的の形式を選択し、**[保存]** をクリックして、ファイルを保存する場所を選択します。

![Export API format][api-management-export-api-format]

## <a name="next-steps"> </a>次のステップ

API を作成して操作をインポートした後は、追加の設定を確認して構成する作業、API を成果物に追加する作業、API を発行して開発者が利用できるようにする作業を行います。詳細については、次のガイドを参照してください。

-	[API 設定を構成する方法][]
-	[成果物を作成して発行する方法][]




[api-management-management-console]: ./media/api-management-howto-import-api/api-management-management-console.png
[api-management-import-apis]: ./media/api-management-howto-import-api/api-management-api-import-apis.png
[api-management-import-api-clipboard]: ./media/api-management-howto-import-api/api-management-import-api-wizard.png
[api-management-export-api]: ./media/api-management-howto-import-api/api-management-export-api.png
[api-management-export-api-format]: ./media/api-management-howto-import-api/api-management-export-api-format.png

[Import an API]: #import-api
[Export an API]: #export-api
[Configure API settings]: #configure-api-settings
[Next steps]: #next-steps

[API Management インスタンスの作成]: api-management-get-started.md
[Azure API Management の使用]: api-management-get-started.md#create-service-instance

[Azure API Management で API に操作を追加する方法]: api-management-howto-add-operations.md
[成果物を作成して発行する方法]: api-management-howto-add-products.md
[Azure API Management で API を作成する方法]: api-management-howto-create-apis.md
[API 設定を構成する方法]: api-management-howto-create-apis.md#configure-api-settings

<!---HONumber=Oct15_HO3-->