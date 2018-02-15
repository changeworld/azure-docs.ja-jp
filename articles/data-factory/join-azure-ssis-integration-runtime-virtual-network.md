---
title: "Azure-SSIS 統合ランタイムの VNet への参加 | Microsoft Docs"
description: "Azure-SSIS 統合ランタイムを Azure 仮想ネットワークに参加させる方法について説明します。"
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: spelluru
ms.openlocfilehash: 2131aa75dcfb975f11cff9800087c3e4e7170378
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/01/2018
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network"></a>Azure-SSIS 統合ランタイムを仮想ネットワークに参加させる
以下のシナリオでは、Azure-SSIS 統合ランタイム (IR) を Azure 仮想ネットワーク (VNet) に参加させます。 

- SSIS カタログ データベースのホストとなる SQL Server マネージ インスタンス (プライベート プレビュー) が VNet に属している。
- Azure-SSIS 統合ランタイム上で実行される SSIS パッケージからオンプレミス データ ストアに接続する必要がある。

 Azure Data Factory バージョン 2 (プレビュー) では、Azure-SSIS 統合ランタイムをクラシック VNet または Azure Resource Manager VNet に参加させることができます。 

 > [!NOTE]
> この記事は、現在プレビュー段階にある Data Factory のバージョン 2 に適用されます。 一般公開 (GA) されている Data Factory サービスのバージョン 1 を使用している場合は、[Data Factory バージョン 1 のドキュメント](v1/data-factory-introduction.md)を参照してください。

## <a name="access-on-premises-data-stores"></a>オンプレミスのデータ ストアにアクセスする
SSIS パッケージがパブリック クラウドのデータ ストアだけにアクセスする場合は、VNet に Azure-SSIS IR を参加させる必要はありません。 SSIS パッケージがオンプレミスのデータ ストアにアクセスする場合は、オンプレミスのネットワークに接続されている VNet に Azure-SSIS IR を参加させる必要があります。 SSIS カタログが VNet 内にない Azure SQL Database でホストされている場合は、適切なポートを開く必要があります。 SSIS カタログが Azure Resource Manager VNet 内またはクラシック VNet 内にある Azure SQL マネージ インスタンスでホストされている場合は、同じ VNet に、または Azure SQL マネージ インスタンスが存在する VNet との間に VNet 間接続のある別の VNet に、Azure-SSIS IR を参加させることができます。 以降のセクションではさらに詳しく説明します。

注意すべき重要な点がいくつかあります。 

