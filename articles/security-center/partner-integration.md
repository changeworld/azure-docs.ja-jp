---
title: Microsoft Defender for Cloudでセキュリティ ソリューションを統合|Microsoft Docs
description: Microsoft Defender for Cloud をパートナーと統合して、Azure リソースの全体的なセキュリティを強化する方法について説明します。
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 12/10/2020
ms.author: memildin
ms.custom: ignite-fall-2021
ms.openlocfilehash: b5b6869cf1d61f89ce9ac072411b0786da552a46
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091926"
---
# <a name="integrate-security-solutions-in-microsoft-defender-for-cloud"></a>Microsoft Defender for Cloud でセキュリティ ソリューションを統合します。

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

このドキュメントは、既にMicrosoft Defender for Cloud に接続されているセキュリティ ソリューションを管理し、新しいソリューションを追加するのに役立ちます。

## <a name="integrated-azure-security-solutions"></a>統合された Azure セキュリティ ソリューション
Defender for Cloud を使用すると、Azure で統合セキュリティ ソリューションを簡単に有効にできます。 利点は次のとおりです。

- **簡略化されたデプロイ**: Defender for Cloud では、統合パートナー ソリューションの合理化されたプロビジョニングが提供されます。 マルウェア対策や脆弱性評価などのソリューションについては、Defender for Cloud によって仮想マシンにエージェントをプロビジョニングできます。 ファイアウォール アプライアンスの場合、Defender for Cloud は、必必要なネットワーク構成の多くに対処できます。
- **統合された検出**: パートナー ソリューションからのセキュリティ イベントは、Defender for Cloud の警告とインシデントの一環として自動的に収集、集計、表示されます。 また、これらのイベントは、他のソースからの検出とも組み合わされ、高度な脅威検出機能を提供します。
- **統合された正常性の監視と管理**:ユーザーは、統合された正常性イベントを使用して、すべてのパートナー ソリューションをひとめで監視できます。 基本的な管理は、パートナー ソリューションを使用して高度な設定に簡単にアクセスできれば、使用できます。

現時点では、統合セキュリティ ソリューションに、[Qualys](https://www.qualys.com/public-cloud/#azure) と [Rapid7](https://www.rapid7.com/products/insightvm/) による脆弱性評価と、[Azure Application Gateway 上の Microsoft Azure Web Application Firewall](../web-application-firewall/ag/ag-overview.md) が含まれます。

> [!NOTE]
> ほとんどのセキュリティ ベンダーがアプライアンス上での外部エージェントの実行を禁止しているため、Defender for Cloud は はパートナー仮想アプライアンスに Log Analytics エージェントをインストールしません。

Microsoft Defender for servers を有効にしているお客様が利用できる組み込みのスキャナーなど、Qualys の脆弱性スキャン ツールの統合の詳細については、[「Defender for Cloud の Azure とハイブリッド マシン用の 統合された Qualys脆弱性スキャナー」](deploy-vulnerability-assessment-vm.md)を参照してください。

Defender for Cloud には、次の脆弱性分析も用意されています。

* SQL データベース - 「[脆弱性評価ダッシュボードで脆弱性評価レポートの探索をする](defender-for-sql-on-machines-vulnerability-assessment.md#explore-vulnerability-assessment-reports) 」を参照する
* イメージの Azure Container Registry-「[コンテナーレジストリに Microsoft Defender を使用して、イメージの脆弱性をスキャンする」を](defender-for-container-registries-usage.md)参照してください。

## <a name="how-security-solutions-are-integrated"></a>セキュリティ ソリューションを統合するしくみ
Defender for Cloud からデプロイされている Azure セキュリティ ソリューションは、自動的に接続されます。 オンプレミスまたは他のクラウドで実行されているコンピューターなど、その他のセキュリティ データ ソースも接続できます。

:::image type="content" source="./media/partner-integration/security-solutions-page.png" alt-text="パートナー ソリューションの統合。" lightbox="./media/partner-integration/security-solutions-page.png":::

## <a name="manage-integrated-azure-security-solutions-and-other-data-sources"></a>統合された Azure セキュリティ ソリューションとその他のデータ ソースの管理

1. [Azure portal](https://azure.microsoft.com/features/azure-portal/)から、**Defender for Cloud** を開きます。

1. Defender for Cloud のメニューから、 **[セキュリティ ソリューション]** を選択します。

**[セキュリティ ソリューション]** ページから、統合された Azure セキュリティ ソリューションの正常性を確認し、基本的な管理タスクを実行できます。

### <a name="connected-solutions"></a>接続済みソリューション

**[接続済みソリューション]** セクションには、Defender for Cloud に現在接続されているセキュリティ ソリューションが含まれています。 また、各ソリューションの正常性状態も表示されます。  

![接続済みのソリューション。](./media/partner-integration/connected-solutions.png)

パートナー ソリューションの状態は、次のいずれかになります。

* **正常** (緑) - 正常性の問題はありません。
* **異常** (赤) - 早急な対処が必要な正常性の問題があります。
* **レポート停止** (オレンジ) - ソリューションが正常性の報告を停止しています。
* **レポートなし** (灰色) - ソリューションがまだ何も報告しておらず、正常性データはありません。 最近接続されて、まだデプロイ中の場合、ソリューションの状態が報告されない可能性があります。

> [!NOTE]
> 正常性状態データを使用できない場合、Defender for Cloud には、最後に受信したイベントの日時が表示され、ソリューションが報告されているかどうかを示します。 使用できる正常性データがなく、過去 14 日以内にアラートを受信していなかった場合、Defender for Cloud は、ソリューションで異常が発生しているか、報告が行われていないことを示します。
>
>

**[表示]** を選択すると、次のような追加情報とオプションを確認できます。

   - **ソリューション コンソール** - このソリューションの管理エクスペリエンスが開きます。
   - **VM をリンクする** - [アプリケーションのリンク] ページが開きます。 ここで、パートナー ソリューションにリソースを接続できます。
   - **Delete solution (ソリューションを削除する)**
   - **構成**

   ![パートナー ソリューションの詳細。](./media/partner-integration/partner-solutions-detail.png)


### <a name="discovered-solutions"></a>検出されたソリューション

Defender for Cloudは、Azure で実行されていても Security Center に接続されていないセキュリティ ソリューションを自動的に検出し、 **[検出されたソリューション]** セクションにそのソリューションを表示します。 これらのソリューションには、[Azure AD Identity Protection](../active-directory/identity-protection/overview-identity-protection.md) などの Azure ソリューションや、パートナー ソリューションが含まれます。

> [!NOTE]
> 検出されたソリューション機能に対して、サブスクリプション レベルで **advanced protections** を有効にします。 詳細については、[クイックスタート: 強化されたセキュリティ機能を有効にする](enable-enhanced-security.md)を参照してください。

ソリューションの下の **[接続]** を選択して、Defender for Cloudと統合し、セキュリティのアラートが通知されるようにします。

### <a name="add-data-sources"></a>データ ソースの追加

**[データ ソースの追加]** セクションには、接続できるその他の使用可能なデータ ソースが表示されます。 このようなソースのいずれかからデータを追加する手順については、 **[追加]** をクリックしてください。

![データ ソース:](./media/partner-integration/add-data-sources.png)



## <a name="next-steps"></a>次のステップ

この記事では、Defender for Cloud でパートナー ソリューションを統合する方法について学習しました。 Microsoft Sentinel またはその他の SIEM との統合をセットアップする方法については、「[Security Center のデータを連続的にエクスポートする](continuous-export.md)」を参照してください。
