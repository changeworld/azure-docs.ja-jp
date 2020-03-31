---
title: 条件付きアクセス ベースライン ポリシー - Azure Active Directory
description: 組織を一般的な攻撃から保護する条件付きアクセス ベースライン ポリシー
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 55de5a5c604273225a85e49ca682980f83a951d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "75767570"
---
# <a name="what-are-baseline-policies"></a>ベースライン ポリシーとは?

ベースライン ポリシーは、多くの一般的な攻撃から組織を保護するために役立つ一連の定義済みのポリシーです。 これらの一般的な攻撃としては、パスワード スプレー、リプレイ、およびフィッシングなどが考えられます。 ベースライン ポリシーは、Azure AD のすべてのエディションで使用できます。 この数年間で ID ベースの攻撃が増えているため、Microsoft はこれらのベースライン保護ポリシーをすべてのユーザーが利用できるようにしています。 これらの 4 つのポリシーの目標は、すべての組織が追加の費用なしでベースライン レベルのセキュリティを確実に有効にできるようにすることです。

カスタマイズされた条件付きアクセス ポリシーを管理するには、Azure AD Premium ライセンスが必要です。

> [!IMPORTANT]
> ベースライン ポリシーは非推奨になります。 詳細については、「[Azure Active Directory の新着情報](../fundamentals/whats-new.md#replacement-of-baseline-policies-with-security-defaults)」を参照してください。

## <a name="baseline-policies"></a>ベースライン ポリシー

![Azure portal での条件付きアクセス ベースライン ポリシー](./media/concept-baseline-protection/conditional-access-baseline-policies.png)

ベースライン ポリシーには、次の 4 つがあります。

* Require MFA for admins (プレビュー)
* End user protection (プレビュー)
* Block legacy authentication (プレビュー)
* Require MFA for Service Management (プレビュー)

この 4 つのポリシーはすべて、POP、IMAP、より以前の Office デスクトップ クライアントなどのレガシ認証フローに影響を与えます。

### <a name="exclusions"></a>除外

ベースライン ポリシーが最初のパブリック プレビューになった時点では、ユーザーをポリシーから除外するオプションが存在していました。 この機能はプレビューを通じて進化し、2019 年 7 月に削除されました。 既に除外を作成していた組織は除外を保持し続けることができましたが、新しいユーザーは除外をポリシーに追加できませんでした。

### <a name="require-mfa-for-admins-preview"></a>Require MFA for admins (プレビュー)

管理者アカウントには権限とアクセス権があるため、特別な注意を払って対処する必要があります。 特権アカウントの保護を向上するための一般的な方法の 1 つは、特権アカウントがサインインに使用されるときに、強力な形式のアカウント検証を必須にすることです。 Azure Active Directory では、Azure Multi-Factor Authentication への登録とその使用を管理者に要求することで、アカウント検証を強力にすることができます。

管理者に MFA を要求する (プレビュー) は、最も高い特権を持つ Azure AD ロールであると見なされている次のディレクトリ ロールに多要素認証 (MFA) を要求するベースライン ポリシーです。

* 全体管理者
* SharePoint 管理者
* Exchange 管理者
* 条件付きアクセス管理者
* セキュリティ管理者
* ヘルプデスク管理者/パスワード管理者
* 課金管理者
* ユーザー管理者

組織のスクリプトまたはコードでこれらのアカウントが使用されている場合は、それを[マネージド ID](../managed-identities-azure-resources/overview.md) に置き換えることを検討してください。

### <a name="end-user-protection-preview"></a>End user protection (プレビュー)

高い特権を持つ管理者だけが攻撃の標的になるわけではありません。 悪意のあるアクターは通常のユーザーをターゲットにする傾向があります。 これらの悪意のあるアクターは、アクセス権を取得した後、元々のアカウント所有者に代わって機密性の高い情報へのアクセスを要求したり、ディレクトリ全体をダウンロードして組織全体にフィッシング攻撃を仕掛けたりする可能性があります。 すべてのユーザーを対象にした保護を向上するための一般的な方法の 1 つは、危険なサインインが検出されたときに、より強力な形式のアカウント検証を要求することです。

**End user protection (プレビュー)** は、ディレクトリ内のすべてのユーザーを保護するベースライン ポリシーです。 このポリシーを有効にすると、すべてのユーザーが 14 日以内に Azure Multi-Factor Authentication に登録することを求められます。 登録されると、ユーザーはリスクの高いサインインの試行中にのみ、MFA を求められます。 侵害されたユーザー アカウントは、パスワードがリセットされてリスクがなくなるまでブロックされます。 

> [!NOTE]
> ポリシーをアクティブ化すると、以前にリスクのフラグが付けられたユーザーは、パスワードがリセットされてリスクがなくなるまでブロックされます。

### <a name="block-legacy-authentication-preview"></a>Block legacy authentication (プレビュー)

レガシ認証プロトコル (例: IMAP、SMTP、POP3) は、より古いメール クライアントで、認証のために通常使用されているプロトコルです。 レガシ プロトコルでは、多要素認証はサポートされていません。 ご利用のディレクトリに対して多要素認証を要求するポリシーをお持ちの場合でも、悪意のあるアクターはこれらのレガシ プロトコルのいずれかを使用して多要素認証をバイパスすることができます。

レガシ プロトコルによって行われる悪意のある認証要求からアカウントを保護するための最善の方法は、それらをブロックすることです。

**Block legacy authentication (プレビュー)** ベースライン ポリシーでは、レガシ プロトコルを使用して行われる認証要求がブロックされます。 すべてのユーザーを正常にサインインさせるには、先進認証を使用する必要があります。 その他のベースライン ポリシーと組み合わせて使用すれば、レガシ プロトコルから出される要求はブロックされます。 さらに、すべてのユーザーは必要に応じて MFA を要求されます。 このポリシーで、Exchange ActiveSync はブロックされません。

### <a name="require-mfa-for-service-management-preview"></a>Require MFA for Service Management (プレビュー)

組織はさまざまな Azure サービスを使用すると共に、それらを Azure Resource Manager ベースの次のようなツールで管理します。

* Azure portal
* Azure PowerShell
* Azure CLI

これらのツールのいずれかを使用してリソースを管理することは、高い特権が必要なアクションです。 これらのツールでは、サービス設定やサブスクリプションの課金など、サブスクリプション全体の構成を変更できます。

特権を必要とするアクションを保護するために、この **Require MFA for Service Management (プレビュー)** ポリシーでは、Azure portal、Azure PowerShell、または Azure CLI にアクセスするどのユーザーに対しても多要素認証を要求します。

## <a name="next-steps"></a>次のステップ

詳細については、次を参照してください。

* [セキュリティ既定値の有効化](../fundamentals/concept-fundamentals-security-defaults.md)
* [一般的な条件付きアクセス ポリシー](concept-conditional-access-policy-common.md)
* [ID インフラストラクチャをセキュリティ保護する 5 つのステップ](../../security/fundamentals/steps-secure-identity.md)
