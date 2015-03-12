<properties 
	pageTitle="Azure API Management の開発者アカウントの管理方法" 
	description="<作成または Azure API Management の開発者を招待する方法をについてください。" 
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

# Azure API Management で開発者アカウントを管理する方法

API Management (プレビュー) では、開発者は、API Management を使用して公開された API のユーザーになります。このガイドでは、開発者を作成する方法と、API Management インスタンスで公開した API および成果物を案内する招待メッセージを開発者に送信する方法について説明します。

## このトピックの内容

-   [新しい開発者の作成][新しい開発者の作成]
-   [開発者の招待][開発者の招待]
-   [開発者アカウントの非アクティブ化および再アクティブ化][開発者アカウントの非アクティブ化および再アクティブ化]
-   [次のステップ][次のステップ]

## <a name="create-developer"> </a>新しい開発者の作成

新しい開発者を作成するには、ご利用の API Management サービスの Azure ポータルで **[管理コンソール]** をクリックします。API Management の管理ポータルが表示されます。

> まだ API Management サービス インスタンスを作成していない場合は、「[Azure API Management の使用][Azure API Management の使用]」チュートリアルの「[API Management インスタンスの作成][API Management インスタンスの作成]」を参照してください。

![API Management console][API Management console]

左側の **[API Management]** メニューの **[開発者]** をクリックし、**[ユーザーの追加]** をクリックします。

![Create developer][Create developer]

新しい開発者の**電子メール**、**パスワード**、および**名前**を入力し、**[保存]** をクリックします。

![Create developer][1]

既定では、新しく作成された開発者アカウントは**アクティブ**になり、**開発者**グループに関連付けられます。

![New developer][New developer]

**[アクティブ]** 状態の開発者アカウントを使用すると、サブスクリプションがあるすべての API にアクセスできます。新しく作成した開発者を追加のグループに関連付ける方法については、「[グループと開発者の関連付け][グループと開発者の関連付け]」を参照してください。

## <a name="invite-developer"> </a>開発者の招待

開発者を招待するには、左側の **[API Management]** メニューの **[開発者]** をクリックし、**[ユーザーの招待]** をクリックします。

![Invite developer][Invite developer]

開発者の名前と電子メール アドレスを入力し、**[招待]** をクリックします。

![Invite developer][2]

確認メッセージが表示されます。ただし、新しく招待した開発者は、招待が受け入れられるまで一覧に表示されません。

![Invite confirmation][Invite confirmation]

> 開発者を招待すると、招待された開発者に電子メールが送信されます。この電子メールはテンプレートを使用して生成され、カスタマイズできます。詳細については、「[電子メール テンプレートの構成][電子メール テンプレートの構成]」を参照してください。

招待が受け入れられると、そのアカウントがアクティブになります。

## <a name="block-developer"> </a>開発者アカウントの非アクティブ化および再アクティブ化

既定では、新しく作成または招待された開発者アカウントは**アクティブ**になります。開発者アカウントを非アクティブ化するには、**[ブロック]** をクリックします。ブロックされている開発者アカウントを再アクティブ化するには、**[アクティブ化]** をクリックします。ブロックされている開発者アカウントは、開発者ポータルにアクセスすることも、API を呼び出すこともできません。

![Block developer][New developer]

## <a name="next-steps"> </a>次のステップ

開発者アカウントを作成した後は、このアカウントをロールに関連付け、成果物と API をサブスクライブできます。詳細については、「[How to create and use groups (グループを作成して使用する方法)][How to create and use groups (グループを作成して使用する方法)]」を参照してください。

  [新しい開発者の作成]: #create-developer
  [開発者の招待]: #invite-developer
  [開発者アカウントの非アクティブ化および再アクティブ化]: #block-developer
  [次のステップ]: #next-steps
  [Azure API Management の使用]: ../api-management-get-started
  [API Management インスタンスの作成]: ../api-management-get-started/#create-service-instance
  [API Management console]: ./media/api-management-howto-create-or-invite-developers/api-management-management-console.png
  [Create developer]: ./media/api-management-howto-create-or-invite-developers/api-management-create-developer.png
  [1]: ./media/api-management-howto-create-or-invite-developers/api-management-add-new-user.png
  [New developer]: ./media/api-management-howto-create-or-invite-developers/api-management-new-developer.png
  [グループと開発者の関連付け]: ../api-management-howto-create-groups/#associate-group-developer
  [Invite developer]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer.png
  [2]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer-window.png
  [Invite confirmation]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer-confirmation.png
  [電子メール テンプレートの構成]: ../api-management-howto-configure-notifications/#email-templates
  [How to create and use groups (グループを作成して使用する方法)]: ../api-management-howto-create-groups

<!--HONumber=46--> 
