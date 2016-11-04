---
title: 'Azure Active Directory B2C: Azure Active Directory B2C テナントを作成する | Microsoft Docs'
description: Azure Active Directory B2C テナントの作成方法に関するトピック
services: active-directory-b2c
documentationcenter: ''
author: swkrish
manager: msmbaldwin
editor: bryanla

ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 08/30/2016
ms.author: swkrish

---
# Azure Active Directory B2C: Azure AD B2C テナントを作成する
Microsoft Azure Active Directory (Azure AD) B2C を使い始めるには、以下に示す 3 つの手順を実行します。

## 手順 1: Azure サブスクリプションにサインアップする
既に Azure サブスクリプションがある場合は、この手順を省略できます。ない場合は、[Azure サブスクリプション](../active-directory/sign-up-organization.md)にサインアップし、Azure AD B2C にアクセスできるようにします。

## 手順 2: Azure AD B2C テナントを作成する
次の手順に従って、新しい Azure AD B2C テナントを作成します。現在、既存のテナントで B2C の機能を有効にすることはできません。

1. サブスクリプション管理者として [Azure クラシック ポータル](https://manage.windowsazure.com/)にサインインします。これは、Azure へのサインアップに使用したものと同じ職場/学校アカウント、または同じ Microsoft アカウントです。
2. **[新規]**、**[App Services]**、**[Active Directory]**、**[ディレクトリ]**、**[カスタム作成]** の順にクリックします。
   
    ![テナント作成時のスクリーン ショット](./media/active-directory-b2c-get-started/new-directory.png)
3. テナントの **[名前]**、**[ドメイン名]**、**[国/地域]** を選択します。
4. **[これは B2C ディレクトリです]** オプションをオンにします。
5. チェック マークをクリックして設定を完了します。
   
    ![B2C ディレクトリ作成時のチェック マークのスクリーン ショット](./media/active-directory-b2c-get-started/create-b2c-directory.png)
6. テナントが作成され、Active Directory 拡張機能に表示されます。また、現在のユーザーがテナントのグローバル管理者になります。必要に応じて、他のグローバル管理者を追加できます。
   
   > [!IMPORTANT]
   > B2C テナントを運用アプリ向けに使用する予定がある場合は、[運用スケールとプレビュー B2C テナントの比較](active-directory-b2c-reference-tenant-type.md)に関する記事を参照してください。既存の B2C テナントを削除し、同じドメイン名でそれを再作成する場合に、既知の問題があることに注意してください。B2C テナントは異なるドメイン名で作成する必要があります。
   > 
   > 

## 手順 3: Azure ポータルで B2C 機能ブレードに移動する
1. 左側のナビゲーション バーで Active Directory 拡張機能に移動します。
2. **[ディレクトリ]** タブで、作成したテナントを見つけてクリックします。
3. [**構成**] タブをクリックします。
4. **[B2C 管理]** セクションの **[B2C 設定の管理]** リンクをクリックします。
   
    ![B2C のディレクトリ構成のスクリーン ショット](./media/active-directory-b2c-get-started/b2c-directory-configure-tab.png)
5. 新しいブラウザー タブまたはウィンドウで、B2C 機能ブレードが含まれた Azure ポータルが開きます。
   
   > [!IMPORTANT]
   > テナントが Azure ポータルでアクセスできるようになるまでに、最大で 2 ～ 3 分かかる場合があります。しばらく待ってからこれらの手順をもう一度試すと、これは修正されます。解決されない場合は、サポートにご連絡ください。
   > 
   > 
6. アクセスしやすいように、このブレードをスタート画面にピン留めします(機能ブレードの右上隅、赤でマークされているのがピン留めツールです)。
   
    ![B2C 機能ブレードのスクリーン ショット](./media/active-directory-b2c-get-started/b2c-features-blade.png)
   
   > [!NOTE]
   > [Azure クラシック ポータル](https://manage.windowsazure.com/)では、テナントのユーザーとグループ、セルフサービスによるパスワードのリセット構成、会社のブランド化機能を管理できます。
   > 
   > 

## Azure ポータルの B2C 機能ブレードへの簡単アクセス
Azure ポータルの B2C 機能ブレードへのショートカットを追加し、アクセスしやすくしました。

1. B2C テナントの全体管理者として Azure ポータルにサインインします。既に別のテナントにサインインしている場合は、テナントを切り替えます (右上隅)。
2. 左側のナビゲーションで **[参照]** をクリックします。
3. **[Azure AD B2C]** をクリックして B2C 機能ブレードにアクセスします。
   
    ![Screen shot of Browse to B2C features blade](./media/active-directory-b2c-get-started/b2c-browse.png)

## 次のステップ
「[Azure Active Directory B2C: アプリケーションを登録する](active-directory-b2c-app-registration.md)」を読み、Azure AD B2C にアプリケーションを登録する方法と、クイック スタート アプリケーションを構築する方法を学習します。

<!---HONumber=AcomDC_0831_2016-->