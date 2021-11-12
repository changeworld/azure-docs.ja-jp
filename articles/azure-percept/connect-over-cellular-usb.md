---
title: USB モデムを使用して 5G および LTE ネットワーク経由で Azure Percept DK を接続する
description: この記事では、USB モデムを使用して 5G または LTE ネットワークで Azure Percept DK を接続する方法について説明します。
author: juhaluoto
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 09/03/2021
ms.custom: template-how-to
ms.openlocfilehash: 4bb866c25c81b08be7d4769d33b7d49b3ac6849f
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131440026"
---
# <a name="connect-azure-percept-dk-over-5g-and-lte-networks-by-using-a-usb-modem"></a>USB モデムを使用して 5G および LTE ネットワーク経由で Azure Percept DK を接続する

この記事では、USB モデムを使用して Azure Percept DK を 5G ットワークまたは LTE ネットワークに接続する方法について説明します。 

> [!NOTE]
> この記事の情報は、次のセクションの手順に従ってダウンロードできる特別な Azure Percept DK ソフトウェアにのみ適用されます。 特別な Azure Percept DK イメージには、さまざまな USB モデムをサポートする ModemManager オープンソース ソフトウェアが含まれています。 イメージは、オペレーティング システムまたはその他のソフトウェアに対する無線 (OTA) 更新をサポートしていません。 ModemManager のオープンソース ソフトウェアを使用すると、シンプルでコスト効率のよい LTE USB モデム、またはより高度な 5G モデムを使用して、Azure Percept DK をインターネットと Azure に接続できます。 
>
> この記事の手順は、モバイル ブロードバンド インターフェイス モデル (MBIM) インターフェイスをサポートする USB モデムで使用することを目的としています。 USB モデムを入手する前に、MBIM インターフェイスがサポートされていることを確認してください。 また、サポートされているモデムの ModemManager の一覧にも表示されていることを確認します。 ModemManager ソフトウェアは、他のインターフェイスでも使用できますが、この記事では、MBIM インターフェイスに焦点を当てます。 詳細については、[freedesktop.org の ModemManager](https://www.freedesktop.org/wiki/Software/ModemManager/) に関するページを参照してください。


:::image type="Image" source="media/connect-over-cellular/azure-percept-all-modems-v2.png" alt-text="USB モデムを使用して 5G および LTE ネットワークに接続する、Azure Precept DK の写真の図。":::

## <a name="set-up-azure-percept-dk-to-use-a-usb-modem"></a>USB モデムを使用するように Azure Percept DK を設定する

1. ModemManager をサポートする [Azure Percept 5G ソフトウェア イメージをダウンロード](https://aka.ms/azpercept5gimage)します。 

   この 3 つのファイルは、USB モデムをサポートするように Azure Percept DK ソフトウェアを更新するために必要です。

1. 前の手順でダウンロードした特別な 5G/LTE 対応 Azure Percept DK ソフトウェアを使用して [Azure Percept DK ソフトウェアを更新](./how-to-update-via-usb.md)します。 

   > [!IMPORTANT]
   > 「[Azure Percept DK を USB-C 接続で更新する](./how-to-update-via-usb.md)」の手順に従ってください。ただし、この記事で説明されているファイルではなく、前の手順でダウンロードしたファイルのみを使用してください。

1. よく知らない場合は、通常のプロセスに従って [Azure Percept DK デバイスを設定](./quickstart-percept-dk-set-up.md)します。 

   Azure Percept DK の ModemManager に対応したこのバージョンでは、セットアップ エクスペリエンスが異なります。

1. [Secure Shell (SSH) ネットワーク プロトコルを使用して Azure Percept DK を接続します](./how-to-ssh-into-percept-dk.md)。

## <a name="connect-to-a-modem"></a>モデムへの接続

次の 3 つのセクションでは、さまざまな USB モデムに接続する方法について説明します。  

### <a name="vodafone-usb-connect-4g-v2-modem"></a>Vodafone USB Connect 4G v2 モデム

この Vodafone USB モデムは、特別な機能を持たないシンプルな LTE CAT 4 USB ドングルです。 このモデムの手順は、他の同様の単純なコスト効率の高い USB モデムに使用できます。

:::image type="Image" source="media/connect-over-cellular/vodafone-usb-modem-75.png" alt-text="Vodafone 4G v2 USB モデムの上部ビューと下部ビューの図。":::

Vodafone USB Connect 4G v2 などのシンプルな USB モデムを使用して Azure Percept DK に接続する手順については、[Vodafone Connect 4G v2 USB モデムを使用した接続](./connect-over-cellular-usb-vodafone.md)に関する記事を参照してください。   

### <a name="multitech-multiconnect-usb-modem"></a>MultiTech Multiconnect USB モデム

この MultiTech USB モデムには、いくつかの USB 操作モードが用意されています。 この種類のモデムでは、まず適切な USB モードを有効にして、ModemManager がサポートする MBIM インターフェイスを有効にする必要があります。

:::image type="Image" source="media/connect-over-cellular/multitech-usb-modem-75.png" alt-text="MultiTech Multiconnect USB モデムの図。":::

MultiTech USB モデム (MTCM-LNA3-B03) などの単純な usb モデムを使用して Azure Percept DK 接続するには、[MultiTech USB モデムを使用した接続](./connect-over-cellular-usb-multitech.md)の手順に従います。

### <a name="quectel-5g-developer-kit"></a>Quectel 5G 開発者キット

3 番目のモデムは、Quectel El 5G DK です。 また、いくつかのモードが用意されており、最初に適切な MBIM モードを有効にする必要があります。

:::image type="Image" source="media/connect-over-cellular/quectel-5-g-dk-75.png" alt-text="Quectel 5G DK USB モデムの図。":::

5G USB モデム (Quectel RM500Q-GL など) を使用して Azure Percept DK に接続する手順については、[Quectel 5G Developer Kit を使用した接続](./connect-over-cellular-usb-quectel.md)に関する記事を参照してください。 

## <a name="help-your-5g-or-lte-connection-recover-from-reboot"></a>5G または LTE 接続の再起動からの回復を支援する 
USB モデムを構成してネットワークに接続できますが、デバイスを再起動する場合は、手動で再接続する必要があります。 現在、このエクスペリエンスを向上させるためにソリューションに取り組んでいます。 詳細については、この問題を参照する短いメモを添えて[サポート チーム](mailto:azpercept5G@microsoft.com)にお問い合わせください。 

## <a name="debugging-information"></a>デバッグ情報 
使用する予定の特定のハードウェアで SIM カードが機能していることを確認します。 一部の通信事業者は、1 つのデバイスでしか動作しないように、データのみの IoT SIM カードを制限しています。 このため、デバイスの International Mobile 装置 ID (IMEI) またはシリアル番号が、通信事業者の SIM カードの許可されたデバイスの一覧に記載されていることを確認してください。

### <a name="modemmanager-debug-mode"></a>ModemManager デバッグ モード

次の例に示すように、*/lib/systemd/system/ModemManager.service* ファイルの `ExecStart=/usr/sbin/ModemManager [...]` 行に `--debug` を追加して編集することで、ModemManager デバッグ モードを有効にすることができます。

```  
[...]  
ExecStart=/usr/sbin/ModemManager [...] --debug  
[...]  
```

変更を有効にするには、次に示すように、サービスを再読み込みし、ModemManager を再起動します。

```
systemctl daemon-reload
systemctl restart ModemManager
```

次のコマンドを実行すると、ログを表示し、ログ ファイルをクリーンアップできます。

```
journalctl -u ModemManager.service
journalctl --rotate
journalctl --vacuum-time=1s

```

### <a name="enhance-reliability-and-stability"></a>信頼性と安定性の強化

ModemManager がモデム以外のシリアル インターフェイスと通信しないようにするために、[フィルター ポリシー](https://www.freedesktop.org/software/ModemManager/api/latest/ch03s02.html)を変更することによって、プローブされる (どれがモデムか判断するため) インターフェイスを制限できます。 

`STRICT` モードの使用をお勧めします。

これを行うには、次の例に示すように、*/lib/systemd/system/ModemManager.service* ファイルを、`ExecStart=/usr/sbin/ModemManager [...]` 行に `--filter-policy=STRICT` を追加するように編集します。

```
[...]
ExecStart=/usr/sbin/ModemManager --filter-policy=STRICT
[...]
```
変更を有効にするには、次に示すように、サービスを再読み込みし、ModemManager を再起動します。

```
systemctl daemon-reload
systemctl restart ModemManager
```

## <a name="next-steps"></a>次の手順

* [5G または LTE を使用して接続する](./connect-over-cellular.md)
* [携帯電話ゲートウェイを使用して接続する](./connect-over-cellular-gateway.md)
