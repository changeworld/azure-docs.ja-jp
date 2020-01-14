---
title: Azure Security Center でサポートされている使用可能な機能 | Microsoft Docs
description: このドキュメントでは、Azure Security Center でサポートされるサービスの一覧を示します。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 870ebc8d-1fad-435b-9bf9-c477f472ab17
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/05/2020
ms.author: memildin
ms.openlocfilehash: 6325af9f577fbee1ab1621acbf10497fab2b78df
ms.sourcegitcommit: 2f8ff235b1456ccfd527e07d55149e0c0f0647cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/07/2020
ms.locfileid: "75689157"
---
# <a name="supported-features-available-in-azure-security-center"></a>Azure Security Center でサポートされている使用可能な機能

> [!NOTE]
>一部の機能は、Standard レベルでのみ使用できます。 Security Center の Standard レベルにまだサインアップしていない場合は、無料試用期間を利用できます。 詳細については、「[Security Center の価格](https://azure.microsoft.com/pricing/details/security-center/)」を参照してください。

以下のセクションでは、[サポートされているプラットフォーム](security-center-os-coverage.md)で使用できる Security Center の機能を示します。

* [仮想マシン/サーバー](#vm-server-features)
* [PaaS サービス](#paas-services)


## <a name="vm-server-features"></a>仮想マシン/サーバーでサポートされる機能

### <a name="windowstabfeatures-windows"></a>[Windows](#tab/features-windows)

|||||||||
|----|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
||**Azure Virtual Machines**|**Azure Virtual Machine Scale Sets**|**Azure 以外のマシン**|**料金**
|[Microsoft Defender ATP の統合](security-center-wdatp.md)|✔ (サポートされているバージョン)|✔ (サポートされているバージョン)|✔|Standard|
|[仮想マシン動作分析の脅威検出アラート](security-center-alerts-iaas.md)|✔|✔|✔|推奨事項 (無料) 脅威の検出 (Standard)|
|[ファイルレス脅威の検出アラート](alerts-reference.md#alerts-filelessattackdetect)|✔|✔|✔|Standard|
|[ネットワーク経由の脅威検出アラート](security-center-alerts-service-layer.md#azure-network-layer)|✔|✔|-|Standard|
|[Just-In-Time VM アクセス](security-center-just-in-time.md)|✔|-|-|Standard|
|[ネイティブ脆弱性評価](built-in-vulnerability-assessment.md)|✔|-|-|Standard|
|[ファイルの整合性の監視](security-center-file-integrity-monitoring.md)|✔|✔|✔|Standard|
|[アダプティブ アプリケーション制御](security-center-adaptive-application.md)|✔|-|✔|Standard|
|[ネットワーク マップ](security-center-network-recommendations.md#network-map)|✔|✔|-|Standard|
|[アダプティブ ネットワークのセキュリティ強化機能](security-center-adaptive-network-hardening.md)|✔|-|-|Standard|
|アダプティブ ネットワーク制御|✔|✔|-|Standard|
|[規制へのコンプライアンス ダッシュボードおよびレポート](security-center-compliance-dashboard.md)|✔|✔|✔|Standard|
|Docker でホストされている IaaS コンテナーに対する推奨事項と脅威の検出|-|-|-|Standard|
|足りない OS パッチの評価|✔|✔|✔|無料|
|セキュリティの誤った構成の評価|✔|✔|✔|無料|
|[エンドポイント保護の評価](security-center-services.md#supported-endpoint-protection-solutions-)|✔|✔|✔|無料|
|ディスク暗号化の評価|✔|✔|-|無料|
|サードパーティの脆弱性評価|✔|-|-|無料|
|[ネットワーク セキュリティ評価](security-center-network-recommendations.md)|✔|✔|-|無料|


### <a name="linuxtabfeatures-linux"></a>[Linux](#tab/features-linux)

|||||||||
|----|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
||**Azure Virtual Machines**|**Azure Virtual Machine Scale Sets**|**Azure 以外のマシン**|**料金**
|[Microsoft Defender ATP の統合](security-center-wdatp.md)|-|-|-|Standard|
|[仮想マシン動作分析の脅威検出アラート](security-center-alerts-iaas.md)|✔ (サポートされているバージョン)|✔ (サポートされているバージョン)|✔|推奨事項 (無料) 脅威の検出 (Standard)|
|[ファイルレス脅威の検出アラート](alerts-reference.md#alerts-filelessattackdetect)|-|-|-|Standard|
|[ネットワーク経由の脅威検出アラート](security-center-alerts-service-layer.md#azure-network-layer)|✔|✔|-|Standard|
|[Just-In-Time VM アクセス](security-center-just-in-time.md)|✔|-|-|Standard|
|[ネイティブ脆弱性評価](built-in-vulnerability-assessment.md)|✔|-|-|Standard|
|[ファイルの整合性の監視](security-center-file-integrity-monitoring.md)|✔|✔|✔|Standard|
|[アダプティブ アプリケーション制御](security-center-adaptive-application.md)|✔|-|✔|Standard|
|[ネットワーク マップ](security-center-network-recommendations.md#network-map)|✔|✔|-|Standard|
|[アダプティブ ネットワークのセキュリティ強化機能](security-center-adaptive-network-hardening.md)|✔|-|-|Standard|
|アダプティブ ネットワーク制御|✔|✔|-|Standard|
|[規制へのコンプライアンス ダッシュボードおよびレポート](security-center-compliance-dashboard.md)|✔|✔|✔|Standard|
|Docker でホストされている IaaS コンテナーに対する推奨事項と脅威の検出|✔|✔|✔|Standard|
|足りない OS パッチの評価|✔|✔|✔|無料|
|セキュリティの誤った構成の評価|✔|✔|✔|無料|
|[エンドポイント保護の評価](security-center-services.md#supported-endpoint-protection-solutions-)|-|-|-|無料|
|ディスク暗号化の評価|✔|✔|-|無料|
|サードパーティの脆弱性評価|✔|-|-|無料|
|[ネットワーク セキュリティ評価](security-center-network-recommendations.md)|✔|✔|-|無料|

--- 

## サポートされているエンドポイント保護ソリューション <a name="endpoint-supported"></a>

次の表に、次の項目のマトリックスを示します。

 - Azure Security Center を使用して、各ソリューションをインストールできるかどうか。
 - Security Center で検出できるエンドポイント保護ソリューションはどれか。 この一覧からのエンドポイント保護ソリューションが検出された場合、Security Center で 1 つをインストールすることが推奨されることはありません。

これらの各保護に対する推奨事項がいつ生成されるかの情報については、[エンドポイント保護の評価と推奨事項](security-center-endpoint-protection.md)に関するページを参照してください。

| エンドポイント保護| プラットフォーム | Security Center のインストール | Security Center の検出 |
|------|------|-----|-----|
| Windows Defender (Microsoft Antimalware)| Windows Server 2016| いいえ、OS に組み込まれている| はい |
| System Center Endpoint Protection (Microsoft Antimalware) | Windows Server 2012 R2、2012、2008 R2 (下記の注を参照) | 拡張機能を介して | はい |
| Trend Micro – すべてのバージョン* | Windows Server ファミリ  | いいえ | はい |
| Symantec v12.1.1100 以降| Windows Server ファミリ  | いいえ | はい |
| McAfee v10 以降 | Windows Server ファミリ  | いいえ | はい |
| McAfee v10 以降 | Linux Server ファミリ  | いいえ | はい **\*** |
| Sophos V9+| Linux Server ファミリ  | いいえ | はい  **\***  |

 **\*** カバレッジの状態とサポート データは、現在、保護されているサブスクリプションに関連付けられている Log Analytics ワークスペース内でのみ使用できます。 Azure Security Center ポータルには反映されません。

> [!NOTE]
> - Windows Server 2008 R2 の仮想マシンで System Center Endpoint Protection (SCEP) が検出されるには、PowerShell 3.0 (またはそれ以降) の後で SCEP をインストールする必要があります。
> - Trend Micro 保護の検出が Deep Security エージェント用にサポートされています。  OfficeScan エージェントはサポートされていません。


## PaaS サービスでサポートされる機能 <a name="paas-services"> </a>

Azure Security Center では、次の PaaS リソースがサポートされています。

|サービス|推奨事項 (無料)|脅威検出アラート (標準)|脆弱性評価 (標準)|
|----|:----:|:----:|:----:|
|SQL データベース|✔|✔|✔|
|Azure Container Registry|-|-|✔|
|Azure Kubernetes Service|✔|✔|-|
|Azure Database for PostgreSQL*|✔|✔|-|
|Azure Database for MySQL*|✔|✔|-|
|Azure CosmosDB*|-|✔|-|
|ストレージ アカウント|✔|-|-|
|Blob Storage|✔|✔|-|
|App Service|✔|✔|-|
|関数アプリ|✔|-|-|
|Cloud Services|✔|-|-|
|Virtual Network|✔|-|-|
|Subnet|✔|-|-|
|NIC|✔|-|-|
|ネットワーク セキュリティ グループ|✔|-|-|
|サブスクリプション|✔ **|✔|-|
|Batch アカウント|✔|-|-|
|Service Fabric アカウント|✔|-|-|
|Automation アカウント|✔|-|-|
|Load Balancer|✔|-|-|
|Cognitive Search|✔|-|-|
|Service Bus 名前空間|✔|-|-|
|Stream Analytics|✔|-|-|
|イベント ハブの名前空間|✔|-|-|
|ロジック アプリ|✔|-|-|
|Cache for Redis|✔|-|-|
|Data Lake Analytics|✔|-|-|
|Azure Data Lake Storage|✔|-|-|
|Key Vault|✔|✔ *|-|

\* 現在、これらの機能はプレビューでサポートされています。

\*\* Azure Active Directory (Azure AD) のレコメンデーションは、Standard サブスクリプションでのみ使用できます。

## <a name="next-steps"></a>次のステップ

- [Security Center によるデータの収集方法と Log Analytics エージェント](security-center-enable-data-collection.md)について確認します。
- [Security Center でデータを管理および保護する](security-center-data-security.md)方法を確認します。
- [Azure Security Center を導入するための設計上の考慮事項を計画し、理解する](security-center-planning-and-operations-guide.md)方法について説明しています。
- [Security Center がサポートされているプラットフォーム](security-center-os-coverage.md)を確認します。
- [Azure Security Center での VM と サーバーの脅威検出](security-center-alerts-iaas.md)の詳細を確認します。
- [Azure Security Center の使用に関してよく寄せられる質問](security-center-faq.md)が記載されています。
- [Azure のセキュリティとコンプライアンスについてのブログ記事](https://blogs.msdn.com/b/azuresecurity/)を確認できます。