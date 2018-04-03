---
title: 'Azure Active Directory B2C: セルフサービスによるパスワードのリセット | Microsoft Docs'
description: Azure Active Directory B2C でコンシューマー向けにセルフサービスによるパスワードのリセットをセットアップする方法を示すトピック
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 12/06/2016
ms.author: davidmu
ms.openlocfilehash: f38473989f90bfe6d35bffb17a02a892ad08cf5e
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-set-up-self-service-password-reset-for-your-consumers"></a>Azure Active Directory B2C: コンシューマー向けにセルフサービスによるパスワードのリセットをセットアップする
セルフサービスのパスワード リセット機能を使用すると、コンシューマー (ローカル アカウントにサインアップしたユーザー) はパスワードを自分でリセットできます。 これによりサポート スタッフの負担を大幅に軽減でき、アプリケーションを定期的に使用するコンシューマーが何百万人もいるような場合に特に効果的です。 現在、回復の手段としては検証済みの電子メール アドレスのみがサポートされています。 将来的には、他の回復方法 (検証済みの電話番号、セキュリティの質問など) が追加される予定です。

> [!NOTE]
> この記事は、サインイン ポリシーのコンテキストで使用されるセルフ サービスのパスワード リセットに適用されます。 アプリケーションから呼び出される完全にカスタマイズ可能なパスワードのリセット ポリシーが必要な場合は、 [この記事](active-directory-b2c-reference-policies.md#create-a-password-reset-policy)を参照してください。
> 
> 

既定のディレクトリでは、セルフサービスによるパスワードのリセットは無効になっています。 この機能を有効にするには次のようにします。

1. サブスクリプション管理者として [Azure Portal](https://portal.azure.com/) にサインインします。 これは、ディレクトリを作成するときに使用したものと同じ職場/学校アカウント、または同じ Microsoft アカウントです。
2. (左側にあるナビゲーション バーで) Active Directory を開きます。
3. **[プロパティ]**を選択します。
4. **[セルフ サービスによるパスワードのリセットが有効]** セクションまでスクロールし、**[すべて]** に切り替えます。 
5. ページの上部にある **[保存]** をクリックします。 以上で終わりです。

テストするには、任意のサインイン ポリシー (ID プロバイダーがローカル アカウントのもの) で [今すぐ実行] 機能を使用します。 ローカル アカウントのサインイン ページ (電子メール アドレスとパスワードまたはユーザー名とパスワードを入力する場所) で、 **[アカウントにアクセスできない場合]** をクリックしてコンシューマー エクスペリエンスを確認します。

> [!NOTE]
> [セルフサービスによるパスワードのリセット] ページは、 [会社のブランド機能](../active-directory/customize-branding.md)を使用してカスタマイズできます。
> 
> 

