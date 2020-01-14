---
title: Azure サービス レイヤーの脅威検出 - Azure Security Center
description: このトピックでは、Azure Security Center で使用可能な Azure サービス レイヤーのアラートについて説明します。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 33c45447-3181-4b75-aa8e-c517e76cd50d
ms.service: security-center
ms.topic: conceptual
ms.date: 08/25/2019
ms.author: memildin
ms.openlocfilehash: d4033989830323856ac14ed06eea7df74806f128
ms.sourcegitcommit: ff9688050000593146b509a5da18fbf64e24fbeb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/06/2020
ms.locfileid: "75665707"
---
# <a name="threat-detection-for-the-azure-service-layer-in-azure-security-center"></a>Azure Security Center での Azure サービス レイヤーの脅威検出

このトピックでは、次の Azure サービス レイヤーを監視する場合に使用可能な Azure Security Center のアラートについて説明します。

* [Azure ネットワーク レイヤー](#network-layer)
* [Azure 管理レイヤー (Azure Resource Manager) (プレビュー)](#management-layer)
* [Azure Key Vault](#azure-keyvault)

## Azure ネットワーク レイヤー<a name="network-layer"></a>

Security Center のネットワーク レイヤー分析は、サンプルの [IPFIX データ](https://en.wikipedia.org/wiki/IP_Flow_Information_Export)に基づくものであり、Azure コア ルーターによって収集されるパケット ヘッダーです。 このデータ フィードに基づき、Security Center は機械学習モデルを使用して、悪意のあるトラフィック アクティビティを特定し、それらにフラグを設定します。 IP アドレスを強化するために、Security Center では、Microsoft の脅威インテリジェンス データベースも使用します。

ネットワーク構成によっては、Security Center が疑わしいネットワーク アクティビティに対してアラートを生成することを制限する場合があります。 Security Center でネットワーク アラートを生成するには、次のことを確認してください。

- 仮想マシンにパブリック IP アドレスがある (または、パブリック IP アドレスを持つロード バランサー上にある)。

- 仮想マシンのネットワーク エグレス トラフィックが、外部 ID ソリューションによってブロックされていない。

- 不審な通信が発生した時間全体で仮想マシンに同じ IP アドレスが割り当てられている。 これは、管理されたサービスの一部として作成された VM (例: AKS、Databricks) にも適用されます。

Azure ネットワーク レイヤー アラートの一覧については、[アラートのリファレンス表](alerts-reference.md#alerts-azurenetlayer)に関するページを参照してください。

Security Center でネットワーク関連のシグナルを使用して脅威の防止を適用する方法の詳細については、「[Security Center でのヒューリスティック DNS 検出](https://azure.microsoft.com/blog/heuristic-dns-detections-in-azure-security-center/)」を参照してください。


## Azure 管理レイヤー (Azure Resource Manager) (プレビュー)<a name ="management-layer"></a>

Azure Resource Manager に基づく Security Center の保護レイヤーは、現在、プレビュー段階です。

Security Center では、Azure のコントロール プレーンと見なされる Azure Resource Manager イベントを使用することで、追加の保護レイヤーが提供されます。 Security Center では、Azure Resource Manager のレコードを分析することで、Azure サブスクリプション環境での異常な、または害を及ぼす可能性のある操作を検出します。

Azure Resource Manager (プレビュー) アラートの一覧については、[アラートのリファレンス表](alerts-reference.md#alerts-azureresourceman)に関するページを参照してください。



>[!NOTE]
> 上記のいくつかの分析では、Microsoft Cloud App Security が利用されています。 これらの分析を活用するには、Cloud App Security ライセンスをアクティブにする必要があります。 Cloud App Security ライセンスをお持ちの場合、これらのアラートは既定で有効になります。 これらを無効にするには、次のようにします。
>
> 1. **[Security Center]** ブレードで **[セキュリティ ポリシー]** を選択します。 変更するサブスクリプションに対して **[設定の編集]** を選択します。
> 2. **[脅威検出]** を選択します。
> 3. **[統合の有効化]** で、 **[Microsoft Cloud App Security にデータへのアクセスを許可する]** チェック ボックスをクリアして、 **[保存]** を選択します。

>[!NOTE]
>Security Center では、そのリソースと同じ地域でセキュリティ関連の顧客データが格納されます。 Microsoft によってまだリソースの地域に Security Center がデプロイされていない場合、米国でデータが格納されます。 Cloud App Security が有効になっている場合、この情報は、Cloud App Security の地域の場所のルールに従って格納されます。 詳細については、[非リージョン サービスのデータ ストレージ](https://azuredatacentermap.azurewebsites.net/)に関するページを参照してください。

## Azure Key Vault (プレビュー)<a name="azure-keyvault"></a>

Azure Key Vault は、暗号化キーとシークレット (証明書、接続文字列、パスワードなど) を保護するクラウド サービスです。 

Azure Security Center には、Azure Key Vault 用に Azure ネイティブの高度な脅威保護機能が含まれており、セキュリティ インテリジェンスのレイヤーが追加されます。 Security Center では、異常であり、害を及ぼす可能性のある、Key Vault アカウントに対するアクセスまたは悪用の試みを検出できます。 この保護層により、セキュリティの専門家でなくても、サードパーティ製のセキュリティ監視システムを管理する必要なしに、脅威に対処することができます。  

異常なアクティビティが発生した場合、Security Center では、アラートが表示され、必要に応じてサブスクリプション管理者にメールが送信されます。 これらのアラートには、不審なアクティビティの詳細と、脅威の調査や修復方法に関する推奨事項が含まれます。 

> [!NOTE]
> 現在、Azure Government およびソブリン クラウド リージョンでは、このサービスを利用できません。

Azure Key Vault アラートの一覧については、[アラートのリファレンス表](alerts-reference.md#alerts-azurekv)に関するページを参照してください。
