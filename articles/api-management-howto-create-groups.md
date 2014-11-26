<properties pageTitle="How to create and use groups to manage developer accounts in Azure API Management" metaKeywords="" description="Learn how to manage developer accounts using groups in Azure API Management" metaCanonical="" services="" documentationCenter="API Management" title="How to create and use groups to manage developer accounts in Azure API Management" authors="sdanie" solutions="" manager="" editor="" />

<tags ms.service="api-management" ms.workload="mobile" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="sdanie" />

# Azure API Management でグループを作成および使用して開発者アカウントを管理する方法

API Management (プレビュー) では、開発者に成果物の表示を許可するかどうかが、グループを使用して管理されます。グループに対して成果物の表示が許可されると、そのグループに属する開発者は、グループに関連付けられた成果物を表示してサブスクライブできるようになります。

API Management には、次に示す組み込みのグループが用意されています。

-   **管理者** - API Management サービス インスタンスを管理します。開発者が使用する API とその操作、成果物は、管理者が作成します。
-   **開発者** - API の利用者です。管理者によって作成された API を使用してアプリケーションを構築します。開発者は、開発者ポータルへのアクセスが認められており、API の操作を呼び出すアプリケーションを構築します。
-   **ゲスト** - API Management インスタンスの開発者ポータルに訪れる非認証ユーザー (利用予定者など) がこのグループに該当します。特定の読み取り専用アクセスを許可することができます (API の閲覧はできるが、呼び出すことはできないなど)。

組み込みのグループに加え、管理者はカスタム グループを作成できます。カスタム グループは、組み込みの開発者グループと同じ特権を持ちます。カスタム グループを使用して、さまざまな開発者グループを管理することができます。たとえば、A という成果物に含まれている API を使用する開発者と、B という成果物に含まれている API を使用する開発者向けに、それぞれ異なるカスタム グループを作成することもできます。

このガイドでは、API Management インスタンスの管理者が新しいグループを追加して成果物および開発者に関連付ける方法について説明します。

## このトピックの内容

-   [グループの作成][グループの作成]
-   [グループと成果物の関連付け][グループと成果物の関連付け]
-   [グループと開発者の関連付け][グループと開発者の関連付け]
-   [次のステップ][次のステップ]

## <a name="create-group"> </a>グループの作成

新しいグループを作成するには、ご利用の API Management サービスの Azure ポータルで **[管理コンソール]** をクリックします。API Management の管理ポータルが表示されます。

> まだ API Management サービス インスタンスを作成していない場合は、「[Azure API Management の使用][Azure API Management の使用]」チュートリアルの「[API Management インスタンスの作成][API Management インスタンスの作成]」を参照してください。

![API Management console][API Management console]

左側の **[API Management]** メニューの **[グループ]** をクリックし、**[グループの追加]** をクリックします。

![Add new group][Add new group]

グループの一意の名前とオプションの説明を入力し、**[保存]** をクリックします。

![Add new group][1]

新しいグループが [グループ] タブに表示されます。グループの **[名前]** または **[説明]** を編集するには、一覧内のグループの名前をクリックします。グループを削除するには、**[削除]** をクリックします。

![Group added][Group added]

グループが作成されます。このグループは、成果物および開発者と関連付けることができます。

## <a name="associate-group-product"> </a>グループと成果物の関連付け

グループを成果物に関連付けるには、左側の **[API Management]** メニューの **[成果物]** をクリックし、目的の成果物の名前をクリックします。

![Set visibility][Set visibility]

**[可視性]** タブを選択すると、グループを追加または削除したり、成果物の現在のグループを表示したりできます。グループを追加または削除するには、目的のグループのチェック ボックスをオンまたはオフにし、**[保存]** をクリックします。

![Set visibility][2]

> **[可視性]** タブで成果物のグループを構成するには、**[グループの管理]** をクリックします。

成果物をグループに関連付けると、そのグループに属する開発者は、成果物を表示してサブスクライブすることができます。

## <a name="associate-group-developer"> </a>グループと開発者の関連付け

グループを開発者に関連付けるには、左側の **[API Management]** メニューの **[開発者]** をクリックし、グループに関連付ける開発者の横のチェック ボックスをオンにします。

![Add developer to group][Add developer to group]

目的の開発者を選択したら、**[グループに追加]** ボックスの一覧で目的のグループをクリックします。開発者をグループから削除するには、**[グループから削除]** ボックスを使用します。

![開発者](./media/api-management-howto-create-groups/api-management-add-group-to-developer-saved.png)

開発者とグループの間に関連付けを追加すると、**[開発者]** タブにその関連付けが表示されるようになります。

## <a name="next-steps"> </a>次のステップ

開発者をグループに関連付けると、開発者は、グループに関連付けられた成果物を表示してサブスクライブすることができます。詳細については、「[Azure API Management で成果物を作成して発行する方法][Azure API Management で成果物を作成して発行する方法]」を参照してください。

  [グループの作成]: #create-group
  [グループと成果物の関連付け]: #associate-group-product
  [グループと開発者の関連付け]: #associate-group-developer
  [次のステップ]: #next-steps
  [Azure API Management の使用]: ../api-management-get-started
  [API Management インスタンスの作成]: ../api-management-get-started/#create-service-instance
  [API Management console]: ./media/api-management-howto-create-groups/api-management-management-console.png
  [Add new group]: ./media/api-management-howto-create-groups/api-management-add-group.png
  [1]: ./media/api-management-howto-create-groups/api-management-add-group-window.png
  [Group added]: ./media/api-management-howto-create-groups/api-management-new-group.png
  [Set visibility]: ./media/api-management-howto-create-groups/api-management-add-group-to-product.png
  [2]: ./media/api-management-howto-create-groups/api-management-add-group-to-product-visibility.png
  [Add developer to group]: ./media/api-management-howto-create-groups/api-management-add-group-to-developer.png
  [Azure API Management で成果物を作成して発行する方法]: ../api-management-howto-add-products
