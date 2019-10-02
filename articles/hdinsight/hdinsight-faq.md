---
title: Azure HDInsight - よく寄せられる質問
description: Azure HDInsight の概要について説明します
author: Ramakoni1
ms.author: ramakoni
ms.reviewer: v-miegge
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: 4accd8c2f58dd89fb5f918ca6e58e71caeaed57f
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2019
ms.locfileid: "71213871"
---
# <a name="azure-hdinsight-frequently-asked-questions"></a>Azure HDInsight: よく寄せられる質問

この記事では、[Azure HDInsight](https://azure.microsoft.com/services/hdinsight/) の実行方法についてよく寄せられる質問への回答を提供します。

## <a name="creating-or-deleting-hdinsight-clusters"></a>HDInsight クラスターの作成または削除

### <a name="how-do-i-provision-an-hdinsight-cluster"></a>HDInsight クラスターをプロビジョニングするにはどうすればよいですか。

使用できる HDInsight クラスターの種類とプロビジョニング方法を確認するには、「[HDInsight で Apache Hadoop、Apache Spark、Apache Kafka などを使用してクラスターを設定する](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-provision-linux-clusters)」を参照してください。

### <a name="how-do-i-delete-an-existing-hdinsight-cluster"></a>既存の HDInsight クラスターを削除するにはどうすればよいですか。

使用されなくなったクラスターを削除する方法の詳細については、「[ブラウザー、PowerShell、または Azure CLI を使用して HDInsight クラスターを削除する](https://docs.microsoft.com/azure/hdinsight/hdinsight-delete-cluster)」を参照してください。 1 つのクラスターに対する作成操作と削除操作の間隔は少なくとも 30 から 60 分にすることをお勧めします。 そうしないと、操作が失敗し、次のエラー メッセージが返される可能性があります。

``Conflict (HTTP Status Code: 409) error when attempting to delete a cluster immediately after creation of a cluster. If you encounter this error, wait until the newly created cluster is in operational state before attempting to delete it.``

### <a name="how-do-i-select-the-correct-number-of-cores-or-nodes-for-my-workload"></a>ワークロードに適したコアまたはノードの数を選択するにはどうすればよいですか。

適切なコア数とその他の構成オプションは、さまざまな要因によって変わります。

詳細については、「[HDInsight クラスターの容量計画](https://docs.microsoft.com/azure/hdinsight/hdinsight-capacity-planning)」を参照してください。

### <a name="what-can-i-do-when-cluster-provisioning-fails-because-of-a-capacity-issue"></a>容量の問題のためにクラスターのプロビジョニングが失敗した場合はどうすればよいですか。

容量に関する一般的な問題のエラーと軽減策については、このセクションで説明しています。

#### <a name="error-the-deployment-would-exceed-the-quota-of-800"></a>エラー:The deployment would exceed the quota of '800' (デプロイが '800' のクォータを超えています)

Azure では、リソース グループあたり 800 デプロイのクォータ制限があります。 リソース グループ、サブスクリプション、アカウント、または他のスコープごとに異なるクォータが適用されています。 たとえば、ご利用のサブスクリプションの構成により、リージョンごとのコア数が制限されている場合があります。 許容される量よりも多くのコアがある仮想マシンをデプロイしようとすると、クォータを超えたことを示すエラー メッセージが表示されます。

この問題を解決するには、Azure portal、CLI、または PowerShell を使用して、不要になったデプロイを削除します。

詳細については、「[リソース クォータのエラーを解決する](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors)」を参照してください。

#### <a name="error-the-maximum-node-exceeded-the-available-cores-in-this-region"></a>エラー:The maximum node exceeded the available cores in this region (最大ノードがこのリージョンで使用できるコア数を超えました)

ご利用のサブスクリプションの構成により、リージョンごとのコア数が制限されている場合があります。 許容される量よりも多くのコアがあるリソースをデプロイしようとすると、クォータを超えたことを示すエラー メッセージが表示されます。

クォータの引き上げを依頼するには、次の手順を実行します。

1. Azure portal にアクセスし、[ヘルプとサポート] を選択します。
2. [新しいサポート要求] を選択します
3. [新しいサポート要求] ページの [基本] タブで、次のオプションを選択します。

   * [問題の種類]\: サービスとサブスクリプションの制限 (クォータ)
   * サブスクリプション:変更するサブスクリプション
   * [クォータの種類]: HDInsight

詳細については、[サポート チケットを作成してコア数を増やす](https://docs.microsoft.com/azure/hdinsight/hdinsight-capacity-planning#quotas)方法に関する記事を参照してください。

### <a name="what-are-the-various-types-of-nodes-in-an-hdinsight-cluster"></a>HDInsight クラスターにはどのような種類のノードがありますか。

Azure HDInsight クラスターには、さまざまな種類の仮想マシン、つまりノードがあります。 それぞれのノードの種類が、システムの運用において役割を果たしています。

詳細については、「[Azure HDInsight クラスターでのリソースの種類](https://docs.microsoft.com/azure/hdinsight/hdinsight-virtual-network-architecture#resource-types-in-azure-hdinsight-clusters)」を参照してください。

## <a name="individual-components"></a>個別のコンポーネント

### <a name="can-i-install-additional-components-on-my-cluster"></a>クラスターには追加のコンポーネントをインストールできますか。

はい。次のいずれかの方法を使用して、追加のコンポーネントをインストールしたり、クラスター構成をカスタマイズしたりすることができます。

* 作成時または作成後にスクリプトを使用します。 このようなスクリプトは、[スクリプト操作](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux)を介して呼び出されます。 この構成オプションは、Azure portal、HDInsight Windows PowerShell コマンドレット、または HDInsight .NET SDK から使用できます。 
* クラスターがプロビジョニングされた後、sudo または他のメソッドを使用します。
* [HDInsight アプリケーション プラットフォーム](https://azure.microsoft.com/services/hdinsight/partner-ecosystem/)を使用して、エコシステム アプリケーションをインストールします。

ただし、Microsoft サポート チームは、次の状況に対してのみサポートを提供できます。

* スクリプトの読み込み中に発生した問題またはエラー。 カスタム スクリプトの実行中に発生したエラーは、サポート チケットの範囲外です。 
* クラスターの作成プロセスに含まれる追加のアプリケーション。 

サポートされるコンポーネントの一覧については、「[HDInsight で使用できる Apache Hadoop コンポーネントおよびバージョンとは](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions)」を参照してください。

個々のコンポーネントのサポートについても、クラスターの種類ごとに異なる場合があります。 たとえば、Spark は Kafka クラスターではサポートされません。また、その逆も同様です。

クラスター作成プロセス以外のアプリケーションまたはサービスについては、対応するベンダーまたはサービス プロバイダーにお問い合わせください。 これらのアクションのサポートには、コミュニティ サイトを利用することもできます。 多くのコミュニティ サイトを利用できます。 たとえば、[HDInsight の MSDN フォーラム](https://social.msdn.microsoft.com/Forums/home?forum=hdinsight)や、[Stack Overflow](https://stackoverflow.com/questions/tagged/hdinsight) などです。 Apache プロジェクトにも、[Apache の Web サイト](https://apache.org/)にプロジェクトのサイトがあります。 たとえば、[Hadoop](https://hadoop.apache.org/) などです。

 Azure サポートに関連するその他の質問については、「[Azure サポートに関する FAQ](https://azure.microsoft.com/en-us/support/faq/)」を参照してください。

### <a name="can-i-upgrade-the-individual-components-that-are-pre-installed-on-the-cluster"></a>クラスターにプレインストールされている個々のコンポーネントをアップグレードできますか。

クラスターにプレインストールされている組み込みのコンポーネントまたはアプリケーションをアップグレードする場合、結果の構成は Microsoft によってサポートされません。 このようなシステム構成は、Microsoft によってテストされていません。 アップグレード済みのバージョンのコンポーネントがプレインストールされている可能性のある別のバージョンの HDInsight クラスターを使用してみてください。

たとえば、個々のコンポーネントとして Hive をアップグレードすることはサポートされていません。 HDInsight はマネージド サービスであり、多くのサービスは Ambari サーバーと統合され、テストされています。 Hive のみをアップグレードすると、他のコンポーネントのインデックス付きバイナリが変更され、クラスター上でコンポーネント統合の問題が発生します。

### <a name="can-spark-and-kafka-run-on-the-same-hdinsight-cluster"></a>Spark と Kafka は同じ HDInsight クラスター上で実行できますか。

いいえ。同じ HDInsight クラスター上で Apache Kafka と Apache Spark を実行することはできません。 リソースの競合の問題を回避するには、それぞれに個別のクラスターを作成する必要があります。

### <a name="how-do-i-change-timezone-in-ambari"></a>Ambari のタイムゾーンは変更するにはどうすればよいですか。

1. https://CLUSTERNAME.azurehdinsight.net の Ambari Web UI を開きます。この CLUSTERNAME は実際のクラスターの名前です。
2. 右上隅の [admin | Settings]\(管理者 | 設定\) を選択します。 

   ![Ambari の設定](media/hdinsight-faq/ambari-settings.png)

3. [User Settings]\(ユーザー設定\) ウィンドウの [Timezone]\(タイムゾーン\) ドロップ ダウンから新しいタイムゾーンを選択し、[Save]\(保存\) をクリックします。

   ![Ambari の [User Settings]\(ユーザー設定\)](media/hdinsight-faq/ambari-user-settings.png)

## <a name="metastore"></a>メタストア

### <a name="how-can-i-migrate-from-the-existing-metastore-to-azure-sql-server"></a>既存のメタストアから Azure SQL Server に移行するにはどうすればよいですか。 

SQL Server から Azure SQL Server に移行する方法については、「[チュートリアル:DMS を使用して SQL Server を Azure SQL Database の単一データベースまたはプールされたデータベースにオフラインで移行する](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-azure-sql)」を参照してください。

### <a name="is-the-hive-metastore-deleted-when-the-cluster-is-deleted"></a>クラスターが削除されると Hive メタストアは削除されますか。

使用するようにクラスターが構成されているメタストアの種類によって異なります。

既定のメタストアの場合:既定のメタストアは、クラスターのライフサイクルの一部です。 クラスターを削除すると、対応する metastore とメタデータも削除されます。

カスタムのメタストアの場合:メタストアのライフサイクルは、クラスターのライフサイクルに関連付けられていません。 そのため、メタデータを失うことなくクラスターを作成および削除できます。 Hive スキーマなどのメタデータは、HDInsight クラスターを削除して再作成した後でも保持されます。

詳細については、[Azure HDInsight での外部メタデータ ストアの使用](https://docs.microsoft.com/azure/hdinsight/hdinsight-use-external-metadata-stores)に関する記事を参照してください。

### <a name="does-migrating-a-hive-metastore-also-migrate-the-default-policies-of-the-ranger-database"></a>Hive メタストアを移行すると、Rangers データベースの既定のポリシーも移行されますか。

いいえ。ポリシー定義は、Rangers データベース内にあります。 そのため、Rangers データベースを移行してもポリシーは移行されません。

### <a name="can-you-migrate-a-hive-metastore-from-an-enterprise-security-package-esp-cluster-to-a-non-esp-cluster-and-vice-versa"></a>Enterprise セキュリティ パッケージ (ESP) クラスターから非 ESP クラスターに (およびその逆に) Hive メタストアを移行できますか。

はい。ESP から非 ESP クラスターに Hive メタストアを移行することができます。

### <a name="how-can-i-estimate-the-size-of-a-hive-metastore-database"></a>Hive メタストア データベースのサイズを見積もるにはどうすればよいですか。

Hive メタストアは、Hive サーバーによって使用されるデータ ソースのメタデータを格納するために使用されます。 そのため、サイズの要件は、Hive に使用する必要があるデータ ソースの数と、データ ソースの複雑さによって影響を受けます。 そのため、サイズを事前に見積もることはできません。 「[Hive metastore のベスト プラクティス](https://docs.microsoft.com/azure/hdinsight/hdinsight-use-external-metadata-stores#hive-metastore-best-practices)」で説明されているように、S2 以上のレベルでは 50 DTU と 250 GB のストレージが提供されます。 ボトルネックにぶつかった場合は、データベースをスケールアップできます。

### <a name="do-you-support-any-other-database-other-than-azure-sql-database-as-an-external-metastore"></a>外部メタストアとして Azure SQL Database 以外のデータベースはサポートされていますか。

いいえ。Microsoft では、外部カスタム メタストアとして Azure SQL Database のみをサポートしています。

### <a name="can-i-share-a-metastore-across-multiple-clusters"></a>複数のクラスター間でメタストアを共有することはできますか。

はい。同じバージョンの HDInsight を使用している限り、複数のクラスターでカスタムのメタストアを共有できます。

## <a name="connectivity-and-virtual-networks"></a>接続と仮想ネットワーク  

### <a name="what-are-the-implications-of-blocking-ports-22-and-23-on-my-network"></a>ネットワーク上でポート 22 と 23 をブロックすると、どのような影響がありますか。

ポート 22 とポート 23 をブロックすると、クラスターに SSH でアクセスできなくなります。 これらのポートは、HDInsight サービスでは使用されません。

詳細については、以下のドキュメントをご覧ください。

* [ネットワーク トラフィックのコントロール](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment#networktraffic)
* [Secure incoming traffic to HDInsight clusters in a virtual network with private endpoint](https://azure.microsoft.com/blog/secure-incoming-traffic-to-hdinsight-clusters-in-a-vnet-with-private-endpoint/) (プライベート エンドポイントを使用した、仮想ネットワーク内の HDInsight クラスターへのセキュリティで保護された着信トラフィック)
* [HDInsight の管理 IP アドレス](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses)

### <a name="can-i-deploy-an-additional-virtual-machine-within-the-same-subnet-as-an-hdinsight-cluster"></a>HDInsight クラスターと同じサブネット内に追加の仮想マシンをデプロイできますか。

はい。HDInsight クラスターと同じサブネット内に追加の仮想マシンをデプロイできます。 次の構成が可能です。

* エッジ ノード:クラスターに別のエッジ ノードを追加することができます。詳細については、「[HDInsight の Apache Hadoop クラスターで空のエッジ ノードを使用する](https://docs.microsoft.com/azure/hdinsight/hdinsight-apps-use-edge-node)」を参照してください。 
* スタンドアロン ノード:スタンドアロンの仮想マシンを同じサブネットに追加し、その仮想マシンからクラスターにアクセスするには、プライベート エンド ポイント `https://<CLUSTERNAME>-int.azurehdinsight.net` を使用します。 詳細については、「[ネットワーク トラフィックのコントロール](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment#networktraffic)」を参照してください。

### <a name="can-i-add-an-existing-hdinsight-cluster-to-another-virtual-network"></a>既存の HDInsight クラスターを別の仮想ネットワークに追加することはできますか。

いいえ。既存の HDInsight クラスターを別の仮想ネットワークに追加することはできません。 仮想ネットワークは、プロビジョニング時に指定する必要があります。 プロビジョニング時に仮想ネットワークが指定されていない場合は、デプロイによって、外部からアクセスできない内部ネットワークが作成されます。 詳細については、「[既存の仮想ネットワークへの HDInsight の追加](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment#existingvnet)」を参照してください。

## <a name="security-and-certificates"></a>セキュリティと証明書

Azure HD insights クラスターでのマルウェア対策に関する Microsoft の推奨事項は何ですか。

マルウェア対策の詳細については、「[Azure Cloud Services および Virtual Machines 向け Microsoft マルウェア対策](https://docs.microsoft.com/azure/security/fundamentals/antimalware)」を参照してください。

### <a name="how-do-i-create-a-keytab-for-an-hdinsight-esp-cluster"></a>HDInsight ESP クラスター用にキー タブを作成するにはどうすればよいですか。

ドメイン ユーザー名の Kerberos keytab を作成できます。 後でこの keytab を使用して、パスワードを入力することなく、リモートのドメインに参加しているクラスターに対する認証を行うことができます。 ドメイン名に大文字が含まれていることに注意してください。

```
o ktutil 
o ktutil: addent -password -p stXX@ DOMAIN.COM -k 1 -e RC4-HMAC 
o Password for stXX@DOMAIN.COM: provided by lab instructors 
o ktutil: wkt stXX.keytab 
o ktutil: q
```

### <a name="can-i-use-an-existing-azure-active-directory-tenant-to-create-an-hdinsight-cluster-that-has-the-esp"></a>既存の Azure Active Directory テナントを使用して、ESP がある HDInsight クラスターを作成できますか。

ESP がある HDInsight クラスターを作成する前に、Azure Active Directory Domain Services (AAD-DS) を有効にしておく必要があります。 オープンソースの Hadoop は、(OAuth とは対照的に) 認証に Kerberos を使用します。

仮想マシンをドメインに参加させるには、ドメイン コントローラーが必要です。 AAD-DS は、マネージド ドメイン コントローラーです。 これは、セキュリティで保護された Hadoop クラスターをマネージ方式で構築するための Kerberos 要件をすべて提供する AAD の拡張機能と考えられます。 マネージド サービスとしての HDInsight は、エンドツーエンドのセキュリティを提供するために AAD-DS と統合されています。

### <a name="can-i-use-a-self-signed-certificate-in-an-aad-ds-secure-ldap-setup-and-provision-an-esp-cluster"></a>AAD-DS のセキュリティで保護された LDAP 設定で自己署名証明書を使用し、ESP クラスターをプロビジョニングすることはできますか。

証明機関 (CA) から発行された証明書を使用することをお勧めしますが、自己署名証明書の使用は ESP でもサポートされています。

詳細については、次を参照してください。

* [Azure AD-DS](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds#enable-azure-ad-ds) を有効にする
* [チュートリアル:Azure Active Directory Domain Services のマネージド ドメイン用に Secure LDAP を構成する](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-configure-ldaps)

### <a name="how-can-i-pull-login-activity-shown-in-ranger"></a>Ranger に表示されているログイン アクティビティをプルするにはどうすればよいですか。

監査の要件については、「[Azure Monitor ログを使用して HDInsight クラスターを監視する](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial)」の説明に従って Azure Monitor ログを有効にすることをお勧めします。

### <a name="can-i-disable-clamscan-on-my-cluster"></a>クラスターで Clamscan を無効にすることはできますか。

Clamscan は、HDInsight クラスターで実行され、Azure セキュリティ (azsecd) によって使用され、ウイルス攻撃からクラスターを保護するために使用されるウイルス対策ソフトウェアです。 Microsoft では、ユーザーが既定の Clamscan 構成を変更しないようにすることを強くお勧めします。

このプロセスは、他のプロセスに干渉したり、他のプロセスを中断したりすることはありません。 常に他のプロセスに生成されます。 Clamscan からの CPU のスパイクは、システムがアイドル状態のときにのみ表示されます。  

スケジュールを制御する必要があるシナリオでは、次の手順を使用できます。

1. 次のコマンドを使用して、自動実行を無効にします。

   ``/usr/local/vbin/azsecd config -s clamav -d Disabled``

2. このコマンドを root として実行する Cron ジョブを作成します。

   ``/usr/local/bin/azsecd manual -s clamav``

Cron ジョブを設定して実行する方法の詳細については、「[Cron ジョブを設定する方法](https://askubuntu.com/questions/2368/how-do-i-set-up-a-cron-job)」を参照してください。

## <a name="storage"></a>Storage

### <a name="can-i-add-an-azure-data-lake-storage-gen2-to-an-existing-hdinsight-cluster-as-an-additional-storage-account"></a>既存の HDInsight クラスターに追加のストレージ アカウントとして Azure Data Lake Storage Gen2 を追加することはできますか。

いいえ。 現在のところ、BLOB ストレージをプライマリ ストレージとして使用する既存の HDInsight クラスターに Azure Data Lake Storage Gen2 を追加することはできません。 詳細については、「[Azure HDInsight クラスターで使用するストレージ オプションを比較する](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-compare-storage-options)」を参照してください。 

### <a name="how-can-i-find-the-currently-linked-service-principal-for-a-data-lake-storage-account"></a>Data Lake ストレージ アカウントの現在リンクされているサービス プリンシパルを見つける方法を教えてください。

これらの設定を確認するには、ポータルでクラスターのプロパティにある [Data Lake Storage Gen1 アクセス] を確認します。 詳細については、「[クラスターのセットアップを確認する](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-hdinsight-hadoop-use-portal#verify-cluster-set-up)」を参照してください。
 
HDInsight クラスターのストレージ アカウントと BLOB コンテナーの使用量を計算するにはどうすればよいですか。
次のいずれかの手順を使用できます。

* [PowerShell の使用](https://docs.microsoft.com/azure/storage/scripts/storage-blobs-container-calculate-size-powershell)
* 次のコマンドを使用して、HDI クラスター上の /user/hive/.Trash/ フォルダーのサイズを確認することもできます。

   ``hdfs dfs -du -h /user/hive/.Trash/``

### <a name="how-can-i-set-up-auditing-for-my-blob-storage-account"></a>BLOB ストレージ アカウントの監査を設定するにはどうすればよいですか。

BLOB ストレージ アカウントを監査するには、「[Azure ポータルでのストレージ アカウントの監視](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account)」に記載されている手順に従って BLOB ストレージ アカウントの監視を構成する必要があります。 HDFS 監査ログでは、ローカルの HDFS ファイルシステムのみの監査情報のみが提供されます (hdfs://mycluster)。  リモート ストレージで実行される操作は含まれません。

### <a name="how-can-i-transfer-files-between-a-blob-container-and-an-hdinsight-head-node"></a>BLOB コンテナーと HDInsight ヘッド ノードの間でファイルを転送する方法を教えてください。

次の例のように、ヘッド ノード上でシェル スクリプトを実行することで、BLOB コンテナーと HDInsight ヘッド ノードの間でファイルを転送できます。

```
for i in cat filenames.txt

do
         hadoop fs -get $i <local destination>
done
```
 
> [!NOTE]
> ファイル *filenames.txt* は、BLOB コンテナー内のファイルの絶対パスを持ちます。
 
### <a name="are-there-any-ranger-plugins-for-storage"></a>ストレージ用の Ranger プラグインはありますか。

現在のところ、BLOB ストレージ、Azure Data Lake Storage (ADLS) Gen1、または Azure Data Lake Storage Gen2 用の Ranger プラグインは存在しません。 ESP クラスターの場合、ベスト プラクティスとして ADLS を使用し、HDFS ツールを使用してファイル システム レベルで細かいアクセス許可を手動で設定します。 また、ADLS を使用する場合、ESP クラスターではクラスター レベルで AAD を使用して、ファイル システム アクセス制御の一部が実行されます。 

次の記事に記載されている手順に従って、Azure Storage Explorer を使用し、ユーザーが配置されているセキュリティ グループにデータ アクセス ポリシーを割り当てることができます。

* [Hive またはその他のサービスを使用して Data Lake Storage Gen2 でデータをクエリするためのアクセス許可を Azure AD ユーザーに設定するにはどうすればよいですか。](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2#how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services)
* [Azure Data Lake Storage Gen2 で Azure Storage Explorer を使用してファイルとディレクトリ レベルのアクセス許可を設定する](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-how-to-set-permissions-storage-explorer)

### <a name="can-i-increase-hdfs-storage-on-a-cluster-without-increasing-the-disk-size-of-worker-nodes"></a>ワーカー ノードのディスク サイズを増やすことなく、クラスター上の HDFS ストレージを増やすことはできますか。

いいえ。ワーカー ノードのディスク サイズを増やすことはできません。 ディスク サイズを大きくする唯一の方法は、クラスターを削除し、大きな worker 仮想マシン上で再作成することです。

クラスターを削除するとデータが削除されるため、Microsoft では HDInsight 内のデータの格納に HDFS を使用することはお勧めしません。 代わりに、Azure にデータを格納することをお勧めします。 クラスターをスケールアップして、HDInsight クラスターに容量を追加することもできます。

## <a name="edge-nodes"></a>エッジ ノード

### <a name="can-i-add-an-edge-node-after-the-cluster-has-been-created"></a>クラスターの作成後にエッジ ノードを追加することはできますか。

はい。空のエッジ ノードは、既存の HDInsight クラスターに、またはクラスターの作成時に新しいクラスターに追加できます。

詳細については、「[HDInsight の Apache Hadoop クラスターで空のエッジ ノードを使用する](https://docs.microsoft.com/azure/hdinsight/hdinsight-apps-use-edge-node)」を参照してください。

### <a name="how-can-i-connect-to-an-edge-node"></a>エッジ ノードに接続するにはどうすればよいですか。

エッジ ノードを作成した後、ポート 22 で SSH を使用してエッジ ノードに接続できます。 エッジ ノードの名前はクラスター ポータルから確認できます (名前の末尾は一般に "-ed" です)。

### <a name="why-are-persisted-scripts-not-running-automatically-on-newly-created-edge-nodes"></a>保存されたスクリプトが新しく作成されたエッジ ノード上で自動的に実行されないのはなぜですか。

保存されたスクリプトは、スケール操作によってクラスターに追加される新しいワーカー ノードのカスタマイズに使用されます。エッジ ノードには適用されません。

## <a name="rest-api"></a>REST API

### <a name="what-are-the-rest-api-calls-to-pull-a-tez-query-view-from-the-cluster"></a>クラスターから Tez クエリ ビューをプルする REST API 呼び出しを教えてください。

次の REST エンドポイントを使用して、応答が JSON 形式になる必要な情報を取得できます。 基本認証ヘッダーを使用して、これらの要求を行うことができます。

* "Tez クエリ" ビュー - https://`<cluster name>`.azurehdinsight.net/ws/v1/timeline/HIVE_QUERY_ID/

* "Tez DAG" ビュー - https://`<cluster name>`.azurehdinsight.net/ws/v1/timeline/TEZ_DAG_ID/

### <a name="how-do-i-retrieve-the-configuration-details-from-hdi-cluster-by-using-an-azure-active-directory-user"></a>Azure Active Directory ユーザーを使用して HDI クラスターから構成の詳細を取得するにはどうすればよいですか。

AAD ユーザーと適切な認証トークンをネゴシエートするには、次の形式を使用してゲートウェイを経由します。

* https://`<cluster dnsname>`.azurehdinsight.net/api/v1/clusters/testclusterdem/stack_versions/1/repository_versions/1 

### <a name="how-do-i-use-ambari-restful-api-to-monitor-yarn-performance"></a>Ambari restful API を使用して YARN パフォーマンスを監視するにはどうすればよいですか。

curl コマンドを同じ VNet またはピアリングされた VNet で呼び出す場合は、次のコマンドを使用します。

``curl -u <cluster login username> -sS -G http://headnodehost:8080/api/v1/clusters/anssenllap/services/YARN/components/NODEMANAGER?fields=metrics/cpu``
 
VNet 以外またはピアリングされていない VNet からコマンドを呼び出す場合、コマンドの形式は次のようになります。
 
* 非 ESP クラスターの場合: ``curl -u <cluster login username> -sS -G https://ClusterName.azurehdinsight.net/api/v1/clusters/ClusterName/services/YARN/components/NODEMANAGER?fields=metrics/cpu``
 
* ESP クラスターの場合: ``curl -u <cluster login username> -sS -G https://ClusterName.azurehdinsight.net/api/v1/clusters/ClusterName/services/YARN/components/NODEMANAGER?fields=metrics/cpu``

> [!NOTE]
> Curl でパスワードの入力を求められます。 クラスター ログイン ユーザー名の有効なパスワードを入力する必要があります。

## <a name="billing"></a>課金

### <a name="how-much-does-it-cost-to-deploy-an-hdinsight-cluster"></a>HDInsight クラスターのデプロイにはどのくらいのコストがかかりますか。

価格の詳細と課金に関連する FAQ については、「[Azure HDInsight の料金](https://azure.microsoft.com/pricing/details/hdinsight/)」を参照してください。

### <a name="how-do-i-cancel-my-subscription"></a>サブスクリプションを取り消すにはどうすればよいですか。

サブスクリプションを取り消す方法の詳細については、「[Azure サブスクリプションの取り消し](https://docs.microsoft.com/azure/billing/billing-how-to-cancel-azure-subscription)」を参照してください。

### <a name="for-pay-as-you-go-subscriptions-what-happens-after-i-cancel-my-subscription"></a>従量課金制サブスクリプションの場合、サブスクリプションを取り消した後はどうなりますか。

取り消し後のサブスクリプションの詳細については、「[サブスクリプションの取り消し後の流れ](https://docs.microsoft.com/azure/billing/billing-how-to-cancel-azure-subscription#what-happens-after-i-cancel-my-subscription)」を参照してください。

## <a name="hive"></a>Hive

### <a name="why-does-the-hive-version-appear-as-121000-instead-of-21-in-the-ambari-ui-even-though-i-am-running-an-hdinsight-36-cluster"></a>HDInsight 3.6 クラスターを実行している場合でも、Ambari UI で Hive バージョンが 2.1 ではなく 1.2.1000 と表示されるのはなぜですか。

Ambari UI に表示されるのは 1.2 のみですが、HDInsight 3.6 には Hive 1.2 と Hive 2.1 の両方が含まれています。

## <a name="other-faq"></a>その他の FAQ

### <a name="what-does-hdinsight-offer-in-terms-of-real-time-stream-processing-capabilities"></a>HDInsight はリアルタイムのストリーム処理機能に関してどのような機能を提供していますか。

Azure HDInsight でのストリーム処理の統合機能の詳細については、「[Azure でのストリーム処理テクノロジの選択](https://docs.microsoft.com/azure/architecture/data-guide/technology-choices/stream-processing)」を参照してください。

### <a name="is-there-a-way-to-dynamically-terminate-the-head-node-of-the-cluster-when-the-cluster-is-idle-for-a-specific-period"></a>クラスターが一定期間アイドル状態になったときに、クラスターのヘッド ノードを動的に終了する方法はありますか。

いいえ。クラスターのヘッド ノードを動的に終了することはできません。 このシナリオには Azure Data Factory を使用できます。
