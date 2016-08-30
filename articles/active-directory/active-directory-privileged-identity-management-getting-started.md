<properties
   pageTitle="Azure AD Privileged Identity Management の使用 | Microsoft Azure"
   description="Azure ポータルの Azure Active Directory Privileged Identity Management アプリケーションで特権 ID を管理する方法について説明します。"
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="06/29/2016"
   ms.author="kgremban"/>

# Azure AD Privileged Identity Management の使用


Azure Active Directory (AD) Privileged Identity Management (PIM) を使用すると、特権 ID と、Azure AD や他の Microsoft オンライン サービス (Office 365 や Microsoft Intune など) のリソースへのアクセスを管理、制御、監視できます。

この記事では、Azure AD PIM アプリを Azure ポータル ダッシュボードに追加する方法を説明します。

## Privileged Identity Management アプリケーションの追加

Azure AD Privileged Identity Management を使用するには、まず Azure ポータル ダッシュボードにアプリケーションを追加する必要があります。

1. ディレクトリのグローバル管理者として [Azure ポータル](https://portal.azure.com/)にサインインします。
2. 組織に複数のディレクトリがある場合は、Azure ポータルの右上隅に表示されているユーザー名をクリックし、PIM を使用するディレクトリを選択します。
3. **[新規]**、**[セキュリティ + ID]**、**[Azure AD Privileged Identity Management]** の順に選択します。

    ![Enable PIM in the portal][1]

4. **[ダッシュボードにピン留めする]** チェック ボックスをオンにし、**[作成]** をクリックします。Privileged Identity Management アプリケーションが起動します。


ディレクトリ内で Azure AD Privileged Identity Management を使用する最初のユーザーには、[セキュリティ ウィザード](active-directory-privileged-identity-management-security-wizard.md)に最初の割り当て操作の手順が表示されます。手順を実行した後、ディレクトリの最初の**セキュリティ管理者**と**特権ロール管理者**に自動的に設定されます。特権ロール管理者だけが、このアプリケーションにアクセスして他の管理者のアクセスを管理できます。

1 つ以上のロールに割り当てられると、**[ロールをアクティブ化する]** オプションが表示されます。特権ロール管理者である場合は、**[特権ロールの管理]** オプションも表示されます。


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 次のステップ

[Azure AD Privileged Identity Management の概要](active-directory-privileged-identity-management-configure.md)に関するページに、組織で管理アクセスを管理する方法の詳細が記載されています。

[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-configure/PIM_EnablePim.png

<!-----HONumber=AcomDC_0706_2016-->