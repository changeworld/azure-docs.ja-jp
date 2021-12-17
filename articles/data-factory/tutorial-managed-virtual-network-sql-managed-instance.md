---
title: プライベート エンドポイントを使用して、Data Factory マネージド VNET から Microsoft Azure SQL Managed Instance にアクセスする
description: このチュートリアルでは、Azure portal を使用して Private Link サービスを設定し、プライベート エンドポイントを使用してマネージド VNET から SQL Managed Instance にアクセスする手順について説明します。
author: lrtoyou1223
ms.author: lle
ms.service: data-factory
ms.subservice: tutorials
ms.topic: tutorial
ms.date: 05/06/2021
ms.openlocfilehash: cb4d07c28e14ab18500218e3b3942eb768db1755
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128632445"
---
# <a name="tutorial-how-to-access-sql-managed-instance-from-data-factory-managed-vnet-using-private-endpoint"></a>チュートリアル: プライベート エンドポイントを使用して、Data Factory マネージド VNET から SQL Managed Instance にアクセスする方法

このチュートリアルでは、Azure portal を使用して Private Link サービスを設定し、プライベート エンドポイントを使用してマネージド VNET から SQL Managed Instance にアクセスする手順について説明します。

:::image type="content" source="./media/tutorial-managed-virtual-network/sql-mi-access-model.png" alt-text="SQL MI のアクセス モデルを示すスクリーンショット。" lightbox="./media/tutorial-managed-virtual-network/sql-mi-access-model-expanded.png":::

## <a name="prerequisites"></a>前提条件

* **Azure サブスクリプション**。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。
* **Virtual Network**。 仮想ネットワークがない場合は、[仮想ネットワークの作成](../virtual-network/quick-create-portal.md)に関する記事に従って作成します。
* **オンプレミス ネットワークへの仮想ネットワーク**。 [ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md?toc=%2fazure%2fvirtual-network%2ftoc.json) と [VPN](../vpn-gateway/tutorial-site-to-site-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) のいずれかを使用して、仮想ネットワークとオンプレミス ネットワークの間の接続を作成します。
* **マネージド VNET が有効なデータ ファクトリ**。 データ ファクトリがない、またはマネージド VNET が有効でない場合は、[データ ファクトリとマネージド VNET の作成](./tutorial-copy-data-portal-private.md)に関する記事に従って作成します。

## <a name="create-subnets-for-resources"></a>リソース用のサブネットの作成

**ポータルを使用して、仮想ネットワークにサブネットを作成します。**

| Subnet | 説明 |
|:--- |:--- |
|be-subnet |バックエンド サーバー用のサブネット|
|fe-subnet |標準の内部ロード バランサー用のサブネット|
|pls-subnet|Private Link サービス用のサブネット|

:::image type="content" source="./media/tutorial-managed-virtual-network/subnets.png" alt-text="サブネットを示すスクリーンショット。" lightbox="./media/tutorial-managed-virtual-network/subnets-expanded.png":::

## <a name="create-a-standard-load-balancer"></a>Standard Load Balancer の作成

ポータルを使用して Standard 内部ロード バランサーを作成します。

1. 画面の左上で、 **[リソースの作成]、[ネットワーク]、[Load Balancer]** の順に選択します。
2. **[ロード バランサーの作成]** ページの **[基本]** タブで、次の情報を入力または選択します。

    | 設定 | 値 |
    |:--- |:--- |
    |サブスクリプション|サブスクリプションを選択します。|
    |Resource group|リソース グループを選択します。|
    |名前|「**myLoadBalancer**」と入力します。|
    |リージョン|**[米国東部]** を選択します。|
    |Type|**[内部]** を選択します。|
    |SKU|**[Standard]** を選択します。|
    |仮想ネットワーク|仮想ネットワークを選択します。|
    |Subnet|前の手順で作成した **fe-subnet** を選択します。|
    |IP アドレスの割り当て|**[動的]** を選択します。|
    |可用性ゾーン|**[ゾーン冗長]** を選択します。|

3. 残りの設定は既定値をそのまま使用し、 **[確認と作成]** を選択します。
4. **[確認および作成]** タブで、 **[作成]** を選択します。
    
    :::image type="content" source="./media/tutorial-managed-virtual-network/create-load-balancer.png" alt-text="標準ロード バランサーを作成する手順を示すスクリーンショット。":::

## <a name="create-load-balancer-resources"></a>ロード バランサーのリソースを作成する

### <a name="create-a-backend-pool"></a>バックエンド プールの作成

