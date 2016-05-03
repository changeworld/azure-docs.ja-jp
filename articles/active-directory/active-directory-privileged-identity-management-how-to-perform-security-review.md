<properties
   pageTitle="セキュリティ レビューを実行する方法 | Microsoft Azure"
   description="Azure Privileged Identity Management アプリケーションでレビューを実行する方法について説明します。"
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
   ms.date="04/15/2016"
   ms.author="kgremban"/>

# Azure AD Privileged Identity Management でセキュリティ レビューを実行する方法

Azure Active Directory (AD) Privileged Identity Management を使用すると、企業における特権 ID と、Azure AD や他の Microsoft オンライン サービス (Office 365 や Microsoft Intune など) のリソースへのアクセスの管理が簡略化されます。

既に管理者ロールに割り当てられているユーザーは、組織のセキュリティ管理者から、自分の業務にそのロールがまだ必要であるかどうかを確認するよう求められることがあります。リンクが記載された電子メールが届く場合もあれば、直接 [Azure ポータル](https://portal.azure.com)にアクセスすることもできます。この記事に記載された手順に従って、割り当てられたロールの自己レビューを実行することができます。

セキュリティ管理者としてセキュリティ レビューに関心がある場合は、「[セキュリティ レビューを開始する方法](active-directory-privileged-identity-management-how-to-start-security-review.md)」で詳細を確認できます。

## Privileged Identity Management アプリケーションの追加

[Azure ポータル](https://portal.azure.com/) で Azure AD Privileged Identity Management (PIM) アプリケーションを使用して、レビューを実行できます。ポータルに Azure AD Privileged Identity Management アプリケーションがない場合は、まず、次の手順を実行してください。

1. まだサインインしていない場合は、[Azure ポータル](https://portal.azure.com/)にサインインします。
2. 組織に複数のディレクトリがある場合は、Azure ポータルの右上隅に表示されているユーザー名をクリックし、操作するディレクトリを選択します。
3. 左側のナビゲーションの **[新規]** アイコンをクリックします。
4. メニューから **[セキュリティ + ID]** を選択します。
5. **[Azure AD Privileged Identity Management]** を選択します。
6. **[ダッシュボードにピン留めする]** チェック ボックスはオンのままで **[作成]** ボタンをクリックします。Privileged Identity Management アプリケーションが起動します。


## アクセスの承認または拒否

アクセス権は[レビューが完了する](active-directory-privileged-identity-management-how-to-complete-review.md)まで変更されません。このプロセスは単に、ロールのアクセスを変更するユーザーのチェックリストを作成します。少なくとも 1 人のユーザーを選択すると、**[アクセスを承認する]** ボタンと **[アクセスを拒否する]** ボタンが有効になります。

1. PIM アプリケーションのメイン メニューで、**[管理アクセスのレビュー]** をクリックします。セキュリティ レビューの一覧が表示されます。
2. 一覧でアクセスを変更する**ユーザー**を選択します。
3. 選択したユーザーの **[アクセスを許可]** または **[アクセスを拒否]** をクリックします。Azure ポータルのメイン メニューに通知が表示され、レビュー一覧で選択した名前が表示されなくなります (フィルター オプションを変更すると、再び表示できます)。**[Azure AD ロールのレビュー]** ブレードを閉じます。


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 次のステップ
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0420_2016-->