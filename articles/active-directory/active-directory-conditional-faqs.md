<properties
	pageTitle="条件付きアクセスのよく寄せられる質問 | Microsoft Azure"
	description="条件付きアクセスに関してよく寄せられる質問"
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="swadhwa"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/15/2016"
	ms.author="femila"/>

# 条件付きアクセスのよく寄せられる質問

## 条件付きアクセス ポリシーを使用するアプリケーションにはどのようなものがありますか?
「[条件付きアクセス - サポートされるアプリケーション)](active-directory-conditional-access-supported-apps.md)」のトピックを参照してください。

## 条件付きアクセス ポリシーは、B2B コラボレーション ユーザーおよびゲスト ユーザーには適用されますか?
B2B コラボレーション ユーザーにはポリシーが適用されます。ただし、ユーザーがポリシー要件を満たすことができない場合もあります。たとえば、組織が多要素認証をサポートしていない場合などです。現在、SharePoint ゲスト ユーザーにはポリシーが適用されません。ゲスト リレーションシップが SharePoint 内で維持されるため、ゲスト ユーザー アカウントは認証サーバーのアクセス ポリシーに影響を受けません。ゲスト アクセスは、SharePoint で管理できます。

## SharePoint Online ポリシーは、OneDrive for Business にも適用されますか?
はい。
 
## Word や Outlook などのクライアント アプリにポリシーを設定できないのはなぜですか?
条件付きアクセス ポリシーは、サービスへのアクセスの要件を設定し、そのサービスへの認証を実行するときに適用されます。したがって、ポリシーはクライアント アプリケーションで直接設定されるのではなく、サービスを呼び出すときに適用されます。たとえば、SharePoint で設定したポリシーは、SharePoint を呼び出すクライアントに適用されて、Exchange で設定したポリシーは、Outlook に適用されます。

<!---HONumber=AcomDC_0615_2016-->