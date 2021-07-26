---
title: OS、マシンの種類、クラウドに応じた Azure Security Center の機能
description: OS、種類、クラウドのデプロイに応じて使用できる Azure Security Center の機能について説明します。
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 06/17/2021
ms.custom: references_regions
ms.author: memildin
ms.openlocfilehash: 05b535a0aabe0108c0a9abcedef166293541d407
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2021
ms.locfileid: "113005476"
---
# <a name="feature-coverage-for-machines"></a>マシンを対象とする機能

以下の 2 つのタブは、Windows および Linux の仮想マシンとサーバーで使用できる Azure Security Center の機能を示しています。

## <a name="supported-features-for-virtual-machines-and-servers"></a>仮想マシンとサーバーでサポートされている機能 <a name="vm-server-features"></a>

### <a name="windows-machines"></a>[**Windows マシン**](#tab/features-windows)

|**機能**|**Azure Virtual Machines**|**Azure Virtual Machine Scale Sets**|**Azure Arc 対応マシン**|**Azure Defender が必要**
|----|:----:|:----:|:----:|:----:|
|[Microsoft Defender for Endpoint 統合](security-center-wdatp.md)|✔</br>(サポートされているバージョン)|✔</br>(サポートされているバージョン)|✔|はい|
|[仮想マシン行動分析 (およびセキュリティ アラート)](alerts-reference.md)|✔|✔|✔|はい|
|[ファイルレスのセキュリティ アラート](alerts-reference.md#alerts-windows)|✔|✔|✔|はい|
|[ネットワーク ベースのセキュリティ アラート](other-threat-protections.md#network-layer)|✔|✔|-|はい|
|[Just In Time VM アクセス](security-center-just-in-time.md)|✔|-|-|はい|
|[ネイティブ脆弱性評価](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)|✔|-|✔|はい|
|[ファイルの整合性の監視](security-center-file-integrity-monitoring.md)|✔|✔|✔|はい|
|[アダプティブ アプリケーション制御](security-center-adaptive-application.md)|✔|-|✔|はい|
|[ネットワーク マップ](security-center-network-recommendations.md#network-map)|✔|✔|-|はい|
|[アダプティブ ネットワークのセキュリティ強化機能](security-center-adaptive-network-hardening.md)|✔|-|-|はい|
|[規制へのコンプライアンス ダッシュボードおよびレポート](security-center-compliance-dashboard.md)|✔|✔|✔|はい|
|Docker でホストされている IaaS コンテナーに対する推奨事項と脅威の防止|-|-|-|はい|
|足りない OS パッチの評価|✔|✔|✔|Azure: いいえ<br><br>Arc 対応:はい|
|セキュリティの誤った構成の評価|✔|✔|✔|Azure: いいえ<br><br>Arc 対応:はい|
|[エンドポイント保護の評価](security-center-services.md#supported-endpoint-protection-solutions-)|✔|✔|✔|Azure: いいえ<br><br>Arc 対応:はい|
|ディスク暗号化の評価|✔</br>([サポートされるシナリオ](../virtual-machines/windows/disk-encryption-windows.md#unsupported-scenarios)の場合)|✔|-|いいえ|
|サードパーティの脆弱性評価|✔|-|✔|いいえ|
|[ネットワーク セキュリティ評価](security-center-network-recommendations.md)|✔|✔|-|いいえ|


### <a name="linux-machines"></a>[**Linux マシン**](#tab/features-linux)

|**機能**|**Azure Virtual Machines**|**Azure Virtual Machine Scale Sets**|**Azure Arc 対応マシン**|**Azure Defender が必要**
|----|:----:|:----:|:----:|:----:|
|[Microsoft Defender for Endpoint 統合](security-center-wdatp.md)|-|-|-|-|
|[仮想マシン行動分析 (およびセキュリティ アラート)](./azure-defender.md)|✔</br>(サポートされているバージョン)|✔</br>(サポートされているバージョン)|✔|はい|
|[ファイルレスのセキュリティ アラート](alerts-reference.md#alerts-windows)|-|-|-|はい|
|[ネットワーク ベースのセキュリティ アラート](other-threat-protections.md#network-layer)|✔|✔|-|はい|
|[Just In Time VM アクセス](security-center-just-in-time.md)|✔|-|-|はい|
|[ネイティブ脆弱性評価](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)|✔|-|✔|はい|
|[ファイルの整合性の監視](security-center-file-integrity-monitoring.md)|✔|✔|✔|はい|
|[アダプティブ アプリケーション制御](security-center-adaptive-application.md)|✔|-|✔|はい|
|[ネットワーク マップ](security-center-network-recommendations.md#network-map)|✔|✔|-|はい|
|[アダプティブ ネットワークのセキュリティ強化機能](security-center-adaptive-network-hardening.md)|✔|-|-|はい|
|[規制へのコンプライアンス ダッシュボードおよびレポート](security-center-compliance-dashboard.md)|✔|✔|✔|はい|
|Docker でホストされている IaaS コンテナーに対する推奨事項と脅威の防止|✔|✔|✔|はい|
|足りない OS パッチの評価|✔|✔|✔|Azure: いいえ<br><br>Arc 対応:はい|
|セキュリティの誤った構成の評価|✔|✔|✔|Azure: いいえ<br><br>Arc 対応:はい|
|[エンドポイント保護の評価](security-center-services.md#supported-endpoint-protection-solutions-)|-|-|-|いいえ|
|ディスク暗号化の評価|✔</br>([サポートされるシナリオ](../virtual-machines/windows/disk-encryption-windows.md#unsupported-scenarios)の場合)|✔|-|いいえ|
|サードパーティの脆弱性評価|✔|-|✔|いいえ|
|[ネットワーク セキュリティ評価](security-center-network-recommendations.md)|✔|✔|-|いいえ|

--- 


> [!TIP]
>Azure Defender でのみ利用可能な機能を試すために、30 日間の試用版に登録できます。 詳細については、 [価格に関するページ](https://azure.microsoft.com/pricing/details/security-center/)を参照してください。


## <a name="supported-endpoint-protection-solutions"></a>サポートされているエンドポイント保護ソリューション <a name="endpoint-supported"></a>

次の表に、次の項目のマトリックスを示します。

 - Azure Security Center を使用して、各ソリューションをインストールできるかどうか。
 - Security Center で検出できるエンドポイント保護ソリューションはどれか。 この一覧からのエンドポイント保護ソリューションが検出された場合、Security Center で 1 つをインストールすることが推奨されることはありません。

これらの各保護に対する推奨事項がいつ生成されるかの情報については、[エンドポイント保護の評価と推奨事項](security-center-endpoint-protection.md)に関するページを参照してください。

| エンドポイント保護| プラットフォーム | Security Center のインストール | Security Center の検出 |
|------|------|-----|-----|
| Microsoft Defender ウイルス対策| Windows Server 2016 以降| いいえ、OS に組み込まれている| はい |
| System Center Endpoint Protection (Microsoft Antimalware) | Windows Server 2012 R2、2012、2008 R2 (下記の注を参照) | 拡張機能を介して | はい |
| Trend Micro – Deep Security | Windows Server ファミリ  | いいえ | はい |
| Symantec v12.1.1100 以降| Windows Server ファミリ  | いいえ | はい |
| McAfee v10 以降 | Windows Server ファミリ  | いいえ | はい |
| McAfee v10 以降 | Linux Server ファミリ  | いいえ | はい |
| Sophos V9+| Linux Server ファミリ  | いいえ | はい |

> [!NOTE]
> Windows Server 2008 R2 の仮想マシンで System Center Endpoint Protection (SCEP) を検出するには、PowerShell (v3.0 またはそれ以降) の後で SCEP をインストールする必要があります。



## <a name="feature-support-in-government-and-sovereign-clouds"></a>政府機関クラウドとソブリン クラウドでの機能のサポート

| 機能とサービス                                                                                                                                                             | Azure          | 米国政府                  | Azure 中国   |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------|--------------------------------|---------------|
| **Security Center の無料機能**                                                                                                                                           |                |                                |               |
| - [連続エクスポート](/azure/security-center/continuous-export)                                                                                                             | GA             | GA                             | GA            |
| - [ワークフローの自動化](/azure/security-center/continuous-export)                                                                                                           | GA             | GA                             | GA            |
| - [推奨事項の除外規則](/azure/security-center/exempt-resource)                                                                                                  | パブリック プレビュー | 利用不可                  | 利用不可 |
| - [アラートの抑制ルール](/azure/security-center/alerts-suppression-rules)                                                                                                | GA             | GA                             | GA            |
| - [セキュリティ アラートのメール通知](/azure/security-center/security-center-provide-security-contact-details)                                                        | GA             | GA                             | GA            |
| - [エージェントと拡張機能の自動プロビジョニング](/azure/security-center/security-center-enable-data-collection)                                                              | GA             | GA                             | GA            |
| - [資産インベントリ](/azure/security-center/asset-inventory)                                                                                                                 | GA             | GA                             | GA            |
| - [Azure Security Center のブック ギャラリーの Azure Monitor ブック レポート](/azure/security-center/custom-dashboards-azure-workbooks)                                  | GA             | GA                             | GA            |
| **Azure Defender のプランと拡張機能**                                                                                                                                     |                |                                |               |
| - [Azure Defender for servers](/azure/security-center/defender-for-servers-introduction)                                                                                    | GA             | GA                             | GA            |
| - [Azure Defender for App Service](/azure/security-center/defender-for-app-service-introduction)                                                                            | GA             | 利用不可                  | 利用不可 |
| - [Azure Defender for DNS](/azure/security-center/defender-for-dns-introduction)                                                                                            | GA             | 利用不可                  | 利用不可 |
| - [コンテナー レジストリ用 Azure Defender](/azure/security-center/defender-for-container-registries-introduction) <sup>[1](#footnote1)</sup>                               | GA             | GA  <sup>[2](#footnote2)</sup> | GA  <sup>[2](#footnote2)</sup> |
| - [CI/CD ワークフロー内の画像をスキャンするコンテナー レジストリ用 Azure Defender](/azure/security-center/defender-for-container-registries-cicd) <sup>[3](#footnote3)</sup> | パブリック プレビュー | 利用不可                  | 利用不可 |
| - [Azure Defender for Kubernetes](/azure/security-center/defender-for-kubernetes-introduction) <sup>[4](#footnote4)</sup>                                                   | GA             | GA                             | GA            |
| - [Azure Arc 対応 Kubernetes クラスター用 Azure Defender 拡張機能](/azure/security-center/defender-for-kubernetes-azure-arc) <sup>[5](#footnote5)</sup>                 | パブリック プレビュー | 利用不可                  | 利用不可 |
| - [Azure SQL データベース サーバー用 Azure Defender](/azure/security-center/defender-for-sql-introduction)                                                                     | GA             | GA                             | GA  <sup>[9](#footnote9)</sup> |
| - [Azure Defender for SQL servers on machines](/azure/security-center/defender-for-sql-introduction)                                                                        | GA             | GA                             | 利用不可 |
| - [オープンソース リレーショナル データベース用 Azure Defender](/azure/security-center/defender-for-databases-introduction)                                                         | GA             | 利用不可                  | 利用不可 |
| - [Azure Defender for Key Vault](/azure/security-center/defender-for-key-vault-introduction)                                                                                | GA             | 利用不可                  | 利用不可 |
| - [Azure Defender for Resource Manager](/azure/security-center/defender-for-resource-manager-introduction)                                                                  | GA             | パブリック プレビュー                 | 利用不可 |
| - [Azure Defender for Storage](/azure/security-center/defender-for-storage-introduction) <sup>[6](#footnote6)</sup>                                                         | GA             | GA                             | 利用不可 |
| - [Threat protection for Cosmos DB](/azure/security-center/other-threat-protections.md#threat-protection-for-azure-cosmos-db-preview)                                       | パブリック プレビュー | 利用不可                  | 利用不可 |
| - [Kubernetes ワークロード保護](/azure/security-center/kubernetes-workload-protections)                                                                                  | GA             | GA                             | GA            |
| **Azure Defender for servers features** <sup>[7](#footnote7)</sup>                                                                                                          |                |                                |               |
| - [Just-In-Time VM アクセス](/azure/security-center/security-center-just-in-time)                                                                                             | GA             | GA                             | GA            |
| - [ファイルの整合性の監視](/azure/security-center/security-center-file-integrity-monitoring)                                                                             | GA             | GA                             | GA            |
| - [適応型アプリケーション制御](/azure/security-center/security-center-adaptive-application)                                                                              | GA             | GA                             | GA            |
| - [アダプティブ ネットワークのセキュリティ強化機能](/azure/security-center/security-center-adaptive-network-hardening)                                                                           | GA             | 利用不可                  | 利用不可 |
| - [Docker ホストのセキュリティ強化機能](/azure/security-center/harden-docker-hosts)                                                                                                       | GA             | GA                             | GA            |
| - [マシンに対する統合された脆弱性評価](/azure/security-center/deploy-vulnerability-assessment-vm)                                                             | GA             | 利用不可                  | 利用不可 |
| - [規制へのコンプライアンス ダッシュボードおよびレポート](/azure/security-center/security-center-compliance-dashboard) <sup>[8](#footnote8)</sup>                                       | GA             | GA                             | GA            |
| - [Microsoft Defender for Endpoint のデプロイと統合ライセンス](/azure/security-center/security-center-wdatp)                                                         | GA             | GA                             | 利用不可 |
| - [AWS アカウントを接続する](/azure/security-center/quickstart-onboard-aws)                                                                                                      | GA             | 利用不可                  | 利用不可 |
| - [GCP アカウントを接続する](/azure/security-center/quickstart-onboard-gcp)                                                                                                      | GA             | 利用不可                  | 利用不可 |
|                                                                                                                                                                             |                |                                |

<sup><a name="footnote1" /></a>1</sup> 部分的に GA: 脆弱性スキャンで発見された特定の項目を無効にする機能はパブリック プレビュー段階です。

<sup><a name="footnote2" /></a>2</sup> Azure Gov 上のコンテナー レジストリの脆弱性スキャンは、スキャン オン プッシュ機能でのみ実行できます。

<sup><a name="footnote3" /></a>3</sup> コンテナー レジストリ用 Azure Defender が必要です。

<sup><a name="footnote4" /></a>4</sup> 部分的に GA: Arc 対応クラスターのサポートはパブリック プレビュー段階であり、Azure Government では使用できません。

<sup><a name="footnote5" /></a>5</sup> Azure Defender for Kubernetes が必要です。

<sup><a name="footnote6" /></a>6</sup> 部分的に GA: Azure Defender for Storage の脅威保護アラートの一部はパブリック プレビュー段階です。

<sup><a name="footnote7" /></a>7</sup> これらの機能には、すべて [Azure Defender for servers](/azure/security-center/defender-for-servers-introduction) が必要です。

<sup><a name="footnote8" /></a>8</sup> クラウドの種類ごとに提供される標準が異なる場合があります。
 
<sup><a name="footnote9" /></a>9</sup> 部分的に GA: SQL サーバーのアラートと脆弱性評価のサブセット。 Behavioral Threat Protection は使用できません。

## <a name="next-steps"></a>次のステップ

- [Security Center による Log Analytics エージェントを使用したデータの収集](security-center-enable-data-collection.md)について確認します。
- [Security Center でデータを管理および保護する](security-center-data-security.md)方法を確認します。
- [Security Center がサポートされているプラットフォーム](security-center-os-coverage.md)を確認します。