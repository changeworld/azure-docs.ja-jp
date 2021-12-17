---
title: Azure Stack Edge デバイス上の VM の概要
description: Azure Stack Edge デバイス上の仮想マシンについて説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 07/09/2021
ms.author: alkohli
ms.openlocfilehash: 73cb44dd3564f56f8edd5304a344a50f1e4b5c9d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121740759"
---
# <a name="virtual-machines-on-your-azure-stack-edge-pro-gpu-device"></a>Azure Stack Edge Pro GPU デバイス上の仮想マシン

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

この記事では、Azure Stack Edge デバイスで実行される仮想マシン (VM) と、サポートされている VM サイズについて概要を説明します。また、VM イメージを作成、デプロイし、その後 VM を管理するさまざまな方法の要約を示します。 

## <a name="about-vms"></a>VM について

Azure Stack Edge ソリューションでは、Microsoft 製の専用デバイスが "サービスとしてのハードウェア" の形態で提供されます。これらのデバイスを使用して、エッジ コンピューティングのワークロードをデプロイしたり、迅速なアクションにつながる分析情報をデータの生成場所であるエッジで入手したりできます。 

環境に応じて、また実行しているアプリケーションの種類に応じて、次のエッジ コンピューティング ワークロードのいずれかをこれらのデバイスにデプロイできます。 

- **コンテナー化** - IoT Edge または Kubernetes を使用してコンテナー化アプリケーションを実行します。
- **非コンテナー化** - Windows と Linux 両方の仮想マシンをデバイスにデプロイして、コンテナー化されていないアプリケーションを実行します。 

コンピューティング環境をより細かく制御する必要がある場合は、デバイスに VM をデプロイします。 デバイス上の VM は、開発やテストから、エッジでのアプリケーションの実行まで、さまざまな様態で使用できます。

## <a name="before-you-create-a-vm"></a>VM を作成する前に

開始する前に、VM に関する次の考慮事項を確認してください。

- 使用する VM のサイズ。
- デバイス上に作成できる VM の最大数。
- VM で実行されるオペレーティング システム。
- 開始した後の VM の構成。


### <a name="vm-size"></a>VM サイズ

