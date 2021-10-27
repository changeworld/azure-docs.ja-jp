---
title: USB モデム MultiTech Multiconnect を使用して Azure Percept を LTE で接続する
description: この記事では、MultiTech USB モデムを使用して 5G または LTE ネットワークで Azure Percept DK を接続する方法について説明します。
author: juhaluoto
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 09/23/2021
ms.custom: template-how-to
ms.openlocfilehash: 336e67de6d178dcff1b57fe75d57f2272386d2c5
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2021
ms.locfileid: "130007033"
---
# <a name="connect-azure-percept-over-lte-with-usb-modem-multitech-multiconnect"></a>USB モデム MultiTech Multiconnect を使用して Azure Percept を LTE で接続する 
ここでは、MultiTech Multiconnect (MTCM-LNA3-B03) USB モデムを使用して Azure Percept を接続する手順について説明します。 

> [!Note]
> いくつかのモデルがありますが、少なくとも Verizon および Vodafone SIM カードで動作する LNA3 を使用しています。 AT&T ネットワークに接続できませんでしたが、現在調査中で、根本的な原因が見つかった場合は修正します。 この特定のモデム ハードウェアの詳細については、次のページを参照してください: https://www.multitech.com/brands/multiconnect-microcell

## <a name="preparation"></a>準備
[USB モデムを使用した接続](./connect-over-cellular-usb.md)に関する記事で、Azure Percept の準備が完了していることを確認し、使用する必要がある USB ケーブルに関するコメントにも注目してください。 

### <a name="preparation-of-the-modem"></a>モデムの準備
まず、モデムを MBIM モードにする必要があります。 その方法については、この AT コマンド リファレンス ガイドを参照してください: https://www.telit.com/wp-content/uploads/2018/01/Telit-LE910-V2-Modules-AT-Commands-Reference-Guide-r3.pdf

AT コマンド `AT#USBCFG=<mode>` を使用して、MBIM インターフェイスを有効にする適切な USB モードを設定します。

AT コマンド リファレンス ガイドには、使用可能なすべてのモードが一覧表示されていますが、ここでは、モード `3` に注目しており、既定は `0` です。

モードを構成する最も簡単な方法は、MultiTech モデムを PC に接続して、TeraTerm や Putty PC ソフトウェアなどのターミナル ソフトウェア を使用することです。 Windows デバイス マネージャーを使用すると、モデムに割り当てられている USB ポートを確認できます。複数ある場合は、どれが AT コマンドに応答しているかをテストする必要がある可能性があります。 COM ポートの設定は次のようにする必要があります: ボーレート: 9600 (または 115200) ストップビット: 1 パリティ: なし バイトサイズ: 8 フロー制御: 制御フローなし

AT コマンドは次のようになります。MultiTech デバイスが現在どの USB モードであるかを確認します。
```
AT#USBCFG?
```
モード 3 に変更するには:
```
AT#USBCFG=3
```
最初の AT コマンドを使用してもう一度確認すると、次のようになります: `#USBCFG: 3`

正しい USB モードを設定したら、次のようにリセットを発行する必要があります。
```
AT#REBOOT
```
この時点で、モデムは接続を切断され、後で上記の設定モードを使用して USB ポートに再接続する必要があります。

## <a name="using-the-modem-to-connect"></a>モデムを使用した接続
[USB モデムを使用した接続](./connect-over-cellular-usb.md)に関する記事で、Azure Percept の準備が完了していることを確認します。   

**1. MultiTech モデムに SIM カードを接続します**

**2. MultiTech モデムを Azure Percept USB A ポートに接続します**

**3. Azure Percept の電源をオンにします**

**4. Azure Percept DK に SSH 接続します**

**5. ModemManager が実行されていることを確認します**

SSH プロンプトに次のコマンドを書き込みます。
```
systemctl status ModemManager
```
すべて問題がなければ、次のようになります。

*ModemManager.service - Modem Manager*
*Loaded: loaded (/lib/systemd/system/ModemManager.service; enabled; vendor preset: enabled)*
*Active: active (running) since Mon 2021-08-09 20:52:03 UTC; 23 s ago*

**6. アクティブなモデムを一覧表示します**

この場合、FIH7160 モデルが ModemManager によって認識されていることがわかります。
```
mmcli --list-modems
```
次のようになります: */org/freedesktop/ModemManager1/Modem/0 [Telit] FIH7160*

**7. モデムの詳細を取得します**

