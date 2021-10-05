---
title: Azure Data Lake Storage Gen2 の使用に関するベスト プラクティス | Microsoft Docs
description: データ インジェスト、データのセキュリティ、および Azure Data Lake Storage Gen2 の使用に関連するパフォーマンスに関するベスト プラクティスについて説明します
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 09/21/2021
ms.author: normesta
ms.reviewer: sachins
ms.openlocfilehash: b2d631d165b4a8cd585fbe012d9ab9afaa2e6bf3
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128555434"
---
# <a name="best-practices-for-using-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 の使用に関するベスト プラクティス

この記事では、Data Lake Storage Gen2 が有効なストレージ アカウントのセキュリティ、回復性、ディレクトリ構造に関するベスト プラクティスの推奨事項を示します。 パフォーマンスの最適化に関するベスト プラクティスの推奨事項については、「[パフォーマンス用に Azure Data Lake Storage Gen2 を最適化する](data-lake-storage-performance-tuning-guidance.md)」を参照してください。

## <a name="security-considerations"></a>セキュリティに関する考慮事項

### <a name="use-security-groups-instead-of-individual-users"></a>個々のユーザーの代わりにセキュリティ グループを使用する

アクセス制御リスト (ACL) を適用する場合は、ACL エントリで常に、割り当てられたプリンシパルとして Azure AD セキュリティ グループを使用します。 個々のユーザーまたはサービス プリンシパルを直接割り当てることを抑止します。 この構造体を使用すると、ユーザーまたはサービス プリンシパルを追加したり削除したりすることができます。ACL をディレクトリ構造全体に再適用する必要はありません。 代わりに、適切な Azure AD セキュリティ グループからユーザーとサービス プリンシパルを追加または削除することができます。