VM をデプロイすることを計画している場合は、VM のサイズに注意する必要があります。 VM には、デバイス上でアプリやワークロードを実行するために使用できる複数のサイズがあります。 さらに、選択したサイズによって、処理能力、メモリ、ストレージの容量などの要素が決まります。 詳細については、「[サポートされる VM のサイズ](azure-stack-edge-gpu-virtual-machine-sizes.md#supported-vm-sizes)」を参照してください。

デバイスにデプロイできる VM のサイズと数を算出するには、デバイスで使用可能なコンピューティングと、実行している他のワークロードを考慮します。 Kubernetes を実行している場合は、Kubernetes のマスター VM とワーカー VM のコンピューティング要件も考慮します。

|Kubernetes VM の種類|CPU とメモリの要件|
|---------|---------|
|マスター VM|4 コア、4 GB RAM|
|ワーカー VM|12 コア、32 GB RAM|

デバイスで使用可能なコンピューティングとメモリについては、使用しているデバイス モデルの「[コンピューティングとメモリの仕様](azure-stack-edge-gpu-technical-specifications-compliance.md#compute-and-memory-specifications)」を参照してください。 

GPU 仮想マシンの場合は、[NCasT4-v3 シリーズの VM サイズ](azure-stack-edge-gpu-virtual-machine-sizes.md#ncast4_v3-series-preview)を使用する必要があります。


### <a name="vm-limits"></a>VM の制限

デバイスで実行できる VM は最大 24 個です。 これは、ワークロードをデプロイする際に考慮する必要があるもう 1 つの要因です。

### <a name="operating-system-disks-and-images"></a>オペレーティング システム ディスクおよびイメージ

デバイスでは、固定仮想ハード ディスク (VHD) 形式の第 1 世代 VM のみを使用できます。 VHD を使用して、コンピューターのオペレーティング システム (OS) とデータを格納します。 VHD は、OS をインストールするためのイメージにも使用されます。 

VM イメージの作成に使用するイメージは、一般化または特殊化できます。 VM のイメージを作成するときは、イメージを準備する必要があります。 デバイスで VM イメージを準備して使用するためのさまざまな方法を確認してください。

- [Windows の一般化されたイメージを VHD から準備する](azure-stack-edge-gpu-prepare-windows-vhd-generalized-image.md)
- [一般化されたイメージを ISO から準備する](azure-stack-edge-gpu-prepare-windows-generalized-image-iso.md)
- [Azure VM から開始してカスタム VM イメージを作成する](azure-stack-edge-gpu-create-virtual-machine-image.md)
- [特殊化されたイメージを使用する](azure-stack-edge-gpu-deploy-vm-specialized-image-powershell.md)

### <a name="extensions"></a>の拡張

デバイス上の VM に対して、カスタム スクリプト拡張機能を使用できます。これは、VM のプロビジョニング時にスクリプトを実行してワークロードを構成するのに役立ちます。

詳細については、[デバイスで実行されている VM へのカスタム スクリプト拡張機能のデプロイ](azure-stack-edge-gpu-deploy-virtual-machine-custom-script-extension.md)に関するページを参照してください。

GPU VM のプロビジョニング時に GPU ドライバーをインストールする場合、GPU 拡張機能を VM に使用することもできます。 詳細については、「[GPU VM の作成](azure-stack-edge-gpu-deploy-gpu-virtual-machine.md#create-gpu-vms)」および「[GPU 拡張機能のインストール](azure-stack-edge-gpu-deploy-virtual-machine-install-gpu-extension.md)」を参照してください。

## <a name="create-a-vm"></a>VM の作成

VM をデプロイするには、まず、VM の作成に必要なすべてのリソースを作成する必要があります。 VM の作成に使用する方法に関係なく、次の手順を実行します。 

1. デバイスのローカル Azure Resource Manager に接続します。 
1. デバイス上の組み込みサブスクリプションを特定します。
1. VM イメージを用意します。
    1. 組み込みサブスクリプションにリソース グループを作成します。 このリソース グループに、VM とすべての関連リソースが含まれます。
    2. VM イメージの作成に使用する VHD を格納するためのローカル ストレージ アカウントをデバイス上に作成します。
    3. Windows/Linux ソース イメージをストレージ アカウントにアップロードして、マネージド ディスクを作成します。
    4. マネージド ディスクを使用して VM イメージを作成します。
1. デバイス ポートでコンピューティングを有効にして、仮想スイッチを作成します。
    1. これにより、コンピューティングを有効にしたポートに接続された仮想スイッチを使用して仮想ネットワークが作成されます。  
1. VM を作成します。これには、以前に作成した VM イメージ、仮想ネットワーク、および、仮想ネットワーク内で通信するための仮想ネットワーク インターフェイスを使用します。次に、リモートから VM にアクセスするためのパブリック IP アドレスを割り当てます。 必要に応じて、VM により多くのストレージを提供するためにデータ ディスクを含ます。
 
次の図にデプロイのワークフローを示します。

![VM デプロイ ワークフローの図。](media/azure-stack-edge-gpu-deploy-virtual-machine-powershell/vm-workflow-r.svg)

デバイスに VM をデプロイするには、いくつかの方法があります。 どれを選ぶかは環境によって異なります。 次の表は、デバイスに VM をデプロイするさまざまな方法をまとめたものです。

|Method|[アーティクル]|
|---------|---------|
|Azure portal|[Azure portal を使用してデバイスに VM をデプロイする](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)|
|テンプレート|[テンプレートを使用してデバイスに VM をデプロイする](azure-stack-edge-gpu-deploy-virtual-machine-templates.md)|
|PowerShell|[Azure PowerShell コマンドレットを使用してデバイスに VM をデプロイする](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md)<br>[Azure PowerShell スクリプトを使用してデバイスに VM をデプロイする](azure-stack-edge-gpu-deploy-virtual-machine-powershell-script.md)|
|CLI/Python|[Azure CLI/Python を使用してデバイスに VM をデプロイする](azure-stack-edge-gpu-deploy-virtual-machine-cli-python.md)|
|GPU|[GPU を使用してデバイスに VM をデプロイする](azure-stack-edge-gpu-deploy-gpu-virtual-machine.md)|


## <a name="manage-your-vm"></a>VM の管理

デバイス上の VM は、Azure portal から、デバイスの PowerShell インターフェイスから、または API を使用して直接、のいずれかの方法で管理できます。 一般的な管理タスクは次のとおりです。

- VM に関する情報を取得します。
- VM に接続し、VM を起動、停止、削除します。
- ディスク、VM サイズ、ネットワーク インターフェイス、仮想スイッチを管理します。
- VM ディスクをバックアップします。

### <a name="get-information-about-your-vm"></a>VM に関する情報を取得する

Azure portal から VM の詳しい情報を取得するには、次の手順を実行します。

1. デバイスの Azure Stack Edge リソースにアクセスし、 **[仮想マシン] > [概要]** を選択します。 
1. **[概要]** ページの **[仮想マシン]** にアクセスして、目的の仮想マシンを選択します。 その後、VM の詳細を表示できます。 

### <a name="connect-to-your-vm"></a>VM に接続する

VM が実行されている OS に応じて、次のようにして VM に接続できます。 

- [デバイス上の Windows VM に接続します](azure-stack-edge-gpu-deploy-virtual-machine-templates.md#connect-to-windows-vm)。
- [デバイス上の Linux VM に接続します](azure-stack-edge-gpu-deploy-virtual-machine-templates.md#connect-to-linux-vm)。

### <a name="start-stop-delete-vms"></a>VM の起動、停止、削除

VM の[電源オン](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#turn-on-the-vm)、[一時停止またはシャットダウン](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#suspend-or-shut-down-the-vm)を実行できます。 最後に、VM の使用を終了したら、[VM を削除](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#delete-the-vm)できます。

### <a name="manage-network-interfaces-virtual-switches"></a>ネットワーク インターフェイスと仮想スイッチの管理

VM の[ネットワーク インターフェイスの追加、変更、デタッチ](azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal.md)を実行できます。 デバイス上に[新しい仮想スイッチを作成](azure-stack-edge-gpu-create-virtual-switch-powershell.md)して VM をデプロイすることもできます。 

### <a name="manage-data-disks-vm-size"></a>データ ディスクと VM サイズの管理

Azure portal を使用して、[既存の VM へのデータ ディスクの追加](azure-stack-edge-gpu-manage-virtual-machine-disks-portal.md#add-a-data-disk)、[既存のディスクの接続](azure-stack-edge-gpu-manage-virtual-machine-disks-portal.md#add-a-data-disk)、[データ ディスクのデタッチ](azure-stack-edge-gpu-manage-virtual-machine-disks-portal.md#detach-a-data-disk)、そして最後に [VM 自体のサイズ変更](azure-stack-edge-gpu-manage-virtual-machine-resize-portal.md#resize-a-vm)を実行できます。

### <a name="back-up-vms"></a>VM をバックアップする

VM ディスクをバックアップし、デバイスで障害が発生した場合にバックアップからデータを復元することができます。 詳細については、[VM ディスクのバックアップ](azure-stack-edge-gpu-back-up-virtual-machine-disks.md)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

- [Azure Stack Edge Pro GPU 用の VM のサイズと種類](azure-stack-edge-gpu-virtual-machine-sizes.md)について理解します。


