<properties
	pageTitle="Azure Active Directory のレポート通知"
	description="疑わしいサインインに対して Azure Active Directory のレポート通知を使用する方法。"
	services="active-directory"
	documentationCenter=""
	authors="dhanyahk"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/07/2016"
	ms.author="dhanyahk"/>

# Azure Active Directory のレポート通知

## 電子メール通知を生成するレポート

現時点では、電子メール通知は、不規則なサインイン アクティビティ レポートによってのみトリガーされます。

## "不規則なサインイン" とは何ですか。

不規則なサインインは、異常なサインイン場所とデバイスを組み合わせた「不可能な移動」条件に基づいてマイクロソフトの機械学習アルゴリズムによって識別されるサインインです。これは、ハッカーがこのアカウントを使用してサインインを試行したことを示している可能性があります。

## 誰が電子メールの通知を受け取りますか。

電子メールは、Active Directory Premium ライセンスが割り当てられているすべてのグローバル管理者に送信されます。確実に配信されるようにするために、管理者の連絡用電子メール アドレスにも送信されます。管理者は、aad-alerts-noreply@mail.windowsazure.com電子メールを確実に受信するために、差出人セーフ リストに登録する必要があります。

## どの程度の頻度で電子メールが送信されますか。

電子メールは、過去 30 日以内、または最後の電子メールの送信後、10 回の新しい不規則なサインイン アクティビティが発生した場合に送信されます。

## 電子メールに記載されているレポートには、どのようにアクセスできますか。

リンクをクリックすると、Azure クラシック ポータル内のレポート ページにリダイレクトされます。レポートにアクセスするには、次の両方の条件を満たす必要があります。

- Azure サブスクリプションの管理者または共同管理者

- ディレクトリのグローバル管理者であり、Active Directory Premium ライセンスが割り当てられている詳細については、「[Azure Active Directory のエディション](active-directory-editions.md)」をご覧ください。

## 電子メールの通知を無効にすることができますか。

はい。Azure クラシック ポータル内で異常なサインインに関連する通知をオフにするには、**[構成]** をクリックして **[通知]** セクションの下で **[無効]** をクリックします。

## 参照トピック
- 使用可能なセキュリティ、監査、およびアクティビティ レポートに興味がある場合は、 「[View your access and usage reports (アクセスおよび使用状況のレポートの表示)](active-directory-view-access-usage-reports.md)」を参照してください
- [Azure Active Directory Premium の概要](active-directory-get-started-premium.md)
- [サインイン ページとアクセス パネル ページに対する会社のブランドの追加](active-directory-add-company-branding.md)

<!---HONumber=AcomDC_0928_2016-->