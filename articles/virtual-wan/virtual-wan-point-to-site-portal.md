---
title: チュートリアル:Azure Virtual WAN を使用し、Azure へのポイント対サイト接続を作成する
description: このチュートリアルでは、Azure Virtual WAN を使用して Azure へのユーザー VPN (ポイント対サイト) 接続を作成する方法について説明します。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 08/23/2021
ms.author: cherylmc
ms.openlocfilehash: e08123ce666efebf7db2e4c07167125a76b46609
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/24/2021
ms.locfileid: "122779907"
---
# <a name="tutorial-create-a-user-vpn-connection-using-azure-virtual-wan"></a>チュートリアル:Azure Virtual WAN を使用してユーザー VPN 接続を作成する

このチュートリアルでは、Virtual WAN を使用して、ユーザー VPN (P2S) 構成で、OpenVPN または IPsec/IKE (IKEv2) VPN 接続を介して Azure のリソースに接続する方法を説明します。 この種類の接続には、接続する各クライアント コンピューター上でネイティブの VPN クライアントを構成する必要があります。
* Azure AD 認証を使用してユーザー VPN 接続を作成する場合は、代わりに「[ユーザー VPN 接続の構成 - Azure Active Directory 認証](virtual-wan-point-to-site-azure-ad.md)」をご覧ください。
* Virtual WAN の詳細については、[Virtual WAN の概要](virtual-wan-about.md)に関するページを参照してください。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * 仮想 WAN を作成する
> * ユーザー VPN 構成を作成する
> * 仮想ハブとゲートウェイを作成する
> * クライアント構成ファイルを生成する
> * VPN クライアントの構成
> * VNet に接続する
> * 仮想 WAN を表示する
> * 設定を変更する

:::image type="content" source="./media/virtual-wan-about/virtualwanp2s.png" alt-text="仮想 WAN の図。":::

## <a name="prerequisites"></a>前提条件

[!INCLUDE [Before beginning](../../includes/virtual-wan-before-include.md)]

## <a name="create-a-virtual-wan"></a><a name="wan"></a>仮想 WAN を作成する

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="create-a-user-vpn-configuration"></a><a name="p2sconfig"></a>ユーザー VPN 構成を作成する

ユーザー VPN (P2S) 構成では、接続するリモート クライアントのパラメーターが定義されています。 従う手順は、使用する認証方法によって異なります。

以下のステップでは、認証方法を選択する際に、3 つの選択肢があります。 各方法には特定の要件があります。 次のいずれかの方法を選択し、手順を完了します。

* **Azure 証明書:** この構成では、証明書が必要です。 証明書を生成または取得する必要があります。 クライアント証明書はクライアントごとに必要です。 また、ルート証明書情報 (公開キー) をアップロードする必要があります。 必要な証明書の詳細については、[証明書の生成とエクスポート](certificates-point-to-site.md)に関するページを参照してください。

* **Azure Active Directory 認証:** この構成に必要な特定の手順を含む、「[ユーザー VPN 接続の構成 - Azure Active Directory 認証](virtual-wan-point-to-site-azure-ad.md)」を参照してください。

* **Radius ベースの認証:** Radius サーバーの IP、Radius サーバーのシークレット、および証明書の情報を取得します。

### <a name="configuration-steps"></a>構成手順

[!INCLUDE [Create P2S configuration](../../includes/virtual-wan-p2s-configuration-include.md)]

## <a name="create-a-virtual-hub-and-gateway"></a><a name="hub"></a>仮想ハブとゲートウェイを作成する

[!INCLUDE [Create hub](../../includes/virtual-wan-p2s-hub-include.md)]

## <a name="generate-client-configuration-files"></a><a name="download"></a>クライアント構成ファイルを生成する

ユーザー VPN (P2S) を使用して VNet に接続する場合には、接続元のオペレーティング システムにネイティブにインストールされている VPN クライアントを使用します。 VPN クライアントに必要なすべての構成設定は、VPN クライアント構成 ZIP ファイルに含まれています。 ZIP ファイル内の設定を使用することで、VPN クライアントを簡単に構成できます。 生成する VPN クライアント構成ファイルは、お使いのゲートウェイのユーザー VPN 構成に固有です。 このセクションでは、VPN クライアントの構成に使用するファイルを生成してダウンロードします。

[!INCLUDE [Download profile](../../includes/virtual-wan-p2s-download-profile-include.md)]

## <a name="configure-vpn-clients"></a><a name="configure-client"></a>VPN クライアントの構成

ダウンロードしたプロファイル パッケージを使用して、リモート アクセス VPN クライアントを構成します。 オペレーティング システムごとに手順は異なります。 お使いシステムに適用される指示に従ってください。
クライアントの構成が完了したら、接続できます。

[!INCLUDE [Configure clients](../../includes/virtual-wan-p2s-configure-clients-include.md)]

## <a name="connect-vnet-to-hub"></a><a name="connect-vnet"></a>VNet をハブに接続する

このセクションでは、仮想ハブと VNet の間の接続を作成します。 このチュートリアルでは、ルーティング設定を構成する必要はありません。

[!INCLUDE [Connect virtual network](../../includes/virtual-wan-connect-vnet-hub-include.md)]

## <a name="view-a-virtual-wan"></a><a name="viewwan"></a>仮想 WAN を表示する

1. お使いの **仮想 WAN** に移動します。

1. **[概要]** ページで、マップ上の各ポイントはハブを表します。

1. **[ハブと接続]** セクションでは、ハブの状態、サイト、リージョン、VPN 接続の状態、入出力バイト数を確認できます。

## <a name="modify-settings"></a>設定を変更する

### <a name="modify-client-address-pool"></a><a name="address-pool"></a>クライアント アドレス プールを変更する

[!INCLUDE [Modify client address pool](../../includes/virtual-wan-client-address-pool-include.md)]

### <a name="modify-dns-servers"></a><a name="dns"></a>DNS サーバーを変更する

1. **[仮想ハブ] -> [ユーザ VPN (ポイントからサイトへ)]** に移動します。

1. **[カスタム DNS サーバー]** の横にある値をクリックして **[ユーザー VPN ゲートウェイの編集]** ページを開きます。

1. **[ユーザー VPN ゲートウェイの編集]** ページで、 **[カスタム DNS サーバー]** フィールドを編集します。 DNS サーバーの IP アドレスを、 **[カスタム DNS サーバー]** テキスト ボックスに入力します。 DNS サーバーは 5 個まで指定できます。

1. ページの下部にある **[編集]** をクリックして、設定を検証します。

1. **[確認]** をクリックして設定を保存します。 このページでの変更は、完了まで最大 30 分かかる場合があります。

## <a name="clean-up-resources"></a><a name="cleanup"></a>リソースをクリーンアップする

作成したリソースは、不要になったら削除してください。 Virtual WAN リソースのいくつかは、依存関係に応じた特定の順序で削除する必要があります。 削除が完了するまでに 30 分程度かかる場合があります。

[!INCLUDE [Delete resources](../../includes/virtual-wan-resource-cleanup.md)]

## <a name="next-steps"></a>次のステップ


> [!div class="nextstepaction"]
> * [スポーク VNet にあるリソースへの安全なアクセスを管理する](manage-secure-access-resources-spoke-p2s.md)
