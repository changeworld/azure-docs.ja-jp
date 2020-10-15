---
title: テンプレートを使用して Azure Stack Edge Pro デバイスに VM をデプロイする
description: テンプレートを使用して、Azure Stack Edge Pro デバイスに仮想マシン (VM) を作成し、管理する方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/04/2020
ms.author: alkohli
ms.openlocfilehash: eeefbcdc080620c60f7cd49b8f749375e23ddd02
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "90899718"
---
# <a name="deploy-vms-on-your-azure-stack-edge-pro-gpu-device-via-templates"></a>テンプレートを使用して Azure Stack Edge Pro GPU デバイスに VM をデプロイする

このチュートリアルでは、テンプレートを使用して、Azure Stack Edge Pro デバイスに VM を作成し、管理する方法について説明します。 これらのテンプレートは、VM のインフラストラクチャと構成を定義する JavaScript Object Notation (JSON) ファイルです。 これらのテンプレートでは、デプロイするリソースとそれらのリソースのプロパティを指定します。

テンプレートは、ファイルから実行時に入力としてパラメーターを受け取ることができるため、さまざまな環境で柔軟に使用できます。 標準的な名前付け構造は、テンプレートの場合は `TemplateName.json`、パラメーター ファイルの場合は `TemplateName.parameters.json` となります。 ARM テンプレートの詳細については、「[Azure Resource Manager テンプレートとは](../azure-resource-manager/templates/overview.md)」を参照してください。

このチュートリアルでは、事前に記述されたサンプル テンプレートを使用してリソースを作成します。 テンプレート ファイルを編集する必要はありません。`.parameters.json` ファイルのみを変更して、コンピューターへのデプロイをカスタマイズすることができます。 

## <a name="vm-deployment-workflow"></a>VM デプロイのワークフロー

多数のデバイスに Azure Stack Edge Pro VM をデプロイする場合は、完全なフリート用に 1 つの sysprep 済みの VHD を、デプロイ用に同じテンプレートを使用することができます。また、デプロイの場所ごとにそのテンプレートへのパラメーターに対して、わずかな変更を行うだけで済みます (これらの変更は、ここで行っているように手動で行うことも、プログラムで行うこともできます)。 

テンプレートを使用するデプロイの大まかなワークフローは次のとおりです。

