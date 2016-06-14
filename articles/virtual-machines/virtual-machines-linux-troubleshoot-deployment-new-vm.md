<properties
   pageTitle="Linux VM の RM デプロイメントのトラブルシューティング | Microsoft Azure"
   description="Azure で新しい Linux 仮想マシンを作成するときに発生する Resource Manager デプロイメントの問題のトラブルシューティング"
   services="virtual-machines-linux, azure-resource-manager"
   documentationCenter=""
   authors="jiangchen79"
   manager="felixwu"
   editor=""
   tags="top-support-issue, azure-resource-manager"/>

<tags
  ms.service="virtual-machines-linux"
  ms.workload="na"
  ms.tgt_pltfrm="vm-linux"
  ms.devlang="na"
  ms.topic="article"
  ms.date="05/06/2016"
  ms.author="cjiang"/>

# Azure での新しい Linux 仮想マシンの作成に関する Resource Manager デプロイメントの問題のトラブルシューティング

[AZURE.INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-selectors](../../includes/virtual-machines-linux-troubleshoot-deployment-new-vm-selectors-include.md)]

[AZURE.INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## 監査ログの収集

トラブルシューティングを開始するには、監査ログを収集して問題に関連するエラーを特定します。このプロセスの詳細については、次のリンクをご覧ください。

[Azure ポータルでのリソース グループのデプロイのトラブルシューティング](../resource-manager-troubleshoot-deployments-portal.md)

[リソース マネージャーの監査操作](../resource-group-audit.md)

[AZURE.INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[AZURE.INCLUDE [virtual-machines-linux-troubleshoot-deployment-new-vm-table](../../includes/virtual-machines-linux-troubleshoot-deployment-new-vm-table.md)]

**Y:** OS が一般化された Linux であり、一般化された設定でアップロード/キャプチャされた場合、エラーは発生しません。同様に、OS が特殊化された Linux であり、特殊化された設定でアップロード/キャプチャされた場合、エラーは発生しません。

**アップロード エラー:**

**N<sup>1</sup>:** OS が一般化された Linux であり、特殊化された Linux としてアップロードされた場合、プロビジョニングの段階で VM がスタックされるため、プロビジョニング タイムアウト エラーが発生します。

**N<sup>2</sup>:** OS が特殊化された Linux であり、一般化された Linux としてアップロードされた場合、新しい VM は元のコンピューター名、ユーザー名、パスワードを使用して実行されるため、プロビジョニング エラー (プロビジョニング失敗) が発生します。

**解決策:**

これらのエラーを解決するには、OS と同じ設定 (一般化/特殊化) を使用して、オンプレミスで使用可能な元の VHD をアップロードします。一般化された OS としてアップロードするには、まずプロビジョニング解除を必ず実行してください。

**キャプチャ エラー:**

**N<sup>3</sup>:** OS が一般化された Linux であり、特殊化された Linux としてキャプチャされた場合、一般化された Linux としてマークされた元の VM を使用できないため、プロビジョニング タイムアウト エラーが発生します。

**N<sup>4</sup>:** OS が特殊化された Linux であり、一般化された Linux としてキャプチャされた場合、新しい VM は元のコンピューター名、ユーザー名、パスワードを使用して実行されるため、プロビジョニング エラー (プロビジョニング失敗) が発生します。また、元の VM は特殊化としてマークされているので使用できません。

**解決策:**

これらのエラーを解決するには、ポータルから現在のイメージを削除し、OS と同じ設定 (一般化/特殊化) で[現在の VHD からイメージをキャプチャし直します](virtual-machines-linux-capture-image.md)。

## 問題: カスタム/ギャラリー/Marketplace イメージ - 割り当てエラー
このエラーは、新しい VM 要求が、要求されている VM サイズをサポートできないか、要求に対応するための使用可能な空き領域がないクラスターに固定されている場合に発生します。

**原因 1:** クラスターが要求された VM サイズをサポートできない。

**解決策 1:**

- VM サイズを小さくして要求を再試行します。
- 要求した VM のサイズを変更できない場合は、次の手順を実行します。
  - 可用性セットのすべての VM を停止します。**[リソース グループ]**、*対象のリソース グループ*、**[リソース]**、*対象の可用性セット*、**[Virtual Machines]**、*対象の仮想マシン*、**[停止]** の順にクリックします。
  - すべての VM が停止したら、目的のサイズで新しい VM を作成します。
  - 新しい VM を起動してから、停止している各 VM を選択し、**[起動]** をクリックします。

**原因 2:** クラスターに空きリソースがない。

**解決策 2:**

- 後で要求を再試行します。
- 新しい VM を別の可用性セットに含めることができる場合
  - 新しい VM を (同じリージョンの) 別の可用性セットに作成します。
  - 新しい VM を同じ仮想ネットワークに追加します。

<!---HONumber=AcomDC_0601_2016-->