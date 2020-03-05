---
title: HDInsight に Azure Storage アカウントを追加する
description: 既存の HDInsight クラスターに Azure Storage アカウントを追加する方法について説明します。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: 87eb04b7323186175195babf6a602fa12d25176f
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2020
ms.locfileid: "78206709"
---
# <a name="add-additional-storage-accounts-to-hdinsight"></a>HDInsight にストレージ アカウントを追加する

HDInsight に Azure Storage "*アカウント*" を追加するためにスクリプト アクションを使用する方法について説明します。 このドキュメントの手順では、既存の HDInsight クラスターにストレージ "*アカウント*" を追加します。 この記事は、ストレージ *アカウント* (既定のクラスター ストレージ アカウントではなく) に適用されます。[Azure Data Lake Storage Gen1](hdinsight-hadoop-use-data-lake-store.md) および [Azure Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md) などの追加ストレージには適用されません。

> [!IMPORTANT]  
> このドキュメントでは、クラスターの作成後に、そのクラスターにストレージ アカウントを追加する方法を取り上げています。 クラスター作成時にストレージ アカウントを追加する方法については、「[Apache Hadoop、Apache Spark、Apache Kafka などの HDInsight クラスターをセットアップする](hdinsight-hadoop-provision-linux-clusters.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

* HDInsight 上の Hadoop クラスター。 [Linux での HDInsight の概要](./hadoop/apache-hadoop-linux-tutorial-get-started.md)に関するページを参照してください。
* ストレージ アカウントの名前とキー。 「[ストレージ アカウント アクセス キーを管理する](../storage/common/storage-account-keys-manage.md)」をご覧ください。
* PowerShell を使用する場合は、AZ モジュールが必要です。  「[Azure PowerShell の概要](https://docs.microsoft.com/powershell/azure/overview)」を参照してください。

## <a name="how-it-works"></a>しくみ

このスクリプトは、処理中に次のアクションを実行します。

* クラスターの core-site.xml 構成にストレージ アカウントが既に存在する場合は、スクリプトが終了し、それ以降の操作は実行されません。

* ストレージ アカウントが存在し、キーを使用してアクセスできることを確認します。

* クラスターの資格情報を使用して、キーを暗号化します。

* core-site.xml ファイルにストレージ アカウントを追加します。

* [Apache Oozie](https://oozie.apache.org/)、 [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html)、 [Apache Hadoop MapReduce2](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html)、および[Apache Hadoop HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html)サービスを停止して再起動します。 これらのサービスを停止して再起動することで、新しいストレージ アカウントを使用できるようになります。

> [!WARNING]  
> HDInsight クラスター以外の場所でストレージ アカウントを使用することはできません。

## <a name="add-storage-account"></a>ストレージ アカウントの追加

[スクリプトアクション](hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster) を使用して、次の点を考慮して変更を適用します。

|プロパティ | Value |
|---|---|
|Bash スクリプト URI|`https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh`|
|ノードの種類|Head|
|パラメーター|`ACCOUNTNAME` `ACCOUNTKEY` `-p` (省略可)|

* `ACCOUNTNAME` は HDInsight クラスターに追加するストレージ アカウントの名前です。
* `ACCOUNTKEY` は `ACCOUNTNAME` のアクセス キーです。
* `-p` はオプションです。 指定した場合、キーは暗号化されず、プレーンテキストとして core-site.xml ファイルに格納されます。

## <a name="verification"></a>検証

Azure portal で HDInsight クラスターを表示しているときに、 __[プロパティ]__ の下の __[ストレージ アカウント]__ エントリを選択すると、このスクリプト アクションを通じて追加したストレージ アカウントは表示されません。 Azure PowerShell と Azure CLI でも、追加のストレージ アカウントは表示されません。 ストレージ情報が表示されないのは、このスクリプトがクラスターの `core-site.xml` 構成のみを変更するためです。 この情報は、Azure 管理 API を使用してクラスター情報を取得するときには使用されません。

追加のストレージを確認するには、次に示す方法のいずれかを使用します。

### <a name="powershell"></a>PowerShell

このスクリプトは、指定されたクラスターに関連付けられているストレージ アカウント名を返します。 `CLUSTERNAME` を実際のクラスター名に置き換えてから、このスクリプトを実行してください。

```powershell
# Update values
$clusterName = "CLUSTERNAME"

$creds = Get-Credential -UserName "admin" -Message "Enter the cluster login credentials"

$clusterName = $clusterName.ToLower();

# getting service_config_version
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_service_config_versions/HDFS" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content

$configVersion=$respObj.Clusters.desired_service_config_versions.HDFS.service_config_version

$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=$configVersion" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content

# extract account names
$value = ($respObj.items.configurations | Where type -EQ "core-site").properties | Get-Member -membertype properties | Where Name -Like "fs.azure.account.key.*"
foreach ($name in $value ) { $name.Name.Split(".")[4]}
```

### <a name="apache-ambari"></a>Apache Ambari

1. Web ブラウザーから、`https://CLUSTERNAME.azurehdinsight.net` に移動します。ここで、`CLUSTERNAME` はクラスターの名前です。

1. **[HDFS]** 、 **[構成]** 、 **[詳細]** 、 **[カスタム core-site]** の順に移動します。

1. `fs.azure.account.key` で始まるキーを確認します。 このサンプル イメージに示すように、アカウント名はキーの一部になります。

   ![Apache Ambari による検証](./media/hdinsight-hadoop-add-storage/apache-ambari-verification.png)

## <a name="remove-storage-account"></a>ストレージ アカウントの削除

1. Web ブラウザーから、`https://CLUSTERNAME.azurehdinsight.net` に移動します。ここで、`CLUSTERNAME` はクラスターの名前です。

1. **[HDFS]** 、 **[構成]** 、 **[詳細]** 、 **[カスタム core-site]** の順に移動します。

1. 次のキーを削除します。
    * `fs.azure.account.key.<STORAGE_ACCOUNT_NAME>.blob.core.windows.net`
    * `fs.azure.account.keyprovider.<STORAGE_ACCOUNT_NAME>.blob.core.windows.net`

これらのキーを削除して構成を保存したら、Oozie、Yarn、MapReduce2、HDFS、Hive を 1 つずつ再起動する必要があります。

## <a name="known-issues"></a>既知の問題

### <a name="storage-firewall"></a>ストレージ ファイアウォール

**[選択されたネットワーク]** で **[ファイアウォールと仮想ネットワーク]** に関する制限を使用してストレージ アカウントをセキュリティで保護する場合、 **[Allow trusted Microsoft services]\(信頼された Microsoft サービスを許可)** の例外を有効にして、HDInsight ストレージ アカウントにアクセスできるようにしてください。

### <a name="unable-to-access-storage-after-changing-key"></a>キーの変更後にストレージにアクセスできない

ストレージ アカウントのキーを変更すると、HDInsight はストレージ アカウントにアクセスできなくなります。 HDInsight は、クラスターの core-site.xml 内のキャッシュされたキーのコピーを使用します。 このキャッシュされたコピーは、新しいキーに一致するように更新する必要があります。

スクリプト アクションを再実行しても、キーは __更新されません__。スクリプトはストレージ アカウントのエントリが既に存在するかどうかを確認します。 エントリが既に存在する場合、いかなる変更もしません。

この問題を回避するには、次のようにします。  
1. ストレージ アカウントを削除します。
1. ストレージ アカウントを追加します。

> [!IMPORTANT]  
> クラスターに接続されているプライマリ ストレージ アカウントのストレージ キーのローテーションはサポートされていません。

### <a name="poor-performance"></a>パフォーマンスが低い

ストレージ アカウントが HDInsight クラスターとは異なるリージョンにある場合は、パフォーマンスが低下することがあります。 別のリージョンのデータにアクセスすると、そのリージョンの Azure データ センター外にネットワーク トラフィックが送信され、パブリック インターネットを経由するため、遅延が生じる場合があります。

### <a name="additional-charges"></a>追加料金が発生する

ストレージ アカウントが HDInsight クラスターとは異なるリージョンにある場合は、Azure の課金にエグレス料金が追加されていることがあります。 データが地域データ センターを離れると、エグレス料金が適用されます。 この料金は、トラフィックが他のリージョンの別の Azure データ センター宛てであっても適用されます。

## <a name="next-steps"></a>次のステップ

既存の HDInsight クラスターにストレージ アカウントを追加する方法について説明しました。 スクリプト アクションの詳細については、「[スクリプト アクションを使用して Linux ベースの HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster-linux.md)」を参照してください。
