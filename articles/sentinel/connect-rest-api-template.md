---
title: データ ソースを Azure Sentinel の Data Collector API に接続してデータを取り込む | Microsoft Docs
description: 外部のシステムを Azure Sentinel の Data Collector API に接続し、ワークスペースのカスタム ログにログのデータを取り込む方法を説明します。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/17/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: ed50304d02026a9cb0eafc818116fcd32035488d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131046966"
---
# <a name="connect-your-data-source-to-the-azure-sentinel-data-collector-api-to-ingest-data"></a>データ ソースを Azure Sentinel のデータ コレクター API に接続してデータを取り込む

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

サードパーティ ベンダー製 API との連携機能では、製品のデータ ソースからデータをプルし、Azure Sentinel の [Azure Monitor Data Collector API](../azure-monitor/logs/data-collector-api.md) に接続して、Azure Sentinel ワークスペースのカスタム ログ テーブルにデータをプッシュします。

ほとんどの場合、こうしたデータ ソースを Azure Sentinel に接続する設定を行うのに必要な情報は、各ベンダーのドキュメントに記載されています。

[データ コネクタの参考情報](data-connectors-reference.md)に関するページで目的の製品のセクションを確認し、そこに記載された情報と、ベンダーによる説明へのリンクを利用してください。

> [!NOTE]
> データは、Azure Sentinel を実行しているワークスペースの地理的な場所に格納されます。

## <a name="prerequisites"></a>前提条件

- Azure Sentinel ワークスペースに対する読み取りおよび書き込みアクセス許可が必要です。

- ワークスペースの共有キーに対する読み取りアクセス許可が必要です。 ワークスペース キーの詳細については、[こちら](../azure-monitor/agents/agent-windows.md)を参照してください。

## <a name="configure-and-connect-your-data-source"></a>データ ソースを設定、接続する

1. Azure Sentinel のポータルのナビゲーション メニューで **[Data connectors]\(データ コネクタ\)** をクリックします。

1. データ コネクタのギャラリーで製品の項目を選択し **[Open connector page]\(コネクタのページを開く\)** をクリックします。

1. コネクタのページに表示される手順に従うか、そこに表示されるリンクからベンダーによる説明に移動します。

1. ワークスペース ID と主キーを求められたら、データ コネクタのページでそれらをコピーし、ベンダーの説明に従って構成に貼り付けます。 次の例を見てください。

    :::image type="content" source="media/connect-rest-api-template/workspace-id-primary-key.png" alt-text="ワークスペース ID と主キー":::

## <a name="find-your-data"></a>データの検索

接続に成功したら、 **[CustomLogs]** セクションの **[Logs]\(ログ\)** にデータが表示されます。 [データ コネクタの参考情報](data-connectors-reference.md)に関するページで目的の製品のセクションを見て、テーブル名を確認してください。

データを取得するクエリを製品から実行するには、それらのテーブル名をクエリで指定します。

Log Analytics でログの表示が開始されるまで、最大 20 分かかる場合があります。

## <a name="next-steps"></a>次のステップ

このドキュメントでは、外部のデータ ソースを Azure Sentinel の Data Collector API に接続する方法を説明します。 これらのデータ コネクタに組み込まれた機能を有効に活用するには、データ コネクタのページの **[Next steps]\(次のステップ\)** タブに移動します。 そこには、クエリ、ブック、分析ルールのテンプレートのサンプルがいくつか用意されているので、作業の参考にできます。

Azure Sentinel の詳細については、次の記事をご覧ください。

- [データと潜在的な脅威を可視化](get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](detect-threats-built-in.md)の概要。
- [ブックを使用](monitor-your-data.md)してデータを監視する。