バックエンド アドレス プールには、ロード バランサーに接続された仮想 NIC の IP アドレスが含まれています。

バックエンド アドレス プール **myBackendPool** を作成し、インターネット トラフィックを負荷分散するために仮想マシンを含めます。

1. 左側のメニューで **[すべてのサービス]** 、 **[すべてのリソース]** の順に選択し、リソースの一覧で **[myLoadBalancer]** を選択します。
2. **[設定]** で、 **[バックエンド プール]** 、 **[追加]** の順に選択します。
3. **[バックエンド プールの追加]** ページ上で、バックエンド プールの名前として「**myBackendPool**」と入力し、 **[追加]** を選択します。

### <a name="create-a-health-probe"></a>正常性プローブの作成

ロード バランサーは、正常性プローブを使用してアプリの状態を監視します。

正常性プローブは、正常性チェックへの応答に基づいて、ロード バランサーに含める VM を追加したり削除したりします。

**myHealthProbe** という正常性プローブを作成し、VM の正常性を監視します。

1. 左側のメニューで **[すべてのサービス]** 、 **[すべてのリソース]** の順に選択し、リソースの一覧で **[myLoadBalancer]** を選択します。
2. **[設定]** で、 **[正常性プローブ]** 、 **[追加]** の順に選択します。

    | 設定 | 値 |
    |:--- |:--- |
    |名前|「**myHealthProbe**」と入力します。|
    |Protocol|**[TCP]** を選択します。|
    |Port|「22」を入力します。|
    |Interval|プローブの試行の **間隔** を示す秒数として、「**15**」を入力します。|
    |異常のしきい値|**異常しきい値** またはプローブの連続する失敗の回数として **[2]** を選択します。この回数を超えると、VM は異常と見なされます。|

3. 残りは既定値のままにして、 **[OK]** を選択します。

### <a name="create-a-load-balancer-rule"></a>ロード バランサー規則の作成

ロード バランサー規則の目的は、一連の VM に対するトラフィックの分散方法を定義することです。 着信トラフィック用のフロントエンド IP 構成と、トラフィックを受信するためのバックエンド IP プールを定義します。 送信元と送信先のポートは、この規則で定義します。

このセクションでは、ロード バランサー規則を作成します。

1. 左側のメニューで **[すべてのサービス]** 、 **[すべてのリソース]** の順に選択し、リソースの一覧で **[myLoadBalancer]** を選択します。
2. **[設定]** で、 **[負荷分散規則]** 、 **[追加]** の順に選択します。
3. 負荷分散規則の構成には、以下の値を使用します。

    |設定 |値 |
    |:--- |:--- |
    |Name|「**myRule**」を入力します。|
    |IP バージョン|**[IPv4]** を選択します。|
    |フロントエンド IP アドレス|**[LoadBalancerFrontEnd]** を選択します。|
    |Protocol|**[TCP]** を選択します。|
    |Port|「**1433**」を入力します。|
    |バックエンド ポート|「**1433**」を入力します。|
    |バックエンド プール|**[myBackendPool]** を選択します。|
    |正常性プローブ|**[myHealthProbe]** を選択します。|
    |アイドル タイムアウト (分)|スライダーを **15** 分に移動します。|
    |TCP リセット|**[無効]** をクリックします。|

4. 残りの部分は既定値のままにし、次に **[OK]** を選択します。

## <a name="create-a-private-link-service"></a>プライベート リンク サービスを作成する

このセクションでは、Standard ロード バランサーの背後に Private Link サービスを作成します。

1. Azure portal のページ左上隅の領域にある **[リソースの作成]** を選択します。
2. **[Marketplace を検索]** ボックスで「**Private Link**」を検索します。
3. **［作成］** を選択します
4. **[Private Link センター]** の **[概要]** で、青色の **[Create private link service]\(Private Link サービスの作成\)** ボタンを選択します。
5. **[Create private link service]\(Private Link サービスの作成\)** の **[基本]** タブで、次の情報を入力または選択します。

    |設定 |値|
    |---------|--------|
    |**プロジェクトの詳細**||
    |サブスクリプション |サブスクリプションを選択します。|
    |リソース グループ |リソース グループを選択します。|
    |**インスタンスの詳細**||
    |名前  |「**myPrivateLinkService**」と入力します。|
    |リージョン  |**[米国東部]** を選択します。|

