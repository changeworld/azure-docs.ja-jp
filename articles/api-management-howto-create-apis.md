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
	ms.date="11/18/2014" 
	ms.author="sdanie"/>

# Azure API Management で API を作成する方法

API Management (プレビュー) における API は、クライアント アプリケーションから呼び出すことのできる一連の操作を表します。管理コンソールで新しい API を作成した後、目的の操作を追加します。操作を追加し、API を成果物に追加すると、API を発行できます。API が発行されると、開発者は、発行された成果物をサブスクライブして使用することができます。

このガイドでは、プロセスの最初のステップである、API Management で新しい API を作成して構成する方法について説明します。操作を追加する方法、および成果物を発行する方法の詳細については、「[Azure API Management で API に操作を追加する方法][Azure API Management で API に操作を追加する方法]」および「[Azure API Management で成果物を作成して発行する方法][Azure API Management で成果物を作成して発行する方法]」を参照してください。

## このトピックの内容

-   [新しい API の作成][新しい API の作成]
-   [API 設定の構成][API 設定の構成]
-   [次のステップ][次のステップ]

## <a name="create-new-api"> </a>新しい API の作成

API を作成して構成するには、ご利用の API Management サービス インスタンスの Azure ポータルで **[管理コンソール]** をクリックします。API Management の管理ポータルが表示されます。

> まだ API Management サービス インスタンスを作成していない場合は、「[Azure API Management の使用][Azure API Management の使用]」チュートリアルの「[API Management インスタンスの作成][API Management インスタンスの作成]」を参照してください。

![Management console][Management console]

左側の **[API Management]** メニューの **[API]** をクリックし、**[API の追加]** をクリックします。

![Create API][Create API]

**[新しい API の追加]** ウィンドウを使用して、新しい API を構成します。

![Add new API][Add new API]

新しい API を構成するには、次の 3 つのフィールドを使用します。

-   **[Web API タイトル]** は、API に対する一意のわかりやすい名前です。開発者ポータルと管理ポータルには、この名前が表示されます。
-   **[Web サービスの URL]** は、API が実装されている HTTP サービスの URL です。要求は、API Management によってこのアドレスに転送されます。
-   **[Web API URL サフィックス]** は、API Management サービスのベース URL に付加されます。ベース URL は、API Management サービス インスタンスによってホストされるすべての API に共通です。API Management では API がサフィックスによって識別されるため、サフィックスは、特定の発行者のすべての API で一意である必要があります。

3 つの値を構成したら、**[保存]** をクリックします。新しい API が作成されると、その API の概要ページが管理ポータルに表示されます。

![API summary][API summary]

## <a name="configure-api-settings"> </a>API 設定の構成

API の構成を確認および編集するには、**[設定]** タブを使用します。**[Web API タイトル]**、**[Web サービスの URL]**、および **[Web API URL サフィックス]** は、最初に API の作成時に設定され、ここで編集できます。**[説明]** には、オプションの説明を入力します。**[使用する資格情報]** では、基本 HTTP 認証を構成できます。

![API settings][API settings]

API を実装する Web サービスの HTTP 基本認証を構成するには、**[使用する資格情報]** ボックスの一覧の **[基本]** を選択し、使用する資格情報を入力します。

![Basic authentication settings][Basic authentication settings]

**[保存]** をクリックして、API 設定に加えた変更を保存します。

## <a name="next-steps"> </a>次のステップ

API を作成し、設定を構成した後は、操作を API に追加します。さらに、API を成果物に追加し、開発者が利用できるように発行します。詳細については、次のガイドを参照してください。

-   [API に操作を追加する方法][Azure API Management で API に操作を追加する方法]
-   [Azure API Management で成果物を作成して発行する方法][Azure API Management で成果物を作成して発行する方法]

  [Azure API Management で API に操作を追加する方法]: ../api-management-howto-add-operations
  [Azure API Management で成果物を作成して発行する方法]: ../api-management-howto-add-products
  [新しい API の作成]: #create-new-api
  [API 設定の構成]: #configure-api-settings
  [次のステップ]: #next-steps
  [Azure API Management の使用]: ../api-management-get-started
  [API Management インスタンスの作成]: ../api-management-get-started/#create-service-instance
  [Management console]: ./media/api-management-howto-create-apis/api-management-management-console.png
  [Create API]: ./media/api-management-howto-create-apis/api-management-create-api.png
  [Add new API]: ./media/api-management-howto-create-apis/api-management-add-new-api.png
  [API summary]: ./media/api-management-howto-create-apis/api-management-api-summary.png
  [API settings]: ./media/api-management-howto-create-apis/api-management-api-settings.png
  [Basic authentication settings]: ./media/api-management-howto-create-apis/api-management-api-settings-credentials.png

<!--HONumber=46--> 
