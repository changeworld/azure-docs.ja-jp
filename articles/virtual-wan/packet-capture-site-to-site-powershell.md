---
title: Virtual WAN のサイト間接続でパケット キャプチャを構成する
description: PowerShell を使用して、Virtual WAN のサイト間 VPN ゲートウェイでパケット キャプチャを実行する方法について説明します。
services: virtual-wan
titleSuffix: Azure Virtual WAN
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 11/09/2021
ms.author: cherylmc
ms.openlocfilehash: a5b4427627b164c4f87d2c65203a69c1d2cc2699
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2021
ms.locfileid: "132138633"
---
# <a name="configure-a-packet-capture-for-virtual-wan-site-to-site-vpn-powershell"></a>Virtual WAN のサイト間 VPN でパケット キャプチャを構成する: PowerShell

この記事では、Azure PowerShell を使用して、Azure Virtual WAN のサイト間 VPN ゲートウェイでパケット キャプチャを作成する方法について説明します。 パケット キャプチャは、問題の範囲をネットワークの特定の部分に絞り込むのに役立ちます。 これによって、問題がネットワークのオンプレミス側にあるのか、ネットワークの Azure 側にあるのか、またはその間のどこかにあるのかを判断できます。 問題を絞り込むと、デバッグと修復アクションをより効率的に行うことができます。

一般に入手可能なパケット キャプチャ ツールはいくつか存在しますが、これらのツールで関連するパケット キャプチャを取得するのは、特に大量のトラフィックが発生するシナリオでは面倒になる可能性があります。 Virtual WAN パケット キャプチャで提供されるフィルタリング機能が、主な差別化要因となります。 Virtual WAN パケット キャプチャは、一般に入手可能なパケット キャプチャ ツールと共に使用できます。

## <a name="prerequisites"></a>前提条件

環境に次の構成が既に設定されていることを確認します。

* 仮想 WAN と仮想ハブ。
* 仮想ハブにデプロイされたサイト間 VPN ゲートウェイ。
* VPN サイトとサイト間 VPN ゲートウェイとを結ぶ接続が必要となる場合もあります。

### <a name="working-with-azure-powershell"></a>Azure PowerShell を使用する

