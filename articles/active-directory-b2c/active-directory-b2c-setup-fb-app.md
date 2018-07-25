---
title: Azure Active Directory B2C を使用して Facebook アカウントでのサインアップおよびサインインを設定する | Microsoft Docs
description: Azure Active Directory B2C を使用するアプリケーションで Facebook アカウントを持つ顧客にサインアップとサインインを提供します。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/06/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: de059e3875b5f15526cb176d43a019fd2d9ee9b9
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2018
ms.locfileid: "37901383"
---
# <a name="set-up-sign-up-and-sign-in-with-a-facebook-account-using-azure-active-directory-b2c"></a>Azure Active Directory B2C を使用して Facebook アカウントでのサインアップおよびサインインを設定する

## <a name="create-a-facebook-application"></a>Facebook アプリケーションを作成する

Azure Active Directory (Azure AD) B2C で ID プロバイダーとして Facebook アカウントを使用するには、テナントにそれを表すアプリケーションを作成する必要があります。 まだ Facebook アカウントを持っていない場合は、[https://www.facebook.com/](https://www.facebook.com/) で取得できます。

1. Facebook アカウントの資格情報を使用して、[開発者向けの Facebook](https://developers.facebook.com/)にサインインします。
2. まだ登録していない場合は、Facebook 開発者として登録する必要があります。 これを行うには、ページの右上隅にある **[登録]** を選択し、Facebook のポリシーに同意して登録手順を完了します。
3. **[マイ アプリ]** を選択し、**[Add New App] (新しいアプリの追加)** をクリックします。 
4. **[表示名]** および有効な **[連絡先の電子メール]** を入力します。
5. **[Create App ID] \(アプリ ID の作成)** をクリックします。 Facebook プラットフォームのポリシーを受け入れ、オンライン セキュリティ チェックを完了する必要があります。
6. **[設定]** > **[基本]** を選択します。
7. ページの下部で、**[プラットフォームの追加]**、**[Web サイト]** の順に選択します。
8. **[サイトの URL]** に「`https://login.microsoftonline.com/`」と入力します。 **[Privacy Policy URL] (プライバシー ポリシーの URL)** に URL (`http://www.contoso.com` など) を入力します。
9. **[変更の保存]** を選択します。
11. ページの上部で、**[App ID] (アプリ ID)** の値をコピーします。 
12. **[Show (表示)]** をクリックし、**[App Secret (アプリ シークレット)]** の値をコピーします。 テナントで ID プロバイダーとして Facebook を構成するには、この両方を使用します。 **[App Secret]** は、重要なセキュリティ資格情報です。
13. **[Products] (製品)** を選択し、**[Facebook Login] (Facebook ログイン)** で **[セットアップ]** を選択します。
14. **[Facebook Login] (Facebook ログイン)** の **[設定]** を選択します。
15. **[Valid OAuth redirect URIs] (有効な OAuth リダイレクト URI)** に「`https://login.microsoftonline.com/te/{tenant}/oauth2/authresp`」と入力します。 **{tenant}** は、実際のテナントの名前 (例: contosob2c.onmicrosoft.com) に置き換えます。 ページの下部にある **[Save Changes]** をクリックします。
16. Facebook アプリケーションを Azure AD B2C で使用できるようにするには、**[App Review] (アプリのレビュー)** を選択し、**[Make My Application public?] (アプリケーションを公開する)** を **[はい]** に設定し、カテゴリ (`Business and Pages` など) を選択して **[確認]** をクリックします。

## <a name="configure-a-facebook-account-as-an-identity-provider"></a>ID プロバイダーとして Facebook アカウントを構成する

1. Azure AD B2C テナントの全体管理者として [Azure Portal](https://portal.azure.com/) にサインインします。
2. Azure Portal の右上隅でディレクトリを切り替えて、Azure AD B2C テナントが含まれるディレクトリを使用していることを確認してください。 サブスクリプション情報を選択し、**[ディレクトリの切り替え]** を選択します。 

    ![Azure AD B2C テナントに切り替え](./media/active-directory-b2c-setup-fb-app/switch-directories.png)

    テナントが含まれるディレクトリを選択します。

    ![新しいディレクトリを選択する](./media/active-directory-b2c-setup-fb-app/select-directory.png)

3. Azure Portal の左上隅の **[すべてのサービス]** を選択し、**[Azure AD B2C]** を検索して選択します。
4. **[ID プロバイダー]**、**[追加]** の順に選択します。
5. **[名前]** を入力します。 たとえば、「*Facebook*」と入力します。
6. **[ID プロバイダーの種類]**、**[Facebook]** の順に選択し、**[OK]** をクリックします。
7. **[この ID プロバイダーをセットアップします]** を選択し、**[クライアント ID]** として前に記録したアプリ ID を入力し、前に作成した Facebook アプリケーションの **[クライアント シークレット]** として記録したアプリ シークレットを入力します。
8. **[OK]** をクリックし、**[作成]** をクリックして Facebook の構成を保存します。