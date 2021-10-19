---
title: Azure Monitor ブックを使用して Azure Database for MySQL - フレキシブル サーバーを監視する
description: Azure Monitor ブックを使用して Azure Database for MySQL - フレキシブル サーバーを監視する方法について説明します。
author: SudheeshGH
ms.author: sunaray
ms.service: mysql
ms.topic: conceptual
ms.date: 10/01/2021
ms.openlocfilehash: a773048b2d1ddf8ad7b7993ef7975517506bbd07
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/06/2021
ms.locfileid: "129620916"
---
# <a name="monitoring-azure-database-for-mysql---flexible-server-with-azure-monitor-workbooks"></a>Azure Monitor ブックを使用して Azure Database for MySQL - フレキシブル サーバーを監視する

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

Azure Database for MySQL - フレキシブル サーバーが、Azure Monitor ブックに統合されました。 ブックは、Azure portal 内でデータを分析し、高度な視覚的レポートを作成するための柔軟なキャンバスを提供します。 これにより、Azure 全体から複数のデータ ソースを活用し、それらを結合して、統合された対話型エクスペリエンスにすることができます。 ブックのテンプレートは、複数のユーザーとチームが柔軟に再利用できるよう設計されている、精選されたレポートとして機能します。 テンプレートを開くと、テンプレートの内容が設定された一時的なブックが作成されます。 この統合により、サーバーにはブックやいくつかのサンプル テンプレートへのリンクが用意され、大規模なサービスの監視に役立ちます。これらのテンプレートは、編集したり、顧客の要求に合わせてカスタマイズしたり、ダッシュボードにピン留めして Azure リソースに焦点を絞ったビューを作成したりすることができます。
 
この記事では、フレキシブル サーバーで使用できる、さまざまなブック テンプレートについて説明します

Azure Database for MySQL - フレキシブル サーバーで使用できる既定のテンプレートは、3 つあります
 
- **概要:** サーバー上のリソース使用率を把握するのに役立つ、インスタンスの概要と最上位レベルのメトリックが表示されています。 Azure Database for MySQL - フレキシブル サーバーに関する、以下の詳細を確認できます

    * サーバーの概要 
    * データベースの概要
    * 接続のメトリック 
    * パフォーマンス メトリック 
    * ストレージのメトリック 

* **監査:** サーバーで収集された監査イベントの概要と詳細。 Azure Database for MySQL - フレキシブル サーバーのこのテンプレートでは、以下のビューを利用することができます

    * サービスに対する管理アクション
    * 監査の概要
    * 接続イベントの監査の概要
    * 接続イベントの監査
    * テーブル アクセスの概要
    * 識別されたエラー

* **クエリ パフォーマンスの分析情報:** インスタンス上のクエリのワークロードの概要と詳細、実行時間の長いクエリ、低速クエリ分析、および接続のメトリック。 Azure Database for MySQL - フレキシブル サーバーのこのテンプレートでは、以下のビューを利用することができます。

    * クエリの負荷
    * アクティブな接続の合計
    * 低速クエリの傾向 (クエリ時間が 10 秒を超えるもの)
    * 低速クエリの詳細
    * 上位 5 件の最長クエリを表示する
    * 低速クエリの最小、最大、平均、および標準偏差のクエリ時間を集計する

これらのテンプレートを編集し、要件に従ってカスタマイズすることもできます。 詳細については、[Azure Monitor ブックの概要 - Azure Monitor](../../azure-monitor/visualize/workbooks-overview.md#editing-mode) に関するページを参照してください

 ## <a name="how-to-access-workbook-templates"></a>ブック テンプレートにアクセスする方法

テンプレートを表示するには、Azure portal で、Azure Database for MySQL - フレキシブル サーバーの **[監視]** ブレードに移動し、 **[Workbooks]** を選択します。

:::image type="content" source="./media/concept-workbook/monitor-workbooks-all.png" alt-text="ブックを示す図。":::

テンプレートの一覧は、 **[パブリック テンプレート]** に移動することでも表示できます。

:::image type="content" source="./media/concept-workbook/monitor-workbooks-public.png" alt-text="ブックのテンプレートを示す図":::


## <a name="next-steps"></a>次の手順
- [Azure Monitor ブック](../../azure-monitor/visualize/workbooks-access-control.md)と、ブックの豊富な視覚化オプションを確認します。
- [Azure Monitor ブックの使用を開始](../../azure-monitor/visualize/workbooks-overview.md#visualizations)し、ブックの豊富な視覚化オプションを確認します。
