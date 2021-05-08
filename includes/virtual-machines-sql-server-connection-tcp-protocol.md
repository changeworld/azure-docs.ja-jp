---
author: rothja
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 10/26/2018
ms.author: jroth
ms.openlocfilehash: f7af6962c3343cd9d3e35e96d88650aa6a42c6b3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95560422"
---
1. リモート デスクトップを使用して仮想マシンに接続している状態で、**構成マネージャー** を検索します。

    ![SSCM を開く](./media/virtual-machines-sql-server-connection-tcp-protocol/sql-server-configuration-manager.png)

1. SQL Server 構成マネージャーのコンソール ペインで、 **[SQL Server ネットワークの構成]** を展開します。

1. コンソール ペインで、 **[MSSQLSERVER のプロトコル]** (既定のインスタンス名) をクリックします。詳細ウィンドウで、 **[TCP]** を右クリックし、 **[有効]** をクリックします (既に有効になっていない場合)。

    ![TCP を有効にする](./media/virtual-machines-sql-server-connection-tcp-protocol/enable-tcp.png)

1. コンソール ペインで、 **[再起動]** をクリックします。 詳細ペインで、**[SQL Server (*インスタンス名*)]** (既定のインスタンスは **[SQL Server (MSSQLSERVER)]**) を右クリックし、**[再起動]** をクリックして、SQL Server のインスタンスを停止および再起動します。

    ![データベース エンジンの再起動](./media/virtual-machines-sql-server-connection-tcp-protocol/restart-sql-server.png)

1. SQL Server 構成マネージャーを閉じます。

SQL Server データベース エンジン用のプロトコルを有効にする方法の詳細については、「 [サーバー ネットワーク プロトコルの有効化または無効化](/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol)」を参照してください。