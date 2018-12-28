---
title: Azure Active Directory B2C を使用して GitHub アカウントでのサインアップおよびサインインを設定する | Microsoft Docs
description: Azure Active Directory B2C を使用するアプリケーションで GitHub アカウントを持つ顧客にサインアップとサインインを提供します。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: f3eb5cd62d24ea7251829aed8abba38415835023
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2018
ms.locfileid: "53162819"
---
# <a name="set-up-sign-up-and-sign-in-with-a-github-account-using-azure-active-directory-b2c"></a>Azure Active Directory B2C を使用して GitHub アカウントでのサインアップおよびサインインを設定する

> [!NOTE]
> この機能はプレビュー段階にあります。
> 

Azure Active Directory (Azure AD) B2C で ID プロバイダーとして GitHub アカウントを使用するには、それを表すアプリケーションをテナント内に作成する必要があります。 まだ GitHub アカウントを持っていない場合は、[https://www.github.com/](https://www.github.com/) で取得できます。

## <a name="create-a-github-oauth-application"></a>GitHub OAuth アプリケーションを作成する

1. GitHub 資格情報を使用して [GitHub Developer](https://github.com/settings/developers) Web サイトにサインインします。
2. **OAuth アプリ**を選択し、**新規 OAuth アプリ**を選択します。
3. **アプリケーション名**と**ホームページ URL** を入力します。
4. **[Authorization callback URL]**(承認コールバック URL) に `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` を入力します。 `your-tenant-name`を Azure AD B2C テナントの名前に置き換えます。 テナントが Azure AD B2C に大文字で定義されている場合でも、テナント名を入力するときに、すべての小文字を使用します。
5. **[Register application (アプリケーションを登録する)]** をクリックします。
6. **[クライアント ID]** と **[クライアント シークレット]** の値をコピーします。 ID プロバイダーをテナントに追加するには、両方が必要です。

## <a name="configure-a-github-account-as-an-identity-provider"></a>ID プロバイダーとして GitHub アカウントを構成する

1. Azure AD B2C テナントの全体管理者として [Azure Portal](https://portal.azure.com/) にサインインします。
2. お使いの Azure AD B2C テナントを含むディレクトリを使用していることを確認してください。確認のために、トップ メニューにある **[ディレクトリとサブスクリプション フィルター]** をクリックして、お使いのテナントを含むディレクトリを選択します。
3. Azure Portal の左上隅の **[すべてのサービス]** を選択し、**[Azure AD B2C]** を検索して選択します。
4. **[ID プロバイダー]**、**[追加]** の順に選択します。
5. **[名前]** を入力します。 たとえば、「*GitHub*」と入力します。
6. **[ID プロバイダーの種類]** を選択し、**[GitHub (Preview)]**(GitHub (プレビュー)) を選択して、**[OK]** をクリックします。
7. **[この ID プロバイダーをセットアップします]** を選択し、**[クライアント ID]** として前に記録したクライアント ID を入力し、前に作成した GitHub アカウント アプリケーションの **[クライアント シークレット]** として記録したクライアント シークレットを入力します。
8. **[OK]** をクリックし、**[作成]** をクリックして GitHub アカウントの構成を保存します。
