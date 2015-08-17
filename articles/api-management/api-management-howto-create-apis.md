<properties 
	pageTitle="Azure API Management で API を作成する方法" 
	description="Azure API Management で API を作成して構成する方法について説明します。" 
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
	ms.date="06/16/2015" 
	ms.author="sdanie"/>

# Azure API Management で API を作成する方法

API Management における API は、クライアント アプリケーションから呼び出すことのできる一連の操作を表します。パブリッシャー ポータルで新しい API を作成した後、目的の操作を追加します。操作を追加し、API を成果物に追加すると、API を発行できます。API が発行されると、開発者は、発行された成果物をサブスクライブして使用することができます。

このガイドでは、プロセスの最初のステップである、API Management で新しい API を作成して構成する方法について説明します。操作を追加する方法、および成果物を発行する方法の詳細については、「[Azure API Management で API に操作を追加する方法][]」および「[Azure API Management で成果物を作成して発行する方法][]」を参照してください。

## <a name="create-new-api"> </a>新しい API の作成

API を作成および構成するには、パブリッシャー ポータルを使用します。パブリッシャー ポータルにアクセスするには、API Management サービスの Azure ポータルで **[管理]** をクリックします。

![パブリッシャー ポータル][api-management-management-console]

>まだ API Management サービス インスタンスを作成していない場合は、「[Azure API Management の使用][]」チュートリアルの「[API Management インスタンスの作成][]」を参照してください。

左側の **[API Management]** メニューの **[API]** をクリックし、**[API の追加]** をクリックします。

![Create API][api-management-create-api]

**[新しい API の追加]** ウィンドウを使用して、新しい API を構成します。

![新しい API を追加する][api-management-add-new-api]

新しい API を構成するには、次の 3 つのフィールドを使用します。

-	**[Web API タイトル]** は、API に対する一意のわかりやすい名前です。開発者ポータルと管理ポータルには、この名前が表示されます。
-	**[Web サービスの URL]** は、API が実装されている HTTP サービスの URL です。要求は、API Management によってこのアドレスに転送されます。
-	**[Web API URL サフィックス]** は、API Management サービスのベース URL に付加されます。ベース URL は、API Management サービス インスタンスによってホストされるすべての API に共通です。API Management では API がサフィックスによって識別されるため、サフィックスは、特定の発行者のすべての API で一意である必要があります。
-	**[Web API の URL スキーム]** により、API へのアクセスに使用できるプロトコルが決まります。既定では、HTTP が指定されます。

3 つの値を構成したら、**[保存]** をクリックします。新しい API が作成されると、その API の概要ページが管理ポータルに表示されます。

![API summary][api-management-api-summary]

## <a name="configure-api-settings"> </a>API 設定の構成

API の構成を確認および編集するには、**[設定]** タブを使用します。**[Web API タイトル]**、**[Web サービスの URL]**、および **[Web API URL サフィックス]** は、最初に API の作成時に設定され、ここで編集できます。**[説明]** には、オプションの説明を入力します。**[Web API の URL スキーム]** により、API へのアクセスに使用できるプロトコルが決まります。

![API settings][api-management-api-settings]

API を実装する Web サービスの**プロキシ認証**を構成するには、**[セキュリティ]** タブを選択します。**[資格情報]** ボックスの一覧を使用して、**基本認証**または**相互証明書**認証を構成できます。基本認証を使用するには、単に必要な資格情報を入力します。相互証明書認証の使用方法については、「[Azure API Management で相互証明書認証を使用してバックエンド サービスを保護する方法][]」を参照してください。

**[セキュリティ]** タブを使用して、OAuth 2.0 を使用するように**ユーザー承認**を構成することもできます。詳細については、「[Azure API Management の OAuth 2.0 を使用して開発者アカウントを認証する方法][]」を参照してください。

![Basic authentication settings][api-management-api-settings-credentials]

**[保存]** をクリックして、API 設定に加えた変更を保存します。

## <a name="next-steps"> </a>次のステップ

API を作成し、設定を構成した後は、操作を API に追加します。さらに、API を成果物に追加し、開発者が利用できるように発行します。詳細については、次の 2 つのガイドを参照してください。

-	[API に操作を追加する方法][]
-	[成果物を作成して発行する方法][]





[api-management-create-api]: ./media/api-management-howto-create-apis/api-management-create-api.png
[api-management-management-console]: ./media/api-management-howto-create-apis/api-management-management-console.png
[api-management-add-new-api]: ./media/api-management-howto-create-apis/api-management-add-new-api.png
[api-management-api-settings]: ./media/api-management-howto-create-apis/api-management-api-settings.png
[api-management-api-settings-credentials]: ./media/api-management-howto-create-apis/api-management-api-settings-credentials.png
[api-management-api-summary]: ./media/api-management-howto-create-apis/api-management-api-summary.png
[api-management-echo-operations]: ./media/api-management-howto-create-apis/api-management-echo-operations.png

[What is an API?]: #what-is-api
[Create a new API]: #create-new-api
[Configure API settings]: #configure-api-settings
[Configure API operations]: #configure-api-operations
[Next steps]: #next-steps

[API に操作を追加する方法]: api-management-howto-add-operations.md
[Azure API Management で API に操作を追加する方法]: api-management-howto-add-operations.md
[Azure API Management で成果物を作成して発行する方法]: api-management-howto-add-products.md
[成果物を作成して発行する方法]: api-management-howto-add-products.md

[API Management インスタンスの作成]: api-management-get-started.md
[Azure API Management の使用]: api-management-get-started.md#create-service-instance
[Azure API Management で相互証明書認証を使用してバックエンド サービスを保護する方法]: api-management-howto-mutual-certificates.md
[Azure API Management の OAuth 2.0 を使用して開発者アカウントを認証する方法]: api-management-howto-oauth2.md

<!---HONumber=August15_HO6-->