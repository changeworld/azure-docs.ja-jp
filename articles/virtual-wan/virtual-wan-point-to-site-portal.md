---
title: Azure Virtual WAN を使用して Azure へのポイント対サイト接続を作成する | Microsoft Docs
description: このチュートリアルでは、Azure Virtual WAN を使用して Azure へのポイント対サイト VPN 接続を作成する方法を学習します。
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: alzam
ms.openlocfilehash: 394ca0c244db751645eb56f50e7023fcee229876
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2019
ms.locfileid: "74807461"
---
# <a name="tutorial-create-a-user-vpn-connection-using-azure-virtual-wan"></a>チュートリアル:Azure Virtual WAN を使用してユーザー VPN 接続を作成する

このチュートリアルでは、Virtual WAN を使用して IPsec/IKE (IKEv2) または OpenVPN VPN 接続経由で Azure のリソースに接続する方法を示します。 この種類の接続では、クライアント コンピューターでクライアントを構成する必要があります。 Virtual WAN の詳細については、[Virtual WAN の概要](virtual-wan-about.md)に関するページを参照してください

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * WAN を作成する
> * ハブを作成する
> * P2S の構成を作成する
> * VPN クライアント プロファイルをダウンロードする
> * P2S の構成をハブに適用する
> * VNet をハブに接続する
> * VPN クライアント構成をダウンロードして適用する
> * 仮想 WAN を表示する
> * リソースの正常性を表示する

![Virtual WAN のダイアグラム](./media/virtual-wan-about/virtualwanp2s.png)

## <a name="before-you-begin"></a>開始する前に

構成を開始する前に、以下の条件を満たしていることを確認します。

* 接続先の仮想ネットワークが用意されていること。 オンプレミス ネットワークのどのサブネットも接続先の仮想ネットワークと重複していないことを確認してください。 Azure portal で仮想ネットワークを作成するには、[クイックスタート](../virtual-network/quick-create-portal.md)を参照してください。

* 仮想ネットワークに仮想ネットワーク ゲートウェイが存在しないこと。 仮想ネットワークにゲートウェイ (VPN または ExpressRoute のどちらか) が存在する場合は、すべてのゲートウェイを削除する必要があります。 代わりに、この構成では、仮想ネットワークが Virtual WAN ハブ ゲートウェイに接続されている必要があります。

* ハブ リージョンの IP アドレス範囲を取得します。 このハブは、Virtual WAN によって作成および使用される仮想ネットワークです。 ハブに指定するアドレス範囲が、接続先の既存の仮想ネットワークと重複することはできません。 さらに、オンプレミスで接続するアドレス範囲と重複することもできません。 オンプレミス ネットワーク構成に含まれている IP アドレス範囲になじみがない場合は、それらの詳細を提供できるだれかと調整してください。

* Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="wan"></a>仮想 WAN を作成する

