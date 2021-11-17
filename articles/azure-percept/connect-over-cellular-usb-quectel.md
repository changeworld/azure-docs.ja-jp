---
title: Quectel RM500 5G モデムを使用して 5G または LTE 経由で Azure Percept DK を接続する
description: この記事では、Quectel 5G モデムを使用して 5G または LTE ネットワークで Azure Percept DK を接続する方法について説明します。
author: juhaluoto
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 09/03/2021
ms.custom: template-how-to
ms.openlocfilehash: de4f889883b7c3da4e8129fc66d31aed094ebda6
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130252423"
---
# <a name="connect-azure-percept-dk-over-5g-or-lte-by-using-a-quectel-rm500-gl-5g-modem"></a>Quectel RM500-GL 5G モデムを使用して 5G または LTE 経由で Azure Percept DK を接続する  

この記事では、Quectel RM500-GL 5G モデムを使用して 5G または LTE で Azure Percept DK を接続する方法について説明します。 

この 5G モデム開発キットについて詳しくは、お近くの Quectel セールス チームにお問い合わせください。

* 北米のお客様の場合: northamerica-sales@quectel.com
* 世界各地のお客様の場合: sales@quectel.com

> [!Note] 
> **5G モデム用の USB ケーブルについて**:  
> 5G モデムは LTE モデムより多くの電力を必要とし、不適切な USB ケーブルを使用すると、最善の 5G データ速度の実現に対してボトルネックになる可能性があります。 5G モデムに十分な電源を安定して供給するには、USB ケーブルが次の標準を満たしていることを確認します。  
> **電力:**
> - 最大アンペア数は、3 アンペア以上である必要があります。
> - ケーブルの長さは、1 メートル未満にする必要があります。
> - 5G モデムを使用するときは、Azure Percept DK 上の 1 つの USB A ポートのみをアクティブにする必要があります。
>  
> **スループット**:
> - USB 3.1 Gen2
> - USB-IF 認定済み

## <a name="prepare-to-connect-azure-percept-dk"></a>Azure Percept DK の接続を準備する
Azure Percept DK を準備する方法については、[USB モデムを使用した 5G または LTE ネットワーク経由での Azure Percept DK の接続](./connect-over-cellular-usb.md)に関する記事をご覧ください。 使用する必要がある USB ケーブルに関するコメントに注意してください。 

### <a name="prepare-the-modem"></a>モデムを準備する
始める前に、モデムをモバイル ブロードバンド インターフェイス モデル (MBIM) モードにしておく必要があります。 ドキュメントには記載されていませんが、これには標準の Quectel Attention (AT) コマンドを使用できます: `AT+QCFG="usbnet"`。

`usbnet` プロパティは、`0` から `3` の 4 つの異なる値に設定できます。
- `0` - **NDIS/PPP/QMI モード** (`CONFIG_USB_NET_QMI_WWAN=y|m` で有効な `qmi_wwan` ドライバーでサポート)
- `1` - **CDC イーサネット モード** (`CONFIG_USB_NET_CDCETHER=y|m` のときに Linux でサポート)
- `2` - **MBIM モード** (`CONFIG_USB_NET_CDC_MBIM=y|m` のときに Linux でサポート)
- `3` - **RNDIS モード**

モードを構成する最も簡単な方法は、Quectel 5G モデムを PC に接続して、TeraTerm のようなターミナル ソフトウェアや、QCOM のような Quectel 独自の PC ソフトウェアを使用することです。 Windows デバイス マネージャーを使用して、モデムに割り当てられている USB ポートを確認できます。 COM ポートは次のように設定されている必要があります。
* **ボー レート**: 115200
* **ストップ ビット**: 1
* **パリティ**: なし
* **バイト サイズ**: 8
* **フロー制御**: フロー制御なし

AT コマンドを次に示します。

現在動作中の USB モード Quectel デバイスを確認するには、以下を使用します。

```
AT+QCFG="usbnet"
```

モード 2 に変更するには、以下を使用します。

```
AT+QCFG="usbnet",2
```

最初の AT コマンドを使用してもう一度確認すると、次のようになるはずです。