- オンプレミスのネットワークに接続された既存の VNet がない場合は、最初に、Azure-SSIS 統合ランタイムを参加させるための [Azure Resource Manager VNet](../virtual-network/quick-create-portal.md#create-a-virtual-network) または[クラシック VNet](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) を作成します。 次に、その VNet からオンプレミス ネットワークへのサイト間 [VPN ゲートウェイ接続](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md)/[ExpressRoute](../expressroute/expressroute-howto-linkvnet-classic.md) 接続を構成します。
- Azure-SSIS IR と同じ場所にオンプレミス ネットワークに接続された既存の Azure Resource Manager またはクラシック VNet がある場合は、その VNet に IR を参加させることができます。
- Azure-SSIS IR とは異なる場所にオンプレミス ネットワークに接続された既存のクラシック VNet がある場合は、最初に、Azure-SSIS IR を参加させるための[クラシック VNet](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) を作成します。 次に、[クラシック間の VNet](../vpn-gateway/vpn-gateway-howto-vnet-vnet-portal-classic.md) 接続を構成します。 または、Azure-SSIS 統合ランタイムを参加させるための [Azure Resource Manager VNet](../virtual-network/quick-create-portal.md#create-a-virtual-network) を作成します。 次に、[クラシックと Azure Resource Manager の間の VNet](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md) 接続を構成します。
- Azure-SSIS IR とは異なる場所にオンプレミス ネットワークに接続された既存の Azure Resource Manager VNet がある場合は、最初に、Azure-SSIS IR を参加させるための [Azure Resource Manager VNet](../virtual-network/quick-create-portal.md##create-a-virtual-network) を作成します。 次に、Azure Resource Manager 間の VNet 接続を構成します。 または、Azure-SSIS IR を参加させるための [クラシック VNet](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) を作成します。 次に、[クラシックと Azure Resource Manager の間の VNet](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md) 接続を構成します。

## <a name="domain-name-services-server"></a>ドメイン ネーム サービス サーバー 
Azure-SSIS 統合ランタイムが参加する VNet で独自のドメイン ネーム サービス (DNS) サーバーを使う必要がある場合は、ガイダンスに従い、[VNet 内の Azure-SSIS 統合ランタイムのノードが Azure エンドポイントを解決できるようにします](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server)。

## <a name="network-security-group"></a>ネットワーク セキュリティ グループ
Azure-SSIS 統合ランタイムが参加する VNet にネットワーク セキュリティ グループ (NSG) を実装する必要がある場合は、次のポートを受信/送信トラフィックが通過できるようにします。

| ポート | 方向 | トランスポート プロトコル | 目的 | 受信元/送信先 |
| ---- | --------- | ------------------ | ------- | ----------------------------------- |
| 10100、20100、30100 (IR をクラシック VNet に参加させる場合)<br/><br/>29876、29877 (IR を Azure Resource Manager VNet に参加させる場合) | 受信 | TCP | Azure サービスはこれらのポートを使って、VNet の Azure-SSIS 統合ランタイムのノードと通信します。 | インターネット | 
| 443 | 送信 | TCP | VNet の Azure-SSIS 統合ランタイムのノードはこのポートを使って、Azure Storage、Event Hub などの Azure サービスにアクセスします。 | INTERNET | 
| 1433<br/>11000 ～ 11999<br/>14000 ～ 14999  | 送信 | TCP | VNet の Azure-SSIS 統合ランタイムのノードはこれらのポートを使って、Azure SQL Database サーバーによってホストされている SSISDB にアクセスします (Azure SQL マネージ インスタンスによってホストされている SSISDB には該当しません)。 | インターネット | 

## <a name="azure-portal-data-factory-ui"></a>Azure Portal (データ ファクトリ UI)
このセクションでは、Azure Portal とデータ ファクトリ UI を使用して、既存の Azure SSIS ランタイムを VNet (クラシックまたは Azure Resource Manager) に参加させる方法について説明します。 Azure SSIS IR を VNet に参加させる前に、まず VNet を適切に構成する必要があります。 VNet の種類 (クラシックまたは Azure Resource Manager) に基づいて次の 2 つのセクションのいずれかを実行します。 次に 3 番目のセクションに進み、Azure SSIS IR を VNet に参加させます。 

### <a name="use-portal-to-configure-a-classic-vnet"></a>ポータルを使ってクラシック VNet を構成する
Azure-SSIS IR を VNet に参加させる前に、まず VNet を構成する必要があります。

1. [Azure Portal](https://portal.azure.com) にログインします。
2. **[その他のサービス]** をクリックします。 **[仮想ネットワーク (クラシック)]** を選びます。
3. 一覧で目的の**仮想ネットワーク**を選びます。 
4. [仮想ネットワーク (クラシック)] ページで、**[プロパティ]** を選びます。 

    ![クラシック VNet のリソース ID](media/join-azure-ssis-integration-runtime-virtual-network/classic-vnet-resource-id.png)
5. **[リソース ID]** のコピー ボタンをクリックして、クラシック ネットワークのリソース ID をクリップボードにコピーします。 クリップボードから OneNote またはファイルに ID を保存します。
6. 左側のメニューの **[サブネット]** をクリックし、**[使用可能なアドレス]** の数が Azure-SSIS 統合ランタイムのノード数より多いことを確認します。

    ![VNet で使用可能なアドレスの数](media/join-azure-ssis-integration-runtime-virtual-network/number-of-available-addresses.png)
7. **MicrosoftAzureBatch** を VNet の**従来の仮想マシン共同作成者**ロールに参加させます。 
    1. 左側メニューの [アクセス制御 (IAM)] をクリックし、ツール バーの **[追加]** をクリックします。
    
        ![アクセス制御 -> 追加](media/join-azure-ssis-integration-runtime-virtual-network/access-control-add.png) 
    2. **[アクセス許可の追加]** ページで、**[ロール]** の **[従来の仮想マシン共同作成者]** を選びます。 **[選択]** テキスト ボックスに **ddbf3205-c6bd-46ae-8127-60eb93363864** をコピー/貼り付け、検索結果の一覧から **[Microsoft Azure Batch]** を選択します。 
    
        ![アクセス許可の追加 - 検索](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-to-vm-contributor.png)
    3. [保存] をクリックして設定を保存し、ページを閉じます。
    
        ![アクセス設定の保存](media/join-azure-ssis-integration-runtime-virtual-network/save-access-settings.png)
    4. 共同作成者の一覧に **MicrosoftAzureBatch** があることを確認します。
    
        ![AzureBatch アクセスの確認](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-in-list.png)
5. VNet が含まれる Azure サブスクリプションに Azure Batch プロバイダーが登録されていることを確認します。登録されていない場合は、Azure Batch プロバイダーを登録します。 Azure Batch アカウントがサブスクリプションに既にある場合は、サブスクリプションは Azure Batch に登録されています。
    1. Azure Portal で、左メニューの **[サブスクリプション]** をクリックします。 
    2. **サブスクリプション**を選択します。 
    3. 左側の **[リソース プロバイダー]** をクリックし、`Microsoft.Batch` が登録済みのプロバイダーであることを確認します。 
    
        ![confirmation-batch-registered](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

    `Microsoft.Batch` が一覧に表示されない場合は、サブスクリプションに[空の Azure Batch アカウントを作成](../batch/batch-account-create-portal.md)します。 これは後で削除することができます。 

### <a name="use-portal-to-configure-an-azure-resource-manager-vnet"></a>ポータルを使ってAzure Resource Manager VNet を構成する
Azure-SSIS IR を VNet に参加させる前に、まず VNet を構成する必要があります。

1. [Azure Portal](https://portal.azure.com) にログインします。
2. **[その他のサービス]** をクリックします。 フィルターを適用して **[仮想ネットワーク]** を選択します。
3. 一覧で目的の**仮想ネットワーク**を選びます。 
4. [仮想ネットワーク] ページで、**[プロパティ]** を選択します。 
5. **[リソース ID]** のコピー ボタンをクリックして、仮想ネットワークのリソース ID をクリップボードにコピーします。 クリップボードから OneNote またはファイルに ID を保存します。
6. 左側のメニューの **[サブネット]** をクリックし、**[使用可能なアドレス]** の数が Azure-SSIS 統合ランタイムのノード数より多いことを確認します。
5. VNet が含まれる Azure サブスクリプションに Azure Batch プロバイダーが登録されていることを確認します。登録されていない場合は、Azure Batch プロバイダーを登録します。 Azure Batch アカウントがサブスクリプションに既にある場合は、サブスクリプションは Azure Batch に登録されています。
    1. Azure Portal で、左メニューの **[サブスクリプション]** をクリックします。 
    2. **サブスクリプション**を選択します。 
    3. 左側の **[リソース プロバイダー]** をクリックし、`Microsoft.Batch` が登録済みのプロバイダーであることを確認します。 
    
        ![confirmation-batch-registered](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

    `Microsoft.Batch` が一覧に表示されない場合は、サブスクリプションに[空の Azure Batch アカウントを作成](../batch/batch-account-create-portal.md)します。 これは後で削除することができます。

### <a name="join-the-azure-ssis-ir-to-a-vnet"></a>Azure SSIS IR を VNet に参加させる


1. [Azure Portal](https://portal.azure.com) の左側のメニューで **[データ ファクトリ]** を選択します。 メニューに **[データ ファクトリ]** が表示されない場合は、**[その他のサービス]** を選択し、**[インテリジェンス + 分析]** セクションで **[データ ファクトリ]** を選択します。 
    
    ![[データ ファクトリ] リスト](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)
2. リストで Azure SSIS 統合ランタイムを使用するデータ ファクトリを選択します。 データ ファクトリのホーム ページが表示されます。 **[作成およびデプロイ]** タイルを選択します。 データ ファクトリのユーザー インターフェイス (UI) が別のタブに表示されます。 

    ![データ ファクトリのホーム ページ](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)
3. データ ファクトリの UI で、**[編集]** タブに切り替え、**[接続]** を選択し、**[統合ランタイム]** タブに切り替えます。 

    ![[統合ランタイム] タブ](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)
4. Azure SSIS IR が実行されている場合、統合ランタイム リストで、Azure SSIS IR の **[アクション]** 列の **[停止]** ボタンを選択します。 IR は、停止するまで編集できません。 

    ![IR を停止する](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)
1. 統合ランタイム リストで、Azure SSIS IR の **[アクション]** 列の **[編集]** ボタンを選択します。

    ![統合ランタイムの編集](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)
5. **[統合ランタイムのセットアップ]** ウィンドウの **[全般設定]** ページで、**[次へ]** を選択します。 

    ![IR のセットアップ - 全般設定](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-general-settings.png)
6. **[SQL 設定]** ページで管理者の**パスワード**を入力し、**[次へ]** を選択します。

    ![IR のセットアップ - SQL 設定](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-sql-settings.png)
7. **[詳細設定]** ページで、次の操作を実行します。 

    1. **[Select a VNet for your Azure-SSIS Integration Runtime to join and allow Azure services to configure VNet permissions/settings]\(参加させる Azure-SSIS 統合ランタイム用の VNet を選択し、Azure サービスが VNet の権限/設定を構成することを許可する\)** チェックボックスをオンにします。 
    2. **[種類]** で VNet がクラシック VNet か Azure Resource Manager VNet かを指定します。 
    3. **[VNET 名]** で VNet を選択します。
    4. **[サブネット名]** で、VNet のサブネットを選択します。 
    5. **[Update]\(更新\)** を選択します。 

        ![IR のセットアップ - 詳細設定](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-advanced-settings.png)
8. これで、Azure SSIS IR の **[アクション]** 列の **[開始]** ボタンを使用して IR を開始できます。 Azure SSIS IR の開始には約 20 分かかります。 


## <a name="azure-powershell"></a>Azure PowerShell

### <a name="configure-vnet"></a>VNet を構成する
Azure-SSIS IR を VNet に参加させる前に、まず VNet を構成する必要があります。 Azure-SSIS 統合ランタイムが VNet に参加するための VNet のアクセス許可/設定を自動的に構成するには、次のスクリプトを追加します。

```powershell
# Register to Azure Batch resource provider
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    $BatchObjectId = (Get-AzureRmADServicePrincipal -ServicePrincipalName "MicrosoftAzureBatch").Id
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign VM contributor role to Microsoft.Batch
        New-AzureRmRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="create-an-azure-ssis-ir-and-join-it-to-a-vnet"></a>Azure-SSIS IR を作成して VNet に参加させる
Azure-SSIS IR を作成し、作成と同時に VNet に参加させることができます。 Azure-SSIS IR の作成と VNet への参加を同時に実行するための完全なスクリプトと手順については、[Azure SSIS IR の作成](create-azure-ssis-integration-runtime.md#azure-powershell)に関する記事を参照してください。

### <a name="join-an-existing-azure-ssis-ir-to-a-vnet"></a>既存の Azure-SSIS IR を VNet に参加させる
[Azure-SSIS 統合ランタイムの作成](create-azure-ssis-integration-runtime.md)に関する記事に示されているスクリプトは、Azure-SSIS IR の作成と VNet への参加を同じスクリプトで実行する方法を示しています。 既存の Azure SSIS がある場合に、それを VNet に参加させるには、次の手順を実行します。 

1. Azure-SSIS IR を停止します。
2. VNet に参加するように Azure SSIS IR を構成します。 
3. Azure-SSIS IR を開始します。 

### <a name="define-the-variables"></a>変数を定義する

```powershell
$ResourceGroupName = "<Azure resource group name>"
$DataFactoryName = "<Data factory name>" 
$AzureSSISName = "<Specify Azure-SSIS IR name>"
## These two parameters apply if you are using a VNet and an Azure SQL Managed Instance (private preview) 
# Specify information about your classic or Azure Resource Manager virtual network (VNet).
$VnetId = "<Name of your Azure virtual netowrk>"
$SubnetName = "<Name of the subnet in VNet>"
```

### <a name="stop-the-azure-ssis-ir"></a>Azure-SSIS IR を停止する
VNet に参加させる前に、Azure SSIS の統合ランタイムを停止します。 このコマンドは、そのすべてのノードを解放し、課金を停止します。

```powershell
Stop-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force 
```
### <a name="configure-vnet-settings-for-the-azure-ssis-ir-to-join"></a>Azure-SSIS IR が参加するように VNet 設定を構成する
Azure Backup リソース プロバイダーに登録します。

```powershell
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    $BatchObjectId = (Get-AzureRmADServicePrincipal -ServicePrincipalName "MicrosoftAzureBatch").Id
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
        Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign VM contributor role to Microsoft.Batch
        New-AzureRmRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="configure-the-azure-ssis-ir"></a>Azure-SSIS IR を構成する
Set-AzureRmDataFactoryV2IntegrationRuntime コマンドを実行して、Azure-SSIS 統合ランタイムが VNet に参加するように構成します。 

```powershell
Set-AzureRmDataFactoryV2IntegrationRuntime  -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Type Managed `
                                            -VnetId $VnetId `
                                            -Subnet $SubnetName
```

### <a name="start-the-azure-ssis-ir"></a>Azure-SSIS IR を開始する
Azure-SSIS 統合ランタイムを起動するには、次のコマンドを実行します。 

```powershell
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

```
このコマンドは、完了までに **20 ～ 30 分**かかります。

## <a name="next-steps"></a>次の手順
Azure-SSIS 統合ランタイムについて詳しくは、以下のトピックをご覧ください。 

- [Azure-SSIS 統合ランタイム](concepts-integration-runtime.md#azure-ssis-integration-runtime):  この記事では、Azure-SSIS IR など、統合ランタイムの一般的な概念について説明されています。 
- [チュートリアル: SSIS パッケージを Azure にデプロイする](tutorial-create-azure-ssis-runtime-portal.md):  この記事では、Azure-SSIS IR を作成し、Azure SQL Database を使って SSIS カタログをホストする手順が説明されています。 
- [方法: Azure-SSIS 統合ランタイムを作成する](create-azure-ssis-integration-runtime.md):  この記事では、チュートリアルを基に、Azure SQL マネージ インスタンス (プライベート プレビュー) の使い方と、IR を VNet に参加させる方法が説明されています。 
- [Azure-SSIS IR を監視する](monitor-integration-runtime.md#azure-ssis-integration-runtime):  この記事では、Azure-SSIS IR に関する情報を取得する方法と、返された情報での状態が説明されています。 
- [Azure-SSIS IR を管理する](manage-azure-ssis-integration-runtime.md):  この記事では、Azure-SSIS IR を停止、開始、削除する方法が説明されています。 また、IR にノードを追加することで Azure-SSIS IR をスケールアウトする方法も説明されています。 
