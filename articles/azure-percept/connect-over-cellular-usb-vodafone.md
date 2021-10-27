---
title: Vodafone USB モデムを使用して Azure Percept を 5G または LTE 経由で接続する
description: この記事では、Vodafone USB モデムを使用して 5G または LTE ネットワーク経由で Azure Percept DK を接続する方法について説明します。
author: juhaluoto
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 09/23/2021
ms.custom: template-how-to
ms.openlocfilehash: 6264bd855cdc4ff186f51748bf426573e6316e5c
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2021
ms.locfileid: "130007008"
---
# <a name="connect-azure-percept-over-lte-with-usb-modem-vodafone-usb-connect-4g"></a>Vodafone USB モデム USB Connect 4G を使用して Azure Percept を LTE 経由で接続する
Vodafone USB Connect 4G v2 を使用して Azure Percept を接続する手順を次に示します。
この特定のモデム ハードウェアの詳細については、Vodafone のページ https://www.vodafone.com/business/iot/iot-devices/integrated-terminals を参照してください。

## <a name="using-the-modem-to-connect"></a>モデムを使用した接続
[USB モデムを使用した接続](./connect-over-cellular-usb.md)に関するページで説明されている Azure Percept DK の準備が完了していることを確認します。 USB モデム自体の準備は必要ありません。   

**1. Vodafone モデムに SIM カードを挿入します**

**2. Vodafone モデムを Azure Percept の USB A ポートに接続します**

**3. Azure Percept の電源をオンにします**

**4. Azure Percept DK に SSH 接続します**

**5. ModemManager が実行されていることを確認します**

SSH プロンプトに次のコマンドを入力します。
```
systemctl status ModemManager
```
すべて問題がなければ、次のようになります。

*ModemManager.service - Modem Manager*
*Loaded: loaded (/lib/systemd/system/ModemManager.service; enabled; vendor preset: enabled)* 
*Active: active (running) since Mon 2021-08-09 20:52:03 UTC; 23 s ago*

**6. アクティブなモデムを一覧表示します**

次に、お使いのモデムが ModemManager によって認識されていることを確認します。
```
mmcli --list-modems
```
次のようになります。ここでは、モデム ID が `0`になっていますが、他の値が表示される場合もあります。

 */org/freedesktop/ModemManager1/Modem/0 [Alcatel] Mobilebroadband*

**7. モデムの詳細を取得します**

