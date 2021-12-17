---
title: 一般的な条件付きアクセス ポリシー - Azure Active Directory
description: 組織で一般的に使用される条件付きアクセス ポリシー
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 11/05/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: calebb, davidspo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 35e0fdbc31b6bb9719f2b5bfcac8c80e1d3fce64
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132709923"
---
# <a name="common-conditional-access-policies"></a>一般的な条件付きアクセス ポリシー

[セキュリティの既定値群](../fundamentals/concept-fundamentals-security-defaults.md)は一部の人々には適していますが、多くの組織では、それによって提供されるものよりも高い柔軟性が必要です。 多くの組織では、緊急アクセスや緊急用管理アカウントなどの特定のアカウントを条件付きアクセス ポリシーから除外する必要があります。 この記事で参照されているポリシーは、組織のニーズに基づいてカスタマイズできます。 組織は、[条件付きアクセスのレポート専用モードを使用して、新しいポリシー決定の結果を判断します。](concept-conditional-access-report-only.md)

## <a name="conditional-access-templates-preview"></a>条件付きアクセス テンプレート (プレビュー)

条件付きアクセス テンプレートは、Microsoft の推奨事項に合わせて新しいポリシーをデプロイするための便利な方法を提供するように設計されています。 これらのテンプレートは、さまざまな顧客の種類および場所で一般的に使用されるポリシーに合わせて、最大限の保護を提供するように設計されています。

:::image type="content" source="media/concept-conditional-access-policy-common/conditional-access-policies-azure-ad-listing.png" alt-text="Azure portal における条件付きアクセス ポリシーとテンプレート。" lightbox="media/concept-conditional-access-policy-common/conditional-access-policies-azure-ad-listing.png":::

14 個のポリシー テンプレートは、ユーザー ID またはデバイスに割り当てられるポリシーに分割されます。 **[Azure portal]**  >  **[Azure Active Directory]**  >  **[セキュリティ]**  >  **[条件付きアクセス]**  >  **[テンプレートから新しいポリシーを作成]** で、テンプレートを見つけます。

:::image type="content" source="media/concept-conditional-access-policy-common/create-policy-from-template-identity.png" alt-text="Azure portal で事前構成済みのテンプレートから条件付きアクセス ポリシーを作成します。" lightbox="media/concept-conditional-access-policy-common/create-policy-from-template-identity.png":::

> [!IMPORTANT]
> 条件付きアクセス テンプレート ポリシーは、ポリシーを作成しているユーザーのみをテンプレートから除外します。 組織が[他のアカウントを除外する](../roles/security-emergency-access.md)必要がある場合、ポリシーを開き、除外されたユーザーとグループを変更してそれらを含めます。 
> 
> 既定では、各ポリシーは[レポート専用モード](concept-conditional-access-report-only.md)で作成されます。意図した結果を得るために、各ポリシーを有効にする前に、組織で使用状況をテストおよび監視することをお勧めします。

- Identities
   - [管理者に多要素認証を要求する](howto-conditional-access-policy-admin-mfa.md)\*
   - [セキュリティ情報登録のセキュリティ保護](howto-conditional-access-policy-registration.md)
   - [レガシ認証をブロックする](howto-conditional-access-policy-block-legacy.md)\*
   - [すべてのユーザーに多要素認証を要求する](howto-conditional-access-policy-all-users-mfa.md)\*
   - ゲスト アクセスに多要素認証を要求する
   - [Azure 管理に多要素認証を要求する](howto-conditional-access-policy-azure-management.md)\*
   - [危険なサインインに多要素認証を要求する](howto-conditional-access-policy-risk.md) **Azure AD Premium P2 を要求する**
   - [危険性の高いユーザーにパスワードの変更を要求する](howto-conditional-access-policy-risk-user.md) **Azure AD Premium P2 を要求する**
- デバイス
   - [準拠しているか、Hybrid Azure AD Join を使用したデバイスを管理者に要求する](howto-conditional-access-policy-compliant-device.md)
   - 不明なまたはサポートされていないデバイス プラットフォームのアクセスをブロックする
   - 永続的なブラウザー セッションなし
   - [承認済みのクライアント アプリまたはアプリ保護を要求する](howto-policy-approved-app-or-app-protection.md)
   - 準拠しているか、Hybrid Azure AD Join を使用したデバイス、または多要素認証をすべてのユーザーに要求する
   - アンマネージド デバイスに対してアプリケーションによって適用される制限を使用する

> \* これらの 4 つのポリシーをまとめて構成すると、[セキュリティの既定値群](../fundamentals/concept-fundamentals-security-defaults.md)によって有効にされたものと同じ機能が提供されます。

Microsoft にこれらのポリシーの作成を許可しない組織は、**ポリシーの概要の表示** から設定をコピーして手動でポリシーを作成するか、リンクされた記事を使用してポリシーを作成できます。 

### <a name="other-policies"></a>その他のポリシー

* [場所ごとにアクセスをブロックする](howto-conditional-access-policy-location.md)
* [特定のアプリ以外のアクセスをブロックする](howto-conditional-access-policy-block-access.md)

## <a name="emergency-access-accounts"></a>緊急アクセス アカウント

緊急アクセス アカウントとそれが重要である理由の詳細については、以下の記事を参照してください。 

* [Azure AD で緊急アクセス用アカウントを管理する](../roles/security-emergency-access.md)
* [Azure Active Directory で回復性があるアクセス制御管理戦略を作成する](../authentication/concept-resilient-controls.md)

## <a name="next-steps"></a>次のステップ

- [条件付きアクセスの What If ツールを使用してサインイン動作をシミュレートします。](troubleshoot-conditional-access-what-if.md)

- [条件付きアクセスのレポート専用モードを使用して、新しいポリシー決定の結果を判断します。](concept-conditional-access-report-only.md)