1. **前提条件を構成する** - 前提条件には、デバイス、クライアント、および VM に関する 3 種類があります。

    1. **デバイスに関する前提条件**

        1. デバイス上の Azure Resource Manager に接続します。
        2. ローカル UI を使用するコンピューティングを有効にします。

    2. **クライアントに関する前提条件**

        1. クライアント上に VM テンプレートと関連ファイルをダウンロードします。
        1. 必要に応じて、クライアントで TLS 1.2 を構成します。
        1. クライアント上に [Microsoft Azure Storage Explorer をダウンロードしてインストール](https://go.microsoft.com/fwlink/?LinkId=708343&clcid=0x409)します。

    3. **VM に関する前提条件**

        1. すべての VM リソースを含めるデバイスの場所にリソース グループを作成します。
        1. VM イメージの作成に使用される VHD をアップロードするためのストレージ アカウントを作成します。
        1. ローカル ストレージ アカウントの URI を、デバイスにアクセスするクライアント上で DNS または hosts ファイルに追加します。
        1. デバイスにアクセスするローカル クライアントだけでなく、デバイスにも BLOB ストレージ証明書をインストールします。 必要に応じて、Storage Explorer に BLOB ストレージ証明書をインストールします。
        1. VHD を作成し、先ほど作成したストレージ アカウントにアップロードします。

2. **テンプレートから VM を作成する**

    1. `CreateImageAndVnet.parameters.json` パラメーター ファイルと `CreateImageAndVnet.json` デプロイ テンプレートを使用して、VM イメージと VNet を作成します。
    1. `CreateVM.parameters.json` パラメーター ファイルと `CreateVM.json` デプロイ テンプレートを使用して、前に作成したリソースで VM を作成します。

## <a name="device-prerequisites"></a>デバイスに関する前提条件

Azure Stack Edge Pro デバイスでこれらの前提条件を構成します。

[!INCLUDE [azure-stack-edge-gateway-deploy-virtual-machine-prerequisites](../../includes/azure-stack-edge-gateway-deploy-virtual-machine-prerequisites.md)]

## <a name="client-prerequisites"></a>クライアントに関する前提条件

Azure Stack Edge Pro デバイスにアクセスするために使用されるクライアントで、これらの前提条件を構成します。

1. VHD をアップロードするために使用する場合は、[Storage Explorer をダウンロード](https://azure.microsoft.com/features/storage-explorer/)します。 AzCopy をダウンロードして VHD をアップロードすることもできます。 以前のバージョンの AzCopy を実行している場合は、クライアント コンピューターでの TLS 1.2 の構成が必要になることがあります。 
1. クライアント コンピューターに [VM テンプレートとパラメーター ファイルをダウンロード](https://aka.ms/ase-vm-templates)します。 それを、作業ディレクトリとして使用するディレクトリに解凍します。


## <a name="vm-prerequisites"></a>VM に関する前提条件

VM の作成に必要なリソースを作成するために、これらの前提条件を構成します。 

    
### <a name="create-a-resource-group"></a>リソース グループを作成する

[New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) を使用して Azure リソース グループを作成します。 リソース グループは、ストレージ アカウント、ディスク、マネージド ディスクなどの Azure リソースをデプロイおよび管理するのに使用する論理コンテナーです。

> [!IMPORTANT]
> すべてのリソースは、デバイスと同じ場所に作成され、その場所は **DBELocal** に設定されます。

```powershell
New-AzureRmResourceGroup -Name <Resource group name> -Location DBELocal
```

サンプル出力を次に示します。

```powershell
PS C:\windows\system32> New-AzureRmResourceGroup -Name myasegpurgvm -Location DBELocal

ResourceGroupName : myasegpurgvm
Location          : dbelocal
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/DDF9FC44-E990-42F8-9A91-5A6A5CC472DB/resourceGroups/myasegpurgvm

PS C:\windows\system32>
```

### <a name="create-a-storage-account"></a>ストレージ アカウントの作成

前の手順で作成したリソース グループを使用して、新しいストレージ アカウントを作成します。 これは、VM の仮想ディスク イメージのアップロードに使用される**ローカル ストレージ アカウント**です。

```powershell
New-AzureRmStorageAccount -Name <Storage account name> -ResourceGroupName <Resource group name> -Location DBELocal -SkuName Standard_LRS
```

> [!NOTE]
> Azure Resource Manager を使用して作成できるのは、ローカル冗長ストレージ (Standard_LRS または Premium_LRS) などのローカル ストレージ アカウントのみです。 階層型ストレージ アカウントを作成する場合は、[Azure Stack Edge Pro でのストレージ アカウントの追加と接続](azure-stack-edge-j-series-deploy-add-storage-accounts.md)に関するページの手順を参照してください。

サンプル出力を次に示します。

```powershell
PS C:\windows\system32> New-AzureRmStorageAccount -Name myasegpusavm -ResourceGroupName myasegpurgvm -Location DBELocal -SkuName Standard_LRS

StorageAccountName ResourceGroupName Location SkuName     Kind    AccessTier CreationTime
------------------ ----------------- -------- -------     ----    ---------- ------------
myasegpusavm       myasegpurgvm      DBELocal StandardLRS Storage            7/29/2020 10:11:16 PM

PS C:\windows\system32>
```

ストレージ アカウント キーを取得するには、`Get-AzureRmStorageAccountKey` コマンドを実行します。 このコマンドのサンプル出力を次に示します。

```powershell
PS C:\windows\system32> Get-AzureRmStorageAccountKey

cmdlet Get-AzureRmStorageAccountKey at command pipeline position 1
Supply values for the following parameters:
(Type !? for Help.)
ResourceGroupName: myasegpurgvm
Name: myasegpusavm

KeyName    Value                                                  Permissions   
-------     -----                                                   --
key1 GsCm7QriXurqfqx211oKdfQ1C9Hyu5ZutP6Xl0dqlNNhxLxDesDej591M8y7ykSPN4fY9vmVpgc4ftkwAO7KQ== 11 
key2 7vnVMJUwJXlxkXXOyVO4NfqbW5e/5hZ+VOs+C/h/ReeoszeV+qoyuBitgnWjiDPNdH4+lSm1/ZjvoBWsQ1klqQ== ll
```

### <a name="add-blob-uri-to-hosts-file"></a>hosts ファイルに BLOB の URI を追加する

BLOB ストレージへの接続に使用するクライアント用に、hosts ファイルに BLOB URI を既に追加していることを確認します。 **管理者としてメモ帳を実行**し、`C:\windows\system32\drivers\etc\hosts` で BLOB URI の次のエントリを追加します。

`<Device IP> <storage account name>.blob.<Device name>.<DNS domain>`

一般的な環境では、すべてのストレージ アカウントが、`*.blob.devicename.domainname.com` エントリがある Azure Stack Edge Pro デバイスを指すように DNS を構成します。

### <a name="optional-install-certificates"></a>(省略可能) 証明書をインストールする

*http* を使用して Storage Explorer 経由で接続する場合は、この手順をスキップします。 *https* を使用している場合は、Storage Explorer に適切な証明書をインストールする必要があります。 この場合は、BLOB エンドポイント証明書をインストールします。 詳細については、[証明書を管理する](azure-stack-edge-j-series-manage-certificates.md)方法に関するページで、証明書を作成およびアップロードする方法を参照してください。 

### <a name="create-and-upload-a-vhd"></a>VHD を作成してアップロードする

後の手順でアップロードに使用できる仮想ディスク イメージがあることを確認します。 [VM イメージを作成する](azure-stack-edge-j-series-create-virtual-machine-image.md)方法に関するページの手順に従います。 

前の手順で作成したローカル ストレージ アカウントのページ BLOB に、使用するディスク イメージをコピーします。 前の手順で作成した[ストレージ アカウントに VHD をアップロードするために AzCopy](azure-stack-edge-j-series-deploy-virtual-machine-powershell.md#upload-a-vhd) や [Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) などのツールを使用することができます。 

### <a name="use-storage-explorer-for-upload"></a>アップロードに Storage Explorer を使用する

1. ストレージ エクスプローラーを開きます。 **[編集]** に移動し、アプリケーションが **[Azure Stack API を対象にする]** に設定されていることを確認します。

    ![対象を Azure Stack API に設定する](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/set-target-apis-1.png)

2. PEM 形式でクライアント証明書をインストールします。 **[編集]、[SSL 証明書]、[証明書のインポート]** の順に移動します。 クライアント証明書をポイントします。

    ![BLOB ストレージのエンドポイント証明書をインポートする](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/import-blob-storage-endpoint-certificate-1.png)

    - デバイスによって生成された証明書を使用する場合は、BLOB ストレージのエンドポイント `.cer` 証明書をダウンロードし、`.pem` 形式に変換します。 次のコマンドを実行します。 
    
        ```powershell
        PS C:\windows\system32> Certutil -encode 'C:\myasegpu1_Blob storage (1).cer' .\blobstoragecert.pem
        Input Length = 1380
        Output Length = 1954
        CertUtil: -encode command completed successfully.
        ```
    - 独自の証明書を持ち込む場合は、`.pem` 形式で署名チェーンのルート証明書を使用します。

3. 証明書をインポートした後、変更を有効にするために Storage Explorer を再起動します。

    ![Storage Explorer を再起動する](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/restart-storage-explorer-1.png)

4. 左ペインで、 **[ストレージ アカウント]** を右クリックし、 **[Azure Storage へ接続]** を選択します。 

    ![Azure Storage に接続する 1](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/connect-azure-storage-1.png)

5. **[Use a storage account name and key]\(ストレージ アカウント名とキーを使用\)** を選択します。 **[次へ]** を選択します。

    ![Azure Storage に接続する 2](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/connect-azure-storage-2.png)

6. **[名前とキーを使用して接続する]** で、**表示名**、**ストレージ アカウント名**、Azure Storage の**アカウント キー**を指定します。 **その他**のストレージ ドメインを選択してから、`<device name>.<DNS domain>` 接続文字列を指定します。 Storage Explorer に証明書をインストールしなかった場合は、 **[HTTP を使用する]** オプションをオンにします。 **[次へ]** を選択します。

    ![名前とキーを使用して接続する](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/connect-name-key-1.png)

7. **[接続の概要]** を確認し、 **[接続]** を選択します。

8. 左ペインにストレージ アカウントが表示されます。 そのストレージ アカウントを選択して展開します。 **[BLOB コンテナー]** を選択し、右クリックして **[BLOB コンテナーの作成]** を選択します。 BLOB コンテナーの名前を指定します。

9. 先ほど作成したコンテナーを選択し、右ペインで **[アップロード]、[ファイルのアップロード]** の順に選択します。 

    ![VHD ファイルをアップロードする 1](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/upload-vhd-file-1.png)

10. **[選択したファイル]** で、アップロードする VHD を参照してポイントします。 **[BLOB の種類]** で **[ページ BLOB]** を選び、 **[アップロード]** を選択します。

    ![VHD ファイルをアップロードする 2](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/upload-vhd-file-2.png)

11. VHD が BLOB コンテナーに読み込まれたら、その VHD を選んで右クリックし、 **[プロパティ]** 選択します。 

    ![VHD ファイルをアップロードする 3](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/upload-vhd-file-3.png)

12. **Uri** をコピーして保存します。これは後の手順で使用します。

    ![URI をコピーする](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/copy-uri-1.png)

<!--### Use AzCopy for upload

Before you use AzCopy, make sure that the [AzCopy is configured correctly](#configure-azcopy) for use with the blob storage REST API version that you are using with your Azure Stack Edge Pro device.


```powershell
AzCopy /Source:<sourceDirectoryForVHD> /Dest:<blobContainerUri> /DestKey:<storageAccountKey> /Y /S /V /NC:32  /BlobType:page /destType:blob 
```

> ![NOTE]
> Set `BlobType` to page for creating a managed disk out of VHD. Set `BlobType` to block when writing to tiered storage accounts using AzCopy.

You can download the disk images from the marketplace. For detailed steps, go to [Get the virtual disk image from Azure marketplace](azure-stack-edge-j-series-create-virtual-machine-image.md).

A sample output using AzCopy 7.3 is shown below. For more information on this command, go to [Upload VHD file to storage account using AzCopy](../devtest-labs/devtest-lab-upload-vhd-using-azcopy.md).


```powershell
AzCopy /Source:\\hcsfs\scratch\vm_vhds\linux\ /Dest:http://sa191113014333.blob.dbe-1dcmhq2.microsoftdatabox.com/vmimages /DestKey:gJKoyX2Amg0Zytd1ogA1kQ2xqudMHn7ljcDtkJRHwMZbMK== /Y /S /V /NC:32 /BlobType:page /destType:blob /z:2e7d7d27-c983-410c-b4aa-b0aa668af0c6
```-->

## <a name="create-image-and-vnet-for-your-vm"></a>VM のイメージと VNet を作成する

VM のイメージと仮想ネットワークを作成するには、`CreateImageAndVnet.parameters.json` パラメーター ファイルを編集してから、このパラメーター ファイルを使用するテンプレート `CreateImageAndVnet.json` をデプロイする必要があります。


### <a name="edit-parameters-file"></a>パラメーター ファイルを編集する

`CreateImageAndVnet.parameters.json` ファイルでは、次のパラメーターを受け取ります。 

```json
"parameters": {
        "osType": {
              "value": "<Operating system corresponding to the VHD you upload can be Windows or Linux>"
        },
        "imageName": {
            "value": "<Name for the VM iamge>"
        },
        "imageUri": {
              "value": "<Path to the VHD that you uploaded in the Storage account>"
        },
        "vnetName": {
            "value": "<Name for the virtual network where you will deploy the VM>"
        },
        "subnetName": {
            "value": "<Name for the subnet for the VNet>"
        },
        "addressPrefix": {
            "value": "<Address prefix for the virtual network>"
        },
        "subnetPrefix": {
            "value": "<Subnet prefix for the subnet for the Vnet>"
        }
    }
```

`CreateImageAndVnet.parameters.json` ファイルを編集し、Azure Stack Edge Pro デバイスに次のものを含めます。

1. アップロードする VHD に対応する OS の種類を指定します。 OS の種類は、Windows または Linux にすることができます。

    ```json
    "parameters": {
            "osType": {
              "value": "Windows"
            },
    ```

2. イメージの URI を、前の手順でアップロードしたイメージの URI に変更します。

    ```json
    "imageUri": {
        "value": "https://myasegpusavm.blob.myasegpu1.wdshcsso.com/windows/WindowsServer2016Datacenter.vhd"
        },
    ```
    Storage Explorer で *http* を使用している場合は、これを *https* URI に変更します。

3. `addressPrefix` と `subnetPrefix` を変更します。 デバイスのローカル UI で、 **[ネットワーク]** ページに移動します。 コンピューティングに対して有効にしたポートを見つけます。 基本ネットワークの IP アドレスを取得し、サブネット マスクを追加して CIDR 表記を作成します。 標準の 255.255.255.0 サブネットがある場合は、IP アドレスの最後の番号を 0 に置き換え、その末尾に /24 を追加することでこれを行います。 したがって、サブネット マスクが 255.255.255.0 の 10.126.68.0 は、10.126.68.0/24 となります。 
    
    ```json
    "addressPrefix": {
                "value": "10.126.68.0/24"
            },
            "subnetPrefix": {
                "value": "10.126.68.0/24"
            }
    ```  

4. パラメーターの一意のイメージ名、VNet 名、およびサブネット名を指定します。

    この記事で使用される json のサンプルを次に示します。

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
      "parameters": {
        "osType": {
          "value": "Windows"
        },
        "imageName": {
          "value": "image1"
        },
        "imageUri": {
          "value": "https://myasegpusavm.blob.myasegpu1.wdshcsso.com/windows/WindowsServer2016Datacenter.vhd"
        },
        "vnetName": {
          "value": "vnet1"
        },
        "subnetName": {
          "value": "subnet1"
        },
        "addressPrefix": {
          "value": "10.126.68.0/24"
        },
        "subnetPrefix": {
          "value": "10.126.68.0/24"
        }
      }
    }
    ```
5. パラメーター ファイルを保存します。


### <a name="deploy-template"></a>テンプレートのデプロイ 

テンプレート `CreateImageAndVnet.json` をデプロイします。 このテンプレートにより、後の手順で VM を作成するために使用される VNet およびイメージ リソースがデプロイされます。

> [!NOTE]
> テンプレートをデプロイするときに認証エラーが発生する場合は、このセッションの Azure 資格情報の有効期限が切れている可能性があります。 `login-AzureRM` コマンドを再実行し、Azure Stack Edge Pro デバイスで Azure Resource Manager にもう一度接続します。

1. 次のコマンドを実行します。 
    
    ```powershell
    $templateFile = "Path to CreateImageAndVnet.json"
    $templateParameterFile = "Path to CreateImageAndVnet.parameters.json"
    $RGName = "<Name of your resource group>"
    New-AzureRmResourceGroupDeployment `
        -ResourceGroupName $RGName `
        -TemplateFile $templateFile `
        -TemplateParameterFile $templateParameterFile `
        -Name "<Name for your deployment>"
    ```

2. イメージおよび VNet リソースが正常にプロビジョニングされているかどうかを確認します。 正常に作成されたイメージと VNet のサンプル出力を次に示します。
    
    ```powershell
    PS C:\07-30-2020> login-AzureRMAccount -EnvironmentName aztest1 -TenantId c0257de7-538f-415c-993a-1b87a031879d
    
    Account               SubscriptionName              TenantId                             Environment
    -------               ----------------              --------                             -----------
    EdgeArmUser@localhost Default Provider Subscription c0257de7-538f-415c-993a-1b87a031879d aztest1
    
    PS C:\07-30-2020> $templateFile = "C:\07-30-2020\CreateImageAndVnet.json"
    PS C:\07-30-2020> $templateParameterFile = "C:\07-30-2020\CreateImageAndVnet.parameters.json"
    PS C:\07-30-2020> $RGName = "myasegpurgvm"
    PS C:\07-30-2020> New-AzureRmResourceGroupDeployment `
    >>     -ResourceGroupName $RGName `
    >>     -TemplateFile $templateFile `
    >>     -TemplateParameterFile $templateParameterFile `
    >>     -Name "Deployment1"
    
    DeploymentName          : Deployment1
    ResourceGroupName       : myasegpurgvm
    ProvisioningState       : Succeeded
    Timestamp               : 7/30/2020 5:53:32 PM
    Mode                    : Incremental
    TemplateLink            :
    Parameters              :
                              Name             Type                       Value
                              ===============  =========================  ==========
                              osType           String                     Windows
                              imageName        String                     image1
                              imageUri         String
                              https://myasegpusavm.blob.myasegpu1.wdshcsso.com/windows/WindowsServer2016Datacenter.vhd
                              vnetName         String                     vnet1
                              subnetName       String                     subnet1
                              addressPrefix    String                     10.126.68.0/24
                              subnetPrefix     String                     10.126.68.0/24
    
    Outputs                 :
    DeploymentDebugLogLevel :
    
    PS C:\07-30-2020>
    ```
    
## <a name="create-vm"></a>VM を作成する

### <a name="edit-parameters-file-to-create-vm"></a>パラメーター ファイルを編集して VM を作成する
 
VM を作成するには、`CreateVM.parameters.json` パラメーター ファイルを使用します。 次のパラメーターを受け取ります。
    
```json
"vmName": {
            "value": "<Name for your VM>"
        },
        "adminUsername": {
            "value": "<Username to log into the VM>"
        },
        "Password": {
            "value": "<Password to log into the VM>"
        },
        "imageName": {
            "value": "<Name for your image>"
        },
        "vmSize": {
            "value": "<A supported size for your VM>"
        },
        "vnetName": {
            "value": "<Name for the virtual network you created earlier>"
        },
        "subnetName": {
            "value": "<Name for the subnet you created earlier>"
        },
        "nicName": {
            "value": "<Name for the network interface>"
        },
        "privateIPAddress": {
            "value": "<Private IP address, enter a static IP in the subnet created earlier or leave empty to assign DHCP>"
        },
        "IPConfigName": {
            "value": "<Name for the ipconfig associated with the network interface>"
        }
```    

`CreateVM.parameters.json` で、Azure Stack Edge Pro デバイスに適したパラメーターを割り当てます。

1. 一意の名前、ネットワーク インターフェイス名、および ipconfig 名を指定します。 
1. ユーザー名、パスワード、およびサポートされている VM のサイズを入力します。
1. `CreateImageAndVnet.parameters.json` のパラメーターに指定されているのと同じ名前を、**VnetName**、**subnetName**、および **ImageName** に付けます。 たとえば、VnetName、subnetName、ImageName を **vnet1**、**subnet1**、**image1** と指定した場合は、このテンプレートのパラメーターも同じ値のままにします。
1. ここで、前に定義したサブネット ネットワーク内の VM に割り当てる静的 IP アドレスが必要になります。 **PrivateIPAddress** を、パラメーター ファイルのこのアドレスに置き換えます。 VM により、ローカルの DCHP サーバーから IP アドレスが取得されるようにするには、`privateIPAddress` の値を空白のままにします。  
    
    ```json
    "privateIPAddress": {
                "value": "5.5.153.200"
            },
    ```
    
4. パラメーター ファイルを保存します。

    この記事で使用される json のサンプルを次に示します。
    
    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "vmName": {
                "value": "mywindowsvm"
            },
            "adminUsername": {
                "value": "Administrator"
            },
            "Password": {
                "value": "Password1"
            },
            "imageName": {
                "value": "image1"
            },
            "vmSize": {
                "value": "Standard_D1_v2"
            },
            "vnetName": {
                "value": "vnet1"
            },
            "subnetName": {
                "value": "subnet1"
            },
            "nicName": {
                "value": "nic1"
            },
            "privateIPAddress": {
                "value": "10.126.68.186"
            },
            "IPConfigName": {
                "value": "ipconfig1"
            }
        }
    }
    ```      

### <a name="deploy-template-to-create-vm"></a>テンプレートをデプロイして VM を作成する

VM 作成テンプレートの `CreateVM.json` をデプロイします。 このテンプレートにより、既存の VNet からネットワーク インターフェイスが作成され、デプロイされたイメージから VM が作成されます。

1. 次のコマンドを実行します。 
    
    ```powershell
    Command:
        
        $templateFile = "<Path to CreateVM.json>"
        $templateParameterFile = "<Path to CreateVM.parameters.json>"
        $RGName = "<Resource group name>"
             
        New-AzureRmResourceGroupDeployment `
            -ResourceGroupName $RGName `
            -TemplateFile $templateFile `
            -TemplateParameterFile $templateParameterFile `
            -Name "<DeploymentName>"
    ```   

    VM の作成には、15 分から 20 分かかります。 正常に作成された VM のサンプル出力を次に示します。
    
    ```powershell
    PS C:\07-30-2020> $templateFile = "C:\07-30-2020\CreateWindowsVM.json"
        PS C:\07-30-2020> $templateParameterFile = "C:\07-30-2020\CreateWindowsVM.parameters.json"
        PS C:\07-30-2020> $RGName = "myasegpurgvm"
        PS C:\07-30-2020> New-AzureRmResourceGroupDeployment `
        >>     -ResourceGroupName $RGName `
        >>     -TemplateFile $templateFile `
        >>     -TemplateParameterFile $templateParameterFile `
        >>     -Name "Deployment2"    
        
        DeploymentName          : Deployment2
        ResourceGroupName       : myasegpurgvm
        ProvisioningState       : Succeeded
        Timestamp               : 7/30/2020 6:21:09 PM
        Mode                    : Incremental
        TemplateLink            :
        Parameters              :
                                  Name             Type                       Value
                                  ===============  =========================  ==========
                                  vmName           String                     MyWindowsVM
                                  adminUsername    String                     Administrator
                                  password         String                     Password1
                                  imageName        String                     image1
                                  vmSize           String                     Standard_D1_v2
                                  vnetName         String                     vnet1
                                  subnetName       String                     subnet1
                                  nicName          String                     Nic1
                                  ipConfigName     String                     ipconfig1
                                  privateIPAddress  String                    10.126.68.186
        
        Outputs                 :
        DeploymentDebugLogLevel :    
        
        PS C:\07-30-2020>
    ```   
また、`–AsJob` パラメーターを使用して `New-AzureRmResourceGroupDeployment` コマンドを非同期に実行することもできます。 コマンドレットがバックグラウンドで実行される場合の出力例を次に示します。 その後、 `Get-Job` コマンドレットを使用して、作成されたジョブの状態をクエリできます。

    ```powershell   
    PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment `
    >>     -ResourceGroupName $RGName `
    >>     -TemplateFile $templateFile `
    >>     -TemplateParameterFile $templateParameterFile `
    >>     -Name "Deployment2" `
    >>     -AsJob
     
    Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
    --     ----            -------------   -----         -----------     --------             -------
    2      Long Running... AzureLongRun... Running       True            localhost            New-AzureRmResourceGro...
     
    PS C:\WINDOWS\system32> Get-Job -Id 2
     
    Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
    --     ----            -------------   -----         -----------     --------             -------
    2      Long Running... AzureLongRun... Completed     True            localhost            New-AzureRmResourceGro...
    ```

7. VM が正常にプロビジョニングされているかどうかを確認します。 次のコマンドを実行します。

    `Get-AzureRmVm`


## <a name="connect-to-a-vm"></a>VM への接続

Windows と Linux のどちらの VM を作成したかによって、接続する手順が異なる場合があります。

### <a name="connect-to-windows-vm"></a>Windows VM への接続

Windows VM に接続するには、これらの手順に従います。

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-windows.md)]

### <a name="connect-to-linux-vm"></a>Linux VM への接続

Linux VM に接続するには、これらの手順に従います。

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-linux.md)]

<!--## Manage VM

The following section describes some of the common operations around the VM that you will create on your Azure Stack Edge Pro device.

[!INCLUDE [azure-stack-edge-gateway-manage-vm](../../includes/azure-stack-edge-gateway-manage-vm.md)]-->


## <a name="supported-vm-sizes"></a>サポートされる VM のサイズ

[!INCLUDE [azure-stack-edge-gateway-supported-vm-sizes](../../includes/azure-stack-edge-gateway-supported-vm-sizes.md)]

## <a name="unsupported-vm-operations-and-cmdlets"></a>サポートされていない VM 操作およびコマンドレット

拡張機能、スケール セット、可用性セット、スナップショットはサポートされていません。

<!--## Configure AzCopy

When you install the latest version of AzCopy, you will need to configure AzCopy to ensure that it matches the blob storage REST API version of your Azure Stack Edge Pro device.

On the client used to access your Azure Stack Edge Pro device, set up a global variable to match the blob storage REST API version.

### On Windows client 

`$Env:AZCOPY_DEFAULT_SERVICE_API_VERSION = "2017-11-09"`

### On Linux client

`export AZCOPY_DEFAULT_SERVICE_API_VERSION=2017-11-09`

To verify if the environment variable for AzCopy was set correctly, take the following steps:

1. Run "azcopy env".
2. Find `AZCOPY_DEFAULT_SERVICE_API_VERSION` parameter. This should have the value you set in the preceding steps.-->


## <a name="next-steps"></a>次のステップ

[Azure Resource Manager コマンドレット](https://docs.microsoft.com/powershell/module/azurerm.resources/?view=azurermps-6.13.0)
