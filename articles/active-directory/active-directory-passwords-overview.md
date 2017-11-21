---
title: "Azure AD のセルフサービスによるパスワードのリセットの概要 | Microsoft Docs"
description: "Azure AD のセルフサービスによるパスワードのリセットが組織に提供するものは何か。"
services: active-directory
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: f577ee95c34f9bc3065c026bc943d30622b9d658
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/31/2017
---
# <a name="azure-ad-self-service-password-reset-for-the-it-professional"></a>IT プロフェッショナルにとっての Azure AD のセルフサービスによるパスワードのリセット

Azure Active Directory (Azure AD) のセルフ サービスによるパスワードのリセット (SSPR) を使用すると、ユーザーが、必要なときに必要な場所で自分でパスワードをリセットできます。一方、管理者は、ユーザーがどのようにパスワードをリセットするかを制御することができます。 ユーザーがパスワードのリセットだけのために、ヘルプ デスクに連絡する必要がなくなりました。

* **セルフ サービスによるパスワード変更** - ユーザーは自身のパスワードを知っていますが、新しいパスワードに変更することを希望しています。
* **セルフ サービスによるパスワードのリセット** - サインインできないユーザーが、次の検証済み認証方法を使って、パスワードをリセットすることを希望しています。
   * 検証済み携帯電話にテキスト メッセージを送信する
   * 検証済み携帯電話または職場の電話に電話する
   * 検証済みセカンダリ メール アカウントに電子メールを送信する
   * セキュリティの質問に回答する
* **セルフ サービスによるアカウントのロック解除** - パスワードがロックされているためサインインできないユーザーが、自分の認証方法を使用して、管理者による介入なしで、アカウントのロックを解除することを希望しています。

## <a name="why-choose-azure-ad-self-service-password-reset"></a>Azure AD のセルフ サービスによるパスワードのリセットを選択する理由

* **コストの削減**: ヘルプデスクの支援によるパスワードのリセットは、通常、IT 組織のサポート コール 20% を占めています。 
* **エンド ユーザーのエクスペリエンスの向上**と**ヘルプデスクの仕事量の削減**: 自分のパスワードに関する問題をすぐに解決できる能力をエンド ユーザーに与えることで、ヘルプデスクへの連絡やサポートの要求数が減少します。
* **モビリティの推進**: ユーザーはどこからでも自分のパスワードをリセットできます。
* **制御を維持**: セキュリティ ポリシーの制御は維持されます。 管理者は現行のポリシーを引き続き強制できます。

準備ができたら、[クイック スタート ガイダンス](active-directory-passwords-getting-started.md)から Azure AD SSPR の使用を開始して、すぐにユーザーが自分のパスワードをリセットできるように設定できます。

## <a name="azure-ad-self-service-password-reset-availability"></a>Azure AD のセルフ サービスによるパスワードのリセットの使用可能性

Azure AD のセルフサービスによるパスワードのリセットは、サブスクリプションに応じて次の 3 つのレベルで使用できます。

* **Azure AD Free** - クラウドのみの管理者が、自分のパスワードをリセットできます。
* **Azure AD Basic** または **Office 365 のすべての有料サブスクリプション** - クラウドのみのユーザーが自分のパスワードをリセットできます。
* **Azure AD Premium** - クラウドのみ、フェデレーション、パスワード同期ユーザーを含むすべてのユーザーまたは管理者が、自分のパスワードをリセットできます。 オンプレミスのパスワードでは、パスワードの書き戻しが有効になっている必要があります。

## <a name="azure-ad-pricing-sla-updates-and-roadmap"></a>Azure AD の価格、SLA、更新、およびロードマップ

ライセンス、価格、および今後のプランの詳細については、次のサイトをご覧ください。

* [**Azure Active Directory の価格**](https://azure.microsoft.com/pricing/details/active-directory/)
* [**Office 365 の価格**](https://products.office.com/compare-all-microsoft-office-products?tab=2)
* [**Azure サービス レベル アグリーメント**](https://azure.microsoft.com/support/legal/sla/)
* [**Microsoft Online Services サービス レベル アグリーメント**](http://go.microsoft.com/fwlink/?LinkID=272026&clcid=0x409)
* [**Azure の更新**](https://azure.microsoft.com/updates/)
* [**Azure のロードマップ**](https://www.microsoft.com/cloud-platform/roadmap-recently-available)

## <a name="next-steps"></a>次のステップ

* SSPR を開始できるように [Azure AD のセルフサービスによるパスワードのリセットを設定。](active-directory-passwords-getting-started.md)
* [**ロールアウト ガイド**](active-directory-passwords-best-practices.md)のガイダンスを使用して、ユーザーに SSPR を適切にデプロイできるよう計画。
* [パスワードのリセットと変更。](active-directory-passwords-update-your-own-password.md)
* [セルフサービスによるパスワード リセットの登録。](active-directory-passwords-reset-register.md)