ここではモデム ID は `0` ですが、場合によっては異なります。 モデム ID (`--modem 0`.) は、次のような ModemManager のコマンドで使用されます
```
mmcli --modem 0
```
既定では、モデムは無効になっています (`Status -> state: disabled`)
```
 --------------------------------
  General  |                 path: /org/freedesktop/ModemManager1/Modem/0
           |            device id: f89a480d73f1a9cfef28102a0b44be2a47329c8b
  --------------------------------
  Hardware |         manufacturer: Telit
           |                model: FIH7160
           |    firmware revision: 20.00.525
           |         h/w revision: XMM7160_V1.1_HWID437_MBIM_NAND
           |            supported: gsm-umts, lte
           |              current: gsm-umts, lte
           |         equipment id: xxxx
  --------------------------------
  System   |               device: /sys/devices/platform/soc@0/38200000.usb/xhci-hcd.1.auto/usb3/3-1/3-1.1
           |              drivers: cdc_acm, cdc_mbim
           |               plugin: telit
           |         primary port: cdc-wdm0
           |                ports: cdc-wdm0 (mbim), ttyACM1 (at), ttyACM2 (ignored),
           |                       ttyACM3 (ignored), ttyACM4 (at), ttyACM5 (ignored), ttyACM6 (ignored),
           |                       wwan0 (net)
  --------------------------------
  Status   |       unlock retries: sim-pin2 (3)
           |                state: disabled
           |          power state: on
           |       signal quality: 0% (cached)
  --------------------------------
  Modes    |            supported: allowed: 3g; preferred: none
           |                       allowed: 4g; preferred: none
           |                       allowed: 3g, 4g; preferred: none
           |              current: allowed: 3g, 4g; preferred: none
  --------------------------------
  Bands    |            supported: utran-5, utran-2, eutran-2, eutran-4, eutran-5, eutran-12,
           |                       eutran-13, eutran-17
           |              current: utran-2, eutran-2
  --------------------------------
  IP       |            supported: ipv4, ipv6, ipv4v6
  --------------------------------
  3GPP     |                 imei: xxxxxxxxxxxxxxx
           |        enabled locks: fixed-dialing
  --------------------------------
  3GPP EPS | ue mode of operation: csps-2
  --------------------------------
  SIM      |     primary sim path: /org/freedesktop/ModemManager1/SIM/0
```

**8. モデムを有効にします**

接続を確立する前に、モデムの無線をオンにする必要があります。
```
mmcli --modem 0 --enable
```
次のような応答が表示されます。*モデムが正常に有効化されました*

しばらくすると、モデムがセル タワーに登録され、再度実行するとモデムの状態 `Status -> state: registered` が表示されます。
```
mmcli --modem 0
```

**9. APN 情報を使用して接続します**

アクセス ポイント名 = APN は、携帯電話プロバイダー (ここでは Verizon) によって提供されます。
```
mmcli --modem 0 --simple-connect="apn=vzwinternet"  
```
すべて問題がなければ、*モデムに正常に接続されます*

**10. モデムの状態を取得します**

これで `Status -> state: connected` と表示され、ステータス メッセージの最後に新しい `Bearer` カテゴリが表示されます。
```
mmcli --modem 0
```
```
 --------------------------------
  General  |                 path: /org/freedesktop/ModemManager1/Modem/0
           |            device id: f89a480d73f1a9cfef28102a0b44be2a47329c8b
  --------------------------------
  Hardware |         manufacturer: Telit
           |                model: FIH7160
           |    firmware revision: 20.00.525
           |         h/w revision: XMM7160_V1.1_HWID437_MBIM_NAND
           |            supported: gsm-umts, lte
           |              current: gsm-umts, lte
           |         equipment id: xxxx
  --------------------------------
  System   |               device: /sys/devices/platform/soc@0/38200000.usb/xhci-hcd.1.auto/usb3/3-1/3-1.1
           |              drivers: cdc_acm, cdc_mbim
           |               plugin: telit
           |         primary port: cdc-wdm0
           |                ports: cdc-wdm0 (mbim), ttyACM1 (at), ttyACM2 (ignored),
           |                       ttyACM3 (ignored), ttyACM4 (at), ttyACM5 (ignored), ttyACM6 (ignored),
           |                       wwan0 (net)
  --------------------------------
  Numbers  |                  own: +1xxxxxxxx
  --------------------------------
  Status   |       unlock retries: sim-pin2 (3)
           |                state: connected
           |          power state: on
           |          access tech: lte
           |       signal quality: 16% (recent)
  --------------------------------
  Modes    |            supported: allowed: 3g; preferred: none
           |                       allowed: 4g; preferred: none
           |                       allowed: 3g, 4g; preferred: none
           |              current: allowed: 3g, 4g; preferred: none
  --------------------------------
  Bands    |            supported: utran-5, utran-2, eutran-2, eutran-4, eutran-5, eutran-12,
           |                       eutran-13, eutran-17
           |              current: utran-2, eutran-2
  --------------------------------
  IP       |            supported: ipv4, ipv6, ipv4v6
  --------------------------------
  3GPP     |                 imei: xxxxxxxxxxxxxxx
           |        enabled locks: fixed-dialing
           |          operator id: 311480
           |        operator name: Verizon
           |         registration: home
  --------------------------------
  3GPP EPS | ue mode of operation: csps-2
  --------------------------------
  SIM      |     primary sim path: /org/freedesktop/ModemManager1/SIM/0
  --------------------------------
  Bearer   |                paths: /org/freedesktop/ModemManager1/Bearer/0
```

