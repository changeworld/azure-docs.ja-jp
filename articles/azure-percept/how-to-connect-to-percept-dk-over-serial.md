---
title: Azure Percept DK にシリアルで接続する
description: USB to TTL シリアル ケーブルを使用して、Azure Percept DK へのシリアル接続を設定する方法
author: MrHamlet
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/03/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 47c3c4d5a696087164ef4992ae75e5433de86722
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131006318"
---
# <a name="connect-to-azure-percept-dk-over-serial"></a>Azure Percept DK にシリアルで接続する

[PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) から Azure Percept DK へのシリアル接続を設定するには、次の手順に従います。

> [!WARNING]
> **プライベート プレビュー開発キットをお持ちの場合**、極端な障害 (デバイスが使用不能になるなど) の場合を除き、シリアル経由で開発キットを接続しようと **しないで** ください。 シリアル経由で接続するには、プライベート プレビュー開発キットを逆アセンブルして、GPIO ピンにアクセスする必要があります。 キャリア ボードの格納装置を取り外すことは非常に困難であり、Wi-Fi アンテナ ケーブルを破損する可能性があります。

## <a name="prerequisites"></a>前提条件

- [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)
- ホスト PC
- Azure Percept DK
- [USB to TTL シリアル ケーブル](https://www.adafruit.com/product/954)

    :::image type="content" source="./media/how-to-connect-to-percept-dk-over-serial/usb-serial-cable.png" alt-text="USB to TTL シリアル ケーブル。":::

## <a name="start-the-serial-connection"></a>シリアル接続を開始する

1. 次に示すように、[USB to TTL シリアル ケーブル](https://www.adafruit.com/product/954)をマザーボードの 3 つの GPIO ピンに接続します。

    :::image type="content" source="./media/how-to-connect-to-percept-dk-over-serial/apdk-serial-pins.jpg" alt-text="キャリア ボードのシリアル ピンの接続。":::

1. 開発キットの電源を入れ、シリアル ケーブルの USB 側を PC に接続します。

1. Windows で、 **[スタート]**  ->  **[Windows Update の設定]**  ->  **[View optional updates]\(オプションの更新プログラムの表示\)**  ->  **[ドライバー更新プログラム]** の順に移動します。 一覧でシリアルから USB への更新プログラムを探し、その横にあるチェック ボックスをオンにして、 **[ダウンロードしてインストール]** を選択します。  

1. 次に、Windows デバイス マネージャーを開きます ( **[スタート]**  ->  **[デバイス マネージャー]** )。 **[ポート]** に移動し、 **[USB to UART]\(USB から UART へ\)** を選択して、 **[プロパティ]** を開きます。 デバイスが接続されている COM ポートを書き留めておきます。

1. **[ポートの設定]** タブを選択します。 **[bps]** が 115200 に設定されていることを確認します。

1. PuTTY を開きます。 次のように入力し、 **[開く]** を選択して、シリアルで開発キットに接続します。

    1. シリアル回線: COM [ポート番号]
    1. 速度: 115200
    1. 接続の種類: シリアル

    :::image type="content" source="./media/how-to-connect-to-percept-dk-over-serial/putty-serial-session.png" alt-text="シリアル パラメーターが選択された PuTTY セッション ウィンドウ。":::
