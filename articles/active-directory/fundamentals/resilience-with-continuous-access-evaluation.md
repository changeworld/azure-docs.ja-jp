---
title: Azure Active Directory で継続的アクセス評価を使用して回復性を強化する
description: アーキテクトと IT 管理者を対象とした CAE の使用に関するガイド
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2d792c1eb0a85f2a898d1dc5c63047a4553f13b0
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/01/2021
ms.locfileid: "106106774"
---
# <a name="build-resilience-by-using-continuous-access-evaluation"></a>継続的アクセス評価を使用して回復性を強化する

[継続的アクセス評価](../conditional-access/concept-continuous-access-evaluation.md) (CAE) を使用すると、Azure AD アプリケーションで重要なイベントをサブスクライブし、それらのイベントを評価して適用することができます。 評価の対象となるイベントには次のものが含まれます。

* ユーザー アカウントが削除または無効化された

* ユーザーのパスワードが変更された

* ユーザーに対して MFA が有効化された

* 管理者がトークンを明示的に取り消した

* 管理者特権ユーザーのリスクが検出された

これにより、アプリケーションは Azure AD から通知されたイベントに基づいて、期限が切れていないトークンを拒否できます (次の図を参照)。CAE の概念図

![CAE の概念図](./media/resilience-with-cae/admin-resilience-continuous-access-evaluation.png)

## <a name="how-does-cae-help"></a>CAE がもたらすメリット

このメカニズムにより、Azure AD では有効期間の長いトークンを発行できるようになるほか、必要な場合にのみ、アプリケーションによってアクセスを取り消して再認証を強制できるようになります。 このパターンにより、最終的に、トークンを取得する呼び出しの数が減るため、エンド ツー エンドのフローの回復性が向上します。 

CAE を使用するには、サービスとクライアントの両方が CAE に対応している必要があります。 Exchange Online、Teams、SharePoint Online などの Microsoft 365 サービスでは、CAE がサポートされています。 クライアント側では、これらの Office 365 サービス (Outlook Web App など) を使用するブラウザー ベースのエクスペリエンスと、特定バージョンの Office 365 ネイティブ クライアントが CAE に対応しています。 CAE 対応になる Microsoft クラウド サービスは、今後増える予定です。

Microsoft は業界と連携して、サードパーティのアプリケーションがこの機能を使用できるようにするための[標準](https://openid.net/wg/sse/)を作成しています。 CAE 対応のアプリケーションを開発することも可能です。 詳細については、アプリケーションで回復性を強化する方法に関するページを参照してください。

## <a name="how-do-i-implement-cae"></a>CAE を実装する方法

* [CAE 対応 API を使用するようにコードを更新します](../develop/app-resilience-continuous-access-evaluation.md)。

* Azure AD セキュリティ構成で [CAE を有効化](../conditional-access/concept-continuous-access-evaluation.md)します。

* 組織で使用されている Microsoft Office ネイティブ アプリケーションが[互換性のあるバージョン](../conditional-access/concept-continuous-access-evaluation.md)であることを確認します。

* [再認証プロンプトを最適化します](../authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime.md)。

 
## <a name="next-steps"></a>次のステップ
管理者とアーキテクト向けの回復性に関するリソース
 
* [資格情報管理を使用して回復性を強化する](resilience-in-credentials.md)

* [デバイスの状態を使用して回復性を強化する](resilience-with-device-states.md)

* [外部ユーザー認証の回復性を強化する](resilience-b2b-authentication.md)

* [ハイブリッド認証で回復性を強化する](resilience-in-hybrid.md)

* [アプリケーション プロキシを使用したアプリケーション アクセスで回復性を強化する](resilience-on-premises-access.md)

開発者向けの回復性に関するリソース

* [アプリケーションで IAM の回復性を強化する](resilience-app-development-overview.md)

* [CIAM システムで回復性を強化する](resilience-b2c.md)
