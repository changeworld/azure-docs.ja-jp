---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: 21c19027d21a87e199d74644cfc5c8f3cd52ba4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "79129159"
---
デバイスをリセットするには、データ ディスクのすべてのデータおよびデバイスのブート ディスクを安全に消去する必要があります。 

データ ディスクとブート ディスクの両方を消去する、またはデータ ディスクのみを消去するには、`Reset-HcsAppliance` コマンドレットを使用します。 `ClearData` および `BootDisk` スイッチによってデータ ディスクとブート ディスクをそれぞれ消去できます。

`BootDisk` スイッチによってブート ディスクがワイプされ、デバイスが使用できなくなります。 これは、デバイスを Microsoft に返す必要がある場合にのみ使用してください。 詳細については、[Microsoft へのデバイスの返却](https://docs.microsoft.com/azure/databox-online/data-box-edge-return-device)に関する記事を参照してください。

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
