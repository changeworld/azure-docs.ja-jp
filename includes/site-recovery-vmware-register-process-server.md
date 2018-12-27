---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: e18d0a6a01a86f844edc213fc95003cf4f4b46c9
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/26/2018
ms.locfileid: "50164794"
---
* リモート デスクトップ接続を使用して、プロセス サーバー仮想マシンに接続します。
* デスクトップにあるショートカットをクリックして、cspsconfigtool.exe を起動できます  (プロセス サーバーに初めてログインする場合、このツールは自動的に起動します)。
  - 構成サーバーの完全修飾名 (FQDN) または IP アドレス。
  - 構成サーバーがリッスンするポート。 値は 443 にする必要があります。
  - 構成サーバーに接続するための接続パスフレーズ。
  - このプロセス サーバー用に構成したデータ転送ポート。 環境で別のポート番号に変更していない限り、既定値のままにします。

    ![プロセス サーバーの登録](./media/site-recovery-vmware-register-process-server/register-ps.png)
* [保存] をクリックして構成を保存し、プロセス サーバーを登録します。
