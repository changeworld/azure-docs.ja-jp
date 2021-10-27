---
title: Azure Monitor ブックを使用して Azure Database for MySQL フレキシブル サーバーを監視する
description: この記事では、Azure Monitor ブックを使用して Azure Database for MySQL フレキシブル サーバーを監視する方法について説明します。
author: SudheeshGH
ms.author: sunaray
ms.service: mysql
ms.topic: conceptual
ms.date: 10/01/2021
ms.openlocfilehash: aa26531dd9f644b4dc5b3343674abd7920fe57dd
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130064592"
---
# <a name="monitor-azure-database-for-mysql-flexible-server-by-using-azure-monitor-workbooks"></a>Azure Monitor ブックを使用して Azure Database for MySQL フレキシブル サーバーを監視する

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

Azure Database for MySQL フレキシブル サーバーは、Azure Monitor ブックに統合されるようになりました。 ブックを使用すると、データを分析し、Azure portal 内に豊富な視覚レポートを作成するための柔軟なキャンバスが得られます。 ブックを使用して、Azure 全体の複数のデータ ソースにアクセスし、それらを結合して統合された対話型エクスペリエンスにすることができます。 ブックのテンプレートは、複数のユーザーとチームが柔軟に再利用できるよう設計されている、精選されたレポートとして機能します。 

テンプレートを開くと、テンプレートの内容を含む一時ブックが作成されます。 この統合により、サーバーがブックといくつかのサンプル テンプレートにリンクされます。これは、サービスを大規模に監視するのに役立ちます。 これらのテンプレートを編集して、要件に合わせてカスタマイズし、ダッシュボードにピン留めして、焦点の合った整理された Azure リソースのビューを作成できます。
 
この記事では、フレキシブル サーバーで使用できる、さまざまなブック テンプレートについて説明します。

Azure Database for MySQL フレキシブル サーバーには、3 つの利用可能なテンプレートがあります。
 
- **概要**: サーバー上のリソース使用率を把握するのに役立つ、インスタンスの概要と最上位レベルのメトリックが表示されます。 このテンプレートには、次のビューが表示されます。

    * サーバーの概要 
    * データベースの概要
    * 接続のメトリック 
    * パフォーマンス メトリック 
    * ストレージのメトリック 

* **監査**: サーバーで収集された監査イベントの概要と詳細が表示されます。 このテンプレートには、次のビューが表示されます。

    * サービスでの管理操作
    * 監査の概要
    * 接続イベントの監査の概要
    * 接続イベントの監査
    * テーブル アクセスの概要
    * 識別されたエラー

* **クエリ パフォーマンスの分析情報**: インスタンス上のクエリのワークロードの概要と詳細、実行時間の長いクエリ、低速クエリ分析、および接続のメトリック。 このテンプレートには、次のビューが表示されます。

    * クエリの読み込み
    * アクティブな接続の合計
    * 低速クエリの傾向 (クエリ時間が 10 秒を超えるもの)
    * 低速クエリの詳細
    * 長さが上位 5 件のクエリを一覧表示する
    * 低速クエリの最小、最大、平均、および標準偏差のクエリ時間を集計する

これらのテンプレートを編集し、要件に従ってカスタマイズすることもできます。 詳細については、[Azure Monitor ブックの概要](../../azure-monitor/visualize/workbooks-overview.md#editing-mode)に関する記事を参照してください。

 ## <a name="access-the-workbook-templates"></a>ブック テンプレートにアクセスする

Azure portal でテンプレートを表示するには、Azure Database for MySQL フレキシブル サーバーの **[監視]** ペインに移動し、**[ブック]** を選択します。

:::image type="content" source="./media/concept-workbook/monitor-workbooks-all.png" alt-text="[ブック] ペインの [概要]、[監査]、および [クエリ パフォーマンスの分析情報] を示すスクリーンショット":::。

**[パブリック テンプレート ]** ペインに移動して、テンプレートの一覧を表示することもできます。

:::image type="content" source="./media/concept-workbook/monitor-workbooks-public.png" alt-text="[パブリック テンプレート] ペインの [概要]、[監査]、および [クエリ パフォーマンスの分析情報] テンプレートを示している図。":::


## <a name="next-steps"></a>次のステップ
- Azure Monitor ブックの[アクセス制御](../../azure-monitor/visualize/workbooks-access-control.md)について確認します。
- Azure Monitor ブックの[視覚化オプション](../../azure-monitor/visualize/workbooks-overview.md#visualizations)について確認します。 
