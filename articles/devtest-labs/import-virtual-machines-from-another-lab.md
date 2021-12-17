---
title: 別のラボから仮想マシンをインポートする
description: Azure DevTest Labs の別のラボに、あるラボから仮想マシンをインポートする方法について説明します。
ms.topic: how-to
ms.date: 11/08/2021
ms.openlocfilehash: 50f17183caa7da86ec6704af35129ed8bd707d5a
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2021
ms.locfileid: "132059849"
---
# <a name="import-virtual-machines-from-one-lab-to-another"></a>あるラボから別のラボに仮想マシンをインポートする

この記事では、仮想マシン (VM) を DevTest Labs ラボから別のラボにインポートする方法について説明します。

## <a name="scenarios"></a>シナリオ
一部のシナリオでは、あるラボから別のラボに VM をインポートする必要があります。

- 個人があるグループから別のグループに移動するので、新しいチームのラボに開発者のデスクトップを持ち込むことを希望しています。
- グループは[サブスクリプションレベルのクォータ](../azure-resource-manager/management/azure-subscription-service-limits.md)に達し、チームを複数の Azure サブスクリプションに分割することにしました。
- 会社は Azure ExpressRoute またはいくつかその他の新しいネットワーク トポロジに移行しており、チームはこの新しいインフラストラクチャへの VM の移動を希望しています。

## <a name="requirements-and-constraints"></a>要件と制約

インポート プロセスでは、ソース ラボからインポート先ラボに VM がインポートされます。 必要に応じて、プロセス内の VM の名前を変更できます。 インポート処理には、ディスク、スケジュール、ネットワーク設定などのすべての依存関係が含まれています。

このプロセスは、移動操作ではなくコピー操作であり、時間がかかる場合があります。 インポート時間は、次の要因によって一部異なります。

- ソース マシンに接続されているディスクの数とサイズ
- ソース リージョンと宛先リージョン間の距離

インポートが完了すると、プロセスによってソース VM がシャットダウンされ、宛先ラボで実行されている新しい VM が開始されます。

ラボ間で VM をインポートするには、いくつかの要件と制約があります。

- サブスクリプションやリージョンが異なる VM をインポートすることはできますが、どちらのサブスクリプションにも、同じ Azure Active Directory テナントが関連付けられている必要があります。
- VM がインポート元のラボで要求可能な状態にない必要があります。
- あなたが、インポート元とインポート先の両方のラボで VM の所有者であること。
- 現在のところ、この機能がサポートされているのは、PowerShell と REST API を介してのみ。

## <a name="use-powershell-to-import-one-or-all-lab-vms"></a>PowerShell を使用して 1 つまたはすべてのラボ VM をインポートする

[ImportVirtualMachines.ps1](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImportVirtualMachines) をダウンロードして実行します。 スクリプトを使用し、インポート元のラボの 1 つまたは全部の VM をインポート先のラボにインポートできます。

この PowerShell スクリプトを実行するには、インポート元とインポート先のサブスクリプションとラボ、およびインポート元の VM を特定します。 必要に応じて、インポート元となる VM の新しい名前を指定します。

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source lab>"`
                            -SourceDevTestLabName "<Name of the source lab>"`
                            -SourceVirtualMachineName "<Name of the VM to import from the source lab>" `
                            -DestinationSubscriptionId "<ID of the subscription that contains the destination lab>"`
                            -DestinationDevTestLabName "<Name of the destination lab>"`
                            -DestinationVirtualMachineName "<Optional: specify a new name for the imported VM in the destination lab>"
```
インポート元の VM を指定しない場合、スクリプトはインポート元のラボ内のすべての VM を自動的にインポートします。

## <a name="use-http-rest-to-import-a-vm"></a>HTTP REST を使用し、VM をインストールする

REST の呼び出しは簡単です。 インポート元のリソースとインポート先のリソースを特定するために情報を指定します。 この操作はインポート先のラボのリソースで行われます。

```http
POST https://management.azure.com/subscriptions/<DestinationSubscriptionID>/resourceGroups/<DestinationResourceGroup>/providers/Microsoft.DevTestLab/labs/<DestinationLab>/ImportVirtualMachine?api-version=2017-04-26-preview
{
   sourceVirtualMachineResourceId: "/subscriptions/<SourceSubscriptionID>/resourcegroups/<SourceResourceGroup>/providers/microsoft.devtestlab/labs/<SourceLab>/virtualmachines/<NameofVMTobeImported>",
   destinationVirtualMachineName: "<NewNameForImportedVM>"
}
```

## <a name="next-steps"></a>次の手順

- [ラボのポリシーを設定する](devtest-lab-set-lab-policy.md)
- [DevTest Labs についてよく寄せられる質問](devtest-lab-faq.yml)
