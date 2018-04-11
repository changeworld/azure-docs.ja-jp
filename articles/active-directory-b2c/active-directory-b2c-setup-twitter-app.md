---
title: 'Azure Active Directory B2C: Twitter の構成 | Microsoft Docs'
description: Azure Active Directory B2C によってセキュリティ保護されたアプリケーションで、Twitter アカウントを使用するコンシューマーにサインアップとサインインを提供します。
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 4/06/2017
ms.author: davidmu
ms.openlocfilehash: ee2d82f8c90b88a898428973a1febaa21034a14f
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-twitter-accounts"></a>Azure Active Directory B2C: Twitter アカウントを使用するコンシューマーにサインアップとサインインを提供する

## <a name="create-a-twitter-application"></a>Twitter アプリケーションを作成する
Azure Active Directory (Azure AD) B2C で ID プロバイダーとして Twitter を使用するには、Twitter アプリケーションを作成し、適切なパラメーターを指定する必要があります。 そのためには、Twitter の開発者アカウントが必要です。 アカウントがない場合は、[https://dev.twitter.com/](https://dev.twitter.com/) で取得できます。

1. [Twitter 開発者の Web サイト](https://dev.twitter.com/)に移動し、資格情報を使用してサインインします。
2. **[Tools & Support]** の **[My apps]** をクリックし、**[Create New App]** をクリックします。 
3. フォームの **[Name]****[Description]****[Website]** に値を入力します。
4. **[Callback URL]** に「`https://login.microsoftonline.com/te/{tenant}/oauth2/authresp`」と入力します。 **{tenant}** は、実際のテナントの名前 (例: contosob2c.onmicrosoft.com) に置き換える必要があります。
5. **開発者契約**に同意するボックスをオンにし、**[Create your Twitter application]** をクリックします。
6. アプリが作成されたら、**[Keys and Access Tokens]** をクリックします。
7. **[Consumer Key]** と **[Consumer Secret]** の値をコピーします。 テナントで ID プロバイダーとして Twitter を構成するには、この両方の値が必要です。

## <a name="configure-twitter-as-an-identity-provider-in-your-tenant"></a>テナントで ID プロバイダーとして Twitter を構成する
1. この手順に従って、Azure Portal で [B2C 機能ブレードに移動](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) します。
2. B2C 機能ブレードで、 **[ID プロバイダー]**をクリックします。
3. ブレードの上部にある **[+追加]** をクリックします。
4. ID プロバイダー構成のわかりやすい **[名前]** を指定します。 たとえば、「Twitter」と入力します。
5. **[ID プロバイダーの種類]** をクリックし、**[Twitter]** を選択して、**[OK]** をクリックします。
6. **[この ID プロバイダーをセットアップします]** をクリックします。**[クライアント ID]** に **Twitter のコンシューマー キー**を入力し、**[クライアント シークレット]** に **Twitter のコンシューマー シークレット**を入力します。
7. **[OK]** をクリックし、**[作成]** をクリックして Twitter の構成を保存します。

## <a name="next-steps"></a>次の手順

[組み込みのポリシー](active-directory-b2c-reference-policies.md)を作成するか編集し、ID プロバイダーとして Twitter を追加します。