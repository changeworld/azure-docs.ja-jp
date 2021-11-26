---
title: Azure portal を使用して サイト間 VPN 接続のパケット キャプチャを構成する
titleSuffix: Azure Virtual WAN
description: ポータルを使用して Azure Virtual WAN のサイト間パケット キャプチャを構成する方法について説明します
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 11/09/2021
ms.author: cherylmc
ms.openlocfilehash: c378a6a6e38739600ddad8b0b956e31136d6fffe
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132733859"
---
# <a name="configure-a-packet-capture-for-virtual-wan-site-to-site-vpn-azure-portal"></a>Virtual WAN のサイト間 VPN でパケット キャプチャを構成する: Azure portal

この記事では、Azure portal を使用して、Azure Virtual WAN のサイト間 VPN ゲートウェイでパケット キャプチャを作成する方法について説明します。 パケット キャプチャは、問題の範囲をネットワークの特定の部分に絞り込むのに役立ちます。 これによって、問題がネットワークのオンプレミス側にあるのか、ネットワークの Azure 側にあるのか、またはその間のどこかにあるのかを判断できます。 問題を絞り込むと、デバッグと修復アクションをより効率的に行うことができます。

一般に入手可能なパケット キャプチャ ツールはいくつか存在しますが、これらのツールで関連するパケット キャプチャを取得するのは、特に大量のトラフィックが発生するシナリオでは面倒になる可能性があります。 Virtual WAN パケット キャプチャで提供されるフィルタリング機能が、主な差別化要因となります。 Virtual WAN パケット キャプチャは、一般に入手可能なパケット キャプチャ ツールと共に使用できます。

[!INCLUDE [Portal rollout](../../includes/virtual-wan-portal-feature-rollout.md)]

## <a name="prerequisites"></a>前提条件

環境に次の構成が既に設定されていることを確認します。

* 仮想 WAN と仮想ハブ。
* 仮想ハブにデプロイされたサイト間 VPN ゲートウェイ。
* VPN サイトとサイト間 VPN ゲートウェイとを結ぶ接続が必要となる場合もあります。

## <a name="create-a-storage-account-and-container"></a><a name="storage"></a> ストレージ アカウントとコンテナーを作成する

ストレージ アカウントは、パケット キャプチャの結果の保存に使用されます。

1. ストレージ アカウントを作成します。 手順については、「[ストレージ アカウントを作成する](../storage/common/storage-account-create.md?tabs=azure-portal)」を参照してください。
1. ストレージ アカウント内にコンテナー オブジェクトを作成します。 手順については、「[コンテナーを作成する](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)」を参照してください。

## <a name="generate-the-sas-url"></a><a name="URL"></a> SAS URL を生成する

パケット キャプチャを停止するときは、作成したストレージ コンテナーの **SAS URL** を指定する必要があります。 パケット キャプチャの結果は、この URL を使用して格納されます。
ストレージ コンテナーの SAS URL を生成するには:

1. 新しく作成したコンテナーに移動します。
1. **[設定]** で、 **[共有アクセス トークン]** を選択します。
1. **[アクセス許可]** タブで、 **[読み取り]** と **[書き込み]** の両方が有効になっているか確認します。
1. ページの下部にある **[SAS トークンおよび URL を生成]** ボタンをクリックします。
1. 生成された **BLOB SAS URL** リンクをクリップボードにコピーします。

   :::image type="content" source="./media/packet-capture-site-to-site-portal/generate-url.png" alt-text="[読み取り] と [書き込み] の両方が選択された [共有アクセス トークン] ページ。" lightbox="./media/packet-capture-site-to-site-portal/generate-url-expand.png":::

## <a name="start-a-packet-capture"></a><a name="start"></a> パケット キャプチャを開始する

このセクションでは、仮想ハブでパケット キャプチャを開始します。

1. 仮想ハブに移動します。
1. **[VPN (サイト間)]** をクリックします。
1. **[VPN (サイト間)]** ページで、ページの上部にある **[パケット キャプチャ]** ボタンをクリックします。

   :::image type="content" source="./media/packet-capture-site-to-site-portal/packet-capture.png" alt-text="[キャプチャの開始] ページを示す図。" lightbox="./media/packet-capture-site-to-site-portal/packet-capture-expand.png":::
