---
title: Vodafone USB モデムを使用して 5G および LTE 経由で Azure Percept DK を接続する
description: この記事では、Vodafone USB モデムを使用して 5G または LTE ネットワークで Azure Percept DK を接続する方法について説明します。
author: juhaluoto
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 09/23/2021
ms.custom: template-how-to
ms.openlocfilehash: 226fc6890253bd4701b400dcd42c420618701630
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130252366"
---
# <a name="connect-azure-percept-dk-over-5g-and-lte-by-using-a-vodafone-usb-connect-4g-v2-modem"></a>Vodafone USB Connect 4G v2 モデムを使用して 5G および LTE 経由で Azure Percept DK を接続する

この記事では、Vodafone USB Connect 4G v2 モデムを使用して Azure Percept DK を接続する方法について説明します。

このモデムについて詳しくは、[Vodafone 統合端末](https://www.vodafone.com/business/iot/iot-devices/integrated-terminals)に関するページをご覧ください。

## <a name="use-the-modem-to-connect"></a>モデムを使用して接続する

始める前に、[USB モデムを使用した接続](./connect-over-cellular-usb.md)用に Azure Percept DK の準備ができていることを確認します。 USB モデム自体の準備は必要ありません。   

1. Vodafone モデムに SIM カードを挿入します。

1. Vodafone モデムを Azure Percept の USB A ポートに接続します。

1. Azure Percept DK の電源をオンにします。

1. Secure Shell (SSH) ネットワーク プロトコルを使用して Azure Percept DK を接続します。

1. SSH プロンプトで次のコマンドを入力して、ModemManager が実行されていることを確認します。

    ```
    systemctl status ModemManager
    ```

    成功すると、次のような結果が表示されます。

    ```
    ModemManager.service - Modem Manager
    Loaded: loaded (/lib/systemd/system/ModemManager.service; enabled; vendor preset: enabled)
    Active: active (running) since Mon 2021-08-09 20:52:03 UTC; 23 s ago
    ```

1. アクティブなモデムの一覧を表示します。

    お使いのモデムを ModemManager が認識できることを確認するには、次のように実行します。

    ```
    mmcli --list-modems
    ```

    次のような結果が表示される必要があります。 ここではモデム ID は `0` ですが、実際の結果は異なる場合があります。

    ```
    /org/freedesktop/ModemManager1/Modem/0 [Alcatel] Mobilebroadband
    ```

1. モデムの詳細を取得します。

    モデムの状態の詳細を取得するには、次のコマンドを実行します (モデム ID は `0` です)。

    ```
    mmcli --modem 0
    ```

    既定では、モデムは無効 (`Status -> state: disabled`) になっています。

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

    既定の設定で始めることをお勧めします。 
    
    `Modes: current: allowed: 2g, 3g, 4g; preferred: 2g`. 
    
    この設定をまだ使用していない場合は、次のように実行します。
    
     `mmcli --modem 0 --set-allowed-modes='2g|3g|4g' --set-preferred-mode='2g'`.

1. モデムを有効にします。

    接続を確立する前に、次のコードを実行してモデムの無線をオンにします。

    ```
    mmcli --modem 0 --enable
    ```

    "successfully enabled the modem" (モデムが正常に有効になりました) のような応答が表示されます。

    しばらくすると、モデムがセル タワーに登録され、次のコードを実行するとモデムの状態 `Status -> state: registered` が表示されます。

    ```
    mmcli --modem 0
    ```

1. アクセス ポイント名 (APN) の情報を使用して接続します。

    APN は携帯電話プロバイダーから提供されます。Vodafone の場合は次のような APN になります。

    ```
    mmcli --modem 0 --simple-connect="apn=internet4gd.gdsp"  
    ```

    "successfully connected the modem" (モデムが正常に接続されました) のような応答が表示されます。

1. モデムの状態を取得します。

    これで、ステータス メッセージの最後に状態 `Status -> state: connected` と新しいカテゴリ `Bearer` が表示されるはずです。

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

1. ベアラーの詳細を取得します。

    モデムと携帯ネットワークの間に確立されたパケット データ接続にオペレーティング システムを接続するには、ベアラーの詳細が必要です。 この時点で、モデムには IP 接続がありますが、オペレーティング システムはまだそれを使用するように構成されていません。

    ```
    mmcli --bearer 0
    ```

    ベアラーの詳細が次のコードで一覧表示されます。

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

1. ネットワーク インターフェイスを起動します。

    ```
    sudo ip link set dev wwan0 up
    ```

1. ネットワーク インターフェイスを構成します。

    ベアラーによって提供される情報を使用し、IP アドレス (たとえば、ここでは 162.177.2.0/22 を使用しています) を、ベアラーのものに置き換えます。

    ```
    sudo ip address add 162.177.2.0/22 dev wwan0
    ```

1. IP の情報を確認します。

    このインターフェイスの IP 構成は、ModemManager のベアラーの詳細と一致している必要があります。 次を実行します。

    ```
    sudo ip address show dev wwan0
    ```

    次に示すように、ベアラー IP の一覧が表示されます。

    ```
    wwan0: <BROADCAST,MULTICAST,NOARP,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UNKNOWN group default qlen 1000
        link/ether c2:12:44:c4:27:3c brd ff:ff:ff:ff:ff:ff
        inet 162.177.2.0/22 scope global wwan0
          valid_lft forever preferred_lft forever
        inet6 fe80::c012:44ff:fec4:273c/64 scope link 
          valid_lft forever preferred_lft forever
    ```

1. 既定のルートを設定します。

    ここでも、ベアラーによって提供された情報を使用し、ネットワーク パケットの既定の宛先としてモデムのゲートウェイを使用して (162.177.2.1 を置き換えます)、次のように実行します。

    ```
    sudo ip route add default via 162.177.2.1 dev wwan0
    ```

    これで、Azure Percept DK が、LTE モデムを使用して Azure に接続できるようになります。


1. 接続をテストします。

    この記事では、`wwan0` インターフェイスを介して `ping` 要求を実行します。 ただし、Azure Percept Studio を使用して、テレメトリ メッセージが到着しているかどうかを確認することもできます。 LTE を使用しているので、イーサネット ケーブルを使用していないこと、および Wi-Fi が有効になっていないことを確認します。 次を実行します。

    ```
    ping -I wwan0 8.8.8.8
    ```

    次のような結果が表示される必要があります。

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

デバッグに関する一般的な情報については、[USB モデムを使用した接続](./connect-over-cellular-usb.md)に関する記事をご覧ください。
   
### <a name="vodafone-modem-rules-to-mitigate-enumeration-issues"></a>列挙の問題を軽減するための Vodafone モデムの規則

モデムがサポートされていないモードで列挙されないようにするため、次の userspace/dev (udev) ルールを適用して、Modem Manager で不要なインターフェイスが無視されるようにすることをお勧めします。

次のような内容で */usr/lib/udev/rules.d/77-mm-vodafone-port-types.rules* ファイルを作成します。

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

インストールした後、udev ルールを再度読み込んで、ModemManager を再起動します。

```
udevadm control -R
systemctl restart ModemManager
```

## <a name="next-steps"></a>次のステップ

アクセスできる携帯デバイスに応じて、次の 2 つの方法のいずれかを使用して接続できます。

* [USB モデムを使用して接続する](./connect-over-cellular-usb.md)
* [5G または LTE を使用して接続する](./connect-over-cellular.md)
