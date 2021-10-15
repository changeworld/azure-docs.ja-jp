---
title: Apache Hadoop コンポーネントおよびバージョン - Azure HDInsight
description: Azure HDInsight 内の Apache Hadoop コンポーネントおよびバージョンについて説明します。
ms.service: hdinsight
ms.topic: conceptual
ms.date: 09/29/2021
ms.openlocfilehash: 16b9e7f2b790e6f4e758f07c4f352b30357ae32b
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2021
ms.locfileid: "129277311"
---
# <a name="azure-hdinsight-versions"></a>Azure HDInsight のバージョン

HDInsight では、Apache Hadoop 環境コンポーネントと HDInsight プラットフォームが、クラスター上にデプロイされたパッケージ内にバンドルされます。 詳細については、[HDInsight のバージョン管理のしくみ](hdinsight-overview-versioning.md)に関する記事を参照してください。

## <a name="supported-hdinsight-versions"></a>サポートされる HDInsight のバージョン

この表は、Azure portal およびその他のデプロイ方法 (PowerShell、CLI、.NET SDK など) で使用可能な、HDInsight のバージョンをまとめたものです。

| HDInsight のバージョン | VM の OS | リリース日| サポートの種類 | サポート有効期限 | 提供終了日 | 高可用性 |
| --- | --- | --- | --- | --- | --- | ---|
| [HDInsight 4.0](hdinsight-40-component-versioning.md) |Ubuntu 18.0.4 LTS |2018 年 9 月 24 日 | [Standard](hdinsight-component-versioning.md#support-options-for-hdinsight-versions) | | |はい |
| [HDInsight 3.6](hdinsight-36-component-versioning.md) |Ubuntu 16.0.4 LTS |2017 年 4 月 4 日      | [Basic](hdinsight-component-versioning.md#support-options-for-hdinsight-versions) | Standard サポートは、すべてのクラスターの種類で 2021 年 6 月 30 日に期限切れになりました。<br> Basic サポートは、2022 年 4 月 3 日に期限切れになります。 クラスターの種類の詳細については、「[HDInsight 3.6 のコンポーネントのバージョン](hdinsight-36-component-versioning.md)」を参照してください。 |2022 年 4 月 4 日 |はい |

**サポートの有効期限** は、特定の HDInsight バージョンに対して Microsoft によるサポートが提供されなくなることを意味します。 また、クラスターを作成するために Azure portal を介して使用できなくなる場合があります。

**提供終了** とは、ある HDInsight バージョンの既存のクラスターが引き続きそのまま実行されることを意味します。 このバージョンの新しいクラスターは、CLI や SDK など、どのような方法でも作成することはできません。 手動によるスケーリングや自動スケールなど、その他のコントロール プレーン機能は、バージョンの提供終了日以降、機能することが保証されません。 提供が終了したバージョンでサポートを利用することはできません。

## <a name="support-options-for-hdinsight-versions"></a>HDInsight バージョンのサポート オプション

サポートは、HDInsight のバージョンが Microsoft カスタマー サービスおよびサポートによってサポートされる期間として定義されます。 HDInsight では、次の 2 種類のサポートが提供されます。 
- **Standard サポート**
- **Basic サポート**

### <a name="standard-support"></a>Standard サポート

Standard サポートの場合、HDInsight クラスターの更新プログラムとサポートを利用できます。 Microsoft は、最新の完全サポート バージョンを使用してソリューションを構築することをお勧めします。 

Standard サポートの内容は次のとおりです。
- HDInsight 4.0 クラスターに関するサポート リクエストを作成する機能。
- 4\.0 クラスター上に構築されたソリューションのトラブルシューティングのサポート。 
- サービスまたはノードの再起動の要求。
- サポート リクエストに関する根本原因の分析調査。
- ジョブやクエリのパフォーマンスを改善するための根本原因の分析または修正。
- サービス構成の変更やカスタム スクリプトの動作による問題など、お客様が行った変更を改善するための根本原因の分析または修正。
- バージョンの廃止までの重要なセキュリティ修正プログラムに対応する製品の更新プログラム。
- HDInsight リソース プロバイダーにスコープが指定された製品の更新プログラム。
- HDInsight 4.0 イメージまたはオープンソース ソフトウェア (OSS) コンポーネントのバージョンに対する選択的な修正プログラムまたは変更。

### <a name="basic-support"></a>Basic サポート

Basic サポートは、HDInsight リソース プロバイダーに限定されたサービスを提供します。 HDInsight イメージとオープンソース ソフトウェア (OSS) コンポーネントは提供されません。 HDInsight クラスターには、重要なセキュリティ修正プログラムのみが適用されます。 

Basic サポートの内容は次のとおりです。
- 既存の HDInsight 3.6 クラスターの継続使用。
- 既存の HDInsight 3.6 のお客様が新たに 3.6 クラスターを作成する機能。
- 自動スケーリングまたは手動スケーリングによって HDInsight 3.6 クラスターをスケールアップ、スケールダウンする機能。
- HDInsight リソース プロバイダーにスコープが指定された製品の更新プログラム。
- バージョンの廃止までの重要なセキュリティ修正プログラムに対応する製品の更新プログラム。
- HDInsight 3.6 クラスターに関するサポート リクエストを作成する機能。
- サービスまたはノードの再起動の要求。

Basic サポートには、次の項目は含まれていません。
- HDInsight 3.6 イメージまたはオープンソース ソフトウェア (OSS) コンポーネントのバージョンに対する修正プログラムまたは変更。
- 3\.6 クラスター上に構築されたソリューションのトラブルシューティングのサポート。 
- 新しい機能の追加。
- アドバイスまたはアドホック クエリのサポート。
- サポート リクエストに関する根本原因の分析調査。
- ジョブやクエリのパフォーマンスを改善するための根本原因の分析または修正。
- サービス構成の変更やカスタム スクリプトの動作による問題など、お客様が行った変更を改善するための根本原因の分析または修正。

Microsoft は、Basic サポートでクラスター上に分析パイプラインまたはソリューションを作成することを推奨していません。 既存のクラスターを最新の完全サポート バージョンに移行することを推奨しています。 

## <a name="release-notes"></a>リリース ノート

HDInsight の最新バージョンに関する追加のリリース ノートは、[HDInsight リリース ノート](hdinsight-release-notes.md)を参照してください。

## <a name="versioning-considerations"></a>バージョン管理に関する考慮事項
- イメージを使ってクラスターがデプロイされると、そのクラスターは自動的には新しいイメージ バージョンへとアップグレードされません。 新しいクラスターを作成する際には、最新のイメージ バージョンがデプロイされます。
- 新しい HDInsight バージョンを使用する場合は、アプリケーションが正常に動作するかどうかをテストして検証する必要があります。
- HDInsight は、事前の通知なく既定のバージョンを変更する権利を留保します。 バージョンの依存関係がある場合は、クラスターを作成する際に HDInsight バージョンを指定してください。
- HDInsight では、HDInsight バージョンの提供が終了する前に、OSS コンポーネントのバージョンが提供終了となる場合があります。

## <a name="next-steps"></a>次のステップ

- [HDInsight で Apache Hadoop、Spark、その他のクラスターをセットアップする](hdinsight-hadoop-provision-linux-clusters.md)
- [Enterprise セキュリティ パッケージ](./enterprise-security-package.md)
- [Windows PC から HDInsight の Apache Hadoop で作業する](hdinsight-hadoop-windows-tools.md)
