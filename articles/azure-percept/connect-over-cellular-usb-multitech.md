---
title: USB モデム MultiTech Multiconnect を使用して Azure Percept DK を LTE で接続する
description: この記事では、MultiTech MultiConnect USB モデムを使用して、Azure Percept DK を 5G または LTE ネットワークで接続する方法を説明します。
author: juhaluoto
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 09/23/2021
ms.custom: template-how-to
ms.openlocfilehash: 5000c7be25a82069da0585a40252c91262a7ea12
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130252442"
---
# <a name="connect-azure-percept-dk-over-lte-by-using-a-multitech-multiconnect-usb-modem"></a>USB モデム MultiTech Multiconnect を使用して Azure Percept DK を LTE で接続する 

この記事では、MultiTech MultiConnect (MTCM-LNA3-B03) USB モデムを使用して、Azure Percept DK を接続する方法を説明します。 

> [!Note]
> MultiTech MultiConnect USB モデムには、さまざまなモデルがあります。 この記事では、Verizon、Vodafone などの SIM カードに対応している LNA3 モデルを使用します。 現在、AT&T のネットワークに接続することができませんが、目下問題を調査しており、根本原因が分かり次第この記事を更新します。 MultiTech MultiConnect USB モデムの詳しい情報は、[MultiTech](https://www.multitech.com/brands/multiconnect-microcell) のサイトでご覧ください。

## <a name="prepare-to-connect-azure-percept-dk"></a>Azure Percept DK の接続準備をする
Azure Percept DK の接続準備の方法は「[USB モデムを使用して Azure Percept を 5G または LTE ネットワークで接続する](./connect-over-cellular-usb.md)」をご覧ください。 使用する USB ケーブルに関するコメントをご確認ください。 

### <a name="prepare-the-modem"></a>モデムを準備する
作業開始前に、モデムを Mobile Broadband Interface Model (MBIM) モードにする必要があります。 モデムを準備する方法は、[Telit wireless solutions の Attention (AT) コマンド参照ガイド](
https://www.telit.com/wp-content/uploads/2018/01/Telit-LE910-V2-Modules-AT-Commands-Reference-Guide-r3.pdf)をご覧ください。

この記事では、MBIM インターフェイスを有効にするために、AT コマンド `AT#USBCFG=<mode>` で適切な USB モードを設定します。

AT コマンド参照ガイドでは選択できるすべてのモードを説明していますが、この記事では `3` モードだけを扱います。 既定のモードは `0` です。

モードを設定する最も簡単な方法は、MultiTech のモデムを PC に接続して、TeraTerm や PuTTY などのターミナル ソフトウェアを使用する方法です。 モデムに割り当てている USB ポートは Windows デバイス マネージャーで確認できます。 複数のポートがある場合は、テストを実行して、AT コマンドに反応しているポートを確かめなければならない場合があります。 COM ポートは次のように設定します。
* **ボー レート**: 9600 (または 115200)
* **ストップ ビット**: 1
* **パリティ**: なし
* **byte サイズ**: 8
* **フロー制御**: 制御フローなし

AT コマンドは次のとおりです。

MultiTech デバイスが現在どのモードで動作しているかを確認するには、次のものを実行します。

```
AT#USBCFG?
```

モード 3 に変更するには、次のものを実行します。

```
AT#USBCFG=3
```

最初の AT コマンドでもう一度確認すると、`#USBCFG: 3` という結果が得られるはずです。

適切な USB モードを設定してから、次のものを実行してリセット命令を送ります。

```
AT#REBOOT
```

すると、モデムの接続がいったん解除され、前に設定したモードで USB ポートに再接続します。

## <a name="use-the-modem-to-connect"></a>モデムを使用して接続する

[USB モデムを使用して接続する方法](./connect-over-cellular-usb.md)に関する記事で概説している、Azure Percept DK の準備を済ませてください。   

1. MultiTech モデムに SIM カードを挿入します。

1. MultiTech モデムを Azure Percept DK の USB A ポートに接続します。

1. Azure Percept DK を起動します。

1. Secure Shell (SSH) ネットワーク プロトコルで Azure Percept DK に接続します。

1. SSH プロンプトに次のコマンドを入力し、ModemManager が立ち上がっていることを確認します。

    ```
    systemctl status ModemManager
    ```
    作業が順調に進んでいれば、次のような結果が得られます。

    *ModemManager.service - Modem Manager* *Loaded: loaded (/lib/systemd/system/ModemManager.service; enabled; vendor preset: enabled)* *Active: active (running) since Mon 2021-08-09 20:52:03 UTC; 23 s ago* (ModemManager.service - Modem Manager ロード済み: ロード済み (/lib/systemd/system/ModemManager.service; 有効; ベンダー プリセット: 有効) アクティブ: Mon 2021-08-09 20:52:03 UTC (23 秒前) からアクティブ (実行中))

1. アクティブなモデムを一覧表示します。

    次を実行し、ModemManager でモデムが認識されることを確認します。

    ```
    mmcli --list-modems
    ```

    次のような結果が得られます。

    ```
    /org/freedesktop/ModemManager1/Modem/0 [Telit] FIH7160
    ```

1. モデムの詳細を取得します。

    ここではモデム ID が `0` となっていますが、実際の結果はこれと異なる場合があります。 モデム ID (`--modem 0`) を使用して、ModemManager コマンドをこのように実行します。
    
    ```
    mmcli --modem 0
    ```
    
    既定では、モデムは無効になっています (`Status -> state: disabled`)。

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

1. モデムを有効にします。

    接続を確立するには、次のコードを実行して、モデムの無線を有効にしておく必要があります。

    ```
    mmcli --modem 0 --enable
    ```

    “successfully enabled the modem.” (モデムを有効にしました。) のような応答があります。

    しばらくするとモデムが基地局に登録され、次のコードを実行したときに、`Status -> state: registered` というモデムの状態が表示されるようになります。

    ```
    mmcli --modem 0
    ```

1. アクセス ポイント名 (APN) の情報を使用して接続します。

    APN は携帯電話プロバイダーから提供されます。たとえば、Verizon だと次のようなものになります。

    ```
    mmcli --modem 0 --simple-connect="apn=vzwinternet"  
    ```

    “successfully enabled the modem.” (モデムを有効にしました。) のような応答があります。

1. モデムの状態を取得します。

    `Status -> state: connected` という状態が表示され、ステータス メッセージの最後に `Bearer` という新しいカテゴリが表示されるようになります。

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

1. ベアラーの詳細を取得する。

    今モデムで確立した携帯電話ネットワークへのパケット データ接続に、オペレーティング システムを接続するには、ベアラーの詳細情報が必要です。 この時点で、モデムでは IP 接続を確立できていますが、オペレーティング システムではまだ、それを使用できるように設定していません。
  
    ```
    mmcli --bearer 0
    ```

    次のコードで、ベアラーの詳細を一覧表示します。

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

1. ネットワーク インターフェイスを起動します。

    ```
    sudo ip link set dev wwan0 up
    ```

1. ネットワーク インターフェイスを設定します。

    IP アドレスを、ベアラーから取得する情報に含まれる IP アドレスに置き換えます (例として、ここでは 100.112.107.46/24 を使用しています)。

    ```
    sudo ip address add 100.112.107.46/24 dev wwan0
    ```

1. IP 情報を確認します。

    このインターフェイスの IP 構成は、ModemManager のベアラーの詳細と一致している必要があります。 次を実行します。

    ```
    sudo ip address show dev wwan0
    ```

    このように、ベアラー IP の情報が一覧表示されます。

    ```
    6: wwan0: <BROADCAST,MULTICAST,NOARP,UP,LOWER_UP> mtu 1428 qdisc pfifo_fast state UNKNOWN group default qlen 1000
        link/ether 1e:fb:08:e9:2a:25 brd ff:ff:ff:ff:ff:ff
        inet 100.112.107.46/24 scope global wwan0
          valid_lft forever preferred_lft forever
        inet6 fe80::1cfb:8ff:fee9:2a25/64 scope link
          valid_lft forever preferred_lft forever
    ```

1. デフォルト ルートを設定します。

    先ほどと同様に、ベアラーから取得した情報に含まれるモデムのゲートウェイを、ネットワーク パケットのデフォルトの宛先に指定して、次のものを実行します (実際に実行するときは 100.112.107.1 を必要な値に置き換えてください)。

    ```
    sudo ip route add default via 100.112.107.1 dev wwan0
    ```

    これで Azure Percept DK を USB をモデムに接続できました。

1. 接続をテストします。

    この記事では、`ping` インターフェイスで `wwan0` 要求を実行します。 その他に、Azure Percept Studio でテレメトリ メッセージが届くかどうかを確認する方法もあります。 LTE を使用するため、Ethernet ケーブルを使用していないこと、Wi-Fi が有効になっていないことを確認してください。 次を実行します。

    ```
    ping -I wwan0 8.8.8.8
    ```

    次のような結果が得られます。

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

デバッグに関する一般的な情報は、[USB モデムを使用して接続する方法](./connect-over-cellular-usb.md)に関する記事をご覧ください。

## <a name="next-steps"></a>次のステップ

アクセス先の携帯デバイスに応じて、次の 2 つのうちどちらかの方法で接続できます。

* [USB モデムを使用して接続する](./connect-over-cellular-usb.md)
* [5G または LTE で接続する](./connect-over-cellular.md)
