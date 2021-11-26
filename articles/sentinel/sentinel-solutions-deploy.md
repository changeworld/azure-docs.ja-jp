---
title: Microsoft Sentinel のすぐに使えるコンテンツとソリューションを一元的に検出してデプロイする
description: この記事では、データコネクタやその他のコンテンツと共にパッケージ化された、データ分析ツールを簡単に見つけてデプロイする方法について説明します。
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: c1e10e0ee346358b095d6eb5bec4fafab1f56799
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132711700"
---
# <a name="centrally-discover-and-deploy-microsoft-sentinel-out-of-the-box-content-and-solutions-public-preview"></a>Microsoft Sentinel のすぐに使えるコンテンツとソリューションを一元的に検出してデプロイする (パブリック プレビュー)

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!IMPORTANT]
>
> Microsoft Sentinel ソリューションと Microsoft Sentinel コンテンツ ハブは現在 **プレビュー版** です。個別のソリューション パッケージもすべてプレビュー版です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

Microsoft Sentinel コンテンツ ハブを使用すると、エンド ツー エンドの製品、ドメイン、または業界のニーズに対応したコンテンツと共に提供される、Microsoft Sentinel のすぐに使用できる (組み込みの) コンテンツやソリューションにアクセスできます。

この記事では、Microsoft Sentinel ワークスペースにソリューションをインストールし、その中のコンテンツを使用できるようにする方法について説明します。

- コンテンツハブの状態、含まれるコンテンツ、サポートなどに基づいて、ソリューションを検索します。

- 組織のニーズに合ったソリューションが見つかったら、ワークスペースにソリューションをインストールします。 常に最新の変更内容で更新してください。

> [!TIP]
> パートナーが独自のソリューションを作成する場合は、ソリューションの作成と公開に関する [Microsoft Sentinel ソリューションのビルド ガイド](https://aka.ms/sentinelsolutionsbuildguide)を参照してください。
>
## <a name="find-a-solution"></a>ソリューションの検索

1. Microsoft Sentinel ナビゲーション メニューの **[コンテンツ管理]** で、 **[コンテンツ ハブ (プレビュー)]** を選択します。

1. **コンテンツハブ** ページには、検索とフィルターが適用されたソリューションのグリッドが表示されます。

    フィルターから特定の値を選択するか、 **検索** フィールドにソリューション名または説明の一部を入力することによって、表示される一覧をフィルター処理します。

    詳細については、「[Microsoft Sentinel のすぐに使えるコンテンツとソリューションのカテゴリ](sentinel-solutions.md#microsoft-sentinel-out-of-the-box-content-and-solution-categories)」を参照してください。

    > [!TIP]
    > デプロイしたソリューションに更新プログラムが配置されていた場合、そのソリューションは、展開する更新プログラムがあることを示しており、ページの上部にある青い三角形で示されます。
    >

グリッド内の各ソリューションには、ソリューションに適用されるカテゴリと、ソリューションに含まれるコンテンツの種類が表示されます。

たとえば、次の図では、 **Cisco の包括的** なソリューションが **セキュリティ、その他** のカテゴリを示しています。このソリューションには、10の分析ルール、11個の検索クエリ、パーサー、3つのプレイブックなどが含まれています。

:::image type="content" source="./media/sentinel-solutions-deploy/solutions-list.png" alt-text="Microsoft Sentinel コンテンツ ハブのスクリーンショット。" lightbox="./media/sentinel-solutions-deploy/solutions-list.png":::

## <a name="install-or-update-a-solution"></a>ソリューションのインストールまたは更新

1. コンテンツハブでソリューションを選択すると、右側に詳細な情報が表示されます。 更新プログラムが必要な場合は、 **[インストール]** または **[更新]** を選択します。 次に例を示します。

1. ソリューションの詳細ページで、 **[作成]** または **[更新]** を選択して、ソリューションウィザードを開始します。 ウィザードの **[基本]** タブで、ソリューションをデプロイするサブスクリプション、リソースグループ、およびワークスペースを入力します。 次に例を示します。

    :::image type="content" source="media/sentinel-solutions-deploy/wizard-basics.png" alt-text="[基本] タブが表示されているソリューションインストールウィザードのスクリーンショット。":::

1. **[次へ]** を選択して、(ソリューションに含まれるコンポーネントに対応する) 残りのタブを順番に表示します。ここで、各コンテンツコンポーネントについて学習したり、構成したりすることができます。

    > [!NOTE]
    > 表示されるタブは、ソリューションによって提供されるコンテンツに対応しています。 ソリューションによってコンテンツの種類が異なる場合があるため、すべてのソリューションで同じタブが表示されないことがあります。
    >
    > Microsoft Sentinel でシステムに対して認証できるように、サード パーティのサービスに資格情報を入力するように求められる場合もあります。 たとえば、プレイブックでは、システムで規定されているように応答アクションを実行することができます。
    >

1. 最後に、 **[レビュー + 作成]** タブでメッセージを待機し、[ `Validation Passed`**作成** ] または **[更新]** を選択してソリューションをデプロイします。 **[自動化テンプレートのダウンロード]** リンクをクリックし、ソリューションをコードとしてデプロイすることもできます。

詳細については、「[Microsoft Sentinel コンテンツ ハブ カタログ](sentinel-solutions-catalog.md)」と「[Microsoft Azure Sentinel データ コネクタを見つける](data-connectors-reference.md)」を参照してください。

## <a name="find-the-support-model-for-your-solution"></a>ソリューションのサポートモデルを見つける

各ソリューションには、ソリューションの詳細ペインでサポートモデルの詳細が一覧表示されます。 **[サポート]** ボックスには、 **Microsoft** またはパートナーの名前が表示されます。 次に例を示します。

:::image type="content" source="media/sentinel-solutions-deploy/find-support-details.png" alt-text="ソリューションのサポートモデルを見つけることができる場所のスクリーンショット。" lightbox="media/sentinel-solutions-deploy/find-support-details.png":::

サポートに連絡する際には、発行元、プロバイダー、プラン ID の値など、ソリューションに関するその他の詳細情報が必要になる場合があります。 これらの各項目は、ソリューションの詳細ページの **[使用状況情報 & サポート]** タブで確認できます。例えば：

:::image type="content" source="media/sentinel-solutions-deploy/usage-support.png" alt-text="ソリューションの使用状況とサポートの詳細のスクリーンショット。":::

## <a name="next-steps"></a>次の手順

このドキュメントでは、Microsoft Sentinel ソリューションと、組み込みコンテンツを検索してデプロイする方法について説明しました。

- [Microsoft Sentinel ソリューション](sentinel-solutions.md)に関する詳細を確認します。
- 完全な [Microsoft Sentinel ソリューション カタログ](sentinel-solutions-catalog.md)に関するページを参照してください。
