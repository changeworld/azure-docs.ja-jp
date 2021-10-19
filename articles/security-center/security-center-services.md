---
title: OS、マシンの種類、クラウドに応じた Azure Security Center の機能
description: OS、マシンの種類、クラウドのデプロイに応じて使用できる Azure Security Center の機能について説明します。
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 08/18/2021
ms.custom: references_regions
ms.author: memildin
ms.openlocfilehash: 21c453da9a2e84e4e936b92f04748854cbd928d3
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2021
ms.locfileid: "129710746"
---
# <a name="feature-coverage-for-machines"></a>マシンを対象とする機能

下の 2 つのタブは、Windows および Linux マシンで使用できる Azure Security Center の機能を示しています。

## <a name="supported-features-for-virtual-machines-and-servers"></a>仮想マシンとサーバーでサポートされている機能 <a name="vm-server-features"></a>

### <a name="windows-machines"></a>[**Windows マシン**](#tab/features-windows)

|**機能**|**Azure Virtual Machines**|**Azure Virtual Machine Scale Sets**|**Azure Arc 対応マシン**|**Azure Defender が必要**
|----|:----:|:----:|:----:|:----:|
|[Microsoft Defender for Endpoint 統合](security-center-wdatp.md)|✔</br>(サポートされているバージョン)|✔</br>(サポートされているバージョン)|✔|はい|
|[仮想マシン行動分析 (およびセキュリティ アラート)](alerts-reference.md)|✔|✔|✔|はい|
|[ファイルレスのセキュリティ アラート](alerts-reference.md#alerts-windows)|✔|✔|✔|はい|
|[ネットワーク ベースのセキュリティ アラート](other-threat-protections.md#network-layer)|✔|✔|-|はい|
|[Just In Time VM アクセス](security-center-just-in-time.md)|✔|-|-|はい|
|[統合された Qualys 脆弱性スキャナー](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)|✔|-|✔|はい|
|[ファイルの整合性の監視](security-center-file-integrity-monitoring.md)|✔|✔|✔|はい|
|[アダプティブ アプリケーション制御](security-center-adaptive-application.md)|✔|-|✔|はい|
|[ネットワーク マップ](security-center-network-recommendations.md#network-map)|✔|✔|-|はい|
|[アダプティブ ネットワークのセキュリティ強化機能](security-center-adaptive-network-hardening.md)|✔|-|-|はい|
|[規制へのコンプライアンス ダッシュボードおよびレポート](security-center-compliance-dashboard.md)|✔|✔|✔|はい|
|[Docker ホストのセキュリティ強化機能](./harden-docker-hosts.md)|-|-|-|はい|
|足りない OS パッチの評価|✔|✔|✔|Azure: いいえ<br><br>Azure Arc 対応: はい|
|セキュリティの誤った構成の評価|✔|✔|✔|Azure: いいえ<br><br>Azure Arc 対応: はい|
|[エンドポイント保護の評価](security-center-services.md#supported-endpoint-protection-solutions-)|✔|✔|✔|Azure: いいえ<br><br>Azure Arc 対応: はい|
|ディスク暗号化の評価|✔</br>([サポートされるシナリオ](../virtual-machines/windows/disk-encryption-windows.md#unsupported-scenarios)の場合)|✔|-|いいえ|
|サードパーティの脆弱性評価|✔|-|✔|いいえ|
|[ネットワーク セキュリティ評価](security-center-network-recommendations.md)|✔|✔|-|いいえ|
||||||

### <a name="linux-machines"></a>[**Linux マシン**](#tab/features-linux)

| **機能**                                                                                                               | **Azure Virtual Machines**                                                                                      | **Azure Virtual Machine Scale Sets** | **Azure Arc 対応マシン** | **Azure Defender が必要**       |
|---------------------------------------------------------------------------------------------------------------------------|:---------------------------------------------------------------------------------------------------------------:|:------------------------------------:|:------------------------------:|:---------------------------------:|
| [Microsoft Defender for Endpoint 統合](security-center-wdatp.md)                                                   | ✔                                                                                                               | -                                    | ✔                              | はい                              |
| [仮想マシン行動分析 (およびセキュリティ アラート)](./azure-defender.md)                                         | ✔</br>(サポートされているバージョン)                                                                                  | ✔</br>(サポートされているバージョン)        | ✔                             | はい                               |
| [ファイルレスのセキュリティ アラート](alerts-reference.md#alerts-windows)                                                            | -                                                                                                               | -                                    | -                              | はい                               |
| [ネットワーク ベースのセキュリティ アラート](other-threat-protections.md#network-layer)                                                | ✔                                                                                                              | ✔                                    | -                              | はい                               |
| [Just In Time VM アクセス](security-center-just-in-time.md)                                                                 | ✔                                                                                                              | -                                    | -                              | はい                               |
| [統合された Qualys 脆弱性スキャナー](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner) | ✔                                                                                                              | -                                    | ✔                             | はい                               |
| [ファイルの整合性の監視](security-center-file-integrity-monitoring.md)                                                 | ✔                                                                                                              | ✔                                    | ✔                             | はい                               |
| [アダプティブ アプリケーション制御](security-center-adaptive-application.md)                                                  | ✔                                                                                                              | -                                    | ✔                             | はい                               |
| [ネットワーク マップ](security-center-network-recommendations.md#network-map)                                                     | ✔                                                                                                              | ✔                                    | -                              | はい                               |
| [アダプティブ ネットワークのセキュリティ強化機能](security-center-adaptive-network-hardening.md)                                               | ✔                                                                                                              | -                                    | -                              | はい                               |
| [規制へのコンプライアンス ダッシュボードおよびレポート](security-center-compliance-dashboard.md)                                      | ✔                                                                                                              | ✔                                    | ✔                             | はい                               |
| [Docker ホストのセキュリティ強化機能](./harden-docker-hosts.md)                                                                         | ✔                                                                                                              | ✔                                    | ✔                             | はい                               |
| 足りない OS パッチの評価                                                                                             | ✔                                                                                                              | ✔                                    | ✔                             | Azure: いいえ<br><br>Azure Arc 対応: はい |
| セキュリティの誤った構成の評価                                                                                     | ✔                                                                                                              | ✔                                    | ✔                             | Azure: いいえ<br><br>Azure Arc 対応: はい |
| [エンドポイント保護の評価](security-center-services.md#supported-endpoint-protection-solutions-)                    | -                                                                                                               | -                                    | -                              | いいえ                                |
| ディスク暗号化の評価                                                                                                | ✔</br>([サポートされるシナリオ](../virtual-machines/windows/disk-encryption-windows.md#unsupported-scenarios)の場合) | ✔                                    | -                              | いいえ                                |
| サードパーティの脆弱性評価                                                                                      | ✔                                                                                                              | -                                    | ✔                             | いいえ                                |
| [ネットワーク セキュリティ評価](security-center-network-recommendations.md)                                                 | ✔                                                                                                              | ✔                                    | -                              | いいえ                                |
||||||

--- 


> [!TIP]
>Azure Defender でのみ利用可能な機能を試すために、30 日間の試用版に登録できます。 詳細については、 [価格に関するページ](https://azure.microsoft.com/pricing/details/security-center/)を参照してください。


## <a name="supported-endpoint-protection-solutions"></a>サポートされているエンドポイント保護ソリューション <a name="endpoint-supported"></a>

次の表に、次の項目のマトリックスを示します。

 - Azure Security Center を使用して、各ソリューションをインストールできるかどうか。
 - Security Center で検出できるエンドポイント保護ソリューションはどれか。 この一覧からのエンドポイント保護ソリューションが検出された場合、Security Center で 1 つをインストールすることが推奨されることはありません。

これらの各保護に対する推奨事項がいつ生成されるかの情報については、[エンドポイント保護の評価と推奨事項](security-center-endpoint-protection.md)に関するページを参照してください。

| ソリューション                                                  | サポートされているプラットフォーム                                    | Security Center のインストール | Security Center の検出 |
|-----------------------------------------------------------|--------------------------------------------------------|------------------------------|---------------------------|
| Microsoft Defender ウイルス対策                              | Windows Server 2016 以降                           | いいえ (OS に組み込み)           | はい                       |
| System Center Endpoint Protection (Microsoft Antimalware) | Windows Server 2012 R2                                 | 拡張機能経由                | はい                       |
| Trend Micro – Deep Security                               | Windows Server (すべて)                                   | いいえ                           | はい                       |
| Symantec v12.1.1100 以降                                      | Windows Server (すべて)                                   | いいえ                           | はい                       |
| McAfee v10 以降                                               | Windows Server (すべて)                                   | いいえ                           | はい                       |
| McAfee v10 以降                                               | Linux (プレビュー)                                        | いいえ                           | はい                       |
| Sophos V9+                                                | Linux (プレビュー)                                        | いいえ                           | はい                       |
|                                                           |                                                        |                              |                           |



## <a name="feature-support-in-government-and-sovereign-clouds"></a>政府機関クラウドとソブリン クラウドでの機能のサポート

| 機能とサービス                                                                                                                                           | Azure          | Azure Government               | Azure China 21Vianet   |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------|----------------|--------------------------------|---------------|
| **Security Center の無料機能**                                                                                                                         |                |                                |               |
| - [連続エクスポート](./continuous-export.md)                                                                                                             | GA             | GA                             | GA            |
| - [ワークフローの自動化](./continuous-export.md)                                                                                                           | GA             | GA                             | GA            |
| - [推奨事項の除外規則](./exempt-resource.md)                                                                                                  | パブリック プレビュー | 利用不可                  | 利用不可 |
| - [アラートの抑制ルール](./alerts-suppression-rules.md)                                                                                                | GA             | GA                             | GA            |
| - [セキュリティ アラートのメール通知](./security-center-provide-security-contact-details.md)                                                        | GA             | GA                             | GA            |
| - [エージェントと拡張機能の自動プロビジョニング](./security-center-enable-data-collection.md)                                                              | GA             | GA                             | GA            |
| - [資産インベントリ](./asset-inventory.md)                                                                                                                 | GA             | GA                             | GA            |
| - [Azure Security Center のブック ギャラリーの Azure Monitor ブック レポート](./custom-dashboards-azure-workbooks.md)                                  | GA             | GA                             | GA            |
| **Azure Defender のプランと拡張機能**                                                                                                                   |                |                                |               |
| - [Azure Defender for servers](./defender-for-servers-introduction.md)                                                                                    | GA             | GA                             | GA            |
| - [Azure Defender for App Service](./defender-for-app-service-introduction.md)                                                                            | GA             | 利用不可                  | 利用不可 |
| - [Azure Defender for DNS](./defender-for-dns-introduction.md)                                                                                            | GA             | GA                             | GA            |
| - [コンテナー レジストリ用 Azure Defender](./defender-for-container-registries-introduction.md) <sup>[1](#footnote1)</sup>                               | GA             | GA  <sup>[2](#footnote2)</sup> | GA  <sup>[2](#footnote2)</sup> |
| - [CI/CD ワークフロー内の画像をスキャンするコンテナー レジストリ用 Azure Defender](./defender-for-container-registries-cicd.md) <sup>[3](#footnote3)</sup> | パブリック プレビュー | 利用不可                  | 利用不可 |
| - [Azure Defender for Kubernetes](./defender-for-kubernetes-introduction.md) <sup>[4](#footnote4)</sup>                                                   | GA             | GA                             | GA            |
| - [Azure Arc 対応 Kubernetes クラスター用 Azure Defender 拡張機能](./defender-for-kubernetes-azure-arc.md) <sup>[5](#footnote5)</sup>                 | パブリック プレビュー | 利用不可                  | 利用不可 |
| - [Azure SQL データベース サーバー用 Azure Defender](./defender-for-sql-introduction.md)                                                                     | GA             | GA                             | GA  <sup>[9](#footnote9)</sup> |
| - [Azure Defender for SQL servers on machines](./defender-for-sql-introduction.md)                                                                        | GA             | GA                             | 利用不可 |
| - [オープンソース リレーショナル データベース用 Azure Defender](./defender-for-databases-introduction.md)                                                         | GA             | 利用不可                  | 利用不可 |
| - [Azure Defender for Key Vault](./defender-for-key-vault-introduction.md)                                                                                | GA             | 利用不可                  | 利用不可 |
| - [Azure Defender for Resource Manager](./defender-for-resource-manager-introduction.md)                                                                  | GA             | GA                             | GA            |
| - [Azure Defender for Storage](./defender-for-storage-introduction.md) <sup>[6](#footnote6)</sup>                                                         | GA             | GA                             | 利用不可 |
| - [Threat protection for Cosmos DB](./other-threat-protections.md#threat-protection-for-azure-cosmos-db-preview)                                          | パブリック プレビュー | 利用不可                  | 利用不可 |
| - [Kubernetes ワークロード保護](./kubernetes-workload-protections.md)                                                                                  | GA             | GA                             | GA            |
| - [Sentinel との双方向アラート同期](../sentinel/connect-azure-security-center.md)                                                      | パブリック プレビュー | 利用不可                  | 利用不可 |
| **Azure Defender for servers features** <sup>[7](#footnote7)</sup>                                                                                        |                |                                |               |
| - [Just-In-Time VM アクセス](./security-center-just-in-time.md)                                                                                             | GA             | GA                             | GA            |
| - [ファイルの整合性の監視](./security-center-file-integrity-monitoring.md)                                                                             | GA             | GA                             | GA            |
| - [適応型アプリケーション制御](./security-center-adaptive-application.md)                                                                              | GA             | GA                             | GA            |
| - [アダプティブ ネットワークのセキュリティ強化機能](./security-center-adaptive-network-hardening.md)                                                                           | GA             | 利用不可                  | 利用不可 |
| - [Docker ホストのセキュリティ強化機能](./harden-docker-hosts.md)                                                                                                       | GA             | GA                             | GA            |
| - [統合された Qualys 脆弱性スキャナー](./deploy-vulnerability-assessment-vm.md)                                                             | GA             | 利用不可                  | 利用不可 |
| - [規制へのコンプライアンス ダッシュボードおよびレポート](./security-center-compliance-dashboard.md) <sup>[8](#footnote8)</sup>                                       | GA             | GA                             | GA            |
| - [Microsoft Defender for Endpoint のデプロイと統合ライセンス](./security-center-wdatp.md)                                                         | GA             | GA                             | 利用不可 |
| - [AWS アカウントを接続する](./quickstart-onboard-aws.md)                                                                                                      | GA             | 利用不可                  | 利用不可 |
| - [GCP アカウントを接続する](./quickstart-onboard-gcp.md)                                                                                                      | GA             | 利用不可                  | 利用不可 |
|                                                                                                                                                           |                |                                |               |

<sup><a name="footnote1" /></a>1</sup> 部分的に GA: 脆弱性スキャンで発見された特定の項目を無効にする機能はパブリック プレビュー段階です。

<sup><a name="footnote2" /></a>2</sup> Azure Government 上のコンテナー レジストリの脆弱性スキャンは、スキャン オン プッシュ機能でのみ実行できます。

<sup><a name="footnote3" /></a>3</sup> コンテナー レジストリ用 Azure Defender が必要です。

<sup><a name="footnote4" /></a>4</sup> 部分的に GA: Azure Arc 対応クラスターのサポートはパブリック プレビュー段階であり、Azure Government では使用できません。

<sup><a name="footnote5" /></a>5</sup> Azure Defender for Kubernetes が必要です。

<sup><a name="footnote6" /></a>6</sup> 部分的に GA: Azure Defender for Storage の脅威保護アラートの一部はパブリック プレビュー段階です。

<sup><a name="footnote7" /></a>7</sup> これらの機能には、すべて [Azure Defender for servers](./defender-for-servers-introduction.md) が必要です。

<sup><a name="footnote8" /></a>8</sup> クラウドの種類ごとに提供される標準が異なる場合があります。
 
<sup><a name="footnote9" /></a>9</sup> 部分的に GA: SQL サーバーのアラートと脆弱性評価のサブセット。 Behavioral Threat Protection は使用できません。

## <a name="next-steps"></a>次のステップ

- [Security Center による Log Analytics エージェントを使用したデータの収集](security-center-enable-data-collection.md)について確認します。
- [Security Center でデータを管理および保護する](security-center-data-security.md)方法を確認します。
- [Security Center がサポートされているプラットフォーム](security-center-os-coverage.md)を確認します。
