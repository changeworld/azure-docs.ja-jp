---
title: Azure HDInsight の Hadoop で Data Lake Store を使用する
description: Azure Data Lake Store のデータに対してクエリを実行し、分析結果を格納する方法について説明します。
services: hdinsight,storage
author: jasonwhowell
ms.author: jasonh
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 07/23/2018
ms.openlocfilehash: 3f72e18fbf0f3796d85b4acfb74223b6bea24c6e
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2018
ms.locfileid: "39591013"
---
# <a name="use-data-lake-store-with-azure-hdinsight-clusters"></a>Azure HDInsight クラスターで Data Lake Store を使用する

HDInsight クラスターでデータを分析するために、[Azure Storage](../storage/common/storage-introduction.md) と [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md) のいずれか、または両方にデータを格納できます。 両方のストレージ オプションにより、計算で使用される HDInsight クラスターを安全に削除できます。このとき、ユーザー データは失われません。

この記事では、HDInsight クラスターでの Data Lake Store の動作について説明します。 THDInsight クラスターでの Data Lake Store の動作については、「[Azure HDInsight クラスターで Azure Storage を使用する](hdinsight-hadoop-use-blob-storage.md)」をご覧ください。 HDInsight クラスターの作成について詳しくは、「[HDInsight で Hadoop クラスターを作成する](hdinsight-hadoop-provision-linux-clusters.md)」をご覧ください。

> [!NOTE]
> Data Lake Store へのアクセスには必ずセキュリティで保護されたチャネルが使われるため、`adls` ファイルシステム スキーム名はありません。 常に `adl` を使用します。
> 


## <a name="availability-for-hdinsight-clusters"></a>HDInsight クラスターの可用性

Hadoop は、既定のファイル システムの概念をサポートしています。 既定のファイル システムは、既定のスキームとオーソリティを意味します。 これは相対パスの解決に使用することもできます。 HDInsight クラスターの作成プロセス時に、Azure Storage 内の BLOB コンテナーを既定のファイル システムとして指定できます。また、HDInsight 3.5 以降のバージョンでは、Azure Storage と Azure Data Lake Store のいずれかを既定のファイル システムとして選択できます (いくつか例外があります)。 

HDInsight クラスターでは、2 つの方法で Data Lake Store を使用できます。

* 既定のストレージとして
* 既定のストレージが Azure Storage Blob のときの追加ストレージとして

現時点では、Data Lake Store を既定ストレージおよび追加ストレージ アカウントとして使うことができるのは、HDInsight クラスターの一部の種類/バージョンのみです。

| HDInsight クラスターの種類 | 既定ストレージとしての Data Lake Store | 追加ストレージとしての Data Lake Store| メモ |
|------------------------|------------------------------------|---------------------------------------|------|
| HDInsight Version 3.6 | [はい] | [はい] | |
| HDInsight Version 3.5 | [はい] | [はい] | HBase は例外|
| HDInsight Version 3.4 | いいえ  | [はい] | |
| HDInsight Version 3.3 | いいえ  | いいえ  | |
| HDInsight Version 3.2 | いいえ  | [はい] | |
| Storm | | |Data Lake Store を使って、Storm トポロジからデータを書き込むことができます。 また、Data Lake Store を、Storm トポロジから読み取ることができる参照データとして使用することもできます。|

Data Lake Store を追加ストレージ アカウントとして使っても、クラスターから Azure ストレージに対する読み取り/書き込みのパフォーマンスや機能には影響しません。


## <a name="use-data-lake-store-as-default-storage"></a>Data Lake Store を既定のストレージとして使用する

Data Lake Store を既定のストレージとして HDInsight がデプロイされている場合、クラスター関連のファイルは次の場所の Data Lake Store に格納されます。

    adl://mydatalakestore/<cluster_root_path>/

`<cluster_root_path>` は、Data Lake Store に作成するフォルダーの名前です。 クラスターごとにルート パスを指定することで、複数のクラスターに対して同じ Data Lake Store アカウントを使うことができます。 このため、次の場所にセットアップを設定できます。

* Cluster1 は、パス `adl://mydatalakestore/cluster1storage` を使用できます
* Cluster2 は、パス `adl://mydatalakestore/cluster2storage` を使用できます

両方のクラスターが同じ Data Lake Store アカウント **mydatalakestore** を使用していることに注意してください。 クラスターそれぞれが、Data Lake Store で独自のルート ファイルシステムにアクセスします。 特に Azure Portal をデプロイすると、ルート パスの **/clusters/\<clustername >** などのフォルダー名を使用するよう求められます。

