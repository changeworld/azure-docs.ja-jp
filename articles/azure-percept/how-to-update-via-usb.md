---
title: USB 接続で Azure Percept DK を更新する
description: USB 接続で Azure Percept DK を更新する方法について説明します
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/18/2021
ms.custom: template-how-to
ms.openlocfilehash: cd6e4e62123b4d4b927cf385aaf64a066eecc1e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104887752"
---
# <a name="how-to-update-azure-percept-dk-over-a-usb-connection"></a>USB 接続で Azure Percept DK を更新する方法

無線 (OTA) の更新プログラムは、開発キットのオペレーティング システムとファームウェアを最新の状態に保つための最適な方法ですが、USB 接続を使用して必要に応じて開発キットを更新 (または "フラッシュ") するシナリオもあります。

- 接続またはその他の技術的な問題のため、OTA 更新を実行できない
- デバイスを工場出荷時の状態にリセットする必要がある

このガイドでは、USB 接続を使用して開発キットのオペレーティング システムとファームウェアを正常に更新する方法について説明します。

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

1. [NXP UUU ツール](https://github.com/NXPmicro/mfgtools/releases)。 **[アセット]** タブで、uuu.exe ファイル (Windows の場合) または uuu ファイル (Linux の場合) の **最新リリース** をダウンロードします。

1. [7-Zip](https://www.7-zip.org/)。 このソフトウェアは、XZ 圧縮ファイルから生のイメージ ファイルを抽出するために使用されます。 適切な .exe ファイルをダウンロードし、インストールします。

1. [更新ファイルをダウンロードします](https://go.microsoft.com/fwlink/?linkid=2155734)。

1. 次の 3 つのすべてのビルド アーティファクトが存在することを確認します。
    - Azure-Percept-DK- *&lt;バージョン番号&gt;* .raw.xz
    - fast-hab-fw.raw
    - emmc_full.txt

## <a name="set-up-your-environment"></a>環境を設定する

1. コマンドラインを使用して簡単にアクセスできる場所にホスト コンピューター上のフォルダーまたはディレクトリを作成します。

1. UUU ツール (**uuu.exe** または **uuu**) を新しいフォルダーにコピーします。

1. **Azure-Percept-DK- *&lt;バージョン番号&gt;* .raw.xz** を右クリックし、 **[7-Zip]** &gt; **[ここに展開]** を選択して、圧縮ファイルから **Azure-Percept-DK- *&lt;バージョン番号&gt;* .raw** ファイルを抽出します。

1. 抽出された **Azure-Percept-DK- *&lt;バージョン番号&gt;* .raw** ファイル、**fast-hab-fw.raw**、および **emmc_full.txt** を、UUU ツールが格納されているフォルダーに移動します。

## <a name="update-your-device"></a>デバイスを更新する

1. [開発キットに SSH で接続します](./how-to-ssh-into-percept-dk.md)。

1. 次に、Windows コマンド プロンプト ( **[スタート]**  >  **「cmd」** ) または Linux ターミナルを開き、更新ファイルと UUU ツールが格納されているフォルダーに移動します。 コマンド プロンプトまたはターミナルで次のコマンドを入力して、フラッシュ可能なデバイスを受信するようにコンピューターを準備します。

    - Windows:

        ```console
        uuu -b emmc_full.txt fast-hab-fw.raw Azure-Percept-DK-<version number>.raw 
        ```

    - Linux:

        ```bash
        sudo ./uuu -b emmc_full.txt fast-hab-fw.raw Azure-Percept-DK-<version number>.raw
        ```

1. Azure Percept Vision デバイスを、キャリア ボードの USB-C ポートから切断します。

1. 指定された USB-C ケーブルを、キャリア ボードの USB-C ポートとホスト コンピューターの USB-C ポートに接続します。 コンピューターに USB-A ポートしかない場合は、USB-C to USB-A ケーブル (別売り) に接続して、キャリア ボードとホスト コンピューターに接続します。

1. SSH クライアントのプロンプトで、次のコマンドを入力します。

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

1. 更新が完了したら、キャリア ボードの電源をオフにします。 PC から USB ケーブルを取り外します。  

## <a name="next-steps"></a>次のステップ

[Azure Percept DK セットアップ エクスペリエンス](./quickstart-percept-dk-set-up.md)を使用して、デバイスを再構成します。