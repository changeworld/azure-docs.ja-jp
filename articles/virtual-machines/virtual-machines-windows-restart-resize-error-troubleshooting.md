---
title: "VM の再起動またはサイズ変更に関する問題 | Microsoft Docs"
description: "Azure での既存の Windows 仮想マシンの再起動またはサイズ変更に関する Resource Manager デプロイメントの問題のトラブルシューティング"
services: virtual-machines-windows, azure-resource-manager
documentationcenter: 
author: Deland-Han
manager: felixwu
editor: 
tags: top-support-issue
ms.assetid: 0756b52d-4f5a-4503-ae45-c00a6a2edcdf
ms.service: virtual-machines-windows
ms.topic: support-article
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.workload: required
ms.date: 01/10/2017
ms.author: delhan
translationtype: Human Translation
ms.sourcegitcommit: 94c18aa0c4fe38fb74931d5ed61fece207c8b5ce
ms.openlocfilehash: afa8d2b76ab0f85b5eaf615b264af2f29936016c


---
# <a name="troubleshoot-resource-manager-deployment-issues-with-restarting-or-resizing-an-existing-windows-virtual-machine-in-azure"></a>Azure での既存の Windows 仮想マシンの再起動またはサイズ変更に関する Resource Manager デプロイメントの問題のトラブルシューティング
停止している Azure 仮想マシン (VM) を起動しようとしたとき、または既存の Azure VM のサイズを変更しようとしたときに発生する一般的なエラーは割り当てエラーです。 このエラーは、クラスターまたはリージョンに使用可能なリソースがないか、要求された VM サイズをサポートできない場合に発生します。

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="collect-activity-logs"></a>アクティビティ ログを収集する
トラブルシューティングを開始するには、アクティビティ ログを収集して問題に関連するエラーを特定します。 このプロセスの詳細については、次のリンクをご覧ください。

[デプロイ操作の表示](../azure-resource-manager/resource-manager-deployment-operations.md)

[アクティビティ ログを表示して Azure リソースを管理する](../azure-resource-manager/resource-group-audit.md)

## <a name="issue-error-when-starting-a-stopped-vm"></a>問題: 停止している VM の起動時のエラー
停止している VM を起動しようとしたときに、割り当てエラーが発生します。

### <a name="cause"></a>原因
停止している VM の起動要求は、クラウド サービスをホストしている元のクラスターで行う必要がありますが、 クラスターに要求の処理に使用できる空き領域がありません。

### <a name="resolution"></a>解決策
* 可用性セットのすべての VM を停止し、各 VM を再起動します。
  
  1. **[リソース グループ]** > *対象のリソース グループ* > **[リソース]** > *対象の可用性セット* > **[Virtual Machines]** > *対象の仮想マシン* > **[停止]** の順にクリックします。
  2. すべての VM が停止したら、停止している各 VM を選択し、[起動] をクリックします。
* 後で再起動要求を再試行します。

## <a name="issue-error-when-resizing-an-existing-vm"></a>問題: 既存の VM のサイズ変更時のエラー
既存の VM のサイズを変更しようとしたときに割り当てエラーが発生します。

### <a name="cause"></a>原因
VM のサイズ変更要求は、クラウド サービスをホストしている元のクラスターで行う必要がありますが、 クラスターが要求された VM サイズをサポートしていません。

### <a name="resolution"></a>解決策
* VM サイズを小さくして要求を再試行します。
* 要求した VM のサイズを変更できない場合は、次の手順を実行します。
  
  1. 可用性セットのすべての VM を停止します。
     
     * **[リソース グループ]** > *対象のリソース グループ* > **[リソース]** > *対象の可用性セット* > **[Virtual Machines]** > *対象の仮想マシン* > **[停止]** の順にクリックします。
  2. すべての VM が停止したら、目的の VM のサイズを大きなサイズに変更します。
  3. サイズ変更した VM を選択して **[起動]**をクリックした後、停止している各 VM を起動します。

## <a name="next-steps"></a>次のステップ
Azure で新しい Windows VM を作成するときに問題が発生する場合は、[Azure での新しい Windows 仮想マシンの作成に関するデプロイメントの問題のトラブルシューティング](virtual-machines-windows-troubleshoot-deployment-new-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)に関する記事を参照してください。




<!--HONumber=Jan17_HO2-->


