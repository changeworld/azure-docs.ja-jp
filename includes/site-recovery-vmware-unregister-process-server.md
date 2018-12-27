---
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: include
ms.date: 08/06/2018
ms.author: ramamill
ms.openlocfilehash: 81390d38b4c0c38b7ac6883ae2bf18c64542fa00
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2018
ms.locfileid: "39582857"
---
プロセス サーバーの登録を解除する手順は、構成サーバーとの接続状態によって異なります。

### <a name="unregister-a-process-server-that-is-in-a-connected-state"></a>接続状態のプロセス サーバーの登録解除

1. 管理者としてプロセス サーバーにリモート接続します。
2. **[コントロール パネル]** を開き、**[プログラム]、[プログラムのアンインストール]** の順に開きます。
3. **Microsoft Azure Site Recovery Configuration/Process Server (Microsoft Azure Site Recovery 構成/プロセス サーバー)** という名前のプログラムをアンインストールします。
4. 手順 3. が完了すると、**Microsoft Azure Site Recovery Configuration/Process Server Dependencies (Microsoft Azure Site Recovery 構成/プロセス サーバーの依存関係)** をアンインストールできます。

### <a name="unregister-a-process-server-that-is-in-a-disconnected-state"></a>切断状態のプロセス サーバーの登録解除

> [!WARNING]
> プロセス サーバーがインストールされていた仮想マシンを回復する方法がない場合は、以下の手順を使用します。

1. 管理者として構成サーバーにサインインします。
2. 管理コマンド プロンプトを開き、`%ProgramData%\ASR\home\svsystems\bin` ディレクトリを参照します。
3. 次のコマンドを実行します。

    ```
    perl Unregister-ASRComponent.pl -IPAddress <IP_of_Process_Server> -Component PS
    ```
4. 上記のコマンドによって、次のように、プロセス サーバーの一覧が示されます (重複するエントリの場合は複数になることがあります)。これには、シリアル番号 (S.No)、IP アドレス (IP)、プロセス サーバーがデプロイされる VM の名前 (Name)、VM のハートビート (Heartbeat) が含まれます。
    ![登録解除コマンド](media/site-recovery-vmware-unregister-process-server/Unregister-cmd.PNG)
5. ここで、登録を解除するプロセス サーバーのシリアル番号を入力します。
6. これによってシステムからプロセス サーバーの詳細が消去され、"**<サーバー名 (サーバーの IP アドレス) の登録が正常に解除されました**" というメッセージが表示されます。

