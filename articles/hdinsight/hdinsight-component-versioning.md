---
title: "Hadoop コンポーネントとバージョン - Azure HDInsight | Microsoft Docs"
description: "HDInsight での Hadoop コンポーネントおよびバージョンと、このクラウド ディストリビューションの HortonWorks Data Platform で使用できるサービス レベルについて説明します。"
keywords: "hadoop バージョン,hadoop エコシステム コンポーネント,hadoop コンポーネント,hadoop バージョンの確認方法"
services: hdinsight
editor: cgronlun
manager: asadk
author: bprakash
tags: azure-portal
documentationcenter: 
ms.assetid: 367b3f4a-f7d3-4e59-abd0-5dc59576f1ff
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/14/2017
ms.author: bprakash
ms.translationtype: Human Translation
ms.sourcegitcommit: c308183ffe6a01f4d4bf6f5817945629cbcedc92
ms.openlocfilehash: f6f50906766408226ad3ab9fdfd67b4707961b9b
ms.contentlocale: ja-jp
ms.lasthandoff: 05/17/2017

 

---
# <a name="what-are-the-hadoop-components-and-versions-available-with-hdinsight"></a>HDInsight で使用可能な Hadoop コンポーネントとバージョンとは

Azure HDInsight の Hadoop エコシステムのコンポーネントおよびバージョンと、Standard と Premium の各サービス レベルについて説明します。 また、HDInsight で Hadoop コンポーネントのバージョンを確認する方法についても説明します。 

HDInsight の各バージョンは、あるバージョンの HortonWorks Data Platform (HDP) のクラウド ディストリビューションです。

## <a name="hadoop-components-available-with-different-hdinsight-versions"></a>HDInsight の各バージョンで使用できる Hadoop コンポーネント
Azure HDInsight は、いつでもデプロイできる Hadoop クラスター バージョンを複数サポートしています。 各バージョンを選択すると、特定のバージョンの Hortonworks Data Platform (HDP) ディストリビューションと、そのディストリビューションに含まれるコンポーネントが作成されます。 Azure HDInsight で使用される既定のクラスター バージョンは、2017 年 2 月 17 日現在、HDP 2.5 を基盤とした 3.5 です。

HDInsight クラスター バージョンに対応するコンポーネントのバージョンを、次の表にまとめます。 

> [!NOTE]
> サービスの既定のバージョンは、予告なく変更される場合があります。 バージョンの依存関係がある場合は、.NET SDK/Azure PowerShell や Azure CLI を使用してクラスターを作成する際にバージョンを指定することをお勧めします。 
>
>


| コンポーネント | HDInsight Version 3.6 | HDInsight Version 3.5 (既定) | HDInsight Version 3.4 | HDInsight Version 3.3 | HDInsight Version 3.2 | HDInsight Version 3.1 | HDInsight Version 3.0 |
| --- | --- | --- | --- | --- | --- | --- |--- |
| Hortonworks Data Platform |2.6 |2.5 |2.4 |2.3 |2.2 |2.1.7 |2.0 |
| Apache Hadoop & YARN |2.7.3 |2.7.3 |2.7.1 |2.7.1 |2.6.0 |2.4.0 |2.2.0 |
| Apache Tez |0.7.0 |0.7.0 |0.7.0 |0.7.0 |0.5.2 |0.4.0 |-|
| Apache Pig |0.16.0 |0.16.0 |0.15.0 |0.15.0 |0.14.0 |0.12.1 |0.12.0 |
| Apache Hive & HCatalog |1.2.1 |1.2.1 |1.2.1 |1.2.1 |0.14.0 |0.13.1 |0.12.0 |
| Apache Hive2 | 2.1.0 |-|-|-|-|-|-|
| Apache Tez-Hive2 | 0.8.4 |-|-|-|-|-|-|
| Apache Ranger | 0.7.0 |0.6.0 |-|-|-|-|-|
| Apache HBase |1.1.2 |1.1.2 |1.1.2 |1.1.1 |0.98.4 |0.98.0 |-|
| Apache Sqoop |1.4.6 |1.4.6 |1.4.6 |1.4.6 |1.4.5 |1.4.4 |1.4.4 |
| Apache Oozie |4.2.0 |4.2.0 |4.2.0 |4.2.0 |4.1.0 |4.0.0 |4.0.0 |
| Apache Zookeeper |3.4.6 |3.4.6 |3.4.6 |3.4.6 |3.4.6 |3.4.5 |3.4.5 |
| Apache Storm |1.1.0 |1.0.1 |0.10.0 |0.10.0 |0.9.3 |0.9.1 |-|
| Apache Mahout |0.9.0+ |0.9.0+ |0.9.0+ |0.9.0+ |0.9.0 |0.9.0 |-|
| Apache Phoenix |4.7.0 |4.7.0 |4.4.0 |4.4.0 |4.2.0 |4.0.0.2.1.7.0-2162 |-|
| Apache Spark |2.1.0 (Linux のみ) |1.6.2 + 2.0 (Linux のみ) |1.6.0 (Linux のみ) |1.5.2 (Linux のみ/試験的ビルド) |1.3.1 (Windows のみ) |-|-|
| Apache Kafka | 0.10.0 | 0.10.0 | 0.9.0 |-|-|-|-|
| Apache Ambari | 2.5.0 | 2.4.0 | 2.2.1 | 2.1.0 |-|-|-|
| Apache Zeppelin | 0.7.0 |-|-|-|-|-|-|
| Mono |4.2.1 |4.2.1 |3.2.8 |-|-|-|


