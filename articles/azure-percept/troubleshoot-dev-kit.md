---
title: Azure Percept DK と IoT Edge に関する一般的な問題をトラブルシューティングする
description: オンボーディング エクスペリエンス中に多く見られるいくつかの問題について、トラブルシューティングのヒントを取り上げます
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: c8027b62c0c463e134817f589ba3e1957cea5b39
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2021
ms.locfileid: "101678648"
---
# <a name="azure-percept-dk-dev-kit-troubleshooting"></a>Azure Percept DK (開発キット) のトラブルシューティング

Azure Percept DK の一般的なトラブルシューティングのヒントについては、以下のガイダンスを参照してください。

## <a name="general-troubleshooting-commands"></a>一般的なトラブルシューティング コマンド

これらのコマンドを実行するには、次のようにします。 
1. [開発キットの Wi-Fi AP](./quickstart-percept-dk-set-up.md) に接続します
1. [開発キットに SSH で接続](./how-to-ssh-into-percept-dk.md)します
1. SSH ターミナルにコマンドを入力します

詳しく分析するために、出力を .txt ファイルにリダイレクトするには、次の構文を使用します。

```console
[command] > [file name].txt
```

出力を .txt ファイルにリダイレクトしたら、SCP を使用してファイルをホスト PC にコピーします。

```console
scp [remote username]@[IP address]:[remote file path]/[file name].txt [local host file path]
```

```[local host file path]``` は、.txt ファイルのコピー先となるホスト PC 上の場所を指します。 ```[remote username]``` は、[セットアップ エクスペリエンス](./quickstart-percept-dk-set-up.md)中に選択された SSH ユーザー名です。 OOBE 中に SSH ログインを設定しなかった場合、リモートユーザー名は ```root``` になります。

Azure IoT Edge コマンドの詳細については、[Azure IoT Edge デバイスのトラブルシューティングに関するドキュメント](https://docs.microsoft.com/azure/iot-edge/troubleshoot)を参照してください。

|カテゴリ:         |コマンド:                    |関数:                  |
|------------------|----------------------------|---------------------------|
|OS                |```cat /etc/os-release```         |Mariner イメージ バージョンを確認します |
|OS                |```cat /etc/os-subrelease```      |派生イメージ バージョンを確認します |
|OS                |```cat /etc/adu-version```        |ADU バージョンを確認します |
|気温       |```cat /sys/class/thermal/thermal_zone0/temp``` |開発キットの温度を確認します |
|Wi-Fi             |```journalctl -u hostapd.service``` |SoftAP ログを確認します|
|Wi-Fi             |```journalctl -u wpa_supplicant.service``` |Wi-Fi サービス ログを確認します |
|Wi-Fi             |```journalctl -u ztpd.service```  |Wi-Fi ゼロタッチ プロビジョニング サービスのログを確認します |
|Wi-Fi             |```journalctl -u systemd-networkd``` |Mariner ネットワーク スタックのログを確認します |
|Wi-Fi             |```/data/misc/wifi/hostapd_virtual.conf``` |WiFi アクセス ポイントの構成の詳細を確認します |
|OOBE              |```journalctl -u oobe -b```       |OOBE ログを確認します |
|テレメトリ         |```azure-device-health-id```      |一意のテレメトリ HW_ID を検索します |
|Azure IoT Edge          |```sudo iotedge check```          |一般的な問題について、構成と接続のチェックを実行します |
|Azure IoT Edge          |```sudo iotedge logs [container name]``` |音声モジュールやビジョン モジュールなどのコンテナーログを確認します |
|Azure IoT Edge          |```sudo iotedge support-bundle --since 1h``` |モジュールのログ、Azure IoT Edge セキュリティ マネージャーのログ、コンテナー エンジンのログ、```iotedge check``` JSON 出力など、直近 1 時間にさかのぼって有益なデバッグ情報を収集します |
|Azure IoT Edge          |```sudo journalctl -u iotedge -f``` |Azure IoT Edge Security Manager のログを確認します |
|Azure IoT Edge          |```sudo systemctl restart iotedge``` |Azure IoT Edge セキュリティ デーモンを再起動します |
|Azure IoT Edge          |```sudo iotedge list```           |デプロイされている Azure IoT Edge モジュールを一覧表示します |
|その他             |```df [option] [file]```          |指定されたファイル システムの空き領域と合計領域に関する情報を表示します |
|その他             |```ip route get 1.1.1.1```        |デバイスの IP とインターフェイスの情報を表示します |
|その他             |```ip route get 1.1.1.1 \| awk '{print $7}'``` <br> ```ifconfig [interface]``` |デバイスの IP アドレスのみを表示します |


```journalctl``` Wi-Fi コマンドは、次のように 1 つにまとめることができます。

```console
journalctl -u hostapd.service -u wpa_supplicant.service -u ztpd.service -u systemd-networkd -b
```

## <a name="docker-troubleshooting-commands"></a>Docker のトラブルシューティング コマンド

|コマンド:                        |関数:                  |
|--------------------------------|---------------------------|
|```docker ps``` |[実行中のコンテナーを表示します](https://docs.docker.com/engine/reference/commandline/ps/) |
|```docker images``` |[デバイス上にあるイメージを表示します](https://docs.docker.com/engine/reference/commandline/images/)|
|```docker rmi [image id] -f``` |[デバイスからイメージを削除します](https://docs.docker.com/engine/reference/commandline/rmi/) |
|```docker logs -f edgeAgent``` <br> ```docker logs -f [module_name]``` |[指定したモジュールのコンテナーのログを取得します](https://docs.docker.com/engine/reference/commandline/logs/) |
|```docker image prune``` |[未解決のイメージをすべて削除します](https://docs.docker.com/engine/reference/commandline/image_prune/) |
|```watch docker ps``` <br> ```watch ifconfig [interface]``` |Docker コンテナーのダウンロード状態を確認します |

## <a name="usb-updating"></a>USB の更新

|エラー:                                    |解決方法:                                               |
|------------------------------------------|--------------------------------------------------------|
|LIBUSB_ERROR_XXX during USB flash via UUU (UUU での USB フラッシュ中に LIBUSB_ERROR_XXX が発生しました) |このエラーは、UUU 更新中に USB 接続に失敗したことが原因で発生します。 PE-10X または PC 上の USB ポートに USB ケーブルが適切に接続されていない場合、この形式のエラーが発生します。 USB ケーブルの両端を抜いてから再度差し込み、ケーブルを軽く揺すって、しっかり接続されていることを確認します。 ほとんどの場合、これで問題は解決します。 |

## <a name="azure-percept-dk-carrier-board-led-states"></a>Azure Percept DK キャリア ボードの LED の状態

キャリア ボードの筐体上部には、小さな LED が 3 つあります。 LED 1 の横にはクラウド アイコンが、LED 2 の横には Wi-Fi アイコンが、LED 3 の横には感嘆符アイコンが印字されています。 各 LED 状態については、次の表を参照してください。

|LED             |State      |説明                      |
|----------------|-----------|---------------------------------|
|LED 1 (IoT Hub) |オン (点灯) |デバイスは IoT ハブに接続されています。 |
|LED 2 (Wi-Fi)   |ゆっくり点滅 |デバイスの認証が進行中です。 |
|LED 2 (Wi-Fi)   |高速点滅 |認証に成功しました。デバイスの関連付けを行っています。 |
|LED 2 (Wi-Fi)   |オン (点灯) |認証と関連付けに成功しました。デバイスは Wi-Fi ネットワークに接続されています。 |
|LED 3           |NA         |LED が使用されていません。 |


