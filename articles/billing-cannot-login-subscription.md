<properties
	pageTitle="Azure サブスクリプションにログインして管理することができない | Microsoft Azure"
	description="Azure サブスクリプションのログインに関するいくつかの一般的な問題について、トラブルシューティング情報を掲載しています。"
	services=""
	documentationCenter=""
	authors="genlin"
	manager="msmbaldwin"
	editor=""
	tags="billing"
	/>

<tags
	ms.service="billing"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/17/2016"
	ms.author="genli"/>

# Azure サブスクリプションにログインして管理することができない

> [AZURE.NOTE] この記事についてさらにヘルプが必要な場合は、問題を迅速に解決できるよう[サポートにお問い合わせ](http://go.microsoft.com/fwlink/?linkid=544831&clcid=0x409)ください。

この記事は、ログインの問題のよくあるいくつかの原因のトラブルシューティングに役立ちます。

## どのポータルにアクセスしようとしていますか?

アカウント管理者は[アカウント センター](https://account.windowsazure.com/)のみにアクセスできるのに対し、サービス管理者 (SA) と共同管理者 (CA) がアクセスできるのは [Azure ポータル](https://portal.azure.com)または [Azure クラシック ポータル](https://manage.windowsazure.com/)のみです。

Azure 管理者ロールの詳細については、「[Azure 管理者ロールを追加または変更する方法](billing-add-change-azure-subscription-administrator.md)」を参照してください。

## サブスクリプションは Microsoft アカウントまたは組織アカウントに関連付けられていますか?

Microsoft アカウントとはお使いの電子メール アドレスとそのパスワードです。これを使用して、Windows Live プログラムまたはサービス (Outlook、Hotmail、MSN、OneDrive など) にサインインします。Microsoft アカウントを設定するには、会社の電子メールなど、所有する任意の電子メール アドレスを使用します。詳しくは、[www.microsoft.com/account](http://www.microsoft.com/account) をご覧ください。

アカウントを組織アカウントに関連付けた場合、適切なログイン オプション次のように選択してください。組織アカウントの使用方法について詳しくは、「[Azure への組織としてのサインアップ](./active-directory/sign-up-organization.md)」を参照してください。

![サインイン ページ](./media/billing-cannot-login-subscription/signin.png)

## 共同管理者: 他のアカウントを管理するのに適切な種類のアカウントを使用していますか?

- Microsoft アカウントを使用してログインした場合、他の Microsoft アカウントを共同管理者として追加することしかできません。これは、非組織アカウントが特定のアカウント (たとえば、janedoe@contoso.com) が有効であるかどうかを検出することを防止するためのセキュリティ上の要件です。
- 組織アカウントを使用してログインした場合、組織内の他の組織アカウントを共同管理者として追加することができます。たとえば、abby@contoso.com はサービス管理者または共同管理者として bob@contoso.com を追加できますが、john@notcontoso.com を追加することはできません。組織アカウントでログインしているユーザーは、Microsoft アカウント ユーザーをサービス管理者または共同管理者として追加することもできます。

組織アカウントで Azure にログインできるようになると、サービス管理者 (SA) と共同管理者 (CA) アカウントの要件は次のように変わります。

| ログイン方法| Microsoft アカウントを共同管理者またはサービス管理者として追加できる |同じ組織内の組織アカウントを共同管理者またはサービス管理者として追加できる |異なる組織の組織アカウントを共同管理者またはサービス管理者として追加できる
| ------------- | ------------- |---------------|---------------|
|Microsoft アカウント |はい|なし|なし|
|組織アカウント|はい|はい|なし|

## インターネット ブラウザーに問題がある

IE InPrivate ブラウズ モードを使用したキャッシュ/Cookie の削除や、他のブラウザーの使用を試してください。

> [AZURE.NOTE] まだ他に質問がある場合は、問題を迅速に解決できるよう[サポートにお問い合わせ](http://go.microsoft.com/fwlink/?linkid=544831&clcid=0x409)ください。

<!---HONumber=AcomDC_0824_2016-->