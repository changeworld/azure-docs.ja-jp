---
title: USB 接続で Azure Percept DK を更新する
description: USB 接続で Azure Percept DK を更新する方法について説明します
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: 7f5e5e4da9fea671fc85a55fc8cc191fa14b720f
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2021
ms.locfileid: "101660571"
---
# <a name="how-to-update-azure-percept-dk-over-a-usb-connection"></a>USB 接続で Azure Percept DK を更新する方法

Azure Percept DK のキャリア ボードに対して USB 更新を実行する方法を学ぶには、このガイドに従ってください。

## <a name="prerequisites"></a>前提条件
- 使用可能な USB-C ポートまたは USB-A ポートがあるホスト コンピューター。
- Azure Percept DK (開発キット) キャリア ボードと、付属の USB-C to USB-C ケーブル。 ホスト コンピューターに USB-A ポートはあるものの、USB-C ポートがない場合は、USB-C to USB-A ケーブル (別売り) を使用できます。
- [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) をインストールします (管理者のアクセス権が必要です)。
- NXP UUU ツールをインストールします。 [アセット] タブで、uuu.exe ファイル (Windows の場合) または uuu ファイル (Linux の場合) の[最新リリースをダウンロード](https://github.com/NXPmicro/mfgtools/releases)します。
- [7-Zip をインストール](https://www.7-zip.org/)します。 このソフトウェアは、XZ 圧縮ファイルから生のイメージ ファイルを抽出するために使用されます。 適切な .exe ファイルをダウンロードし、インストールします。

## <a name="steps"></a>手順
1.  次の [3 つの USB 更新ファイル](https://go.microsoft.com/fwlink/?linkid=2155734)をダウンロードします。
    - pe101-uefi-***&lt;バージョン番号&gt;***.raw.xz
    - emmc_full.txt
    - fast-hab-fw.raw
 
1. pe101-uefi-* **&lt;バージョン番号&gt;** _.raw を、圧縮された pe101-uefi_* _&lt;バージョン番号&gt;_ **.raw.xz ファイルから抽出します。 抽出方法がわからない場合 7-Zip をダウンロードしてインストールし、 **.xz** イメージ ファイルを右クリックして、[7-Zip] &gt; [Extract Here]\(ここに抽出\) を選択します。

1. UUU ツールが格納されているフォルダーに、次の 3 つのファイルをコピーします。
    - 抽出された pe101-uefi-***&lt;バージョン番号&gt;***.raw ファイル (手順 2. より)。
    - emmc_full.txt (手順 1. より)。
    - fast-hab-fw.raw (手順 1. より)。
 
1. 開発キットの電源を入れます。
1. [SSH 経由で開発キットに接続](./how-to-ssh-into-percept-dk.md)します
1. Windows コマンド プロンプト ([スタート] &gt; 「cmd」) または Linux ターミナルを開き、更新ファイルが格納されているフォルダーに移動します。 次のコマンドを実行し、更新を開始します。
    - Windows: ```uuu -b emmc_full.txt fast-hab-fw.raw pe101-uefi-<version number>.raw```
    - Linux: ```sudo ./uuu -b emmc_full.txt fast-hab-fw.raw pe101-uefi-<version number>.raw```
    
これらのコマンドの実行後、コマンド プロンプトに "デバイスを待機しています..." というメッセージが表示されることがあります。 これは想定されていることなので、次の手順に進んでください。
    
1. USB ケーブルを使用して、開発キットのキャリア ボードをホスト コンピューターに接続します。 常に、キャリア ボードの USB-C ポートから、ホスト コンピューターの使用可能なポートに応じて USB-C ポートまたは USB-A ポートに接続するようにしてください (USB-C to USB-A ケーブルは別売り)。 
 
1. SSH または PuTTY ターミナルで、次のコマンドを入力して開発キットを USB モードに設定し、開発キットを再起動します。
    - ```flagutil    -wBfRequestUsbFlash    -v1```
    - ```reboot -f```
 
1. ホスト コンピューターがデバイスを認識し、更新プロセスが自動的に開始されることが示される場合があります。 状態を確認するためにコマンド プロンプトに戻ります。 このプロセスには最大で 10 分かかります。更新が成功すると、"Success 1 Failure 0 (成功 1 失敗 0)" というメッセージが表示されます
 
1. 更新が完了したら、キャリア ボードの電源をオフにします。 PC から USB ケーブルを取り外します。  USB ケーブルを使用して、Azure Percept Vision のモジュールをキャリア ボードに接続し直します。

1. キャリア ボードの電源を再度オンにします。

## <a name="next-steps"></a>次のステップ

これで、開発キットが正常に更新されました。 開発キットを使用して開発と操作を続けることができます。