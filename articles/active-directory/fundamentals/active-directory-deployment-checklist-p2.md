---
title: Premium P2 ライセンス機能のチェックリスト - Azure Active Directory | Microsoft Docs
description: 30 日目まで、90 日目まで、およびそれ以降の Azure Active Directory Premium P2 機能のデプロイのためのデプロイ チェックリスト。
services: active-directory
ms.service: active-directory
ms.component: ''
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.custom: it-pro, seodec18
ms.openlocfilehash: 4fcb692d7189c84e32f55995538ffc692cb67dd6
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/07/2019
ms.locfileid: "54064714"
---
# <a name="azure-active-directory-premium-p2-licensing-feature-checklist"></a>Azure Active Directory Premium P2 ライセンス機能のチェックリスト

組織の Azure Active Directory (Azure AD) をデプロイして、継続的にセキュリティで保護することは困難と感じる場合があります。 この記事では、参考になる一般的なタスクをいくつか示します。 セキュリティ体制を強化するには、通常、30 日、90 日、またはそれを超える期間にわたってこのようなタスクを実行します。 Azure AD を既にデプロイした組織でも、投資による最大のメリットが確実に得られるように、このチェックリストを活用できます。

綿密に計画され、実行された ID インフラストラクチャは、認証済みのユーザーおよびデバイスのみから生産性ワークロードとデータへより安全にアクセスするための基礎になります。

## <a name="prerequisites"></a>前提条件

このガイドは、Azure AD Premium P2 ライセンス、Enterprise Mobility + Security E5、Microsoft 365 E5、または同様のライセンス バンドルがあることを前提としています。

[Azure AD のライセンス](https://azure.microsoft.com/pricing/details/active-directory/)

[Microsoft 365 Enterprise](https://www.microsoft.com/en-us/licensing/product-licensing/microsoft-365-enterprise)

[Enterprise Mobility + Security](https://www.microsoft.com/en-us/licensing/product-licensing/enterprise-mobility-security)

## <a name="plan-and-deploy-day-1-30"></a>計画とデプロイ: 1 ～ 30 日

- 複数のグローバル管理者を指定する (非常用アカウント)
   - [Azure AD で緊急アクセス用管理者アカウントを管理する](../users-groups-roles/directory-emergency-access.md)
- Azure AD Privileged Identity Management (PIM) を有効にしてレポートを表示する
   - [PIM の使用を開始する](../privileged-identity-management/pim-getting-started.md)
- 可能な場合、非グローバル管理者ロールを使用する
   - [Azure Active Directory での管理者ロールの割り当て](../users-groups-roles/directory-assign-admin-roles.md)
- Authentication
   - [セルフサービスによるパスワードのリセットをロール アウトする](../authentication/howto-sspr-deployment.md)
   - Azure AD パスワード保護のプレビューをデプロイする
      - [組織内の不適切なパスワードを排除する](../authentication/concept-password-ban-bad.md)
      - [Windows Server Active Directory に Azure AD パスワード保護を適用する](../authentication/concept-password-ban-bad-on-premises.md)
   - アカウント ロックアウト ポリシーを構成する
      - [Azure Active Directory のスマート ロックアウト](../authentication/howto-password-smart-lockout.md)
      - [AD FS エクストラネットのロックアウトおよびエクストラネットのスマート ロックアウト](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection)
   - [条件付きアクセス ポリシーを使用して Azure AD Multi-Factor Authentication をデプロイする](../authentication/howto-mfa-getstarted.md)
   - [セルフサービスのパスワード リセットと Azure AD Multi-Factor Authentication の集中型登録 (プレビュー) を有効にする](../authentication/concept-registration-mfa-sspr-converged.md)
   - [Azure Active Directory Identity Protection を有効にする](../identity-protection/enable.md)
      - [リスク イベントを使用して Multi-Factor Authentication とパスワードの変更をトリガーする](../authentication/tutorial-risk-based-sspr-mfa.md)
   - [パスワードのガイダンス](https://www.microsoft.com/research/publication/password-guidance/)
      - 最低 8 文字長の要件を維持する。必ずしも、長いほど良いわけではない
      - 文字構成の要件を削除する
      - [必須になっている定期的なユーザー アカウントのパスワード リセットを削除する](../authentication/concept-sspr-policy.md#set-a-password-to-never-expire)
- オンプレミス Active Directory からユーザーを同期する
   - [Azure AD Connect のインストール](../connect/active-directory-aadconnect-select-installation.md)
   - [パスワード ハッシュ同期の実装](../connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md)
   - [パスワード ライトバックの実装](../authentication/howto-sspr-writeback.md)
   - [Azure AD Connect Health の実装](../connect-health/active-directory-aadconnect-health.md)
- [Azure Active Directory のグループ メンバーシップでユーザーにライセンスを割り当てる](../users-groups-roles/licensing-groups-assign.md)

## <a name="plan-and-deploy-day-31-90"></a>計画とデプロイ: 31 ～ 90 日

- [ゲスト ユーザー アクセスを計画する](../b2b/what-is-b2b.md)
   - [Azure Portal で Azure Active Directory B2B コラボレーション ユーザーを追加する](../b2b/add-users-administrator.md)
   - [B2B ユーザーに対する特定組織からの招待を許可またはブロックする](../b2b/allow-deny-list.md)
   - [Azure AD の B2B ユーザーにオンプレミスのアプリケーションへのアクセス許可する](../b2b/hybrid-cloud-to-on-premises.md)
- ユーザーのライフサイクル管理の戦略に関する決定を行う
- [デバイス管理戦略を決定する](../devices/overview.md)
   - [Azure AD 参加の使用シナリオとデプロイに関する考慮事項](../devices/azureadjoin-plan.md)
- [組織の Windows Hello for Business を管理する](/windows/security/identity-protection/hello-for-business/hello-manage-in-organization)

## <a name="plan-and-deploy-day-90-and-beyond"></a>計画とデプロイ: 90 日目以降

- [Azure AD Privileged Identity Management](../privileged-identity-management/pim-configure.md)
   - [PIM で Azure AD ディレクトリ ロールの設定を構成する](../privileged-identity-management/pim-how-to-change-default-settings.md)
   - [PIM で Azure AD ディレクトリ ロールを割り当てる](../privileged-identity-management/pim-how-to-add-role-to-user.md)
- [PIM で Azure AD ディレクトリ ロールのアクセス レビューを完了する](../privileged-identity-management/pim-how-to-start-security-review.md)
- ユーザーのライフサイクルを総合的に管理する
   - Azure AD が ID ライフサイクルを管理する方法を備えている
   - 未承認のアクセスを防ぐために、従業員のアカウント ライフサイクルから手動の手順を削除する
      - 適切なソース (人事システム) からの ID を Azure AD に同期する。
      - [動的グループを使用して、部門、役職、リージョン、その他の属性など、人事 (または、信頼できるソース) からの属性に基づいてグループにユーザーを自動的に割り当てる](../users-groups-roles/groups-dynamic-membership.md)
      - [グループベース アクセス管理のプロビジョニングを使用して、SaaS アプリケーションにユーザーを自動的にプロビジョニングする](../manage-apps/what-is-access-management.md)

## <a name="next-steps"></a>次の手順

[ID とデバイスのアクセスの構成](https://docs.microsoft.com/microsoft-365/enterprise/microsoft-365-policies-configurations)

[推奨される一般的な ID とデバイスのアクセス ポリシー](https://docs.microsoft.com/microsoft-365/enterprise/identity-access-policies)
