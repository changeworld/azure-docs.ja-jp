<properties
   pageTitle="VM の再起動またはサイズ変更の問題 | Microsoft Azure"
   description="Azure での既存の Windows 仮想マシンの再起動またはサイズ変更に関する Resource Manager デプロイメントの問題のトラブルシューティング"
   services="virtual-machines-windows, azure-resource-manager"
   documentationCenter=""
   authors="Deland-Han"
   manager="felixwu"
   editor=""
   tags="top-support-issue"/>

<tags
   ms.service="virtual-machines-windows"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.devlang="na"
   ms.workload="required"
   ms.date="05/12/2016"
   ms.author="delhan"/>

# Azure での既存の Windows 仮想マシンの再起動またはサイズ変更に関する Resource Manager デプロイメントの問題のトラブルシューティング

> [AZURE.SELECTOR]
- [クラシック](../articles/virtual-machines/virtual-machines-windows-classic-restart-resize-error-troubleshooting.md)
- [リソース マネージャー](../articles/virtual-machines/virtual-machines-windows-restart-resize-error-troubleshooting.md)

停止している Azure 仮想マシン (VM) を起動しようとしたとき、または既存の Azure VM のサイズを変更しようとしたときに発生する一般的なエラーは割り当てエラーです。このエラーは、クラスターまたはリージョンに使用可能なリソースがないか、要求された VM サイズをサポートできない場合に発生します。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]クラシック デプロイ モデル。

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## 監査ログの収集

トラブルシューティングを開始するには、監査ログを収集して問題に関連するエラーを特定します。このプロセスの詳細については、次のリンクをご覧ください。

[Azure ポータルでのリソース グループのデプロイのトラブルシューティング](../resource-manager-troubleshoot-deployments-portal.md)

[リソース マネージャーの監査操作](../resource-group-audit.md)

## 問題: 停止している VM の起動時のエラー

停止している VM を起動しようとしたときに、割り当てエラーが発生します。

### 原因

停止している VM の起動要求は、クラウド サービスをホストしている元のクラスターで行う必要がありますが、クラスターに要求の処理に使用できる空き領域がありません。

### 解決策

*	可用性セットのすべての VM を停止し、各 VM を再起動します。

  1. **[リソース グループ]**、_対象のリソース グループ_、**[リソース]**、_対象の可用性セット_、**[Virtual Machines]**、_対象の仮想マシン_、**[停止]** の順にクリックします。

  2. すべての VM が停止したら、停止している各 VM を選択し、[起動] をクリックします。

*	後で再起動要求を再試行します。

## 問題: 既存の VM の再起動時のエラー

既存の VM のサイズを変更しようとしたときに割り当てエラーが発生します。

### 原因

VM のサイズ変更要求は、クラウド サービスをホストしている元のクラスターで行う必要がありますが、クラスターが要求された VM サイズをサポートしていません。

### 解決策

* VM サイズを小さくして要求を再試行します。

* 要求した VM のサイズを変更できない場合は、次の手順を実行します。

  1. 可用性セットのすべての VM を停止します。

    * **[リソース グループ]**、_対象のリソース グループ_、**[リソース]**、_対象の可用性セット_、**[Virtual Machines]**、_対象の仮想マシン_、**[停止]** の順にクリックします。

  2. すべての VM が停止したら、目的の VM のサイズを大きなサイズに変更します。
  3. サイズ変更した VM を選択して **[起動]** をクリックした後、停止している各 VM を起動します。

<!---HONumber=AcomDC_0518_2016-->