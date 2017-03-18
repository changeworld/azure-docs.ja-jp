---
title: "PowerShell: Data Lake Store を既定のストレージとして使用する Azure HDInsight クラスター | Microsoft Docs"
description: "Azure PowerShell を使用して、Azure Data Lake を使用する HDInsight クラスターを作成します"
services: data-lake-store,hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 8917af15-8e37-46cf-87ad-4e6d5d67ecdb
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/02/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 1e6ae31b3ef2d9baf578b199233e61936aa3528e
ms.openlocfilehash: 01c75f20909c7334981bf407e775656476972276
ms.lasthandoff: 03/03/2017


---
# <a name="use-azure-powershell-to-create-an-hdinsight-cluster-with-data-lake-store-as-default-storage"></a>Azure PowerShell を使用して、Data Lake Store を (既定のストレージとして) 使用する HDInsight クラスターを作成する
> [!div class="op_single_selector"]
> * [ポータルの使用](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [PowerShell の使用 (既定のストレージ)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [PowerShell の使用 (追加のストレージ)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Resource Manager の使用](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Azure PowerShell を使用して、Azure Data Lake Store を**既定のストレージとして**使用する HDInsight クラスターを構成する方法について説明します。 追加のストレージとして Azure Data Lake Store を使用する HDInsight クラスターの作成方法については、「[Data Lake Store を使用する HDInsight クラスターを作成する](data-lake-store-hdinsight-hadoop-use-powershell.md)」をご覧ください。

## <a name="using-data-lake-store-for-hdinsight-cluster-storage"></a>HDInsight クラスター ストレージでの Data Lake Store の使用

HDInsight で Data Lake Store を使用するための重要な考慮事項を次に示します。

* Data Lake Store にアクセスできる HDInsight クラスターを既定のストレージとして作成するオプションは、HDInsight バージョン 3.5 で使用できます。

* Data Lake Store にアクセスできる HDInsight クラスターを既定のストレージとして作成するオプションは、HDInsight Premium クラスターでは使用できません。

* HBase クラスター (Windows および Linux) の場合、Data Lake Store は、既定のストレージとしても追加のストレージとしても**使用できません**。


PowerShell を使用して、Data Lake Store を使用するように HDInsight を構成するには、次の手順が必要です。

* Azure Data Lake Store を作成する
* Data Lake Store へのロールベースのアクセスの認証を設定する
* Data Lake Store への認証を使用して HDInsight クラスターを作成する
* クラスター上でテスト ジョブを実行する

## <a name="prerequisites"></a>前提条件
このチュートリアルを読み始める前に、次の項目を用意する必要があります。

* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。
* **Azure PowerShell 1.0 以上**。 「 [Azure PowerShell のインストールと構成の方法](/powershell/azureps-cmdlets-docs)」を参照してください。
* **Windows SDK**。 [こちら](https://dev.windows.com/en-us/downloads)からインストールできます。 この機能は、セキュリティ証明書の作成に使用します。
* **Azure Active Directory Service のプリンシパル**。 このチュートリアルの手順では、Azure AD でサービス プリンシパルを作成する方法を説明します。 ただし、サービス プリンシパルを作成するには、Azure AD 管理者である必要があります。 Azure AD 管理者である場合は、この前提条件をスキップしてチュートリアルを進めることができます。

    **Azure AD 管理者でない場合**は、サービス プリンシパルの作成に必要な手順を実行することはできません。 その場合は、Data Lake Store で HDInsight クラスターを作成する前に、まず Azure AD 管理者がサービス プリンシパルを作成する必要があります。 また、「[Create a service principal with certificate](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-certificate)」 (証明書でサービス プリンシパルを作成する) で説明しているように、サービス プリンシパルは証明書を使って作成する必要があります。

## <a name="create-an-azure-data-lake-store"></a>Azure Data Lake Store を作成する
Data Lake Store を作成するには、次の手順に従います。

1. デスクトップで、新しい Azure PowerShell ウィンドウを開き、次のスニペットを入力します。 ログインを求められたら、必ず、サブスクリプションの管理者または所有者としてログインしてください。

        # Log in to your Azure account
        Login-AzureRmAccount

        # List all the subscriptions associated to your account
        Get-AzureRmSubscription

        # Select a subscription
        Set-AzureRmContext -SubscriptionId <subscription ID>

        # Register for Data Lake Store
        Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

   > [!NOTE]
   > Data Lake Store リソース プロバイダーの登録時に `Register-AzureRmResourceProvider : InvalidResourceNamespace: The resource namespace 'Microsoft.DataLakeStore' is invalid` のようなエラーが発生した場合は、サブスクリプションが Azure Data Lake Store のホワイトリストに登録されていない可能性があります。 こちらの [指示](data-lake-store-get-started-portal.md)に従って Data Lake Store パブリック プレビューで Azure サブスクリプションを有効にしていることを確認してください。
   >
   >
2. Azure Data Lake Store アカウントは、Azure リソース グループに関連付けられます。 まず、Azure リソース グループを作成します。

        $resourceGroupName = "<your new resource group name>"
        New-AzureRmResourceGroup -Name $resourceGroupName -Location "East US 2"

    ![Azure リソース グループの作成](./media/data-lake-store-hdinsight-hadoop-use-powershell/ADL.PS.CreateResourceGroup.png "Azure リソース グループの作成")
3. Azure Data Lake Store アカウントを作成します。 指定するアカウント名には、小文字と数字のみを含める必要があります。

        $dataLakeStoreName = "<your new Data Lake Store name>"
        New-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName -Location "East US 2"

    ![Azure Data Lake アカウントの作成](./media/data-lake-store-hdinsight-hadoop-use-powershell/ADL.PS.CreateADLAcc.png "Azure Data Lake アカウントの作成")
4. アカウントが正常に作成されたことを確認します。

        Test-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

    この出力は **True**になります。

5. Data Lake Store を既定のストレージとして使用するには、クラスターの作成時にクラスター固有のファイルのコピー先となるルート パスを指定する必要があります。 次のコマンドレットを使用してルート パスを作成します。このスニペットでは**/clusters/hdiadlcluster** です。

        $myrootdir = "/"
        New-AzureRmDataLakeStoreItem -Folder -AccountName $dataLakeStoreName -Path $myrootdir/clusters/hdiadlcluster


## <a name="set-up-authentication-for-role-based-access-to-data-lake-store"></a>Data Lake Store へのロールベースのアクセスの認証を設定する
すべての Azure サブスクリプションは Azure Active Directory と関連付けられます。 Azure クラシック ポータルか Azure Resource Manager API を使ってサブスクリプションのリソースにアクセスするユーザーやサービスは、最初にその Azure Active Directory での認証を実行する必要があります。 Azure のサブスクリプションやサービスにアクセス権を付与するには、Azure リソースに対する該当するロールを割り当てます。  サービスの場合は、サービス プリンシパルにより、Azure Active Directory (AAD) 内のサービスが識別されます。 このセクションでは、Azure PowerShell を使用してアプリケーションのサービス プリンシパルを作成し、作成したサービス プリンシパルにロールを割り当てることで、HDInsight のようなアプリケーション サービスに Azure のリソース (先ほど作成した Azure Data Lake Store アカウント) へのアクセス権を付与する方法を説明します。

Azure Data Lake の Active Directory 認証を設定するには、次のタスクを行う必要があります。

* 自己署名証明書の作成
* Azure Active Directory でのアプリケーションの作成、およびサービス プリンシパルの作成

### <a name="create-a-self-signed-certificate"></a>自己署名証明書の作成
このセクションの手順を進める前に、[Windows SDK](https://dev.windows.com/en-us/downloads) がインストールされていることを確認してください。 証明書の作成先となるディレクトリ (**C:\mycertdir** など) も作成しておく必要があります。

1. PowerShell ウィンドウで、Windows SDK をインストールした場所 (通常は `C:\Program Files (x86)\Windows Kits\10\bin\x86`) に移動し、[MakeCert][makecert] ユーティリティを使用して、自己署名証明書と秘密キーを作成します。 次のコマンドを使用します。

        $certificateFileDir = "<my certificate directory>"
        cd $certificateFileDir
        
        makecert -sv mykey.pvk -n "cn=HDI-ADL-SP" CertFile.cer -r -len 2048

    秘密キーのパスワードを入力するよう求められます。 コマンドが正常に実行されると、指定した証明書ディレクトリに **CertFile.cer** と **mykey.pvk** が表示されます。
2. [Pvk2Pfx][pvk2pfx] ユーティリティを使用して、MakeCert によって作成された .pvk ファイルと .cer ファイルを .pfx ファイルに変換します。 次のコマンドを実行します。

        pvk2pfx -pvk mykey.pvk -spc CertFile.cer -pfx CertFile.pfx -po <password>

    メッセージが表示されたら、先ほど指定した秘密キーのパスワードを入力します。 **-po** パラメーターに指定する値は、.pfx ファイルに関連付けられているパスワードです。 コマンドが正常に完了すると、指定した証明書ディレクトリに CertFile.pfx も表示されます。

### <a name="create-an-azure-active-directory-and-a-service-principal"></a>Azure Active Directory とサービス プリンシパルの作成
このセクションでは、Azure Active Directory アプリケーションのサービス プリンシパルを作成し、そのサービス プリンシパルにロールを割り当てて、証明書を指定することでサービス プリンシパルとして認証する手順を実行します。 Azure Active Directory でアプリケーションを作成するには、次のコマンドを実行します。

1. PowerShell コンソール ウィンドウで、次のコマンドレットを貼り付けます。 **-DisplayName** プロパティに指定する値は一意になるようにしてください。 また、**-HomePage** と **-IdentiferUris** の値はプレースホルダー値であるため、確認されません。

        $certificateFilePath = "$certificateFileDir\CertFile.pfx"

        $password = Read-Host –Prompt "Enter the password" # This is the password you specified for the .pfx file

        $certificatePFX = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($certificateFilePath, $password)

        $rawCertificateData = $certificatePFX.GetRawCertData()

        $credential = [System.Convert]::ToBase64String($rawCertificateData)

        $application = New-AzureRmADApplication `
            -DisplayName "HDIADL" `
            -HomePage "https://contoso.com" `
            -IdentifierUris "https://mycontoso.com" `
            -CertValue $credential  `
            -StartDate $certificatePFX.NotBefore  `
            -EndDate $certificatePFX.NotAfter

        $applicationId = $application.ApplicationId
2. アプリケーション ID を使用してサービス プリンシパルを作成します。

        $servicePrincipal = New-AzureRmADServicePrincipal -ApplicationId $applicationId

        $objectId = $servicePrincipal.Id
3. Data Lake Store のルートと、先ほど指定したルート パス内のすべてのフォルダーに、サービス プリンシパルのアクセス権を付与します。 次のコマンドレットを使用します。

        Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStoreName -Path / -AceType User -Id $objectId -Permissions All
        Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStoreName -Path /clusters -AceType User -Id $objectId -Permissions All
        Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStoreName -Path /clusters/hdiadlcluster -AceType User -Id $objectId -Permissions All

## <a name="create-an-hdinsight-linux-cluster-with-data-lake-store-as-default-storage"></a>Data Lake Store を既定のストレージとして使用する HDInsight Linux クラスターを作成する

ここでは、Data Lake Store を既定のストレージとして使用する HDInsight Hadoop Linux クラスターを作成します。 このリリースでは、HDInsight クラスターと Data Lake Store は同じ場所にある必要があります。

1. 最初に、サブスクリプションのテナント ID を取得します。 この情報は後で必要になります。

        $tenantID = (Get-AzureRmContext).Tenant.TenantId
        
2. HDInsight クラスターを作成します。 次のコマンドレットを使用します。

        # Set these variables
        
        $location = "East US 2"
        $storageAccountName = $dataLakeStoreName                          # Data Lake Store account name
        $storageRootPath = "<Storage root path you specified earlier>" # E.g. /clusters/hdiadlcluster
        $clusterName = $containerName                   # As a best practice, have the same name for the cluster and container
        $clusterNodes = <ClusterSizeInNodes>            # The number of nodes in the HDInsight cluster
        $httpCredentials = Get-Credential
        $sshCredentials = Get-Credential

        New-AzureRmHDInsightCluster `
               -ClusterType Hadoop `
               -OSType Linux `
               -ClusterSizeInNodes $clusterNodes `
               -ResourceGroupName $resourceGroupName `
               -ClusterName $clusterName `
               -HttpCredential $httpCredentials `
               -Location $location `
               -DefaultStorageAccountType AzureDataLakeStore `
               -DefaultStorageAccountName "$storageAccountName.azuredatalakestore.net" `
               -DefaultStorageRootPath $storageRootPath `
               -Version "3.5" `
               -SshCredential $sshCredentials `
               -AadTenantId $tenantId `
               -ObjectId $objectId `
               -CertificateFilePath $certificateFilePath `
               -CertificatePassword $password

    コマンドレットが正常に完了すると、クラスターの詳細を一覧表示する出力が表示されます。

        
## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-the-data-lake-store"></a>Data Lake Store を使用する HDInsight クラスターでテスト ジョブを実行する
HDInsight クラスターを構成した後は、クラスターでテスト ジョブを実行し、HDInsight クラスターが Data Lake Store にアクセス可能であるかどうかをテストできます。 これを行うには、Data Lake Store (**<cluster root>/example/data/sample.log**) に既にあるサンプル データを使用してテーブルを作成するサンプル Hive ジョブを実行します。

このセクションでは、作成した HDInsight Linux クラスターに SSH でアクセスし、サンプルの Hive クエリを実行します。

* クラスターへの SSH アクセスに Windows クライアントを使用する場合は、「[Windows での PuTTY から HDInsight (Hadoop) で SSH を使用します](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md)」をご覧ください。
* クラスターへの SSH アクセスに Linux クライアントを使用する場合は、「[Windows、Linux、Unix、または OS X から HDInsight (Hadoop) で SSH を使用する](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)」をご覧ください。

1. 接続したら、次のコマンドを使用して Hive CLI を起動します。

        hive
2. CLI を使用して次のステートメントを入力し、サンプル データを使用して Data Lake Store 内に **vehicles** という名前の新しいテーブルを作成します。

        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'adl:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    クエリの結果は SSH コンソールに出力されます。 

    > [!NOTE]
       > 上の CREATE TABLE コマンドのサンプル データへのパスは `adl:///example/data/` です。`adl:///` にはクラスターのルートを指定します。 このチュートリアルで指定するクラスターのルートは `adl://hdiadlstore.azuredatalakestore.net/clusters/hdiadlcluster` です。 そのため、短縮バージョンを使用するか、またはクラスター ルートへの完全なパスを指定することができます。 
       >
       >



## <a name="access-data-lake-store-using-hdfs-commands"></a>HDFS コマンドを使用して Data Lake Store にアクセスする
Data Lake Store を使用するように HDInsight クラスターを構成したら、HDFS シェル コマンドを使用してストアにアクセスできます。

このセクションでは、作成した HDInsight Linux クラスターに SSH でアクセスし、HDFS コマンドを実行します。 

* クラスターへの SSH アクセスに Windows クライアントを使用する場合は、「[Windows での PuTTY から HDInsight (Hadoop) で SSH を使用します](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md)」をご覧ください。
* クラスターへの SSH アクセスに Linux クライアントを使用する場合は、「[Windows、Linux、Unix、または OS X から HDInsight (Hadoop) で SSH を使用する](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)」をご覧ください。

接続されたら、次の HDFS ファイル システム コマンドを使用して、Data Lake Store 内のファイルを一覧表示します。

    hdfs dfs -ls adl:///

`hdfs dfs -put` コマンドを使用して Data Lake Store にいくつかのファイルをアップロードし、`hdfs dfs -ls` を使用してファイルが正常にアップロードされたかどうかを確認することもできます。

## <a name="see-also"></a>関連項目
* [ポータル: Data Lake Store を使用する HDInsight クラスターを作成する](data-lake-store-hdinsight-hadoop-use-portal.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx

