---
title: USB モデム Quectel RM500 を使用して Azure Percept を 5G または LTE で接続する
description: この記事では、Quectel USB モデムを使用して 5G または LTE ネットワークで Azure Percept DK を接続する方法について説明します。
author: juhaluoto
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 09/03/2021
ms.custom: template-how-to
ms.openlocfilehash: 52914140f80bad9c8bd8bb57bec0938211c25e71
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2021
ms.locfileid: "130007011"
---
# <a name="connect-azure-percept-over-5g-or-lte-with-usb-modem-quectel-5g-rm500-gl"></a>USB モデム Quectel 5G RM500-GL を使用して Azure Percept を 5G または LTE で接続する 

Quectel RM500-GL 5G モデムを使用して Azure Percept を接続するステップを、次に示します。 この 5G モデム開発者キットの詳細については、地域の Quectel 販売チームにお問い合わせください。

northamerica-sales@quectel.com – 北米のお客様

sales@quectel.com – 世界のお客様

> [!Note] 
> 5G モデムと USB ケーブルについて これは 5G モデムにのみ適用されます。 5G モデムは、LTE モデムよりも多くの電力を必要とします。 また、USB ケーブルは最適な 5G データ レートのボトルネックになることがあります。 5G モデムに十分かつ一貫した電力を供給するには、USB ケーブルが長すぎないようにし、少なくとも 3 A の電流に耐えられるようにする必要があります。 最大スループットを実現するには、USB 3.1 Gen2 ケーブルを使用し、認定を示す USB-IF ロゴがあることを確認する必要があります。 まとめると、次のようになります。**電力:**
> - 最大アンペア数が 3 Amp 以上である必要があります
> - 長さ 1 m 未満
> - 5G モデムを使用する場合、Azure Percept DK 上の 1 つの USB A ポートのみをアクティブにする必要があります
>  
> **スループット:**
> - USB 3.1 Gen2
> - USB-IF 認定済み

## <a name="preparation"></a>準備
[USB モデムを使用した接続](./connect-over-cellular-usb.md)に関する記事で、Azure Percept の準備が完了していることを確認し、使用する必要がある USB ケーブルに関するコメントにも注目してください。 

### <a name="preparation-of-the-modem"></a>モデムの準備
まず、モデムを MBIM モードにする必要があります。 ドキュメントには記載されていませんが、標準の Quectel AT コマンドは、`AT+QCFG="usbnet"` に使用できます。

`usbnet` プロパティは、`0` から `3` の 4 つの異なる値に設定できます。
- `0` - **NDIS/PPP/QMI モード** (`CONFIG_USB_NET_QMI_WWAN=y|m` で有効な `qmi_wwan` ドライバーでサポート)
- `1` - **CDC イーサネット モード** (`CONFIG_USB_NET_CDCETHER=y|m` のときに Linux でサポート)
- `2` - **MBIM モード** (`CONFIG_USB_NET_CDC_MBIM=y|m` のときに Linux でサポート)
- `3` - **RNDIS モード**

モードを構成する最も簡単な方法は、Quectel 5G モデムを PC に接続して、TeraTerm のようなターミナル SW や、QCOM のような Quectel 独自の PC ソフトウェアを使用することです。 Windows デバイス マネージャーを使用すると、AT コマンドで割り当てられている USB ポートを確認し、それを使用することができます。 COM ポートの設定は次のようにする必要があります: ボーレート: 115200 ストップビット: 1 パリティ: なし バイトサイズ: 8 フロー制御: 制御フローなし

AT コマンドは次のようになります: Quectel デバイスがどの USB モードであるかを確認するには:
```
AT+QCFG="usbnet"
```
モード 2 に変更するには:
```
AT+QCFG="usbnet",2
```
最初の AT コマンドを使用してもう一度確認すると、"+QCFG: "usbnet",2" と表示されるはずです