モデムの状態の詳細を取得するには、次のコマンドとモデム ID `0`を使用します
```
mmcli --modem 0
```
既定では、モデムは無効 (`Status -> state: disabled`) になっています
```
  --------------------------------
  General  |                 path: /org/freedesktop/ModemManager1/Modem/0
           |            device id: 20a6021958444bcb6f6589b47fd264932c340e69
  --------------------------------
  Hardware |         manufacturer: Alcatel
           |                model: Mobilebroadband
           |    firmware revision: MPSS.JO.2.0.2.c1.7-00004-9607_
           |       carrier config: default
           |         h/w revision: 0
           |            supported: gsm-umts, lte
           |              current: gsm-umts, lte
           |         equipment id: xxx
  --------------------------------
  System   |               device: /sys/devices/platform/soc@0/38200000.usb/xhci-hcd.1.auto/usb3/3-1/3-1.2
           |              drivers: option, cdc_mbim
           |               plugin: generic
           |         primary port: cdc-wdm0
           |                ports: cdc-wdm0 (mbim), ttyUSB0 (at), ttyUSB1 (qcdm), 
           |                       ttyUSB2 (at), wwan0 (net)
  --------------------------------
  Status   |       unlock retries: sim-pin2 (3)
           |                state: disabled
           |          power state: on
           |       signal quality: 0% (cached)
  --------------------------------
  Modes    |            supported: allowed: 2g; preferred: none
           |                       allowed: 3g; preferred: none
           |                       allowed: 4g; preferred: none
           |                       allowed: 2g, 3g; preferred: 3g
           |                       allowed: 2g, 3g; preferred: 2g
           |                       allowed: 2g, 4g; preferred: 4g
           |                       allowed: 2g, 4g; preferred: 2g
           |                       allowed: 3g, 4g; preferred: 4g
           |                       allowed: 3g, 4g; preferred: 3g
           |                       allowed: 2g, 3g, 4g; preferred: 4g
           |                       allowed: 2g, 3g, 4g; preferred: 3g
           |                       allowed: 2g, 3g, 4g; preferred: 2g
           |              current: allowed: 2g, 3g, 4g; preferred: 2g
  --------------------------------
  Bands    |            supported: egsm, dcs, pcs, g850, utran-4, utran-5, utran-2, eutran-2, 
           |                       eutran-4, eutran-5, eutran-7, eutran-12, eutran-13, eutran-71
           |              current: egsm, dcs, pcs, g850, utran-4, utran-5, utran-2, eutran-2, 
           |                       eutran-4, eutran-5, eutran-7, eutran-12, eutran-13, eutran-71
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
既定の設定 `Modes: current: allowed: 2g, 3g, 4g; preferred: 2g` から始めることをお勧めします。これは、`mmcli --modem 0 --set-allowed-modes='2g|3g|4g' --set-preferred-mode='2g'`をまだ使用していない場合に設定できます。

**8. モデムを有効にします**

接続を確立する前に、モデムの無線をオンにする必要があります。
```
mmcli --modem 0 --enable
```
"*successfully enabled the modem*" (モデムが正常に有効になりました) というような応答が表示されます

しばらくすると、モデムがセル タワーに登録され、再度実行すると、モデムの状態 `Status -> state: registered` が表示されます。
```
mmcli --modem 0
```

**9. APN 情報を使用して接続します**

アクセス ポイント名 (APN) は、携帯電話プロバイダーによって提供されます。Vodafone の場合は次のようになります。
```
mmcli --modem 0 --simple-connect="apn=internet4gd.gdsp"  
```
すべて問題がなければ、"*successfully connected the modem*" (モデムが正常に接続されました) と表示されます

**10. モデムの状態を取得します**

これで、`Status -> state: connected` が表示され、ステータス メッセージの最後に新しい `Bearer` カテゴリが表示されます。
```
mmcli --modem 0
```
```
  --------------------------------
  General  |                 path: /org/freedesktop/ModemManager1/Modem/0-mobile.
           |            device id: 20a6021958444bcb6f6589b47fd264932c340e69
  --------------------------------
  Hardware |         manufacturer: Alcatel
           |                model: Mobilebroadband
           |    firmware revision: MPSS.JO.2.0.2.c1.7-00004-9607_
           |       carrier config: default
           |         h/w revision: 0
           |            supported: gsm-umts, lte
           |              current: gsm-umts, lte
           |         equipment id: xxx
  --------------------------------
  System   |               device: /sys/devices/platform/soc@0/38200000.usb/xhci-hcd.1.auto/usb3/3-1/3-1.2
           |              drivers: option, cdc_mbim
           |               plugin: generic
           |         primary port: cdc-wdm0
           |                ports: cdc-wdm0 (mbim), ttyUSB0 (at), ttyUSB1 (qcdm), 
           |                       ttyUSB2 (at), wwan0 (net)
  --------------------------------
  Numbers  |                  own: xxx
  --------------------------------
  Status   |       unlock retries: sim-pin2 (10)
           |                state: connected
           |          power state: on
           |          access tech: lte
           |       signal quality: 19% (recent)
  --------------------------------
  Modes    |            supported: allowed: 2g; preferred: none
           |                       allowed: 3g; preferred: none
           |                       allowed: 4g; preferred: none
           |                       allowed: 2g, 3g; preferred: 3g
           |                       allowed: 2g, 3g; preferred: 2g
           |                       allowed: 2g, 4g; preferred: 4g
           |                       allowed: 2g, 4g; preferred: 2g
           |                       allowed: 3g, 4g; preferred: 4g
           |                       allowed: 3g, 4g; preferred: 3g
           |                       allowed: 2g, 3g, 4g; preferred: 4g
           |                       allowed: 2g, 3g, 4g; preferred: 3g
           |                       allowed: 2g, 3g, 4g; preferred: 2g
           |              current: allowed: 2g, 3g, 4g; preferred: 2g
  --------------------------------
  Bands    |            supported: egsm, dcs, pcs, g850, utran-4, utran-5, utran-2, eutran-2, 
           |                       eutran-4, eutran-5, eutran-7, eutran-12, eutran-13, eutran-71
           |              current: egsm, dcs, pcs, g850, utran-4, utran-5, utran-2, eutran-2, 
           |                       eutran-4, eutran-5, eutran-7, eutran-12, eutran-13, eutran-71
  --------------------------------
  IP       |            supported: ipv4, ipv6, ipv4v6
  --------------------------------
  3GPP     |                 imei: xxxxxxxxxxxxxxx
           |        enabled locks: fixed-dialing
           |          operator id: 302220
           |        operator name: TELUS
           |         registration: roaming
  --------------------------------
  3GPP EPS | ue mode of operation: csps-2
  --------------------------------
  SIM      |     primary sim path: /org/freedesktop/ModemManager1/SIM/0
  --------------------------------
  Bearer   |                paths: /org/freedesktop/ModemManager1/Bearer/0
