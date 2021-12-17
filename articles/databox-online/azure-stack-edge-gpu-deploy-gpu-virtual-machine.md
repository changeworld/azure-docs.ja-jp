---
title: Azure Stack Edge Pro GPU デバイス上で GPU VM をデプロイする
description: Azure portal 経由またはテンプレートを使用して、Azure Stack Edge Pro GPU に GPU 仮想マシン (VM) を作成し、デプロイする方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/03/2021
ms.author: alkohli
ms.openlocfilehash: 958c87a78551555d2994c37e2b72c75cb989a834
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121740836"
---
# <a name="deploy-gpu-vms-on-your-azure-stack-edge-pro-gpu-device"></a>Azure Stack Edge Pro GPU デバイス上で GPU VM をデプロイする

[!INCLUDE [applies-to-GPU-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

この記事では、Azure portal で、または Azure Resource Manager テンプレートを使用して GPU VM を作成する方法について説明します。

Azure portal を使用して単一の GPU VM を迅速にデプロイします。 VM の作成時または作成後に GPU 拡張機能をインストールできます。 または、Azure Resource Manager テンプレートを使用して、複数の GPU VM を効率的にデプロイおよび管理します。

## <a name="create-gpu-vms"></a>GPU VM の作成

GPU VM は、ポータルを介して、または Azure Resource Manager テンプレートを使用してデプロイできます。

GPU VM でサポートされているオペレーティング システム、ドライバー、VM サイズの一覧については、[GPU 仮想マシン](azure-stack-edge-gpu-overview-gpu-virtual-machines.md)に関するページを参照してください。 デプロイに関する考慮事項については、[GPU VM と Kubernetes](azure-stack-edge-gpu-overview-gpu-virtual-machines.md#gpu-vms-and-kubernetes) に関するページを参照してください。


> [!IMPORTANT]
> デバイスで Kubernetes が実行される場合は、GPU VM をデプロイする前に Kubernetes を構成しないでください。 最初に Kubernetes を構成すると、使用可能なすべての GPU リソースが要求され、GPU VM の作成は失敗します。 1 GPU および 2 GPU デバイスでの Kubernetes のデプロイに関する考慮事項については、[GPU VM と Kubernetes](azure-stack-edge-gpu-overview-gpu-virtual-machines.md#gpu-vms-and-kubernetes) に関するページを参照してください。

### <a name="portal"></a>[ポータル](#tab/portal)

Azure portal 経由でデバイスに GPU VM をデプロイする場合は、次の手順に従います。

1. GPU VM を作成するには、次の構成要件に従って、[Azure portal を使用して Azure Stack Edge に VM をデプロイする](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)方法に関するページのすべての手順に従います。

    - [[基本]](azure-stack-edge-gpu-virtual-machine-sizes.md#ncast4_v3-series-preview) タブで、**NCasT4-v3 シリーズの VM サイズ** を選択します。

       ![Azure Stack Edge の [仮想マシンの追加] の [基本] タブのスクリーンショット。 GPU VM でサポートされている VM サイズを含む [サイズ] オプションが強調表示されています。](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/basics-vm-size-for-gpu.png)

    - デプロイ中に GPU 拡張機能をインストールするには、 **[詳細設定]** タブで、 **[インストールする拡張機能を選択します]** を選択します。 次に、インストールする GPU 拡張機能を選択します。 GPU 拡張機能は、[NCasT4-v3 シリーズの VM サイズ](azure-stack-edge-gpu-virtual-machine-sizes.md#ncast4_v3-series-preview)の仮想マシンでのみ使用できます。
        
        > [!NOTE]
        > Red Hat イメージを使用している場合は、VM のデプロイ後に GPU 拡張機能をインストールする必要があります。 [GPU 拡張機能のインストール](azure-stack-edge-gpu-deploy-virtual-machine-install-gpu-extension.md)に関するページの手順に従います。
    
       ![[仮想マシンの追加] の [詳細設定] タブに GPU 拡張機能を追加するための手順の図。 拡張機能を選択して追加するためのオプションが強調表示されています。](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/add-extension-01.png)

       **[詳細設定]** タブには、選択した拡張機能が表示されます。

       ![[仮想マシンの追加] の [詳細設定] タブのスクリーンショット。 インストールされている GPU 拡張機能が強調表示されています。](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/add-extension-02.png)

1. GPU VM が正常に作成されると、Azure portal の Azure Stack Edge リソース内の仮想マシンの一覧にこの VM が表示されます。

    ![Azure Stack Edge の [仮想マシン] ビューのスクリーンショット。新しく作成された GPU VM が強調表示されています。](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/list-virtual-machines-01.png)

    VM を選択し、詳細にドリルダウンします。 GPU 拡張機能の状態が **[成功]** になっていることを確認します。

    ![Azure Stack Edge VM の詳細ウィンドウのスクリーンショット。 インストールされている拡張機能と、インストールされている GPU 拡張機能が強調表示されています。](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/vm-details-extension-installed.png)


### <a name="templates"></a>[テンプレート](#tab/templates)

Azure Resource Manager テンプレートを使用して GPU VM をデバイスにデプロイする場合は、次の手順に従います。

1. クライアント コンピューターに [VM テンプレートとパラメーター ファイルをダウンロード](https://aka.ms/ase-vm-templates)します。 それを、作業ディレクトリとして使用するディレクトリに解凍します。

1. Azure Stack Edge デバイスに VM を展開する前に、Azure PowerShell 経由で Azure Resource Manager を介してデバイスに接続するようにクライアントを構成する必要があります。 詳細な手順については、「[Azure Stack Edge デバイスで Azure Resource Manager に接続する](azure-stack-edge-gpu-connect-resource-manager.md)」を参照してください。

1. GPU VM を作成するには、次の構成要件に従って、[テンプレートを使用して Azure Stack Edge に VM をデプロイする](azure-stack-edge-gpu-deploy-virtual-machine-templates.md)方法に関するページのすべての手順に従います。 
            
    - GPU VM のサイズを指定する場合は、GPU VM でサポートされている NCasT4-v3 シリーズを `CreateVM.parameters.json` で使用してください。 詳細については、[GPU VM でサポートされている VM サイズ](azure-stack-edge-gpu-virtual-machine-sizes.md#ncast4_v3-series-preview)に関するページを参照してください。

       ```json
           "vmSize": {
         "value": "Standard_NC4as_T4_v3"
       },
       ```

    GPU VM が正常に作成されると、Azure portal の Azure Stack Edge リソース内の仮想マシンの一覧にこの VM が表示されます。

    ![Azure Stack Edge の [仮想マシン] ビューのスクリーンショット。 新しく作成された GPU VM が強調表示されています。](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/list-virtual-machines-01.png)

1. VM を選択し、詳細にドリルダウンします。 VM に割り当てられている IP アドレスをコピーします。

    ![Azure Stack Edge 仮想マシンの詳細ウィンドウのスクリーンショット。 [ネットワーク] の下の [IP アドレス] が強調表示されています。](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/get-ip-of-virtual-machine.png)

VM が作成されたら、[拡張機能テンプレートを使用して、GPU 拡張機能をデプロイ](azure-stack-edge-gpu-deploy-virtual-machine-install-gpu-extension.md?tabs=linux)できます。

---

> [!NOTE]
> デバイス ソフトウェアのバージョンを 2012 からそれ以降に更新する場合は、GPU VM を手動で停止する必要があります。

## <a name="install-gpu-extension-after-deployment"></a>デプロイ後に GPU 拡張機能をインストールする

Azure N シリーズ VM の GPU 機能を利用するには、Nvidia GPU ドライバーをインストールする必要があります。 Azure portal から、VM のデプロイ中またはデプロイ後に GPU 拡張機能をインストールできます。 テンプレートを使用している場合は、VM の作成後に GPU 拡張機能をインストールします。

---

### <a name="portal"></a>[ポータル](#tab/portal)

VM の作成時に GPU 拡張機能をインストールしなかった場合は、次の手順に従ってデプロイされた VM にインストールします。

1. GPU 拡張機能を追加する仮想マシンに移動します。

    ![Azure Stack Edge デバイスの [仮想マシン] ビューのスクリーンショット。VM の一覧で仮想マシンが強調表示されています。](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/add-extension-after-deployment-01.png)
  
1. **[詳細]** で **[+ 拡張機能の追加]** を選択します。 次に、インストールする GPU 拡張機能を選択します。

    GPU 拡張機能は、[NCasT4-v3 シリーズの VM サイズ](azure-stack-edge-gpu-virtual-machine-sizes.md#ncast4_v3-series-preview)の仮想マシンでのみ使用できます。 必要に応じて、[デプロイ後に GPU 拡張機能をインストール](azure-stack-edge-gpu-deploy-gpu-virtual-machine.md#install-gpu-extension-after-deployment)できます。

![仮想マシンの [詳細] ウィンドウの [+ 拡張機能の追加] ボタンを使用して、Azure Stack Edge デバイス上の VM に GPU 拡張機能を追加する 2 つの手順を示す図。](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/add-extension-after-deployment-02.png)

> [!Note]
> ポータルを使用して GPU 拡張機能を削除することはできません。 代わりに、Azure PowerShell で [Remove-AzureRmVMExtension](/powershell/module/azurerm.compute/remove-azurermvmextension?view=azurermps-6.13.0&preserve-view=true) コマンドレットを使用します。 手順については、「[GPU 拡張機能の削除](azure-stack-edge-gpu-deploy-virtual-machine-install-gpu-extension.md#remove-gpu-extension)」を参照してください。

### <a name="templates"></a>[テンプレート](#tab/templates)

テンプレートを使用して GPU VM を作成する場合は、デプロイ後に GPU 拡張機能をインストールします。 テンプレートを使用して、Windows 仮想マシンまたは Linux 仮想マシンに GPU 拡張機能をデプロイする詳細な手順については、[GPU 拡張機能のインストール](azure-stack-edge-gpu-deploy-virtual-machine-install-gpu-extension.md)に関するページを参照してください。

---

## <a name="next-steps"></a>次のステップ

- [VM のデプロイのトラブルシューティング](azure-stack-edge-gpu-troubleshoot-virtual-machine-provisioning.md)
- [GPU 拡張機能の問題のトラブルシューティング](azure-stack-edge-gpu-troubleshoot-virtual-machine-gpu-extension-installation.md)
- [デバイスの VM アクティビティの監視](azure-stack-edge-gpu-monitor-virtual-machine-activity.md)
- [VM の CPU とメモリの監視](azure-stack-edge-gpu-monitor-virtual-machine-metrics.md)
