<properties 
   pageTitle="Microsoft Azure Data Lake Analytics の概要 | Azure" 
   description="Data Lake Analytics は Azure のビッグ データ コンピューティング サービスであり、クラウドのデータから得られた洞察を活用し、データを使用してビジネスを推進できます。その場所やサイズは関係ありません。Data Lake Analytics は、可能な限り最もシンプルで、スケーラブルかつ経済的な方法でこれを可能にします。" 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="mumian" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="10/28/2015"
   ms.author="jgao"/>

# Microsoft Azure Data Lake Analytics の概要

## Azure Data Lake Analytics とは

Azure Data Lake Analytics は、ビッグ データ分析を容易にするために構築された、新しいサービスです。このサービスでは、分散インフラストラクチャの操作ではなく、ジョブの記述、実行および管理に集中できます。ハードウェアのデプロイ、構成、チューニングを行う代わりに、クエリを作成してデータを変換し、価値ある洞察を抽出します。この分析サービスでは、必要な性能をダイヤルで設定するだけで、どんな規模のジョブでも直ちに処理できます。ジョブの実行中にのみ課金されるコスト効率の良いサービスです。この分析サービスは Azure Active Directory をサポートしているので、既存のオンプレミスの ID システムと統合してアクセス権限とロールをシンプルに管理できます。また、SQL のメリットとユーザー コードの表現力を融合した U-SQL 言語が組み込まれています。U-SQL のスケーラブルな分散ランタイムで、Azure の SQL Server、Azure SQL Database、Azure SQL Data Warehouse にまたがるストア内のデータを効率良く分析できます。


## 主な機能

- **動的スケーリング** 

    Data Lake Analytics は、クラウドのスケールとパフォーマンスのために一から構築されたものです。リソースを動的にプロビジョニングするので、テラバイト規模や、さらにはエクサバイト規模のデータも分析できます。ジョブが完了するとリソースは自動的に縮小し、使用した処理能力の分だけのお支払いで済みます。格納するデータのサイズや使用するコンピューティングの量を増やしたり減らしたりする際に、コードを書き直す必要はありません。そのため、大規模なデータセットの処理や格納方法ではなく、ビジネス ロジックのみに集中できます。

- **使い慣れたツールで、開発を迅速に、デバッグと最適化をスマートに**

    Data Lake Analytics は Visual Studio と密に連携しているため、使い慣れたツールでコードの実行、デバッグ、調整を行うことができます。U-SQL ジョブがビジュアル化されているので、コードがどのように大規模に実行されるのかを見ることができます。そのため、容易にパフォーマンスのボトルネックを特定したり、コストを最適化したりすることができます。

- **U-SQL: シンプルで使いやすく、強力で拡張が可能**

    Data Lake Analytics には U-SQL が含まれています。これは、SQL の使いやすくシンプルな宣言的な特性を C# の表現力で拡張するクエリ言語です。U-SQL 言語は、Microsoft 内のビッグ データ システムを支えているのと同じ分散ランタイムを基礎にしています。何百万人もの SQL や .NET の開発者が、既に持っているスキルで、あらゆるデータを処理して分析できるようになりました。

- **既存の IT 投資とシームレスに統合**

    Data Lake Analytics では、ID、管理、セキュリティ、データ ウェアハウジングのために既存の IT 投資を使用できます。データ ガバナンスがシンプルになり、現在のデータ アプリケーションも容易に拡張できます。Data Lake Analytics はユーザー管理とアクセス許可のための Active Directory と統合されており、監視と監査も組み込まれています。

- **リーズナブルでコスト効率が高い**

    Data Lake Analytics は、ビッグ データ ワークロードを実行するためのコスト効率の良いソリューションです。データが処理されたときに、ジョブ ベースで料金が発生します。ハードウェア、ライセンス、サービス固有のサポート契約は必要ありません。ジョブの開始や完了に応じて、システムのスケールアップとスケールダウンが自動的に行われます。つまり、余分に課金されることはありません。

- **すべての Azure データに対応**

    Data Lake Analytics は、Azure BLOB ストレージや Azure SQL Database などの数多くの Azure データ ソースに対応できます。もちろん、Data Lake Analytics は Azure Data Lake Store と連動するように特に最適化されており、ビッグ データ ワークロードに対して最高レベルのパフォーマンス、スループット、並列化を提供します。

## 関連項目

- 作業開始
    - [Azure プレビュー ポータルで Azure Data Lake Analytics の使用を開始する](data-lake-analytics-get-started-portal.md)
    - [Azure PowerShell で Azure Data Lake Analytics の使用を開始する](data-lake-analytics-get-started-powershell.md)
    - [Azure .NET SDK で Azure Data Lake Analytics の使用を開始する](data-lake-analytics-get-started-net-sdk.md)
    - [Data Lake Tools for Visual Studio を使用する U-SQL スクリプトの開発](data-lake-analytics-data-lake-tools-get-started.md)
    - [Azure Data Lake Analytics U-SQL 言語の使用](data-lake-analytics-u-sql-get-started.md)
    
- U-SQL および開発
    - [Azure Data Lake Analytics U-SQL 言語の使用](data-lake-analytics-u-sql-get-started.md)
    - [Azure Data Lake Analytics ジョブに U-SQL ウィンドウ関数を使用する](data-lake-analytics-use-window-functions.md)
    - [Data Lake Analytics ジョブの U-SQL ユーザー定義演算子の開発](data-lake-analtyics-u-sql-develop-user-defined-operators.md)
    
- 管理
    - [Azure プレビュー ポータルを使用する Azure Data Lake Analytics の管理](data-lake-analytics-manage-use-portal.md)
    - [Azure PowerShell を使用する Azure Data Lake Analytics の管理](data-lake-analytics-manage-use-powershell.md)
    - [Azure プレビュー ポータルを使用する Azure Data Lake Analytics ジョブの監視とトラブルシューティング](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

- エンド ツー エンド チュートリアル
    - [Azure Data Lake Analytics の対話型チュートリアルの使用](data-lake-analytics-use-interactive-tutorials.md)
    - [Azure Data Lake Analytics を使用する Web サイト ログの分析](data-lake-analytics-analyze-weblogs.md)

- 意見の投稿
    - [ドキュメント バックログへのコメント](data-lake-analytics-documentation-backlog.md)
    - [機能要求の送信](http://aka.ms/adlafeedback)
    - [フォーラムでサポートを受ける](http://aka.ms/adlaforums)

<!---HONumber=Nov15_HO2-->