正しい USB モードを設定したら、次を使用してハードウェア リセットを発行する必要があります。
```
AT+CFUN=1,1
```
この時点で、モデムは接続を切断し、後で USB ポートに再接続します。


## <a name="using-the-modem-to-connect"></a>モデムを使用した接続

**1. SIM カードを Quectel モデムに装着します**

**2. Quectel モデムを Azure Percept USB ポートに接続します。必ず適切な USB ケーブルを使用してください。**

**3. Azure Percept の電源をオンにします**

**4. ModemManager が実行されていることを確認します**

```
systemctl status ModemManager
```
次のようなものが返されます。
```
* ModemManager.service - Modem Manager
   Loaded: loaded (/lib/systemd/system/ModemManager.service; enabled; vendor pre set: enabled)
   Active: active (running) since Mon 2021-08-09 20:52:03 UTC; 23s ago
[...]
```
それ以外の場合は、適切なイメージを Azure Percept (5G 対応) にフラッシュしていることをご確認ください。

**5. アクティブなモデムを一覧表示します**

モデムを一覧表示すると、Quectel モデムが認識され、現在は ModemManager によって処理されていることがわかります。
```
mmcli --list-modems
```
次のような結果が得られます。
```
 /org/freedesktop/ModemManager1/Modem/0 [Quectel] RM500Q-GL
```
ここでのモデム ID は `0` です。これは、アドレス指定 (つまり、`--modem 0`) のために次のコマンドで使用されます。

**6. モデムの詳細を取得します**

既定では、モデムは無効 (`Status -> state: disabled`) になっています。状態をご確認ください。
```
mmcli --modem 0
```
すると、次のようなものが返されます。
```
General  |                    path: /org/freedesktop/ModemManager1/Modem/0
           |               device id: 8e3fb84e3755524d25dfa6f3f1943dc568958a2b
  -----------------------------------
  Hardware |            manufacturer: Quectel
           |                   model: RM500Q-GL
           |       firmware revision: RM500QGLABR11A04M4G
           |          carrier config: CDMAless-Verizon
           | carrier config revision: 0A010126
           |            h/w revision: RM500Q-GL
           |               supported: gsm-umts, lte, 5gnr
           |                 current: gsm-umts, lte, 5gnr
           |            equipment id: xxxx
  -----------------------------------
  System   |                  device: /sys/devices/platform/soc@0/38200000.usb/xhci-hcd.1.auto/usb4/4-1/4-1.1
           |                 drivers: option, cdc_mbim
           |                  plugin: quectel
           |            primary port: cdc-wdm0
           |                   ports: cdc-wdm0 (mbim), ttyUSB0 (qcdm), ttyUSB1 (gps),
           |                          ttyUSB2 (at), ttyUSB3 (at), wwan0 (net)
  -----------------------------------
  Numbers  |                     own: +1xxxx
  -----------------------------------
  Status   |          unlock retries: sim-pin2 (3)
           |                   state: disabled
           |             power state: on
           |          signal quality: 0% (cached)
  -----------------------------------
  Modes    |               supported: allowed: 3g; preferred: none
           |                          allowed: 4g; preferred: none
           |                          allowed: 3g, 4g; preferred: 4g
           |                          allowed: 3g, 4g; preferred: 3g
           |                          allowed: 5g; preferred: none
           |                          allowed: 3g, 5g; preferred: 5g
           |                          allowed: 3g, 5g; preferred: 3g
           |                          allowed: 4g, 5g; preferred: 5g
           |                          allowed: 4g, 5g; preferred: 4g
           |                          allowed: 3g, 4g, 5g; preferred: 5g
           |                          allowed: 3g, 4g, 5g; preferred: 4g
           |                          allowed: 3g, 4g, 5g; preferred: 3g
           |                 current: allowed: 3g, 4g, 5g; preferred: 5g
  -----------------------------------
  Bands    |               supported: utran-1, utran-3, utran-4, utran-6, utran-5, utran-8,
           |                          utran-2, eutran-1, eutran-2, eutran-3, eutran-4, eutran-5, eutran-7,
           |                          eutran-8, eutran-12, eutran-13, eutran-14, eutran-17, eutran-18,
           |                          eutran-19, eutran-20, eutran-25, eutran-26, eutran-28, eutran-29,
           |                          eutran-30, eutran-32, eutran-34, eutran-38, eutran-39, eutran-40,
           |                          eutran-41, eutran-42, eutran-43, eutran-46, eutran-48, eutran-66,
           |                          eutran-71, utran-19
           |                 current: utran-1, utran-3, utran-4, utran-6, utran-5, utran-8,
           |                          utran-2, eutran-1, eutran-2, eutran-3, eutran-4, eutran-5, eutran-7,
           |                          eutran-8, eutran-12, eutran-13, eutran-14, eutran-17, eutran-18,
           |                          eutran-19, eutran-20, eutran-25, eutran-26, eutran-28, eutran-29,
           |                          eutran-30, eutran-32, eutran-34, eutran-38, eutran-39, eutran-40,
           |                          eutran-41, eutran-42, eutran-43, eutran-46, eutran-48, eutran-66,
           |                          eutran-71, utran-19
  -----------------------------------
  IP       |               supported: ipv4, ipv6, ipv4v6
  -----------------------------------
  3GPP     |                    imei: xxxxxxxxxxxxxxx
           |           enabled locks: fixed-dialing
  -----------------------------------
  3GPP EPS |    ue mode of operation: csps-1
           |      initial bearer apn: ims
           |  initial bearer ip type: ipv4v6
  -----------------------------------
  SIM      |        primary sim path: /org/freedesktop/ModemManager1/SIM/0
```