[!INCLUDE [PowerShell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

### <a name="set-up-the-environment"></a>環境を設定する

次のコマンドを使用して、正しいサブスクリプションを使用していること、およびサイト間 VPN ゲートウェイでパケット キャプチャを実行するためのアクセス許可を持つユーザーとしてログインしていることを確認します

```azurepowershell-interactive
$subid = “<insert Virtual WAN subscription ID here>”
Set-AzContext -SubscriptionId $subid
```

## <a name="create-a-storage-account-and-container"></a><a name="storage"></a> ストレージ アカウントとコンテナーを作成する

ストレージ アカウントは、パケット キャプチャの結果の保存に使用されます。

1. ストレージ アカウントを作成します。 手順については、「[ストレージ アカウントを作成する](../storage/common/storage-account-create.md?tabs=azure-portal)」を参照してください。
1. ストレージ アカウント内にコンテナー オブジェクトを作成します。 手順については、「[コンテナーを作成する](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)」を参照してください。

## <a name="generate-the-sas-url"></a><a name="URL"></a> SAS URL を生成する

パケット キャプチャを停止するときは、作成したストレージ コンテナーの **SAS URL** を指定する必要があります。 パケット キャプチャの結果は、この URL を使用して格納されます。 

次のコマンドを実行して、Shared Access Signature (SAS) URL を生成します。

```azurepowershell-interactive
$rg = “<resource group name containing storage account>” 
$storeName = “<name of storage account> “
$containerName = “<name of container you want to store packet capture in>
$key = Get-AzStorageAccountKey -ResourceGroupName $rg -Name $storeName
$context = New-AzStorageContext -StorageAccountName  $storeName -StorageAccountKey $key[0].value
New-AzStorageContainer -Name $containerName -Context $context
$container = Get-AzStorageContainer -Name $containerName  -Context $context
$now = get-date
$sasurl = New-AzStorageContainerSASToken -Name $containerName -Context $context -Permission "rwd" -StartTime $now.AddHours(-1) -ExpiryTime $now.AddDays(1) -FullUri
```

## <a name="start-a-packet-capture"></a><a name="start"></a> パケット キャプチャを開始する

このセクションは、サイト間 VPN ゲートウェイ (すべての接続) のパケット キャプチャを開始するために役立ちます。

1. パケット キャプチャを実行するには、サイト間 VPN ゲートウェイの **-Name** 値が必要です。 **-Name** 値を見つけるには、Azure portal で仮想ハブに移動し、 **[接続]** で **[VPN (サイト間)]** を選択します。

   :::image type="content" source="./media/packet-capture-site-to-site-powershell/vpn-gateway-name.png" alt-text="Virtual WAN ゲートウェイの名前の画像。" lightbox="./media//packet-capture-site-to-site-powershell/vpn-gateway-name.png":::

1. パケット キャプチャを開始するには、次のコマンドを実行します。

   ```azurepowershell-interactive
   Start-AzVpnGatewayPacketCapture -ResourceGroupName $rg -Name "<name of the Gateway>"
   ```

## <a name="optional-specify-filters"></a><a name="filters"></a> 省略可能: フィルターを指定する

パケット キャプチャを単純化するために、パケット キャプチャにフィルターを指定して特定の動作のみを対象にすることができます。

>[!NOTE]
> TracingFlags と TCPFlags については、キャプチャしたいプロトコルの数値を (論理和と同様に) 加算することで複数のプロトコルを指定できます。 たとえば、ESP および OPVN パケットだけをキャプチャする場合は、TracingFlag 値として 8 + 1 = 9 を指定します。  

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

次の例は、フィルター文字列を使用したパケット キャプチャを示しています。 ニーズに合うようにパラメーターを変更できます。

```azurepowershell-interactive
$filter="{`"TracingFlags`":11,`"MaxPacketBufferSize`":120,`"MaxFileSize`":500,`"Filters`":[{`"SourceSubnets`":[`"10.19.0.4/32`",`"10.20.0.4/32`"],`"DestinationSubnets`":[`"10.20.0.4/32`",`"10.19.0.4/32`"],`"TcpFlags`":9,`"CaptureSingleDirectionTrafficOnly`":true}]}"
Start-AzVpnConnectionPacketCapture -ResourceGroupName $rg -Name "<name of the VPN connection>" -ParentResourceName “<name of the Gateway>” -LinkConnection “<comma separated list of links>” -SasUrl $sasurl -FilterData $filter

Start-AzVpnGatewayPacketCapture -ResourceGroupName $rg -Name "<name of the Gateway>" -FilterData $filter
```

## <a name="stop-a-packet-capture"></a><a name="stop"></a> パケット キャプチャを停止する

パケット キャプチャを停止する前に少なくとも 600 秒間実行することをお勧めします。 パケット キャプチャを停止するときのパラメーターは、「[パケット キャプチャを開始する](#start)」セクションのパラメーターに似ています。 コマンドでは、SAS URL 値は「[ストレージ アカウントを作成する](#storage)」セクションで生成されました。 `SasUrl` パラメーターが正しく構成されていない場合、キャプチャはストレージ エラーで失敗する可能性があります。

パケット キャプチャを停止する準備が整ったら、次のコマンドを実行します。

```azurepowershell-interactive
Stop-AzVpnGatewayPacketCapture -ResourceGroupName $rg -Name <GatewayName> -SasUrl $sasurl
```

## <a name="view-a-packet-capture"></a><a name="view"></a> パケット キャプチャを表示する

このセクションは、パケット キャプチャの PCAP ファイルをダウンロードして表示するために役立ちます。

1. Azure portal で、作成したストレージ アカウントに移動します。
1. **[コンテナー]** を選択すると、ストレージ アカウントのコンテナーが表示されます。
1. 作成したコンテナーを選択します。
1. フォルダー構造内を移動して、PCAP ファイルを見つけます。 フォルダーの名前と構造は、日付と UTC 時間に基づいています。 PCAP ファイルを見つけたら、 **[ダウンロード]** を選択します。

   :::image type="content" source="./media/packet-capture-site-to-site-powershell/download.png" alt-text="ファイルをダウンロードする方法を示すグラフィック。" lightbox="./media/packet-capture-site-to-site-powershell/download-expand.png":::
1. パケット キャプチャ データ ファイルは PCAP 形式で生成されます。 PCAP ファイルは、Wireshark または他の一般に利用できるアプリケーションを使用して開くことができます。

## <a name="next-steps"></a>次のステップ

次に、Virtual WAN についてさらに学習するには、「[Virtual WAN の FAQ](virtual-wan-faq.md)」を参照してください。
