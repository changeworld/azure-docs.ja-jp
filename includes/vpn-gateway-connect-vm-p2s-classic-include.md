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
ms.openlocfilehash: e49cfe786272b34675ca377808e2961e61a757e4
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2018
ms.locfileid: "30197586"
---
リモート デスクトップ接続を作成すると、VNet にデプロイされている VM に接続できます。 VM に接続できるかどうかを初めて確認する際に最も良い方法は、その VM のコンピューター名ではなく、プライベート IP アドレスを使って接続してみることです。 この方法であれば、名前の解決が適切に構成されているかではなく、VM に接続できるかどうかをテストすることができます。 

1. ご利用の VM のプライベート IP アドレスを特定します。 VM のプライベート IP アドレスは、Azure Portal で VM のプロパティを表示するか、PowerShell を使うと確認できます。
2. ポイント対サイト VPN 接続を使って VNet に接続していることを確認します。 
3. タスク バーの検索ボックスに「RDP」または「リモート デスクトップ接続」と入力してリモート デスクトップ接続を開き、リモート デスクトップ接続を選択します。 このほか、PowerShell で "mstsc" コマンドを使ってリモート デスクトップ接続を開くこともできます。 
3. リモート デスクトップ接続で、VM のプライベート IP アドレスを入力します。 必要に応じて [オプションの表示] をクリックして追加の設定を済ませたら、接続します。

### <a name="to-troubleshoot-an-rdp-connection-to-a-vm"></a>VM に対する RDP 接続をトラブルシューティングするには

VPN 接続を使って仮想マシンに接続する際に問題が発生した場合には、いくつかの点を確認する必要があります。 トラブルシューティングの詳細については、[VM へのリモート デスクトップ接続のトラブルシューティング](../articles/virtual-machines/windows/troubleshoot-rdp-connection.md)に関するページを参照してください。

- VPN 接続が成功したことを確認します。
- VM のプライベート IP アドレスに接続できていることを確認します。
- 接続元のコンピューターのイーサネット アダプターに割り当てられている IPv4 アドレスを "ipconfig" でチェックします。 その IP アドレスが、接続先の VNet のアドレス範囲内または VPNClientAddressPool のアドレス範囲内にある場合、これを "アドレス空間の重複" といいます。 アドレス空間がこのように重複していると、ネットワーク トラフィックが Azure に到達せずローカル ネットワーク上に留まることになります。
- プライベート IP アドレスを使って VM に接続できるものの、コンピューター名では接続できない場合には、DNS が正しく構成されているかどうかを確認します。 VM の名前解決の動作について詳しくは、[VM の名前解決](../articles/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)に関するページを参照してください。
- VNet に対して DNS サーバーの IP アドレスが指定された後に VPN クライアント構成パッケージが生成されたことを確認します。 DNS サーバーの IP アドレスを更新した場合は、新しい VPN クライアント構成パッケージを生成してインストールしてください。