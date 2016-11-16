---
title: "Azure Portal を使用した Azure Virtual Network へのポイント対サイト VPN ゲートウェイ接続の構成 | Microsoft Docs"
description: "Azure Portal を使用してポイント対サイト VPN ゲートウェイ接続を作成することで、Azure Virtual Network に安全に接続します。"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: carmonm
editor: 
tags: azure-service-management
ms.assetid: 65e14579-86cf-4d29-a6ac-547ccbd743bd
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/17/2016
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 87d52de2d6ccb80390f8680371527a23904c5bb0


---
# <a name="configure-a-pointtosite-connection-to-a-vnet-using-the-azure-portal"></a>Azure Portal を使用した VNet へのポイント対サイト接続の構成
> [!div class="op_single_selector"]
> * [Resource Manager - Azure Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [クラシック - Azure Portal](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
> 
> 

この記事では、Azure Portal を使用して、ポイント対サイト接続を備えた VNet をクラシック デプロイメント モデルで作成する手順を説明します。 ポイント対サイト (P2S) 構成では、個々のクライアント コンピューターから仮想ネットワークへのセキュリティで保護された接続を作成することができます。 P2S 接続は、自宅や会議室など、リモートの場所から VNet に接続する場合に便利です。 また、仮想ネットワークに接続するクライアントの数がごくわずかである場合にも便利です。

ポイント対サイト接続を機能させるために、VPN デバイスや公開 IP アドレスは必要ありません。 VPN 接続を確立するには、クライアント コンピューターからの接続を開始します。 ポイント対サイト接続の詳細については、「[VPN Gateway に関する FAQ](vpn-gateway-vpn-faq.md#point-to-site-connections)」と「[VPN ゲートウェイについて](vpn-gateway-about-vpngateways.md#point-to-site)」を参照してください。

### <a name="deployment-models-and-methods-for-p2s-connections"></a>P2S 接続のデプロイメント モデルとデプロイ方法
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

次の表は、2 つのデプロイメント モデルと、P2S 構成で使用可能なデプロイ方法を示しています。 構成手順を説明した記事が利用できるようになったら、表から直接リンクできるようにします。

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-table-point-to-site-include.md)]

## <a name="basic-workflow"></a>基本的なワークフロー
![ポイント対サイトのダイアログ](./media/vpn-gateway-howto-point-to-site-rm-ps/p2srm.png "point-to-site")

以降のセクションでは、仮想ネットワークに対してセキュリティで保護されたポイント対サイト接続を作成する方法を説明します。 

1. 仮想ネットワークと VPN ゲートウェイの作成
2. 証明書の生成
3. .cer ファイルのアップロード
4. VPN クライアント構成パッケージの生成
5. クライアント コンピューターの構成
6. Connect to Azure

### <a name="example-settings"></a>設定例
以下の設定例を使用できます。

* **名前: VNet1**
* **アドレス空間: 192.168.0.0/16**
* **サブネット名: FrontEnd**
* **サブネットのアドレス範囲: 192.168.1.0/24**
* **サブスクリプション:** サブスクリプションが複数ある場合は、適切なサブスクリプションを使用していることを確認します。
* **リソース グループ: TestRG**
* **場所: 米国東部**
* **接続の種類: ポイント対サイト**
* **クライアント アドレス空間: 172.16.201.0/24**。 このポイント対サイト接続を利用して VNet に接続する VPN クライアントは、指定したプールから IP アドレスを受け取ります。
* **GatewaySubnet: 192.168.200.0/24**。 ゲートウェイ サブネット名は、"GatewaySubnet" にする必要があります。
* **サイズ:** 使用するゲートウェイ SKU を選択します。
* **ルーティングの種類: 動的**

## <a name="a-namevnetvpnasection-1-create-a-virtual-network-and-a-vpn-gateway"></a><a name="vnetvpn"></a>セクション 1 - 仮想ネットワークと VPN ゲートウェイの作成
### <a name="a-namecreatevnetapart-1-create-a-virtual-network"></a><a name="createvnet"></a>パート 1: 仮想ネットワークの作成
まだ仮想ネットワークがない場合は作成します。 スクリーンショットは例として示されています。 サンプルの値は必ず実際の値に変更してください。 Azure Portal を使用して VNet を作成するには、以下の手順に従ってください。 

1. ブラウザーから [Azure Portal](http://portal.azure.com) に移動します。必要であれば Azure アカウントでサインインします。
2. **[新規]**をクリックします。 **[Marketplace を検索]** フィールドに「仮想ネットワーク」と入力します。 検索結果の一覧から **[仮想ネットワーク]** を探してクリックし、**[仮想ネットワーク]** ブレードを開きます。
   
    ![[仮想ネットワーク] ブレードの検索](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/newvnetportal700.png "Search for virtual network blade")
3. [仮想ネットワーク] ブレードの下の方にある **[デプロイ モデルの選択]** の一覧で、**[クラシック]** を選択し、**[作成]** をクリックします。
   
    ![デプロイメント モデルの選択](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/selectmodel.png "Select deployment model")
4. **[仮想ネットワークの作成]** ブレードで、VNet の設定を構成します。 このブレードでは、最初のアドレス空間と 1 つのサブネット アドレスの範囲を追加します。 VNet の作成が完了したら、戻って、さらにサブネットとアドレス空間を追加できます。
   
    ![仮想ネットワーク ブレードの作成](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/vnet125.png "Create virtual network blade")
5. **サブスクリプション** が正しいものであることを確認します。 ドロップダウンを使用して、サブスクリプションを変更できます。
6. **[リソース グループ]** をクリックし、既存のリソース グループを選択するか、新しいリソース グループの名前を入力して新しく作成します。 新しいグループを作成する場合は、計画した構成値に基づいて、リソース グループに名前を付けます。 リソース グループの詳細については、「[Azure Resource Manager の概要](../azure-resource-manager/resource-group-overview.md#resource-groups)」を参照してください。
7. 次に、VNet の **[場所]** 設定を選択します。 この場所の設定によって、VNet にデプロイしたリソースがどこに配置されるかが決まります。
8. ダッシュボードで VNet を簡単に検索できるようにするには、**[ダッシュボードにピン留めする]** を選択します。その後、**[作成]** をクリックします。
   
    ![Pin to dashboard](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/pintodashboard150.png "Pin to dashboard")
9. [作成] をクリックした後で、VNet の進捗状況を反映するタイルがダッシュボードに表示されます。 タイルは、VNet の作成が進むに従って変化します。
   
    ![仮想ネットワーク タイルの作成](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/deploying150.png "Creating virtual network tile")
10. 仮想ネットワークを作成した後は、名前解決を処理するために、DNS サーバーの IP アドレスを追加できます。 仮想ネットワークの設定を開き、DNS サーバーをクリックして、使用する DNS サーバーの IP アドレスを追加します。 この設定で、新しい DNS サーバーが作成されることはありません。 リソースが通信できる DNS サーバーを必ず追加してください。

仮想ネットワークが作成されると、Azure クラシック ポータルの [ネットワーク] ページの **[状態]** に **[作成済み]** と表示されます。

### <a name="a-namegatewayapart-2-create-gateway-subnet-and-a-dynamic-routing-gateway"></a><a name="gateway"></a>パート 2: ゲートウェイ サブネットと動的ルーティング ゲートウェイの作成
この手順では、ゲートウェイ サブネットと動的ルーティング ゲートウェイを作成します。 クラシック デプロイメント モデルの Azure Portal では、ゲートウェイ サブネットとゲートウェイを同じ構成ブレードで作成できます。

1. ポータルで、ゲートウェイを作成する仮想ネットワークに移動します。
2. 仮想ネットワークのブレードの **[概要]** ブレードにある [VPN 接続] で、**[ゲートウェイ]** をクリックします。
   
    ![ゲートウェイを作成するには、ここをクリックしてください](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/beforegw125.png "Click here to create a gateway")
3. **[新しい VPN 接続]** ブレードで、**[ポイント対サイト]** を選択します。
   
    ![P2S 接続の種類](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/newvpnconnect.png "P2S connection type")
4. **[クライアント アドレス空間]** で、IP アドレスの範囲を追加します。 これは、VPN クライアントが接続時に受け取る IP アドレスの範囲です。 自動入力される範囲を削除して、独自の範囲を追加します。
   
    ![クライアント アドレス空間](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clientaddress.png "Client address space")
5. **[ゲートウェイをすぐに作成する]** チェック ボックスをオンにします。
   
    ![ゲートウェイをすぐに作成する](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/creategwimm.png "Create gateway immediately")
6. **[ゲートウェイの構成 (オプション)]** をクリックして、**[ゲートウェイの構成]** ブレードを開きます。
   
    ![[ゲートウェイの構成 (オプション)] をクリック](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/optsubnet125.png "Click optional gateway configuration")
7. **[Subnet Configure required settings (サブネット構成の必須設定)]** をクリックして、**ゲートウェイ サブネット**を追加します。 /29 と同程度の小規模なゲートウェイ サブネットを作成することはできますが、少なくとも /28 または /27 以上を選択してさらに多くのアドレスが含まれる大規模なサブネットを作成することをお勧めします。 これにより、十分な数のアドレスが、将来的に必要になる可能性のある追加の構成に対応できるようになります。
   
   > [!IMPORTANT]
   > ゲートウェイ サブネットを使用する場合は、ゲートウェイ サブネットにネットワーク セキュリティ グループ (NSG) を関連付けないようにしてください。 このサブネットにネットワーク セキュリティ グループを関連付けると、VPN ゲートウェイが正常に動作しなくなることがあります。 ネットワーク セキュリティ グループの詳細については、「[ネットワーク セキュリティ グループ (NSG) について](../virtual-network/virtual-networks-nsg.md)」を参照してください。
   > 
   > 
   
    ![GatewaySubnet の追加](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsubnet125.png "Add GatewaySubnet")
8. ゲートウェイの **[サイズ]** を選択します。 これは、仮想ネットワーク ゲートウェイの作成に使用するゲートウェイ SKU です。 ポータルでは、既定の SKU は **[Basic]** です。 ゲートウェイ SKU の詳細については、「[VPN Gateway の設定について](vpn-gateway-about-vpn-gateway-settings.md#gwsku)」を参照してください。
   
    ![ゲートウェイのサイズ](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsize125.png)
9. ゲートウェイの **[ルーティングの種類]** を選択します。 P2S 構成には、**動的**なルーティングの種類が必要です。 このブレードの構成を終了したら、**[OK]** をクリックします。
   
    ![ルーティングの種類の構成](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/routingtype125.png "Configure routing type")
10. **[新しい VPN 接続]** ブレードで、ブレードの下部にある **[OK]** をクリックして、仮想ネットワーク ゲートウェイの作成を開始します。 完了するまで最大 45 分かかることがあります。 

## <a name="a-namegeneratecertsasection-2-generate-certificates"></a><a name="generatecerts"></a>セクション 2 - 証明書の生成
証明書は、ポイント対サイト VPN の VPN クライアントを認証するために、Azure によって使用されます。 秘密キーではなく公開証明書データを Base 64 エンコードの X.509 .cer ファイルとして、エンタープライズ証明書ソリューションによって生成されたルート証明書から、または自己署名ルート証明書からエクスポートします。 その後、公開証明書データをルート証明書から Azure にインポートします。 また、クライアントのルート証明書からクライアント証明書を生成する必要があります。 P2S 接続を使用して仮想ネットワークに接続する各クライアントには、ルート証明書から生成されたクライアント証明書をインストールする必要があります。

### <a name="a-namecerapart-1-obtain-the-cer-file-for-the-root-certificate"></a><a name="cer"></a>パート 1: ルート証明書の .cer ファイルの取得
エンタープライズ ソリューションを使用している場合、既存の証明書チェーンを使うことができます。 エンタープライズ CA ソリューションを使用していない場合、自己署名ルート証明書を作成できます。 自己署名証明書を作成する方法の 1 つに、makecert があります。

* エンタープライズ証明書システムを使用している場合は、使用するルート証明書の .cer ファイルを取得します。 
* エンタープライズ証明書ソリューションを使用していない場合は、自己署名ルート証明書を生成する必要があります。 Windows 10 向けの手順については、 [ポイント対サイト構成の自己署名ルート証明書の操作](vpn-gateway-certificates-point-to-site.md)に関する記事を参照してください。

1. 証明書から .cer ファイルを取得するには、**certmgr.msc** を開き、ルート証明書を見つけます。 自己署名ルート証明書を右クリックし、**[すべてのタスク]** をクリックしてから **[エクスポート]** をクリックします。 **証明書のエクスポート ウィザード**が開きます。
2. ウィザードで **[次へ]** をクリックし、**[いいえ、秘密キーをエクスポートしません]** を選択して、**[次へ]** をクリックします。
3. **[エクスポート ファイルの形式]** ページで、**[Base-64 encoded X.509 (.CER)]** を選択します。 次に、 **[次へ]**をクリックします。 
4. **[エクスポートするファイル]** で、**[参照]** をクリックして証明書をエクスポートする場所を選択します。 **[ファイル名]**に証明書ファイルの名前を指定します。 その後、 **[次へ]**をクリックします。
5. **[完了]** をクリックして、証明書をエクスポートします。

### <a name="a-namegenclientcertapart-2-generate-a-client-certificate"></a><a name="genclientcert"></a>パート 2: クライアント証明書の生成
接続するクライアントごとに一意の証明書を生成することも、複数のクライアントに同じ証明書を使用することもできます。 一意のクライアント証明書を生成する利点は、必要に応じて 1 つの証明書を失効させることができる点です。 そうでなければ、すべてのユーザーが同じクライアント証明書を使用していて、1 つのクライアントの証明書を失効させる必要がある場合は、認証にその証明書を使用するすべてのクライアントに新しい証明書を生成してインストールする必要があります。

* エンタープライズ証明書ソリューションを使用している場合は、"domain name\username" 形式ではなく、共通名の値の形式 "'name@yourdomain.com'," を使用してクライアント証明書を生成します。 
* 自己署名証明書を使用している場合は、 [ポイント対サイト構成の自己署名ルート証明書の操作](vpn-gateway-certificates-point-to-site.md) に関する記事を参照して、クライアント証明書を生成してください。

### <a name="a-nameexportclientcertapart-3-export-the-client-certificate"></a><a name="exportclientcert"></a>パート 3: クライアント証明書のエクスポート
仮想ネットワークに接続する各コンピューターに、クライアント証明書をインストールします。 クライアント証明書は認証に必要です。 クライアント証明書は、自動でも手動でもインストールできます。 次の手順では、手動によるクライアント証明書のエクスポートとインストールについて説明します。

1. クライアント証明書をエクスポートするには、 *certmgr.msc*を使用できます。 エクスポートするクライアント証明書を右クリックして、**[すべてのタスク]**、**[エクスポート]** の順にクリックします。
2. クライアント証明書と秘密キーをエクスポートします。 これは *.pfx* ファイルです。 この証明書に設定したパスワード (キー) を記録するか、覚えておいてください。

## <a name="a-nameuploadasection-3-upload-the-root-certificate-cer-file"></a><a name="upload"></a>セクション 3: ルート証明書 .cer ファイルのアップロード
ゲートウェイが作成されたら、信頼されたルート証明書の .cer ファイルを Azure にアップロードできます。 最大 20 個のルート証明書のファイルをアップロードすることができます。 ルート証明書の秘密キーは、Azure にアップロードしません。 .cer ファイルをアップロードすると、Azure は仮想ネットワークに接続するクライアントの認証にそれを使用します。

1. VNet のブレードの **[VPN 接続]** セクションで**クライアント**のグラフィックをクリックして、**[ポイント対サイト VPN 接続]** ブレードを開きます。
   
    ![クライアント](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clients125.png "Clients")
2. **[ポイント対サイト接続]** ブレードで **[証明書の管理]** をクリックして、**[証明書]** ブレードを開きます。<br>
   
    ![[証明書] ブレード](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/ptsmanage.png "Certificates blade")<br><br>
3. **[証明書]** ブレードで **[アップロード]** をクリックして、**[証明書のアップロード]** ブレードを開きます。<br>
   
    ![[証明書のアップロード] ブレード](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/uploadcerts.png "Upload certificates blade")<br>
4. フォルダーのグラフィックをクリックして、.cer ファイルを参照します。 ファイルを選択し、**[OK]** をクリックします。 ページを更新して、アップロードした証明書を **[証明書]** ブレードで確認します。
   
    ![証明書のアップロード](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/upload.png "Upload certificate")<br>

## <a name="a-namevpnclientconfigasection-4-generate-the-vpn-client-configuration-package"></a><a name="vpnclientconfig"></a>セクション 4: VPN クライアント構成パッケージの生成
仮想ネットワークに接続するには、VPN クライアントも構成する必要があります。 クライアント コンピューターが接続するためには、クライアント証明書と適切な VPN クライアント構成パッケージの両方が必要です。

VPN クライアント パッケージには、Windows に組み込まれた VPN クライアント ソフトウェアを構成する構成情報が含まれています。 このパッケージでは、追加のソフトウェアはインストールされません。 この設定は、接続先の仮想ネットワークに固有です。 サポートされているクライアント オペレーティング システムの一覧については、「VPN Gateway に関する FAQ」の「[ポイント対サイト接続](vpn-gateway-vpn-faq.md#point-to-site-connections)」セクションを参照してください。 

### <a name="to-generate-the-vpn-client-configuration-package"></a>VPN クライアント構成パッケージを生成するには
1. Azure Portal で、VNet の **[概要]** ブレードの **[VPN 接続]** にあるクライアントのグラフィックをクリックして、**[ポイント対サイト VPN 接続]** ブレードを開きます。
2. **[ポイント対サイト VPN 接続]** ブレードの上部で、インストール先のクライアントのオペレーティング システムに対応するダウンロード パッケージをクリックします。
   
   * 64 ビット クライアントの場合は、**[VPN クライアント (64 ビット)]** を選択します。
   * 32 ビット クライアントの場合は、**[VPN クライアント (32 ビット)]** を選択します。
     
     ![VPN クライアント構成パッケージのダウンロード](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/dlclient.png "Download VPN client configuration package")<br>
3. Azure が仮想ネットワークの VPN クライアント構成パッケージを生成しているというメッセージが表示されます。 数分後にパッケージが生成され、パッケージがダウンロードされたというメッセージがローカル コンピューターに表示されます。 構成パッケージ ファイルを保存します。 これを、P2S を使用して仮想ネットワークに接続する各クライアント コンピューターにインストールします。

## <a name="a-nameclientconfigurationasection-5-configure-the-client-computer"></a><a name="clientconfiguration"></a>セクション 5: クライアント コンピューターの構成
### <a name="part-1-install-the-client-certificate"></a>パート 1: クライアント証明書のインストール
各クライアント コンピューターには、認証のためにクライアント証明書が必要です。 クライアント証明書をインストールするときに、クライアント証明書のエクスポート時に作成されたパスワードが必要になります。

1. .pfx ファイルをクライアント コンピューターにコピーします。
2. .pfx ファイルをダブルクリックしてインストールします。 インストール先は変更しないでください。

### <a name="part-2-install-the-vpn-client-configuration-package"></a>パート 2: VPN クライアント構成パッケージのインストール
バージョンがクライアントのアーキテクチャと一致すれば、各クライアント コンピューターで同じ VPN クライアント構成パッケージを使用できます。

1. 仮想ネットワークに接続するコンピューターのローカルに構成ファイルをコピーして、.exe ファイルをダブルクリックします。 
2. パッケージがインストールされると、VPN 接続を開始できます。 構成パッケージは Microsoft によって署名されていません。 パッケージは、組織の署名サービスを使用して署名できます。または、[SignTool](http://go.microsoft.com/fwlink/p/?LinkId=699327) を使用してユーザー自身が署名することもできます。 署名なしでパッケージを使用することもできます。 ただし、パッケージが署名されていない場合は、パッケージのインストール時に警告が表示されます。
3. クライアント コンピューターで **[ネットワークの設定]** に移動し、**[VPN]** をクリックします。 接続が一覧で表示されます。 接続先となる仮想ネットワークの名前が、次のように表示されます。 
   
    ![VPN client](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/vpn.png "VNet VPN client")

## <a name="a-nameconnectasection-6-connect-to-azure"></a><a name="connect"></a>セクション 6 - Azure への接続
### <a name="connect-to-your-vnet"></a>VNet への接続
1. VNet に接続するには、クライアント コンピューターで [VPN 接続] に移動し、作成した VPN 接続を見つけます。 仮想ネットワークと同じ名前が付いています。 **[接続]**をクリックします。 証明書を使用することを示すポップアップ メッセージが表示される場合があります。 その場合、 **[続行]** をクリックして、昇格された特権を使用します。 
2. **接続**の状態ページで、**[接続]** をクリックして接続を開始します。 **[証明書の選択]** 画面が表示された場合は、表示されているクライアント証明書が接続に使用する証明書であることを確認します。 そうでない場合は、ドロップダウン矢印を使用して適切な証明書を選択し、 **[OK]**をクリックします。
   
    ![接続](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clientconnect.png "VPN client connection")
3. これで接続が確立されたはずです。
   
    ![確立された接続](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/connected.png "Connection established")

### <a name="verify-the-vpn-connection"></a>VPN 接続の確認
1. VPN 接続がアクティブであることを確認するには、管理者特権でのコマンド プロンプトを開いて、 *ipconfig/all*を実行します。
2. 結果を表示します。 受信した IP アドレスが、VNet の作成時に指定したポイント対サイト接続アドレス範囲内のアドレスのいずれかであることに注意してください。 結果は、次のようになります。

例:

    PPP adapter VNet1:
        Connection-specific DNS Suffix .:
        Description.....................: VNet1
        Physical Address................:
        DHCP Enabled....................: No
        Autoconfiguration Enabled.......: Yes
        IPv4 Address....................: 192.168.130.2(Preferred)
        Subnet Mask.....................: 255.255.255.255
        Default Gateway.................:
        NetBIOS over Tcpip..............: Enabled

## <a name="next-steps"></a>次のステップ
仮想ネットワークに仮想マシンを追加できます。 「 [カスタム仮想マシンを作成する方法](../virtual-machines/virtual-machines-windows-classic-createportal.md)」を参照してください。




<!--HONumber=Nov16_HO2-->