## <a name="how-to-check-current-hadoop-component-version-information"></a>現在の Hadoop コンポーネントのバージョン情報を確認する方法

HDInsight クラスターのバージョンに関連付けられた Hadoop エコシステム コンポーネントのバージョンは、将来 HDInsight が更新されたときに変更される可能性があります。 Hadoop コンポーネントを調べて、どのバージョンがクラスターに使用されているかを確認するには、Ambari REST API を使用します。 **GetComponentInformation** コマンドによって、サービス コンポーネントに関する情報を取得します。 詳細については、[Ambari のドキュメント][ambari-docs]を参照してください。

Windows ベースのクラスターのみ: コンポーネントのバージョンを取得するもう 1 つの方法は、リモート デスクトップを使用してクラスターにログインし、"C:\apps\dist\" ディレクトリの内容を直接確認することです。

> [!IMPORTANT]
> Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[HDInsight 3.3 の廃止](#hdi-version-33-nearing-deprecation-date)に関するページを参照してください。 

### <a name="release-notes"></a>リリース ノート

HDInsight の最新バージョンに関する追加のリリース ノートは、 [HDInsight リリース ノート](hdinsight-release-notes.md) を参照してください。

## <a name="supported-hdinsight-versions"></a>サポートされる HDInsight のバージョン
次の表に、現在使用できる HDInsight のバージョン、該当する Hortonworks Data Platform のバージョン、そのリリース日を示します。 非推奨となった場合は、サポート有効期限とその日付も記載されます。 次のバージョン情報に注意してください。

* ヘッド ノードを 2 つ備えた可用性の高いクラスターは、HDInsight 2.1 以降では既定でデプロイされています。 HDInsight 1.6 クラスターでは利用できません。
* 特定のバージョンのサポート期限が切れると、Azure ポータルから利用できなくなる可能性があります。 次の表に、Azure クラシック ポータルで利用できるバージョンを示します。 クラスター バージョンは、非推奨となるまでは、Windows PowerShell [New-AzureRmHDInsightCluster](https://msdn.microsoft.com/library/mt619331.aspx) コマンドの `Version` パラメーターと .NET SDK で利用できます。

| HDInsight のバージョン | HDP のバージョン | VM の OS | 高可用性 | リリース日 | Azure ポータルでの利用 | サポート有効期限 | 非推奨となる日 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| HDI 3.6 |HDP 2.6 |Ubuntu 16 |はい |04/06/2017 |はい | | |
| HDI 3.5 |HDP 2.5 |Ubuntu 16 |はい |9/30/2016 |はい |07/05/2017 |05/31/2018 |
| HDI 3.4 |HDP 2.4 |Ubuntu 14.0.4 LTS |あり |2016 年 3 月 29 日 |はい |12/29/2016 |1/9/2018 |
| HDI 3.3 |HDP 2.3 |Ubuntu 14.0.4 LTS または Windows Server 2012R2 |あり |2015 年 12 月 2 日 |あり |2016 年 6 月 27 日 |2017 年 7 月 31 日 |
| HDI 3.2 |HDP 2.2 |Ubuntu 12.04 LTS または Windows Server 2012R2 |あり |2015 年 2 月 18 日 |いいえ |2016 年 3 月 1 日 |2017 年 4 月 1 日 |
| HDI 3.1 |HDP 2.1 |Windows Server 2012R2 |あり |2014 年 6 月 24 日 |いいえ |2015 年 5 月 18 日 |2016 年 6月 30 日 |
| HDI 3.0 |HDP 2.0 |Windows Server 2012R2 |あり |02/11/2014 |いいえ |09/17/2014 |06/30/2015 |
| HDI 2.1 |HDP 1.3 |Windows Server 2012R2 |あり |10/28/2013 |いいえ |05/12/2014 |05/31/2015 |
| HDI 1.6 |HDP 1.1 | |いいえ |10/28/2013 |いいえ |04/26/2014 |05/31/2015 |

## <a name="hdi-version-33-nearing-deprecation-date"></a>非推奨となる日が近づいている HDI バージョン 3.3
HDI 3.3 クラスターは、2016 年 6 月 27 日にサポート期間が終了しており、2017 年 7 月 31 日に非推奨となります。 HDI 3.3 クラスターを利用している場合は、すぐにクラスターを HDI 3.5 または HDI 3.6 にアップグレードしてください。 HDI 3.3 Windows の非推奨となるタイムラインは、地域によって異なる場合があります。 非推奨となる日付の予定がお住まいの地域で異なる場合は、個別に通知されます。

### <a name="the-service-level-agreement-for-hdinsight-cluster-versions"></a>HDInsight クラスター バージョンのサービス レベル アグリーメント
SLA は、**サポート ウィンドウ**の条件で定義されます。 サポート ウィンドウとは、HDInsight クラスターのバージョンが Microsoft カスタマー サービス & サポートによってサポートされる期間を指しています。 HDInsight クラスターは、そのバージョンの **サポート有効期限** が現在の日付を過ぎている場合、サポート対象外となります。 サポートされている HDInsight クラスターのバージョンの一覧は上記のテーブルに示されています。 所定の HDInsight Version X (新しい X+1 バージョンが利用可能なった後) のサポート有効期限は、次の数式で計算した日付のうち、遅い方とされます。  

* 数式 1: HDInsight クラスター バージョン X がリリースされた日に 180 日を加える。
* 数式 2: HDInsight クラスター バージョン X+1 (X の次のバージョン) がポータルで使用可能になった日付に 90 日を加える。

**非推奨となる日** は、それ以降 HDInsight でそのクラスター バージョンが作成できなくなる日付です。 2017 年 7 月 31 日以降、非推奨となる日より後にクラスターのサイズは変更できません。 

> [!NOTE]
> Windows ベースの HDInsight クラスター (バージョン 2.1、3.0、3.1、3.2、および 3.3 を含む) は Azure ゲスト OS ファミリ 4 で実行されます。この OS は Windows Server 2012 R2 の 64 ビット版を使用し、.NET Framework 4.0、4.5、4.5.1、および 4.5.2 をサポートします。
>
>

## <a name="hortonworks-release-notes-associated-with-hdinsight-versions"></a>HDInsight バージョンに対応する Hortonworks リリース ノート
* HDInsight クラスター Version 3.6 は、[Hortonworks Data Platform 2.6](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.0/bk_release-notes/content/ch_relnotes.html) を基盤とする Hadoop ディストリビューションを使用します。 
* HDInsight クラスター Version 3.5 は、[Hortonworks Data Platform 2.5](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.5.0/bk_release-notes/content/ch_relnotes_v250.html) を基盤とする Hadoop ディストリビューションを使用します。 これが、ポータルの使用時に作成される **既定** の Hadoop クラスターです。
* HDInsight クラスター Version 3.4 は、 [Hortonworks Data Platform 2.4](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.4.0/bk_HDP_RelNotes/content/ch_relnotes_v240.html)を基盤とする Hadoop ディストリビューションを使用します。 
* HDInsight クラスター Version 3.3 は、 [Hortonworks Data Platform 2.3](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.3.0/bk_HDP_RelNotes/content/ch_relnotes_v230.html)を基盤とする Hadoop ディストリビューションを使用します。

  * Apache Storm リリース ノートは、 [こちら](https://storm.apache.org/2015/11/05/storm0100-released.html)で入手できます。
  * Apache Hive リリース ノートは、 [こちら](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12332384&styleName=Text&projectId=12310843)で入手できます。
* HDInsight クラスター Version 3.2 は、[Hortonworks Data Platform 2.2][hdp-2-2] を基盤とする Hadoop ディストリビューションを使用します。  

  * 各 Apache コンポーネントのリリース ノート  - [Hive 0.14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310843&version=12326450)、[Pig 0.14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310730&version=12326954)、[HBase 0.98.4](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310753&version=12326810)、[Phoenix 4.2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12315120&version=12327581)、[M/R 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310941&version=12327180)、[HDFS 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310942&version=12327181)、[YARN 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12313722&version=12327197)、[Common](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310240&version=12327179)、[Tez 0.5.2](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314426&version=12328742)、[Ambari 2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12312020&version=12327486)、[Storm 0.9.3](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314820&version=12327112)、[Oozie 4.1.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12324960&projectId=12311620)。
* HDInsight クラスター Version 3.1 は、[Hortonworks Data Platform 2.1.7][hdp-2-1-7] を基盤とする Hadoop ディストリビューションを使用します。2014 年 11 月 7 日より前に作成された HDInsight 3.1 クラスターは、[Hortonworks Data Platform 2.1.1][hdp-2-1-1] に基づいています。
* HDInsight クラスター Version 3.0 は、[Hortonworks Data Platform 2.0][hdp-2-0-8] を基盤とする Hadoop ディストリビューションを使用します。
* HDInsight クラスター Version 2.1 は、[Hortonworks Data Platform 1.3][hdp-1-3-0] を基盤とする Hadoop ディストリビューションを使用します。
* HDInsight クラスター Version 1.6 は、[Hortonworks Data Platform 1.1][hdp-1-1-0] を基盤とする Hadoop ディストリビューションを使用します。

## <a name="hdinsight-standard-and-hdinsight-premium"></a>HDInsight Standard と HDInsight Premium

Azure HDInsight では、**Standard** と **Premium** の 2 つのカテゴリでビッグ データ クラウド サービスを提供します。 以下の表に、**Premium でのみ**利用できる機能を示します。 この表に明示的に書かれていない機能は、Standard の機能として提供されています。

> [!NOTE]
> HDInsight Premium は現在プレビューの段階で、Linux クラスターでのみ利用できます。
>
>

| HDInsight Premium の機能 | Description |
| --- | --- |
| ドメイン参加済み HDInsight クラスター |HDInsight クラスターを Azure Active Directory (AAD) のドメインに参加させることで、エンタープライズ レベルのセキュリティを実現します。 Azure Active Directory を使って HDInsight クラスターへのログオンを認証できる従業員の一覧を構成できるようになりました。 エンタープライズ管理者は、[Apache Ranger](http://hortonworks.com/apache/ranger/) を使用して Hive のセキュリティを確保するためのロールベースのアクセス制御を構成することもできるため、データへのアクセスを必要な分だけに制限できます。 また、管理者は、従業員によるデータ アクセスとアクセス制御ポリシーに対して行われた変更を監査できるため、企業リソースの高度なガバナンスを実現できます。 詳しくは、[ドメイン参加済み HDInsight クラスターの構成](hdinsight-domain-joined-configure.md)に関するページをご覧ください。 |

### <a name="cluster-types-supported-for-hdinsight-premium"></a>HDInsight Premium でサポートされるクラスターの種類
次の表に、HDInsight クラスターの種類と Premium のサポート状況を示します。

| クラスターの種類 | Standard | Premium (プレビュー) |
| --- | --- | --- |
| Hadoop は、 |はい |はい (HDInsight 3.5 および 3.6 のみ) |
| Spark |はい |いいえ |
| HBase |あり |いいえ |
| Storm |あり |いいえ |
| R Server  |はい |いいえ |
| 対話型 Hive (プレビュー) |はい |なし |
| Kafka (プレビュー)|はい|いいえ| 

HDInsight Premium でサポートされるクラスターの種類が追加されたら、それに応じてこの表も更新されます。

### <a name="features-not-supported-for-hdinsight-premium"></a>HDInsight Premium でサポートされない機能

現時点では、次の機能は HDInsight Premium クラスターでサポートされていません。

* **プライマリ ストレージとしての Azure Data Lake Store の使用**。 ただし、HDInsight Premium クラスターで Azure Data Lake Store をアドオン ストレージとして使用することはできます。

### <a name="pricing-and-sla"></a>料金と SLA
HDInsight Premium の料金と SLA の詳細については、「 [HDInsight の価格](https://azure.microsoft.com/pricing/details/hdinsight/)」を参照してください。


[image-hdi-versioning-versionscreen]: ./media/hdinsight-component-versioning/hdi-versioning-version-screen.png

[wa-forums]: http://azure.microsoft.com/support/forums/

[connect-excel-with-hive-ODBC]: hdinsight-connect-excel-hive-ODBC-driver.md

[hdp-2-2]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.2.0/HDP_2.2.0_Release_Notes_20141202_version/index.html

[hdp-2-1-7]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html

[hdp-2-1-1]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html

[hdp-2-0-8]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html

[hdp-1-3-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html

[hdp-1-1-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-Win-1.1/bk_releasenotes_HDP-Win/content/ch_relnotes-hdp-win-1.1.0_1.html

[ambari-docs]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md

[zookeeper]: http://zookeeper.apache.org/

