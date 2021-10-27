---
title: USB モデムを使用して Azure Percept を 5G または LTE ネットワークで接続する
description: この記事では、USB モデムを使用して 5G または LTE ネットワークで Azure Percept DK を接続する方法について説明します。
author: juhaluoto
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 09/03/2021
ms.custom: template-how-to
ms.openlocfilehash: 2e9fb2ae534d11fb0a85199baf7256ab174e3f25
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2021
ms.locfileid: "130007005"
---
# <a name="connect-azure-percept-over-5g-or-lte-networks-using-a-usb-modem"></a>USB モデムを使用して Azure Percept を 5G または LTE ネットワークで接続する

以下では、Azure Percept DK を準備し、USB モデムを使用して 5G または LTE ネットワークに接続する手順について説明します。 これらの手順は、以下の手順に従ってダウンロードできる特別な Azure Percept DK SW にのみ適用されます。 この特別な Azure Percept イメージには、さまざまな USB モデムをサポートする ModemManager オープンソース SW が含まれています。 このイメージでは、OS または他の SW に対する OTA 更新プログラムはサポートされないことに注意してください。 この SW を使用すると、シンプルなコスト効率の高い LTE USB モデムまたはより高度な 5G モデムを使用して、Azure Percept をインターネットや Azure に接続できます。 

> [!Note]
> これらの手順は、MBIM インターフェイスをサポートする USB モデムで使用することを目的としています。 USB モデムを取得する前に、MBIM インターフェイスがサポートされていることを確認してください。 次に、ModemManager でサポートされているモデムの一覧にそのインターフェイスが含まれていることを確認します。 ModemManager SW は他のインターフェイスと一緒に使用できますが、ここでは MBIM インターフェイスに焦点を合わせています。 ModemManager SW の詳細については、https://www.freedesktop.org/wiki/Software/ModemManager/ を参照してください。


:::image type="Image" source="media/connect-over-cellular/azure-percept-all-modems.png" alt-text="Azure Percept に接続されている USB モデム。":::

## <a name="setting-up-the-devkit-for-using-usb-modem"></a>USB モデムを使用するための開発キットの設定

