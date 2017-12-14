---
title: "Azure AD のセルフサービスによるパスワードのリセットの概要 | Microsoft Docs"
description: "Azure AD のセルフサービスによるパスワードのリセットが組織に提供するものは何か。"
services: active-directory
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
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
ms.openlocfilehash: 433ee5b6a1d6897dd911dcfc5e95c7dd1d977cd5
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/11/2017
---
# <a name="azure-ad-self-service-password-reset-for-the-it-professional"></a>IT プロフェッショナルにとっての Azure AD のセルフサービスによるパスワードのリセット

Azure Active Directory (Azure AD) のセルフサービスによるパスワードのリセット (SSPR) を使用すると、ユーザーが必要なときに必要な場所で自分でパスワードをリセットできます。 同時に、管理者は、ユーザーのパスワードがどのようにリセットされるかを制御することができます。 ユーザーがパスワードのリセットだけのためにヘルプ デスクに連絡する必要がなくなりました。 Azure AD SSPR には以下が含まれます。

* **セルフ サービスによるパスワード変更** - ユーザーは自身のパスワードを知っているが、新しいパスワードに変更することを希望している。
* **セルフ サービスによるパスワードのリセット** - サインインできないユーザーが、次の検証済み認証方法を 1 つ以上使って、パスワードをリセットすることを希望している。
   * 検証済み携帯電話にテキスト メッセージを送信する。
   * 検証済みの携帯電話または職場電話に電話する。
   * 検証済みセカンダリ電子メール アカウントに電子メールを送信する。
   * セキュリティの質問に回答する。
* **セルフ サービスによるアカウントのロック解除**: ユーザーが自分のパスワードでサインインできず、ロックアウトされている。ユーザーが管理者の介入なしで、自分の認証方法を使用したアカウントのロック解除を希望している。

## <a name="why-choose-azure-ad-sspr"></a>Azure AD SSPR を選ぶ理由

Azure AD SSPR は、以下を行うのに役立ちます。

* **コストの削減**: ヘルプデスクの支援によるパスワードのリセットは、通常、IT 組織のサポート コール 20% を占めています。 
* **エンド ユーザー エクスペリエンスの向上**と**ヘルプ デスクの量の削減**: エンドユーザーに、自分自身のパスワードに関する問題を解決する権限を与えることで実現します。 ヘルプ デスクを呼び出したり、サポート リクエストを送ったりする必要はありません。
* **モビリティの推進**: ユーザーはどこからでも自分のパスワードをリセットできます。
* **制御を維持**: セキュリティ ポリシーの制御は維持されます。 管理者は現行のポリシーを引き続き強制できます。

準備ができたら、[クイック スタート ガイド](active-directory-passwords-getting-started.md)を使用して、Azure AD SSPR の使用を開始できます。 ユーザーに、自分のパスワードをリセットする機能を迅速に付与できます。

## <a name="azure-ad-sspr-availability"></a>Azure AD SSPR の可用性

Azure AD SSPR は、サブスクリプションに応じて次の 3 つのレベルで使用できます。

* **Azure AD Free**: クラウドのみの管理者が、自分のパスワードをリセットできます。
* **Azure AD Basic** または **Office 365 のすべての有料サブスクリプション**: クラウドのみのユーザーが自分のパスワードをリセットできます。
* **Azure AD Premium**: クラウドのみ、フェデレーション、パスワード同期ユーザーを含むすべてのユーザーまたは管理者が、自分のパスワードをリセットできます。 オンプレミスのパスワードでは、パスワードの書き戻しが有効になっている必要があります。

## <a name="azure-ad-pricing-sla-updates-and-roadmap"></a>Azure AD の価格、SLA、更新、およびロードマップ

ライセンス、価格、および今後のプランの詳細については、次のサイトをご覧ください。

* [Azure AD の価格詳細](https://azure.microsoft.com/pricing/details/active-directory/)
* [Office 365 の価格](https://products.office.com/compare-all-microsoft-office-products?tab=2)
* [Azure サービス レベル アグリーメント](https://azure.microsoft.com/support/legal/sla/)
* [Microsoft Online Services のサービス レベル アグリーメント](http://go.microsoft.com/fwlink/?LinkID=272026&clcid=0x409)
* [Azure の更新情報](https://azure.microsoft.com/updates/)
* [Azure のロードマップ](https://www.microsoft.com/cloud-platform/roadmap-recently-available)

## <a name="next-steps"></a>次のステップ

* SSPR を開始できるように [Azure AD のセルフ サービスによるパスワードのリセットをセットアップ](active-directory-passwords-getting-started.md)。
* [ロールアウト ガイド](active-directory-passwords-best-practices.md)のガイダンスを使用して、ユーザーに SSPR を適切にデプロイできるよう計画。
* [パスワードのリセットと変更。](active-directory-passwords-update-your-own-password.md)
* [セルフサービスによるパスワード リセットの登録。](active-directory-passwords-reset-register.md)
