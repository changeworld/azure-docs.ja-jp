---
title: Azure Stack Edge Pro GPU で VM ゲスト ログを収集する
description: Azure Stack Edge Pro GPU デバイス上の VM のゲスト ログを含むサポート パッケージの作成方法を説明します。
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 07/30/2021
ms.author: alkohli
ms.openlocfilehash: 1c25ea8c35b81169119b0f10025b36319d4dc2c9
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121744615"
---
# <a name="collect-vm-guest-logs-on-an-azure-stack-edge-pro-gpu-device"></a>Azure Stack Edge Pro GPU デバイスで VM ゲスト ログを収集する

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Azure Stack Edge Pro GPU デバイスで VM プロビジョニング エラーを診断するには、失敗した仮想マシンのゲスト ログを確認します。 この記事では、サポート パッケージ内の VM のゲスト ログを収集する方法について説明します。

> [!NOTE]
> また、Azure portal で仮想マシンのアクティビティ ログを監視することもできます。 詳細については、[デバイスでの VM アクティビティの監視](azure-stack-edge-gpu-monitor-virtual-machine-activity.md)に関する記事を参照してください。


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

- [VM アクティビティ ログの監視](azure-stack-edge-gpu-monitor-virtual-machine-activity.md)。
- [Azure Stack Edge Pro GPU での VM プロビジョニングのトラブルシューティング](azure-stack-edge-gpu-troubleshoot-virtual-machine-provisioning.md)。