**7. モデムを有効にします**

接続を確立する前に、モデムの無線をオンにする必要があります。
```
mmcli --modem 0 --enable
```
これで、次のようになります。
```
successfully enabled the modem
```
しばらくすると、モデムがセル タワーに登録され、モデムの状態 `Status -> state: registered` が表示されます。 次を使用して、これをもう一度確認できます。
```
mmcli --modem 0
```

**8. APN 情報を使用して接続します**

通常、モデムは、使用する APN (`3GPP EPS -> initial bearer APN` の情報を参照) を提供して、接続の確立に使用できるようにします。 それ以外の場合は、その APN を使用する携帯電話会社にお問い合わせください。 次に示すのは、Verizon APN `APN=vzwinternet` などを使用して接続するための、ModemManager コマンドです
```
mmcli --modem 0 --simple-connect="apn=vzwinternet"
```
この場合も、次のようになります。
```
successfully connected the modem
```

**9. モデムの状態を取得します**

これで、ステータス メッセージの最後に `Status -> state: connected` と新しいカテゴリ `Bearer` が表示されるはずです。
```
mmcli -m 0
```
```
-----------------------------------
  General  |                    path: /org/freedesktop/ModemManager1/Modem/0
           |               device id: 8e3fb84e3755524d25dfa6f3f1943dc568958a2b
  -----------------------------------
  Hardware |            manufacturer: Quectel
           |                   model: RM500Q-GL
           |       firmware revision: RM500QGLABR11A04M4G
           |          carrier config: CDMAless-Verizon
           | carrier config revision: 0A010126
           |            h/w revision: RM500Q-GL
           |               supported: gsm-umts, lte, 5gnr
           |                 current: gsm-umts, lte, 5gnr
           |            equipment id: xxx
  -----------------------------------
  System   |                  device: /sys/devices/platform/soc@0/38200000.usb/xhci-hcd.1.auto/usb4/4-1/4-1.1
           |                 drivers: option, cdc_mbim
           |                  plugin: quectel
           |            primary port: cdc-wdm0
           |                   ports: cdc-wdm0 (mbim), ttyUSB0 (qcdm), ttyUSB1 (gps),
           |                          ttyUSB2 (at), ttyUSB3 (at), wwan0 (net)
  -----------------------------------
  Numbers  |                     own: +1xxxx
  -----------------------------------
  Status   |          unlock retries: sim-pin2 (3)
           |                   state: connected
           |             power state: on
           |             access tech: lte
           |          signal quality: 12% (recent)
  -----------------------------------
  Modes    |               supported: allowed: 3g; preferred: none
           |                          allowed: 4g; preferred: none
           |                          allowed: 3g, 4g; preferred: 4g
           |                          allowed: 3g, 4g; preferred: 3g
           |                          allowed: 5g; preferred: none
           |                          allowed: 3g, 5g; preferred: 5g
           |                          allowed: 3g, 5g; preferred: 3g
           |                          allowed: 4g, 5g; preferred: 5g
           |                          allowed: 4g, 5g; preferred: 4g
           |                          allowed: 3g, 4g, 5g; preferred: 5g
           |                          allowed: 3g, 4g, 5g; preferred: 4g
           |                          allowed: 3g, 4g, 5g; preferred: 3g
           |                 current: allowed: 3g, 4g, 5g; preferred: 5g
  -----------------------------------
  Bands    |               supported: utran-1, utran-3, utran-4, utran-6, utran-5, utran-8,
           |                          utran-2, eutran-1, eutran-2, eutran-3, eutran-4, eutran-5, eutran-7,
           |                          eutran-8, eutran-12, eutran-13, eutran-14, eutran-17, eutran-18,
           |                          eutran-19, eutran-20, eutran-25, eutran-26, eutran-28, eutran-29,
           |                          eutran-30, eutran-32, eutran-34, eutran-38, eutran-39, eutran-40,
           |                          eutran-41, eutran-42, eutran-43, eutran-46, eutran-48, eutran-66,
           |                          eutran-71, utran-19
           |                 current: utran-1, utran-3, utran-4, utran-6, utran-5, utran-8,
           |                          utran-2, eutran-1, eutran-2, eutran-3, eutran-4, eutran-5, eutran-7,
           |                          eutran-8, eutran-12, eutran-13, eutran-14, eutran-17, eutran-18,
           |                          eutran-19, eutran-20, eutran-25, eutran-26, eutran-28, eutran-29,
           |                          eutran-30, eutran-32, eutran-34, eutran-38, eutran-39, eutran-40,
           |                          eutran-41, eutran-42, eutran-43, eutran-46, eutran-48, eutran-66,
           |                          eutran-71, utran-19
  -----------------------------------
  IP       |               supported: ipv4, ipv6, ipv4v6
  -----------------------------------
  3GPP     |                    imei: xxxxxxxxxxxxxxx
           |           enabled locks: fixed-dialing
           |             operator id: 311480
           |           operator name: Verizon
           |            registration: home
           |                     pco: 0: (partial) '27058000FF0100'

  -----------------------------------
  3GPP EPS |    ue mode of operation: csps-1
           |     initial bearer path: /org/freedesktop/ModemManager1/Bearer/0
           |      initial bearer apn: ims
           |  initial bearer ip type: ipv4v6
  -----------------------------------
  SIM      |        primary sim path: /org/freedesktop/ModemManager1/SIM/0
  -----------------------------------
  Bearer   |                   paths: /org/freedesktop/ModemManager1/Bearer/1

```