1. **[パケット キャプチャ]** ページで、 **[開始]** をクリックします。
1. **[パケット キャプチャの開始] ページ** で、必要に応じて設定を変更します。 オプションについては、[[フィルター]](#filters) セクションを参照してください。
1. **[開始]** ボタンをクリックしてパケット キャプチャを開始します。 パケット キャプチャは、600 秒以上実行することをお勧めします。 パス上の複数のコンポーネント間で同期の問題が発生するため、パケット キャプチャの時間が短いと、完全なデータを取得できない場合があります。

## <a name="optional-specify-filters"></a><a name="filters"></a> 省略可能: フィルターを指定する

パケット キャプチャを単純化するために、パケット キャプチャにフィルターを指定して特定の動作のみを対象にすることができます。

| パラメーター | Description | 既定の値 | 使用できる値 |
|---|---|---|---|
|TracingFlags|  キャプチャするパケットの種類を指定する整数| 11 (ESP、IKE、OVPN) |ESP = 1、IKE = 2、OPVN = 8|
|TCPFlags|  キャプチャする TCP パケットの種類を指定する整数 |0 (なし)|  FIN = 1、SYN = 2、RST = 4、PSH = 8、ACK = 16、URG = 32、ECE = 64、CWR = 128|
|MaxPacketBufferSize|   キャプチャするパケットの最大サイズ (バイト単位)。 指定された値より大きい場合、パケットは切り捨てられます。    |120    |Any|
|MaxFileSize |  キャプチャ ファイルの最大サイズ (MB)。 キャプチャは循環バッファーに格納されるので、オーバーフローは FIFO 方式で処理されます (古いパケットから先に削除されます)。|    100|    Any|
|SourceSubnets |    指定した CIDR 範囲からのパケットがキャプチャされます。 配列として指定します。 |   [] (すべての IPv4 アドレス)    |コンマ区切りの IPV4 サブネットの配列|
|DestinationSubnets |指定した CIDR 範囲宛てのパケットがキャプチャされます。 配列として指定します。 |  [] (すべての IPv4 アドレス)|   コンマ区切りの IPV4 サブネットの配列|
|SourcePort|    送信元が、指定した範囲内であるパケットがキャプチャされます。 配列として指定します。|   [] (すべてのポート)|    コンマ区切りのポートの配列|
|DestinationPort|   送信先が、指定した範囲内であるパケットがキャプチャされます。 配列として指定します。   |[] (すべてのポート)|   コンマ区切りのポートの配列|
|CaptureSingleDirectionTrafficOnly |    true の場合、双方向フローのうち一方のみがパケット キャプチャの対象となります。 考えられる IP とポートのすべての組み合わせがキャプチャされます。|  ○|   True、False|
|Protocol|  IANA のプロトコルに対応する整数の配列。|   [] (すべてのプロトコル)|    この [iana.org](https://www.iana.org/assignments/protocol-numbers/protocol-numbers.xhtml) ページに記載されているすべてのプロトコル。|

> [!NOTE]
> TracingFlags と TCPFlags については、キャプチャしたいプロトコルの数値を (論理 OR と同様に) 加算することで複数のプロトコルを指定できます。 たとえば、ESP および OPVN パケットだけをキャプチャする場合は、TracingFlag 値として 8 + 1 = 9 を指定します。
>

## <a name="stop-a-packet-capture"></a>パケット キャプチャを停止する

このセクションは、パケット キャプチャを停止または中止するのに役立ちます。

1. 仮想ハブ ページで、 **[パケット キャプチャ]** ボタンをクリックして **[パケット キャプチャ]** ページを開き、 **[停止]** をクリックします。 これにより、 **[パケット キャプチャの停止]** ページが開きます。 この時点では、パケット キャプチャはまだ停止していません。
   :::image type="content" source="./media/packet-capture-site-to-site-portal/packet-stop.png" alt-text="[停止] ボタンを示す図。" lightbox="./media/packet-capture-site-to-site-portal/packet-stop-expand.png":::

1. **[パケット キャプチャの停止]** ページで、先に作成したストレージ コンテナーの *SaS URL* を **[出力 SAS URL]** フィールドに貼り付けます。 これは、パケット キャプチャ ファイルが格納される場所です。

   :::image type="content" source="./media/packet-capture-site-to-site-portal/output.png" alt-text="出力 SAS URL を貼り付ける場所を示す図。" lightbox="./media/packet-capture-site-to-site-portal/output-expand.png":::
1. 次に、 **[停止]** をクリックします。 パケット キャプチャが停止し、PCAP ファイルが作成され、ストレージ アカウントに保存されます。 これが完了するまでに数分かかることがあります。

### <a name="to-abort-a-packet-capture"></a>パケット キャプチャを中止する方法

何らかの理由でパケット キャプチャを中止する必要がある場合は、仮想ハブに移動し、 **[パケット キャプチャ]** ボタンをクリックして **[パケット キャプチャ]** ページを開き、 **[中止]** をクリックします。 パケット キャプチャが中止された場合、PCAP ファイルは生成または格納されません。

## <a name="view-a-packet-capture"></a><a name="view"></a> パケット キャプチャを表示する

このセクションは、パケット キャプチャの PCAP ファイルをダウンロードして表示するために役立ちます。

1. Azure portal で、作成したストレージ アカウントに移動します。
1. **[コンテナー]** を選択すると、ストレージ アカウントのコンテナーが表示されます。
1. 作成したコンテナーを選択します。
1. フォルダー構造内を移動して、PCAP ファイルを見つけます。 フォルダーの名前と構造は、日付と UTC 時間に基づいています。 PCAP ファイルを見つけたら、 **[ダウンロード]** を選択します。

   :::image type="content" source="./media/packet-capture-site-to-site-portal/download.png" alt-text="ファイルをダウンロードする方法を示すグラフィック。" lightbox="./media/packet-capture-site-to-site-portal/download-expand.png":::
1. パケット キャプチャ データ ファイルは PCAP 形式で生成されます。 PCAP ファイルは、Wireshark または他の一般に利用できるアプリケーションを使用して開くことができます。

## <a name="key-considerations"></a>重要な考慮事項

* パケット キャプチャの実行は、パフォーマンスに影響を与える可能性があります。 必要ない場合は、パケット キャプチャを忘れずに停止してください。
* 推奨される最小パケット キャプチャ期間は 600 秒です。 パス上の複数のコンポーネント間で同期の問題が発生するため、パケット キャプチャの時間が短いと、完全なデータを取得できない場合があります。
* パケット キャプチャ データ ファイルは PCAP 形式で生成されます。 PCAP ファイルを開くには、Wireshark または他の一般に利用できるアプリケーションを使用します。
* SASurl パラメーターが正しく構成されていない場合、トレースはストレージ エラーで失敗する可能性があります。

## <a name="next-steps"></a>次のステップ

[FAQ](virtual-wan-faq.md) を参照して、Azure Virtual WAN の詳細を確認する。
