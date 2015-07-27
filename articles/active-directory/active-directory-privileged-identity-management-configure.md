<properties 
	pageTitle="Azure AD Privileged Identity Management" 
	description="Azure AD Privileged Identity Management とは何か、およびその構成方法について説明します。" 
	services="active-directory" 
	documentationCenter="" 
	authors="Justinha" 
	manager="TerryLan" 
	editor="LisaToft"/>

<tags 
	ms.service="active-directory" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/04/2015" 
	ms.author="Justinha"/>

# Azure AD Privileged Identity Management

Azure AD Privileged Identity Management を使用すると、特権 ID と、Azure AD および他の Microsoft オンライン サービス (Office 365 や Microsoft Intune など) のリソースへの特権 ID のアクセスを管理、制御、監視できます。

ユーザーが特権操作を実行できるよう、組織は多くのユーザーに Azure AD、Azure や Office 365 のリソース、他の SaaS アプリへの永続的な特権アクセスを付与することが必要になる場合がよくあります。多くの顧客にとって、これは、このようなユーザーが管理者特権で行っていることを十分に監視できないため、クラウドでホストされるリソースに対するセキュリティ リスクの増大になります。さらに、特権アクセス権を持つユーザー アカウントが侵害されると、全体的なクラウド セキュリティに影響を与える可能性があります。Azure AD Privileged Identity Management はこのリスクの解決に役立ちます。

このプレビューの Azure AD Privileged Identity Management では次のことが可能です。

- Azure AD の管理者であるユーザーを検出する
- ディレクトリ リソースへのオンデマンド "ジャスト イン タイム" 管理アクセスを有効にする
- 管理者のアクセス履歴および管理者の割り当ての変更に関するレポートを取得する 
- 特権ロールへのアクセスに関するアラートを受け取る 

このプレビューの Azure AD Privileged Identity Management では、次の組み込み Azure Active Directory 組織ロールを管理できます。

- グローバル管理者 
- 課金管理者 
- サービス管理者  
- ユーザー管理者 
- パスワード管理者 

## ジャスト イン タイム管理者アクセス 

従来、管理者ロールへのユーザーの割り当てには、Azure 管理ポータルまたは Windows PowerShell が使用されています。その結果、そのユーザーは**永続的な管理者**になり、割り当てられたロールが常に有効になっています。このプレビューでは、**一時的な管理者**のサポートが追加されています。これは、割り当てられたロールに対するアクティブ化プロセスを完了する必要があるユーザーです。アクティブ化プロセスにより、Azure AD でのロールへのユーザーの割り当てが非アクティブからアクティブに変わります。

## ディレクトリの Privileged Identity Management の有効化

[Microsoft Azure ポータル](https://portal.azure.com/)にアクセスすることで、Azure AD Privileged Identity Management の使用を開始できます。現在、Azure AD Privileged Identity Management は Microsoft Azure ポータルのみに表示されます。ディレクトリの Azure AD Privileged Identity Management の有効化は、グローバル管理者が行う必要があります。

![][1]

この拡張機能を初期化したユーザーは、自動的にディレクトリの最初の**セキュリティ管理者**になります。セキュリティ管理者だけが、この拡張機能にアクセスして他の管理者のアクセスを管理できます。初期化中に、Azure AD Privileged Identity Management のタイルが、Azure プレビュー ポータルのスタート画面に追加されます。

## Privileged Identity Management ダッシュボード 

Azure AD Privileged Identity Management には、次のような重要な情報を提供するダッシュボードがあります。

- 各特権ロールに割り当てられているユーザーの数  
- 一時的管理者および永続的管理者の数 
- 管理者のアクセスの履歴 

![][2]

## 特権ロールの管理 

Azure AD Privileged Identity Management では、各ロールに永続的または一時的な管理者を追加または削除することによって、管理者を管理できます。

![][3]

## ロール アクティブ化設定の構成 

ロールのアクティブ化の設定を使用して、次のような一時的ロール アクティブ化プロパティを構成できます。

- ロールのアクティブ化の期間
- ロールのアクティブ化の通知 
- ロール アクティブ化プロセス中にユーザーが提供する必要のある情報  

![][4]

## ロールのアクティブ化  

ロールをアクティブ化するには、一時的管理者は、ロールに対する時間制限付き「アクティブ化」を要求する必要があります。アクティブ化の要求は、Azure AD Privileged Identity Management の **[ロールのアクティブ化]** オプションを使用して行うことができます。

ロールのアクティブ化を要求する管理者は、Azure プレビュー ポータルで Azure AD Privileged Identity Management を初期化する必要があります。

任意の種類の管理者が、Azure AD Privileged Identity Management を使用して自分のロールをアクティブ化できます。
 
ロールのアクティブ化には時間制限があります。ロール アクティブ化の設定では、アクティブ化の期間だけでなく、ロールをアクティブ化するために管理者が指定する必要がある情報を構成できます。

![][5]

## ロール アクティブ化の履歴

Azure AD Privileged Identity Management を使用すると、特権ロールの割り当ての変更およびロールのアクティブ化の履歴を追跡することもできます。これは、監査ログ オプションを使用して行うことができます。

![][6]

## 参照トピック

[Microsoft Azure ブログ](http://azure.microsoft.com/blog/) [Azure プレビュー ポータルでのロール ベースのアクセス制御](../role-based-access-control-configure.md)

<!--Image references-->
[1]: ./media/active-directory-privileged-identity-management-configure/Search_PIM.png
[2]: ./media/active-directory-privileged-identity-management-configure/PIM_Dash.png
[3]: ./media/active-directory-privileged-identity-management-configure/PIM_AddRemove.png
[4]: ./media/active-directory-privileged-identity-management-configure/PIM_RoleActivationSettings.png
[5]: ./media/active-directory-privileged-identity-management-configure/PIM_RequestActivation.png
[6]: ./media/active-directory-privileged-identity-management-configure/PIM_ActivationHistory.png

 

<!---HONumber=July15_HO3-->