---
title: Azure Stack Edge Pro GPU での仮想マシンのプロビジョニングに関するトラブルシューティング | Microsoft Docs
description: Azure Stack Edge Pro GPU で新しい仮想マシンをプロビジョニングするときに発生する問題のトラブルシューティングを行う方法について説明します。
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 08/03/2021
ms.author: alkohli
ms.openlocfilehash: e04d3a0a5b7c48117af1f597606658a878a3a6ff
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121740770"
---
# <a name="troubleshoot-vm-deployment-in-azure-stack-edge-pro-gpu"></a>Azure Stack Edge Pro GPU での VM のデプロイのトラブルシューティング

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

この記事では、Azure Stack Edge Pro GPU デバイスに仮想マシンをデプロイするときに発生する一般的なエラーのトラブルシューティングを行う方法について説明します。 この記事では、VM のプロビジョニングのタイムアウトおよびネットワーク インターフェイスと VM の作成中の問題の原因である最も一般的な問題を調査するときのガイダンスを提供します。

VM のプロビジョニングの失敗を診断するには、失敗した仮想マシンのゲスト ログを確認します。 VM ゲスト ログを収集してサポート パッケージに含める手順については、[Azure Stack Edge Pro 上の VM のゲスト ログ収集](azure-stack-edge-gpu-collect-virtual-machine-guest-logs.md)に関する記事を参照してください。

VM のデプロイ前に VM イメージの正常なアップロードを妨げる問題のガイダンスについては、「[Azure Stack Edge Pro GPU での仮想マシン イメージのアップロードのトラブルシューティング](azure-stack-edge-gpu-troubleshoot-virtual-machine-image-upload.md)」を参照してください。


## <a name="vm-provisioning-timeout"></a>VM プロビジョニングのタイムアウト

このセクションでは、VM プロビジョニングのタイムアウトの最も一般的な原因のトラブルシューティングについて説明します。

VM のプロビジョニングがタイムアウトすると、次のエラーが表示されます。 

![VM のプロビジョニングが Azure Stack Edge でタイムアウトしたときに Azure portal に表示されるエラーのスクリーンショット。](./media/azure-stack-edge-gpu-troubleshoot-virtual-machine-provisioning/vm-provisioning-timeout-01.png) 

