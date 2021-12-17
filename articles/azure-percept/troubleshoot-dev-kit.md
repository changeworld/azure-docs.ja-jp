---
title: Azure Percept DK デバイスのトラブルシューティング
description: Azure Percept DK と IoT Edge の一般的な問題に対するトラブルシューティングのヒントを入手する
author: juniem
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 08/10/2021
ms.custom: template-how-to
ms.openlocfilehash: 8db131bd39ae8ebe27720a7d725f6ab8082dfd83
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124780017"
---
# <a name="troubleshoot-the-azure-percept-dk-device"></a>Azure Percept DK デバイスのトラブルシューティング

このトラブルシューティング記事の目的は、Azure Percept DK ユーザーが開発キットに関する一般的な問題を迅速に解決できるようにすることです。 追加のサポートが必要な場合のログの収集に関するガイダンスも提供します。

## <a name="log-collection"></a>ログ コレクション
このセクションでは、収集するログとその収集方法に関するガイダンスを示します。

### <a name="how-to-collect-logs"></a>ログを収集する方法
1. [SSH 経由](./how-to-ssh-into-percept-dk.md)で開発キットに接続します。
1. SSH ターミナル ウィンドウ内で必要なコマンドを実行します。 ログ収集コマンドの一覧については、次のセクションを参照してください。
1. 詳しく分析するために、出力を .txt ファイルにリダイレクトし、次の構文を使用します。
    ```console
    sudo [command] > [file name].txt
    ```
1. .txt ファイルのアクセス許可を変更して、コピーできるようにします。
    ```console
    sudo chmod 666 [file name].txt
    ```
1. SCP を介してファイルをホスト PC にコピーします。
    ```console
    scp [remote username]@[IP address]:[remote file path]/[file name].txt [local host file path]
    ```

    ```[local host file path]``` は、.txt ファイルのコピー先となるホスト PC 上の場所を指します。 ```[remote username]``` は、[セットアップ エクスペリエンス](./quickstart-percept-dk-set-up.md)中に選択された SSH ユーザー名です。

### <a name="log-types-and-commands"></a>ログの種類とコマンド

|ログの目的      |収集するタイミング         |command                     |
|-----------------|---------------------------|----------------------------|
|*サポート バンドル* - ほとんどのカスタマー サポート リクエストに必要なログのセットが用意されています。|サポートを要求するたびに収集します。|```sudo iotedge support-bundle --since 1h``` <br><br>*"--since 1h" を任意の期間に変更できます。例: "--6h" (6 時間)、"6d" (6 日間)、"6m" (6 分)*|
|*OOBE ログ* - セットアップ エクスペリエンスの詳細を記録します。|セットアップ エクスペリエンス中に問題が検出されたときに収集します。|```sudo journalctl -u oobe -b```|
|*edgeAgent logs* - デバイス上で実行されているすべてのモジュールのバージョン番号を記録します。|1 つ以上のモジュールが動作していない場合に収集します。|```sudo iotedge logs edgeAgent```|
|*モジュール コンテナー ログ* - 特定の IoT Edge モジュール コンテナーの詳細を記録します|モジュールに問題が検出されたときに収集します|```sudo iotedge logs [container name]```|
|*ネットワーク ログ* -Wi-Fi サービスとネットワーク スタックをカバーするログのセット。|Wi-Fi またはネットワークの問題が検出されたときに収集します。|```sudo journalctl -u hostapd.service -u wpa_supplicant.service -u ztpd.service -u systemd-networkd > network_log.txt```<br><br>```cat /etc/os-release && cat /etc/os-subrelease && cat /etc/adu-version && rpm -q ztpd > system_ver.txt```<br><br>両方のコマンドを実行します。 各コマンドによって複数のログが収集され、1 つの出力に格納されます。|

> [!WARNING]
> `support-bundle` コマンドからの出力には、ホスト、デバイス名とモジュール名、モジュールによってログに記録された情報などが含まれる場合があります。パブリック フォーラムで出力を共有する場合は、この点に注意してください。

## <a name="troubleshooting-commands"></a>コマンドのトラブルシューティング
開発キットで発生する可能性のある問題のトラブルシューティングに使用できる一連のコマンドを次に示します。 これらのコマンドを実行するには、まず [SSH 経由](./how-to-ssh-into-percept-dk.md)で開発キットに接続する必要があります。 

Azure IoT Edge コマンドの詳細については、[Azure IoT Edge デバイスのトラブルシューティングに関するドキュメント](../iot-edge/troubleshoot.md)を参照してください。 

