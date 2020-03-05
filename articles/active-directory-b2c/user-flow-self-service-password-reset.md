---
title: Azure Active Directory B2C でのセルフサービス パスワード リセット | Microsoft Docs
description: Azure Active Directory B2C で顧客向けにセルフサービス パスワード リセットをセットアップする方法を示します
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d6dad52c8a3e63c64bb8e0e0030e8c50b5bab42c
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/29/2020
ms.locfileid: "78183110"
---
# <a name="set-up-self-service-password-reset-for-your-customers"></a>顧客向けにセルフサービス パスワード リセットをセットアップする

セルフサービス パスワード リセット機能を使用すると、ローカル アカウントにサインアップした顧客はパスワードを自分でリセットできます。 これによりサポート スタッフの負担を大幅に軽減でき、アプリケーションを定期的に使用する顧客が何百万人もいるような場合に特に効果的です。 現在、回復の手段としては、検証済みの電子メール アドレスの使用のみがサポートされています。

> [!NOTE]
> この記事は、ID プロバイダーとして**ローカル アカウント サインイン**を使用する、V1 **サインイン** ユーザー フローのコンテキスト内で使用されるセルフサービス パスワード リセットに適用されます。 ご利用のアプリから呼び出される完全にカスタマイズ可能なパスワードのリセット ユーザー フローが必要な場合は、[こちらの記事](user-flow-overview.md)を参照してください。
>
>

既定のディレクトリでは、セルフサービス パスワード リセット は無効になっています。 この機能を有効にするには次のようにします。

1. サブスクリプション管理者として [Azure Portal](https://portal.azure.com/) にサインインします。 これは、ディレクトリを作成するときに使用したものと同じ職場/学校アカウント、または同じ Microsoft アカウントです。
2. (左側にあるナビゲーション バーで) **Azure Active Directory** を開きます。
3. オプション ブレードで下にスクロールし、 **[パスワードのリセット]** を選択します。
4. **[セルフ サービスによるパスワードのリセットが有効]** を **[すべて]** に設定します。
5. ページの上部にある **[保存]** をクリックします。 以上で終わりです。

テストするには、任意のサインイン ユーザー フロー (ID プロバイダーがローカル アカウントのもの) で [今すぐ実行] 機能を使用します。 ローカル アカウントのサインイン ページ (電子メール アドレスとパスワード、またはユーザー名とパスワードを入力する場所) で、 **[アカウントにアクセスできない場合]** をクリックしてカスタマー エクスペリエンスを確認します。

> [!NOTE]
> [セルフサービス パスワード リセット ] ページは、 [会社のブランド機能](../active-directory/fundamentals/customize-branding.md)を使用してカスタマイズできます。
>
>

