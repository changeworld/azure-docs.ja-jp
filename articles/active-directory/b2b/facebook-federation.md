---
title: Facebook を ID プロバイダーとして追加する - Azure AD
description: Facebook とフェデレーションして、外部ユーザー (ゲスト) が自分の Facebook アカウントで Azure AD アプリにサインインできるようにします。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: a92fbd254f223e2c7eb70a4e86bb7e904294395e
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2020
ms.locfileid: "83595081"
---
# <a name="add-facebook-as-an-identity-provider-for-external-identities"></a>外部 ID のために Facebook を ID プロバイダーとして追加する

Facebook をセルフサービス サインアップのユーザー フロー (プレビュー) に追加して、ユーザーが自分の Facebook アカウントを使用してアプリケーションにサインインできるようにすることができます。 ユーザーが Facebook を使用してサインインできるようにするには、まず、テナントに対して[セルフサービス サインアップ](self-service-sign-up-user-flow.md)を有効にする必要があります。 Facebook を ID プロバイダーとして追加した後、アプリケーションに対するユーザー フローを設定し、サインイン オプションの 1 つとして Facebook を選択します。

## <a name="create-an-app-in-the-facebook-developers-console"></a>Facebook 開発者コンソールでアプリを作成する

[ID プロバイダー](identity-providers.md)として Facebook アカウントを使用するには、Facebook 開発者コンソールでアプリケーションを作成する必要があります。 まだ Facebook アカウントを持っていない場合は、[https://www.facebook.com/](https://www.facebook.com) でサインアップできます。

> [!NOTE]  
> 以下のステップ 9 と 16 では、次の URL を使用します。
> - **[Site URL]\(サイトの URL\)** には、「`https://login.microsoftonline.com`」と入力します。
> - **[Valid OAuth redirect URIs]\(有効な OAuth リダイレクト URI\)** には、「`https://login.microsoftonline.com/te/<tenant-id>/oauth2/authresp`」と入力します。 自分の `<tenant-ID>` は、Azure Active Directory の [概要] ブレードで確認できます。


1. Facebook アカウントの資格情報を使用して、[開発者向けの Facebook](https://developers.facebook.com/)にサインインします。
2. まだ登録していない場合は、Facebook 開発者として登録する必要があります。 これを行うには、ページの右上隅にある **[Get Started]\(スタートガイド\)** を選択し、Facebook のポリシーに同意して登録手順を完了します。
3. **[マイ アプリ]** を選択し、 **[アプリの作成]** を選択します。
4. **[表示名]** および有効な **[連絡先の電子メール]** を入力します。
5. **[Create App ID]\(アプリ ID の作成\)** を選択します。 Facebook プラットフォームのポリシーを受け入れ、オンライン セキュリティ チェックを完了する必要があります。
6. **[設定]**  >  **[基本]** を選択します。
7. **[Category]\(カテゴリ\)** を選択します (たとえば [Business and Pages]\(ビジネスとページ\))。 この値は Facebook では必須ですが、Azure AD では使用されません。
8. ページの下部で、 **[プラットフォームの追加]** 、 **[Web サイト]** の順に選択します。
9. **[Site URL]\(サイトの URL\)** に、適切な URL を入力します (上に記載)。
10. **[Privacy Policy URL]\(プライバシー ポリシーの URL\)** に、アプリケーションのプライバシー情報が保持されているページの URL を入力します (例: `http://www.contoso.com`)。
11. **[変更の保存]** を選択します。
12. ページの上部で、 **[App ID] (アプリ ID)** の値をコピーします。
13. **[Show (表示)]** を選択し、 **[App Secret (アプリ シークレット)]** の値をコピーします。 テナントで ID プロバイダーとして Facebook を構成するには、この両方を使用します。 **[App Secret]** は、重要なセキュリティ資格情報です。
14. **[Products]\(製品\)** を選択し、 **[Facebook Login]\(Facebook ログイン\)** で **[Set up]\(セットアップ\)** を選択します。
15. **[Facebook Login]\(Facebook ログイン\)** の **[Settings]\(設定\)** を選択します。
16. **[Valid OAuth redirect URIs]\(有効な OAuth リダイレクト URI\)** に、適切な URL を入力します (上に記載)。
17. ページの下部にある **[Save Changes]\(変更の保存\)** を選択します。
18. Facebook アプリケーションを Azure AD で使用できるようにするには、ページの右上にある状態セレクターを選択し、それを **[オン]** にしてアプリケーションをパブリックにした後、 **[Switch Mode]\(モードの切り替え\)** を選択します。 この時点で、状態は**開発**から**ライブ**に変更されます。
    
## <a name="configure-a-facebook-account-as-an-identity-provider"></a>ID プロバイダーとして Facebook アカウントを構成する

1. Azure AD テナントの全体管理者として [Azure portal](https://portal.azure.com) にサインインします。
2. **[Azure サービス]** で **[Azure Active Directory]** を選択します。
3. 左側のメニューで、 **[External Identities]** を選択します。
4. **[All identity providers]\(すべての ID プロバイダー\)** を選択してから、 **[Facebook]** を選択します。
5. **[クライアント ID]** には、前に作成した Facebook アプリケーションの**アプリ ID** を入力します。
6. **[クライアント シークレット]** には、記録した**アプリ シークレット**を入力します。

   ![[ソーシャル ID プロバイダーの追加] ページを示すスクリーンショット](media/facebook-federation/add-social-identity-provider-page.png)

7. **[保存]** を選択します。

## <a name="next-steps"></a>次のステップ

- [外部ユーザーをコラボレーションに招待する](add-users-administrator.md)
- [セルフサービス サインアップをアプリに追加する](self-service-sign-up-user-flow.md)
