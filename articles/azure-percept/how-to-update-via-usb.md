---
title: Azure Percept DK を USB-C 接続で更新する
description: USB-C ケーブル接続で Azure Percept DK を更新する方法について説明します
author: EthanChangAED
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/18/2021
ms.custom: template-how-to
ms.openlocfilehash: 293249c764155aefe16ef23738b1e4efea4255e5
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124751063"
---
# <a name="update-azure-percept-dk-over-a-usb-c-connection"></a>Azure Percept DK を USB-C 接続で更新する

このガイドでは、USB 接続を使用して開発キットのオペレーティング システムとファームウェアを正常に更新する方法について説明します。 この手順で行う操作の概要を次に示します。

1. 更新プログラム パッケージをホスト コンピューターにダウンロードする
1. 更新プログラム パッケージを開発キットに転送するコマンドを実行する
1. SSH または DIP スイッチを使用して開発キットを USB モードに設定する
1. USB-C ケーブルを使用して、開発キットをホスト コンピューターに接続する
1. 消去が完了するまで待つ

> [!WARNING]
> USB を使用して開発キットを更新すると、AI モデルとコンテナーを含む、デバイス上の既存のデータがすべて削除されます。
>
> すべての手順を順番に実行します。 手順をスキップすると、開発キットが使用できない状態になる可能性があります。

## <a name="prerequisites"></a>前提条件

- Azure Percept DK
- Wi-Fi 機能が搭載された Windows または Linux ベースのホスト コンピューターと、使用可能な USB-C または USB-A ポート
- USB-C to USB-A ケーブル (オプション、別売り)
- [Azure Percept DK セットアップ エクスペリエンス](./quickstart-percept-dk-set-up.md)中に作成された SSH ログイン アカウント
- 開発キットに同梱されている、開発キット背面のねじを外すための六角レンチ (DIP スイッチ方式を使用している場合)

> [!NOTE]
> **Mac ユーザー** - Mac をホスト コンピューターとして使用する場合、USB 接続での Azure Percept DK の更新は機能しません。 

## <a name="download-software-tools-and-update-files"></a>ソフトウェア ツールのダウンロードとファイルの更新

