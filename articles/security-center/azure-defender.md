---
title: Azure Defender の概要と使用可能なプラン
description: Azure Defender のプラン、保護、アラートについて説明します。 その後、サブスクリプションで Azure Defender を有効にする操作に進みます。
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: conceptual
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: bb1c1e6443b5087b48aad7c3171bef557707adb1
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/22/2020
ms.locfileid: "90977285"
---
# <a name="introduction-to-azure-defender"></a>Azure Defender の概要

Azure Security Center の機能は、クラウド セキュリティの 2 つの大きな柱をカバーしています。

- **クラウド セキュリティ態勢管理 (CSPM)**
- **クラウド ワークロード保護 (CWP)**

Security Center の CSPM 機能 (セキュリティ スコア、Windows と Linux の Azure マシンでのセキュリティ構成の誤りの検出など) は、すべての Azure ユーザーが利用できる無料の Security Center エクスペリエンスの一部です。 これらの CSPM 機能を使用して、態勢を強化し、規制コンプライアンスを保証します。

**Azure Defender** は、Azure とハイブリッド ワークロードを高度かつインテリジェントに保護するために Security Center 内に統合された、クラウド ワークロード保護プラットフォーム (CWPP) です。

Azure Security Center の Azure Defender ダッシュボードを次に示します。

:::image type="content" source="./media/azure-defender/sample-defender-dashboard.png" alt-text="Azure Defender ダッシュボードの例" lightbox="./media/azure-defender/sample-defender-dashboard.png":::

## <a name="what-resource-types-can-azure-defender-secure"></a>Azure Defender によって、どのような種類のリソースがセキュリティ保護されますか?

Azure Defender では、仮想マシン、SQL データベース、コンテナー、Web アプリケーション、ネットワークなどに対して、セキュリティ アラートと高度な脅威保護が提供されます。

Azure Security Center の**価格と設定**の領域から Azure Defender を有効にすると、次の Defender プランが同時にすべて有効になり、環境のコンピューティング、データ、およびサービス レイヤーに対する包括的な防御が提供されます。

- [Azure Defender for servers](defender-for-servers-introduction.md)
- [Azure Defender for App Service](defender-for-app-service-introduction.md)
- [Azure Defender for Storage](defender-for-storage-introduction.md)
- [Azure Defender for SQL](defender-for-sql-introduction.md)
- [Azure Defender for IoT](defender-for-iot-introduction.md)
- [Azure Defender for Kubernetes](defender-for-kubernetes-introduction.md)
- [Azure Defender for container registries](defender-for-container-registries-introduction.md)
- [Azure Defender for Key Vault](defender-for-key-vault-introduction.md)

これらのプランについてはそれぞれ、Security Center のドキュメントで個別に説明されています。


## <a name="hybrid-cloud-protection"></a>ハイブリッド クラウド保護

Azure 環境の防護に加えて、次のような Azure Defender 機能をハイブリッド クラウド環境に追加することができます。

- 非 Azure サーバーを保護する
- 他のクラウド (AWS、GCP など) 内の仮想マシンを保護する
- IoT デバイスを保護する

特定の環境に応じてカスタマイズされた脅威インテリジェンスと優先順位が付けられたアラートを取得して、最も重要な問題に専念できるようにします。

[Azure Arc](https://azure.microsoft.com/services/azure-arc/) をデプロイし、Azure Defender を有効にして、オンプレミスおよびマルチクラウドの仮想マシンと SQL データベースに保護を拡張します。 Azure Arc for servers は無料のサービスですが、Arc 対応サーバー (Azure Defender など) で使用されるサービスは、そのサービスの価格に従って課金されます。

[Azure Arc の詳細情報を参照してください](https://docs.microsoft.com/azure/azure-arc/overview)。


## <a name="azure-defender-alerts"></a>Azure Defender アラート 

Azure Defender により、環境のいずれかの領域で脅威が検出されると、アラートが生成されます。 これらのアラートでは、影響を受けるリソースや推奨される修復手順の詳細と、場合によっては、ロジック アプリを応答でトリガーするオプションが示されます。

アラートは、Security Center によって生成されたか、統合されているセキュリティ製品から Security Center によって受信されたかにかかわらず、エクスポートすることができます。 アラートを Azure Sentinel、サードパーティの SIEM、またはその他の外部ツールにエクスポートする場合は、[SIEM へのアラートのエクスポート](continuous-export.md)に関するページの手順に従ってください。

> [!NOTE]
> アラートの出力元によって、表示されるまでの時間が変わる場合があります。 たとえば、ネットワーク トラフィックの分析を必要とするアラートは、仮想マシンで実行されている疑わしいプロセスに関連するアラートよりも、表示されるまでより長い時間がかかる可能性があります。


## <a name="azure-defender-advanced-protection-capabilities"></a>Azure Defender の高度な保護機能

Azure Defender では、リソースに関連する、調整された推奨事項を得るために、高度な分析を使用します。 

保護には、ジャスト インタイム アクセスと適応型アプリケーション制御を使用した、VM の管理ポートのセキュリティ保護が含まれます。これにより、コンピューター上で実行すべきアプリや実行する必要がないアプリを示す許可リストが作成されます。 

Azure Defender ダッシュボードの [高度な保護] タイルを使用して、これらの保護をそれぞれ監視し、構成します。 

## <a name="vulnerability-assessment-and-management"></a>脆弱性の評価と管理

Azure Defender には、仮想マシンとコンテナー レジストリの脆弱性スキャンが追加費用なしで含まれています。 スキャナーは Qualys を使用していますが、Qualys ライセンスも Qualys アカウントも必要ありません。すべてが Security Center 内でシームレスに処理されます。 

これらの脆弱性スキャナーの結果を確認し、すべての結果に対して Security Center 内から対応します。 これにより、Security Center では、クラウド セキュリティのすべての取り組みを、より一元的に確認できます。

詳細については、次のページを参照してください。

- [Azure 仮想マシンに対する Security Center の統合脆弱性評価](deploy-vulnerability-assessment-vm.md)
- [Azure コンテナー レジストリでイメージの脆弱性を識別する](defender-for-container-registries-usage.md#identify-vulnerabilities-in-images-in-other-container-registries)



## <a name="next-steps"></a>次の手順

この記事では、Azure Defender の利点について説明しました。 

> [!div class="nextstepaction"]
> [Azure Defender を有効にする](security-center-pricing.md)