```
+QCFG: "usbnet",2`
```

正しい USB モードを設定した後、以下を使用してハードウェア リセットを実行します。

```
AT+CFUN=1,1
```

この時点で、モデムは接続を切断し、後で USB ポートに再接続します。


## <a name="use-the-modem-to-connect"></a>モデムを使用して接続する

1. SIM カードを Quectel モデムに装着します。

1. Quectel モデムを Azure Percept DK の USB ポートに接続します。 必ず適切な USB ケーブルを使用してください。

1. Azure Percept DK の電源をオンにします。

1. ModemManager が実行されていることを確認します。

      ```
      systemctl status ModemManager
      ```
   
      成功すると、次のような結果が表示されます。

      ```
      * ModemManager.service - Modem Manager
         Loaded: loaded (/lib/systemd/system/ModemManager.service; enabled; vendor pre set: enabled)
         Active: active (running) since Mon 2021-08-09 20:52:03 UTC; 23s ago
      [...]
      ```

      成功しなかった場合は、Azure Percept DK に正しいイメージをフラッシュしたことを確認します (5G 対応)。

1. アクティブなモデムの一覧を表示します。

      モデムを一覧表示すると、Quectel モデムが認識され、現在は ModemManager によって処理されていることがわかります。

      ```
      mmcli --list-modems
      ```
   
      次のような結果が表示される必要があります。

      ```
      /org/freedesktop/ModemManager1/Modem/0 [Quectel] RM500Q-GL
      ```

      ここでのモデム ID は `0` です。これは、アドレス指定 (つまり、`--modem 0`) のために次のコマンドで使用されます。

1. モデムの詳細を取得します。

      既定では、モデムは無効 (`Status -> state: disabled`) になっています。 状態を表示するには、以下を実行します。

      ```
      mmcli --modem 0
      ```
      
      次のような結果が表示される必要があります。

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

1. モデムを有効にします。

      接続を確立する前に、次を実行してモデムの無線をオンにします。

      ```
      mmcli --modem 0 --enable
      ```

      次のような応答が表示される必要があります。

      ```
      successfully enabled the modem
      ```
   
      しばらくすると、モデムがセル タワーに登録され、次のコードを実行するとモデムの状態 `Status -> state: registered` が表示されます。
   
      ```
      mmcli --modem 0
      ```

1. アクセス ポイント名 (APN) の情報を使用して接続します。

      通常は、使用する APN はモデムによって提供されるので (`3GPP EPS -> initial bearer APN` の情報を参照)、それを使用して接続を確立できます。 モデムで APN が提供されない場合は、使用する APN を携帯電話会社にお問い合わせください。 
      
      次に示すのは、たとえば Verizon の APN `APN=vzwinternet` を使用して接続するための ModemManager のコマンドです

      ```
      mmcli --modem 0 --simple-connect="apn=vzwinternet"
      ```

      やはり、次のような応答が表示される必要があります。

      ```
      successfully connected the modem
      ```

1. モデムの状態を取得します。

      これで、ステータス メッセージの最後に状態 `Status -> state: connected` と新しいカテゴリ `Bearer` が表示されるはずです。
 
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

1. ベアラーの詳細を取得します。

      前のステップ `--simple-connect` の結果として得られるベアラーは、パス `/org/freedesktop/ModemManager1/Bearer/1` にあります。

      これは、アクティブな接続に関するモデムの情報を照会しているベアラーです。 最初のベアラーは、アクティブな接続に接続されていないため、IP 情報を保持していません。

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

      いくつかの重要な詳細を次に示します。
      - `Status -> interface: wwan0`: このモデムに一致する Linux ネットワーク インターフェイスの一覧を表示します。
      - `IPv4 configuration`: 上のインターフェイスを使用できるように設定するための IP 構成を提供します。

1. モデム ネットワーク インターフェイスの状態をチェックします。

      既定では、ネットワーク インターフェイスに `DOWN` と表示されます。

      ```
      ip link show dev wwan0
      ```

      次のような結果が表示される必要があります。

      ```
      4: wwan0: <BROADCAST,MULTICAST,NOARP> mtu 1500 qdisc noop state DOWN mode DEFAULT group default qlen 1000
         link/ether ce:92:c2:b8:1e:f2 brd ff:ff:ff:ff:ff:ff
      ```

1. インターフェイスを表示します。

      ```
      sudo ip link set dev wwan0 up
      ```

1. IP の情報を確認します。

      既定では、インターフェイスに `UP,LOWER_UP` と表示され、IP 情報はありません。

      ```
      sudo ip address show dev wwan0
      ```

      ```
      4: wwan0: <BROADCAST,MULTICAST,NOARP,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UNKNOWN group default qlen 1000
         link/ether ce:92:c2:b8:1e:f2 brd ff:ff:ff:ff:ff:ff
         inet6 fe80::cc92:c2ff:feb8:1ef2/64 scope link 
            valid_lft forever preferred_lft forever
      ```

1. DHCP 要求を発行します。

      この機能は、Quectel モジュールに固有ですが、これに限定されません。 通常、IP 情報は、インターフェイスまたは ModemManager をサポートするネットワークマネージャー デーモン (NetworkManager など) を介して手動で設定する必要がありますが、ここでは、単に Quectel モデムで dhclient を使用することができます。

      ```
      sudo dhclient wwan0
      ```

1. IP の情報を確認します。

      このインターフェイスの IP 構成は、ModemManager のベアラーの詳細と一致している必要があります。

      ```
      sudo ip address show dev wwan0
      ```

      次のような結果が表示される必要があります。

      ```
      4: wwan0: <BROADCAST,MULTICAST,NOARP,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UNKNOWN group default qlen 1000
         link/ether ce:92:c2:b8:1e:f2 brd ff:ff:ff:ff:ff:ff
         inet 25.21.113.165/30 brd 25.21.113.167 scope global wwan0
            valid_lft forever preferred_lft forever
         inet6 fe80::cc92:c2ff:feb8:1ef2/64 scope link 
            valid_lft forever preferred_lft forever
      ```

1. インターフェイス ルートを確認します。

      DHCP クライアントで、`wwan0` インターフェイスを通過するパケットの既定のルートも設定されていることに注意してください。

      ```
      ip route show dev wwan0
      ```

      次のような結果が表示される必要があります。

      ```
      default via 25.21.113.166 
      25.21.113.164/30 proto kernel scope link src 25.21.113.165
      ```

      これで、Quectel モデムを使用して Azure Percept DK への接続が確立されました。


1. 接続をテストします。

      `wwan0` インターフェイスを介して `ping` 要求を実行します。

      ```
      ping -I wwan0 8.8.8.8
      ```

      次のような結果が表示される必要があります。

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

デバッグに関する一般的な情報については、[USB モデムを使用した接続](./connect-over-cellular-usb.md)に関する記事をご覧ください。

## <a name="next-steps"></a>次のステップ

アクセスできる携帯デバイスに応じて、次の 2 つの方法のいずれかを使用して接続できます。

* [USB モデムを使用して接続する](./connect-over-cellular-usb.md)
* [5G または LTE を使用して接続する](./connect-over-cellular.md)