既定のストレージとして Data Lake Store を使うには、サービス プリンシパルに次のパスへのアクセスを許可する必要があります。

- Data Lake Store アカウントのルート。  例: adl://mydatalakestore/。
- すべてのクラスター フォルダー用のフォルダー。  例: adl://mydatalakestore/clusters。
- クラスター用のフォルダー。  例: adl://mydatalakestore/clusters/cluster1storage。

サービス プリンシパルの作成とアクセスの許可に関して詳しくは、「[Data Lake Store へのアクセスを構成する](#configure-data-lake-store-access)」をご覧ください。


## <a name="use-data-lake-store-as-additional-storage"></a>Data Lake Store を追加のストレージとして使用する

Data Lake Store を、クラスターの追加のストレージとして使用することもできます。 この場合、クラスターの既定のストレージとしては、Azure Storage Blob アカウントまたは Data Lake Store アカウントを使うことができます。 追加のストレージとしての Data Lake Store に格納されているデータに対して HDInsight ジョブを実行する場合は、ファイルへの完全修飾パスを使う必要があります。 例: 

    adl://mydatalakestore.azuredatalakestore.net/<file_path>

URL に **cluster_root_path** がないことに注意してください。 これは、Data Lake Store が既定のストレージでないためです。必要な操作は、ファイルへのパスを指定することだけです。

追加のストレージとして Data Lake Store を使うには、ファイルが保存されているパスへのアクセスをサービス プリンシパルに許可することだけが必要です。  例: 

    adl://mydatalakestore.azuredatalakestore.net/<file_path>

サービス プリンシパルの作成とアクセスの許可に関して詳しくは、「[Data Lake Store へのアクセスを構成する](#configure-data-lake-store-access)」をご覧ください。


## <a name="use-more-than-one-data-lake-store-accounts"></a>複数の Data Lake Store アカウントを使用する

Data Lake Store アカウントを追加ストレージとして使い、複数の Data Lake Store アカウントを追加するには、1 つ以上の Data Lake Store アカウント内のデータに HDInsight クラスターのアクセス許可を付与します。 「[Data Lake Store へのアクセスを構成する](#configure-data-lake-store-access)」をご覧ください。

## <a name="configure-data-lake-store-access"></a>Data Lake Store へのアクセスを構成する

HDInsight クラスターから Data Lake Store へのアクセスを構成するには、Azure Active Directory (Azure AD) のサービス プリンシパルが必要です。 サービス プリンシパルを作成できるのは、Azure AD 管理者だけです。 サービス プリンシパルは証明書で作成する必要があります。 詳しくは、「[クイック スタート: HDInsight のクラスターを設定する](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)」および「[自己署名証明書を使用したサービス プリンシパルの作成](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-self-signed-certificate)」をご覧ください。

> [!NOTE]
> Azure Data Lake Store を HDInsight クラスターの追加ストレージとして使用する場合は、この記事で説明されているように、クラスターを作成するときにそうすることを強くお勧めします。 Azure Data Lake Store を既存の HDInsight クラスターに追加のストレージとして追加することは、サポートされていないシナリオです。
>

## <a name="access-files-from-the-cluster"></a>クラスターからファイルにアクセスする

複数の方法で、HDInsight クラスターから Data Lake Store のファイルにアクセスできます。

* **完全修飾名の使用**。 この方法により、アクセスするファイルへの完全パスを指定します。

        adl://mydatalakestore.azuredatalakestore.net/<cluster_root_path>/<file_path>

* **短縮されたパスの使用**。 この方法により、クラスター ルートへのパスを adl:/// に置き換えます。 上記の例では `adl://mydatalakestore.azuredatalakestore.net/<cluster_root_path>/` を `adl:///` に置き換えることができます。

        adl:///<file path>

* **相対パスの使用**。 この方法により、アクセスするファイルへの相対パスのみを指定します。 たとえば、そのファイルへの完全パスが次のとおりだとします。

        adl://mydatalakestore.azuredatalakestore.net/<cluster_root_path>/example/data/sample.log

    この sample.log ファイルには、次の相対パスを使用してアクセスできます。

        /example/data/sample.log

## <a name="create-hdinsight-clusters-with-access-to-data-lake-store"></a>Data Lake Store にアクセスできる HDInsight クラスターを作成する

Data Lake Store にアクセスできる HDInsight クラスターを作成する方法の詳しい手順については、以下のリンクをご覧ください。

* [ポータルの使用](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)
* [PowerShell の使用 (Data Lake Store を既定のストレージとして使用)](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
* [PowerShell の使用 (Data Lake Store を追加のストレージとして使用)](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)
* [Azure テンプレートの使用](../data-lake-store/data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)

## <a name="refresh-the-hdinsight-certificate-for-data-lake-store-access"></a>HDInsight 証明書を更新し、Data Lake Store にアクセスする

次の PowerShell コード サンプルはローカルの証明書ファイルを読み取り、Azure Data Lake Store にアクセスできるように新しい証明書で HDInsight クラスターを更新します。 独自の HDInsight クラスター名、リソース グループ名、サブスクリプション ID、アプリ ID、証明書のローカル パスを指定します。 入力を求められたら、パスワードを入力します。

```powershell-interactive
$clusterName = '<clustername>'
$resourceGroupName = '<resourcegroupname>'
$subscriptionId = '01234567-8a6c-43bc-83d3-6b318c6c7305'
$appId = '01234567-e100-4118-8ba6-c25834f4e938'
$generateSelfSignedCert = $false
$addNewCertKeyCredential = $true
$certFilePath = 'C:\localfolder\adls.pfx'
$certPassword = Read-Host "Enter Certificate Password"

if($generateSelfSignedCert)
{
    Write-Host "Generating new SelfSigned certificate"
    
    $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=hdinsightAdlsCert" -KeySpec KeyExchange
    $certBytes = $cert.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $certPassword);
    $certString = [System.Convert]::ToBase64String($certBytes)
}
else
{

    Write-Host "Reading the cert file from path $certFilePath"

    $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($certFilePath, $certPassword)
    $certString = [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes($certFilePath))
}

Login-AzureRmAccount

if($addNewCertKeyCredential)
{
    Write-Host "Creating new KeyCredential for the app"
    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    New-AzureRmADAppCredential -ApplicationId $appId -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore
    Write-Host "Waiting for 30 seconds for the permissions to get propagated"
    Start-Sleep -s 30
}

Select-AzureRmSubscription -SubscriptionId $subscriptionId
Write-Host "Updating the certificate on HDInsight cluster..."

Invoke-AzureRmResourceAction `
    -ResourceGroupName $resourceGroupName `
    -ResourceType 'Microsoft.HDInsight/clusters' `
    -ResourceName $clusterName `
    -ApiVersion '2015-03-01-preview' `
    -Action 'updateclusteridentitycertificate' `
    -Parameters @{ ApplicationId = $appId; Certificate = $certString; CertificatePassword = $certPassword.ToString() } `
    -Force
```

## <a name="next-steps"></a>次の手順
この記事では、HDInsight で HDFS と互換性のある Azure Data Lake Store を使う方法について説明しました。 これにより、収集したデータを長期にわたって格納できるスケーラブルなソリューションを構築できます。さらに HDInsight を使用すると、格納されている構造化データと非構造化データから有益な情報を得ることができます。

詳細については、次を参照してください。

* [Azure HDInsight の概要][hdinsight-get-started]
* [クイック スタート: HDInsight のクラスターを設定する](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)
* [Azure PowerShell を使用して、Data Lake Store を使用する HDInsight クラスターを作成する](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)
* [HDInsight へのデータのアップロード][hdinsight-upload-data]
* [HDInsight での Hive の使用][hdinsight-use-hive]
* [HDInsight での Pig の使用][hdinsight-use-pig]
* [Azure Storage の Shared Access Signature を使用した HDInsight でのデータへのアクセスの制限][hdinsight-use-sas]

[hdinsight-use-sas]: hdinsight-storage-sharedaccesssignature-permissions.md
[powershell-install]: /powershell/azureps-cmdlets-docs
[hdinsight-creation]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md

[blob-storage-restAPI]: http://msdn.microsoft.com/library/windowsazure/dd135733.aspx
[azure-storage-create]:../storage/common/storage-create-storage-account.md

[img-hdi-powershell-blobcommands]: ./media/hdinsight-hadoop-use-blob-storage/HDI.PowerShell.BlobCommands.png
[img-hdi-quick-create]: ./media/hdinsight-hadoop-use-blob-storage/HDI.QuickCreateCluster.png
[img-hdi-custom-create-storage-account]: ./media/hdinsight-hadoop-use-blob-storage/HDI.CustomCreateStorageAccount.png  
