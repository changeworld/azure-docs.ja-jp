---
title: "異常な定期的ジョブをトラブルシューティングする方法 | Microsoft Docs"
description: "Azure Data Lake Tools for Visual Studio Code を使って異常な定期的ジョブをデバッグする方法について説明します。"
services: data-lake-analytics
documentationcenter: 
author: yanancai
manager: 
editor: 
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/27/2017
ms.author: yanacai
ms.openlocfilehash: a358f94b117c12511028a875e56b5c9dba8d3382
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-troubleshoot-an-abnormal-recurring-job"></a>異常な定期的ジョブをトラブルシューティングする方法

このドキュメントでは、[Azure Data Lake Tools for Visual Studio](http://aka.ms/adltoolsvs) を使って定期的なジョブの問題をトラブルシューティングする方法について説明します。 パイプラインおよび定期的なジョブについて詳しくは、[こちら](https://blogs.msdn.microsoft.com/azuredatalake/2017/09/19/managing-pipeline-recurring-jobs-in-azure-data-lake-analytics-made-easy/)をご覧ください。
通常、定期的なジョブは同じクエリ ロジックと似た入力データを共有します。 たとえば、毎週月曜日の午前 8 時に実行して 前週のアクティブなユーザーをカウントする定期的なジョブがあり、これらのジョブのスクリプトはクエリ ロジックを含む 1 つのスクリプト テンプレートを共有し、これらのジョブの入力は過去 1 週間の使用状況データであるような場合です。 同じクエリ ロジックを共有して入力が似ていると、通常はこれらのジョブのパフォーマンスは似ていて安定しています。定期的なジョブのいずれかで突然異常な実行、障害、速度低下が大量に発生した場合、次のことを行います。

1.  定期的なジョブのプレビュー実行の統計情報レポートを見て、何が起きたかを確認します。
2.  異常なジョブと正常なジョブを比較して、何が変化したかを明らかにします。

Azure Data Lake Tools for Visual Studio の**関連ジョブ ビュー**を使うと、どちらの場合についてもトラブルシューティングがはかどります。

## <a name="step-1-find-recurring-jobs-and-open-related-job-view"></a>ステップ 1: 定期的なジョブを探して関連ジョブ ビューを開く

関連ジョブ ビューを使って定期的なジョブの問題をトラブルシューティングするには、最初に Visual Studio で定期的なジョブを探してから、関連ジョブ ビューを開く必要があります。

### <a name="case-1-you-have-the-url-for-the-recurring-job"></a>ケース 1: 定期的なジョブの URL がわかっている

**[ツール] > [Data Lake] > [ジョブ ビュー]** の順に選び、ジョブの URL を貼り付けて Visual Studio でジョブ ビューを開き、[関連ジョブの表示] を選んで関連ジョブ ビューを開きます。

![Data Lake Analytics Tools の関連ジョブの表示](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/view-related-job.png)
 
### <a name="case-2-you-have-the-pipeline-for-the-recurring-job-but-not-the-url"></a>ケース 2: 定期的なジョブのパイプラインはあるが、URL はわからない

Visual Studio で、**[サーバー エクスプローラー] > Data Lake Analytics のアカウント > [パイプライン]** の順に選んで、パイプライン ブラウザーを開くことができます ([サーバー エクスプローラー] でこのノードが見つからない場合は、[こちら](http://aka.ms/adltoolsvs)で最新のツールを入手してください)。 パイプライン ブラウザーでは、ADLA アカウントのすべてのパイプラインが左側に表示されます。パイプラインを展開してすべての定期的なジョブを表示し、問題があるジョブをクリックすると、右側に関連ジョブ ビューが開きます。

![Data Lake Analytics Tools の関連ジョブの表示](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/pipeline-browser.png)

![Data Lake Analytics Tools の関連ジョブの表示](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-view.png)

## <a name="step-2-analyze-statistics-report"></a>ステップ 2: 統計レポートを分析する

概要および統計レポートは関連ジョブ ビューの上部に表示され、そこから異常の可能性のある根本原因を発見できます。 

1.  最初に、レポートで異常なジョブを探す必要があります。 X 軸はジョブの送信時刻を示し、そこから異常なジョブを見つけることができます。
2.  以下の手順で統計を確認し、異常の詳細情報と考えられる解決策を取得します。

![Data Lake Analytics Tools の関連ジョブの表示](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-metrics-debugging-flow.png)

## <a name="step-3-compare-the-abnormal-recurring-job-to-a-normal-job"></a>ステップ 3: 異常な定期的ジョブと正常なジョブを比較する

関連ジョブ ビューの下部にあるジョブの一覧で、すべての送信された定期的なジョブを見つけることができます。 右クリックし、ジョブの相違点ビューで異常なジョブと以前の正常なジョブを比較して、詳細情報と可能性のある解決策を発見できます。

![Data Lake Analytics Tools の関連ジョブの表示](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/compare-job.png)

通常、これら 2 つのジョブの大きな違いに注意を払う必要があります。パフォーマンスの問題を引き起こした理由と思われます。以下の手順でさらに確認することができます。

![Data Lake Analytics Tools の関連ジョブの表示](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-diff-debugging-flow.png)

## <a name="next-steps"></a>次のステップ

* [データ スキューの問題をデバッグして解決する方法](data-lake-analytics-data-lake-tools-data-skew-solutions.md)
* [U-SQL ジョブの障害でユーザー定義コードのエラーをデバッグする方法](data-lake-analytics-debug-u-sql-jobs.md)