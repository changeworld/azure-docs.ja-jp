---
title: データ ソースを Microsoft Sentinel データ コレクター API に接続してデータを取り込む | Microsoft Docs
description: 外部システムを Microsoft Sentinel データ コレクター API に接続して、そのログ データをワークスペース内のカスタム ログに取り込む方法について説明します。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 733ce3565970a6f858edfe32386a21d7655345a6
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132721824"
---
# <a name="connect-your-data-source-to-the-microsoft-sentinel-data-collector-api-to-ingest-data"></a>データ ソースを Microsoft Sentinel データ コレクター API に接続してデータを取り込む

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

サードパーティ ベンダーによって構築された API 統合では、その製品のデータ ソースからデータをプルし、Microsoft Sentinel の [Azure Monitor Data Collector API](../azure-monitor/logs/data-collector-api.md) に接続して、Microsoft Sentinel ワークスペース内のカスタム ログ テーブルにデータをプッシュします。

ほとんどの場合、Microsoft Sentinel に接続するようにこれらのデータ ソースを構成するために必要な情報はすべて、各ベンダーのドキュメントで見つけることができます。

[データ コネクタの参考情報](data-connectors-reference.md)に関するページで目的の製品のセクションを確認し、そこに記載された情報と、ベンダーによる説明へのリンクを利用してください。

> [!NOTE]
> データは、Microsoft Sentinel を実行しているワークスペースの地理的な場所に格納されます。

## <a name="prerequisites"></a>前提条件

- Microsoft Sentinel ワークスペースに対する読み取りおよび書き込みアクセス許可が必要です。

- ワークスペースの共有キーに対する読み取りアクセス許可が必要です。 ワークスペース キーの詳細については、[こちら](../azure-monitor/agents/agent-windows.md)を参照してください。

## <a name="configure-and-connect-your-data-source"></a>データ ソースを設定、接続する

1. Microsoft Sentinel ポータルで、ナビゲーション メニューにある **[データ コネクタ]** を選択します。

1. データ コネクタのギャラリーで製品の項目を選択し **[Open connector page]\(コネクタのページを開く\)** をクリックします。

1. コネクタのページに表示される手順に従うか、そこに表示されるリンクからベンダーによる説明に移動します。

1. ワークスペース ID と主キーを求められたら、データ コネクタのページでそれらをコピーし、ベンダーの説明に従って構成に貼り付けます。 次の例を見てください。

    :::image type="content" source="media/connect-rest-api-template/workspace-id-primary-key.png" alt-text="ワークスペース ID と主キー":::

## <a name="find-your-data"></a>データの検索

接続に成功したら、 **[CustomLogs]** セクションの **[Logs]\(ログ\)** にデータが表示されます。 [データ コネクタの参考情報](data-connectors-reference.md)に関するページで目的の製品のセクションを見て、テーブル名を確認してください。

データを取得するクエリを製品から実行するには、それらのテーブル名をクエリで指定します。

Log Analytics でログの表示が開始されるまで、最大 20 分かかる場合があります。

## <a name="next-steps"></a>次のステップ

このドキュメントでは、外部データ ソースを Microsoft Sentinel データ コレクター API に接続する方法を学習しました。 これらのデータ コネクタに組み込まれた機能を有効に活用するには、データ コネクタのページの **[Next steps]\(次のステップ\)** タブに移動します。 そこには、クエリ、ブック、分析ルールのテンプレートのサンプルがいくつか用意されているので、作業の参考にできます。

Microsoft Sentinel の詳細については、次の記事を参照してください。

- [データと潜在的な脅威を可視化](get-visibility.md)する方法についての説明。
- [Microsoft Sentinel を使用した脅威の検出](detect-threats-built-in.md)の概要。
- [ブックを使用](monitor-your-data.md)してデータを監視する。
