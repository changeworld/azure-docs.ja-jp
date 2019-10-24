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
ms.date: 09/24/2019
ms.author: memildin
ms.openlocfilehash: 275598aa50c252512348f4a04543e1beaf538626
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/17/2019
ms.locfileid: "72529487"
---
# <a name="supported-features-available-in-azure-security-center"></a>Azure Security Center でサポートされている使用可能な機能

> [!NOTE]
>一部の機能は、Standard レベルでのみ使用できます。 Security Center の Standard レベルにまだサインアップしていない場合は、無料試用期間を利用できます。 詳細については、「[Security Center の価格](https://azure.microsoft.com/pricing/details/security-center/)」を参照してください。

以下のセクションでは、[サポートされているプラットフォーム](security-center-os-coverage.md)で使用できる Security Center の機能を示します。

* [仮想マシン/サーバー](#vm-server-features)
* [PaaS サービス](#paas-services)


## <a name="vm-server-features"></a>仮想マシン/サーバーでサポートされる機能

> [!div class="mx-tableFixed"]

|サーバー|Windows|||Linux|||価格|
|----|----|----|----|----|----|----|----|
|**Environment**|**Azure**||**Azure 以外**|**Azure**||**Azure 以外**||
||**仮想マシン**|**Virtual Machine Scale Set**||**仮想マシン**|**Virtual Machine Scale Set**|
|[Microsoft Defender ATP の統合](https://docs.microsoft.com/azure/security-center/security-center-wdatp)|✔ (サポートされているバージョン)|✔ (サポートされているバージョン)|✔|X|X|X|Standard|
|[仮想マシン動作分析の脅威検出アラート](https://docs.microsoft.com/azure/security-center/security-center-alerts-iaas)|✔|✔|✔|✔ (サポートされているバージョン)|✔ (サポートされているバージョン)|✔|推奨事項 (無料) 脅威の検出 (Standard)|
|[ファイルレス脅威の検出アラート](https://docs.microsoft.com/azure/security-center/security-center-alerts-iaas#fileless-attack-detection-)|✔|✔|✔|X|X|X|Standard|
|[ネットワーク経由の脅威検出アラート](https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer#azure-network-layer)|✔|✔|X|✔|✔|X|Standard|
|[Just-In-Time VM アクセス](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)|✔|X|X|✔|X|X|Standard|
|[ファイルの整合性の監視](https://docs.microsoft.com/azure/security-center/security-center-file-integrity-monitoring)|✔|✔|✔|✔|✔|✔|Standard|
|[アダプティブ アプリケーション制御](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)|✔|X|✔|✔|X|✔|Standard|
|[ネットワーク マップ](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations#network-map)|✔|✔|X|✔|✔|X|Standard|
|[アダプティブ ネットワークのセキュリティ強化機能](https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening)|✔|X|X|✔|X|X|Standard|
|アダプティブ ネットワーク制御|✔|✔|X|✔|✔|X|Standard|
|[規制へのコンプライアンス ダッシュボードおよびレポート](https://docs.microsoft.com/azure/security-center/security-center-compliance-dashboard)|✔|✔|✔|✔|✔|✔|Standard|
|Docker でホストされている IaaS コンテナーに対する推奨事項と脅威の検出|X|X|X|✔|✔|✔|Standard|
|足りない OS パッチの評価|✔|✔|✔|✔|✔|✔|無料|
|セキュリティの誤った構成の評価|✔|✔|✔|✔|✔|✔|無料|
|[エンドポイント保護の評価](https://docs.microsoft.com/azure/security-center/security-center-services#supported-endpoint-protection-solutions-)|✔|✔|✔|X|X|X|無料|
|ディスク暗号化の評価|✔|✔|X|✔|✔|X|無料|
|サードパーティの脆弱性評価|✔|X|X|✔|X|X|無料|
|[ネットワーク セキュリティ評価](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)|✔|✔|X|✔|✔|X|無料|

### サポートされているエンドポイント保護ソリューション <a name="endpoint-supported"></a>

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
>
> - Windows Server 2008 R2 の仮想マシンで System Center Endpoint Protection (SCEP) が検出されるには、PowerShell 3.0 (またはそれ以降) の後で SCEP をインストールする必要があります。
> - Trend Micro 保護の検出が Deep Security エージェント用にサポートされています。  OfficeScan エージェントはサポートされていません。


## PaaS サービスでサポートされる機能 <a name="paas-services"> </a>

Azure Security Center では、次の PaaS リソースがサポートされています。

|Service|推奨事項 (無料)|脅威検出 (Standard)|
|----|----|----|
|SQL|✔| ✔|
|PostGreSQL*|✔| ✔|
|MySQL*|✔| ✔|
|CosmosDB*|X| ✔|
|BLOB ストレージ|✔| ✔|
|ストレージ アカウント|✔| X|
|App Service|✔| ✔|
|Function|✔| X|
|クラウド サービス|✔| X|
|VNet|✔| X|
|Subnet|✔| X|
|NIC|✔| X|
|NSG|✔| X|
|Subscription|✔ **| ✔|
|Batch アカウント|✔| X|
|Service Fabric アカウント|✔| X|
|Automation アカウント|✔| X|
|Load Balancer|✔| X|
|Search|✔| X|
|Service Bus 名前空間|✔| X|
|Stream Analytics|✔| X|
|イベント ハブの名前空間|✔| X|
|ロジック アプリ|✔| X|
|Redis|✔| X|
|Data Lake Analytics|✔| X|
|Data Lake Store|✔| X|
|Key Vault|✔| X|

\* 現在、これらの機能はパブリック プレビューでサポートされています。

\*\* Azure Active Directory (Azure AD) のレコメンデーションは、Standard サブスクリプションでのみ使用できます。

## <a name="next-steps"></a>次の手順

- [Security Center によるデータの収集方法と Log Analytics エージェント](security-center-enable-data-collection.md)について確認します。
- [Security Center でデータを管理および保護する](security-center-data-security.md)方法を確認します。
- [Azure Security Center を導入するための設計上の考慮事項を計画し、理解する](security-center-planning-and-operations-guide.md)方法について説明しています。
- [Security Center がサポートされているプラットフォーム](security-center-os-coverage.md)を確認します。
- [Azure Security Center での VM と サーバーの脅威検出](security-center-alerts-iaas.md)の詳細を確認します。
- [Azure Security Center の使用に関してよく寄せられる質問](security-center-faq.md)が記載されています。
- [Azure のセキュリティとコンプライアンスについてのブログ記事](https://blogs.msdn.com/b/azuresecurity/)を確認できます。
