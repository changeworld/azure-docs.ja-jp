---
title: Azure Percept Audio デバイスを設定する
description: Azure Percept Audio デバイスを Azure Percept DK に接続する方法について説明します。
author: MrHamlet
ms.author: amiyouss
ms.service: azure-percept
ms.topic: quickstart
ms.date: 03/25/2021
ms.openlocfilehash: 8b6ec3ba8473be8e924d3c4b8ae17ccbdcf6568f
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2021
ms.locfileid: "123221837"
---
# <a name="set-up-the-azure-percept-audio-device"></a>Azure Percept Audio デバイスを設定する

Azure Percept Audio は、Azure Percept DK ですぐに使用できます。 特別な設定は必要ありません。

## <a name="prerequisites"></a>前提条件

- Azure Percept DK (開発キット)
- Azure Percept Audio
- [Azure サブスクリプション](https://azure.microsoft.com/free/)
- [Azure Percept DK セットアップ エクスペリエンス](./quickstart-percept-dk-set-up.md): 開発キットを Wi-Fi ネットワークに接続し、IoT ハブを作成して、開発キットを IoT ハブに接続済みであること
- 3\.5 mm オーディオ ジャックに接続できるスピーカーまたはヘッドホン (オプション)

## <a name="connecting-your-devices"></a>デバイスを接続する

1. 同梱されている Micro USB to USB Type-A ケーブルを使用して、Azure Percept Audio デバイスを Azure Percept DK キャリア ボードに接続します。 ケーブルの Micro USB 側をオーディオ インターポーザー (開発者) ボードに、Type-A 側を Percept DK キャリア ボードに接続します。

1. (省略可能) "Line Out" というラベルの付いたオーディオ ジャックを介して、スピーカーまたはヘッドホンを Azure Percept Audio デバイスに接続します。 これによって音声応答を聴くことができます。

1. 電源アダプターに接続し、開発キットをオンにします。 LED L02 が白の点滅に変わり、デバイスの電源が入っており、認証が行われていることを示します。

1. 認証プロセスが完了するまで待機します。これには最大 5 分かかることがあります。

1. 次の LED 状態のいずれかが表示されたら、プロトタイプ作成を開始できます。

    - LED L02 が白の点灯に変わると、認証が完了し、開発キットがキーワードなしで構成されたことを示します。
    - 3 つの LED がすべて青に変わると、認証が完了し、開発キットがキーワードありで構成されたことを示します。

## <a name="next-steps"></a>次のステップ

[Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819) で[コードなしのスピーチ ソリューション](./tutorial-no-code-speech.md)を作成する。