**10. ベアラーの詳細を取得します**

上記の `--simple-connect` から生じたベアラーはパス `/org/freedesktop/ModemManager1/Bearer/1` に記載されています。
これは、アクティブな接続に関するモデム情報を照会しているものです。 最初のベアラーは、アクティブな接続に接続されていないため、IP 情報を保持しません。
```
mmcli --bearer 1
```
```
  --------------------------------
  General            |       path: /org/freedesktop/ModemManager1/Bearer/1
                     |       type: default
  --------------------------------
  Status             |  connected: yes
                     |  suspended: no
                     |  interface: wwan0
                     | ip timeout: 20
  --------------------------------
  Properties         |        apn: fast.t-mobile.com
                     |    roaming: allowed
  --------------------------------
  IPv4 configuration |     method: static
                     |    address: 25.21.113.165
                     |     prefix: 30
                     |    gateway: 25.21.113.166
                     |        dns: 10.177.0.34, 10.177.0.210
                     |        mtu: 1500
  --------------------------------
  Statistics         |   attempts: 1
```
詳細の内容:
- `Status -> interface: wwan0` - このモデムに一致する Linux ネットワーク インターフェイスを一覧表示します。
- `IPv4 configuration` - 上記のインターフェイスを使用できるように設定するための IP 構成を提供します。

