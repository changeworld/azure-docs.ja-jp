---
title: Azure Data Factory の統合ランタイム | Microsoft Docs
description: Azure Data Factory の統合ランタイムについて。
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/22/2018
ms.author: shlo
ms.openlocfilehash: 91225136926427fb22c80bc2e198c153c60614eb
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/11/2018
ms.locfileid: "35298136"
---
# <a name="integration-runtime-in-azure-data-factory"></a>Azure Data Factory の統合ランタイム
統合ランタイム (IR) は、異なるネットワーク環境間で以下のデータ統合機能を提供するために Azure Data Factory によって使用されるコンピューティング インフラストラクチャです。

- **データ移動**: パブリック ネットワーク内のデータ ストアとプライベート ネットワーク (オンプレミスまたは仮想プライベート ネットワーク) 内のデータ ストアの間でデータを移動します。 組み込みコネクタ、形式の変換、列のマッピング、パフォーマンスとスケーラビリティに優れたデータ転送に関するサポートを提供します。
- **アクティビティのディスパッチ**:  Azure HDInsight、Azure Machine Learning、Azure SQL Database、SQL Server などのさまざまなコンピューティング サービスで実行される変換アクティビティをディスパッチして監視します。
- **SSIS パッケージの実行**: 管理されている Azure コンピューティング環境で SQL Server Integration Services (SSIS) パッケージをネイティブに実行します。


> [!NOTE]
> この記事は、現在プレビュー段階にある Data Factory のバージョン 2 に適用されます。 一般公開 (GA) されている Data Factory サービスのバージョン 1 を使用している場合は、[Data Factory バージョン 1 のドキュメント](v1/data-factory-introduction.md)を参照してください。

Data Factory で、アクティビティは、実行されるアクションを定義します。 リンクされたサービスは、ターゲットのデータ ストアやコンピューティング サービスを定義します。 統合ランタイムは、アクティビティとリンクされたサービスとを橋渡しします。  統合ランタイムは、リンクされたサービスによって参照され、アクティビティが実行されたりディスパッチされたりするコンピューティング環境を提供します。  これにより、できるだけターゲットのデータ ストアやコンピューティング サービスに近いリージョンでアクティビティを実行して効率を最大化できる一方、セキュリティとコンプライアンスの必要も満たせます。

## <a name="integration-runtime-types"></a>統合ランタイムの種類
Data Factory には 3 種類の統合ランタイムが用意されているので、ご希望のデータ統合機能やネットワーク環境のニーズに最もかなっている種類を選択する必要があります。  次の 3 種類があります。

- Azure
- セルフホステッド
- Azure-SSIS

次の表で、各種の統合ランタイムの機能とネットワークのサポートについて説明します。

IR の種類 | パブリック ネットワーク | プライベート ネットワーク
------- | -------------- | ---------------
Azure | データの移動<br/>アクティビティのディスパッチ | &nbsp;
セルフホステッド | データの移動<br/>アクティビティのディスパッチ | データの移動<br/>アクティビティのディスパッチ
Azure-SSIS | SSIS パッケージの実行 | SSIS パッケージの実行

次の図は、さまざまな統合ランタイムを組み合わせて使用し、優れたデータ統合機能やネットワークのサポートを提供する方法を示しています。

![各種の統合ランタイム](media\concepts-integration-runtime\different-integration-runtimes.png)


## <a name="azure-integration-runtime"></a>Azure 統合ランタイム
Azure 統合ランタイムにより、次のことが可能になります。

- クラウドのデータ ストア間でコピー アクティビティを実行する
- パブリック ネットワーク内で次の変換アクティビティをディスパッチする: HDInsight Hive アクティビティ、HDInsight Pig アクティビティ、HDInsight MapReduce アクティビティ、HDInsight Spark アクティビティ、HDInsight Streaming アクティビティ、Machine Learning バッチ実行アクティビティ、Machine Learning 更新リソース アクティビティ、ストアド プロシージャ アクティビティ、Data Lake Analytics U-SQL アクティビティ、.Net カスタム アクティビティ、Web アクティビティ、ルックアップ アクティビティ、メタデータの取得アクティビティ。

