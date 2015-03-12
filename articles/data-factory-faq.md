<properties 
	pageTitle="Azure Data Factory - よく寄せられる質問" 
	description="Azure データ ファクトリについてよく寄せられる質問です。" 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="2/10/2015" 
	ms.author="spelluru"/>

# Azure Data Factory - よく寄せられる質問

**Q:Azure Data Factory とは何ですか。**

Data Factory は、完全に管理されたサービスで、データの保存、移動、処理の各サービスを可用性が高くフォールト トレラントなデータ パイプラインとして構成します。Data Factory は内部設置型とクラウドの両方のデータ ストレージで動作します。パイプラインは、一連のデータ入力、処理アクティビティ、およびデータ出力のセットで、シンプルな JSON スクリプトで定義され、PowerShell コマンドを使用してアクティブ化されます。アクティブ化された Data Factory は、ユーザーに代わり、自動クラスター管理のオプションを使用して、パイプラインを HDInsight (Hadoop) 上で実行するための編成とスケジュール設定を行います。また、Data Factory では、Azure プレビュー ポータルによって管理と監視をビジュアルな環境で実行できます。運用とサービスの正常性に関する豊富な情報が表示される単一のダッシュボードですべてのパイプラインを監視できます。
 
**Q:Data Factory では顧客のどのような課題が解決されますか。**

Azure Data Factory は、従来のリレーショナル ストレージと非構造化データを対象とした多様なデータ保存、処理、および移動のサービスを機敏に活用できると共に、完全に管理されたサービスの制御と監視の機能を提供します。

**Q:Data Factory の対象ユーザーはだれですか。**


- Hadoop とその他のシステム間における統合サービスの構築を担当しているデータ開発者。
	- 絶えず変化と拡大を続けるデータ環境への対応と統合を行う必要がある。
	- 情報生成システムがカスタム コードの記述を必要とし、コストもかかり、保守が難しく、高可用性やフォールト トレランスを提供していない。

- 多様化するデータを IT インフラストラクチャに組み込む方法を探している IT プロフェッショナル。
	- 組織内のすべてのデータから豊富なビジネスの洞察を導き出す必要がある。
	- コンピューティングとストレージのリソースを管理し、内部設置型とクラウドの間でコストと規模のバランスをとる必要がある。
	- 新しいビジネス ニーズに対応するためにさまざまなソースと処理を短時間で追加しつつ、すべてのコンピューティング資産とストレージ資産の全体像を常に把握する必要がある。

**Q:Azure Data Factory の料金の詳細はどこで確認できますか。**

Azure Data Factory の料金の詳細については、[Data Factory の料金詳細に関するページ][adf-pricing-details]を参照してください。  

**Q: Azure Data Factory の利用はどのように開始するのですか。**

- Azure Data Factory の概要については、「[Introduction to Azure Data Factory (Azure Data Factory サービスの概要)][adf-introduction]」を参照してください。
- クイック チュートリアルについては、「[Azure Data Factory を使ってみる][adfgetstarted]」を参照してください。
- 包括的なドキュメントについては、[Azure Data Factory のドキュメント][adf-documentation-landingpage]を参照してください。
 
**Q:どのようなデータ ソースとアクティビティがサポートされますか。**

- **サポートされるデータ ソース:** Azure ストレージ (Blob およびテーブル)、SQL Server、Azure SQL データベース。
- **サポートされるアクティビティ**:コピー アクティビティ (内部設置型からクラウドへ、およびクラウドから内部設置型へ)、HDInsight アクティビティ (Pig および Hive の変換)、およびカスタム C# アクティビティ。
  
**Q:顧客は Data Factory にどのようにアクセスしますか。**

お客様は [Azure プレビュー ポータル][azure-preview-portal]を通じて Data Factory にアクセスできます。

**Q:Data Factory を利用可能な地域はどこですか。**

パブリック プレビューの時点では、Data Factory は米国西部でのみ提供されます。Data Factory で使用されるコンピューティング サービスとストレージ サービスは、その他の地域でも利用できます。
 
**Q:Data Factory/パイプライン/アクティビティ/データセットの数の制限値はいくつですか。** 


- 1 つのサブスクリプション内の Data Factory 数:50
- 1 つの Data Factory 内のパイプライン数:100
- 1 つのパイプライン内のアクティビティ数:10
- 1 つの Data Factory 内のデータセット数:100

**Q:コピー アクティビティでサポートされている地域はどこですか。**

コピー アクティビティでは、次の地域へのデータのコピーがサポートされています。米国東部 2、米国西部、北ヨーロッパ、西ヨーロッパ、および東南アジア。

その他の地域へのデータ コピーも、上記の 5 つの地域のいずれかをデータのルーティングに使用することで、実行できます。コピー操作は、データがルーティングされた地域に基づいて課金されます。

コピー先の地域 | ルーティングに使用される地域
-------------------------- | -----------------------
米国東部 | 米国東部 2
米国中央部 | 米国東部 2
米国中北部 | 米国東部 2
米国中南部 | 米国西部
東アジア | 東南アジア
日本東部 | 米国西部
日本西部 | 米国西部
ブラジル南部 | 米国東部 2

**Q:HDInsight クラスターを使用している場合、HDInsight はどの地域でサポートされますか。**

次の記事の「ご利用可能な地域」セクションを参照してください。[HDInsight の料金詳細][hdinsight-supported-regions]

**Q:オンデマンドの HDInsight クラスターはどの地域で使用されますか。**

オンデマンドの HDInsight クラスターは、クラスターで使用するように指定したストレージが存在するのと同じ地域に作成されます。    

## 関連項目
[Azure Data Factory の概要][adf-introduction]
[Azure Data Factory を使ってみる][adfgetstarted]
[Data Factory のトラブルシューティング ガイド][adf-troubleshoot]


[adfgetstarted]: ../data-factory-get-started
[adf-introduction]: ../data-factory-introduction
[adf-troubleshoot]: ../data-factory-troubleshoot
[adf-documentation-landingpage]: http://go.microsoft.com/fwlink/?LinkId=516909
[azure-preview-portal]: http://portal.azure.com

[adf-pricing-details]: http://go.microsoft.com/fwlink/?LinkId=517777
[hdinsight-supported-regions]: http://azure.microsoft.com/pricing/details/hdinsight/

<!--HONumber=35.2-->

<!--HONumber=46--> 
