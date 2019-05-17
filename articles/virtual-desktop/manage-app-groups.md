---
title: Windows Virtual Desktop プレビューのアプリ グループを管理する - Azure
description: Windows Virtual Desktop プレビューのテナントを Azure Active Directory に設定する方法を説明します。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: f0cdd28be8c6e7390aa26fdc2dfbf32ec5542c2d
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2019
ms.locfileid: "65233904"
---
# <a name="tutorial-manage-app-groups-for-windows-virtual-desktop-preview"></a>チュートリアル:Windows Virtual Desktop プレビューのアプリ グループを管理する

Windows Virtual Desktop プレビューの新しいホスト プール向けに作成される既定のアプリ グループには、完全なデスクトップも公開されています。 加えて、ホスト プールには RemoteApp アプリケーション グループ (複数可) を作成することができます。 このチュートリアルに沿って作業すれば、RemoteApp アプリ グループを作成して、独自のスタート メニュー アプリを公開することができます。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * RemoteApp グループを作成する。
> * RemoteApp へのアクセスを許可する。

作業を開始する前に、PowerShell セッションで使用する Windows Virtual Desktop モジュール [Windows Virtual Desktop PowerShell モジュールをダウンロードしてインポート](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview)します (まだ済んでいない場合)。

## <a name="create-a-remoteapp-group"></a>RemoteApp グループを作成する

1. 次の PowerShell コマンドレットを実行して、新しい空の RemoteApp アプリ グループを作成します。

   ```powershell
   New-RdsAppGroup <tenantname> <hostpoolname> <appgroupname> -ResourceType "RemoteApp"
   ```

2. (省略可) アプリ グループが作成されたことを確認したければ、次のコマンドレットを実行すると、ホスト プールのすべてのアプリ グループが一覧表示されます。

   ```powershell
   Get-RdsAppGroup <tenantname> <hostpoolname>
   ```

3. 次のコマンドレットを実行して、ホスト プールの仮想マシン イメージにあるスタート メニュー アプリの一覧を取得します。 **FilePath**、**IconPath**、**IconIndex** など、公開したいアプリケーションの重要な情報の値を書き留めます。

   ```powershell
   Get-RdsStartMenuApp <tenantname> <hostpoolname> <appgroupname>
   ```
   
4. 次のコマンドレットを実行して、appalias に基づくアプリケーションをインストールします。 appalias は、手順 3. の出力を実行すると利用できるようになります。

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -AppAlias <appalias>
   ```

5. (省略可) 次のコマンドレットを実行して、手順 1. で作成したアプリケーション グループに新しい RemoteApp を公開します。

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -Filepath <filepath>  -IconPath <iconpath> -IconIndex <iconindex>
   ```

6. アプリが公開されたことを確認するために、次のコマンドレットを実行します。

   ```powershell
   Get-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname>
   ```

7. このアプリ グループに公開するアプリケーションごとに手順 1. から手順 5. を繰り返します。
8. 次のコマンドレットを実行して、アプリ グループ内の RemoteApp へのアクセスをユーザーに許可します。

   ```powershell
   Add-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname> -UserPrincipalName <userupn>
   ```

## <a name="next-steps"></a>次の手順

このチュートリアルでは、アプリ グループを作成して、それに RemoteApp を設定し、アプリ グループにユーザーを割り当てる方法について説明しました。 Windows Virtual Desktop にサインインする方法について詳しくは、引き続き Windows Virtual Desktop への接続方法に関するページを参照してください。

- [Windows 7 および Windows 10 上でリモート デスクトップ クライアントに接続する](connect-windows-7-and-10.md)
- [Windows Virtual Desktop プレビュー Web クライアントに接続する](connect-web.md)