```

**11. ベアラーの詳細を取得します**

ベアラーの詳細は、モデムと携帯ネットワークとの間で確立されたパケット データ接続に OS を接続するために必要です。 この時点で、モデムは IP 接続されていますが、OS はまだそれを使用するように構成されていません。

```
mmcli --bearer 0
```
ベアラーの詳細を次に示します。
```
  --------------------------------
  General            |       path: /org/freedesktop/ModemManager1/Bearer/0
                     |       type: default
  --------------------------------
  Status             |  connected: yes
                     |  suspended: no
                     |  interface: wwan0
                     | ip timeout: 20
  --------------------------------
  Properties         |        apn: internet4gd.gdsp
                     |    roaming: allowed
  --------------------------------
  IPv4 configuration |     method: static
                     |    address: 162.177.2.0
                     |     prefix: 22
                     |    gateway: 162.177.2.1
                     |        dns: 10.177.0.34, 10.177.0.210
                     |        mtu: 1500
  --------------------------------
  Statistics         |   attempts: 1
```

**12. ネットワーク インターフェイスを起動します**

```
sudo ip link set dev wwan0 up
```

**13. ネットワーク インターフェイスを構成します**

ベアラーの詳細で提供された情報を使用して、IP アドレス (ここでは 162.177.2.0/22) を、ベアラーに含まれるアドレスに置き換えます。
```
sudo ip address add 162.177.2.0/22 dev wwan0
```

**14. IP 情報を確認します**

このインターフェイスの IP 構成は、ModemManager のベアラーの詳細と一致している必要があります。
```
sudo ip address show dev wwan0
```
ベアラー IP の一覧が次のように表示されていることを確認します。
```
wwan0: <BROADCAST,MULTICAST,NOARP,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UNKNOWN group default qlen 1000
    link/ether c2:12:44:c4:27:3c brd ff:ff:ff:ff:ff:ff
    inet 162.177.2.0/22 scope global wwan0
       valid_lft forever preferred_lft forever
    inet6 fe80::c012:44ff:fec4:273c/64 scope link 
       valid_lft forever preferred_lft forever
```

**15. 既定のルートを設定します**

ベアラーによって提供された情報を再度使用し、ネットワーク パケットの既定の宛先としてモデムのゲートウェイを使用します (162.177.2.1 を置き換えます)。
```
sudo ip route add default via 162.177.2.1 dev wwan0
```
これで、LTE モデムを使用して Azure Percept を Azure に接続できるようになりました。


**16. 接続をテストします**

`wwan0` インターフェイスを介して `ping` 要求を実行します。 また、Azure Percept Studio を使用して、テレメトリ メッセージが受信されるかどうかを確認することもできます (LTE を確実に使用されるように、イーサネット ケーブルまたは Wi-Fi が有効になっていないことをご確認ください)
```
ping -I wwan0 8.8.8.8
```
次のように表示されます
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
一般的な情報については、[USB モデムを使用して接続する](./connect-over-cellular-usb.md)方法に関するページを参照してください。
   
### <a name="vodafone-modem-rules-to-mitigate-enumeration-issues"></a>列挙の問題を軽減するための Vodafone モデムの規則

モデムが、サポートされていないモードで列挙されないように、次の UDEV ルールを使用して、不要なインターフェイスが ModemManager で無視されるようにすることをお勧めします。

作成するファイル: 次の内容を含む `/usr/lib/udev/rules.d/77-mm-vodafone-port-types.rules`。
```
ACTION!="add|change|move|bind", GOTO="mm_vodafone_port_types_end"
SUBSYSTEMS=="usb", ATTRS{idVendor}=="1bbb", GOTO="mm_vodafone_generic_vendorcheck"
GOTO="mm_vodafone_port_types_end"

LABEL="mm_vodafone_generic_vendorcheck"
SUBSYSTEMS=="usb", ATTRS{bInterfaceNumber}=="?*", ENV{.MM_USBIFNUM}="$attr{bInterfaceNumber}"

# Interface 1 is QDCM (ignored) and interfaces 3 and 4 are MBIM Control and Data.
ATTRS{idVendor}=="1bbb", ATTRS{idProduct}=="00b6", ENV{.MM_USBIFNUM}=="00", ENV{ID_MM_PORT_TYPE_AT_PRIMARY}="1"
ATTRS{idVendor}=="1bbb", ATTRS{idProduct}=="00b6", ENV{.MM_USBIFNUM}=="01", ENV{ID_MM_PORT_IGNORE}="1"
ATTRS{idVendor}=="1bbb", ATTRS{idProduct}=="00b6", ENV{.MM_USBIFNUM}=="02", ENV{ID_MM_PORT_AT_SECONDARY}="1"

GOTO="mm_vodafone_port_types_end"

LABEL="mm_vodafone_port_types_end"
```
インストールしたら、UDEV ルールを再度読み込み、ModemManager を再起動します。
```
udevadm control -R
systemctl restart ModemManager
```
## <a name="next-steps"></a>次のステップ
[USB モデムを使用して接続します](./connect-over-cellular-usb.md)。

5G または LTE のメインの記事に戻ります。

[5G または LTE を使用して接続します](./connect-over-cellular.md).