ブラウザーから [Azure ポータル](https://portal.azure.com) に移動し、Azure アカウントでサインインします。

1. [仮想 WAN] ページに移動します。 ポータルで **+ [リソースの作成]** をクリックします。 検索ボックスに「**Virtual WAN**」と入力し、Enter キーを押します。
2. 結果から **[Virtual WAN]** を選択します。 [Virtual WAN] (仮想 WAN) ページで、 **[作成]** をクリックして [WAN の作成] ページを開きます。
3. **[WAN の作成]** ページの **[基本]** タブで、次のフィールドに入力します。

   ![Virtual WAN](./media/virtual-wan-point-to-site-portal/vwan.png)

   * **[サブスクリプション]** - 使用するサブスクリプションを選択します。
   * **[リソース グループ]** - 新規作成するか、または既存のものを使用します。
   * **[リソース グループの場所]** - ドロップダウンからリソースの場所を選択します。 WAN はグローバルなリソースであり、特定のリージョンに存在するものではありません。 ただし、作成する WAN リソースをより簡単に管理および検索するために、リージョンを選択する必要があります。
   * **[名前]** - WAN に付ける名前を入力します。
   * **種類:** Standard。 Basic WAN を作成する場合は、Basic ハブのみを作成できます。 Basic ハブでは、VPN サイト間接続のみが可能です。
4. フィールドへの入力を完了したら、 **[確認および作成]** を選択します。
5. 検証に合格したら、 **[作成]** を選択して仮想 WAN を作成します。

## <a name="site"></a>空の仮想ハブを作成する

1. 仮想 WAN で [ハブ] を選択し、 **[+ 新しいハブ]** をクリックします。

   ![新しいサイト](media/virtual-wan-point-to-site-portal/hub1.jpg)
2. [仮想ハブを作成する] ページで、次のフィールドに入力します。

   **[リージョン]** - 仮想ハブをデプロイするリージョンを選択します。

   **[名前]** - 仮想ハブの呼び出しに使う名前を入力します。

   **[ハブ プライベート アドレス空間]** - CIDR 表記のハブのアドレス範囲。

   ![新しいサイト](media/virtual-wan-point-to-site-portal/hub2.jpg)  
3. **[確認と作成]** をクリックします。
4. **[検証に成功しました]** ページで **[作成]** をクリックします。

## <a name="site"></a>P2S の構成を作成する

P2S 構成には、リモート クライアントを接続するためのパラメーターが定義されています。

1. **[すべてのリソース]** に移動します。
2. 作成した仮想 WAN をクリックします。
3. ページの上部にある **[+Create user VPN config]\(+ ユーザー VPN 構成の作成\)** をクリックして、 **[Create new User VPN configuration]\(新しいユーザー VPN 構成の作成\)** ページを開きます。

   ![新しいサイト](media/virtual-wan-point-to-site-portal/p2s1.jpg)
4. **[Create new User VPN configuration]\(新しいユーザー VPN 構成の作成\)** ページで、次のフィールドに入力します。

   **構成名**: 構成の呼称です。

   **トンネルの種類**: トンネルに使用するプロトコル。

   **ルート証明書名**: 証明書のわかりやすい名前。

   **公開証明書データ**: Base-64 でエンコードされた X.509 証明書データです。
  
   ![新しいサイト](media/virtual-wan-point-to-site-portal/p2s2.jpg)
5. **[作成]** をクリックして構成を作成します。

## <a name="hub"></a>ハブの割り当てを編集する

1. 仮想 WAN の下にある **[ハブ]** ブレードに移動します
2. VPN サーバーの構成を関連付けるハブを選択し、 **[...]** をクリックします

   ![新しいサイト](media/virtual-wan-point-to-site-portal/p2s4.jpg)
3. **[仮想ハブを編集する]** をクリックします。
4. **[ポイント対サイト ゲートウェイを含める]** チェック ボックスをオンにし、必要な **[ゲートウェイ スケール ユニット]** を選択します。

   ![新しいサイト](media/virtual-wan-point-to-site-portal/p2s2.jpg)
5. VPN クライアントの IP アドレスの割り当て元となる **[アドレス プール]** を入力します。
6. **[確認]** をクリックします
7. この操作は、完了するまで最大 30 分かかることがあります。

## <a name="device"></a>VPN プロファイルをダウンロードする

VPN プロファイルを使用してクライアントを構成します。

1. 仮想 WAN のページで、 **[User VPN configurations]\(ユーザー VPN の構成\)** をクリックします。
2. ページの上部にある **[Download user VPN config]\(ユーザー VPN 構成をダウンロードする\)** をクリックします。
3. ファイルの作成が完了したら、リンクをクリックしてファイルをダウンロードできます。
4. プロファイル ファイルを使用して VPN クライアントを構成します。

### <a name="configure-user-vpn-clients"></a>ユーザー VPN クライアントを構成する
ダウンロードしたプロファイルを使用して、リモート アクセス クライアントを構成します。 オペレーティング システムごとに手順は異なります。以下の正しい手順を実行してください。

#### <a name="microsoft-windows"></a>Microsoft Windows
##### <a name="openvpn"></a>OpenVPN

1. 公式 Web サイトから OpenVPN クライアントをダウンロードしてインストールします。
2. ゲートウェイの VPN プロファイルをダウンロードします。 この操作は、Azure portal の [User VPN configurations]\(ユーザー VPN の構成\) タブまたは PowerShell の New-AzureRmVpnClientConfiguration で実行できます。
3. プロファイルを展開します。 メモ帳で OpenVPN フォルダーの vpnconfig.ovpn 構成ファイルを開きます。
4. P2S クライアント証明書セクションに、base64 の P2S クライアント証明書の公開キーを指定します。 PEM 形式の証明書の場合、.cer ファイルを開き、証明書ヘッダー間にある base64 キーを上書きしてコピーします。 こちらの[証明書をエクスポートしてエンコードされた公開キーを取得する方法](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-certificates-point-to-site)に関するページを参照してください。
5. 秘密キー セクションに、base64 の P2S クライアント証明書の秘密キーを指定します。 秘密キーを抽出する方法については、こちらを参照してください。
6. その他のフィールドは変更しないでください。 クライアント入力に入力された構成を使用して VPN に接続します。
7. vpnconfig.ovpn ファイルを C:\Program Files\OpenVPN\config フォルダーにコピーします。
8. システム トレイの OpenVPN アイコンを右クリックし、[接続] をクリックします。

##### <a name="ikev2"></a>IKEv2

1. Windows コンピューターのアーキテクチャに対応する VPN クライアント構成ファイルを選択します。 64 ビットのプロセッサ アーキテクチャの場合は、"VpnClientSetupAmd64" インストーラー パッケージを選択します。 32 ビットのプロセッサ アーキテクチャの場合は、"VpnClientSetupX86" インストーラー パッケージを選択します。
2. パッケージをダブルクリックしてインストールします。 SmartScreen ポップアップが表示された場合は、[詳細]、[実行] の順にクリックします。
3. クライアント コンピューターで [ネットワークの設定] に移動し、[VPN] をクリックします。 VPN 接続により、その接続先の仮想ネットワークの名前が表示されます。
4. 接続を試行する前に、クライアント コンピューターにクライアント証明書をインストール済みであることを確認します。 ネイティブ Azure 証明書の認証タイプを使用する場合、認証にはクライアント証明書が必要です。 証明書の生成の詳細については、「[証明書の生成](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-certificates-point-to-site)」をご覧ください。 クライアント証明書のインストール方法については、クライアント証明書のインストールに関するページを参照してください。

## <a name="viewwan"></a>仮想 WAN を表示する

1. 仮想 WAN に移動します。
2. [概要] ページのマップ上の各ポイントは、ハブを表します。 任意のポイントにカーソルを置くと、ハブの正常性の概要が表示されます。
3. ハブと接続のセクションでは、ハブの状態、サイト、リージョン、VPN 接続の状態、および入出力バイト数を表示できます。

## <a name="viewhealth"></a>リソースの正常性を表示する

1. WAN に移動します。
2. WAN のページの **[サポート + トラブルシューティング]** セクションで、 **[正常性]** をクリックしてリソースを表示します。


## <a name="cleanup"></a>リソースをクリーンアップする

これらのリソースが不要になったら、[Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) を使用して、リソース グループとその中のすべてのリソースを削除できます。 "myResourceGroup" をリソース グループの名前に置き換えて、次の PowerShell コマンドを実行します。

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>次の手順

Virtual WAN の詳細については、[Virtual WAN の概要](virtual-wan-about.md)に関するページを参照してください。