6. **[送信設定]** タブを選択するか、ページ下部の **[Next: Outbound settings]\(次へ: 送信設定\)** を選択します。
7. **[送信設定]** タブで、次の情報を入力または選択します。

    | 設定 | 値 |
    |:--- |:--- |
    |Load Balancer|**[myLoadBalancer]** を選択します。|
    |ロード バランサーのフロントエンド IP アドレス|**[LoadBalancerFrontEnd]** を選択します。|
    |ソース NAT サブネット|**pls-subnet** を選択します。|
    |TCP プロキシ v2 を有効にする|既定値の **[いいえ]** のままにします。|
    |**プライベート IP アドレスの設定**||
    |既定の設定のままにします。||   

8. **[アクセス セキュリティ]** タブを選択するか、ページ下部の **[Next: Access security]\(次へ: アクセス セキュリティ\)** を選択します。
9. **[アクセス セキュリティ]** タブは、既定値の **[ロールベースのアクセス制御のみ]** のままにします。
10. **タグ** タブを選択するか、**次へ: タグ** がページの下部にあるので、これをクリックします。
11. **[確認と作成]** タブを選択するか、ページの下部にある **[次へ: 確認と作成]** を選択します。
12. **[確認と作成]** タブの **[作成]** を選択します。


## <a name="create-backend-servers"></a>バックエンド サーバーの作成

1. ポータルの左上で、 **[リソースの作成]、[Compute]、[仮想マシン]** の順に選択します。
2. **[仮想マシンの作成]** の **[Basic]** タブに、値を入力するか選択します。

    |設定 |値|
    |---------|--------|
    |**プロジェクトの詳細**||
    |サブスクリプション |Azure サブスクリプションを選択します。|
    |リソース グループ |リソース グループを選択します。|
    |**インスタンスの詳細**||
    |仮想マシン名  |「**myVM1**」と入力します。|
    |リージョン  |**[米国東部]** を選択します。|
    |可用性オプション  |**[可用性ゾーン]** を選択します。|
    |可用性ゾーン  |**[1]** を選択します。| 
    |Image  |**[Ubuntu Server 18.04LTS – Gen1]** を選択します。| 
    |Azure Spot インスタンス  |このため、 **[いいえ]** を選択します。| 
    |サイズ   |VM サイズを選択するか、既定の設定を使用します。| 
    |**管理者アカウント**||
    |ユーザー名 |ユーザー名を入力します。|
    |SSH 公開キーのソース  |新しいキーの組を生成します。|
    |キーの組名  |mySSHKey。|    
    |**受信ポートの規則**||
    |パブリック受信ポート |[なし] :|   

3. **[ネットワーク]** タブまたは **[次へ: ディスク]** を選択してから **[次へ: ネットワーク]** を選択します。
4. [ネットワーク] タブで、次を選択または入力します。

    | 設定 |値|
    |---------|--------|
    |**ネットワーク インターフェイス**||
    |仮想ネットワーク |仮想ネットワークを選択します。|
    |Subnet |**be-subnet**。|
    |パブリック IP |**[なし]** を選択します。|
    |NIC ネットワーク セキュリティ グループ |**[なし]** を選択します。|
    |**負荷分散**||
    |この仮想マシンを既存の負荷分散ソリューションの後ろに配置しますか?|**[はい]** を選択します。|
    |**ロード バランサーの設定**||
    |負荷分散のオプション |**[Azure load balancing]\(Azure 負荷分散\)** を選択します。|
    |ロード バランサーを選択する |**[myLoadBalancer]** を選択します。|
    |バックエンド プールを選択する |**[myBackendPool]** を選択します。|    

5. **[Review + create]\(レビュー + 作成\)** を選択します。
6. 設定を確認し、 **[作成]** を選択します。
7. 手順 1 から 6 を繰り返して 2 つ以上のバックエンド サーバー VM を用意し、HA を実現できます。

## <a name="creating-forwarding-rule-to-endpoint"></a>エンドポイントへの転送ルールの作成