- **ModemManager をサポートする Azure Percept SW イメージをダウンロードします** - [圧縮された Azure Percept 5G SW イメージ ファイル](https://aka.ms/azpercept5gimage) (USB モデムをサポートするように Azure Percept SW を更新するために必要な 3 つのファイルをダウンロードします)
- **USB メソッドを使用してダウンロードしたファイルで Azure Percept を更新します** - [USB を介して更新する手順](./how-to-update-via-usb.md)に従って、前の手順でダウンロードした、LTE/5G 対応の特別な Azure Percept SW で開発キットを更新します。 *前の手順* でダウンロードしたファイルを使用してください (USB を介して更新する手順にあるファイルは使用しないでください)。
- 使用に慣れていない場合は、**通常のセットアップ プロセス** - [quickstart-percept-dk-set-up](./quickstart-percept-dk-set-up.md)に従ってセットアップを実行します。 Azure Percept の ModemManager に対応したこのバージョンでは、セットアップ エクスペリエンスが異なります。
- **SSH を使用して Azure Percept に接続します** - [how-to-ssh-into-percept-dk](./how-to-ssh-into-percept-dk.md)

## <a name="step-by-step-instructions-for-connecting-three-different-modems"></a>3 つの異なるモデムを接続するためのステップ バイ ステップの手順

以下に、3 つの異なる USB モデムの手順を示します。 1 つ目は、特別な機能を備えていないシンプルな LTE CAT-4 USB ドングル (Vodafone) です。 このモデムの手順は、同様の単純なコスト効率の高い USB モデムに使用できます。 2 つ目 (MultiTech) は、さまざまな USB モードの操作を備える USB モデムの例です。 この種類のモデムでは、まず適切な USB モードを有効にして、ModemManager がサポートする MBIM インターフェイスを有効にする必要があります。 3 つ目は、さまざまなモードを備えている 5G モデム (Quectel DK) で、こちらも最初に適切な MBIM モードを有効にする必要があります。  

### <a name="vodafone-usb-connect-4g-v2-modem"></a>Vodafone USB Connect 4G v2 モデム
:::image type="Image" source="media/connect-over-cellular/vodafone-usb-modem-75.png" alt-text="Vodafone USB モデム":::

次に示すのは、Vodafone USB Connect 4G v2 のような単純な USB モデムを使用して Azure Percept DK を接続する手順です。

[Vodafone Connect 4G と USB モデムを使用した接続](./connect-over-cellular-usb-vodafone.md)   

### <a name="multitech-multiconnect-usb-modem"></a>MultiTech Multiconnect USB モデム
:::image type="Image" source="media/connect-over-cellular/multitech-usb-modem-75.png" alt-text="MultiTech USB モデム":::

次に示すのは、MultiTech USB モデム (MTCM-LNA3-B03) のような単純な USB モデムを使用して Azure Percept DK を接続する手順です。

[Multitech USB モデムを使用した接続](./connect-over-cellular-usb-multitech.md)   

### <a name="quectel-5g-developer-kit"></a>Quectel 5G 開発者キット
:::image type="Image" source="media/connect-over-cellular/quectel-5-g-dk-75.png" alt-text="Quectel 5G DK":::

次に示すのは、Quectel RM500Q-GL のような 5G USB モデムを使用して Azure Percept DK を接続する手順です。

[Quectel 5G 開発者キットを使用した接続](./connect-over-cellular-usb-quectel.md) 

## <a name="how-to-make-your-lte5g-connection-recover-from-reboot"></a>再起動から LTE/5G 接続を復旧させる方法 
上記の手順で USB モデムを構成してネットワークに接続できますが、デバイスを再起動する場合は、手動で再接続する必要があります。 現在、これを改善するために取り組んでいます。 詳細情報の取得に関心をお持ちであれば、こちらを参照した旨を記した簡単なメモを添えて電子メールを azpercept5G@microsoft.com までお送りください。 

## <a name="debugging-information"></a>デバッグ情報 
必ず、使用する特定の HW で SIM カードが動作することを必ず確認してください。 一部の通信事業者は、1 つのデバイスでしか動作しないように、データのみの IoT SIM カードを制限しています。 そのような場合は、デバイスの IMEI/シリアル番号が、通信事業者によって SIM カードの使用が "許可されたデバイスの一覧" に含まれていることを確認する必要があります。

### <a name="modemmanager-debug-mode"></a>ModemManager デバッグ モード

次の例に示されているように、`/lib/systemd/system/ModemManager.service` ファイルの行 `ExecStart=/usr/sbin/ModemManager [...]` を編集し、`--debug` を追加することで、ModemManager のデバッグ モードを有効にできます。
```  
[...]  
ExecStart=/usr/sbin/ModemManager [...] --debug  
[...]  
```
その後、変更を有効にするために、サービスを再度読み込み、ModemManager を再起動する必要があります。
```
systemctl daemon-reload
systemctl restart ModemManager
```
ログを表示したり、ログ ファイルをクリーンアップしたりできるコマンドがいくつかあります。
```
journalctl -u ModemManager.service
journalctl --rotate
journalctl --vacuum-time=1s

```

### <a name="reliability-and-stability-enhancement"></a>信頼性と安定性の強化

#### <a name="strict-mode"></a>厳格モード
ModemManager がモデム以外のシリアル インターフェイスと通信しないようにするために、[フィルター ポリシー](https://www.freedesktop.org/software/ModemManager/api/latest/ch03s02.html)を変更することによって、プローブする (どれがモデムか判断するため) インターフェイスを制限できます。 `STRICT` モードを使用することをお勧めします。

このためには、次の例に示すように、`/lib/systemd/system/ModemManager.service` の行 `ExecStart=/usr/sbin/ModemManager [...]` を編集し、`--filter-policy=STRICT` を追加する必要があります。
```
[...]
ExecStart=/usr/sbin/ModemManager --filter-policy=STRICT
[...]
```
その後、変更を有効にするために、サービスを再度読み込み、ModemManager を再起動する必要があります。
```
systemctl daemon-reload
systemctl restart ModemManager
```

## <a name="next-steps"></a>次のステップ
次に、それぞれの USB モデムの手順を確認します。

[Vodafone Connect 4G と USB モデムを使用して接続する](./connect-over-cellular-usb-vodafone.md)

[Multitech USB モデムを使用して接続する](./connect-over-cellular-usb-multitech.md)

[Quectel 5G Developer Kit を使用して接続する](./connect-over-cellular-usb-quectel.md)

5G または LTE のメインの記事に戻ります。

[5G または LTE を使用して接続する](./connect-over-cellular.md)。

[移動体通信ゲートウェイを使用して接続する](./connect-over-cellular-gateway.md)。
