---
title: 'チュートリアル: Azure Virtual WAN のサイト間接続でパケット キャプチャを実行する'
description: このチュートリアルでは、Virtual WAN のサイト間 VPN ゲートウェイでパケット キャプチャを実行する方法について説明します。
services: virtual-wan
author: wellee
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 04/13/2021
ms.author: wellee
Customer intent: As someone with a networking background using Virtual WAN, I want to perform a packet capture on my Site-to-site VPN Gateway.
ms.openlocfilehash: bb31d6d9c19df7a914593213e98af1d7a54825a4
ms.sourcegitcommit: ce9178647b9668bd7e7a6b8d3aeffa827f854151
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2021
ms.locfileid: "109809528"
---
# <a name="perform-packet-capture-on-the-azure-virtual-wan-site-to-site-vpn-gateway"></a>Azure Virtual WAN のサイト間 VPN ゲートウェイでパケット キャプチャを実行する 

接続とパフォーマンスに関連する問題は複雑になることが多く、 問題の原因を絞り込むだけでも、かなりの時間と労力を要する場合があります。 パケット キャプチャは、問題の範囲をネットワークの特定の部分に絞り込むのに役立ちます。 これを使用すれば、問題がネットワークの顧客側にあるのか、ネットワークの Azure 側にあるのか、またはその間のどこかにあるのかを判断できます。 問題を絞り込んだ後は、デバッグと修復アクションがより効率的に行えるようになります。

次の記事では、Azure Virtual WAN のサイト間 VPN ゲートウェイでパケット キャプチャを開始および停止する方法について説明します。 現在、この機能は PowerShell からのみ利用できます。

## <a name="prerequisites"></a>前提条件

この記事の手順を使用するには、環境内に以下の構成が既に設定されている必要があります。

* 仮想 WAN、仮想ハブ、その仮想ハブにデプロイされたサイト間 VPN ゲートウェイ。 VPN サイトとサイト間 VPN ゲートウェイとを結ぶ接続が必要となる場合もあります。


### <a name="working-with-azure-powershell"></a>Azure PowerShell を使用する