### <a name="network-environment"></a>ネットワーク環境
Azure 統合ランタイムは、誰でもアクセス可能なエンドポイントを使用して、パブリック ネットワーク内のデータ ストアやコンピューティング サービスへの接続をサポートします。 Azure Virtual Network 環境にはセルフホステッド統合ランタイムを使用します。

### <a name="compute-resource-and-scaling"></a>コンピューティング リソースとスケーリング
Azure 統合ランタイムは、Azure 内で完全に管理されたサーバーレス コンピューティングを提供します。  インフラストラクチャのプロビジョニング、ソフトウェアのインストール、修正プログラムの適用、容量のスケーリングについて心配する必要はありません。  さらに、実際の使用時間分だけのお支払いになります。

Azure 統合ランタイムは、ネイティブのコンピューティングを備えており、セキュリティで保護された、信頼性とパフォーマンスの高い方法で、クラウドのデータ ストア間でデータを移動します。  コピー アクティビティで使用するデータの移動単位の数を設定できます。Azure IR のコンピューティング サイズはそれに応じて柔軟にスケール アップし、Azure 統合ランタイムのサイズを明示的に調整する必要はありません。

アクティビティのディスパッチは、アクティビティをターゲット コンピューティング サービスにルーティングする簡易な操作です。したがって、このシナリオでコンピューティング サイズをスケール アップする必要はありません。

Azure IR の作成と構成については、ハウツー ガイドで Azure IR の作成と構成の方法の説明をご覧ください。 

## <a name="self-hosted-integration-runtime"></a>セルフホステッド統合ランタイム
セルフホステッド IR により、次のことが可能になります。

- クラウドのデータ ストアとプライベート ネットワーク内のデータ ストアの間でコピー アクティビティを実行する。
- オンプレミスまたは Azure Virtual Network 内のコンピューティング リソースに対して次の変換アクティビティをディスパッチする: HDInsight Hive アクティビティ (BYOC)、HDInsight Pig アクティビティ (BYOC)、HDInsight MapReduce アクティビティ (BYOC)、HDInsight Spark アクティビティ (BYOC)、HDInsight Streaming アクティビティ (BYOC)、Machine Learning バッチ実行アクティビティ、Machine Learning 更新リソース アクティビティ、ストアド プロシージャ アクティビティ、Data Lake Analytics U-SQL アクティビティ、.Net カスタム アクティビティ、ルックアップ アクティビティ、メタデータの取得アクティビティ。