1. ログインしてスクリプト [ip_fwd.sh](https://github.com/sajitsasi/az-ip-fwd/blob/main/ip_fwd.sh) をバックエンド サーバー VM にコピーします。 
2. 次のオプションを使用してスクリプトを実行します。<br/>
    **sudo ./ip_fwd.sh -i eth0 -f 1433 -a <FQDN/IP> -b 1433**<br/>
    <FQDN/IP> は、自分の SQL マネージド インスタンスのホストです。
    
    :::image type="content" source="./media/tutorial-managed-virtual-network/sql-mi-host.png" alt-text="SQL MI ホストを示すスクリーンショット。" lightbox="./media/tutorial-managed-virtual-network/sql-mi-host-expanded.png":::

3. 以下のコマンドを実行し、バックエンド サーバー VM の iptables をチェックします。 自分のターゲット IP と一緒に iptables のレコードを 1 つ確認できます。 <br/>
    **sudo iptables -t nat -v -L PREROUTING -n --line-number**
    
    :::image type="content" source="./media/tutorial-managed-virtual-network/command-record-2.png" alt-text="コマンドのレコードを示すスクリーンショット。":::

    >[!Note]
    > 注: 2 つ以上の SQL MI またはその他のデータ ソースがある場合は、複数のロード バランサー規則と IP テーブル レコードを異なるポートで定義する必要があります。 そうしないと、なんらかの競合が発生します。 たとえば、次のように入力します。<br/>
    >
    >|                  |ロード バランサー規則のポート|ロード バランサー規則のバックエンド ポート|バックエンド サーバー VM で実行されるコマンド|
    >|------------------|---------|--------|---------|
    >|**SQL MI 1**|1433 |1433 |sudo ./ip_fwd.sh -i eth0 -f 1433 -a <FQDN/IP> -b 1433|
    >|**SQL MI 2**|1434 |1434 |sudo ./ip_fwd.sh -i eth0 -f 1434 -a <FQDN/IP> -b 1433|
    
## <a name="create-a-private-endpoint-to-private-link-service"></a>Private Link サービスに対するプライベート エンドポイントの作成

1. 左側のメニューで [すべてのサービス] を選択し、[すべてのリソース] を選択してから、リソースの一覧で自分のデータ ファクトリを選択します。
2. **[Author & Monitor]\(作成と監視\)** を選択して、別のタブで Data Factory (UI) を起動します。
3. **[管理]** タブに移動してから、 **[Managed private endpoints]\(マネージド プライベート エンドポイント\)** セクションに移動します。
4. **[Managed private endpoints]\(マネージド プライベート エンドポイント\)** で、 **[+ 新規]** を選択します。
5. 一覧から **[Private Link サービス]** タイルを選択し、 **[続行]** を選択します。
6. プライベート エンドポイントの名前を入力して、Private Link サービスの一覧で **myPrivateLinkService** を選択します。
7. 実際のターゲット SQL Managed Instance の FQDN を追加します。
    
    :::image type="content" source="./media/tutorial-managed-virtual-network/sql-mi-host.png" alt-text="SQL MI ホストを示すスクリーンショット。" lightbox="./media/tutorial-managed-virtual-network/sql-mi-host-expanded.png":::


    :::image type="content" source="./media/tutorial-managed-virtual-network/private-endpoint-5.png" alt-text="プライベート エンドポイントの設定を示すスクリーンショット。":::

8. プライベート エンドポイントを作成します。

## <a name="create-a-linked-service-and-test-the-connection"></a>リンク サービスの作成と接続のテスト

1. **[管理]** タブに移動してから、 **[Managed private endpoints]\(マネージド プライベート エンドポイント\)** セクションに移動します。
2. **[リンクされたサービス]** で **[新規]** を選択します。
3. 一覧から **[Azure SQL Database Managed Instance]** タイルを選択し、 **[続行]** を選択します。    

    :::image type="content" source="./media/tutorial-managed-virtual-network/linked-service-mi-1.png" alt-text="リンク サービスの作成ページを示すスクリーンショット。":::        

4. **インタラクティブな編集** を有効にします。

    :::image type="content" source="./media/tutorial-managed-virtual-network/linked-service-mi-2.png" alt-text="インタラクティブな編集を有効にする方法を示すスクリーンショット。":::
  
5. SQL マネージド インスタンスの **ホスト**、**ユーザー名**、**パスワード** を入力します。

    >[!Note]
    >SQL Managed Instance ホストは手動で入力してください。 そうしないと、選択一覧に完全修飾ドメイン名が表示されません。

6. 次に、 **[テスト接続]** をクリックします。

    :::image type="content" source="./media/tutorial-managed-virtual-network/linked-service-mi-3.png" alt-text="SQL MI リンク サービスの作成ページを示すスクリーンショット。":::

## <a name="next-steps"></a>次の手順

次のチュートリアルに進み、プライベート エンドポイントを使用して Data Factory マネージド VNET からオンプレミスの SQL Server にアクセスする方法について確認します。

> [!div class="nextstepaction"]
> [Data Factory マネージド VNET からオンプレミスの SQL Server へのアクセス](tutorial-managed-virtual-network-on-premise-sql-server.md)