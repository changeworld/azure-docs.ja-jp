---
title: チュートリアル:Azure Virtual WAN を使用し、Azure へのポイント対サイト接続を作成する
description: このチュートリアルでは、Azure Virtual WAN を使用して Azure へのポイント対サイト VPN 接続を作成する方法を学習します。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 10/06/2020
ms.author: cherylmc
ms.openlocfilehash: 3d03d0267ff4fb16042d5cc2016e87139b88281a
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92056584"
---
# <a name="tutorial-create-a-user-vpn-connection-using-azure-virtual-wan"></a>チュートリアル:Azure Virtual WAN を使用してユーザー VPN 接続を作成する

このチュートリアルでは、Virtual WAN を使用して IPsec/IKE (IKEv2) または OpenVPN VPN 接続経由で Azure のリソースに接続する方法を示します。 この種類の接続では、クライアント コンピューターでクライアントを構成する必要があります。 Virtual WAN の詳細については、[Virtual WAN の概要](virtual-wan-about.md)に関するページを参照してください

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * WAN を作成する
> * P2S の構成を作成する
> * ハブを作成する
> * DNS サーバーの指定
> * VPN クライアント プロファイルをダウンロードする
> * 仮想 WAN を表示する

![Virtual WAN のダイアグラム](./media/virtual-wan-about/virtualwanp2s.png)

## <a name="prerequisites"></a>前提条件

[!INCLUDE [Before beginning](../../includes/virtual-wan-before-include.md)]

## <a name="create-a-virtual-wan"></a><a name="wan"></a>仮想 WAN を作成する

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="create-a-p2s-configuration"></a><a name="p2sconfig"></a>P2S の構成を作成する

ポイント対サイト (P2S) 構成では、リモート クライアントを接続するためのパラメーターを定義します。

[!INCLUDE [Create client profiles](../../includes/virtual-wan-p2s-configuration-include.md)]

## <a name="create-hub-with-point-to-site-gateway"></a><a name="hub"></a>ポイント対サイト ゲートウェイを持つハブを作成する

[!INCLUDE [Create hub](../../includes/virtual-wan-p2s-hub-include.md)]

## <a name="specify-dns-server"></a><a name="dns"></a>DNS サーバーを指定する

Virtual WAN ユーザー VPN ゲートウェイでは、最大 5 つの DNS サーバーを指定できます。 これはハブの作成プロセス時に構成するか、後で変更することができます。 そのためには、仮想ハブを見つける必要があります。 **[ユーザー VPN (ポイントからサイトへ)]** で **[構成]** を選択し、 **[Custom DNS Servers]\(カスタム DNS サーバー\)** ボックスに DNS サーバーの IP アドレスを入力します。

   :::image type="content" source="media/virtual-wan-point-to-site-portal/custom-dns.png" alt-text="カスタム DNS" lightbox="media/virtual-wan-point-to-site-portal/custom-dns-expand.png":::

## <a name="download-vpn-profile"></a><a name="download"></a>VPN プロファイルをダウンロードする

VPN プロファイルを使用してクライアントを構成します。

[!INCLUDE [Download profile](../../includes/virtual-wan-p2s-download-profile-include.md)]

### <a name="configure-user-vpn-clients"></a>ユーザー VPN クライアントを構成する

ダウンロードしたプロファイルを使用して、リモート アクセス クライアントを構成します。 オペレーティング システムごとに手順は異なります。お使いのシステムに適用される手順に従ってください。

[!INCLUDE [Configure clients](../../includes/virtual-wan-p2s-configure-clients-include.md)]

## <a name="view-your-virtual-wan"></a><a name="viewwan"></a>仮想 WAN を表示する

1. 仮想 WAN に移動します。
1. **[概要]** ページで、マップ上の各ポイントはハブを表します。
1. **[ハブと接続]** セクションでは、ハブの状態、サイト、リージョン、VPN 接続の状態、入出力バイト数を確認できます。

## <a name="clean-up-resources"></a><a name="cleanup"></a>リソースをクリーンアップする

これらのリソースが不要になったら、[Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) を使用して、リソース グループとその中のすべてのリソースを削除できます。 "myResourceGroup" をリソース グループの名前に置き換えて、次の PowerShell コマンドを実行します。

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>次のステップ

次に、Virtual WAN の詳細については下記を参照してください。

> [!div class="nextstepaction"]
> * [Virtual WAN の FAQ](virtual-wan-faq.md)