> [!NOTE] 
> SAP Hana や MySQL などの独自ドライバーを必要とするデータ ストアをサポートするには、セルフホステッド統合ランタイムを使用します。詳細については、「[supported data stores (サポートされるデータ ストア)](copy-activity-overview.md#supported-data-stores-and-formats)」を参照してください。

### <a name="network-environment"></a>ネットワーク環境
パブリック クラウド環境からの直接の通信経路がないプライベート ネットワーク環境で、安全にデータ統合を実行しようとしている場合は、社内のファイアウォール内のオンプレミス環境か仮想プライベート ネットワーク内にセルフホステッド IR をインストールできます。  セルフホステッド統合ランタイムは、開かれたインターネットへの送信 HTTP ベースの接続のみを行います。

### <a name="compute-resource-and-scaling"></a>コンピューティング リソースとスケーリング
セルフホステッド IR のインストールには、オンプレミスのマシンか、プライベート ネットワーク内の仮想マシンが必要です。 現時点では、セルフホステッド IR の実行対象として Windows オペレーティング システムだけがサポートされています。  

高可用性とスケーラビリティを実現するには、アクティブ/アクティブ モードで論理インスタンスをオンプレミスの複数のマシンに関連付けて、セルフホステッド IR をスケールアウトできます。  詳細については、ハウツー ガイドでセルフホステッド IR の作成と構成の方法に関する記事をご覧ください。

## <a name="azure-ssis-integration-runtime"></a>Azure-SSIS 統合ランタイム
既存の SSIS ワークロードをリフトアンドシフトするには、Azure-SSIS IR を作成して SSIS パッケージをネイティブに実行できます。

### <a name="network-environment"></a>ネットワーク環境
Azure-SSIS IR は、パブリック ネットワークかプライベート ネットワーク内でプロビジョニングできます。  オンプレミスのデータ アクセスは、オンプレミスのネットワークに接続している仮想ネットワークと Azure-SSIS IR を結合することでサポートされます。  

### <a name="compute-resource-and-scaling"></a>コンピューティング リソースとスケーリング
Azure-SSIS IR は、SSIS パッケージ実行専用の、Azure VM の完全に管理されたクラスターです。 独自の Azure SQL Database または Managed Instance (プレビュー) サーバーを持ち込み、それに接続する SSIS プロジェクト/パッケージ (SSISDB) のカタログをホストすることができます。 ノードのサイズを指定してコンピューティング能力をスケールアップしたり、クラスター内のノードの数を指定してスケール アウトしたりできます。 必要に応じて Azure-SSIS 統合ランタイムを停止したり開始したりして、その実行のコストを管理できます。

詳細については、ハウツー ガイドで Azure-SSIS IR の作成と構成の方法に関する記事をご覧ください。  作成し終えたら、オンプレミスで SSIS を使用する場合と同様に、SQL Server Data Tools (SSDT) や SQL Server Management Studio (SSMS) などの使い慣れたツールを使用して、まったく、またはほとんど変更を加えずに既存の SSIS パッケージをデプロイして管理することができます。

Azure-SSIS ランタイムの詳細については、次の記事をご覧ください。 

- [チュートリアル: SSIS パッケージを Azure にデプロイする](tutorial-create-azure-ssis-runtime-portal.md):  この記事では、Azure-SSIS IR を作成し、Azure SQL Database を使って SSIS カタログをホストする手順が説明されています。 
- [方法: Azure-SSIS 統合ランタイムを作成する](create-azure-ssis-integration-runtime.md):  この記事では、チュートリアルを基に、Azure SQL Managed Instance (プレビュー) の使い方と、IR を仮想ネットワークに参加させる方法が説明されています。 
- [Azure-SSIS IR を監視する](monitor-integration-runtime.md#azure-ssis-integration-runtime):  この記事では、Azure-SSIS IR に関する情報を取得する方法と、返された情報での状態が説明されています。 
- [Azure-SSIS IR を管理する](manage-azure-ssis-integration-runtime.md):  この記事では、Azure-SSIS IR を停止、開始、削除する方法が説明されています。 また、IR にノードを追加することで Azure-SSIS IR をスケールアウトする方法も説明されています。 
- [仮想ネットワークへの Azure-SSIS IR の参加](join-azure-ssis-integration-runtime-virtual-network.md): この記事では、Azure 仮想ネットワークへの Azure-SSIS IR の参加に関する概念情報が説明されています。 Azure-SSIS IR が仮想ネットワークに参加できるように Azure Portal を使用して仮想ネットワークを構成する手順も説明されています。 

## <a name="determining-which-ir-to-use"></a>使用する IR の判別
各変換アクティビティには、ターゲット コンピューティングのリンクされたサービスがあり、これは統合ランタイムに向けられています。 この統合ランタイム インスタンスから、変換アクティビティがディスパッチされます。

コピー アクティビティの場合、データ フローの方向を定義するのに、ソースとシンクのリンクされたサービスが必要です。 どの統合ランタイム インスタンスを使用してコピーを実行するかを決めるために、次のロジックが使用されます。 

- **2 つのクラウド データ ソース間でのコピー**: ソースとシンクの両方のリンクされたサービスで Azure IR が使用されている場合、シンクのリンクされたサービスで使用されている統合ランタイムを使用して、コピー アクティビティを実行します。
- **クラウド データ ソースとプライベート ネットワーク内のデータ ソースの間でのコピー**: ソースかシンクのいずれかのリンクされたサービスがセルフホステッド IR を指している場合、そのセルフホステッド統合ランタイム上でコピー アクティビティが実行されます。
- **プライベート ネットワーク内の 2 つのデータ ソース間でのコピー**: ソースとシンクの両方のリンクされたサービスが同じ統合ランタイム インスタンスを指す必要があり、その統合ランタイムを使用してコピー アクティビティが実行されます。

次の図は、2 つのコピー アクティビティのサンプルを示しています。

- コピー アクティビティ 1 の場合、セルフホステッド IR A を参照している SQL Server のリンクされたサービスがソースで、Azure IR B を参照している Azure Storage のリンクされたサービスがシンクです。コピー アクティビティの実行時には、セルフホステッド IR A 上で実行されます。
- コピー アクティビティ 2 の場合、Azure IR C を参照している Azure SQL Database のリンクされたサービスがソースで、Azure IR B を参照している Azure Storage のリンクされたサービスがシンクです。シンクのリンクされたサービスで Azure IR B が使用されているので、コピー アクティビティの実行時にはこの統合ランタイム上で実行されます。

![使用する IR](media/concepts-integration-runtime/which-integration-runtime-to-use.png)

## <a name="integration-runtime-location"></a>統合ランタイムの場所
Data Factory の場所とは、データ ファクトリのメタデータが格納され、パイプラインのトリガーが開始される場所のことです。 現時点でサポートされている Data Factory の場所は、米国東部、米国東部 2、東南アジア、西ヨーロッパです。 ただし、データ ファクトリは、他の Azure リージョン内のデータ ストアやコンピューティング サービスにアクセスし、データ ストア間でデータを移動したり、コンピューティング サービスを使用してデータを処理したりできます。 この動作は世界中の複数のリージョンで使用できる IR によって実現し、データのコンプライアンス、効率性、ネットワークのエグレスのコストの削減が保証されます。

IR の場所は、そのバックエンドのコンピューティングの場所を定義するほか、実質的にデータの移動、アクティビティのディスパッチ、SSIS パッケージの実行が行われる場所を定義します。 IR の場所は、それが属しているデータ ファクトリの場所とは別にすることができます。 次の図は、Data Factory とその統合ランタイムの場所の設定を示しています。

![統合ランタイムの場所](media/concepts-integration-runtime/integration-runtime-location.png)

### <a name="azure-ir"></a>Azure IR
Data Factory は、同じ地理的な場所の、シンクに最も近いリージョン内の Azure IR を使用してデータを移動します。 リージョンのマッピングについては、以下の表をご覧ください。

シンクのデータ ストアの地理的な場所 | シンクのデータ ストアの場所 | Azure 統合ランタイムに使用される場所
-------------------------------| ----------------| ------------------
米国 | 米国東部 | 米国東部
&nbsp; | 米国東部 2 | 米国東部 2
&nbsp; | 米国中央部 | 米国中央部
&nbsp; | 米国中北部 | 米国中北部
&nbsp; | 米国中南部 | 米国中南部
&nbsp; | 米国中西部 | 米国中西部
&nbsp; | 米国西部 | 米国西部
&nbsp; | 米国西部 2 | 米国西部 2
カナダ | カナダ東部 | カナダ中部
&nbsp; | カナダ中部 | カナダ中部
ブラジル | ブラジル南部 | ブラジル南部
ヨーロッパ | 北ヨーロッパ | 北ヨーロッパ
&nbsp; | 西ヨーロッパ | 西ヨーロッパ
イギリス | 英国西部 | 英国南部
&nbsp; | 英国南部 | 英国南部
アジア太平洋 | 東南アジア | 東南アジア
&nbsp; | 東アジア | 東南アジア
オーストラリア | オーストラリア東部 | オーストラリア東部
&nbsp; | オーストラリア南東部 | オーストラリア南東部
日本 | 東日本 | 東日本
&nbsp; | 西日本 | 東日本
韓国 | 韓国中部 | 韓国中部
&nbsp; | 韓国南部 | 韓国中部
インド | インド中部 | インド中部
&nbsp; | インド西部 | インド中部
&nbsp; | インド南部 | インド中部

Azure IR の場所を自動解決に設定することもできます。この場合、リンクされたサービス定義に基づいて、使用する最適な場所が Data Factory によって自動的に検出されます。

> [!NOTE] 
> コピー先データ ストアのリージョンがリストにない場合や検出できない場合は、コンプライアンス上の理由で、代わりのリージョンには移動せず、アクティビティは失敗します。 この場合、コピーの実行時に使用する代わりの場所を明示的に指定してください。
 
次の図は、Azure IR の場所が自動解決に設定されている場合に実際に使用される場所の例を示しています。 コピー アクティビティの実行時に、データのコピー先の場所が検出されます。この例では、西日本になります。  表に基づいて、実際のデータのコピーは東日本の Azure IR を使用して実行されます。 同じ IR を使用して Spark 用 HDInsight アクティビティに接続すると、Data Factory の場所 (この例では米国東部) から Spark アプリケーションが送信され、実際の Spark アプリケーションの実行は HDInsight サーバーの場所で行われます。 

![実際に使用される場所](media/concepts-integration-runtime/effective-location.png)

### <a name="self-hosted-ir"></a>セルフホステッド IR
セルフホステッド IR は Data Factory に論理的に登録されます。その機能のサポートのために使用するコンピューティングは自分で提供します。 したがって、セルフホステッド IR の明示的な場所のプロパティはありません。 

セルフホステッド IR を使用してデータの移動を実行する場合、この IR はデータをソースから抽出して移動先に書き込みます。

### <a name="azure-ssis-ir"></a>Azure-SSIS IR
抽出、変換、読み込み (ETL) ワークフローで高いパフォーマンスを実現するには、Azure-SSIS IR の正しい場所を選択することが重要です。  プレビュー版では初期段階で 6 つの場所 (米国東部、米国東部 2、米国中部、オーストラリア東部、北ヨーロッパ、西ヨーロッパ) を使用できます。

- Azure-SSIS IR の場所をデータ ファクトリの場所と同じにする必要はありませんが、SSISDB がホストされる独自の Azure SQL Database/Managed Instance (プレビュー) サーバーの場所と同じにする必要があります。 こうすると、Azure-SSIS 統合ランタイムから SSISDB に簡単にアクセスでき、複数の場所の間で過剰なトラフィックが生じません。
- SSISDB をホストする既存の Azure SQL Database/Managed Instance (プレビュー) サーバーがなく、オンプレミスのデータ ソース/移動先がある場合、オンプレミスのネットワークに接続している仮想ネットワークの場所に新しい Azure SQL Database/Managed Instance (プレビュー) サーバーを作成する必要があります。  この場合、新しい Azure SQL Database/Managed Instance (プレビュー) サーバーを使用し、この仮想ネットワークを参加させ、すべて同一の場所で Azure-SSIS IR を作成することができるため、異なる場所からのデータの移動を効果的に最小限にすることができます。
- SSISDB をホストする既存の Azure SQL Database/Managed Instance (プレビュー) サーバーの場所と、オンプレミスのネットワークに接続している仮想ネットワークの場所が違う場合は、まず、既存の Azure SQL Database/Managed Instance (プレビュー) サーバーを使用して Azure-SSIS IR を作成し、同じ場所の別の仮想ネットワークを参加させます。次に、異なる場所の間の仮想ネットワーク間接続を構成します。


## <a name="next-steps"></a>次の手順
次の記事を参照してください。

- [Create self-hosted integration runtime (セルフホステッド統合ランタイムの作成)](create-self-hosted-integration-runtime.md)
- [Azure-SSIS 統合ランタイムを作成](create-azure-ssis-integration-runtime.md)します。 この記事では、チュートリアルを基に、Azure SQL Managed Instance (プレビュー) の使い方と、IR を仮想ネットワークに参加させる方法が説明されています。 
