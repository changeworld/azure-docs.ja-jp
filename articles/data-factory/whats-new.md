---
title: Azure Data Factory の新機能
description: この新機能のページでは、Azure Data Factory の新機能と機能強化について説明します。
author: pennyzhou-msft
ms.author: xupzhou
ms.reviewer: xupzhou
ms.service: data-factory
ms.topic: overview
ms.date: 07/14/2021
ms.openlocfilehash: fe4c5fb72ce3cd32e14bad211683e01ac6edf3ab
ms.sourcegitcommit: 92dd25772f209d7d3f34582ccb8985e1a099fe62
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2021
ms.locfileid: "114342628"
---
# <a name="whats-new-in-azure-data-factory"></a>Azure Data Factory の新機能

Azure Data Factory には継続的に改善が施されています。 常に最新の開発情報を把握していただけるよう、この記事では以下に関する情報を提供します。

- 最新のリリース
- 既知の問題
- バグの修正
- 非推奨の機能
- 変更の計画

このページは毎月更新されるため、定期的にご確認ください。 

## <a name="june-2021"></a>2021 年 6 月
<br>
<table>
<tr><td><b>サービス カテゴリ</b></td><td><b>サービスの機能強化</b></td><td><b>詳細</b></td></tr>
<tr><td rowspan=4 valign="middle"><b>データの移動</b></td><td>Azure Data Factory データ コピー ツールを使用した新しいユーザー エクスペリエンス</td><td>再設計されたデータ コピー ツールが利用可能になり、データ インジェストの操作性が向上しました。<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/a-re-designed-copy-data-tool-experience/ba-p/2380634">詳細情報</a></td></tr>
<tr><td>MongoDB と MongoDB Atlas が、ソースとシンクの両方としてサポートされます</td><td>この機能強化により、サポートされている任意のデータ ストアと MongoDB または MongoDB Atlas データベース間でのデータのコピーがサポートされます。<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/new-connectors-available-in-adf-mongodb-and-mongodb-atlas-are/ba-p/2441482">詳細情報</a></td></tr>
<tr><td>Always Encrypted が、ソースとシンクの両方として Azure SQL Database、Azure SQL Managed Instance、および SQL Server コネクタでサポートされます</td><td>Always Encrypted は、Azure Data Factory for Azure SQL Database、Azure SQL Managed Instance、およびコピー アクティビティ用の SQL Server コネクタで使用できます。<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/azure-data-factory-copy-now-supports-always-encrypted-for-both/ba-p/2461346">詳細情報</a></td></tr>
<tr><td>ADLS Gen2 または Azure Blob へのシンク時に、コピー アクティビティでカスタム メタデータの設定がサポートされます</td><td>ADLS Gen2 または Azure Blob に書き込むときに、コピー アクティビティで、カスタム メタデータの設定、またはソース ファイルの最後に変更された情報をメタデータとして保存することがサポートされています。<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/support-setting-custom-metadata-when-writing-to-blob-adls-gen2/ba-p/2545506#M490">詳細情報</a></td></tr>
<tr><td rowspan=4 valign="middle"><b>データ フロー</b></td><td>SQL Server がデータ フロー内のソースおよびシンクとしてサポートされるようになりました</td><td>SQL Server がデータ フロー内のソースおよびシンクとしてサポートされるようになりました。 Azure Integration Runtime マネージド VNET 機能を使用してネットワークを構成し、SQL Server のオンプレミスとクラウドの VM ベースのインスタンスと通信する方法の説明については、このリンクをたどってください。<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/new-data-flow-connector-sql-server-as-source-and-sink/ba-p/2406213">詳細情報</a></td></tr>
<tr><td>データフロー クラスターのクイック再利用が、すべての新しい Azure Integration Runtime で既定で有効になりました</td><td>ADF は、人気のデータ フロー クイック スタートアップ再利用機能が一般提供されたことをお知らせします。 すべての新しい Azure Integration Runtime で、クイック再利用が既定で有効になります。<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/how-to-startup-your-data-flows-execution-in-less-than-5-seconds/ba-p/2267365">詳細情報</a></td></tr>
<tr><td>ADF パブリック プレビューでの Power Query アクティビティ</td><td>Azure Data Factory データ ラングリングを使用して、Power Query シンクに複雑なフィールド マッピングを構築できるようになりました。 シンクは、この更新プログラムに対応するために Power Query (プレビュー) アクティビティのパイプラインで構成されるようになりました。<br><a href="wrangling-tutorial.md">詳細情報</a></td></tr>
<tr><td>Azure Data Factory の更新されたデータ フローの監視 UI</td><td>Azure Data Factory には、データ フロー ETL ジョブの実行状況を簡単に確認し、パフォーマンス チューニングの領域をすばやく特定できるようにするための監視 UI 用の新しい更新プログラムがあります。<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/updated-data-flows-monitoring-ui-in-adf-amp-synapse/ba-p/2432199">詳細情報</a></td></tr>
<tr><td><b>SQL Server Integration Services (SSIS)</b></td><td>Azure Data Factory の SSIS を使用して、3 つの簡単な手順で任意の場所で任意の SQL を実行する</td><td>この投稿では、Azure Data Factory の SSIS を使用して、任意の場所で任意の SQL ステートメントまたはスクリプトを実行するための 3 つの簡単な手順について説明します。<ol><li>セルフホステッド統合ランタイムまたは SSIS Integration Runtime を準備します。</li><li>Azure Data Factory パイプラインで SSIS パッケージの実行アクティビティを準備します。</li><li>セルフホステッド統合ランタイムまたは SSIS Integration Runtime で SSIS パッケージの実行アクティビティを実行します。</li></ol><a href="https://techcommunity.microsoft.com/t5/sql-server-integration-services/run-any-sql-anywhere-in-3-easy-steps-with-ssis-in-azure-data/ba-p/2457244">詳細情報</a></td></tr>
</table>

## <a name="more-information"></a>説明を見る

- [ブログ - Azure Data Factory](https://techcommunity.microsoft.com/t5/azure-data-factory/bg-p/AzureDataFactoryBlog)
- [Stack Overflow フォーラム](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Twitter](https://twitter.com/AzDataFactory?ref_src=twsrc%5Egoogle%7Ctwcamp%5Eserp%7Ctwgr%5Eauthor)
- [ビデオ](https://www.youtube.com/channel/UC2S0k7NeLcEm5_IhHUwpN0g/featured)





