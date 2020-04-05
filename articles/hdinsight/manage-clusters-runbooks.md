---
title: チュートリアル:Azure Automation Runbook を使用してクラスターを作成する - Azure HDInsight
description: Azure Automation Runbook を使用してクラウドでスクリプトを実行し、Azure HDInsight クラスターを作成および削除する方法について説明します。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 12/27/2019
ms.openlocfilehash: 05c0aaf6cc33442fa4f36eb38eb0d6d593fc6c1f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2020
ms.locfileid: "75553122"
---
# <a name="tutorial-create-azure-hdinsight-clusters-with-azure-automation"></a>チュートリアル:Azure Automation で Azure HDInsight クラスターを作成する

Azure Automation を使うと、クラウドで実行されるスクリプトを作成し、オンデマンドで、またはスケジュールに基づいて、Azure リソースを管理することができます。 この記事では、Azure HDInsight クラスターを作成および削除するための PowerShell Runbook を作成する方法について説明します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * HDInsight との対話に必要なモジュールをインストールする。
> * クラスターの作成時に必要な資格情報を作成して保存する。
> * HDInsight クラスターを作成するための新しい Azure Automation Runbook を作成する。

Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="prerequisites"></a>前提条件

* 既存の [Azure Automation アカウント](../automation/automation-quickstart-create-account.md)。
* 既存の [Azure ストレージ アカウント](../storage/common/storage-account-create.md)。クラスター記憶域として使用されます。

## <a name="install-hdinsight-modules"></a>HDInsight モジュールをインストールする