このベスト プラクティスを適用する方法の詳細については、「[セキュリティ グループ](data-lake-storage-access-control-model.md#security-groups)」を参照してください。

Data Lake Storage Gen2 アクセス制御モデルに関する一般的な情報については、「[Azure Data Lake Storage Gen2 のアクセス制御モデル](data-lake-storage-access-control-model.md)」を参照してください。

### <a name="configure-the-azure-storage-firewall-with-azure-service-access"></a>Azure サービス アクセスを使用して Azure Storage ファイアウォールを構成する

外部からの攻撃ベクトルを制限するために、Azure Storage ファイアウォールをオンにします。 [Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks) などのサービスからストレージ アカウントにアクセスするには、そのサービスのインスタンスを仮想ネットワークにデプロイします。 その後、そのサービスへのアクセスをストレージ アカウントに許可するようにファイアウォールを構成できます。

このベスト プラクティスを適用する方法の詳細については、「[信頼された Azure サービスにアクセスを許可する](../common/storage-network-security.md)」を参照してください。

セキュリティに関する一般的な推奨事項については、「[BLOB ストレージのセキュリティに関する推奨事項](security-recommendations.md)」を参照してください

## <a name="resiliency-considerations"></a>回復性に関する考慮事項

Data Lake Storage Gen2 などのクラウド サービスでシステムを設計するときは、可用性の要件と、サービスで中断が発生したときの対応について考慮します。 ある問題は特定のインスタンスに限定またはリージョン全域にわたる可能性があるため、両方について計画しておくことが重要です。 ワークロードの目標復旧時間と目標復旧ポイントのサービス レベル アグリーメントによっては、高可用性とディザスター リカバリーのために多少は積極的な戦略を選択することをお勧めします。

### <a name="high-availability-and-disaster-recovery"></a>高可用性とディザスター リカバリー

Data Lake Storage Gen2 は局所的なハードウェア障害に備えて、既に 3 倍のレプリケーションを処理しています。 ゾーン冗長ストレージ (ZRS) や geo ゾーン冗長ストレージ (GZRS) などのその他のレプリケーション オプションを使用すると、高可用性が向上します。 これらの機能により、ローカルにまたは新しいリージョンにある個別にレプリケートされたインスタンスにワークロードを切り替えることによって、サービスの中断に応答できます。

リージョンに致命的な障害が発生した場合に備えて、geo 冗長ストレージ (GRS) や読み取りアクセス用 geo 冗長ストレージ (RA-GRS) などのレプリケーション オプションを使用して、データを別のリージョンにレプリケートするようにしてください。 エッジ ケースに対応する要件も考慮します。たとえば、データの破損では、定期的にスナップショットを作成してフォールバックする必要があります。 データの重要度とサイズによっては、リスクの許容範囲に応じて、1 時間、6 時間、24 時間の期間の差分スナップショットを実行することを検討してください。

適切なレプリケーション モデルを選択するだけでなく、監視トリガー、失敗した試行の長さ、または管理者に手動操作の通知を送信することで、クライアントの接続を自動的にセカンダリ リージョンにフェールオーバーする方法を検討します。 フェールオーバーには、サービスがオンラインに戻ってくるのを待つことと比較して、デメリットが存在します。

### <a name="use-distcp-for-data-movement-between-two-locations"></a>Distcp を使用して 2 つの場所の間でデータを移動する

DistCp (distributed copy の省略形) は Hadoop に付属の Linux のコマンドライン ツールで、2 つの場所の間でデータを分散移動します。 その 2 つの場所は、Data Lake Storage Gen2、Hadoop 分散ファイル システム (HDFS)、または S3 になる場合があります。 このツールは Hadoop クラスター (HDInsight など) で MapReduce ジョブを使用してすべてのノードでスケールアウトします。 DistCp は、特別なネットワーク圧縮アプライアンスを使用せずにビッグ データを移動できる、最速の方法の 1 つと見なされています。 また、Distcp には 2 つの場所間のデルタのみを更新する、自動再試行を処理する、計算の動的な拡大縮小を実行するオプションが用意されています。 この手法は、単一のディレクトリにサイズの大きなファイルが大量にあり、変更があったデータのみを上書きコピーする、Hive または Spark のテーブルなどの項目をレプリケートする場合に非常に効率的です。 これらの理由から、Distcp はビッグ データ ストア間でデータをコピーする最もおすすめのツールです。

コピー ジョブは、Apache Oozie ワークフローで頻度やデータ トリガージョブを使用するほか、Linux の cron トリガーを使用してトリガーできます。 負荷の大きなレプリケーション ジョブでは、コピー ジョブに特化してチューニングおよびスケーリングできる、別個の HDInsight Hadoop クラスターをスピンアップすることをおすすめします。 これにより、コピー ジョブが重要なジョブを妨害することがなくなります。 十分な間隔を空けた頻度でレプリケーションを実行すると、クラスターが各ジョブ間で分解されることもあります。 セカンダリ リージョンにフェールオーバーされる場合、Data Lake Storage Gen2 のプライマリ アカウントが復旧した後に新しいデータが再度レプリケートされるように、セカンダリ リージョンでもう 1 つのクラスターもスピンアップされていることを確認してください。 Distcp の使用例については、[Distcp を使用して Azure Storage BLOB と Data Lake Storage Gen2 の間でデータをコピーする方法](../blobs/data-lake-storage-use-distcp.md)に関するページを参照してください。

### <a name="use-azure-data-factory-to-schedule-copy-jobs"></a>Azure Data Factory を使用してコピー ジョブのスケジュールを設定する

[Azure Data Factory](../../data-factory/introduction.md) ではコピー アクティビティを使用してコピー ジョブのスケジュールを設定できるほか、コピー ウィザードで頻度を設定することもできます。 Azure Data Factory にはクラウド データ移動単位 (DMU) の制限があり、最終的に大規模なデータ ワークロードのスループット/計算に上限を設定します。 さらに、Azure Data Factory では現在、Data Lake Storage Gen2 アカウント間のデルタの更新は提供しておらず、Hive のテーブルなどのディレクトリをレプリケートするには、完全なコピーが必要になります。 Data Factory のコピーに関する詳細については、[データ ファクトリの記事](../../data-factory/load-azure-data-lake-storage-gen2.md)を参照してください。

## <a name="monitoring-considerations"></a>監視に関する考慮事項

Data Lake Storage Gen2 では、Azure portal 内の Data Lake Storage Gen2 アカウントと、Azure Monitor 内にメトリックが用意されています。 Data Lake Storage Gen2 の可用性は、Azure portal に表示されます。 Data Lake Storage Gen2 アカウントの最新の可用性を取得するには、独自の統合テストを実行して可用性を検証する必要があります。 合計ストレージ使用率、読み取り/書き込み要求数、イングレス/エグレスなどの他のメトリックを監視アプリケーションから利用することができます。また、しきい値 (たとえば、平均待機時間や 1 分あたりのエラー数) を超えたときにアラートをトリガーすることもできます。

## <a name="directory-layout-considerations"></a>ディレクトリのレイアウトに関する考慮事項

データを取り込むときは、セキュリティ、パーティション分割、プロセスが効果的に活用されるように、データの構造を事前に計画することが重要です。 次の推奨事項の多くは、すべてのビッグ データ ワークロードに適用できます。 すべてのワークロードのデータの消費方法に関する要件は異なりますが、以下にモノのインターネット (IoT) およびバッチのシナリオを操作するときの共通のレイアウトをいくつか紹介します。

### <a name="iot-structure"></a>IoT の構造

IoT のワークロードでは、取り込まれるデータは、製品、端末、組織、お客様と多岐にわたります。 ダウンストリーム コンシューマーのために、組織のディレクトリのレイアウト、セキュリティ、データの効率的な処理について、事前に計画することが重要です。 考慮する一般的なテンプレートは、次のようなレイアウトになります。

*{Region}/{SubjectMatter(s)}/{yyyy}/{mm}/{dd}/{hh}/*

たとえば、英国の飛行機のエンジンのランディング テレメトリは次のような構造になります。

*UK/Planes/BA1293/Engine1/2017/08/11/12/*

この例では、ディレクトリ構造の末尾に日付を指定することで、ACL を使用して、より簡単に特定のユーザーとグループに対してリージョンと主題をセキュリティで保護できます。 データ構造を先頭に指定すると、これらのリージョンと主題をセキュリティで保護することが大幅に難しくなります。 たとえば、英国のデータまたは特定の飛行機のみにアクセスを提供する場合は、時間ごとのディレクトリの下にある多数のディレクトリに対して個別にアクセス許可を適用する必要があります。 また、この構造の場合、時間が経過するにつれてディレクトリの数が指数関数的に増加します。

### <a name="batch-jobs-structure"></a>バッチ ジョブの構造

バッチ処理で一般的に使用される方法は、データを "in" ディレクトリに配置することです。 その後、データが処理されたら新しいデータを "out" ディレクトリに入れて、ダウンストリームのプロセスで使用できるようにします。 このディレクトリ構造は、個々のファイルに対する処理が必要で、大規模なデータセットに対する膨大な並列処理が必要とされない可能性があるジョブで使用されることがあります。 前述の推奨される IoT の構造と同様に、優れたディレクトリ構造にはリージョンや主題 (例: 組織、製品、製造業者) などの項目用に、親レベルのディレクトリがあります。 プロセスでより優れた組織、フィルター検索、セキュリティ、自動化を実現するために、構造で日付と時間を考慮します。 日付構造の細分性のレベルは、データがアップロードまたは処理される間隔 (毎時、毎日、毎月など) によって決まります。

ファイルのプロセスがデータの破損や予期しない形式により失敗することがあります。 このようなケースでは、 **/bad** フォルダーにファイルを移動してさらに調査すると便利な場合があります。 また、バッチ ジョブはこれらの "*不良*" ファイルのレポート作成や通知を管理し、手動で介入できるようにします。 次のテンプレート構造を考慮してください。

*{Region}/{SubjectMatter(s)}/In/{yyyy}/{mm}/{dd}/{hh}/* \
*{Region}/{SubjectMatter(s)}/Out/{yyyy}/{mm}/{dd}/{hh}/* \
*{Region}/{SubjectMatter(s)}/Bad/{yyyy}/{mm}/{dd}/{hh}/*

たとえば、ある北米のマーケティング企業は、更新されたお客様情報の抽出データをクライアントから毎日受け取ります。 処理される前と後では、次のスニペットのようになります。

*NA/Extracts/ACMEPaperCo/In/2017/08/14/updates_08142017.csv*\
*NA/Extracts/ACMEPaperCo/Out/2017/08/14/processed_updates_08142017.csv*

Hive や従来の SQL データベースなどのデータベースで直接処理される一般的なバッチ データの場合、出力は既に Hive テーブルまたは外部データベースの別のフォルダーに入るように設定されているため、 **/in** または **/out** ディレクトリは不要です。 たとえば、顧客から日単位で抽出したものをそれぞれのディレクトリに配置します。 さらに、[Azure Data Factory](../../data-factory/introduction.md)、[Apache Oozie](https://oozie.apache.org/)、[Apache Airflow](https://airflow.apache.org/) などのサービスによって、Hive や Spark の毎日のジョブをトリガーし、データを処理して Hive のテーブルに書き込みます。

## <a name="see-also"></a>関連項目

- 「[Azure Data Lake Storage Gen2 のアクセス制御モデル](data-lake-storage-access-control-model.md)」
- [パフォーマンス用に Azure Data Lake Storage Gen2 を最適化する](data-lake-storage-performance-tuning-guidance.md)
- [Data Lake のヒッチハイカー ガイド](https://github.com/rukmani-msft/adlsguidancedoc/blob/master/Hitchhikers_Guide_to_the_Datalake.md)
- [Azure Data Lake Storage Gen2 の概要](data-lake-storage-introduction.md)
