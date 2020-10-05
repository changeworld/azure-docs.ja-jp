---
title: Azure セキュリティ ベンチマーク V2 - 特権アクセス
description: Azure セキュリティ ベンチマーク V2 特権アクセス
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/13/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 8727cbd07fad1960f4bdb33742729b6bda3a369e
ms.sourcegitcommit: 94c750edd4d755d6ecee50ac977328098a277479
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2020
ms.locfileid: "90059111"
---
# <a name="security-control-privileged-access"></a>セキュリティ コントロール特権アクセス

特権アクセスには、Azure テナントとリソースへの特権アクセスを保護するためのコントロールが含まれます。 これには、管理モデル、管理アカウント、特権アクセス ワークステーションを、意図的なリスクと偶発的なリスクから保護するためのさまざまなコントロールが含まれています。

## <a name="pa-1-protect-and-limit-the-global-administrators"></a>PA-1:グローバル管理者を保護して制限する

| Azure ID | CIS コントロール v7.1 ID | NIST SP800-53 r4 ID |
|--|--|--|--|
| PA-1 | 4.3、4.8 | AC-2 |

グローバル管理者ロールに割り当てられているユーザーは、Azure AD 組織内のすべての管理設定の読み取りと変更を行うことができます。 Azure 全体管理者アカウントの数は、サブスクリプションあたりに 2 つまでに制限してください。 Azure AD で最も重要な組み込みロールは、全体管理者と特権ロール管理者です。それは、これら 2 つのロールが割り当てられたユーザーが、管理者ロールを委任できるからです。
- 全体管理者または会社の管理者:このロールが割り当てられたユーザーは、Azure AD のすべての管理機能に加え、Azure AD ID を使用するサービスにもアクセスできます。

- 特権ロール管理者:このロールが割り当てられたユーザーは、Azure AD と Azure AD Privileged Identity Management (PIM) 内でロールの割り当てを管理できます。 さらに、このロールは、PIM と管理単位のすべての側面を管理できます。

注:特定の特権アクセス許可を割り当てられたカスタム ロールを使用する場合は、管理する必要のある他の重要なロールがある場合があります。 また、重要なビジネス資産の管理者アカウントに同様のコントロールを適用することもできます。  

Azure Privileged Identity Management (PIM) を使用して、Azure リソースと Azure AD への Just-In-Time (JIT) の特権アクセスを有効にすることができます。 ユーザーが必要とする場合にのみ特権タスクを実行するための一時的なアクセス許可は、JIT によって付与されます。 PIM を使用すると、Azure AD 組織に不審なアクティビティや安全でないアクティビティがある場合に、セキュリティ アラートを生成することもできます。

注:Azure SQL などの一部の Azure サービスでは、Azure AD 認証に加えてローカル ユーザー認証がサポートされています。 この種類のローカル ユーザー認証は、Azure AD によって管理されません。 これらのユーザーは個別に管理する必要があります。

- [Azure AD での管理者ロールのアクセス許可](../../active-directory/users-groups-roles/directory-assign-admin-roles.md)

- [Azure カスタム ロール](../../role-based-access-control/custom-roles.md)

- [Azure Privileged Identity Management のセキュリティ アラートを使用する](../../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md)

- [PowerShell を使用して Azure AD でディレクトリ ロールを取得する方法](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Azure Security Center を使用して ID とアクセスを監視する](../../security-center/security-center-identity-access.md)

**責任**: Customer

**お客様のセキュリティ関係者**:

