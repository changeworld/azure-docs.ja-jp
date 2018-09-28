---
title: Azure データ エクスプローラーとは
description: Azure データ エクスプローラーは、ログと利用統計情報データのための高速で拡張性に優れたデータ探索サービスです。
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: overview
ms.date: 09/24/2018
ms.openlocfilehash: ca6159554b58ce04252901f990b1ef0bea6c0cac
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "46953159"
---
# <a name="what-is-azure-data-explorer"></a>Azure データ エクスプローラーとは

Azure データ エクスプローラーは、ログと利用統計情報データのための高速で拡張性に優れたデータ探索サービスです。 最新のソフトウェアによって生成される多数のデータ ストリームを処理できるので、データを収集、保存、分析できます。 Azure データ エクスプローラーは、Web サイト、アプリケーション、IoT デバイスなどのデータ ソースの大量の多様なデータの分析に最適です。 このデータは、診断、監視、報告、機械学習、付加的分析の各種機能に利用されます。 Azure データ エクスプローラーを利用することでこのデータの取り込みがシンプルになり、データに対する複雑なクエリやアドホック クエリを数秒で実行できます。

## <a name="what-makes-azure-data-explorer-unique"></a>Azure データ エクスプローラーを唯一無二の存在にしている要素は何ですか?

- テラバイト単位のデータに数分でスケール アップします。データ探索を高速で繰り返し、関連する分析情報を見つけることができます。

- 高性能なデータ分析のために最適化された画期的なクエリ言語を提供します。

- 大量の異種データ (構造化データと非構造化データ) を分析します。

- 他のサービスと組み合わせることで、包括的で対話型の高性能データ分析ソリューションを提供します。それにより、まさに必要としているものを構築し、デプロイすることができます。

## <a name="data-warehousing-workflow"></a>データ ウェアハウスのワークフロー

Azure データ エクスプローラーは他の主要なサービスと統合され、データの収集、取り込み、保管、インデックス作成、クエリ実行、視覚化を含むエンドツーエンド ソリューションを提供します。 テラバイト単位の多様な未加工データでフローの **EXPLORE** ステップを実行することで、データ ウェアハウスにおいて中心的な役割を果たします。

![データ ウェアハウスの図](media/data-explorer-overview/data-warehouse.png)

Azure データ エクスプローラーは、イベント ハブなどの一般サービスへの接続、.NET や Python など、SDK を利用したプログラミングによる取り込み、探索目的でのエンジンへの直接アクセスなど、さまざまな取り込み手法に対応しています。 Azure データ エクスプローラーは分析サービスやモデル化サービスと統合され、データをさらに分析したり、視覚化したりできます。

## <a name="azure-data-explorer-flow"></a>Azure データ エクスプローラーのフロー

次は、Azure データ エクスプローラーの使用をさまざまな側面から見た図です。

![Azure データ エクスプローラーのフロー](media/data-explorer-overview/workflow.png)

Azure データ エクスプローラーの使用は一般的に次のパターンに従います。

1. **データベースの作成:** *クラスター*を作成し、そのクラスターで 1 つまたは複数の*データベース*を作成します。 [クイック スタート: Azure データ エクスプローラー クラスターとデータベースを作成する](create-cluster-database-portal.md)

1. **データの取り込み:** クエリを実行できるように、データをデータベース テーブルに読み込みます。 [クイック スタート: イベント ハブから Azure データ エクスプローラーにデータを取り込む](ingest-data-event-hub.md)

1. **クエリ データベース:** Microsoft の Web アプリケーションを使用し、クエリを実行して結果を確認し、共有します。 これは Azure portal で利用できます。また、スタンドアロン アプリケーションとして利用できます。 さらに、(SDK を利用して) プログラミングでクエリを送信したり、REST API エンドポイントにクエリを送信したりできます。 [クイック スタート: Azure データ エクスプローラーでデータのクエリを実行する](web-query-data.md)

## <a name="query-experience"></a>クエリの利用

Azure データ エクスプローラーのクエリは読み取り専用の要求であり、データを処理し、その処理の結果を返します。データやメタデータが修正されることはありません。 分析を完了するまでクエリの微調整を続けます。 このプロセスは、Azure データ エクスプローラーを利用してアドホック クエリを非常に速く実行できるようになるため、簡単になります。

Azure データ エクスプローラーでは、大量の構造化データ、半構造化データ (JSON に似た、入れ子にされた型)、非構造化データ (フリーテキスト) が等しく処理されます。 特定のテキスト語句を検索したり、特定のイベントを見つけたり、構造化データにメトリックスタイルの計算を実行したりできます。 Azure データ エクスプローラーは、自由形式のテキスト フィールドから実行時に値を抽出することで、構造化されていないテキスト ログの領域と構造化されている数字や次元の領域の橋渡しとなります。 データ探索は、高速のテキスト インデックス作成、列ストア、時系列操作を組み合わせることで簡素化されます。

Azure データ エクスプローラー機能は、[Log Analytics](/azure/log-analytics/)、[Application Insights](/azure/application-insights/)、[Time Series Insights](/azure/time-series-insights/)、[Windows Defender Advanced Threat Protection](/windows/security/threat-protection/windows-defender-atp/windows-defender-advanced-threat-protection/) など、その高性能なクエリ言語を基盤に構築された他のサービスにより拡張されます。

## <a name="feedback"></a>フィードバック

Azure データ エクスプローラーとそのクエリ言語に関するフィードバックをお待ちしております。次の方法をご利用ください。

- [フォーラム MSDN で質問する](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureKusto)

- [ユーザーの声で製品について提案する](http://aka.ms/AzureDataExplorer.UserVoice)

## <a name="next-steps"></a>次の手順

[クイック スタート: Azure データ エクスプローラー クラスターとデータベースを作成する](create-cluster-database-portal.md)

[クイック スタート: イベント ハブから Azure データ エクスプローラーにデータを取り込む](ingest-data-event-hub.md)

[クイック スタート: Azure データ エクスプローラーでデータのクエリを実行する](web-query-data.md)
