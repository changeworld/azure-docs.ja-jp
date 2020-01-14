---
title: Azure セキュリティ コントロール - ID とアクセスの制御
description: セキュリティ コントロール ID とアクセスの制御
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 9081f74aee7ff503c7fe29cef6ca76e6d6b46a4f
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/31/2019
ms.locfileid: "75563993"
---
# <a name="security-control-identity-and-access-control"></a>セキュリティ コントロール: ID およびアクセス制御

ID およびアクセス管理の推奨事項では、ID ベースのアクセス制御に関連する問題への対処、管理アクセスのロックダウン、ID 関連イベントのアラート、異常なアカウント動作、およびロールベースのアクセス制御を重点的に取り上げます。

## <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 管理アカウントのインベントリを維持する

| Azure ID | CIS ID | 担当 |
|--|--|--|
| 3.1 | 4.1 | Customer |

Azure AD の組み込みロールは、明示的に割り当てる必要があり、クエリ可能でなければなりません。 Azure AD PowerShell モジュールを使用してアドホック クエリを実行し、管理グループのメンバーであるアカウントを検出します。

PowerShell を使用して Azure AD でディレクトリ ロールを取得する方法:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

PowerShell を使用して Azure AD でディレクトリ ロールのメンバーを取得する方法:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

## <a name="32-change-default-passwords-where-applicable"></a>3.2: 既定のパスワードを変更する (該当する場合)

| Azure ID | CIS ID | 担当 |
|--|--|--|
| 3.2 | 4.2 | Customer |

Azure AD には既定のパスワードという概念がありません。 パスワードを必要とする他の Azure リソースでは、パスワードが強制的に作成されます。これには複雑な要件と、サービスによって異なるパスワードの最小文字数が適用されます。 既定のパスワードが使用される可能性があるサードパーティ製のアプリケーションとマーケットプレース サービスについては、お客様が責任を負うものとします。

## <a name="33-use-dedicated-administrative-accounts"></a>3.3: 専用管理者アカウントを使用する

| Azure ID | CIS ID | 担当 |
|--|--|--|
| 3.3 | 4.3 | Customer |

専用管理者アカウントの使用に関する標準的な操作手順を作成します。 Azure Security Center ID とアクセス管理を使用して、管理者アカウントの数を監視します。

また、Microsoft サービスの Azure AD Privileged Identity Management の特権ロール、および Azure Resource Manager を使用して、Just-In-Time/Just-Enough-Access を有効にすることもできます。 

詳細情報: https://docs.microsoft.com/azure/active-directory/privileged-identity-management/

## <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: シングル サインオン (SSO) と Azure Active Directory を統合する

| Azure ID | CIS ID | 担当 |
|--|--|--|
| 3.4 | 4.4. | Customer |

可能な限り、サービスごとに個別のスタンドアロン資格情報を構成するのではなく、Azure Active Directory SSO を使用します。 Azure Security Center ID とアクセス管理の推奨事項を使用してください。

Azure AD を使用した SSO について: https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

## <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: すべての Azure Active Directory ベースのアクセスに多要素認証を使用する

| Azure ID | CIS ID | 担当 |
|--|--|--|
| 3.5 | 4.5、11.5、12.11、16.3 | Customer |

Azure AD MFA を有効にして、Azure Security Center ID とアクセス管理の推奨事項に従います。

Azure で MFA を有効にする方法:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Azure Security Center で ID とアクセスを監視する方法:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

## <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: すべての管理タスクに専用マシン (特権アクセス ワークステーション) を使用する

| Azure ID | CIS ID | 担当 |
|--|--|--|
| 3.6 | 4.6、11.6、12.12 | Customer |

MFA が構成されている PAW (特権アクセス ワークステーション) を使用して Azure リソースにログインし、構成します。

特権アクセス ワークステーションについて: https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Azure で MFA を有効にする方法: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted


## <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: 管理者アカウントからの疑わしいアクティビティを記録してアラートを生成する