1. [Azure portal](https://portal.azure.com) にサインインします。
1. Azure Automation アカウントを選択します。
1. **[共有リソース]** で **[モジュール ギャラリー]** を選択します。
1. ボックスに「**AzureRM.Profile**」と入力し、Enter キーを押して検索します。 使用可能な検索結果を選択します。
1. **AzureRM.profile** の画面で、 **[インポート]** を選択します。 Azure モジュールを更新するボックスをオンにして、 **[OK]** を選択します。

    ![AzureRM.profile モジュールをインポートする](./media/manage-clusters-runbooks/import-azurermprofile-module.png)

1. **[共有リソース]** の **[モジュール ギャラリー]** を選択して、モジュール ギャラリーに戻ります。
1. 「**HDInsight**」と入力します。 **AzureRM.HDInsight** を選択します。

    ![HDInsight モジュールを参照する](./media/manage-clusters-runbooks/browse-modules-hdinsight.png)

1. **AzureRM.HDInsight** のパネルで、 **[インポート]** を選択して **[OK]** を選択します。

    ![AzureRM.HDInsight モジュールをインポートする](./media/manage-clusters-runbooks/import-azurermhdinsight-module.png)

## <a name="create-credentials"></a>資格情報を作成する

1. **[共有リソース]** で、 **[資格情報]** を選択します。
1. **[資格情報の追加]** を選択します。
1. **[新しい資格情報]** パネルで必要な情報を入力します。 この資格情報は、クラスター パスワードを格納するためのものです。これにより、Ambari にログインできるようになります。

    | プロパティ | Value |
    | --- | --- |
    | 名前 | `cluster-password` |
    | ユーザー名 | `admin` |
    | Password | `SECURE_PASSWORD` |
    | [パスワードの確認入力] | `SECURE_PASSWORD` |

1. **［作成］** を選択します
1. 同じ手順を繰り返し、ユーザー名 `sshuser` と適当なパスワードで新しい資格情報 `ssh-password` を作成します。 **［作成］** を選択します この資格情報は、クラスター用の SSH パスワードを格納するためのものです。

    ![資格情報を作成する](./media/manage-clusters-runbooks/create-credentials.png)

## <a name="create-a-runbook-to-create-a-cluster"></a>クラスターを作成するための Runbook を作成する

1. **[プロセス オートメーション]** の **[Runbook]** を選択します。
1. **[Runbook の作成]** を選択します。
1. **[Runbook の作成]** パネルで、Runbook の名前を入力します (例: `hdinsight-cluster-create`)。 **[Runbook の種類]** ドロップダウンで **[PowerShell]** を選択します。
1. **［作成］** を選択します

    ![Runbook を作成する](./media/manage-clusters-runbooks/create-runbook.png)

1. **[PowerShell Runbook の編集]** 画面で次のコードを入力し、 **[発行]** を選択します。

    ![Runbook を発行する](./media/manage-clusters-runbooks/publish-runbook.png)

    ```powershell
    Param
    (
      [Parameter (Mandatory= $true)]
      [String] $subscriptionID,
    
      [Parameter (Mandatory= $true)]
      [String] $resourceGroup,
    
      [Parameter (Mandatory= $true)]
      [String] $storageAccount,
    
      [Parameter (Mandatory= $true)]
      [String] $containerName,
    
      [Parameter (Mandatory= $true)]
      [String] $clusterName
    )
    ### Authenticate to Azure 
    $Conn = Get-AutomationConnection -Name 'AzureRunAsConnection'
    Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
    
    # Set cluster variables
    $storageAccountKey = (Get-AzureRmStorageAccountKey –Name $storageAccount –ResourceGroupName $resourceGroup)[0].value 
    
    # Setting cluster credentials
    
    #Automation credential for Cluster Admin
    $clusterCreds = Get-AutomationPSCredential –Name 'cluster-password'
    
    #Automation credential for user to SSH into cluster
    $sshCreds = Get-AutomationPSCredential –Name 'ssh-password' 
    
    $clusterType = "Hadoop" #Use any supported cluster type (Hadoop, HBase, Storm, etc.)
    $clusterOS = "Linux"
    $clusterWorkerNodes = 3
    $clusterNodeSize = "Standard_D3_v2"
    $location = Get-AzureRmStorageAccount –StorageAccountName $storageAccount –ResourceGroupName $resourceGroup | %{$_.Location}
    
    ### Provision HDInsight cluster
    New-AzureRmHDInsightCluster –ClusterName $clusterName –ResourceGroupName $resourceGroup –Location $location –DefaultStorageAccountName "$storageAccount.blob.core.windows.net" –DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainer $containerName –ClusterType $clusterType –OSType $clusterOS –Version “3.6” –HttpCredential $clusterCreds –SshCredential $sshCreds –ClusterSizeInNodes $clusterWorkerNodes –HeadNodeSize $clusterNodeSize –WorkerNodeSize $clusterNodeSize
    ```

## <a name="create-a-runbook-to-delete-a-cluster"></a>クラスターを削除するための Runbook を作成する

1. **[プロセス オートメーション]** の **[Runbook]** を選択します。
1. **[Runbook の作成]** を選択します。
1. **[Runbook の作成]** パネルで、Runbook の名前を入力します (例: `hdinsight-cluster-delete`)。 **[Runbook の種類]** ドロップダウンで **[PowerShell]** を選択します。
1. **［作成］** を選択します
1. **[PowerShell Runbook の編集]** 画面で次のコードを入力し、 **[発行]** を選択します。

    ```powershell
    Param
    (
      [Parameter (Mandatory= $true)]
      [String] $clusterName
    )
    
    ### Authenticate to Azure 
    $Conn = Get-AutomationConnection -Name 'AzureRunAsConnection'
    Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
    
    Remove-AzureRmHDInsightCluster -ClusterName $clusterName
    ```

## <a name="execute-runbooks"></a>Runbook を実行する

### <a name="create-a-cluster"></a>クラスターの作成

1. **[プロセス オートメーション]** で **[Runbook]** を選択して、Automation アカウントに含まれる Runbook の一覧を表示します。
1. `hdinsight-cluster-create` またはクラスター作成 Runbook の作成時に使用した名前を選択します。
1. **[開始]** を選択すると、Runbook がすぐに実行されます。 Runbook が定期的に実行されるようにスケジュールすることもできます。 「[Azure Automation の Runbook をスケジュール設定する](../automation/shared-resources/schedules.md)」を参照してください
1. スクリプトに必要なパラメーターを入力して、 **[OK]** を選択します。 これにより、**CLUSTERNAME** パラメーターで指定した名前で新しい HDInsight クラスターが作成されます。

    ![クラスター作成 Runbook を実行する](./media/manage-clusters-runbooks/execute-create-runbook.png)

### <a name="delete-a-cluster"></a>クラスターの削除

作成した `hdinsight-cluster-delete` Runbook を選択して、クラスターを削除します。 **[開始]** を選択し、**CLUSTERNAME** パラメーターを入力して、 **[OK]** を選択します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

必要ない場合は、意図しない料金を避けるため、作成した Azure Automation アカウントを削除します。 そのためには、Azure portal に移動し、Azure Automation アカウントを作成したリソース グループを選択して、Automation アカウントを選択し、 **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure PowerShell を使用して HDInsight の Apache Hadoop クラスターを管理する](hdinsight-administer-use-powershell.md)