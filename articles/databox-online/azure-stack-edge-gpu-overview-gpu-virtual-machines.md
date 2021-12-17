---
title: Azure Stack Edge Pro GPU デバイス上の GPU VM の概要
description: GPU 搭載の Azure Stack Edge Pro 上の GPU 高速ワークロード用に最適化された仮想マシンについて説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 07/13/2021
ms.author: alkohli
ms.openlocfilehash: d8660ace369fe0da0da384dceae6bff35594df74
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121779557"
---
# <a name="gpu-virtual-machines-for-azure-stack-edge-pro-gpu-devices"></a>Azure Stack Edge Pro GPU デバイス用の GPU 仮想マシン

[!INCLUDE [applies-to-GPU-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

Azure Stack Edge Pro GPU デバイス上の GPU 高速ワークロードには、GPU 仮想マシンが必要です。 この記事では、サポートされている OS、GPU ドライバー、VM サイズなど、GPU VM の概要について説明します。 Kubernetes クラスターで使用される GPU VM のデプロイ オプションについても説明します。

## <a name="about-gpu-vms"></a>GPU VM について

お使いの Azure Stack Edge デバイスには、Nvidia の Tesla T4 GPU が 1 つまたは 2 つ搭載されている場合があります。 これらのデバイスに GPU 高速 VM ワークロードをデプロイするには、GPU 最適化 VM サイズを使用します。 たとえば、T4 GPU を利用する推論ワークロードをデプロイするには、NC T4 v3 シリーズを使用する必要があります。 詳細については、[NC T4 v3-シリーズ VM](../virtual-machines/nct4-v3-series.md) に関するページを参照してください。

Azure N シリーズ VM の GPU 機能を利用するには、Nvidia GPU ドライバーをインストールする必要があります。 Nvidia GPU ドライバー拡張機能により、適切な Nvidia CUDA ドライバーまたは GRID ドライバーがインストールされます。 [GPU 拡張機能は、テンプレートを使用するか Azure portal を介してインストール](#gpu-vm-deployment)できます。

VM のデプロイ後に、[Azure Resource Manager テンプレートを使用してこの拡張機能のインストールと管理](azure-stack-edge-gpu-deploy-virtual-machine-install-gpu-extension.md)を行うことができます。 Azure portal では、VM のデプロイ中またはその後に GPU 拡張機能をインストールできます。手順については、[Azure Stack Edge デバイスへの GPU VM のデプロイ](azure-stack-edge-gpu-deploy-gpu-virtual-machine.md)に関する記事を参照してください。

デバイスに Kubernetes クラスターが構成されている場合は、GPU VM をデプロイする前に、[Kubernetes クラスターのデプロイに関する考慮事項](#gpu-vms-and-kubernetes)に関するページを確認してください。

## <a name="supported-os-and-gpu-drivers"></a>サポートされている OS ドライバーと GPU ドライバー 

Windows および Linux 用の Nvidia GPU ドライバー拡張機能では、次の OS バージョンがサポートされています。

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

## <a name="gpu-vm-deployment"></a>GPU VM のデプロイ

GPU VM は、Azure portal を介して、または Azure Resource Manager テンプレートを使用してデプロイできます。 GPU 拡張機能は、VM の作成後にインストールされます。<!--Wording still needs work!-->

- **ポータル:** Azure portal 内で、[VM を作成するとき](azure-stack-edge-gpu-deploy-gpu-virtual-machine.md#create-gpu-vms)または [VM のデプロイ後]()に GPU 拡張機能をすばやくインストールできます。<!--Can they remove the GPU extension. Tomorrow, create a new GPU VM to test.-->

- **テンプレート:** Azure Resource Manager テンプレートを使用して、[VM を作成](azure-stack-edge-gpu-deploy-gpu-virtual-machine.md#install-gpu-extension-after-deployment)し、[GPU 拡張機能をインストール](azure-stack-edge-gpu-deploy-virtual-machine-install-gpu-extension.md)します。


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

## <a name="next-steps"></a>次のステップ
- [GPU VM のデプロイ](azure-stack-edge-gpu-deploy-gpu-virtual-machine.md)方法を学習する。
- お使いのデバイス上で実行されている GPU VM 上に [GPU 拡張機能をインストール](azure-stack-edge-gpu-deploy-virtual-machine-install-gpu-extension.md)する方法について説明します。