**11. モデムのネットワーク インターフェイスの状態を確認します**

既定ではネットワーク インターフェイスが `DOWN` になります。
```
ip link show dev wwan0
```
結果は次のようになります。
```
4: wwan0: <BROADCAST,MULTICAST,NOARP> mtu 1500 qdisc noop state DOWN mode DEFAULT group default qlen 1000
    link/ether ce:92:c2:b8:1e:f2 brd ff:ff:ff:ff:ff:ff
```

**12. インターフェイスを表示します**

```
sudo ip link set dev wwan0 up
```

**13. IP 情報を確認します**

既定では、IP 情報のないインターフェイスが `UP,LOWER_UP` として表示されます。
```
sudo ip address show dev wwan0
```
```
4: wwan0: <BROADCAST,MULTICAST,NOARP,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UNKNOWN group default qlen 1000
    link/ether ce:92:c2:b8:1e:f2 brd ff:ff:ff:ff:ff:ff
    inet6 fe80::cc92:c2ff:feb8:1ef2/64 scope link 
       valid_lft forever preferred_lft forever
```

**14. DHCP 要求を発行します**

これは、Quectel モジュール固有の機能ですが、これに限定されません。 通常、IP 情報は、インターフェイスまたは ModemManager をサポートするネットワークマネージャー デーモン (NetworkManager など) を介して手動で設定する必要がありますが、ここでは、単に Quectel モデムで dhclient を使用することができます。
```
sudo dhclient wwan0
```

**15. IP 情報を確認します**

このインターフェイスの IP 構成は、ModemManager のベアラーの詳細と一致している必要があります。
```
sudo ip address show dev wwan0
```
結果:
```
4: wwan0: <BROADCAST,MULTICAST,NOARP,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UNKNOWN group default qlen 1000
    link/ether ce:92:c2:b8:1e:f2 brd ff:ff:ff:ff:ff:ff
    inet 25.21.113.165/30 brd 25.21.113.167 scope global wwan0
       valid_lft forever preferred_lft forever
    inet6 fe80::cc92:c2ff:feb8:1ef2/64 scope link 
       valid_lft forever preferred_lft forever
```

**16. インターフェイス ルートを確認します**

DHCP クライアントで、`wwan0` インターフェイスを通過するパケットの既定のルートも設定されていることがわかります。
```
ip route show dev wwan0
```
次のようになります:
```
default via 25.21.113.166 
25.21.113.164/30 proto kernel scope link src 25.21.113.165
```
これで、Quectel モデムを使用した Azure への接続が確立されました。


**17. 接続をテストします**

`wwan0` インターフェイスを介して `ping` 要求を実行します。
```
ping -I wwan0 8.8.8.8
```
```
PING 8.8.8.8 (8.8.8.8) from 25.21.113.165 wwan0: 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=114 time=137 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=114 time=114 ms
^C
--- 8.8.8.8 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 2ms
rtt min/avg/max/mdev = 113.899/125.530/137.162/11.636 ms
```

## <a name="debugging"></a>デバッグ
[USB モデムを使用した接続](./connect-over-cellular-usb.md)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ
[USB モデムを使用して接続します](./connect-over-cellular-usb.md)。

5G または LTE のメインの記事に戻ります。

[5G または LTE を使用して接続します](./connect-over-cellular.md).  