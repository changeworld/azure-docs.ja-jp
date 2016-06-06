<properties
   pageTitle="ロールをアクティブ化または非アクティブ化する方法 | Microsoft Azure"
   description="Azure Privileged Identity Management アプリケーションで特権 ID のロールをアクティブ化する方法について説明します。"
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="05/19/2016"
   ms.author="kgremban"/>

# Azure AD Privileged Identity Management でロールをアクティブ化または非アクティブ化する方法

Azure Active Directory (AD) Privileged Identity Management を使用すると、企業における特権 ID と、Azure AD や他の Microsoft オンライン サービス (Office 365 や Microsoft Intune など) のリソースへのアクセスの管理が簡略化されます。

この記事は、Azure AD Privileged Identity Management (PIM) でロールをアクティブ化する必要のある管理者を対象とし、アクセス許可が必要になったときにロールをアクティブ化して、作業が終わったときにロールを非アクティブ化する手順について説明します。

管理ロールが割り当てられている場合は、そのロールが必要なタスクを実行する必要のあるときにロールをアクティブにできます。たとえば、Office 365 をたまに管理するだけでよい場合は、組織の特権ロール管理者に永続的な管理者ロールを割り当ててもらう必要はありません。代わりに、セキュリティ管理者は、あなたを Azure AD でグローバル管理者ロールまたは Exchange Online 管理者ロールの候補に指定します。つまり、このような権限が必要な場合には一時的なロールの割り当てを要求すると、事前に定義された期間だけ Office 365 の管理コントロールが付与されます。


## Privileged Identity Management アプリケーションの追加

別のポータルや PowerShell を使用して操作している場合でも、[Azure ポータル](https://portal.azure.com/)で Azure AD Privileged Identity Management のアプリケーションを使用して、ロールの "アクティブ化" を要求できます。Azure ポータルに Azure AD Privileged Identity Management アプリケーションがない場合は、まず、以下の手順を実行してください。

1. まだサインインしていない場合は、[Azure ポータル](https://portal.azure.com/)にサインインします。
2. 組織に複数のディレクトリがある場合は、Azure ポータルの右上隅に表示されているユーザー名をクリックし、操作するディレクトリを選択します。
3. 左側のナビゲーションの **[新規]** アイコンをクリックします。
4. [作成] メニューから **[セキュリティ + ID]** 選択します。
5. **[Azure AD Privileged Identity Management]** を選択します。
6. **[ダッシュボードにピン留めする]** チェック ボックスをオンにし、**[作成]** ボタンをクリックします。Privileged Identity Management アプリケーションが起動します。

## ロールのアクティブ化

ロールが必要な場合は、Azure AD Privileged Identity Management アプリケーションの **[ロールをアクティブ化する]** ボタンを使用してアクティブ化を要求できます。


1. [Azure ポータル](https://portal.azure.com/)にサインインし、[Azure AD Privileged Identity Management ] タイルをクリックします。
2. **[ロールをアクティブ化する]** をクリックします。割り当てられているロールの一覧が表示されます。
3. アクティブ化するロールを選択します。
4. **[アクティブ化]** を選択します。**[ロール アクティブ化要求]** ブレードが表示されます。
5. グローバル管理者などの一部のロールでは、ロールをアクティブ化する際に Multi-Factor Authentication (MFA) が要求されます。ログイン時に MFA を実行しなかった場合は、ロールをアクティブ化する際に実行する必要があります。
6. テキスト フィールドにアクティブ化要求の理由を入力します。特権ロール管理者から、トラブル チケット番号を提示するよう求められる場合もあります。
7. **[OK]** を選択します。ロールがアクティブ化され、ロールの変更が Microsoft Online Services に表示されます。

## ロールの非アクティブ化

ロールは、アクティブになった後、期限に達すると自動的に非アクティブになります。

早期に終了した場合は、Azure AD Privileged Identity Management アプリケーションでロールを手動で非アクティブにすることもできます。**[ロールをアクティブ化する]** を選択し、不要になったロールを検索して、**[非アクティブ化]** を選択します。


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 次のステップ

Azure AD Privileged Identity Management の詳細を、次のリンクで確認できます。

[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0525_2016-->