---
title: Azure Stack Edge Pro GPU デバイスに Azure Resource Manager パスワードを設定する
description: Azure PowerShell を使用して、Azure Stack Edge Pro GPU 上で実行されている Azure Resource Manager に接続する方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: df5fea8101834dae089ab97354c438363321a707
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/22/2020
ms.locfileid: "90904479"
---
# <a name="set-azure-resource-manager-password-on-azure-stack-edge-pro-gpu-device"></a>Azure Stack Edge Pro GPU デバイスに Azure Resource Manager パスワードを設定する

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

この記事では、Azure Resource Manager パスワードを設定する方法について説明します。 Azure Resource Manager を使用してデバイスのローカル API に接続する場合は、このパスワードを設定する必要があります。

パスワードを設定する手順は、Azure portal と PowerShell コマンドレットのどちらを使用するかによって異なります。 これらの各手順については、以下のセクションで説明します。


## <a name="reset-password-via-the-azure-portal"></a>Azure portal を使用してパスワードをリセットする

1. Azure portal で、デバイスを管理するために作成した Azure Stack Edge リソースに移動します。 **[Edge コンピューティング]、[開始]** の順に移動します。

2. 右側のペインのコマンド バーで、 **[Edge ARM パスワードのリセット]** を選択します。 

    ![EdgeARM ユーザーのパスワードのリセット 1](media/azure-stack-edge-j-series-set-azure-resource-manager-password/set-edgearm-password-1.png)

3. **[EdgeArm ユーザーのパスワードのリセット]** ブレードで、Azure Resource Manager を介してデバイスのローカル API に接続するためのパスワードを入力します。 パスワードを確認し、 **[リセット]** を選択します。

    ![EdgeARM ユーザーのパスワードのリセット 2](media/azure-stack-edge-j-series-set-azure-resource-manager-password/set-edgearm-password-2.png)



## <a name="reset-password-via-powershell"></a>PowerShell を使用してパスワードをリセットする

1. Azure Portal で、デバイスを管理するために作成した Azure Stack Edge リソースに移動します。 **[概要]** ページで、次のパラメーターをメモしておきます。

    - Azure Stack Edge のリソース名
    - サブスクリプション ID

2. **[設定] > [プロパティ]** に移動します。 **[プロパティ]** ページで、次のパラメーターをメモしておきます。

    - Resource group
    - CIK 暗号化キー:[表示] を選択し、 **[暗号化キー]** をコピーします。

    ![CIK 暗号化キーの取得](media/azure-stack-edge-j-series-set-azure-resource-manager-password/get-cik-portal.png)
 
3. Azure Resource Manager に接続するために使用するパスワードを確認します。

4. Cloud Shell を起動します。 右上隅にあるアイコンを選択します。

    ![Cloud Shell の起動](media/azure-stack-edge-j-series-set-azure-resource-manager-password/start-cloud-shell.png) 

    Cloud Shell が起動されたら、PowerShell に切り替える必要がある場合があります。

    ![Cloud Shell](media/azure-stack-edge-j-series-set-azure-resource-manager-password/cloud-shell.png)   


5. コンテキストを設定します。 型:

    `Set-AzContext -SubscriptionId <Subscription ID>`

    出力例を次に示します。

    
    ```azurepowershell
    PS Azure:\> Set-AzContext -SubscriptionId 8eb87630-972c-4c36-a270-f330e6c063df
    
        Name        Account   SubscriptionName   Environment  TenantId
        ----       -------    ----------------   -----------  --------
        DataBox_Edge_Test (8eb87630-972c-4c36-a… MSI@50342 DataBox_Edge_Tes AzureCloud           72f988bf-86f1-41af-91ab-2d7…
    
        PS Azure:/
    ```
    
5.  古い PS モジュールがある場合は、それらをインストールする必要があります。

    `Remove-Module  Az.DataBoxEdge -force`

    出力例を次に示します。 この例では、インストールする古いモジュールがありませんでした。

    
    ```azurepowershell
        PS Azure:\> Remove-Module  Az.DataBoxEdge -force
        Remove-Module : No modules were removed. Verify that the specification of modules to remove is correct and those modules exist in the runspace.
        At line:1 char:1
        + Remove-Module  Az.DataBoxEdge -force
        + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : ResourceUnavailable: (:) [Remove-Module], InvalidOperationException
        + FullyQualifiedErrorId : Modules_NoModulesRemoved,Microsoft.PowerShell.Commands.RemoveModuleCommand
    
        PS Azure:\
    ```

6. 次の一連のコマンドは、PowerShell モジュールをインストールするスクリプトをダウンロードして実行します。
    
    ```azurepowershell
        cd ~/clouddrive
        wget https://aka.ms/dbe-cmdlet-beta -O Az.DataBoxEdge.zip
        unzip ./Az.DataBoxEdge.zip
        Import-Module ~/clouddrive/Az.DataBoxEdge/Az.DataBoxEdge.psd1 -Force
    ```

7. 次の一連のコマンドでは、リソース名、リソース グループ名、暗号化キー、および前の手順で確認したパスワードを指定する必要があります。

    ```azurepowershell
    $devicename = "<Azure Stack Edge resource name>"
    $resourceGroup = "<Resource group name>"
    $cik = "<Encryption key>"
    $password = "<Password>"
    ```
    パスワードと暗号化キーのパラメーターは、セキュリティで保護された文字列として渡す必要があります。 パスワードと暗号化キーをセキュリティで保護された文字列に変換するには、次のコマンドレットを使用します。

    ```azurepowershell
    $pass = ConvertTo-SecureString $password -AsPlainText -Force
    $key = ConvertTo-SecureString $cik -AsPlainText -Force
    ```
    上記で生成したセキュリティで保護された文字列を Set-AzDataBoxEdgeUser コマンドレットのパラメーターとして使用し、パスワードをリセットします。 Azure Stack Edge Pro および Data Box Gateway リソースの作成時に使用したものと同じリソース グループを使用します。

    ```azurepowershell
    Set-AzDataBoxEdgeUser -ResourceGroupName $resourceGroup -DeviceName $devicename -Name EdgeARMUser  -Password $pass -EncryptionKey $key
    ```
    出力例を次に示します。
    
    ```azurepowershell
    PS /home/aseuser/clouddrive> $devicename = "myaseresource"
    PS /home/aseuser/clouddrive> $resourceGroup = "myaserg"
    PS /home/aseuser/clouddrive> $cik = "54a7450fd7b3c506e10efea4e0c88a9390f37e299fbf43e01fb5dfe483ac036b6d0f85a6246e1926e297f98c0ff84c20a57348c689eff179ce31571fc787ac0a"
    PS /home/aseuser/clouddrive> $password = "Password2"
    PS /home/aseuser/clouddrive> $pass = ConvertTo-SecureString $password -AsPlainText -Force
    PS /home/aseuser/clouddrive> $key = ConvertTo-SecureString $cik -AsPlainText -Force
    PS /home/aseuser/clouddrive> Set-AzDataBoxEdgeUser -ResourceGroupName $resourceGroup -DeviceName $devicename -Name EdgeARMUser  -Password $pass -EncryptionKey $key
    
        User name   Type ResourceGroupName DeviceName
    ---------   ---- ----------------- ----------
        EdgeARMUser ARM  myaserg        myaseresource
    
        PS /home/aseuser/clouddrive>
    ```
新しいパスワードを使用して Azure Resource Manager に接続します。

## <a name="next-steps"></a>次のステップ

[Azure Resource Manager に接続する](azure-stack-edge-j-series-connect-resource-manager.md)
