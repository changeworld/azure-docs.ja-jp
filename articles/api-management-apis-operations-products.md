<properties 
	pageTitle="Azure API Management で API とその操作、成果物を作成する方法" 
	description="API Management で API とその操作、成果物を作成する方法について説明します。" 
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

# Azure API Management で API とその操作、成果物を作成する方法

Azure API Management では、API とその操作を成果物に追加します。開発者は、成果物に追加された API を使用してアプリケーションを構築することができます。このセクションでは、API を作成してそこに操作を追加し、そのうえで、開発者が利用できるように API を成果物に関連付けて発行する方法を説明します。

## <a name="create-apis"> </a>API の作成方法

API Management における API は、クライアント アプリケーションから呼び出すことのできる一連の操作を表します。新しい API は、API Management ポータルで作成します。

このガイドでは、API Management で新しい API を作成して構成する方法を説明します。

-   [API の作成方法][API の作成方法]

## <a name="add-operations"> </a>API に操作を追加する方法

API Management 内の API を使用するためには、操作を追加する必要があります。このガイドでは、API Management 内の API に各種の操作を追加して構成する方法を説明します。

-   [API に操作を追加する方法][API に操作を追加する方法]

API とその操作を 1 回のステップで WADL 形式または Swagger 形式にインポートすることもできます。

-   [How to import the definition of an API with operations (API とその操作の定義をインポートする方法)][How to import the definition of an API with operations (API とその操作の定義をインポートする方法)]

## <a name="add-product"> </a>成果物を作成して発行する方法

API Management の成果物には、少なくとも 1 つの API に加え、使用量クォータや使用条件が含まれます。成果物が発行されると、開発者は、その成果物をサブスクライブして、API を使い始めることができます。以下のトピックは、成果物の作成、API の追加、開発者への発行に関する手引きです。

-   [How to add and publish a product (成果物を追加して発行する方法)][How to add and publish a product (成果物を追加して発行する方法)]
-   [Azure API Management で成果物を作成して詳細設定を行う方法][Azure API Management で成果物を作成して詳細設定を行う方法]

  [API の作成方法]: ../api-management-howto-create-apis
  [API に操作を追加する方法]: ../api-management-howto-add-operations
  [How to import the definition of an API with operations (API とその操作の定義をインポートする方法)]: ../api-management-howto-import-api
  [How to add and publish a product (成果物を追加して発行する方法)]: ../api-management-howto-add-products
  [Azure API Management で成果物を作成して詳細設定を行う方法]: ../api-management-howto-product-with-rules

<!--HONumber=46--> 