1. [NXP UUU ツール](https://github.com/NXPmicro/mfgtools/releases)。 **[資産]** タブ下の **最新リリース** uuu.exe ファイル (Windows の場合) または uuu ファイル (Linux の場合) をダウンロードします。UUU は、NXP によって作成されたツールで NXP 開発ボードの更新に使用されます。

1. [更新ファイルをダウンロードします](./how-to-select-update-package.md)。 これらはすべて zip ファイルに含まれており、次のセクションで抽出します。

1. 次の 3 つのすべてのビルド アーティファクトが存在することを確認します。
    - Azure-Percept-DK- *&lt;バージョン番号&gt;* .raw
    - fast-hab-fw.raw
    - emmc_full.txt

## <a name="set-up-your-environment"></a>環境を設定する

1. コマンドラインを使用して簡単にアクセスできる場所にホスト コンピューター上のフォルダーまたはディレクトリを作成します。

1. UUU ツール (**uuu.exe** または **uuu**) を新しいフォルダーにコピーします。

1. UUU ツールが格納されている新しいフォルダーに、以前にダウンロードした更新プログラム ファイルを抽出します。

## <a name="run-the-command-that-transfers-the-update-package-to-the-dev-kit"></a>更新プログラム パッケージを開発キットに転送するコマンドを実行する

1. 次に、Windows コマンド プロンプト ([スタート] - [cmd]) または Linux ターミナルを開き、**更新ファイルと UUU ツールが格納されているフォルダーに移動します**。 

1. コマンド プロンプトまたはターミナルで下記のコマンドを入力します。

    - Windows:

        ```console
        uuu -b emmc_full.txt fast-hab-fw.raw Azure-Percept-DK-<version number>.raw 
        ```

    - Linux:

        ```bash
        sudo ./uuu -b emmc_full.txt fast-hab-fw.raw Azure-Percept-DK-<version number>.raw
        ```

1. コマンド プロンプト ウィンドウに、「**既知の USB デバイスが表示されるのを待機しています...** 」というメッセージが表示されます。UUU ツールは、ホスト コンピューターによって開発キットが検出されるのを待機しています。 **次の手順に進み、開発キットを USB モードにします。**

## <a name="set-the-dev-kit-into-usb-mode"></a>開発キットを USB モードに設定する
デバイス キットを "USB モード" に設定するには、SSH を使用する方法と、デバイス キットの DIP スイッチを変更する方法があります。 状況に応じて最適な方法を選択してください。

### <a name="using-ssh"></a>SSH の使用
デバイス キットを USB モードにするには、SSH が最も安全で推奨される方法です。 ただし、デバイス キットの Wi-Fi アクセス ポイントに接続できる必要があります。 開発キットの Wi-Fi アクセス ポイントに接続できない場合は、DIP スイッチ方式を使用する必要があります。

1. 指定された USB-C ケーブルを、開発キットの USB-C ポートとホスト コンピューターの USB-C ポートに接続します。 コンピューターに USB-A ポートしかない場合は、USB-C to USB-A ケーブル (別売り) をキャリア ボードとホスト コンピューターに接続します。

1. SSH 経由で開発キットに接続します。 SSH に関するヘルプが必要な場合は、 [次の手順に従います](./how-to-ssh-into-percept-dk.md)。

1. SSH ターミナルで、次のコマンドを入力します。

    1. デバイスを USB 更新モードに設定します。

        ```bash
        sudo flagutil    -wBfRequestUsbFlash    -v1
        ```

    1. デバイスを再起動します。 更新プログラムのインストールが開始されます。

        ```bash
        sudo reboot -f
        ```

### <a name="using-the-dip-switch-method"></a>DIP スイッチ方式の使用
デバイスに SSH 接続できない場合は、DIP スイッチ方式を使用します。

1. 開発ボードが電源ケーブルに接続されている場合は、プラグを抜きます。
1. 開発キットに同梱されている六角レンチを使用して、開発ボードの背面にある 4 本のねじを外します。

    :::image type="content" source="media/how-to-usb-update/dip-switch-01.jpg" alt-text="開発ボードの背面にある 4 本のねじを外します":::

1. 開発ボードを LED の方向にゆっくりとスライドさせます。 ヒート シンクは開発ボードの上部に取り付けたままにします。 ケーブルが外れないように、2 cm から 3 cm だけスライドさせます。

    :::image type="content" source="media/how-to-usb-update/dip-switch-02.jpg" alt-text="数センチメートル、ボードをスライドさせます":::

1. DIP スイッチはボードの隅にあります。 4 つのスイッチがあり、それぞれ上 (1) または下 (0) の 2 つの位置があります。 スイッチの既定の位置は、上 - 下 - 下 - 上 (1001) です。 ペーパー クリップなどの先の細い道具を使用して、スイッチの位置を下 - 上 - 下 - 上 (0101) に変更します。

    :::image type="content" source="media/how-to-usb-update/dip-switch-03.jpg" alt-text="ボードの下隅にあるスイッチを見つけます":::

1. これで開発キットは USB モードになり、次の手順に進むことができます。 **更新が完了したら、DIP スイッチを既定の上 - 下 - 下 - 上 (1001) の位置に戻します。** 次に、開発ボードをスライドさせて元の位置に戻し、背面の 4 本のねじを再度取り付けます。

## <a name="connect-the-dev-kit-to-the-host-computer-via-a-usb-c-cable"></a>USB-C ケーブルを使用して、開発キットをホスト コンピューターに接続する
この手順では、開発キットの単一の USB-C ポートを使用して更新します。  お使いのコンピューターに USB-C ポートがある場合は、開発キットに付属の USB-C - USB-C ケーブルを使用することができます。  お使いのコンピューターに USB-A ポートしかない場合は、USB-C - USB-A ケーブル (別売り) を使用する必要があります。

1. 適切な USB-C ケーブルを使用して、開発キットをホスト コンピューターに接続してください。
1. これで、ホスト コンピューターによって開発キットが USB デバイスとして検出されるはずです。 更新プログラム パッケージを開発キットに転送するコマンドを正常に実行し、コマンド プロンプトに "Waiting for Known USB Device to Appear... (既知の USB デバイスが表示されるまで待機しています...)" と表示されると、約 10 秒後に自動的に更新プログラムが開始されます。

## <a name="wait-for-the-update-to-complete"></a>消去が完了するまで待つ

1. 他のコマンド プロンプトまたはターミナルに戻ります。 更新が完了すると、```Success 1    Failure 0``` というメッセージが表示されます。

    > [!NOTE]
    > 更新後は、デバイスが工場出荷時の設定にリセットされ、Wi-Fi 接続と SSH ログインは失われます。

1. 更新が完了したら、開発キットの電源をオフにします。 PC から USB ケーブルを取り外します。
1. DIP スイッチ方式を使用して開発キットを USB モードにした場合は、必ず DIP スイッチを既定の位置に戻してください。 次に、開発ボードをスライドさせて元の位置に戻し、背面の 4 本のねじを再度取り付けます。   

## <a name="next-steps"></a>次のステップ

[Azure Percept DK セットアップ エクスペリエンス](./quickstart-percept-dk-set-up.md)を使用して、デバイスを再構成します。