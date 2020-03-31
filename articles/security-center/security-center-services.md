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
ms.date: 03/01/2020
ms.author: memildin
ms.openlocfilehash: 9d3fa1e0b62ea6f4762c3df6ac7da310d5703807
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "79225243"
---
# <a name="feature-coverage-for-machines"></a>マシンを対象とする機能

以下の表は、仮想マシンとサーバーで使用できる Azure Security Center の機能を示しています。

## <a name="supported-features-for-virtual-machines-and-servers"></a>仮想マシンとサーバーでサポートされている機能 <a name="vm-server-features"></a>

### <a name="windows-machines"></a>[Windows マシン](#tab/features-windows)

|||||||||
|----|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
|**機能**|**Azure Virtual Machines**|**Azure Virtual Machine Scale Sets**|**Azure 以外のマシン**|**料金**
|[Microsoft Defender ATP の統合](security-center-wdatp.md)|✔</br>(サポートされているバージョン)|✔</br>(サポートされているバージョン)|✔|Standard|
|[仮想マシン行動分析 (およびセキュリティ アラート)](threat-protection.md)|✔|✔|✔|推奨事項 (無料) </br></br> セキュリティ アラート (Standard)|
|[ファイルレスのセキュリティ アラート](alerts-reference.md#alerts-windows)|✔|✔|✔|Standard|
|[ネットワーク ベースのセキュリティ アラート](threat-protection.md#network-layer)|✔|✔|-|Standard|
|[Just-In-Time VM アクセス](security-center-just-in-time.md)|✔|-|-|Standard|
|[ネイティブ脆弱性評価](built-in-vulnerability-assessment.md)|✔|-|-|Standard|
|[ファイルの整合性の監視](security-center-file-integrity-monitoring.md)|✔|✔|✔|Standard|
|[アダプティブ アプリケーション制御](security-center-adaptive-application.md)|✔|-|✔|Standard|
|[ネットワーク マップ](security-center-network-recommendations.md#network-map)|✔|✔|-|Standard|
|[アダプティブ ネットワークのセキュリティ強化機能](security-center-adaptive-network-hardening.md)|✔|-|-|Standard|
|アダプティブ ネットワーク制御|✔|✔|-|Standard|
|[規制へのコンプライアンス ダッシュボードおよびレポート](security-center-compliance-dashboard.md)|✔|✔|✔|Standard|
|Docker でホストされている IaaS コンテナーに対する推奨事項と脅威の防止|-|-|-|Standard|
|足りない OS パッチの評価|✔|✔|✔|無料|
|セキュリティの誤った構成の評価|✔|✔|✔|無料|
|[エンドポイント保護の評価](security-center-services.md#supported-endpoint-protection-solutions-)|✔|✔|✔|無料|
|ディスク暗号化の評価|✔|✔|-|無料|
|サードパーティの脆弱性評価|✔|-|-|無料|
|[ネットワーク セキュリティ評価](security-center-network-recommendations.md)|✔|✔|-|無料|


### <a name="linux-machines"></a>[Linux マシン](#tab/features-linux)

|||||||||
|----|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
|**機能**|**Azure Virtual Machines**|**Azure Virtual Machine Scale Sets**|**Azure 以外のマシン**|**料金**
|[Microsoft Defender ATP の統合](security-center-wdatp.md)|-|-|-|Standard|
|[仮想マシン行動分析 (およびセキュリティ アラート)](security-center-alerts-iaas.md)|✔</br>(サポートされているバージョン)|✔</br>(サポートされているバージョン)|✔|推奨事項 (無料) </br></br> セキュリティ アラート (Standard)|
|[ファイルレスのセキュリティ アラート](alerts-reference.md#alerts-windows)|-|-|-|Standard|
|[ネットワーク ベースのセキュリティ アラート](threat-protection.md#network-layer)|✔|✔|-|Standard|
|[Just-In-Time VM アクセス](security-center-just-in-time.md)|✔|-|-|Standard|
|[ネイティブ脆弱性評価](built-in-vulnerability-assessment.md)|✔|-|-|Standard|
|[ファイルの整合性の監視](security-center-file-integrity-monitoring.md)|✔|✔|✔|Standard|
|[アダプティブ アプリケーション制御](security-center-adaptive-application.md)|✔|-|✔|Standard|
|[ネットワーク マップ](security-center-network-recommendations.md#network-map)|✔|✔|-|Standard|
|[アダプティブ ネットワークのセキュリティ強化機能](security-center-adaptive-network-hardening.md)|✔|-|-|Standard|
|アダプティブ ネットワーク制御|✔|✔|-|Standard|
|[規制へのコンプライアンス ダッシュボードおよびレポート](security-center-compliance-dashboard.md)|✔|✔|✔|Standard|
|Docker でホストされている IaaS コンテナーに対する推奨事項と脅威の防止|✔|✔|✔|Standard|
|足りない OS パッチの評価|✔|✔|✔|無料|
|セキュリティの誤った構成の評価|✔|✔|✔|無料|
|[エンドポイント保護の評価](security-center-services.md#supported-endpoint-protection-solutions-)|-|-|-|無料|
|ディスク暗号化の評価|✔|✔|-|無料|
|サードパーティの脆弱性評価|✔|-|-|無料|
|[ネットワーク セキュリティ評価](security-center-network-recommendations.md)|✔|✔|-|無料|

--- 


> [!TIP]
>Standard 価格レベルでのみ利用可能な機能を試すために、Free レベルのユーザーは 30 日間の試用版に登録できます。 詳細については、 [価格に関するページ](https://azure.microsoft.com/pricing/details/security-center/)を参照してください。


## <a name="supported-endpoint-protection-solutions"></a>サポートされているエンドポイント保護ソリューション <a name="endpoint-supported"></a>

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


## <a name="next-steps"></a>次のステップ

- [Security Center によるデータの収集方法と Log Analytics エージェント](security-center-enable-data-collection.md)について確認します。
- [Security Center でデータを管理および保護する](security-center-data-security.md)方法を確認します。
- [Azure Security Center を導入するための設計上の考慮事項を計画し、理解する](security-center-planning-and-operations-guide.md)方法について説明しています。
- [Security Center がサポートされているプラットフォーム](security-center-os-coverage.md)を確認します。
- [Azure Security Center での Windows マシンおよび Linux マシンの脅威の防止](threat-protection.md#windows-machines)について詳しく確認します。
- [Azure Security Center についてよく寄せられる質問](faq-general.md)を確認します。