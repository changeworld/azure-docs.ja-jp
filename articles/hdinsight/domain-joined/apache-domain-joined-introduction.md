---
title: ドメイン参加済み Azure HDInsight クラスターでの Hadoop セキュリティの概要
description: ドメイン参加済み Azure HDInsight クラスターでエンタープライズ セキュリティの 4 つの柱をサポートする方法について説明します。
services: hdinsight
ms.service: hdinsight
author: omidm1
ms.author: omidm
editor: jasonwhowell
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/26/2018
ms.openlocfilehash: e697b0ffe7cde65cd47a2f1b1db5b544b6dc1d3c
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2018
ms.locfileid: "39595632"
---
# <a name="an-introduction-to-hadoop-security-with-domain-joined-hdinsight-clusters"></a>ドメイン参加済み HDInsight クラスターでの Hadoop セキュリティの概要

これまでに、Azure HDInsight では、単一ユーザーのローカル管理者のみをサポートしていました。これは、小規模なアプリケーション チームや部門に適していました。 Hadoop ベースのワークロードがエンタープライズ部門に普及するに伴い、Active Directory ベースの認証、マルチユーザーのサポート、ロールベースのアクセス制御などのエンタープライズ レベルの機能の必要性がますます重要になっています。 

Active Directory ドメインに参加している HDInsight クラスターを作成できます。 Azure Active Directory を使って HDInsight クラスターへのログオンを認証できる企業の従業員の一覧を構成できるようになります。 社外のユーザーは、HDInsight クラスターにログオンすることもアクセスすることもできません。 

エンタープライズ管理者は、[Apache Ranger](http://hortonworks.com/apache/ranger/) を使用して Hive のセキュリティを確保するためのロールベースのアクセス制御 (RBAC) を構成できます。 RBAC を構成すると、必要な内容へのデータ アクセスのみに制限されます。 最後に、管理者は従業員によるデータ アクセスとアクセス制御ポリシーに加えられた変更を監査できます。 その後、管理者は企業リソースにおいて高度なガバナンスを実現できます。

> [!NOTE]
> この記事で説明する新機能のプレビュー版を使用できるクラスターの種類は、Hadoop、Spark、および対話型クエリのみです。 Oozie は、ドメイン参加済みのクラスターで使用できるようになりました。 Oozie Web UI にアクセスするには、[トンネリング](../hdinsight-linux-ambari-ssh-tunnel.md)を有効にする必要があります。

エンタープライズ セキュリティには、境界セキュリティ、認証、承認、暗号化の 4 つの大きな柱があります。

![エンタープライズ セキュリティの 4 つの柱でのドメイン参加済み HDInsight クラスターの利点](./media/apache-domain-joined-introduction/hdinsight-domain-joined-four-pillars.png).

## <a name="perimeter-security"></a>境界セキュリティ
HDInsight の境界セキュリティは、仮想ネットワークと Azure VPN Gateway サービスを使用して実現されます。 エンタープライズ管理者は、仮想ネットワーク内に HDInsight クラスターを作成し、ネットワーク セキュリティ グループ (ファイアウォール規則) を使用して仮想ネットワークへのアクセスを制限できます。 HDInsight クラスターと通信できるのは、受信ファイアウォール規則で定義されている IP アドレスだけです。 この構成では、境界セキュリティを提供します。

境界セキュリティのもう 1 つの層は、VPN Gateway サービスを使用して実現されます。 ゲートウェイは、HDInsight クラスターへのすべての受信要求に対する防御の最前線として機能します。 ゲートウェイでは要求を受け取り、検証して、クラスター内の他のノードに渡す要求のみを許可します。 この方法では、ゲートウェイは境界セキュリティをクラスター内の他のノードとデータ ノードに提供します。

## <a name="authentication"></a>Authentication
エンタープライズ管理者は、ドメイン参加済み HDInsight クラスターを[仮想ネットワーク](https://azure.microsoft.com/services/virtual-network/)に作成できます。 HDInsight クラスターのノードはすべて、企業が管理するドメインに参加することになります。 これは、[Azure Active Directory Domain Services](../../active-directory-domain-services/active-directory-ds-overview.md) を使用して実現されます。 

この設定により、企業の従業員は、ドメイン資格情報を使用してクラスター ノードにログオンできます。 また、ドメイン資格情報を使用して、クラスターと対話する他の承認済みエンドポイント (Ambari Views、ODBC、JDBC、PowerShell、REST API など) で認証することもできます。 管理者は、これらのエンドポイントを使用してクラスターを操作するユーザーの数の制限を完全に制御できます。

## <a name="authorization"></a>Authorization
ほとんどの企業では、すべての従業員がすべてのエンタープライズ リソースにはアクセスできないようにするベスト プラクティスに従っています。 同様に、管理者はクラスター リソースのロールベースのアクセス制御ポリシーを定義できます。 

たとえば、管理者は [Apache Ranger](http://hortonworks.com/apache/ranger/) を構成して Hive のアクセス制御ポリシーを設定できます。 この機能により、従業員は仕事を順調に進めるために必要な量のデータにのみアクセスできます。 クラスターへの SSH アクセスも管理者だけに制限されます。

## <a name="auditing"></a>監査
リソースにおいて許可されていないアクセスや意図しないアクセスを追跡するには、クラスター リソース、およびデータへのアクセスをすべて監査する必要があります。 HDInsight クラスター リソースを許可されていないユーザーから保護し、データをセキュリティで保護することが重要です。 

管理者は HDInsight クラスター リソースとデータへのすべてのアクセスを表示し、レポートを作成できます。 また、管理者は Apache Ranger のサポートされているエンドポイントで作成されたアクセス制御ポリシーのすべての変更を表示し、レポートを作成することもできます。 

ドメイン参加済み HDInsight クラスターでは、使い慣れた Apache Ranger UI を使用して監査ログを検索します。 バックエンドでは、Ranger はログの保存と検索に [Apache Solr](http://hortonworks.com/apache/solr/) を使用します。

## <a name="encryption"></a>暗号化
データの保護は、組織のセキュリティとコンプライアンス要件を満たすために重要です。 許可されていない従業員からデータへのアクセスを制限すると共に、暗号化する必要があります。 

HDInsight クラスターのデータ ストア (Azure BLOB ストレージおよび Azure Data Lake Storage Gen1) はどちらも、保存データの透過的なサーバー側[暗号化](../../storage/common/storage-service-encryption.md)をサポートしています。 セキュリティで保護された HDInsight クラスターは、この保存データのサーバー側暗号化の機能とシームレスに連携します。

## <a name="next-steps"></a>次の手順
* [ドメイン参加済み HDInsight クラスターの計画](apache-domain-joined-architecture.md)
* [ドメイン参加済み HDInsight クラスターを構成する](apache-domain-joined-configure.md)
* [ドメイン参加済み HDInsight クラスターの管理](apache-domain-joined-manage.md)
* [ドメイン参加済み HDInsight クラスターでの Hive ポリシーの構成](apache-domain-joined-run-hive.md)
* [HDInsight で SSH を使用する](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined)

