---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 04/28/2019
ms.author: raynew
ms.openlocfilehash: cf39baf34096691144181332566cf567ebc02310
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67181702"
---
1. プロセス サーバーを実行しているマシンへのリモート デスクトップ接続を確立します。 
2. cspsconfigtool.exe を実行して、Azure Site Recovery プロセス サーバー構成ツールを開始します。
    - このツールは、プロセス サーバーに初めてサインインしたときに自動的に起動します。
    - 自動的に開かれない場合は、デスクトップ上のそのショートカットをクリックしてください。

3. **[Configuration Server FQDN Or IP]\(構成サーバーの FQDN または IP\)** に、プロセス サーバーの登録に使用する構成サーバーの名前または IP アドレスを指定します。
4. **[Configuration Server Port]\(構成サーバーのポート\)** に 443 が指定されていることを確認します。 これは、構成サーバーが要求をリッスンするポートです。
5. **[Connection Passphrase]\(接続パスフレーズ\)** に、構成サーバーを設定するときに指定したパスフレーズを指定します。 パスフレーズを見つけるには:
    -  構成サーバーで、Site Recovery のインストール フォルダー * *\home\svssystems\bin\** に移動します。 
    - コマンド **genpassphrase.exe.n** を実行します。 これにより、パスフレーズの場所が示され、確認できます。

6. **[Data Transfer Port]\(データ転送ポート\)** は、カスタム ポートを指定した場合を除き、既定値のままにします。

7. **[保存]** をクリックして設定を保存し、プロセス サーバーを登録します。

    
    ![プロセス サーバーの登録](./media/site-recovery-vmware-register-process-server/register-ps.png)
