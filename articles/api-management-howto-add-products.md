<properties 
	pageTitle="Azure API Management で成果物を作成して発行する方法" 
	description="Azure API Management で成果物を作成して発行する方法について説明します。" 
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
	ms.date="2/11/2015" 
	ms.author="sdanie"/>

# Azure API Management で成果物を作成して発行する方法

Azure API Management (プレビュー) の成果物には、少なくとも 1 つの API に加え、使用量クォータや使用条件が含まれます。成果物が発行されると、開発者は成果物をサブスクライブして、成果物の API の利用を開始できます。このトピックでは、成果物を作成し、API を追加し、発行して開発者が利用できるようにする方法について説明します。

## このトピックの内容

-   [成果物の作成][成果物の作成]
-   [成果物への API の追加](#add-apis)
-   [成果物への説明情報の追加][成果物への説明情報の追加]
-   [成果物の発行][成果物の発行]
-   [開発者への成果物の公開][開発者への成果物の公開]
-   [成果物のサブスクライバーの表示][成果物のサブスクライバーの表示]
-   [次のステップ][次のステップ]

## <a name="create-product"> </a>成果物の作成

操作を API に追加して構成するには、管理コンソールを使用します。管理コンソールにアクセスするには、ご利用の API Management サービスの Azure ポータルで **[管理コンソール]** をクリックします。

> まだ API Management サービス インスタンスを作成していない場合は、「[Azure API Management の使用][Azure API Management の使用]」チュートリアルの「[API Management インスタンスの作成][API Management インスタンスの作成]」を参照してください。

![API Management console][API Management console]

左側のメニューの **[成果物]** をクリックして **[成果物]** ページを表示し、**[成果物の追加]** をクリックします。

![成果物](./media/api-management-howto-add-products/api-management-products.png)

![New product][New product]

成果物のわかりやすい名前を **[名前]** フィールドに入力し、成果物の説明を **[説明]** フィールドに入力します。

この成果物に対するサブスクリプションの申し込みを管理者の審査の下で承認または拒否する場合は、**[サブスクリプションの承認を必須とする]** チェック ボックスをオンにします。チェック ボックスがオフの場合、サブスクリプションの申し込みは自動承認されます。サブスクリプションの詳細については、「[成果物のサブスクライバーの表示][成果物のサブスクライバーの表示]」を参照してください。

![成果物][1]

> 新しい成果物は既定で発行されず、**Administrators** グループのみに表示されます。

成果物を構成するには、**[成果物]** タブで成果物の名前をクリックします。

## <a name="add-apis"> </a>成果物への API の追加

**[成果物]** ページには、**[概要]**、**[設定]**、**[可視性]**、**[開発者]** の 4 つの構成用リンクが用意されています。**[概要]** タブでは、API を追加したり、成果物の発行や発行の取り消しを行ったりすることができます。

![まとめ][まとめ]

成果物を発行するには、事前に 1 つまたは複数の API を追加する必要があります。そのためには、**[成果物への API の追加]** をクリックします。

![Add APIs][Add APIs]

目的の API を選択し、**[保存]** をクリックします。

## <a name="add-description"> </a>成果物への説明情報の追加

**[設定]** タブでは、その目的、アクセスできる API、その他の有益な情報など、成果物に関する詳しい情報を入力できます。このタブでは、API を呼び出す開発者を対象に、プレーンテキストまたは HTML マークアップ形式で情報を入力できます。

![Product settings][Product settings]

すべての成果物へのサブスクリプション要求を手動で承認する場合は、**[サブスクリプションの承認を必須とする]** をオンにします。既定では、成果物のサブスクリプションはすべて自動的に許可されます。

オプションで、**[使用条件]** に、サブスクライバーが成果物を使用するにあたって同意する必要がある成果物の使用条件を入力します。

## <a name="publish-product"> </a>成果物の発行

成果物に含まれる API を呼び出すには、あらかじめ成果物を発行しておく必要があります。成果物の **[概要]** タブで、**[発行]** をクリックし、**[はい。発行します]** をクリックして確認します。以前に発行した成果物をプライベートに設定するには、**[発行の取り消し]** をクリックします。

![Publish product][Publish product]

## <a name="make-visible"> </a>開発者への成果物の公開

**[可視性]** タブでは、開発者ポータルで成果物を表示してサブスクライブすることができるロールを選択できます。

![Product visibility][Product visibility]

グループ内の開発者に対して成果物の可視性を有効または無効にするには、グループの横のチェック ボックスをオンまたはオフにし、**[保存]** をクリックします。

> 詳細については、「[Azure API Management でグループを作成および使用して開発者アカウントを管理する方法][Azure API Management でグループを作成および使用して開発者アカウントを管理する方法]」を参照してください。

## <a name="view-subscribers"> </a>成果物のサブスクライバーの表示

**[開発者]** タブには、成果物をサブスクライブしている開発者の一覧が表示されます。開発者の名前をクリックすると、それぞれの開発者の詳細および設定が表示されます。この例では、成果物をサブスクライブしている開発者は 1 人もいません。

![開発者]( ./media/api-management-howto-add-products/api-management-developer-list.png)

## <a name="next-steps"> </a>次のステップ

目的の API を追加して成果物を発行すると、開発者が成果物をサブスクライブして API を呼び出せるようになります。これらの操作とより高度な成果物の構成を説明するチュートリアルについては、「[Azure API Management で成果物を作成して詳細設定を行う方法][Azure API Management で成果物を作成して詳細設定を行う方法]」を参照してください。

  [成果物の作成]: #create-product
  [成果物への説明情報の追加]: #add-description
  [成果物の発行]: #publish-product
  [開発者への成果物の公開]: #make-visible
  [成果物のサブスクライバーの表示]: #view-subscribers
  [次のステップ]: #next-steps
  [Azure API Management の使用]: ../api-management-get-started
  [API Management インスタンスの作成]: ../api-management-get-started/#create-service-instance
  [API Management console]: ./media/api-management-howto-add-products/api-management-management-console.png
  [New product]: ./media/api-management-howto-add-products/api-management-add-new-product.png
  [1]: ./media/api-management-howto-add-products/api-management-products-page.png
  [まとめ]: ./media/api-management-howto-add-products/api-management-new-product-summary.png
  [Add APIs]: ./media/api-management-howto-add-products/api-management-add-apis-to-product.png
  [Product settings]: ./media/api-management-howto-add-products/api-management-product-settings.png
  [Publish product]: ./media/api-management-howto-add-products/api-management-publish-product.png
  [Product visibility]: ./media/api-management-howto-add-products/api-management-product-visibility.png
  [Azure API Management でグループを作成および使用して開発者アカウントを管理する方法]: ../api-management-howto-create-groups
  [Azure API Management で成果物を作成して詳細設定を行う方法]: ../api-management-howto-product-with-rules

<!--HONumber=46--> 
