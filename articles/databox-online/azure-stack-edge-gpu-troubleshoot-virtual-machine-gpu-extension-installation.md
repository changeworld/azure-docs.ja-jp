---
title: Azure Stack Edge Pro GPU 上の GPU VM 向け GPU 拡張機能に関する問題のトラブルシューティング
description: Azure Stack Edge Pro GPU 上の GPU VM 向け GPU 拡張機能のインストールに関する問題のトラブルシューティングを行う方法について説明します。
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/02/2021
ms.author: alkohli
ms.openlocfilehash: a2e6996bd2b86da470b364da1349248e26b2fa58
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121747356"
---
# <a name="troubleshoot-gpu-extension-issues-for-gpu-vms-on-azure-stack-edge-pro-gpu"></a>Azure Stack Edge Pro GPU 上の GPU VM 向け GPU 拡張機能に関する問題のトラブルシューティング

[!INCLUDE [applies-to-gpu-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

この記事では、GPU VM への GPU 拡張機能のインストールが Azure Stack Edge Pro GPU デバイスで失敗する原因となる最も一般的な問題を解決するためのガイダンスを提供します。

インストール手順については、[GPU 拡張機能のインストール](./azure-stack-edge-gpu-deploy-virtual-machine-install-gpu-extension.md?tabs=linux)に関するページを参照してください。

## <a name="vm-size-is-not-gpu-vm-size"></a>VM サイズが GPU VM サイズではありません

**エラーの説明:** GPU VM は、Standard_NC4as_T4_v3 または Standard_NC8as_T4_v3 サイズのいずれかである必要があります。 他の VM サイズが使用されていると、GPU 拡張機能のアタッチは失敗します。

**推奨される解決方法:** Standard_NC4as_T4_v3 または Standard_NC8as_T4_v3 VM サイズを使用して VM を作成します。 詳細については、[GPU VM でサポートされている VM サイズ](azure-stack-edge-gpu-virtual-machine-sizes.md#ncast4_v3-series-preview)に関するページを参照してください。 サイズの指定の詳細については、「[GPU VM の作成](./azure-stack-edge-gpu-deploy-gpu-virtual-machine.md#create-gpu-vms)」を参照してください。


## <a name="image-os-is-not-supported"></a>イメージ OS がサポートされていません

**エラーの説明:** GPU 拡張機能で、VM イメージにインストールされているオペレーティング システムがサポートされていません。 

**推奨される解決方法:** GPU 拡張機能でサポートされているオペレーティング システムを備えた新しい VM イメージを準備します。 

* サポートされているオペレーティング システムの一覧については、[GPU VM でサポートされている OS および GPU ドライバー](./azure-stack-edge-gpu-overview-gpu-virtual-machines.md#supported-os-and-gpu-drivers)に関するページを参照してください。

* GPU VM のイメージ準備の要件については、「[GPU VM の作成](./azure-stack-edge-gpu-deploy-gpu-virtual-machine.md#create-gpu-vms)」を参照してください。


## <a name="extension-parameter-is-incorrect"></a>拡張機能パラメーターが正しくありません

**エラーの説明:** GPU 拡張機能を Linux VM にデプロイするときに、不正な拡張機能設定が使用されました。 

**推奨される解決方法:** GPU 拡張機能をデプロイする前に、パラメーター ファイルを編集します。 詳細については、[GPU 拡張機能のインストール](./azure-stack-edge-gpu-deploy-virtual-machine-install-gpu-extension.md?tabs=linux)に関するページを参照してください。


## <a name="vm-extension-installation-failed-in-downloading-package"></a>パッケージのダウンロード中に VM 拡張機能のインストールに失敗しました

**エラーの説明:** 拡張機能のインストール中または有効状態で、拡張機能のプロビジョニングに失敗しました。

1. ゲスト ログで、関連するエラーを確認します。 ゲスト ログを収集するには、[Azure Stack Edge Pro 上の VM のゲスト ログを収集する](azure-stack-edge-gpu-collect-virtual-machine-guest-logs.md)方法に関するページを参照してください。

   Linux VM の場合:
   * `/var/log/waagent.log` または `/var/log/azure/nvidia-vmext-status` を検索します。

   Windows VM の場合:
   * `C:\Packages\Plugins\Microsoft.HpcCompute.NvidiaGpuDriverWindows\1.3.0.0\Status`でエラー状態を確認します。
   * 完全な実行ログ `C:\WindowsAzure\Logs\WaAppAgent.txt`を確認します。

   パッケージのダウンロード中にインストールが失敗した場合、そのエラーは、VM でパブリック ネットワークにアクセスしてドライバーをダウンロードできなかったことを示します。

**推奨されている解決方法:**

1.  インターネットに接続されているポートでコンピューティングを有効にします。 ガイダンスについては、「[GPU VM の作成](azure-stack-edge-gpu-deploy-gpu-virtual-machine.md#create-gpu-vms)」を参照してください。

1.  portal で VM を停止して VM の割り当てを解除します。 VM を停止するには、 **[仮想マシン]**  >  **[概要]** の順に移動し、VM を選択します。 次に、VM のプロパティ ページで、 **[停止]** を選択します。<!--Follow-up (formatting): Create an include file for stopping a VM. Use it here and in prerequisites for "Use the Azure portal to manage network interfaces on the VMs" (https://docs.microsoft.com/azure/databox-online/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal#prerequisites).-->
 
1.  新しい VM を作成します。


## <a name="vm-extension-failed-with-error-dpkg-is-usedyum-lock-is-used-linux-vm"></a>VM 拡張機能がエラー `dpkg is used/yum lock is used` で失敗しました (Linux VM)

**エラーの説明:** 別のプロセスが `dpkg` を使用していたか、または別のプロセスが `yum lock` を作成したため、Linux VM での GPU 拡張機能のデプロイに失敗しました。 

**推奨される解決方法:** 問題を解決するには、次の手順を行います。

1.  ロックを適用しているプロセスを検出するには、\var\log\azure\nvidia-vmext-status ログで、"dpkg is used by another process" または "Another app is holding `yum lock`" などのエラーを探します。

1. プロセスが終了するのを待つか、プロセスを終了します。

1.  再度 [GPU 拡張機能をインストール](./azure-stack-edge-gpu-deploy-virtual-machine-install-gpu-extension.md?tabs=linux)します。

1.  拡張機能のデプロイが再度失敗した場合は、新しい VM を作成して、ロックが存在していないことを確認した後に GPU 拡張機能をインストールします。


## <a name="next-steps"></a>次のステップ

[ゲスト ログを収集し、サポート パッケージを作成する](azure-stack-edge-gpu-collect-virtual-machine-guest-logs.md)
