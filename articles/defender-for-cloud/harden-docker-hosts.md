---
title: Microsoft Defender for Cloud を使用して Docker ホストを強化し、コンテナーを保護する
description: Docker ホストを保護し、CIS Docker ベンチマークに準拠していることを確認する方法
author: memildin
ms.author: memildin
ms.date: 11/09/2021
ms.topic: how-to
ms.service: defender-for-cloud
manager: rkarlin
ms.openlocfilehash: d977fadcb4d7975924afddc5c26770e568daafcb
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132524944"
---
# <a name="harden-your-docker-hosts"></a>Docker ホストのセキュリティを強化する

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft Defender for Cloud は、IaaS Linux Vm でホストされているアンマネージコンテナー、または Docker コンテナーを実行している他の Linux コンピューターを識別します。 Defender for Cloud は、これらのコンテナーの構成を継続的に評価します。 その後、[Center for Internet Security (CIS) Docker Benchmark](https://www.cisecurity.org/benchmark/docker/) と比較されます。

Defender for Cloud には、CI Docker ベンチマークのルールセット全体が含まれており、コンテナーがどのコントロールも満たさない場合は警告が表示されます。 誤った設定が検出されると、Defender for Cloud によってセキュリティの推奨事項が生成されます。 クラウドの [ **推奨事項] ページ** で Defender を使用して、推奨事項を表示し、問題を修復します。

脆弱性が見つかった場合、1 つの推奨事項内にグループ化されます。

>[!NOTE]
> これらの CIS ベンチマーク チェックは、AKS マネージド インスタンスまたは Databricks マネージド VM では実行されません。

## <a name="availability"></a>可用性

|側面|詳細|
|----|:----|
|リリース状態:|一般公開 (GA)|
|価格:|[Microsoft Defender for servers](defender-for-servers-introduction.md) が必要|
|必要なロールとアクセス許可:|ホストが接続するワークスペースの **閲覧者**|
|クラウド:|:::image type="icon" source="./media/icons/yes-icon.png"::: 商用クラウド<br>:::image type="icon" source="./media/icons/yes-icon.png"::: 各国 (Azure Government、Azure China 21Vianet)|
|||

## <a name="identify-and-remediate-security-vulnerabilities-in-your-docker-configuration"></a>Docker 構成のセキュリティの脆弱性を特定して修復する

1. Defender for Cloud のメニューから、[推奨事項] ページ **を開** きます。

1. 推奨事項を **[コンテナーのセキュリティ構成の脆弱性を修復する必要があります]** でフィルター処理して、推奨事項を選択します。

    [推奨事項] ページには、影響を受けるリソース (Docker ホスト) が表示されます。 

    :::image type="content" source="./media/monitor-container-security/docker-host-vulnerabilities-found.png" alt-text="コンテナーのセキュリティ構成の脆弱性を修復するための推奨事項。":::

    > [!NOTE]
    > Docker を実行していないマシンは、 **[適用されないリソース]** タブに表示されます。これらは Azure Policy に [準拠] と表示されます。 

1. 特定のホストで障害が発生した CIS コントロールを表示して修復するには、調査するホストを選択します。 

    > [!TIP]
    > [資産インベントリ] ページで開始し、そこからこの推奨事項に到達した場合は、[推奨事項] ページの **[アクションの実行]** ボタンを選択します。
    >
    > :::image type="content" source="./media/monitor-container-security/host-security-take-action-button.png" alt-text="Log Analytics を起動する [アクションの実行] ボタン。":::

    実行の準備が整ったカスタム操作を示した Log Analytics が開きます。 既定のカスタム クエリには、問題を解決するためのガイドラインと共に評価済みの失敗したルールの全一覧が含まれています。

    :::image type="content" source="./media/monitor-container-security/docker-host-vulnerabilities-in-query.png" alt-text="失敗したすべての CIS コントロールを表示するクエリを含む Log Analytics ページ。":::

1. 必要に応じて、クエリ パラメーターを調整します。

1. コマンドが適切であり、ホストの準備ができていることを確認したら、 **[実行]** を選択します。


## <a name="next-steps"></a>次のステップ

Docker のセキュリティ強化は、Defender for Cloud のコンテナー セキュリティ機能の 1 つの側面にす簡易です。 

詳細については [、「Defender for Cloud のコンテナー セキュリティ」を参照してください](container-security.md)。