[!INCLUDE [PowerShell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

## <a name="set-up-the-environment"></a>環境を設定する

PowerShell で次のスクリプトを実行して、適切なサブスクリプション コンテキストに設定します。 これで、サイト間 VPN ゲートウェイでパケット キャプチャを実行するためのアクセス許可があるユーザーにログインしている状態になります。

   ```azurepowershell-interactive
    $subid = “<insert Virtual WAN subscription ID here>”
    Set-AzContext -SubscriptionId $subid
   ```

## <a name="create-a-storage-account"></a><a name="createstorage"></a> ストレージ アカウントを作成する

自分の Azure サブスクリプションに、パケット キャプチャの結果を格納するためのストレージ アカウントを作成する必要があります。 ストレージ アカウントを作成する手順については、こちらの[ドキュメント](.././storage/common/storage-account-create.md)を参照してください。

アカウントを作成したら、次のコマンドを実行して Shared Access Signature (SAS) URL を生成してください。 パケット キャプチャの結果は、この URL を使用して格納されます。
   ```azurepowershell-interactive
  $rgname = “<resource group name containing storage account>” 
$storeName = “<name of storage account> “
$containerName = “<name of container you want to store packet capture in>
$key = Get-AzStorageAccountKey -ResourceGroupName $rgname -Name $storeNAme
$context = New-AzStorageContext -StorageAccountName  $storeName -StorageAccountKey $key[0].value
New-AzStorageContainer -Name $containerName -Context $context
$container = Get-AzStorageContainer -Name $containerName  -Context $context
$now = get-date
$sasurl = New-AzureStorageContainerSASToken -Name $containerName -Context $context -Permission "rwd" -StartTime $now.AddHours(-1) -ExpiryTime $now.AddDays(1) -FullUri
   ```

## <a name="start-the-packet-capture"></a>パケット キャプチャを開始する

パケット キャプチャを開始するには、2 つの方法があります。パケットを個別の VPN 接続でキャプチャする方法と、サイト間 VPN ゲートウェイ全体でキャプチャする方法です。 VPN 接続でキャプチャを実行する方法を選んだ場合、同時にキャプチャできる接続は 6 つに制限されるので注意してください。

### <a name="packet-capture-on-a-site-to-site-vpn-gateway-all-connections"></a>サイト間 VPN ゲートウェイでのパケット キャプチャ (全接続)

次のコマンドを実行してください。 サイト間 VPN ゲートウェイの名前は、仮想ハブに移動し、[接続] の [VPN (サイト間)] をクリックすると見つかります。

:::image type="content" source="./media/virtual-wan-pcap-screenshots/vpn-gateway-name.png" alt-text="Virtual WAN ゲートウェイの名前の画像。" lightbox="./media/virtual-wan-pcap-screenshots/vpn-gateway-name.png":::

   ```azurepowershell-interactive
Start-AzVpnGatewayPacketCapture -ResourceGroupName $rg -Name "<name of the Gateway>"
   ```

### <a name="packet-capture-on-specific-site-to-site-vpn-connections"></a>個別のサイト間 VPN 接続でのパケット キャプチャ

>[!NOTE]
> 同時にパケット キャプチャを実行できる VPN 接続は 5 つに制限されることに注意してください。


次のコマンドを実行してください。 サイト間 VPN 接続の名前は、仮想ハブに移動し、[接続] の [VPN (サイト間)] をクリックすると見つかります。 次に、パケット キャプチャを実行したい VPN サイトに移動し、右側の 3 つのドットをクリックします。 ポップアップ表示されたメニューの **[VPN 接続を編集する]** をクリックします。

:::image type="content" source="./media/virtual-wan-pcap-screenshots/sample-connection.png" alt-text="VPN 接続の名前の見つけ方を示す画像。" lightbox="./media/virtual-wan-pcap-screenshots/sample-connection.png":::

特定の VPN サイトに接続されているリンクの名前は、前のセクションの VPN サイトをクリックし、 **[リンク]** テーブルを見て確認できます。 

:::image type="content" source="./media/virtual-wan-pcap-screenshots/link-name-sample.png" alt-text="VPN リンクの名前の見つけ方を示す画像。" lightbox="./media/virtual-wan-pcap-screenshots/link-name-sample.png":::

   ```azurepowershell-interactive
Start-AzVpnConnectionPacketCapture -ResourceGroupName $rg -Name "<name of the VPN connection>" -ParentResourceName “<name of the Gateway>” -LinkConnection “<comma separated list of links eg. "link1,link2">”
   ```

## <a name="optional-specifying-filters"></a>省略可能: フィルターを指定する

パケット キャプチャ ツールには、一般に入手可能なものがいくつかあります。 これらのツールで関連するパケット キャプチャを取得することは、大量のトラフィックが発生するシナリオでは特に面倒になることがあります。 パケット キャプチャを単純化するために、パケット キャプチャにフィルターを指定して特定の動作のみを対象にすることができます。 使用できるパラメーターは次のとおりです。

>[!NOTE]
> TracingFlags と TCPFlags については、キャプチャしたいプロトコルの数値を (論理和と同様に) 加算することで複数のプロトコルを指定できます。 たとえば、ESP パケットと OPVN パケットのみをキャプチャしたい場合、TracingFlag の値として 9 (= 8+1) を指定します。  

| パラメーター | Description | 既定の値 | 使用できる値|
|--- |--- | --- | ---|
| TracingFlags | キャプチャするパケットの種類を指定する整数 | 11 (ESP、IKE、OVPN) | ESP = 1、IKE = 2、OPVN = 8 |
| TCPFlags | キャプチャする TCP パケットの種類を指定する整数 | 0 (なし) | FIN = 1、SYN = 2、RST = 4、PSH = 8、ACK = 16、URG = 32、ECE = 64、CWR = 128| 
| MaxPacketBufferSize|キャプチャするパケットの最大サイズ (バイト単位)。 指定された値より大きい場合、パケットは切り捨てられます。 |120|Any|
| MaxFileSize |キャプチャ ファイルの最大サイズ (MB)。 キャプチャは循環バッファーに格納されるので、オーバーフローは FIFO 方式で処理されます (古いパケットから先に削除されます)。|100|Any|
|SourceSubnets|指定した CIDR 範囲からのパケットがキャプチャされます。 配列として指定します。|[] (すべての IPv4 アドレス)|コンマ区切りの IPV4 サブネットの配列|
| DestinationSubnets |指定した CIDR 範囲宛てのパケットがキャプチャされます。 配列として指定します。 |[] (すべての IPv4 アドレス)|コンマ区切りの IPV4 サブネットの配列|
|SourcePort |送信元が、指定した範囲内であるパケットがキャプチャされます。 配列として指定します。|[] (すべてのポート)|コンマ区切りのポートの配列|
|DestinationPort |送信先が、指定した範囲内であるパケットがキャプチャされます。 配列として指定します。|[] (すべてのポート)|コンマ区切りのポートの配列|
| CaptureSingleDirectionTrafficOnly |true の場合、双方向フローのうち一方のみがパケット キャプチャの対象となります。 考えられる IP とポートのすべての組み合わせがキャプチャされます。|○|True、False|
|Protocol|IANA のプロトコルに対応する整数の配列。 |[] (すべてのプロトコル)| [こちら](https://www.iana.org/assignments/protocol-numbers/protocol-numbers.xhtml)に記載されているすべてのプロトコル |

次に示したのは、フィルター文字列を使用したパケット キャプチャの例です。 パラメーターは、前の表に基づき、必要に応じて変更できます。  

   ```azurepowershell-interactive
$filter="{`"TracingFlags`":11,`"MaxPacketBufferSize`":120,`"MaxFileSize`":500,`"Filters`":[{`"SourceSubnets`":[`"10.19.0.4/32`",`"10.20.0.4/32`"],`"DestinationSubnets`":[`"10.20.0.4/32`",`"10.19.0.4/32`"],`"TcpFlags`":9,`"CaptureSingleDirectionTrafficOnly`":true}]}"
Start-AzVpnConnectionPacketCapture -ResourceGroupName $rg -Name "<name of the VPN connection>" -ParentResourceName “<name of the Gateway>” -LinkConnection “<comma separated list of links>” -Sasurl $sasurl -FilterData $filter

Start-AzVpnGatewayPacketCapture -ResourceGroupName $rg -Name "<name of the Gateway>" -FilterData $filter
   ```

## <a name="stopping-the-packet-capture"></a>パケット キャプチャを停止する
パケット キャプチャは、600 秒以上実行することをお勧めします。 パス上の複数のコンポーネント間で同期の問題が発生するため、パケット キャプチャの時間が短いと、完全なデータを取得できない場合があります。 パケット キャプチャを停止する準備が整ったら、次のコマンドを実行します。

パラメーターは、パケット キャプチャの開始に関するセクションで使用したものと同様です。 SAS URL は、「[ストレージ アカウントを作成する](#createstorage)」セクションで生成しました。

### <a name="gateway-level-packet-capture"></a>ゲートウェイレベルのパケット キャプチャ

   ```azurepowershell-interactive
Stop-AzVpnGatewayPacketCapture -ResourceGroupName $rg -Name <GatewayName> -SasUrl $sas
   ```

### <a name="connection-level-packet-captures"></a>接続レベルのパケット キャプチャ

   ```azurepowershell-interactive
Stop-AzVpnConnectionPacketCapture -ResourceGroupName $rg -Name <name of the VPN connection> -ParentResourceName "<name of VPN Gateway>" -LinkConnectionName <comma separated list of links e.g. "link1,link2">-SasUrl $sas
   ```

## <a name="viewing-your-packet-capture"></a>パケット キャプチャを表示する

Azure portal で、「ストレージ アカウントを作成する」で作成したストレージ アカウントに移動し、対象のコンテナーをクリックしてファイルをダウンロードします。 パケット キャプチャ データ ファイルは PCAP 形式で生成されます。 PCAP ファイルは、Wireshark または他の一般に利用できるアプリケーションを使用して開くことができます。

## <a name="next-steps"></a>次のステップ

次に、Virtual WAN の詳細については下記を参照してください。

> [!div class="nextstepaction"]
> * [Virtual WAN の FAQ](virtual-wan-faq.md)
