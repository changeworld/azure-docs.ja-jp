---
title: Azure Sentinel ソリューションをデプロイする | Microsoft Docs
description: この記事では、データ コネクタ付属のデータ分析ツールを簡単に見つけ、デプロイする方法を説明します。
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/05/2021
ms.author: yelevin
ms.openlocfilehash: 5cb84ad912e5430948d0dedc71d94e69d1dd1a86
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/31/2021
ms.locfileid: "123251912"
---
# <a name="discover-and-deploy-azure-sentinel-solutions"></a>Azure Sentinel ソリューションを見つけてデプロイする

> [!IMPORTANT]
>
> Azure Sentinel ソリューションは現在 **プレビュー版** です。個別のソリューション パッケージもすべてプレビュー版です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

Azure Sentinel ソリューションでは、製品内検出機能、ワンステップのデプロイ方法、エンドツーエンド製品の実現手段、ドメイン、Azure Sentinel サービス間の垂直的利用を提供しています。 ソリューションの検索、デプロイ、有効化は [Azure Marketpace](https://azuremarketplace.microsoft.com/marketplace) で、ソリューションの作成と公開は [Microsoft パートナー センター](/partner-center/overview) で行います。

ソリューションは次のコンポーネントの一部または全部で構成できます。

- **データ コネクタ** (一部は **パーサー** が付属します)
- **ブック**
- **分析ルール**
- **ハンティング クエリ**
- **プレイブック**

## <a name="find-your-solution"></a>自分たちに適したソリューションを見つける

1. Azure Sentinel のナビゲーション メニューで、 **[ソリューション (プレビュー)]** をクリックします。

1. **[ソリューション]** ブレードに、ソリューションの検索可能なリストが表示されます。

    :::image type="content" source="./media/sentinel-solutions-deploy/solutions-list.png" alt-text="ソリューションのリスト":::

    - リスト下部までスクロールしても望ましいソリューションが見つからない場合は、一番下の **[さらに読み込む]** リンクを選択して、リストを展開します。

        :::image type="content" source="./media/sentinel-solutions-deploy/load-more.png" alt-text="ソリューションをさらに読み込む":::

1. 選択肢を絞って求めるソリューションを見つけやすくするには、リストの上の **[検索]** 欄にソリューションの名前の一部を入力します。 (この検索エンジンでは、単語未満の文字は認識されせん。)

    :::image type="content" source="./media/sentinel-solutions-deploy/solutions-search-1.png" alt-text="ソリューションを検索する":::

1. 求めるソリューションをリストから選んでデプロイします。 ソリューションの詳細ページは、 **[概要]** タブに開きます。ここに、ソリューションに関する重要情報が表示されます。

    :::image type="content" source="./media/sentinel-solutions-deploy/proofpoint-tap-solution.png" alt-text="Proofpoint Tap ソリューション":::

1. 他にも、ソリューションに関する役立つ情報を **[プラン]** タブと **[使用量情報 + サポート]** タブで見ることができます。また、 **[レビュー]** タブで他の顧客の感想も読めます。

## <a name="deploy-your-solution"></a>ソリューションのデプロイ

1. **[作成]** をクリックしてソリューション デプロイ ウィザードを起動します。すると **[基本]** タブが開きます。

    :::image type="content" source="./media/sentinel-solutions-deploy/wizard-basics.png" alt-text="デプロイ ウィザードの [基本] タブ":::

1. ソリューションをデプロイするサブスクリプション、リソース グループ、ワークスペースを入力します。 

1. **[次へ]** を選択して残りのタブ (ソリューションのコンポーネントに対応) を移動します。そこで各コンポーネントについて学習し、場合によってはそれらを構成することもできます。

    > [!NOTE]
    > 下に挙げるタブは、対応するスクリーンショットに示されたソリューションのコンポーネントに対応しています。 コンポーネントの種類はソリューションによって異なるため、ソリューションにより表示されるタブが異なる場合があり、下に示していないタブが表示される場合もあります。

    1. **[分析]** タブ :::image type="content" source="./media/sentinel-solutions-deploy/wizard-analytics.png" alt-text="デプロイ ウィザードの [分析] タブ":::

    1. **[ブック]** タブ :::image type="content" source="./media/sentinel-solutions-deploy/wizard-workbooks.png" alt-text="デプロイ ウィザードの [ブック] タブ":::

    1. **[プレイブック]** タブ - ここでは Proofpoint TAP の有効なサインイン情報を入力する必要があります。そうすると、プレイブックで Proofpoint システムを認証し所定の応答を実行できます。
        :::image type="content" source="./media/sentinel-solutions-deploy/wizard-playbooks.png" alt-text="デプロイ ウィザード の [プレイブック] タブ":::

1. 最後に、 **[確認と作成] タブ** で、「検証に成功しました」メッセージが表示されるのを待ってから、 **[作成]** を選択してソリューションをデプロイします。 **[自動化テンプレートのダウンロード]** リンクをクリックし、ソリューションをコードとしてデプロイすることもできます。

    :::image type="content" source="./media/sentinel-solutions-deploy/wizard-create.png" alt-text="デプロイ ウィザードの [確認 + 作成] タブ":::

## <a name="next-steps"></a>次の手順

このドキュメントでは、Azure Sentinel ソリューションと、それを見つけてデプロイする方法について説明しました。

- [Azure Sentinel ソリューション](sentinel-solutions.md)について知る。
- すべての [Sentinel ソリューションのカタログ](sentinel-solutions-catalog.md)を見る。
