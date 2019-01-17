---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 01/09/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4d9f05ad1913646e7ca09690af965042817ef7ec
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/10/2019
ms.locfileid: "54193417"
---
この記事では、PowerShell コマンドレットを使用します。 コマンドレットを実行するには、無料のインタラクティブ シェルである Azure Cloud Shell を使用できます。 一般的な Azure ツールが事前にインストールされており、アカウントで使用できるように構成されています。 **[コピー]** をクリックしてコードをコピーし、Cloud Shell に貼り付けて Enter キーを押すだけで、コードを実行することができます。 Cloud Shell は、次のようにいくつかの方法で起動することができます。

|  |   |
|-----------------------------------------------|---|
| コード ブロックの右上隅にある **[使ってみる]** をクリックします。 | ![この記事の Cloud Shell](./media/vpn-gateway-cloud-shell-powershell/cloud-shell-powershell-try-it.png) |
| ブラウザーで Cloud Shell を開きます。 | [![https://shell.azure.com/powershell](./media/vpn-gateway-cloud-shell-powershell/launchcloudshell.png)](https://shell.azure.com/powershell) |
| Azure Portal の右上のメニューの **[Cloud Shell]** ボタンをクリックします。 | [![ポータルの Cloud Shell](./media/vpn-gateway-cloud-shell-powershell/cloud-shell-menu.png)](https://portal.azure.com) |
|  |  |

Azure Cloud Shell を使用しない場合は、代わりにローカルに PowerShell をインストールできます。 PowerShell をローカルにインストールして使用する場合は、必ず Azure Resource Manager PowerShell コマンドレットの最新版をインストールしてください。 PowerShell コマンドレットは頻繁に更新されるため、最新の機能を利用するには、通常、PowerShell コマンドレットを更新する必要があります。 PowerShell コマンドレットを更新しないと、値の指定が失敗することがあります。 

ローカルで実行している PowerShell のバージョンを確認するには、' Get-Module-ListAvailable AzureRM' コマンドレットを使用します。 更新するには、「[Install the Azure PowerShell module (Azure PowerShell モジュールのインストール)](/powershell/azure/azurerm/install-azurerm-ps)」を参照してください。 詳細については、「 [Azure PowerShell のインストールと構成の方法](/powershell/azure/azurerm/overview)」を参照してください。
