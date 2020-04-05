---
title: 顧客のサインアップ時のメール検証を無効にする
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
ms.openlocfilehash: 369b4e13baa344a71a51b358ef810d1a66b4b6ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "79126716"
---
# <a name="disable-email-verification-during-customer-sign-up-in-azure-active-directory-b2c"></a>Azure Active Directory B2C で顧客のサインアップ時のメール検証を無効にする

[!INCLUDE [disable email verification intro](../../includes/active-directory-b2c-disable-email-verification.md)]

次の手順に従って、メールの検証を無効にします。

1. [Azure ポータル](https://portal.azure.com)
1. 上部のメニューにある **[ディレクトリとサブスクリプション]** フィルターを使用して、お使いの Azure AD B2C テナントを含むディレクトリを選択します。
1. 左側のメニューで、 **[Azure AD B2C]** を選択します。 または、 **[すべてのサービス]** を選択し、 **[Azure AD B2C]** を検索して選択します。
1. **[ユーザー フロー]** を選択します。
1. メールの検証を無効にするユーザー フローを選択します。 たとえば、*B2C_1_signinsignup* です。
1. **[Page layouts]\(ページ レイアウト\)** を選択します。
1. **[ローカル アカウント サインアップ ページ]** を選択します。
1. **[ユーザー属性]** で **[メール アドレス]** を選択します。
1. **[確認が必要]** ドロップダウンで **[いいえ]** を選択します。
1. **[保存]** を選択します。 このユーザー フローでは、メールの検証が無効になりました。

## <a name="next-steps"></a>次のステップ

- [Azure Active Directory B2C 内のユーザー インターフェイスをカスタマイズする](customize-ui-overview.md)方法を学習する

