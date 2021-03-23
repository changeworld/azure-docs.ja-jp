---
title: Apache Hadoop コンポーネントおよびバージョン - Azure HDInsight
description: Azure HDInsight 内の Apache Hadoop コンポーネントおよびバージョンについて説明します。
ms.service: hdinsight
ms.topic: conceptual
ms.custom: deshriva
ms.date: 02/08/2021
ms.openlocfilehash: 166c52660c9f47b8ecfce10f7e9c3b81ff067f35
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101699370"
---
# <a name="azure-hdinsight-versions"></a>Azure HDInsight のバージョン

HDInsight では、Apache Hadoop 環境コンポーネントと HDInsight プラットフォームが、クラスター上にデプロイされたパッケージ内にバンドルされます。 詳細については、[HDInsight のバージョン管理のしくみ](hdinsight-overview-versioning.md)に関する記事を参照してください。

## <a name="supported-hdinsight-versions"></a>サポートされる HDInsight のバージョン

この表は、Azure portal およびその他のデプロイ方法 (PowerShell、CLI、.NET SDK など) で使用可能な、HDInsight のバージョンをまとめたものです。

| HDInsight のバージョン | VM の OS | リリース日 | サポート有効期限 | 提供終了日 | 高可用性 |
| --- | --- | --- | --- | --- | --- |
| [HDInsight 4.0](hdinsight-40-component-versioning.md) |Ubuntu 16.0.4 LTS |2018 年 9 月 24 日 | | |はい |
| [HDInsight 3.6](hdinsight-36-component-versioning.md) |Ubuntu 16.0.4 LTS |2017 年 4 月 4 日      | \* 2021 年 6 月 30 日 |2021 年 6 月 30 日 |はい |

\* 特定のクラスターの種類 HDInsight 3.6 のサポート期間を延長しています。 [HDInsight 3.6 コンポーネントのバージョン](hdinsight-36-component-versioning.md)に関する記事を参照してください。

## <a name="release-notes"></a>リリース ノート

HDInsight の最新バージョンに関する追加のリリース ノートは、[HDInsight リリース ノート](hdinsight-release-notes.md)を参照してください。

## <a name="support-options-for-hdinsight-versions"></a>HDInsight バージョンのサポート オプション

HDInsight では、HDInsight バージョンが Microsoft カスタマー サービスおよびサポートによってサポートされる期間として定義された、Standard サポートを提供しています。

**サポートの有効期限** は、特定の HDInsight バージョンに対して Microsoft によるサポートが提供されなくなることを意味します。 また、クラスターを作成するために Azure portal を介して使用することができなくなります。

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