|機能         |使用する場合                    |command                 |
|------------------|----------------------------|---------------------------|
|開発キットのソフトウェア バージョンを確認します。|開発キットにあるソフトウェアのバージョンを確認する必要があるときに、いつでも使用できます。|```cat /etc/os-release && cat /etc/os-subrelease && cat /etc/adu-version```|
|開発キットの温度を確認します|開発キットがオーバーヒートしていると思われる場合に使用します。|```cat /sys/class/thermal/thermal_zone0/temp```|
|開発キットのテレメトリ ID を確認します|開発キットの一意のテレメトリ ID を知る必要がある場合に使用します。|```sudo azure-device-health-id```|
|IoT Edge の状態を確認します|クラウドに接続している IoT Edge モジュールに問題がある場合に使用します。|```sudo iotedge check```|
|Azure IoT Edge セキュリティ デーモンを再起動します|IoT Edge が応答していないか、正常に動作していない場合に使用します。|```sudo systemctl restart iotedge``` |
|デプロイされている Azure IoT Edge モジュールを一覧表示します|開発キットにデプロイされているすべてのモジュールを確認する必要がある場合に使用します|```sudo iotedge list``` |
|指定されたファイル システムの使用可能/合計領域を表示します|開発キット上の使用可能なストレージを知る必要がある場合に使用します。|```df [option] [file]```|
|開発キットの IP およびインターフェイス情報を表示します|開発キットの IP アドレスを知る必要がある場合に使用します。|`ip route get 1.1.1.1`        | 
|開発キットの IP アドレスのみを表示します|開発キットの IP アドレスのみが必要で、他のインターフェイス情報は必要ない場合に使用します。|<code>ip route get 1.1.1.1 &#124; awk '{print $7}'</code> <br> `ifconfig [interface]` |

## <a name="usb-update-errors"></a>USB 更新エラー

|エラー:                                    |解決方法:                                               |
|------------------------------------------|--------------------------------------------------------|
|LIBUSB_ERROR_XXX during USB flash via UUU (UUU での USB フラッシュ中に LIBUSB_ERROR_XXX が発生しました) |このエラーは、UUU 更新中に USB 接続に失敗したことが原因で発生します。 PC 上の USB ポートまたはPercept DK キャリア ボードに USB ケーブルが適切に接続されていない場合、この形式のエラーが発生します。 USB ケーブルの両端を抜いてから再度つなぎ、ケーブルを軽く揺すって、しっかり接続されていることを確認します。|

## <a name="clearing-hard-drive-space-on-the-azure-percept-dk"></a>Azure Percept DK 上のハード ドライブ領域の消去
Azure Percept DK 上のハード ドライブ領域を占有するコンポーネントには、docker コンテナー ログと docker コンテナー自体の 2 つがあります。 コンテナー ログがすべてのハード領域を使い尽くしていないことを確認するために、Azure Percept DK にはログのローテーションが組み込まれています。これにより、新しいログが生成されると、古いログがローテーションされます。

docker コンテナーの数が原因でハード ドライブ領域の問題が発生する場合は、次の手順に従って未使用のコンテナーを削除できます。
1. [開発キットに SSH で接続](./how-to-ssh-into-percept-dk.md)します
1. コマンド `docker system prune` を実行します。

これにより、未使用のすべてのコンテナー、ネットワーク、イメージ、および必要に応じてボリュームが削除されます。 詳細については、[このページを参照してください](https://docs.docker.com/engine/reference/commandline/system_prune/)。

## <a name="azure-percept-dk-carrier-board-led-states"></a>Azure Percept DK キャリア ボードの LED の状態

キャリア ボードの筐体上部には、小さな LED が 3 つあります。 LED 1 の横にはクラウド アイコンが、LED 2 の横には Wi-Fi アイコンが、LED 3 の横には感嘆符アイコンが印字されています。 各 LED 状態については、次の表を参照してください。

|LED             |State      |説明                      |
|----------------|-----------|---------------------------------|
|LED 1 (IoT Hub) |オン (点灯) |デバイスは IoT ハブに接続されています。 |
|LED 2 (Wi-Fi)   |ゆっくり点滅 |Wi-Fi Easy Connect を使ってデバイスを構成する準備ができたことを構成担当者に知らせています。 |
|LED 2 (Wi-Fi)   |高速点滅 |認証に成功しました。デバイスの関連付けを行っています。 |
|LED 2 (Wi-Fi)   |オン (点灯) |認証と関連付けに成功しました。デバイスは Wi-Fi ネットワークに接続されています。 |
|LED 3           |NA         |LED が使用されていません。 |
