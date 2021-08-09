---
title: Azure Stack Edge Pro GPU で VM ゲスト ログを収集する
description: Azure Stack Edge Pro GPU デバイス上の VM のゲスト ログを含むサポート パッケージの作成方法を説明します。
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 06/03/2021
ms.author: alkohli
ms.openlocfilehash: 7ffb96cdc3fbb561009b7f545a3b9a1da9eef729
ms.sourcegitcommit: ef950cf37f65ea7a0f583e246cfbf13f1913eb12
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/04/2021
ms.locfileid: "111421006"
---
# <a name="collect-vm-guest-logs-on-an-azure-stack-edge-pro-gpu-device"></a>Azure Stack Edge Pro GPU デバイスで VM ゲスト ログを収集する

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Azure Stack Edge Pro GPU デバイスで VM プロビジョニング エラーを診断するには、失敗した仮想マシンのゲスト ログを確認します。 この記事では、サポート パッケージ内の VM のゲスト ログを収集する方法について説明します。

## <a name="collect-vm-guest-logs-in-support-package"></a>サポート パッケージで VM ゲスト ログを収集する

Azure Stack Edge Pro GPU デバイスで障害が発生した仮想マシンのゲスト ログを収集するには、次の手順を実行します。

1. [デバイスの PowerShell インターフェイスに接続します](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface)。

2. 次のコマンドを実行して、失敗した VM のゲスト内ログを収集し、これらのログをサポート パッケージに含めます。

   ```powershell
   Get-VMInGuestLogs -FailedVM
   Get-HcsNodeSupportPackage -Path “\\<network path>” -Include InGuestVMLogFiles -Credential “domain_name\user”
   ```

   ログは `hcslogs\VmGuestLogs` フォルダーにあります。

3. VM プロビジョニング履歴の詳細を取得するには、次のログを確認します。

   **Linux VM:**
   - /var/log/cloud-init-output.log
   - /var/log/cloud-init.log
   - /var/log/waagent.log

   **Windows VM:**
   - C:\Windows\Azure\Panther\WaSetup.xml

## <a name="next-steps"></a>次の手順

- [Azure Stack Edge Pro GPU での VM プロビジョニングのトラブルシューティング](azure-stack-edge-gpu-troubleshoot-virtual-machine-provisioning.md)