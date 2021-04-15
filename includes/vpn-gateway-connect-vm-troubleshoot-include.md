---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 3f689f24c80bcb49909bd28f077dc12520b4f47e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105105488"
---
VPN 接続を使って仮想マシンに接続する際に問題が発生した場合には、次のことを確認してください。

- VPN 接続が成功したことを確認します。
- VM のプライベート IP アドレスに接続できていることを確認します。
- プライベート IP アドレスを使って VM に接続できるものの、コンピューター名では接続できない場合には、DNS が正しく構成されているかどうかを確認します。 VM の名前解決の動作について詳しくは、[VM の名前解決](../articles/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)に関するページを参照してください。

ポイント対サイトで接続している場合は、さらに次の事柄を確認してください。

- 接続元のコンピューターのイーサネット アダプターに割り当てられている IPv4 アドレスを "ipconfig" でチェックします。 その IP アドレスが、接続先の VNet のアドレス範囲内または VPNClientAddressPool のアドレス範囲内にある場合、これを "アドレス空間の重複" といいます。 アドレス空間がこのように重複していると、ネットワーク トラフィックが Azure に到達せずローカル ネットワーク上に留まることになります。
- VNet に対して DNS サーバーの IP アドレスが指定された後に VPN クライアント構成パッケージが生成されたことを確認します。 DNS サーバーの IP アドレスを更新した場合は、新しい VPN クライアント構成パッケージを生成してインストールしてください。

RDP 接続のトラブルシューティングの詳細については、[VM に対するリモート デスクトップ接続のトラブルシューティング](/troubleshoot/azure/virtual-machines/troubleshoot-rdp-connection)に関するページを参照してください。