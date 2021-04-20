---
title: Apache Hadoop コンポーネントおよびバージョン - Azure HDInsight
description: Azure HDInsight 内の Apache Hadoop コンポーネントおよびバージョンについて説明します。
ms.service: hdinsight
ms.topic: conceptual
author: deshriva
ms.author: deshriva
ms.date: 02/08/2021
ms.openlocfilehash: dbd5b507fd4a7b2434158dbdc80584a7fd348732
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105726585"
---
# <a name="azure-hdinsight-versions"></a>Azure HDInsight のバージョン

HDInsight では、Apache Hadoop 環境コンポーネントと HDInsight プラットフォームが、クラスター上にデプロイされたパッケージ内にバンドルされます。 詳細については、[HDInsight のバージョン管理のしくみ](hdinsight-overview-versioning.md)に関する記事を参照してください。

## <a name="supported-hdinsight-versions"></a>サポートされる HDInsight のバージョン

この表は、Azure portal およびその他のデプロイ方法 (PowerShell、CLI、.NET SDK など) で使用可能な、HDInsight のバージョンをまとめたものです。

| HDInsight のバージョン | VM の OS | リリース日| サポートの種類 | サポート有効期限 | 提供終了日 | 高可用性 |
| --- | --- | --- | --- | --- | --- | ---|
| [HDInsight 4.0](hdinsight-40-component-versioning.md) |Ubuntu 16.0.4 LTS |2018 年 9 月 24 日 | [Standard](hdinsight-component-versioning.md#support-options-for-hdinsight-versions) | | |はい |
| [HDInsight 3.6](hdinsight-36-component-versioning.md) |Ubuntu 16.0.4 LTS |2017 年 4 月 4 日      | [Basic](hdinsight-component-versioning.md#support-options-for-hdinsight-versions) | Standard サポートの有効期限 - 2021 年 6 月 30 日 <br> Basic サポートの有効期限 - 2022 年 4 月 3 日 |2022 年 4 月 4 日 |はい |

*2021 年 7 月 1 日以降、Microsoft は特定の種類の HDI 3.6 クラスターに対する Basic サポートを提供します。 [HDInsight 3.6 コンポーネントのバージョン](hdinsight-36-component-versioning.md)に関する記事を参照してください。

## <a name="release-notes"></a>リリース ノート

HDInsight の最新バージョンに関する追加のリリース ノートは、[HDInsight リリース ノート](hdinsight-release-notes.md)を参照してください。

## <a name="support-options-for-hdinsight-versions"></a>HDInsight バージョンのサポート オプション

サポートは、HDInsight のバージョンが Microsoft カスタマー サービスおよびサポートによってサポートされる期間として定義されます。 HDInsight では、次の 2 種類のサポートが提供されます。 
- **Standard サポート** は、Microsoft が HDInsight クラスターで更新とサポートを提供する期間です。  
    最新の完全サポート バージョンを使用してソリューションを構築することをお勧めします。 
- **Basic サポート** は、Microsoft が HDInsight リソース プロバイダーに限定されたサービスを提供する期間です。 HDInsight イメージとオープンソース ソフトウェア (OSS) コンポーネントは提供されません。   HDInsight クラスターには、重要なセキュリティ修正プログラムのみが適用されます。  
  Microsoft では、バージョンが Basic サポートの場合には、新しいクラスターの作成や新しいソリューションの構築は推奨していません。 既存のクラスターを最新の完全サポート バージョンに移行することを推奨しています。 

**サポートの有効期限** は、特定の HDInsight バージョンに対して Microsoft によるサポートが提供されなくなることを意味します。 また、クラスターを作成するために Azure portal を介して使用できなくなる場合があります。

**提供終了** とは、ある HDInsight バージョンの既存のクラスターが引き続きそのまま実行されることを意味します。 このバージョンの新しいクラスターは、CLI や SDK など、どのような方法でも作成することはできません。 手動によるスケーリングや自動スケールなど、その他のコントロール プレーン機能は、バージョンの提供終了日以降、機能することが保証されません。 提供が終了したバージョンでサポートを利用することはできません。

## <a name="versioning-considerations"></a>バージョン管理に関する考慮事項
- イメージを使ってクラスターがデプロイされると、そのクラスターは自動的には新しいイメージ バージョンへとアップグレードされません。 新しいクラスターを作成する際には、最新のイメージ バージョンがデプロイされます。
- 新しい HDInsight バージョンを使用する場合は、アプリケーションが正常に動作するかどうかをテストして検証する必要があります。
- HDInsight は、事前の通知なく既定のバージョンを変更する権利を留保します。 バージョンの依存関係がある場合は、クラスターを作成する際に HDInsight バージョンを指定してください。
- HDInsight では、HDInsight バージョンの提供が終了する前に、OSS コンポーネントのバージョンが提供終了となる場合があります。

## <a name="next-steps"></a>次のステップ

- [HDInsight で Apache Hadoop、Spark、その他のクラスターをセットアップする](hdinsight-hadoop-provision-linux-clusters.md)
- [Enterprise セキュリティ パッケージ](./enterprise-security-package.md)
- [Windows PC から HDInsight の Apache Hadoop で作業する](hdinsight-hadoop-windows-tools.md)