VM プロビジョニングがタイムアウトする問題のよくある原因は次のとおりです。
- VM に割り当てた IP アドレスが既に使用されています。 [詳細情報](#vm-provisioning-timeout)
- VM のデプロイに使用した VM イメージが正しく準備されていませんでした。 [詳細情報](#vm-image-not-prepared-correctly)
- 既定のゲートウェイと DNS サーバーに、ゲスト VM から到達できませんでした。 [詳細情報](#gateway-dns-server-couldnt-be-reached-from-guest-vm)
- `cloud init` のインストールの間に、`cloud init` が実行しなかったか、実行中に問題が発生しました。 (Linux VM のみ) [詳細情報](#cloud-init-issues-linux-vms)
- カスタム VM イメージを使用してデプロイされた Linux VM の場合、/etc/waagent.conf ファイルのプロビジョニング フラグが正しくありません。 (Linux VM のみ) [詳細情報](#provisioning-flags-set-incorrectly-linux-vms)

### <a name="ip-assigned-to-the-vm-is-already-in-use"></a>VM に割り当てられた IP が既に使用されています

**エラーの説明:** 既に使用されている静的 IP アドレスが VM に割り当てられ、VM のプロビジョニングが失敗しました。 このエラーは、VM がデプロイされるサブネットで IP アドレスが使用されている場合に発生します。 Azure portal で VM をデプロイすると、プロセスによってデバイス内で既存の IP アドレスがチェックされますが、サブネット上にある可能性のある他のサービスまたは仮想マシンの IP アドレスは確認できません。 

**推奨される解決方法:** 使用されていない静的 IP アドレスを使用するか、DHCP サーバーによって提供される動的 IP アドレスを使用します。

重複する IP アドレスを調べるには:

- 同じネットワーク上の任意のアプライアンスから、次の `ping` と Test-NetConnection (`tnc`) コマンドを実行します。

  ```
  ping <IP address>
  tnc <IP address>
  tnc <IP address> -CommonTCPPort “RDP”
  ```

応答が返された場合、新しい VM に割り当てた IP アドレスは既に使用されています。

### <a name="vm-image-not-prepared-correctly"></a>VM イメージが正しく準備されていない

**エラーの説明:** Azure Stack Edge Pro GPU デバイスで使用する VM イメージを準備するには、特定のワークフローに従う必要があります。 Azure で Gen1 仮想マシンを作成し、VM をカスタマイズし、VHD を一般化してから、その仮想マシンの OS VHD をダウンロードする必要があります。 準備されたイメージは、"vhd" ファイル名拡張子と固定の種類を持つ Gen1 VHD である必要があります。

要件の概要については、[Azure Stack Edge Pro GPU デバイス用のカスタム VM イメージの作成](azure-stack-edge-gpu-create-virtual-machine-image.md)に関する記事を参照してください。 VM イメージの問題の解決に関するガイダンスについては、「[Azure Stack Edge Pro GPU での仮想マシン イメージのアップロードのトラブルシューティング](azure-stack-edge-gpu-troubleshoot-virtual-machine-image-upload.md)」参照してください。  

**推奨される解決方法:** VM イメージを準備するためのワークフローを完了します。 ガイダンスについては、次のいずれかの記事を参照してください。

* [Windows および Linux VM 用のカスタム VM イメージのワークフロー](azure-stack-edge-gpu-create-virtual-machine-image.md)
* [Windows VHD から一般化されたイメージを準備する](azure-stack-edge-gpu-prepare-windows-vhd-generalized-image.md)
* [ISO を使用して一般化されたイメージを準備する](azure-stack-edge-gpu-prepare-windows-generalized-image-iso.md)
* [特殊化されたイメージを使用して VM をデプロイする](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)


### <a name="gateway-dns-server-couldnt-be-reached-from-guest-vm"></a>ゲートウェイと DNS サーバーにゲスト VM から到達できない

**エラーの説明:** VM のデプロイの間に既定のゲートウェイと DNS サーバーに到達できない場合、VM のプロビジョニングはタイムアウトし、VM のデプロイは失敗します。

**推奨される解決方法:** 既定のゲートウェイと DNS サーバーに VM から到達できることを確認します。 その後、VM のデプロイを繰り返します。

VM から既定のゲートウェイと DNS サーバーに到達できることを確認するには、次の手順のようにします。
1. [VM に接続します](azure-stack-edge-gpu-deploy-virtual-machine-portal.md#connect-to-a-vm)。
2. 次のコマンドを実行します。

   ```
   ping <default gateway IP address>
   ping <DNS server IP address>
   ```

   既定のゲートウェイと DNS サーバーの IP アドレスを確認するには、デバイスのローカル UI に移動します。 目的のポートを選択し、ネットワーク設定を表示します。

   ![ポート 2 のネットワーク設定が表示されている Azure Stack Edge デバイスの [ネットワーク] ページのスクリーンショット。](./media/azure-stack-edge-gpu-troubleshoot-virtual-machine-provisioning/gateway-dns-server-settings-01.png) 


### <a name="cloud-init-issues-linux-vms"></a>`cloud init` に関する問題 (Linux VM)

**エラーの説明:** `cloud init` が実行されなかったか、`cloud init` の実行中に問題が発生しました。 `cloud-init` は、VM を初めて起動するときに Linux VM をカスタマイズするために使用します。 詳細については、「[Azure での仮想マシンに対する cloud-init のサポート](../virtual-machines/linux/using-cloud-init.md)」を参照してください。

**推奨される解決方法:** `cloud init` が実行されたときに発生した問題を見つけるには:
1. [VM に接続します](azure-stack-edge-gpu-deploy-virtual-machine-portal.md#connect-to-a-vm)。
1. 次のログ ファイルで `cloud init` のエラーを調べます。

   - /var/log/cloud-init-output.log
   - /var/log/cloud-init.log
   - /var/log/waagent/log 

`cloud init` が正常に実行されるのを妨げる最も一般的な問題の一部を調べるには、次の手順のようにします。

1. VM イメージが `cloud init` に基づいていることを確認します。 次のコマンドを実行します。

   `cloud-init --version`

   コマンドから cloud init のバージョン番号が返される必要があります。 イメージが `cloud init` に基づいていない場合は、コマンドからバージョン情報が返されません。

   `cloud init` オプションに関するヘルプを表示するには、次のコマンドを実行します。

   `cloud-init --help` 

2. データ ソースを *Azure* に設定して、`cloud init` インスタンスを正常に実行できることを確認します。 

   データ ソースが *Azure* に設定されている場合、*cloud init* のログのエントリは次のようになります。

   ![データ ソースが Azure に設定されている VM イメージの cloud-init のログ エントリの図。 識別用のテキストが強調表示されています。](./media/azure-stack-edge-gpu-troubleshoot-virtual-machine-provisioning/cloud-init-log-entry-01.png)

   データ ソースが Azure に設定されていない場合は、`cloud init` スクリプトの修正が必要な場合があります。 詳細については、「[cloud-init を深く知る](../virtual-machines/linux/cloud-init-deep-dive.md)」を参照してください。


### <a name="provisioning-flags-set-incorrectly-linux-vms"></a>プロビジョニング フラグが正しく設定されていない (Linux VM)

**エラーの説明:** Azure に Linux VM を正常にデプロイするには、イメージでプロビジョニングを無効にし、`cloud init` を使用したプロビジョニングを有効にする必要があります。 これらの値を設定するプロビジョニング フラグは、標準の VM イメージに対して正しく構成されます。 カスタム VM イメージを使用する場合は、それらが正しいことを確認する必要があります。 

**推奨される解決方法:** */etc/waagent.conf* ファイルでプロビジョニング フラグに次の値が設定されていることを確認します。<!--Move details to "Create a custom VM image" when the 2 active PRs against that article have been merged. Not before Friday release.-->

   | 機能                      | 必須の値                |
   |---------------------------------|-------------------------------|
   | Enable provisioning             | `Provisioning.Enabled=n`      |
   | cloud-init を使用してプロビジョニングする | `Provisioning.UseCloudInit=y` |


##  <a name="network-interface-creation-issues"></a>ネットワーク インターフェイスの作成に関する問題

このセクションでは、VM のデプロイ中にネットワーク インターフェイスの作成が失敗する原因となる問題に関するガイダンスを提供します。


### <a name="nic-creation-timeout"></a>NIC の作成のタイムアウト

**エラーの説明:** VM でのネットワーク インターフェイスの作成が、許可されたタイムアウト期間内に完了しませんでした。 このエラーは、環境内の DHCP サーバーの問題が原因で発生する可能性があります。 

ネットワーク インターフェイスが正常に作成されたかどうかを確認するには、次の手順のようにします。

1. Azure portal で、デバイスの Azure Stack Edge リソースに移動します ( **[Edge Services]\(Edge サービス\)**  >  **[仮想マシン]** に移動)。 次に、 **[デプロイ]** を選択し、VM のデプロイに移動します。 

1. ネットワーク インターフェイスが正常に作成されていない場合は、次のエラーが表示されます。

   ![Azure Stack Edge デバイスでの VM のデプロイ中にネットワーク インターフェイスの作成が失敗した場合に Azure portal に表示されるエラーのスクリーンショット。](./media/azure-stack-edge-gpu-troubleshoot-virtual-machine-provisioning/nic-creation-failed-01.png)

**推奨される解決方法:** VM を再度作成し、それに静的 IP アドレスを割り当てます。


## <a name="vm-creation-issues"></a>VM の作成に関する問題

このセクションでは、VM の作成時に発生する一般的な問題について説明します。

### <a name="not-enough-memory-to-create-the-vm"></a>VM を作成するのに十分なメモリがない

**エラーの説明:** メモリ不足のために VM の作成が失敗すると、次のエラーが表示されます。
 
![Azure Stack Edge デバイスで VM の作成に失敗した場合に Azure portal に表示されるエラーのスクリーンショット。](./media/azure-stack-edge-gpu-troubleshoot-virtual-machine-provisioning/vm-creation-failed-01.png)

**推奨される解決方法:** デバイスで使用可能なメモリを調べて、適切な VM サイズを選択します。 詳細については、「[Azure Stack Edge でサポートされている仮想マシンのサイズ](azure-stack-edge-gpu-virtual-machine-sizes.md)」を参照してください。

VM のデプロイに使用できるメモリは、いくつかの要因によって制限されます。

- デバイスで使用可能なメモリの量。 詳細については、[Azure Stack Edge Pro GPU 技術仕様](azure-stack-edge-gpu-technical-specifications-compliance.md#compute-and-memory-specifications)に関する記事の「コンピューティングとメモリの仕様」と、「[Azure Stack Edge Mini R の技術仕様](azure-stack-edge-mini-r-technical-specifications-compliance.md#compute-memory)」を参照してください。

- Kubernetes が有効になっている場合、Kubernetes クラスター上で Kubernetes とアプリに必要なコンピューティング メモリ。
- Hyper-V の各仮想マシンのオーバーヘッド。

**推奨される解決方法:**

- 必要なメモリが少ない VM サイズを使用します。
- 新しい VM をデプロイする前に、ポータルから使用されていないすべての VM を停止します。
- 使用されなくなったすべての VM を削除します。


### <a name="insufficient-number-of-gpus-to-create-gpu-vm"></a>GPU VM を作成するのに GPU の数が足りない

Kubernetes が既に有効になっている GPU デバイスに VM をデプロイしようとすると、GPU は使用できなくなり、VM のプロビジョニングは次のエラーで失敗します。

![Azure Stack Edge デバイスに使用可能な GPU がないために GPU VM の作成が失敗した場合に、Azure portal に表示されるエラーのスクリーンショット。](./media/azure-stack-edge-gpu-troubleshoot-virtual-machine-provisioning/gpu-vm-creation-failed-01.png)

**考えられる原因:** VM が作成される前に Kubernetes が有効になっている場合は、使用可能なすべての GPU が Kubernetes によって使用され、どのような GPU サイズの VM も作成できません。 使用可能な GPU の数と同じ GPU サイズの VM を作成できます。 Azure Stack Edge デバイスが備えることのできる GPU は、1 つまたは 2 つです。

**推奨される解決方法:** Kubernetes が構成されている 1 GPU または 2 GPU デバイスでの VM デプロイのオプションについては、「[GPU VM と Kubernetes](azure-stack-edge-gpu-overview-gpu-virtual-machines.md#gpu-vms-and-kubernetes)」を参照してください。


## <a name="next-steps"></a>次のステップ

- [失敗した VM のゲスト ログを含むサポート パッケージの収集](azure-stack-edge-gpu-collect-virtual-machine-guest-logs.md)<!--Does a failed VM have a guest log? Does it have GPU and memory metrics?-->
- [GPU 拡張機能のインストールが失敗する問題のトラブルシューティング](azure-stack-edge-gpu-collect-virtual-machine-guest-logs.md)
- [Azure Resource Manager での問題のトラブルシューティング](azure-stack-edge-gpu-troubleshoot-azure-resource-manager.md)
