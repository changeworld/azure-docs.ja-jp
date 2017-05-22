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
ms.date: 05/06/2017
ms.author: edmaca
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: cb2da3515cfe5fd460e16b019d3738f4a9a050bb
ms.contentlocale: ja-jp
ms.lasthandoff: 05/09/2017


---
# <a name="overview-of-microsoft-azure-data-lake-analytics"></a>Microsoft Azure Data Lake Analytics の概要
## <a name="what-is-azure-data-lake-analytics"></a>Azure Data Lake Analytics とは
Azure Data Lake Analytics は、ビッグ データ分析を簡略化するオンデマンド分析ジョブ サービスです。 分散インフラストラクチャの操作ではなく、ジョブの記述、実行、および管理に集中できます。 ハードウェアのデプロイ、構成、チューニングを行う代わりに、クエリを作成してデータを変換し、価値ある洞察を抽出します。 この分析サービスでは、必要な性能をダイヤルで設定して、どのような規模のジョブでも即座に処理できます。 ジョブの実行中にのみ課金されるコスト効率の良いサービスです。 この分析サービスは Azure Active Directory をサポートしているので、既存のオンプレミスの ID システムと統合してアクセス権限とロールを管理できます。 また、SQL のメリットとユーザー コードの表現力を融合した U-SQL 言語が組み込まれています。 U-SQL のスケーラブルな分散ランタイムで、Azure の SQL Server、Azure SQL Database、Azure SQL Data Warehouse にまたがるストア内のデータを効率良く分析できます。

## <a name="key-capabilities"></a>主な機能
* **動的スケーリング**
  
    Data Lake Analytics は、クラウドのスケールとパフォーマンスを考慮して設計されています。  リソースを動的にプロビジョニングするので、テラバイト規模のデータや、エクサバイト規模のデータも分析できます。 ジョブが完了するとリソースが自動的に縮小され、使用した処理能力の分だけのお支払いで済みます。 保存するデータのサイズや使用するコンピューティングの量を増やしたり減らしたりする際に、コードを書き直す必要はありません。 そのため、大規模なデータセットの処理や保存の仕方に悩むことなく、ビジネス ロジックに集中できます。
* **使い慣れたツールで、開発を迅速に、デバッグと最適化をスマートに**
  
    Data Lake Analytics は Visual Studio と密に連携しているため、使い慣れたツールでコードの実行、デバッグ、調整を行うことができます。 U-SQL ジョブが視覚化されるので、コードがどのように大規模に実行されるのかを見ることができます。そのため、パフォーマンスのボトルネックを簡単に特定し、コストを最適化できます。
* **U-SQL: シンプルで使いやすく、強力で拡張が可能**
  
    Data Lake Analytics には U-SQL が含まれています。これは、SQL のシンプルで使いやすい宣言的な特性を C# の表現力で拡張するクエリ言語です。 U-SQL 言語は、Microsoft 内のビッグ データ システムを支えているのと同じ分散ランタイムを基礎にしています。 SQL や .NET の何百万人もの開発者が、既に身に付けているスキルでデータを処理して分析できるようになります。
* **既存の IT 投資とシームレスに統合**
  
    Data Lake Analytics では、ID、管理、セキュリティ、データ ウェアハウジングのために既存の IT 投資を使用できます。 この方法により、データ ガバナンスがシンプルになり、現在のデータ アプリケーションも容易に拡張できます。 Data Lake Analytics はユーザー管理とアクセス許可のための Active Directory と統合されており、監視と監査も組み込まれています。
* **リーズナブルな料金と高いコスト効率**
  
    Data Lake Analytics は、ビッグ データ ワークロードを実行するためのコスト効率の良いソリューションです。 データが処理されたときに、ジョブ ベースで料金が発生します。 ハードウェア、ライセンス、サービス固有のサポート契約は必要ありません。 ジョブの開始や完了に応じて、システムのスケールアップとスケールダウンが自動的に行われます。つまり、余分に課金されることはありません。
* **すべての Azure データに対応**
  
    Data Lake Analytics は Azure Data Lake と連動するように最適化されており、ビッグ データのワークロードに対して最高レベルのパフォーマンス、スループット、並列化を提供します。  Data Lake Analytics は、Azure Blob Storage および Azure SQL Database とも連動します。

## <a name="see-also"></a>関連項目
* 作業開始
  
  * [Azure Portal で Azure Data Lake Analytics の使用を開始する](data-lake-analytics-get-started-portal.md)
  * [Azure PowerShell で Data Lake Analytics の使用を開始する](data-lake-analytics-get-started-powershell.md)
  * [Azure .NET SDK で Azure Data Lake Analytics の使用を開始する](data-lake-analytics-get-started-net-sdk.md)
  * [Data Lake Tools for Visual Studio を使用する U-SQL スクリプトの開発](data-lake-analytics-data-lake-tools-get-started.md)
  * [Azure Data Lake Analytics U-SQL 言語の使用](data-lake-analytics-u-sql-get-started.md)

* 管理
  
  * [Azure ポータルを使用して Azure Data Lake Analytics を管理する](data-lake-analytics-manage-use-portal.md)
  * [Azure PowerShell を使用する Azure Data Lake Analytics の管理](data-lake-analytics-manage-use-powershell.md)
  * [Azure ポータルを使用して Azure Data Lake Analytics ジョブの監視とトラブルシューティングを行う](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

* 意見の投稿
  
  * [機能要求の送信](http://aka.ms/adlafeedback)
  * [MSDN フォーラムの利用](http://aka.ms/adlaforums)


