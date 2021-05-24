---
title: USB-C ケーブル接続で Azure Percept DK を更新する
description: USB-C ケーブル接続で Azure Percept DK を更新する方法について説明します
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/18/2021
ms.custom: template-how-to
ms.openlocfilehash: 39889455cd41883f5782f9fb140b400d6ed09ed7
ms.sourcegitcommit: 32ee8da1440a2d81c49ff25c5922f786e85109b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2021
ms.locfileid: "109786737"
---
# <a name="how-to-update-azure-percept-dk-over-a-usb-c-cable-connection"></a>USB-C ケーブル接続で Azure Percept DK を更新する方法

このガイドでは、USB 接続を使用して開発キットのオペレーティング システムとファームウェアを正常に更新する方法について説明します。 この手順で行う操作の概要を次に示します。
1. 更新プログラム パッケージをホスト コンピューターにダウンロードする
1. 更新プログラム パッケージを開発キットに転送するコマンドを実行する
1. ホスト コンピューターによって検出され、更新プログラム パッケージを受信できるよう、開発キットを「USB モード」 (SSH を使用) に設定する
1. USB-C ケーブルを使用して、開発キットをホスト コンピューターに接続する
1. 消去が完了するまで待つ

> [!WARNING]
> USB を使用して開発キットを更新すると、AI モデルとコンテナーを含む、デバイス上の既存のデータがすべて削除されます。
>
> すべての手順を順番に実行します。 手順をスキップすると、開発キットが使用できない状態になる可能性があります。


## <a name="prerequisites"></a>前提条件

- Azure Percept DK
- Wi-Fi 機能が搭載された Windows、Linux、または OS X ベースのホスト コンピューターと、使用可能な USB-C または USB-A ポート
- USB-C to USB-A ケーブル (オプション、別売り)
- [Azure Percept DK セットアップ エクスペリエンス](./quickstart-percept-dk-set-up.md)中に作成された SSH ログイン

## <a name="download-software-tools-and-update-files"></a>ソフトウェア ツールのダウンロードとファイルの更新

1. [NXP UUU ツール](https://github.com/NXPmicro/mfgtools/releases)。 **[資産]** タブ下の **最新リリース** uuu.exe ファイル (Windows の場合) または uuu ファイル (Linux の場合) をダウンロードします。UUU は、NXP によって作成されたツールで NXP 開発ボードの更新に使用されます。

1. [更新ファイルをダウンロードします](https://go.microsoft.com/fwlink/?linkid=2155734)。 これらはすべて zip ファイルに含まれており、次のセクションで抽出します。

1. 次の 3 つのすべてのビルド アーティファクトが存在することを確認します。
    - Azure-Percept-DK- *&lt;バージョン番号&gt;* .raw
    - fast-hab-fw.raw
    - emmc_full.txt

## <a name="set-up-your-environment"></a>環境を設定する

1. コマンドラインを使用して簡単にアクセスできる場所にホスト コンピューター上のフォルダーまたはディレクトリを作成します。

1. UUU ツール (**uuu.exe** または **uuu**) を新しいフォルダーにコピーします。

1. UUU ツールが格納されている新しいフォルダーに、以前にダウンロードした更新プログラム ファイルを抽出します。

## <a name="update-your-device"></a>デバイスを更新する

この手順では、開発キットの単一の USB-C ポートを使用して更新します。  コンピューターに USB-C ポートがある場合は、Azure Percept Vision デバイスを切断し、そのケーブルを使用できます。  コンピューターに USB-A ポートがあるだけの場合は、開発キットの USB-C ポートから Azure Percept Vision デバイスを切断し、USB-C to USB-A ケーブル (別売り) を開発キットとホスト コンピュータに接続します。

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

1. コマンド プロンプト ウィンドウに、「**既知の USB デバイスが表示されるのを待機しています...** 」 というメッセージが表示されます。UUU ツールは、ホスト コンピューターによって開発キットが検出されるのを待機しています。 これで、次の手順に進めます。

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

1. 他のコマンド プロンプトまたはターミナルに戻ります。 更新が完了すると、```Success 1    Failure 0``` というメッセージが表示されます。

    > [!NOTE]
    > 更新後は、デバイスが工場出荷時の設定にリセットされ、Wi-Fi 接続と SSH ログインは失われます。

1. 更新が完了したら、開発キットの電源をオフにします。 PC から USB ケーブルを取り外します。  

## <a name="next-steps"></a>次のステップ

[Azure Percept DK セットアップ エクスペリエンス](./quickstart-percept-dk-set-up.md)を使用して、デバイスを再構成します。
