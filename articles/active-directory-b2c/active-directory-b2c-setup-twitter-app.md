---
title: Azure Active Directory B2C を使用して Twitter アカウントでのサインアップおよびサインインを設定する | Microsoft Docs
description: Azure Active Directory B2C を使用するアプリケーションで Twitter アカウントを持つ顧客にサインアップとサインインを提供します。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/09/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: bf5ae39d83fd021775fbd18cf23d2e6b9078e748
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2018
ms.locfileid: "37927898"
---
# <a name="set-up-sign-up-and-sign-in-with-a-twitter-account-using-azure-active-directory-b2c"></a>Azure Active Directory B2C を使用して Twitter アカウントでのサインアップおよびサインインを設定する

## <a name="create-a-twitter-application"></a>Twitter アプリケーションを作成する

Azure Active Directory (Azure AD) B2C で ID プロバイダーとして Twitter アカウントを使用するには、テナントにそれを表すアプリケーションを作成する必要があります。 まだ Twitter アカウントを持っていない場合は、[https://twitter.com/signup](https://twitter.com/signup) で取得できます。

1. Twitter の資格情報を使用して [Twitter アプリ](https://apps.twitter.com/)にサインインします。
2. **[Create New App]** を選択します。
3. **[名前]**、**[説明]**、および **[Web サイト]** を入力します。
4. **[Callback URL] (コールバック URL)** に「`https://login.microsoftonline.com/te/{tenant}/{policyId}/oauth1/authresp`」と入力します。 **{tenant}** をテナントの名前 (contosob2c.onmicrosoft.com など) に、**{policyId}** をポリシー ID (b2c_1_policy など) に置き換えます。 Twitter アカウントを使用するすべてのポリシーのコールバック URL を追加する必要があります。 アプリケーションで使用する場合は、` login.microsoftonline.com` の代わりに `b2clogin.com` を使用してください。
5. **[Developer Agreement] (開発者契約)** に同意し、**[Create your Twitter application] (Twitter アプリケーションを作成する)** を選択します。
7. **[Keys and Access Tokens]** タブをクリックします。
8. **[Consumer Key]** と **[Consumer Secret]** の値をコピーします。 テナントで ID プロバイダーとして Twitter アカウントを構成するには、この両方が必要です。

## <a name="configure-twitter-as-an-identity-provider-in-your-tenant"></a>テナントで ID プロバイダーとして Twitter を構成する

1. Azure AD B2C テナントの全体管理者として [Azure Portal](https://portal.azure.com/) にサインインします。
2. Azure Portal の右上隅でディレクトリを切り替えて、Azure AD B2C テナントが含まれるディレクトリを使用していることを確認してください。 サブスクリプション情報を選択し、**[ディレクトリの切り替え]** を選択します。 

    ![Azure AD B2C テナントに切り替え](./media/active-directory-b2c-setup-twitter-app/switch-directories.png)

    テナントが含まれるディレクトリを選択します。

    ![新しいディレクトリを選択する](./media/active-directory-b2c-setup-twitter-app/select-directory.png)

3. Azure Portal の左上隅の **[すべてのサービス]** を選択し、**[Azure AD B2C]** を検索して選択します。
4. **[ID プロバイダー]**、**[追加]** の順に選択します。
5. **[名前]** を入力します。 たとえば、「*Twitter*」と入力します。
6. **[ID プロバイダーの種類]**、**[Twitter]** の順に選択し、**[OK]** をクリックします。
7. **[この ID プロバイダーをセットアップします]** を選択し、**[クライアント ID]** としてコンシューマー キーを入力し、**[クライアント シークレット]** として**コンシューマー シークレット**を入力します。
8. **[OK]** をクリックし、**[作成]** をクリックして Twitter の構成を保存します。