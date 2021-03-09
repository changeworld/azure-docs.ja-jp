---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: db4e27c0972a93d870d0a6565f1bd3212146df62
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/03/2020
ms.locfileid: "96581229"
---
非 DHCP 環境内で起動する場合は、次の手順に従って、ご使用の Data Box Gateway の仮想マシンをデプロイします。

1. [デバイスの Windows PowerShell インターフェイスに接続します](#connect-to-the-powershell-interface)。
2. `Get-HcsIpAddress` コマンドレットを使用して、ご使用の仮想デバイス上で有効なネットワーク インターフェイスの一覧を表示します。 デバイスで単一のネットワーク インターフェイスが有効になっている場合、このインターフェイスに割り当てられる既定の名前は `Ethernet`です。

    このコマンドレットの使用例を次に示します。

    ```
    [10.100.10.10]: PS>Get-HcsIpAddress

    OperationalStatus : Up
    Name              : Ethernet
    UseDhcp           : True
    IpAddress         : 10.100.10.10
    Gateway           : 10.100.10.1
    ```

3. `Set-HcsIpAddress` コマンドレットを使用してネットワークを構成します。 次の例を参照してください。

    ```
    Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1
    ```

