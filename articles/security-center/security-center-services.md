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
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/12/2020
ms.author: memildin
ms.openlocfilehash: a311439e8efc5481fbfd7431c1514ba6be576e39
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "91858015"
---
# <a name="feature-coverage-for-machines"></a>マシンを対象とする機能

以下の 2 つのタブは、Windows および Linux の仮想マシンとサーバーで使用できる Azure Security Center の機能を示しています。

## <a name="supported-features-for-virtual-machines-and-servers"></a>仮想マシンとサーバーでサポートされている機能 <a name="vm-server-features"></a>

### <a name="windows-machines"></a>[**Windows マシン**](#tab/features-windows)

|**機能**|**Azure Virtual Machines**|**Azure Virtual Machine Scale Sets**|**Azure 以外のマシン**|**Azure Defender が必要**
|----|:----:|:----:|:----:|:----:|
|[Microsoft Defender ATP の統合](security-center-wdatp.md)|✔</br>(サポートされているバージョン)|✔</br>(サポートされているバージョン)|✔|はい|
|[仮想マシン行動分析 (およびセキュリティ アラート)](alerts-reference.md)|✔|✔|✔|はい|
|[ファイルレスのセキュリティ アラート](alerts-reference.md#alerts-windows)|✔|✔|✔|はい|
|[ネットワーク ベースのセキュリティ アラート](other-threat-protections.md#network-layer)|✔|✔|-|はい|
|[Just In Time VM アクセス](security-center-just-in-time.md)|✔|-|-|はい|
|[ネイティブ脆弱性評価](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)|✔|-|-|はい|
|[ファイルの整合性の監視](security-center-file-integrity-monitoring.md)|✔|✔|✔|はい|
|[アダプティブ アプリケーション制御](security-center-adaptive-application.md)|✔|-|✔|はい|
|[ネットワーク マップ](security-center-network-recommendations.md#network-map)|✔|✔|-|はい|
|[アダプティブ ネットワークのセキュリティ強化機能](security-center-adaptive-network-hardening.md)|✔|-|-|はい|
|[規制へのコンプライアンス ダッシュボードおよびレポート](security-center-compliance-dashboard.md)|✔|✔|✔|はい|
|Docker でホストされている IaaS コンテナーに対する推奨事項と脅威の防止|-|-|-|はい|
|足りない OS パッチの評価|✔|✔|✔|Azure: いいえ<br><br>Azure 以外: はい|
|セキュリティの誤った構成の評価|✔|✔|✔|Azure: いいえ<br><br>Azure 以外: はい|
|[エンドポイント保護の評価](security-center-services.md#supported-endpoint-protection-solutions-)|✔|✔|✔|Azure: いいえ<br><br>Azure 以外: はい|
|ディスク暗号化の評価|✔</br>([サポートされるシナリオ](../virtual-machines/windows/disk-encryption-windows.md#unsupported-scenarios)の場合)|✔|-|いいえ|
|サードパーティの脆弱性評価|✔|-|-|いいえ|
|[ネットワーク セキュリティ評価](security-center-network-recommendations.md)|✔|✔|-|いいえ|


### <a name="linux-machines"></a>[**Linux マシン**](#tab/features-linux)

|**機能**|**Azure Virtual Machines**|**Azure Virtual Machine Scale Sets**|**Azure 以外のマシン**|**Azure Defender が必要**
|----|:----:|:----:|:----:|:----:|
|[Microsoft Defender ATP の統合](security-center-wdatp.md)|-|-|-|はい|
|[仮想マシン行動分析 (およびセキュリティ アラート)](security-center-alerts-iaas.md)|✔</br>(サポートされているバージョン)|✔</br>(サポートされているバージョン)|✔|はい|
|[ファイルレスのセキュリティ アラート](alerts-reference.md#alerts-windows)|-|-|-|はい|
|[ネットワーク ベースのセキュリティ アラート](other-threat-protections.md#network-layer)|✔|✔|-|はい|
|[Just In Time VM アクセス](security-center-just-in-time.md)|✔|-|-|はい|
|[ネイティブ脆弱性評価](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)|✔|-|-|はい|
|[ファイルの整合性の監視](security-center-file-integrity-monitoring.md)|✔|✔|✔|はい|
|[アダプティブ アプリケーション制御](security-center-adaptive-application.md)|✔|-|✔|はい|
|[ネットワーク マップ](security-center-network-recommendations.md#network-map)|✔|✔|-|はい|
|[アダプティブ ネットワークのセキュリティ強化機能](security-center-adaptive-network-hardening.md)|✔|-|-|はい|
|[規制へのコンプライアンス ダッシュボードおよびレポート](security-center-compliance-dashboard.md)|✔|✔|✔|はい|
|Docker でホストされている IaaS コンテナーに対する推奨事項と脅威の防止|✔|✔|✔|はい|
|足りない OS パッチの評価|✔|✔|✔|Azure: いいえ<br><br>Azure 以外: はい|
|セキュリティの誤った構成の評価|✔|✔|✔|Azure: いいえ<br><br>Azure 以外: はい|
|[エンドポイント保護の評価](security-center-services.md#supported-endpoint-protection-solutions-)|-|-|-|いいえ|
|ディスク暗号化の評価|✔</br>([サポートされるシナリオ](../virtual-machines/windows/disk-encryption-windows.md#unsupported-scenarios)の場合)|✔|-|いいえ|
|サードパーティの脆弱性評価|✔|-|-|いいえ|
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
| McAfee v10 以降 | Linux Server ファミリ  | いいえ | はい **\*** |
| Sophos V9+| Linux Server ファミリ  | いいえ | はい  **\***  |

 **\*** カバレッジの状態とサポート データは、現在、保護されているサブスクリプションに関連付けられている Log Analytics ワークスペース内でのみ使用できます。 Azure Security Center ポータルには反映されません。

> [!NOTE]
> Windows Server 2008 R2 の仮想マシンで System Center Endpoint Protection (SCEP) を検出するには、PowerShell (v3.0 またはそれ以降) の後で SCEP をインストールする必要があります。


## <a name="next-steps"></a>次のステップ

- [Security Center による Log Analytics エージェントを使用したデータの収集](security-center-enable-data-collection.md)について確認します。
- [Security Center でデータを管理および保護する](security-center-data-security.md)方法を確認します。
- [Security Center がサポートされているプラットフォーム](security-center-os-coverage.md)を確認します。
