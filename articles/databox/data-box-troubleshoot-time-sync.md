---
title: Azure Data Box、Azure Data Box Heavy デバイスの時刻同期の問題をトラブルシューティングする
description: PowerShell インターフェイスを使用して、Azure Data Box または Azure Data Box Heavy デバイスの時刻同期の問題をトラブルシューティングする方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: troubleshooting
ms.date: 11/15/2021
ms.author: alkohli
ms.openlocfilehash: 8999c2873528dd5dd0fefc0b730f4856ac995649
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132557120"
---
# <a name="sync-device-time-for-azure-data-box-and-azure-data-box-heavy"></a>Azure Data Box と Azure Data Box Heavy のデバイス時刻を同期する

この記事では、お使いの Data Box が同期されていないことを診断してから、Data Box デバイスの時刻を変更する方法について説明します。 この記事の情報は、Data Box と Data Box Heavy の両方のデバイスに関するインポート注文とエクスポート注文に当てはまります。


## <a name="about-device-time-sync"></a>デバイス時刻の同期について

Data Box では、インターネットに接続されているときには、既定の Windows タイム サーバー `time.windows.com` を使用して自動的に時刻が同期されます。 ただし、Data Box がインターネットに接続されていない場合、デバイス時刻が同期されないことがあります。この状況は、ソース データから Data Box へのデータ コピーに影響を与える可能性があります。具体的には、REST API または時間的制約がある特定のツールでコピーが行われる場合です。 

Data Box と、サイトの他のローカル デバイスの時刻に時間差が見られる場合は、PowerShell インターフェイスにアクセスして、お使いの Data Box の時刻を同期できます。 デバイス時刻を変更するためには、`Set-Date API` が使用されます。 詳細については、[Set-Date API](/powershell/module/microsoft.powershell.utility/set-date?view=powershell-7.1&preserve-view=true) に関するページを参照してください。


## <a name="connect-to-powershell-interface"></a>PowerShell インターフェイスに接続する

時刻同期の問題をトラブルシューティングするには、まず、デバイスの PowerShell インターフェイスに接続する必要があります。

[!INCLUDE [Connect to Data Box PowerShell interface](../../includes/data-box-connect-powershell-interface.md)]


## <a name="change-device-time"></a>デバイス ID を変更する

デバイス時刻を変更するには、以下の手順に従います。

1. デバイスの時刻が同期されていないかどうかを診断する場合は、まずデバイスの時刻を取得します。 `Get-Date` コマンドレットを使用して、Data Box での日付と時刻を表示します。

    `Get-Date`

1. デバイスの時刻が同期されていない場合は、`Set-Date` コマンドレットを使用して、Data Box での時刻を変更します。

    - 時刻を 2 分進めます。
    
        ```powershell
        Set-Date -Adjust <time change in hours:mins:secs format> -DisplayHint Time
        ```
    - 時刻を 2 分遅らせます。

        ```powershell
        Set-Date -Adjust -<time change in hours:mins:secs format> -DisplayHint Time
        ```    

        出力例を次に示します。
        
        ```powershell
        [by506b4b5d0790.microsoftdatabox.com]: PS>Get-date
        Friday, September 3, 2021 2:22:50 PM
        [by506b4b5d0790.microsoftdatabox.com]: PS>Set-Date -Adjust 00:02:00 -DisplayHint Time
        2:25:18 PM
        [by506b4b5d0790.microsoftdatabox.com]: PS>Set-Date -Adjust -00:02:00 -DisplayHint Time
        2:23:28 PM
        [by506b4b5d0790.microsoftdatabox.com]: PS>Get-date
        Friday, September 3, 2021 2:23:42 PM
        [by506b4b5d0790.microsoftdatabox.com]: PS>
        ```
        詳細については、[Set-Date API](/powershell/module/microsoft.powershell.utility/set-date?view=powershell-7.1&preserve-view=true) に関するページを参照してください。
    
## <a name="next-steps"></a>次のステップ

その他の Data Box の問題をトラブルシューティングする場合は、以下のいずれかの記事を参照してください。

- [Data Box の BLOB ストレージ エラーをトラブルシューティングします](data-box-troubleshoot-rest.md)。
- [Data Box のデータ コピー エラーをトラブルシューティングします](data-box-troubleshoot.md)。
- [Data Box のデータ アップロード エラーをトラブルシューティングします](data-box-troubleshoot-data-upload.md)。
