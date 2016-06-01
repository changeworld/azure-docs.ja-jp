<properties
   pageTitle="Azure AD Privileged Identity Management の使用 | Microsoft Azure"
   description="Azure ポータルの Azure Active Directory Privileged Identity Management アプリケーションで特権 ID を管理する方法について説明します。"
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="05/19/2016"
   ms.author="kgremban"/>

# Azure AD Privileged Identity Management の使用


Azure Active Directory (AD) Privileged Identity Management (PIM) を使用すると、特権 ID と、Azure AD や他の Microsoft オンライン サービス (Office 365 や Microsoft Intune など) のリソースへのアクセスを管理、制御、監視できます。

この記事では、Azure AD PIM アプリを Azure ポータル ダッシュボードに追加する方法を説明します。

## Privileged Identity Management アプリケーションの追加

Azure AD Privileged Identity Management を使用するには、まず Azure ポータル ダッシュボードにアプリケーションを追加する必要があります。

1. ディレクトリのグローバル管理者として [Azure ポータル](https://portal.azure.com/)にサインインします。
2. 組織に複数のディレクトリがある場合は、Azure ポータルの右上隅に表示されているユーザー名をクリックし、PIM を使用するディレクトリを選択します。
3. 左側のナビゲーションの **[新規]** アイコンをクリックします。
4. **[セキュリティ + ID]** を選択します。
5. **[Azure AD Privileged Identity Management]** を選択します。
6. **[ダッシュボードにピン留めする]** チェック ボックスをオンにし、**[作成]** ボタンをクリックします。Privileged Identity Management アプリケーションが起動します。


ディレクトリ内で Azure AD Privileged Identity Management を使用する最初のユーザーには、[セキュリティ ウィザード](active-directory-privileged-identity-management-security-wizard.md)に最初の割り当て操作の手順が表示されます。手順を実行した後、ディレクトリの最初の**セキュリティ管理者**と**特権ロール管理者**に自動的に設定されます。特権ロール管理者だけが、このアプリケーションにアクセスして他の管理者のアクセスを管理できます。

自分が PIM を最初に使用するユーザーではなく、別の特権ロール管理者によって 1 つ以上のロールが割り当てられている場合は、アクティブ化するロールを選択できます。自分にも特権ロール管理者ロールが割り当てられている場合は、**ユーザーの管理**も選択肢として表示されます。


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 次のステップ

[Azure AD Privileged Identity Management の概要](active-directory-privileged-identity-management-configure.md)に関するページに、組織で管理アクセスを管理する方法の詳細が記載されています。

[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0525_2016-->