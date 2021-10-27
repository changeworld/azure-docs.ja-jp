---
title: Azure Active Directory でのパスワード ポリシーと脆弱なパスワードの組み合わせチェック
description: Azure Active Directory でのパスワード ポリシーと脆弱なパスワードの組み合わせチェックについて説明します
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 10/14/2021
ms.author: justinha
author: sajiang
manager: daveba
ms.reviewer: sajiang
ms.collection: M365-identity-device-management
ms.openlocfilehash: 21fb52047822df5c14e8b5a21c017a2e4ca5f1d6
ms.sourcegitcommit: 5361d9fe40d5c00f19409649e5e8fed660ba4800
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/18/2021
ms.locfileid: "130138764"
---
# <a name="combined-password-policy-and-weak-password-check-in-azure-active-directory"></a>Azure Active Directory でのパスワード ポリシーと脆弱なパスワードの組み合わせチェック

2021 年 10 月から、パスワード ポリシーに準拠するための Azure Active Directory (Azure AD) 検証には、[既知の脆弱なパスワード](concept-password-ban-bad.md)とそのバリアントのチェックも含まれます。 パスワード ポリシーと禁止パスワードの組み合わせチェックをテナントに展開すると、Azure AD と Office 365 の管理センター ユーザーは、パスワードを作成、変更、またはリセットするときに違いに気づくことがあります。 このトピックでは、Azure AD によってチェックされるパスワード ポリシー条件について詳しく説明します。 

## <a name="azure-ad-password-policies"></a>Azure AD パスワード ポリシー

パスワード ポリシーは、Azure AD で直接作成および管理されるすべてのユーザーおよび管理者のアカウントに適用されます。 [脆弱なパスワードを禁止し](concept-password-ban-bad.md)、不正なパスワードの試行が繰り返された後に[アカウントをロックする](howto-password-smart-lockout.md)パラメーターを定義できます。 その他のパスワード ポリシー設定は変更できません。

Azure AD のパスワード ポリシーは、EnforceCloudPasswordPolicyForPasswordSyncedUsers を有効にしない限り、Azure AD Connect を使用してオンプレミスの AD DS 環境から同期されたユーザー アカウントに対して適用されません。

Azure AD で作成、変更、またはリセットされるすべてのパスワードには、次の Azure AD のパスワード ポリシー要件が適用されます。 要件は、ユーザー プロビジョニング、パスワードの変更、パスワード リセットのフロー中に適用されます。 特に明記されていない場合、これらの設定を変更することはできません。

| プロパティ | 必要条件 |
| --- | --- |
| 使用できる文字 |大文字 (A から Z)<br>小文字 (a から z)<br>数字 (0 から 9)<br>記号:<br>- @ # $ % ^ & * - _ ! + = [ ] { } &#124; \ : ' , . ? / \` ~ " ( ) ; < ><br>- 空白 |
| 使用できない文字 | Unicode 文字 |
| パスワードの長さ |パスワードに必要な条件<br>- 8 文字以上<br>- 最大 256 文字</li> |
| パスワードの複雑さ |パスワードには次の 4 つのうち 3 つが必要です。<br>- 大文字<br>- 小文字<br>- 数字 <br>- 記号<br> 注: Education テナントでは、パスワードの複雑さのチェックは要求されません。 |
| 最近使用されていないパスワード | ユーザーが自分のパスワードを変更またはリセットする場合、新しいパスワードを、現在のパスワードや最近使用したパスワードと同じにすることはできません。 |
| パスワードが [Azure AD のパスワード保護](concept-password-ban-bad.md)によって禁止されていない | パスワードは、Azure AD のパスワード保護についての禁止パスワードのグローバル リストや、組織に固有のカスタマイズ可能な禁止パスワード リストに含まれていてはいけません。 |

## <a name="password-expiration-policies"></a>パスワードの有効期限のポリシー

パスワードの有効期限のポリシーは変更されていませんが、完全を期すために、このトピックに含まれています。 "*グローバル管理者*" または "*ユーザー管理者*" は、[Windows PowerShell 用 Microsoft Azure AD モジュール](/powershell/module/Azuread/)を使用して、ユーザーのパスワード有効期限が切れないように設定できます。

> [!NOTE]
> 既定では、Azure AD Connect を介して同期されていないユーザー アカウントのパスワードのみ、期限切れにならないように構成できます。 ディレクトリ同期の詳細については、[AD と Azure AD の接続](../hybrid/how-to-connect-password-hash-synchronization.md#password-expiration-policy)に関するページをご覧ください。

また、PowerShell を使用すると、期限のない構成を削除したり、期限が切れないように設定されているユーザー パスワードを確認したりすることもできます。

次の有効期限の要件は、Intune や Microsoft 365 など、ID やディレクトリ サービスに Azure AD を使用する他のプロバイダーに適用されます。 

| プロパティ | 必要条件 |
| --- | --- |
| パスワードの有効期間 (パスワードの最大有効期間) |<ul><li>既定値:**90** 日。</li><li>値を構成するには、Windows PowerShell 用 Azure Active Directory モジュールから `Set-MsolPasswordPolicy` コマンドレットを使用します。</li></ul> |
| パスワードの期限切れの通知 (ユーザーにパスワードの有効期限が通知されるタイミング) |<ul><li>既定値:**14** 日 (パスワードの有効期限が切れる前)。</li><li>値を構成するには、`Set-MsolPasswordPolicy` コマンドレットを使用します。</li></ul> |
| パスワードの有効期限 (パスワードを無期限にします) |<ul><li>既定値: **false** (パスワードの有効期限が指定されていることを示します)。</li><li>各ユーザー アカウントの値を構成するには、`Set-MsolUser` コマンドレットを使用します。</li></ul> |

## <a name="next-steps"></a>次のステップ

- [Azure Active Directory のパスワード ポリシーとアカウント制限](concept-sspr-policy.md)
- [Azure Active Directory パスワード保護を使用して不適切なパスワードを排除する](concept-password-ban-bad.md)