**11. ベアラーの詳細を取得します**

ベアラーの詳細は、モデムによって携帯ネットワークで確立されたパケット データ接続に OS を接続するために必要です。 この時点では、モデムには IP 接続がありますが、OS はまだそれを使用するようには構成されていません。
```
mmcli --bearer 0
```
ベアラーの詳細の一覧:
```
 ------------------------------------
  General            |           path: /org/freedesktop/ModemManager1/Bearer/0
                     |           type: default
  ------------------------------------
  Status             |      connected: yes
                     |      suspended: no
                     |      interface: wwan0
                     |     ip timeout: 20
  ------------------------------------
  Properties         |            apn: vzwinternet
                     |        roaming: allowed
  ------------------------------------
  IPv4 configuration |         method: static
                     |        address: 100.112.107.46
                     |         prefix: 24
                     |        gateway: 100.112.107.1
                     |            dns: 198.224.166.135, 198.224.167.135
  ------------------------------------
  Statistics         |       duration: 119
                     |       attempts: 1
                     | total-duration: 119
```

**12. ネットワーク インターフェイスを起動します**

```
sudo ip link set dev wwan0 up
```

**13. ネットワーク インターフェイスを構成します**

ベアラーによって提供される情報を使用して、IP アドレス (ここでは 100.112.107.46/24) を、ベアラーに含まれているものに置き換えます。
```
sudo ip address add 100.112.107.46/24 dev wwan0
```

**14. IP 情報を確認します**

このインターフェイスの IP 構成は、ModemManager のベアラーの詳細と一致している必要があります。
```
sudo ip address show dev wwan0
```
ベアラー IP が次のように一覧表示されていることを確認します。
```
6: wwan0: <BROADCAST,MULTICAST,NOARP,UP,LOWER_UP> mtu 1428 qdisc pfifo_fast state UNKNOWN group default qlen 1000
    link/ether 1e:fb:08:e9:2a:25 brd ff:ff:ff:ff:ff:ff
    inet 100.112.107.46/24 scope global wwan0
       valid_lft forever preferred_lft forever
    inet6 fe80::1cfb:8ff:fee9:2a25/64 scope link
       valid_lft forever preferred_lft forever
```

**15. 既定のルートを設定します**

ベアラーから提供された情報を再度使用し、モデムのゲートウェイ (100.112.107.1 を置き換える) をネットワーク パケットの既定の宛先として使用します。
```
sudo ip route add default via 100.112.107.1 dev wwan0
```
これで、Azure Percept は USB モデムを使用して接続できるようになりました。

**16. 接続をテストします**

`wwan0` インターフェイスを介して `ping` 要求を実行します。 ただし、Azure Percept Studio を使用してテレメトリ メッセージを受信しているかどうかを確認することもできます (イーサネット ケーブルや Wi-Fi が有効になっていないことを確認し、LTE を使用するようにしてください)
```
ping -I wwan0 8.8.8.8
```
これで、次のようになります
```
PING 8.8.8.8 (8.8.8.8) from 162.177.2.0 wwan0: 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=114 time=111 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=114 time=92.0 ms
64 bytes from 8.8.8.8: icmp_seq=3 ttl=114 time=88.8 ms
^C
--- 8.8.8.8 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 4ms
rtt min/avg/max/mdev = 88.779/97.254/110.964/9.787 ms
```


## <a name="debugging"></a>デバッグ
一般的な情報については、「[USB モデムを使用して接続する](./connect-over-cellular-usb.md)」を参照してください。

## <a name="next-steps"></a>次のステップ
[USB モデムを使用して接続します](./connect-over-cellular-usb.md)。

5G または LTE のメインの記事に戻ります。

[5G または LTE を使用して接続します](./connect-over-cellular.md)。
   

