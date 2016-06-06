<properties
	pageTitle="Azure AD Privileged Identity Management |Microsoft Azure"
	description="Azure AD Privileged Identity Management (PIM) の機能と、PIM を使用してクラウド セキュリティを向上させる方法について説明します。"
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/19/2016"
	ms.author="kgremban"/>

# Azure AD Privileged Identity Management

Azure Active Directory (AD) Privileged Identity Management (PIM) を使用すると、特権 ID と、Azure AD や他の Microsoft オンライン サービス (Office 365 や Microsoft Intune など) のリソースへのアクセスを管理、制御、監視できます。

ユーザーは、Azure や Office 365 のリソース、または他の SaaS アプリで、特権操作を実行することが必要になる場合があります。通常は、組織がユーザーに Azure AD で永続的な特権アクセスを付与する必要があります。しかし、この措置では、ユーザーが管理者特権を使用して実行している内容を組織が十分に監視できないため、クラウドでホストされているリソースのセキュリティ リスクが増大します。また、特権アクセスを持つユーザー アカウントが侵害された場合に、その 1 つの侵害がクラウド セキュリティ全体に影響を与える可能性もあります。Azure AD Privileged Identity Management はこのリスクの解決に役立ちます。

Azure AD Privileged Identity Management では、次のことが可能です。

- Azure AD の管理者であるユーザーを特定する
- Office 365 や Intune などの Microsoft Online Services へのオンデマンドの "ジャスト イン タイム" な管理アクセスを可能にする
- 管理者のアクセス履歴と管理者の割り当ての変更に関するレポートを取得する
- 特権ロールへのアクセスに関するアラートを受け取る

Azure AD Privileged Identity Management では、次のような組み込みの Azure AD 組織ロールを管理できます。

- グローバル管理者
- 課金管理者
- サービス管理者  
- ユーザー管理者
- パスワード管理者

## ジャスト イン タイム管理者アクセス

これまで、管理ロールへのユーザーの割り当てには、Azure 管理ポータルか Windows PowerShell を使用できました。これらの方法では、ユーザーはそのロールの**永続的な管理者**になり、割り当てられたロールが常に有効になります。Azure AD Privileged Identity Management には、ロールの**一時的な管理者**という概念が導入されています。一時的な管理者となるユーザーは、割り当てられたロールのアクティブ化プロセスを完了する必要があります。このアクティブ化プロセスによって、Azure AD におけるユーザーへのロールの割り当てが、8 時間などの指定された期間だけ、非アクティブからアクティブに変更されます。

## ディレクトリで Privileged Identity Management を有効にする

[Azure ポータル](https://portal.azure.com/)にアクセスすることで、Azure AD Privileged Identity Management の使用を開始できます。Azure AD Privileged Identity Management は、以前のクラシック ポータルには表示されません。

>[AZURE.NOTE] ディレクトリで Azure AD Privileged Identity Management を有効にする操作は、Microsoft アカウントではなく組織アカウントを持つグローバル管理者が行う必要があります。

1. ディレクトリのグローバル管理者として [Azure ポータル](https://portal.azure.com/)にサインインします。
2. 組織に複数のディレクトリがある場合は、Azure ポータルの右上隅に表示されているユーザー名をクリックし、Azure AD Privileged Identity Management を使用するディレクトリを選択します。
3. 左側のナビゲーションの **[新規]** アイコンをクリックします。
4. **[セキュリティ + ID]** を選択します。
5. **[Azure AD Privileged Identity Management]** を選択します。
6. **[ダッシュボードにピン留めする]** チェック ボックスをオンにし、**[作成]** ボタンをクリックします。Privileged Identity Management ダッシュボードが開きます。

ディレクトリ内で Azure AD Privileged Identity Management を使用する最初のユーザーには、[セキュリティ ウィザード](active-directory-privileged-identity-management-security-wizard.md)に最初の割り当て操作の手順が表示されます。手順を実行した後、ディレクトリの最初の**セキュリティ管理者**と**特権ロール管理者**に自動的に設定されます。

PIM アプリを使用して他の管理者のアクセスを管理できるのは、特権ロール管理者のみです。特権ロール管理者は [PIM で管理する権限を他のユーザーに付与する](active-directory-privileged-identity-management-how-to-give-access-to-pim.md)ことができます。

## Privileged Identity Management ダッシュボード

Azure AD Privileged Identity Manager には、次のような重要な情報を提供するダッシュボードがあります。

- 各特権ロールに割り当てられているユーザーの数  
- 一時的管理者および永続的管理者の数
- 各管理者のアクセス履歴

![PIM dashboard - screenshot][2]

## 特権ロールの管理

Azure AD Privileged Identity Management では、各ロールに永続的または一時的な管理者を追加または削除することによって、管理者を管理できます。

![PIM の管理者の追加/削除 - スクリーンショット][3]

## ロール アクティブ化設定の構成

ロールのアクティブ化の設定を使用して、次のような一時的ロール アクティブ化プロパティを構成できます。

- ロールのアクティブ化の期間
- ロールのアクティブ化の通知
- ロール アクティブ化プロセス中にユーザーが提供する必要のある情報  

![PIM 設定 - 管理者アクティブ化 - スクリーンショット][4]

## ロールのアクティブ化  

ロールをアクティブ化するには、一時的管理者は、ロールに対する時間制限付き「アクティブ化」を要求する必要があります。アクティブ化の要求は、Azure AD Privileged Identity Management の **[ロールのアクティブ化]** オプションを使用して行うことができます。

ロールのアクティブ化を要求する管理者は、Azure ポータルで Azure AD Privileged Identity Management を初期化する必要があります。

任意の種類の管理者が、Azure AD Privileged Identity Management を使用して自分のロールをアクティブ化できます。

ロールのアクティブ化には時間制限があります。ロールのアクティブ化の設定では、アクティブ化の期間だけでなく、ロールをアクティブ化するために管理者が指定する必要がある情報を設定できます。

![PIM 管理者のロール アクティブ化要求 - スクリーン ショット][5]

## ロール アクティブ化の履歴

Azure AD Privileged Identity Management を使用すると、特権ロールの割り当ての変更およびロールのアクティブ化の履歴を追跡することもできます。これは、監査ログ オプションを使用して行うことができます。

![PIM アクティブ化履歴 - スクリーンショット][6]

## 次のステップ
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[2]: ./media/active-directory-privileged-identity-management-configure/PIM_Dash.png
[3]: ./media/active-directory-privileged-identity-management-configure/PIM_AddRemove.png
[4]: ./media/active-directory-privileged-identity-management-configure/PIM_RoleActivationSettings.png
[5]: ./media/active-directory-privileged-identity-management-configure/PIM_RequestActivation.png
[6]: ./media/active-directory-privileged-identity-management-configure/PIM_ActivationHistory.png

<!---HONumber=AcomDC_0525_2016-->