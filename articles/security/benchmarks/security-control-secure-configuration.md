---
title: Azure のセキュリティ コントロール - セキュリティで保護された構成
description: セキュリティ コントロールのセキュリティで保護された構成
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 03564effeee36ddb3316d48329ccab8ccfce75b9
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/14/2020
ms.locfileid: "75934295"
---
# <a name="security-control-secure-configuration"></a>セキュリティ コントロールセキュリティで保護された構成

攻撃者から脆弱なサービスや設定が悪用されないように、Azure リソースのセキュリティ構成を確立、実装、および積極的に管理 (追跡、レポート、修正) します。

## <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1:すべての Azure リソースに対してセキュリティで保護された構成を確立する

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 7.1 | 5.1 | Customer |

Azure Policy または Azure Security Center を使用して、すべての Azure リソースのセキュリティ構成を維持します。

Azure Policy を構成して管理する方法:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

## <a name="72-establish-secure-operating-system-configurations"></a>7.2:セキュリティで保護されたオペレーティング システムの構成を確立する

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 7.2 | 5.1 | Customer |

Azure Security Center の推奨事項 &quot;Remediate Vulnerabilities in Security Configurations on your Virtual Machines&quot; (仮想マシンのセキュリティ構成の脆弱性を修復する) を使用して、すべてのコンピューティング リソースのセキュリティ構成を維持します。

Azure Security Center の推奨事項を監視する方法:

https://docs.microsoft.com/azure/security-center/security-center-recommendations

Azure Security Center の推奨事項を修復する方法:

https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

## <a name="73-maintain-secure-azure-resource-configurations"></a>7.3:セキュリティで保護された Azure リソースの構成を維持する

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 7.3 | 5.2 | Customer |

Azure リソース全体にセキュリティで保護された設定を適用するには、Azure ポリシー [拒否] と [存在する場合はデプロイする] を使用します。

Azure Policy を構成して管理する方法:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Policy の効果を理解する:

https://docs.microsoft.com/azure/governance/policy/concepts/effects

## <a name="74-maintain-secure-operating-system-configurations"></a>7.4:セキュリティで保護されたオペレーティング システムの構成を維持する

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 7.4 | 5.2 | 共有 |

基本オペレーティング システム イメージは、Microsoft によって管理および保守されます。

ただし、Azure Resource Manager テンプレートや Desired State Configuration を使用して、組織に必要なセキュリティ設定を適用できます。

Azure Resource Manager テンプレートから Azure の仮想マシンを作成する方法:

https://docs.microsoft.com/azure/virtual-machines/windows/ps-template

Azure Virtual Machines の Desired State Configuration を理解する:

https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-overview

## <a name="75-securely-store-configuration-of-azure-resources"></a>7.5:Azure リソースの構成を安全に格納する

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 7.5 | 5.3 | Customer |

カスタム Azure ポリシー定義を使用する場合は、Azure DevOps または Azure Repos を使ってコードを安全に格納して管理します。

Azure DevOps にコードを格納する方法:

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Azure Repos のドキュメント:

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

## <a name="76-securely-store-custom-operating-system-images"></a>7.6:カスタム オペレーティング システム イメージを安全に格納する

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 7.6 | 5.3 | Customer |

カスタム イメージを使用する場合は、RBAC を使用して、承認されたユーザーのみがイメージにアクセスできるようにします。 コンテナー イメージの場合は、Azure Container Registry に保存し、RBAC を利用して、承認されたユーザーだけがイメージにアクセスできるようにします。

Azure での RBAC を理解する:

https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles

Container Registry の RBAC を理解する:

https://docs.microsoft.com/azure/container-registry/container-registry-roles

Azure で RBAC を構成する方法:

https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal

## <a name="77-deploy-system-configuration-management-tools"></a>7.7:システム構成管理ツールをデプロイする

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 7.7 | 5.4 | Customer |

Azure Policy を使用して、システム構成をアラート、監査、および適用します。 さらに、ポリシー例外を管理するためのプロセスとパイプラインを作成します。

Azure Policy を構成して管理する方法:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

## <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8:オペレーティング システム用のシステム構成管理ツールをデプロイする

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 7.8 | 5.4 | Customer |

Windows コンピューティング用の PowerShell Desired State Configuration や Linux 用の Linux Chef 拡張機能などの Azure コンピューティング拡張機能を使用します。

Azure に仮想マシン拡張機能をインストールする方法:

https://docs.microsoft.com/azure/virtual-machines/extensions/overview#how-can-i-install-an-extension

## <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9:Azure サービスの自動構成監視を実装する

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 7.9 | 5.5 | Customer |

Azure Security Center を使用して、Azure リソースのベースライン スキャンを実行する

Azure Security Center の推奨事項を修復する方法

https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

## <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10:オペレーティング システムの自動構成監視を実装する

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 7.1 | 5.5 | Customer |

Azure Security Center を使用して、コンテナーの OS と Docker の設定のベースライン スキャンを実行します。

Azure Security Center のコンテナーの推奨事項を理解する:

https://docs.microsoft.com/azure/security-center/security-center-container-recommendations

## <a name="711-manage-azure-secrets-securely"></a>7.11:Azure シークレットを安全に管理する 

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 7.11 | 13.1 | Customer |

マネージド サービス ID を Azure Key Vault と組み合わせて使用し、クラウド アプリケーションのシークレット管理を簡素化し、セキュリティで保護します。

Azure マネージド ID と統合する方法:

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

キー コンテナーを作成する方法:

https://docs.microsoft.com/azure/key-vault/quick-create-portal

マネージド ID で Key Vault の認証を提供する方法:

https://docs.microsoft.com/azure/key-vault/managed-identity

## <a name="712-manage-identities-securely-and-automatically"></a>7.12:ID を安全かつ自動的に管理する

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 7.12 | 4.1 | Customer |

マネージド ID を使用して、Azure AD で自動的に管理される ID を Azure サービスに提供します。 マネージド ID を使用すると、コードに資格情報を追加しなくても、Azure AD の認証をサポートするさまざまなサービス (Key Vault を含む) に対して認証を行うことができます。

マネージド ID を構成する方法:

https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

## <a name="713-eliminate-unintended-credential-exposure"></a>7.13:意図しない資格情報の公開を排除する

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 7.13 | 13.3 | Customer |

コード内で資格情報を特定する資格情報スキャナーを実装します。 また、資格情報スキャナーを使うと、検出された資格情報を、Azure Key Vault などのより安全な場所に移動しやすくなります。 

資格情報スキャナーを設定する方法:

https://secdevtools.azurewebsites.net/helpcredscan.html

## <a name="next-steps"></a>次のステップ

次のセキュリティ コントロールを参照してください。[マルウェアからの防御](security-control-malware-defense.md)
