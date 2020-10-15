---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 08/31/2020
ms.author: alkohli
ms.openlocfilehash: 3a17e73c66c2296cc36b24e3b0a8abfcab00e46a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "89419405"
---
Azure Stack Edge デバイスに VM を展開する前に、Azure PowerShell 経由で Azure Resource Manager を介してデバイスに接続するようにクライアントを構成する必要があります。 詳細な手順については、「[Azure Stack Edge デバイスで Azure Resource Manager に接続する](../articles/databox-online/azure-stack-edge-j-series-connect-resource-manager.md)」を参照してください。


次の手順を使用してクライアントからデバイスにアクセスできることを確認します (Azure Resource Manager に接続する時点でこの構成は完了していますが、構成が正常に行われたことを確認します)。 

1. Azure Resource Manager の通信が機能していることを確認します。 型:     

    ```powershell
    Add-AzureRmEnvironment -Name <Environment Name> -ARMEndpoint "https://management.<appliance name>.<DNSDomain>"
    ```

1. ローカル デバイス API を呼び出して認証を行います。 型: 

    `login-AzureRMAccount -EnvironmentName <Environment Name>`

    Azure Resource Manager を介して接続するには、ユーザー名 (*EdgeARMuser*) とパスワードを入力します。

1. Kubernetes で**コンピューティング**を構成した場合は、この手順を省略できます。 続行して、コンピューティング用ネットワークを有効にしたことを確認します。 ローカル UI で、**コンピューティング**設定に移動します。 仮想スイッチの作成に使用するネットワーク インターフェイスを選択します。 作成する VM は、このポートおよび関連付けられているネットワークに接続されている仮想スイッチに接続されます。 VM に使用する IP アドレスと一致するネットワークを選択してください。  

    ![コンピューティング設定 1 を有効にする](../articles/databox-online/media/azure-stack-edge-gpu-deploy-virtual-machine-templates/enable-compute-setting.png)

    そのネットワーク インターフェイスでコンピューティングを有効にします。 そのネットワーク インターフェイスに対応する仮想スイッチが Azure Stack Edge によって作成および管理されます。 現時点では、Kubernetes に特定の IP アドレスを入力しないでください。 数分でコンピューティングが有効になります。

    <!--If you decide to use another network interface for compute, make sure that you:
    
    - Delete all the VMs that you have deployed using Azure Resource Manager.
    
    - Delete all virtual network interfaces and the virtual network associated with this network interface. 
    
    - You can now enable another network interface for compute.-->

<!--1. You may also need to configure TLS 1.2 on your client machine if running older versions of AzCopy.--> 

