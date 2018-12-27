---
title: リモート デスクトップを Linux VM にアタッチする | Microsoft Docs
description: リモート デスクトップをインストールして、クラシック デプロイ モデルで Microsoft Azure Linux VM に接続するように構成する方法を説明します
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: 34348659-ddb7-41da-82d6-b5885859e7e4
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: cynthn
ms.openlocfilehash: 5e68774c3edb7d82fef388c593a6b96c52857be6
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2018
ms.locfileid: "37927742"
---
# <a name="using-remote-desktop-to-connect-to-a-microsoft-azure-linux-vm"></a>リモート デスクトップを使用して Microsoft Azure Linux VM に接続する
> [!IMPORTANT] 
> Azure には、リソースの作成と操作に関して、 [Resource Manager とクラシック](../../../resource-manager-deployment-model.md)の 2 種類のデプロイメント モデルがあります。 この記事では、クラシック デプロイ モデルの使用方法について説明します。 最新のデプロイメントでは、リソース マネージャー モデルを使用することをお勧めします。 この記事の更新された Resource Manager のバージョンについては、[こちら](../use-remote-desktop.md)をご覧ください。

## <a name="overview"></a>概要
RDP (リモート デスクトップ プロトコル) は、Windows の専用プロトコルです。 RDP を使用して Linux VM (仮想マシン) をリモート接続するにはどうすればよいでしょうか。

このガイドを読めば、その答えがわかります。 RDP は、Microsoft Azure Linux VM に xrdp をインストールして構成するのに役立ちます。これにより、Windows コンピューターからリモート デスクトップを使って接続できます。 このガイドでは、例として Ubuntu または OpenSUSE を実行する Linux VM を使用します。

xrdp ツールはオープン ソースの RDP サーバーであり、これを使うと、Windows コンピューターからリモート デスクトップを使って Linux サーバーに接続できます。 RDP のパフォーマンスは、VNC (Virtual Network Computing) より優れています。 VNC は JPEG 品質のグラフィックスを使ってレンダリングを行い、遅い場合があるのに対し、RDP は高速で非常に鮮明です。

> [!NOTE]
> Linux を実行している既存の Microsoft Azure VM が必要です。 Linux VM を作成してセットアップするには、[Azure Linux VM のチュートリアル](createportal-classic.md)を参照してください。
> 
> 

## <a name="create-an-endpoint-for-remote-desktop"></a>リモート デスクトップ用のエンドポイントを作成する
このドキュメントでは、リモート デスクトップに既定のエンドポイント 3389 を使用します。次のように、Linux VM に対する `Remote Desktop` としてエンドポイント 3389 を設定します。

![image](./media/remote-desktop/endpoint-for-linux-server.png)

VM のエンドポイントを設定する方法がわからない場合は、[こちらのガイダンス](setup-endpoints.md)をご覧ください。

## <a name="install-gnome-desktop"></a>Gnome デスクトップをインストールする
`putty` を使って Linux VM に接続し、`Gnome Desktop` をインストールします。

Ubuntu の場合は、次のコマンドを使用します。

```bash
sudo apt-get update
sudo apt-get install ubuntu-desktop
```

OpenSUSE の場合は、次のコマンドを使用します。

```bash
sudo zypper install gnome-session
```

## <a name="install-xrdp"></a>xrdp をインストールする
Ubuntu の場合は、次のコマンドを使用します。

```bash
sudo apt-get install xrdp
```

OpenSUSE の場合は、次のコマンドを使用します。

> [!NOTE]
> 次のコマンドで使っているバージョンで、OpenSUSE のバージョンを更新します。 次の例は `OpenSUSE 13.2` についてです。
> 
> 

```bash
sudo zypper in http://download.opensuse.org/repositories/X11:/RemoteDesktop/openSUSE_13.2/x86_64/xrdp-0.9.0git.1401423964-2.1.x86_64.rpm
sudo zypper install tigervnc xorg-x11-Xvnc xterm remmina-plugin-vnc
```

## <a name="start-xrdp-and-set-xdrp-service-at-boot-up"></a>xrdp を開始して起動時に xdrp サービスを設定する
OpenSUSE の場合は、次のコマンドを使用します。

```bash
sudo systemctl start xrdp
sudo systemctl enable xrdp
```

Ubuntu の場合は、インストール後の自動起動時に xrdp が開始され、有効になります。

## <a name="using-xfce-if-you-are-using-an-ubuntu-version-later-than-ubuntu-1204lts"></a>Ubuntu 12.04LTS 以降のバージョンの Ubuntu を使用している場合に xfce を使用する
現在のバージョンの xrdp は Ubuntu 12.04LTS より後のバージョンの Ubuntu に対して Gnome デスクトップをサポートしていないため、代わりに `xfce` デスクトップを使います。

`xfce` をインストールするには、次のコマンドを使います。

```bash
sudo apt-get install xubuntu-desktop
```

次に、以下のコマンドを使って `xfce` を有効にします。

```bash
echo xfce4-session >~/.xsession
```

構成ファイル `/etc/xrdp/startwm.sh` を編集します。

```bash
sudo vi /etc/xrdp/startwm.sh   
```

行 `/etc/X11/Xsession` の前に行 `xfce4-session` を追加します。

xrdp サービスを再起動するには、次のコマンドを使います。

```bash
sudo service xrdp restart
```

## <a name="connect-your-linux-vm-from-a-windows-machine"></a>Windows コンピューターから Linux VM を接続する
Windows コンピューターでリモート デスクトップ クライアントを起動し、Linux VM の DNS 名を入力します。 または、Azure Portal で VM のダッシュボードに移動し、[`Connect`] をクリックして Linux VM に接続します。 その場合は、ログイン ウィンドウが表示されます。

![image](./media/remote-desktop/no2.png)

Linux VM のユーザー名とパスワードでログインします。

## <a name="next-steps"></a>次の手順
xrdp の使い方の詳細については、[http://www.xrdp.org/](http://www.xrdp.org/) を参照してください。
