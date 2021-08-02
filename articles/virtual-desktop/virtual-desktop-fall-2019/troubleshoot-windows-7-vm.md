---
title: Azure Virtual Desktop (クラシック) 上の Windows 7 仮想マシン - Azure
description: Azure Virtual Desktop (クラシック) 環境の Windows 7 仮想マシン (VM) の問題を解決する方法。
author: Heidilohr
ms.topic: troubleshooting
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 2d0ea39dc4a0b217c35322e0aefc477996402a0f
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/09/2021
ms.locfileid: "111753865"
---
# <a name="troubleshoot-windows-7-virtual-machines-in-azure-virtual-desktop-classic"></a>Azure Virtual Desktop (クラシック) での Windows 7 仮想マシンのトラブルシューティング

>[!IMPORTANT]
>このコンテンツは、Azure Resource Manager Azure Virtual Desktop オブジェクトをサポートしていない Azure Virtual Desktop (クラシック) に適用されます。

この記事は、Azure Virtual Desktop セッション ホスト仮想マシン (VM) の構成時に発生する問題のトラブルシューティングを行うときに使用してください。

## <a name="known-issues"></a>既知の問題

Azure Virtual Desktop 上の Windows 7 では、次の機能はサポートされていません。

- 仮想化アプリケーション (RemoteApp)
- タイム ゾーン リダイレクト
- 自動 DPI スケール

Azure Virtual Desktop では、Windows 7 の完全なデスクトップのみを仮想化できます。

自動 DPI スケールはサポートされていませんが、リモート デスクトップ クライアントのアイコンを右クリックして、 **[解像度]** を選択すると、仮想マシンの解像度を手動で変更できます。

## <a name="error-cant-access-the-remote-desktop-user-group"></a>エラー:リモート デスクトップ ユーザー グループにアクセスできない

Azure Virtual Desktop でリモート デスクトップ ユーザー グループのお客様またはそのユーザーの資格情報が見つからない場合、次のエラー メッセージのいずれかが表示されることがあります。

- "このユーザーはリモート デスクトップ ユーザー グループのメンバーではありません"
- "リモート デスクトップ サービスを経由してサインインするためのアクセス許可を得る必要があります"

このエラーを解決するには、リモート デスクトップ ユーザー グループにユーザーを追加します。

1. Azure portal を開きます。
2. エラー メッセージが表示された仮想マシンを選択します。
3. **[コマンドの実行]** を選択します。
4. `<username>` を、追加するユーザーの名前に置き換えて、次のコマンドを実行します。

   ```cmd
   net localgroup "Remote Desktop Users" <username> /add
   ```