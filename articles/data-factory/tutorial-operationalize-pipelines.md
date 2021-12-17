---
title: データ パイプラインを運用化する
description: データ パイプラインのサービス レベル アグリーメントを提供する方法について説明します
ms.service: data-factory
ms.subservice: orchestration
author: chez-charlie
ms.author: chez
ms.reviewer: jburchel
ms.topic: tutorial
ms.date: 09/22/2021
ms.openlocfilehash: d80d2abf4d51d99fb207ec728e29b362079ba64a
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2021
ms.locfileid: "129207547"
---
# <a name="deliver-service-level-agreement-for-data-pipelines"></a>データ パイプラインのサービス レベル アグリーメントを提供する

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory は世界中の企業に支持され、信頼されています。 これは、スケールアウトのサーバーレス データ統合とデータ変換のための Azure のネイティブ クラウド ETL サービスとして、データ パイプラインを実装してデータを準備、処理し、エンタープライズ データ ウェアハウスまたはデータ レイクに読み込むために、広く使用されています。

データ パイプラインは、Git モードで[継続的インテグレーションと継続的デリバリー (CI/CD)](continuous-integration-delivery.md) を通じて、またはライブ モードで直接公開された後、通常はオートパイロットで実行されます。 これらは、[スケジュール トリガー](how-to-create-schedule-trigger.md)または[タンブリング ウィンドウ トリガー](how-to-create-tumbling-window-trigger.md)を使用して、定義済みのタイムテーブルに基づいて実行するか、[ストレージ イベント トリガー](how-to-create-event-trigger.md)または[カスタム イベント トリガー](how-to-create-custom-event-trigger.md)を使用してイベント駆動型アーキテクチャで実行できます。 これらにはミッション クリティカル ワークロードが託されており、ビジネス レポートまたはデータ分析と機械学習プロジェクト用のデータが準備されます。

## <a name="preemptive-warnings-for-long-running-jobs"></a>実行時間の長いジョブに関する事前警告

これらのデータ パイプラインのサービス レベル アグリーメント (SLA) を提供する場合、2 つの大きな課題があります。

* アクティビティのコンピューティング環境 (たとえば、ストアド プロシージャ アクティビティの SQL) でスロットルが実行され、データ パイプライン全体の速度が低下し、パイプラインの SLA が損なわれる可能性があります。
* パイプライン開発者は、常にファクトリを積極的に監視し、SLA を満たさない実行時間の長いパイプラインを事前に探しているわけではありません。

これらの問題に対処するため、適切に構成されていれば、SLA が満たされない場合にパイプラインの実行によって "_経過時間パイプライン実行_" メトリックが出力されます。 [Data Factory のアラート](monitor-metrics-alerts.md#data-factory-alerts)と組み合わせることで、データ パイプライン開発者はより適切に SLA をお客様に提供することができます。パイプラインの予想実行時間を Microsoft に知らせておくと、パイプラインの実行時間が予想より長いときに Microsoft から事前に通知されます。

作成フェーズ中、アラートを作成する各パイプラインで、パイプライン キャンバスの空白領域をクリックしてパイプラインの設定に移動します。

:::image type="content" source="media/tutorial-operationalize-pipelines/elapsed-time-1-set-up.png" alt-text="パイプラインの予想実行時間を指定する方法を示すスクリーンショット。":::

__[設定]__ タブで、[Elapsed time metric]\(経過時間メトリック\) をオンにし、予想されるパイプラインの実行時間を `D.HH:MM:SS` 形式で指定します。 これは、ビジネス SLA (パイプラインがビジネス ニーズを満たすのにかかると思われる時間) に設定することを強くお勧めします。 パイプラインの期間がこの設定を超えると、Data Factory によって "_経過時間パイプライン実行_" メトリック (メトリック ID: `PipelineElapsedTimeRuns`) が Azure Monitor にログされます。 つまり、パイプラインが最終的に終了する前に、実行時間の長いパイプラインについて "_事前に_" 通知されます。

Microsoft では、一部のパイプラインは他のものより多くの手順を含んでいたり、より多くのデータを移動したりするために、完了により多くの時間がかかるのは当然であると理解しています。 実行時間の長いパイプラインでは、すべてに適した定義はありません。 SLA が必要なすべてのパイプラインで、それぞれしきい値を定義することをお勧めします。 また、特定のパイプラインのメトリックをログする場合は、予想される実行時間に関するそのユーザー定義の設定と比較されます。

> [!NOTE]
> これは、パイプラインごとのオプトイン機能です。 前述のパイプラインに予想される実行時間が指定されていない場合、パイプラインのメトリックがログされることはありません。

手順に従って、メトリックに [Data Factory のアラート](monitor-metrics-alerts.md#data-factory-alerts)を設定してください。 エンジニアには、介入して SLA を満たすための手順を行うように、電子メールまたは SMS を通じて通知が送られます。

## <a name="next-steps"></a>次のステップ

[Data Factory のメトリックとアラート](monitor-metrics-alerts.md)

[視覚的な監視](monitor-visually.md#alerts)
