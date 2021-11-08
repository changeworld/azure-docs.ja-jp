---
title: Microsoft Defender for Resource Manager - 利点と機能
description: Microsoft Defender for Resource Manager の利点と機能について説明します
author: memildin
ms.author: memildin
ms.date: 09/05/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: ea1f2259d34389498bcdf144a463973baeb3919d
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131453176"
---
# <a name="introduction-to-microsoft-defender-for-resource-manager"></a>Microsoft Defender for Resource Manager の概要

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

[Azure Resource Manager](../azure-resource-manager/management/overview.md) は、Azure のデプロイおよび管理サービスです。 お使いの Azure アカウント内のリソースを作成、更新、および削除できる管理レイヤーを提供します。 アクセス制御、ロック、タグなどの管理機能を使用して、デプロイ後にリソースを保護および整理します。

このクラウド管理レイヤーは、すべてのクラウド リソースに接続される非常に重要なサービスです。 それだけに攻撃者の標的になるリスクもはらんでいます。 したがって、セキュリティ運用チームでリソース管理レイヤーを厳しく監視することをお勧めします。 

組織内のリソース管理操作は、その手段が Azure portal や Azure REST API、Azure CLI であれ、その他 Azure のプログラマティック クライアントであれ、Microsoft Defender for Resource Manager によって自動的に監視されます。 Defender for Cloud は高度なセキュリティ分析を実行して脅威を検出し、不審なアクティビティについてアラートを生成します。

>[!NOTE]
> これらの分析の一部には、[Microsoft Defender For Cloud Apps (旧称 Microsoft Cloud App Security)](/cloud-app-security/what-is-cloud-app-security) が使用されています。 これらの分析を活用するには、Cloud App Security ライセンスをアクティブにする必要があります。 Cloud App Security ライセンスをお持ちの場合、これらのアラートは既定で有効になります。 アラートを無効にするには、次のようにします。
>
> 1. [Defender for Cloud] メニューで、 **[環境設定]** を開きます。
> 1. 変更するサブスクリプションを選択します。
> 1. **[Integrations]\(統合\)** を選択します。
> 1. **[Allow Microsoft Defender for Cloud Apps to access my data]\(Microsoft Defender for Cloud Apps がデータにアクセスすることを許可する\)** をクリアし、 **[保存]** を選択します。


## <a name="availability"></a>可用性

|側面|詳細|
|----|:----|
|リリース状態:|一般公開 (GA)|
|価格:|**Microsoft Defender for Resource Manager** は、[価格ページ](https://azure.microsoft.com/pricing/details/security-center/)の記載に基づいて課金されます。|
|クラウド:|:::image type="icon" source="./media/icons/yes-icon.png"::: 商用クラウド<br>:::image type="icon" source="./media/icons/yes-icon.png"::: Azure Government<br>:::image type="icon" source="./media/icons/yes-icon.png"::: Azure China 21Vianet|
|||

## <a name="what-are-the-benefits-of-microsoft-defender-for-resource-manager"></a>Microsoft Defender for Resource Manager の利点

Microsoft Defender for Resource Manager は、次のような問題からの保護対策となります。

- **不審なリソース管理操作**: 悪意のある IP アドレスからの操作、マルウェア対策ソフトの無効化、VM 拡張機能で実行される不審なスクリプトなど
- **悪用ツールキット**: Microburst、PowerZure など
- **侵入拡大**: Azure 管理レイヤーから Azure リソースのデータ プレーンへの侵入

:::image type="content" source="media/defender-for-resource-manager-introduction/consistent-management-layer-with-defender.png" alt-text="Azure Resource Manager の概要図。":::

Microsoft Defender for Resource Manager によって提供されるアラートの全一覧は、[アラートのリファレンス ページ](alerts-reference.md#alerts-resourcemanager)に掲載されています。


 ## <a name="how-to-investigate-alerts-from-microsoft-defender-for-resource-manager"></a>Microsoft Defender for Resource Manager からのアラートを調査する方法

Microsoft Defender for Resource Manager からのセキュリティ アラートの基になっているのは、Azure Resource Manager の操作を監視することによって検出された脅威です。 Defender for Cloud では、Azure Resource Manager の内部ログ ソースに加え、Azure アクティビティ ログが使用されます。これは Azure のプラットフォーム ログであり、サブスクリプションレベルのイベントについての分析情報が得られます。

詳細については、「[Azure アクティビティ ログ](../azure-monitor/essentials/activity-log.md)」を参照してください。

Microsoft Defender for Resource Manager からのセキュリティ アラートを調査するには、次の手順に従います。

1. Azure のアクティビティ ログを開きます。

    :::image type="content" source="media/defender-for-resource-manager-introduction/opening-azure-activity-log.png" alt-text="Azure のアクティビティ ログを開く方法。":::

1. イベントに次のフィルターを適用します。
    - アラート内で言及されているサブスクリプション
    - 検出されたアクティビティの期間
    - 関連するユーザー アカウント (該当する場合)

1. 不審なアクティビティを探します。

> [!TIP]
> 調査エクスペリエンスを向上させるには、「[Azure アクティビティ ログからデータを接続する](../sentinel/data-connectors-reference.md#azure-activity)」の説明に従って、Azure アクティビティ ログを Microsoft Sentinel にストリーム配信してください。



## <a name="next-steps"></a>次の手順

この記事では、Microsoft Defender for Resource Manager について学習しました。 

> [!div class="nextstepaction"]
> [強化された保護を有効にする](enable-enhanced-security.md)

関連資料については、次の記事をご覧ください。 

- セキュリティ アラートは、Defender for Cloud によって生成される場合も、別のセキュリティ製品から Defender for Cloud によって受信される場合もあります。 それらすべてのアラートを Microsoft Sentinel、サードパーティの SIEM、または他の外部ツールにエクスポートするには、[SIEM へのアラートのエクスポート](continuous-export.md)に関するページの手順に従ってください。