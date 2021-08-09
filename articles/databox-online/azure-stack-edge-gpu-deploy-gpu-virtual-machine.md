---
title: Azure Stack Edge Pro GPU デバイス上の GPU VM の概要とデプロイ
description: テンプレートを使用して、Azure Stack Edge Pro GPU デバイスに GPU 仮想マシン (VM) を作成し、管理する方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 05/28/2021
ms.author: alkohli
ms.openlocfilehash: 754cb296d6edebe4a8026df612fc52113e171a1c
ms.sourcegitcommit: df574710c692ba21b0467e3efeff9415d336a7e1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/28/2021
ms.locfileid: "110663878"
---
# <a name="deploy-gpu-vms-on-your-azure-stack-edge-pro-gpu-device"></a>Azure Stack Edge Pro GPU デバイス上で GPU VM をデプロイする

[!INCLUDE [applies-to-GPU-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

この記事では、Azure Stack Edge Pro GPU デバイス上の GPU 仮想マシン (VM) の概要を示します。 この記事では、Azure Resource Manager テンプレートを使用して GPU VM を作成する方法についても説明します。 


## <a name="about-gpu-vms"></a>GPU VM について

お使いの Azure Stack Edge デバイスには、Nvidia の Tesla T4 GPU が 1 つまたは 2 つ搭載されている場合があります。 これらのデバイスに GPU 高速 VM ワークロードをデプロイするには、GPU 最適化 VM サイズを使用します。 たとえば、T4 GPU を利用する推論ワークロードをデプロイするには、NC T4 v3 シリーズを使用する必要があります。 

詳細については、[NC T4 v3-シリーズ VM](../virtual-machines/nct4-v3-series.md) に関するページを参照してください。

## <a name="supported-os-and-gpu-drivers"></a>サポートされている OS ドライバーと GPU ドライバー 

Azure N シリーズ VM の GPU 機能を利用するには、Nvidia GPU ドライバーをインストールする必要があります。 

Nvidia GPU ドライバー拡張機能により、適切な Nvidia CUDA ドライバーまたは GRID ドライバーがインストールされます。 この拡張機能は、Azure Resource Manager テンプレートを使用してインストールしたり管理したりできます。

### <a name="supported-os-for-gpu-extension-for-windows"></a>Windows 用の GPU 拡張機能でサポートされている OS

この拡張機能は、次のオペレーティング システム (OS) に対応しています。 その他のバージョンも機能する可能性がありますが、Azure Stack Edge デバイス上で実行されている GPU VM 上で社内テストされていません。

| Distribution | Version |
|---|---|
| Windows Server 2019 | コア |
| Windows Server 2016 | コア |

### <a name="supported-os-for-gpu-extension-for-linux"></a>Linux 用の GPU 拡張でサポートされている OS

この拡張機能では、特定の OS バージョンのドライバー サポートに応じて、次の OS ディストリビューションがサポートされています。 その他のバージョンも機能する可能性がありますが、Azure Stack Edge デバイス上で実行されている GPU VM 上で社内テストされていません。


| Distribution | Version |
|---|---|
| Ubuntu | 18.04 LTS |
| Red Hat Enterprise Linux | 7.4 |


## <a name="gpu-vms-and-kubernetes"></a>GPU VM と Kubernetes

お使いのデバイス上で Kubernetes が構成されている場合は、デバイス上に GPU VM をデプロイする前に、次の考慮事項を確認してください。

#### <a name="for-1-gpu-device"></a>1 GPU デバイスの場合 

- **1 つの GPU VM を作成してから、デバイスで Kubernetes を構成します**。このシナリオでは、GPU VM の作成と Kubernetes の構成の両方が正常に実行されます。 この場合、Kubernetes からは GPU にアクセスできません。

- **デバイスで Kubernetes を構成してから、1 つの GPU VM を作成します**。このシナリオでは、Kubernetes によってデバイス上の GPU が要求され、使用可能な GPU リソースがないために VM の作成は失敗します。

#### <a name="for-2-gpu-device"></a>2 GPU デバイスの場合

- **1 つの GPU VM を作成してから、デバイスで Kubernetes を構成します**。このシナリオでは、作成した GPU VM によってデバイス上の 1 つの GPU が要求され、Kubernetes の構成も正常に完了して、残りの 1 つの GPU が要求されます。 

- **2 つの GPU VM を作成してらか、デバイスで Kubernetes を構成します**。このシナリオでは、GPU VM によってデバイス上の 2 つの GPU が要求され、Kubernetes は GPU なしで正常に構成されます。 

- **デバイスで Kubernetes を構成してから、1 つの GPU VM を作成します**。このシナリオでは、Kubernetes によってデバイス上の両方の GPU が要求され、使用可能な GPU リソースがないために VM の作成は失敗します。

<!--Li indicated that this is fixed. If you have GPU VMs running on your device and Kubernetes is also configured, then anytime the VM is deallocated (when you stop or remove a VM using Stop-AzureRmVM or Remove-AzureRmVM), there is a risk that the Kubernetes cluster will claim all the GPUs available on the device. In such an instance, you will not be able to restart the GPU VMs deployed on your device or create GPU VMs. -->


## <a name="create-gpu-vms"></a>GPU VM の作成

デバイスに GPU VM をデプロイする場合は、次の手順に従います。

1. デバイスで Kubernetes も実行されるかどうかを確認します。 デバイスで Kubernetes が実行される場合は、最初に GPU VM を作成してから、Kubernetes を構成する必要があります。 最初に Kubernetes が構成されている場合は、使用可能なすべての GPU リソースが要求され、GPU VM の作成は失敗します。

1. クライアント コンピューターに [VM テンプレートとパラメーター ファイルをダウンロード](https://aka.ms/ase-vm-templates)します。 それを、作業ディレクトリとして使用するディレクトリに解凍します。

1. Azure Stack Edge デバイスに VM を展開する前に、Azure PowerShell 経由で Azure Resource Manager を介してデバイスに接続するようにクライアントを構成する必要があります。 詳細な手順については、「[Azure Stack Edge デバイスで Azure Resource Manager に接続する](azure-stack-edge-gpu-connect-resource-manager.md)」を参照してください。

1. GPU VM を作成するには、次の相違点を除き、「[テンプレートを使用して Azure Stack Edge Pro GPU デバイスに VM をデプロイする](azure-stack-edge-gpu-deploy-virtual-machine-templates.md)」または「[Azure portal を使用して Azure Stack Edge Pro GPU デバイスに VM をデプロイする](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)」の手順をすべて実行します。 

            
    1. テンプレートを使用して VM を作成する場合、GPU VM のサイズを指定するときは、GPU VM でサポートされている NCasT4-v3 シリーズを `CreateVM.parameters.json` 内で使用してください。 詳細については、[GPU VM でサポートされている VM サイズ](azure-stack-edge-gpu-virtual-machine-sizes.md#ncast4_v3-series-preview)に関するページを参照してください。

        ```json
            "vmSize": {
          "value": "Standard_NC4as_T4_v3"
        },
        ```
        Azure portal を使用して VM を作成する場合でも、NCasT4-v3 シリーズから VM サイズを選択できます。

    1. GPU VM が正常に作成されると、Azure portal の Azure Stack Edge リソース内の仮想マシンの一覧にこの VM が表示されます。

        ![Azure portal 内の仮想マシンの一覧の GPU VM](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/list-virtual-machine-1.png)

1. VM を選択し、詳細にドリルダウンします。 VM に割り当てられている IP をコピーします。

    ![Azure portal 内の GPU VM に割り当てられている IP](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/get-ip-gpu-virtual-machine-1.png)

1. 必要に応じて、コンピューティング ネットワークを任意のものに切り替えることができます。 


VM が作成されたら、拡張機能テンプレートを使用して、GPU 拡張機能をデプロイできます。


> [!NOTE]
> デバイス ソフトウェアのバージョンを 2012 からそれ以降に更新する場合は、GPU VM を手動で停止する必要があります。



## <a name="next-steps"></a>次の手順

- お使いのデバイス上で実行されている GPU VM 上に [GPU 拡張機能をインストール](azure-stack-edge-gpu-deploy-virtual-machine-install-gpu-extension.md)する方法について説明します。
