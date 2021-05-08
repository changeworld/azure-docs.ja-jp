---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: c1b56cfb85595b8a17dc18f69a0b162d504c04ec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96026366"
---
デバイスをリセットするには、データ ディスクのすべてのデータおよびデバイスのブート ディスクを安全に消去する必要があります。 

データ ディスクとブート ディスクの両方を消去する、またはデータ ディスクのみを消去するには、`Reset-HcsAppliance` コマンドレットを使用します。 `ClearData` および `BootDisk` スイッチによってデータ ディスクとブート ディスクをそれぞれ消去できます。

`BootDisk` スイッチによってブート ディスクがワイプされ、デバイスが使用できなくなります。 これは、デバイスを Microsoft に返す必要がある場合にのみ使用してください。 詳細については、[Microsoft へのデバイスの返却](../articles/databox-online/azure-stack-edge-return-device.md)に関する記事を参照してください。

ローカル Web UI でデバイス リセットを使用する場合、データ ディスクのみが安全に消去されますが、ブート ディスクはそのまま維持されます。 ブート ディスクはデバイス構成を含みます。

1. [PowerShell インターフェイスに接続する](#connect-to-the-powershell-interface)。
2. コマンド プロンプトに、次のコマンドを入力します。

    `Reset-HcsAppliance -ClearData -BootDisk`

    次の例は、コマンドレットの使用方法を示しています。

    ```powershell
    [10.128.24.33]: PS>Reset-HcsAppliance -ClearData -BootDisk

    Confirm
    Are you sure you want to perform this action?
    Performing the operation "Reset-HcsAppliance" on target "ShouldProcess appliance".
    [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [?] Help (default is "Y"): N
    ```