- [ID とキー](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [アプリケーションのセキュリティと DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="pa-2-review-and-reconcile-user-access-regularly"></a>PA-2:ユーザー アクセスを定期的に確認して調整する

| Azure ID | CIS コントロール v7.1 ID | NIST SP800-53 r4 ID |
|--|--|--|--|
| PA-2 | 4.1、16.9、16.10 | AC-2 |

ユーザー アカウントとアクセス エンタイトルメントを定期的に確認して、ユーザー アカウントとそのアクセスが有効であることを確実にします。 

Azure AD の ID およびアクセス レビューを使用して、グループ メンバーシップ、エンタープライズ アプリケーションへのアクセス、およびロールの割り当てを管理します。 Azure AD レポートによって、古いアカウントの検出に役立つログが提供されます。 また、Azure AD Privileged Identity Management を使用してアクセス レビュー レポート ワークフローを作成し、レビュー プロセスを容易にすることもできます。

Azure サービスおよびワークロード レベルの管理ユーザーの場合は、より頻繁にユーザーとアクセスのレビューを実行する必要があります。 また、Azure Privileged Identity Management のセキュリティ アラートを使用して、管理者アカウントが作成されたことを検出したり、古くなったり不適切に構成されている管理者アカウントを見つけたりすることもできます。 

注:Azure SQL などの一部の Azure サービスでは、Azure AD で管理されていないローカル ユーザーがサポートされています。 これらのユーザーは個別に管理する必要があります。

- [PowerShell を使用して Azure AD でディレクトリ ロールを取得する方法](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [PowerShell を使用して Azure AD でディレクトリ ロールのメンバーを取得する方法](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

- [Azure Security Center を使用して ID とアクセスを監視する](../../security-center/security-center-identity-access.md)

- [Azure Privileged Identity Management のセキュリティ アラートを使用する](../../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md)

- [チーム間で管理者の責任を調整する](/azure/cloud-adoption-framework/organize/raci-alignment) 

- [Azure AD のレポートの概要](/azure/active-directory/reports-monitoring/)

- [Azure AD の ID およびアクセス レビューの使用方法](../../active-directory/governance/access-reviews-overview.md)

- [Privileged Identity Management - Azure AD ロールへのアクセスのレビュー](../../active-directory/privileged-identity-management/pim-how-to-start-security-review.md)

- [Azure Security Center - ID とアクセスの監視](../../security-center/security-center-identity-access.md)

**責任**: Customer

**お客様のセキュリティ関係者**:

- [ID とキー](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [アプリケーションのセキュリティと DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [セキュリティ コンプライアンス管理](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="pa-3-set-up-an-emergency-access-account-in-azure-ad"></a>PA-3:Azure AD で緊急アクセス用アカウントを設定する

| Azure ID | CIS コントロール v7.1 ID | NIST SP800-53 r4 ID |
|--|--|--|--|
| PA-3 | 12.3 | AC-2 |

Azure AD 組織から誤ってロックアウトされるのを防ぐために、通常の管理者アカウントを使用できない場合にアクセスするための緊急アクセス用アカウントを設定します。 緊急アクセス用アカウントは高い特権を持っており、特定の個人に割り当てることはできません。 緊急アクセス用アカウントは、通常の管理者アカウントを使うことができない "緊急事態" に制限されます。

緊急アクセス用アカウントの資格情報 (パスワード、証明書、スマート カードなど) は安全に保管し、緊急時にのみそれらを使うことを許可された個人のみに知らせる必要があります。

- [Azure AD で緊急アクセス用アカウントを管理する](../../active-directory/users-groups-roles/directory-emergency-access.md)

**責任**: Customer

**お客様のセキュリティ関係者**:

- [ID とキー](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [アプリケーションのセキュリティと DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [セキュリティ コンプライアンス管理](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [セキュリティ オペレーション センター (SOC)](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

## <a name="pa-4-automate-azure-identity-and-access-request-workflow"></a>PA-4:Azure の ID とアクセス要求のワークフローを自動化する

| Azure ID | CIS コントロール v7.1 ID | NIST SP800-53 r4 ID |
|--|--|--|--|
| PA-4 | N/A | AC-2、AC-5、PM-10 |

Azure AD のエンタイトルメント管理機能を使用して、アクセスの割り当て、レビュー、有効期限など、Azure アクセス要求のワークフローを自動化します。 2 段階または複数段階の承認もサポートされています。  

- [Azure AD エンタイトルメント管理とは](../../active-directory/governance/entitlement-management-overview.md)

- [アクセス要求と承認プロセスを設定する方法](../../active-directory/governance/entitlement-management-access-package-request-policy.md)

**責任**: Customer

**お客様のセキュリティ関係者**:

- [ID とキー](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [アプリケーションのセキュリティと DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [セキュリティ コンプライアンス管理](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="pa-5-use-highly-secured-machines-for-administrative-tasks"></a>PA-5:管理タスクに高度にセキュリティ保護されたマシンを使用する

| Azure ID | CIS コントロール v7.1 ID | NIST SP800-53 r4 ID |
|--|--|--|--|
| PA-5 | 4.6、11.6、12.12 | AC-2、SC-7 |

セキュリティで保護された分離したワークステーションは、管理者、開発者、重要なサービス オペレーターのような機密性の高い役割のセキュリティには非常に重要です。 管理タスクに高度にセキュリティ保護されたユーザー ワークステーションや Azure Bastion を使用します。
- Azure Active Directory、Microsoft Defender Advanced Threat Protection (ATP)、または Microsoft Intune を使用して、管理タスクのためにセキュリティで保護されたマネージド ユーザー ワークステーションを展開します。 セキュリティで保護されたワークステーションを一元管理して、強力な認証、ソフトウェアとハードウェアのベースライン、制限された論理アクセスとネットワーク アクセスなどのセキュリティで保護された構成を実施できます。 

- RDP または SSH 経由で仮想マシンにアクセスするためのセキュリティで保護されたパスに Azure Bastion 機能を使用します。 Azure Bastion は、新しい仮想ネットワークを作成することなく、仮想ネットワークごとにプロビジョニングできるフル マネージド PaaS サービスです。 

- [セキュリティで保護された Azure マネージド ワークステーションを理解する](../../active-directory/devices/concept-azure-managed-workstation.md)

- [セキュリティで保護された Azure マネージド ワークステーションのデプロイ](../../active-directory/devices/howto-azure-managed-workstation.md)

- [Azure Bastion ホストを使用する](../../bastion/bastion-create-host-portal.md)

**責任**: Customer

**お客様のセキュリティ関係者**:

- [アプリケーションのセキュリティと DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [セキュリティ オペレーション センター (SOC)](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [ID とキー](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

## <a name="pa-6-assign-privileges-to-resources-using-azure-rbac"></a>PA-6:Azure RBAC を使用してリソースに特権を割り当てる

| Azure ID | CIS コントロール v7.1 ID | NIST SP800-53 r4 ID |
|--|--|--|--|
| PA-6 | 14.6 | AC-2、AC-3 |

Azure ロールベースのアクセス制御 (RBAC) を使用すると、ロールの割り当てを通じて Azure リソース アクセスに対する特権を管理できます。 これらのロールを、ユーザー、グループ サービス プリンシパル、およびマネージド ID に割り当てることができます。 特定のリソースに対して定義済みの組み込みロールがあります。これらのロールは、Azure CLI、Azure PowerShell、Azure portal などのツールを使用してインベントリまたは照会できます。 

- [Azure ロールベースのアクセス制御 (Azure RBAC) とは](../../role-based-access-control/overview.md)

- [Azure で RBAC を構成する方法](../../role-based-access-control/role-assignments-portal.md)

**責任**: Customer

**お客様のセキュリティ関係者**:

- [アプリケーションのセキュリティと DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [セキュリティ コンプライアンス管理](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [体制管理](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [ID とキー](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

## <a name="pa-7-choose-approval-process-for-microsoft-support"></a>PA-7:Microsoft サポートの承認プロセスを選択する

| Azure ID | CIS コントロール v7.1 ID | NIST SP800-53 r4 ID |
|--|--|--|--|
| PA-7 | 16 | AC-2、AC-3、AC-4 |

Microsoft が顧客データにアクセスする必要があるサポート シナリオでは、カスタマー ロックボックスによって、各顧客のデータ アクセス要求を明示的に確認して承認または拒否する機能が提供されます。

- [カスタマー ロックボックスについて](../fundamentals/customer-lockbox-overview.md)

**責任**: Customer

**お客様のセキュリティ関係者**:

- [アプリケーションのセキュリティと DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [セキュリティ コンプライアンス管理](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [ID とキー](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

