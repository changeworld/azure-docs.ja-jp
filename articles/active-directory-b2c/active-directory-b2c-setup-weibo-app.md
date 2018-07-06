---
title: Azure Active Directory B2C の Weibo の構成 | Microsoft Docs
description: Azure Active Directory B2C によってセキュリティ保護されたアプリケーションで、Weibo アカウントを使用するコンシューマーにサインアップとサインインを提供します。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 3/26/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: bfd7dde290bd040f8457e6d095fdf896e802764b
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2018
ms.locfileid: "37444791"
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-weibo-accounts"></a>Azure Active Directory B2C: Weibo アカウントを使用するコンシューマーにサインアップとサインインを提供する

> [!NOTE]
> この機能はプレビュー段階にあります。 運用環境で、この ID プロバイダーを使用しないでください。
> 

## <a name="create-a-weibo-application"></a>Weibo アプリケーションを作成する

Azure Active Directory (Azure AD) B2C で ID プロバイダーとして Weibo を使用するには、Weibo アプリケーションを作成し、適切なパラメーターを指定する必要があります。 そのためには Weibo アカウントが必要です。 アカウントがない場合は、[http://weibo.com/signup/signup.php?lang=en-us](http://weibo.com/signup/signup.php?lang=en-us) で取得できます。

### <a name="register-for-the-weibo-developer-program"></a>Weibo 開発者プログラムに登録する

1. [Weibo 開発者ポータル](http://open.weibo.com/)に移動し、Weibo アカウントの資格情報を使用してサインインします。
2. サインインしたら、右上隅にある表示名をクリックします。
3. ドロップダウンで **[编辑开发者信息 (開発者情報の編集)]** を選択します。
4. フォームに必要な情報を入力し、**[提交 (送信)]** をクリックします。
5. 電子メールによる確認プロセスを完了します。
6. [ID の確認ページ](http://open.weibo.com/developers/identity/edit)に移動します。
7. フォームに必要な情報を入力し、**[提交 (送信)]** をクリックします。

### <a name="register-a-weibo-application"></a>Weibo アプリケーションを登録する

1. [新しい Weibo アプリの登録ページ](http://open.weibo.com/apps/new)に移動します。
2. 必要なアプリケーション情報を入力します。
3. **[创建 (作成)]** をクリックします。
4. **アプリ キー**と**アプリケーション シークレット**の値をコピーします。 この情報は後で必要になります。
5. 必要な写真をアップロードし、必要な情報を入力します。
6. **[保存以上信息 (保存)]** をクリックします。
7. **[高级信息 (詳細情報)]** をクリックします。
8. OAuth2.0 の **[授权设置 (リダイレクト URL)]** フィールドの横の **[编辑 (編集)]** をクリックします。
9. OAuth2.0 の **[授权设置 (リダイレクト URL)]** に、「`https://login.microsoftonline.com/te/{tenant_name}/oauth2/authresp`」と入力します。 たとえば、`tenant_name` が contoso.onmicrosoft.com の場合、URL を `https://login.microsoftonline.com/te/contoso.onmicrosoft.com/oauth2/authresp` に設定します。
10. **[提交 (送信)]** をクリックします。  

## <a name="configure-weibo-as-an-identity-provider-in-your-tenant"></a>テナントで ID プロバイダーとして Weibo を構成する
1. この手順に従って、Azure Portal で [B2C 機能ブレードに移動](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) します。
2. B2C 機能ブレードで、 **[ID プロバイダー]** をクリックします。
3. ブレードの上部にある **[+追加]** をクリックします。
4. ID プロバイダー構成のわかりやすい **[名前]** を指定します。 たとえば、「Weibo」と入力します。
5. **[ID プロバイダーの種類]** をクリックし、**[Weibo]** を選択して、**[OK]** をクリックします。
6. **[この ID プロバイダーをセットアップします]** をクリックします。
7. **クライアント ID** として、先ほどコピーした**アプリ キー**を入力します。
8. **クライアント シークレット**として、先ほどコピーした**アプリケーション シークレット**を入力します。
9. **[OK]** をクリックし、**[作成]** をクリックして Weibo の構成を保存します。

