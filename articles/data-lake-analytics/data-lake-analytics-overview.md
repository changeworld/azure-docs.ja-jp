---
title: "Microsoft Azure Data Lake Analytics の概要 | Microsoft Docs"
description: "Data Lake Analytics は Azure のビッグ データ サービスであり、クラウドのデータから得られた洞察を活用し、データを使用してビジネスを推進できます。その場所やサイズは関係ありません。"
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: saveenr
editor: cgronlun
ms.assetid: 1e1d443a-48a2-47fb-bc00-bf88274222de
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/23/2017
ms.author: saveenr
ms.openlocfilehash: 316c35fa4b04bdb251c2b9ae14f6b32f4e4bf939
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2018
---
# <a name="overview-of-microsoft-azure-data-lake-analytics"></a>Microsoft Azure Data Lake Analytics の概要

## <a name="what-is-azure-data-lake-analytics"></a>Azure Data Lake Analytics とは
Azure Data Lake Analytics は、ビッグ データを簡略化するオンデマンド分析ジョブ サービスです。 ハードウェアのデプロイ、構成、チューニングを行う代わりに、クエリを作成してデータを変換し、価値ある洞察を抽出します。 この分析サービスでは、必要な性能をダイヤルで設定して、どのような規模のジョブでも即座に処理できます。 ジョブの実行中にのみ課金されるコスト効率の良いサービスです。 この分析サービスは、SQL のメリットと命令型コードの強みを融合した U-SQL 言語をサポートしています。 Azure の SQL Server、Data Lake Store、Azure SQL Database、Azure SQL Data Warehouse にまたがるデータを U-SQL で効率よく分析することができます。

## <a name="dynamic-scaling"></a>動的スケーリング
  
Data Lake Analytics は、クラウドのスケールとパフォーマンスを考慮して設計されています。  リソースを動的にプロビジョニングするので、テラバイト規模のデータや、エクサバイト規模のデータも分析できます。 ジョブが完了するとリソースが自動的に縮小され、使用した処理能力の分だけのお支払いで済みます。 保存するデータのサイズや使用するコンピューティング リソースの量を増やしたり減らしたりする際に、コードを書き直す必要はありません。 そのため、大規模なデータセットの処理や保存の仕方に悩むことなく、ビジネス ロジックに集中できます。

## <a name="develop-faster-debug-and-optimize-smarter-using-familiar-tools"></a>使い慣れたツールで、開発を迅速に、デバッグと最適化をスマートに
  
Data Lake Analytics は Visual Studio と密に連携しているため、使い慣れたツールでコードの実行、デバッグ、調整を行うことができます。 U-SQL ジョブが視覚化されるので、コードがどのように大規模に実行されるのかを見ることができます。そのため、パフォーマンスのボトルネックを簡単に特定し、コストを最適化できます。


## <a name="u-sql-simple-and-familiar-powerful-and-extensible"></a>U-SQL: シンプルで使いやすく、強力で拡張が可能
  
Data Lake Analytics には U-SQL が含まれています。これは、SQL のシンプルで使いやすい宣言的な特性を C# の表現力で拡張するクエリ言語です。 U-SQL 言語は、Microsoft 内のビッグ データ システムを支えているのと同じ分散ランタイムを基礎にしています。 SQL や .NET の何百万人もの開発者が、既に身に付けているスキルでデータを処理して分析できるようになります。

## <a name="integrates-seamlessly-with-your-it-investments"></a>既存の IT 投資とシームレスに統合
  
Data Lake Analytics では、ID、管理、セキュリティ、データ ウェアハウジングのために既存の IT 投資を使用できます。 この方法により、データ ガバナンスがシンプルになり、現在のデータ アプリケーションも容易に拡張できます。 Data Lake Analytics はユーザー管理とアクセス許可のための Active Directory と統合されており、監視と監査も組み込まれています。

## <a name="affordable-and-cost-effective"></a>リーズナブルな料金と高いコスト効率

Data Lake Analytics は、ビッグ データ ワークロードを実行するためのコスト効率の良いソリューションです。 データが処理されたときに、ジョブ ベースで料金が発生します。 ハードウェア、ライセンス、サービス固有のサポート契約は必要ありません。 ジョブの開始や完了に応じて、システムのスケールアップとスケールダウンが自動的に行われるため、必要以上に課金されることはありません。 詳細については、[コストの管理と節約](https://1drv.ms/f/s!AvdZLquGMt47h213Hg3rhl-Tym1c)に関するページを参照してください。
    
## <a name="works-with-all-your-azure-data"></a>すべての Azure データに対応
  
Data Lake Analytics は、最高レベルのパフォーマンス、スループット、および並列化のために Azure Data Lake Store と連携し、Azure Storage BLOB、Azure SQL Database、Azure Warehouse とも連動します。

## <a name="next-steps"></a>次の手順
 
  * [Azure Portal](data-lake-analytics-get-started-portal.md) | [Azure PowerShell](data-lake-analytics-get-started-powershell.md) | [CLI](data-lake-analytics-get-started-cli2.md) で Data Lake Analytics の使用を開始する
  * [Azure portal](data-lake-analytics-manage-use-portal.md) | [Azure PowerShell](data-lake-analytics-manage-use-powershell.md) | [CLI](data-lake-analytics-manage-use-cli.md) | [Azure .NET SDK](data-lake-analytics-manage-use-dotnet-sdk.md) | [Node.js](data-lake-analytics-manage-use-nodejs.md) でAzure Data Lake Analytics を管理する
  * [Data Lake Analytics に関するコストを管理して節約する方法](https://1drv.ms/f/s!AvdZLquGMt47h213Hg3rhl-Tym1c)
