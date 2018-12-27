---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/17/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 84bfec375878fe31e085f7de3cd4a41ace645c41
ms.sourcegitcommit: 668b486f3d07562b614de91451e50296be3c2e1f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/19/2018
ms.locfileid: "49458652"
---
"Get-AzureRmVirtualNetworkGatewayConnection" コマンドレットを使用して、接続が成功したことを確認できます。"-Debug" は指定しても指定しなくてもかまいません。 

1. 次のコマンドレットを使用します。値は実際の値に置き換えてください。 プロンプトが表示されたら、"A" を選択して "すべて" (All) を実行します。 この例では、テストする接続の名前が "-Name" で示されています。

  ```azurepowershell-interactive
  Get-AzureRmVirtualNetworkGatewayConnection -Name VNet1toSite1 -ResourceGroupName TestRG1
  ```
2. コマンドレットの実行後、値を確認します。 以下の例では、接続状態は "Connected" と表示され、受信バイトと送信バイトを確認できます。
   
  ```
  "connectionStatus": "Connected",
  "ingressBytesTransferred": 33509044,
  "egressBytesTransferred": 4142431
  ```