| Azure ID | CIS ID | 担当 |
|--|--|--|
| 3.7 | 4.8、4.9 | Customer |

環境内で疑わしいアクティビティまたは安全でないアクティビティが環境で発生したときに、Azure Active Directory セキュリティ レポートを使用して、ログおよびアラートを生成します。 Azure Security Center を使用して ID およびアクセス アクティビティを監視します。

危険なアクティビティのフラグが設定された Azure AD ユーザーを特定する方法:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

Azure Security Center でユーザーの ID およびアクセス アクティビティを監視する方法:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

## <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8:承認された場所からのみ Azure リソースを管理する

| Azure ID | CIS ID | 担当 |
|--|--|--|
| 3.8 | 11.7 | Customer |

条件付きアクセスのネームド ロケーションを使用して、IP アドレス範囲または国/地域の特定の論理グループからのアクセスのみを許可します。

Azure でネームド ロケーションを構成する方法:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

## <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory を使用する

| Azure ID | CIS ID | 担当 |
|--|--|--|
| 3.9 | 16.1、16.2、16.4、16.5、16.6 | Customer |

Azure Active Directory (AAD) を中央認証と承認システムとして使用します。 AAD でデータを保護するには、保存データと転送中のデータに強力な暗号化を使用します。 また、AAD では、ユーザーの資格情報がソルト化およびハッシュされ、安全に格納されます。

AAD インスタンスを作成して構成する方法: https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant

## <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: ユーザー アクセスを定期的に確認して調整する

| Azure ID | CIS ID | 担当 |
|--|--|--|
| 3.1 | 16.9、16.10 | Customer |

Azure AD には、古いアカウントの検出に役立つログが用意されています。 また、Azure ID アクセス レビューを使用して、グループ メンバーシップ、エンタープライズ アプリケーションへのアクセス、およびロールの割り当てを効率的に管理します。 ユーザー アクセスを定期的にレビューし、適切なユーザーのみが継続的なアクセス権を持っていることを確認できます。 

Azure AD レポート https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Azure ID アクセスレビューの使用方法: https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

## <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: 非アクティブ化されたアカウントへのアクセス試行を監視する

| Azure ID | CIS ID | 担当 |
|--|--|--|
| 3.11 | 16.12 | Customer |

Azure AD サインイン アクティビティ、監査、およびリスク イベント ログのソースにアクセスできるため、任意の SIEM/監視ツールとの統合が可能です。

このプロセスを効率化するには、Azure Active Directory ユーザー アカウントの診断設定を作成し、監査ログとサインイン ログを Log Analytics ワークスペースに送信します。 Log Analytics ワークスペースで必要なアラートを構成できます。

Azure Activity Logs を Azure Monitor に統合する方法:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

## <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: アカウント ログイン動作の偏差に関するアラートを生成する

| Azure ID | CIS ID | 担当 |
|--|--|--|
| 3.12 | 16.13 | Customer |

Azure AD のリスクおよび ID Protectoin 機能を使用して、ユーザー ID に関連して検出された疑わしいアクションに対する自動応答を構成します。 Azure Sentinel にデータを取り込んで、さらに詳しく調査することもできます。

Azure AD の危険なサインインを表示する方法:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

ID Protection のリスク ポリシーを構成して有効にする方法:

https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Azure Sentinel をオンボードする方法:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

## <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: サポート シナリオで関連する顧客データに Microsoft がアクセスできるようにする

| Azure ID | CIS ID | 担当 |
|--|--|--|
| 3.13 | 16 | Customer |

Microsoft が顧客データにアクセスする必要があるサポート シナリオで、カスタマー ロックボックスのインターフェイスを使用して、顧客データへのアクセス要求を確認し、承認または拒否することができます。

カスタマー ロックボックスについて:

https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview

## <a name="next-steps"></a>次のステップ

次のセキュリティ コントロールを参照してください。[データ保護](security-control-data-protection.md)
