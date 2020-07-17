---
title: Windows Virtual Desktop 上の Windows 7 仮想マシン - Azure
description: Windows Virtual Desktop 環境で Windows 7 仮想マシン (VM) の問題を解決する方法。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 74f2e22bcc9d75070e4f7af304f92d9c5640ca7a
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/30/2020
ms.locfileid: "82614224"
---
# <a name="troubleshoot-windows-7-virtual-machines-in-windows-virtual-desktop"></a>Windows Virtual Desktop で Windows 7 仮想マシンのトラブルシューティングを行う

>[!IMPORTANT]
>この記事の内容は、Azure Resource Manager Windows Virtual Desktop オブジェクトをサポートしていない Fall 2019 リリースに適用されます。

この記事は、Windows Virtual Desktop セッション ホスト仮想マシン (VM) の構成中に発生する問題を解決するときにご利用ください。

## <a name="known-issues"></a>既知の問題

Windows Virtual Desktop 上の Windows 7 では、次の機能はサポートされていません。

- 仮想化アプリケーション (RemoteApp)
- タイム ゾーン リダイレクト
- 自動 DPI スケール

Windows Virtual Desktop では、Windows 7 の完全なデスクトップのみを仮想化できます。

自動 DPI スケールはサポートされていませんが、リモート デスクトップ クライアントのアイコンを右クリックして、 **[解像度]** を選択すると、仮想マシンの解像度を手動で変更できます。

## <a name="error-cant-access-the-remote-desktop-user-group"></a>エラー:リモート デスクトップ ユーザー グループにアクセスできない

Windows Virtual Desktop でリモート デスクトップ ユーザー グループ内のお客様またはお客様のユーザーの資格情報を見つけられない場合は、次のようなエラー メッセージのいずれかが表示されることがあります。

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