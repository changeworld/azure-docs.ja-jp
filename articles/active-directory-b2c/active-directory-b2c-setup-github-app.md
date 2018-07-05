---
title: Azure Active Directory B2C の GitHub ID プロバイダーの構成 | Microsoft Docs
description: Azure Active Directory B2C によってセキュリティ保護されたアプリケーションで、GitHub アカウントを使用する顧客にサインアップとサインインを提供します。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/06/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 3754a169b301bac97f3e12d10b754222e3cf325d
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2018
ms.locfileid: "37443343"
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-github-accounts"></a>Azure Active Directory B2C: GitHub アカウントでコンシューマーにサインアップおよびサインインを提供する

> [!NOTE]
> この機能はプレビュー段階にあります。
> 

この記事では、GitHub アカウントを持つユーザーのサインインを可能にする方法について説明します。

## <a name="create-a-github-oauth-application"></a>GitHub OAuth アプリケーションを作成する

Azure AD B2C で GitHub を ID プロバイダーとして使用するには、GitHub OAuth アプリを作成して適切なパラメーターを指定する必要があります。

1. GitHub にサインインした後、[GitHub 開発者設定](https://github.com/settings/developers)に移動します。
1. **[New OAuth App]\(新しい OAuth アプリ\)** をクリックします
1. **アプリケーション名**と**ホームページ URL** を入力します。
1. **[Authorization callback URL]\(認証コールバック エンドポイント URL\)** に、「`https://login.microsoftonline.com/te/{tenant}/oauth2/authresp`」と入力します。 **{tenant}** を Azure AD B2C テナントの名前 (例: contosob2c.onmicrosoft.com) に置き換えます。

    >[!NOTE]
    >**[Sign-on URL]\(サインイン URL\)** の "tenant" の値は、すべて小文字にする必要があります。

1. **[Register application (アプリケーションを登録する)]** をクリックします。
1. **[Client ID]\(クライアント ID\)** と **[Client Secret]\(クライアント シークレット\)** の値を保存します。 どちらも次のセクションで必要になります。

## <a name="configure-github-as-an-identity-provider-in-your-azure-ad-b2c-tenant"></a>Azure AD B2C テナントで GitHub を ID プロバイダーとして構成する

1. この手順に従って、Azure Portal で [B2C 機能ブレードに移動](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) します。
1. B2C 機能ブレードで、 **[ID プロバイダー]** をクリックします。
1. ブレードの上部にある **[+追加]** をクリックします。
1. ID プロバイダー構成のわかりやすい **[名前]** を指定します。 たとえば、「GitHub」と入力します。
1. **[ID プロバイダーの種類]** をクリックし、**[GitHub]** を選択して、**[OK]** をクリックします。
1. **[この ID プロバイダーを設定する]** をクリックし、前にコピーした GitHub OAuth アプリケーションのクライアント ID とクライアント シークレットを入力します。
1. **[OK]** をクリックし、**[作成]** をクリックして GitHub の構成を保存します。

## <a name="next-steps"></a>次の手順

[組み込みのポリシー](active-directory-b2c-reference-policies.md)を作成するか編集し、GitHub を ID プロバイダーとして追加します。