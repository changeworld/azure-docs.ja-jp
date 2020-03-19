---
title: カスタム ポリシーを使用して顧客のサインアップ時のメール検証を無効にする
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C で顧客のサインアップ時のメール検証を無効にする方法について説明します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 8ec60f694000985f51db25db621e5814df62cdb3
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/11/2020
ms.locfileid: "79126804"
---
# <a name="disable-email-verification-during-customer-sign-up-using-a-custom-policy-in-azure-active-directory-b2c"></a>Azure Active Directory B2C でカスタム ポリシーを使用して顧客のサインアップ時のメール検証を無効にする

[!INCLUDE [disable email verification intro](../../includes/active-directory-b2c-disable-email-verification.md)]

## <a name="prerequisites"></a>前提条件

[カスタム ポリシーの概要](custom-policy-get-started.md)に関するページの手順を完了します。 ソーシャルとローカルのアカウントを使用したサインアップとサインイン用の実際に機能するカスタム ポリシーが必要です。

## <a name="add-the-metadata-to-the-self-asserted-technical-profile"></a>メタデータをセルフアサート技術プロファイルに追加する

**LocalAccountSignUpWithLogonEmail** 技術プロファイルは[セルフアサート](self-asserted-technical-profile.md)であり、サインアップ フローの間に呼び出されます。 メールの検証を無効にするには、`EnforceEmailVerification` メタデータを false に設定します。 拡張ファイルで LocalAccountSignUpWithLogonEmail 技術プロファイルをオーバーライドします。 `ClaimsProviders` 要素を見つけます。 次のクレーム プロバイダーを `ClaimsProviders` 要素に追加します。


```XML
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
      <Metadata>
        <Item Key="EnforceEmailVerification">false</Item>
      </Metadata>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="test-the-custom-policy"></a>カスタム ポリシーをテストする

1. [Azure portal](https://portal.azure.com) にサインインします。
2. ご利用の Azure AD テナントを含むディレクトリを使用していることを確認してください。そのためには、トップ メニューにある **[ディレクトリ + サブスクリプション]** フィルターをクリックして、ご利用の Azure AD テナントを含むディレクトリを選択します。
3. Azure portal の左上隅にある **[すべてのサービス]** を選択し、 **[アプリの登録]** を検索して選択します。
4. **[Identity Experience Framework]** を選択します。
5. **[カスタム ポリシーのアップロード]** を選択し、変更した 2 つのポリシー ファイルをアップロードします。
2. アップロードしたサインアップまたはサインイン ポリシーを選択し、 **[今すぐ実行]** ボタンをクリックします。
3. 検証なしに、メール アドレスを使用してサインアップできるはずです。


## <a name="next-steps"></a>次のステップ

- IEF のリファレンスで[セルフアサート技術プロファイル](self-asserted-technical-profile.md)についてさらに学習します。
