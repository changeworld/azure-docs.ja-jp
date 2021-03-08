---
title: Azure Percept Audio の使用を開始する
description: Azure Percept Audio デバイスを Azure Percept DK に接続する方法について説明します。
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: quickstart
ms.date: 02/18/2021
ms.custom: template-quickstart
ms.openlocfilehash: 575107859f56df742ab41a299269c250511022b3
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2021
ms.locfileid: "101664301"
---
# <a name="azure-percept-audio-setup"></a>Azure Percept Audio の設定

Azure Percept Audio は、Azure Percept DK ですぐに使用できます。 特別な設定は必要ありません。

## <a name="prerequisites"></a>前提条件

- Azure Percept DK (開発キット)
- Azure Percept Audio
- [Azure サブスクリプション](https://azure.microsoft.com/free/)
- [Azure Percept DK セットアップ エクスペリエンス](./quickstart-percept-dk-set-up.md): 開発キットを Wi-Fi ネットワークに接続し、IoT ハブを作成して、開発キットを IoT ハブに接続済みであること

## <a name="connecting-your-devices"></a>デバイスを接続する

1. 同梱されている USB Micro Type-B to USB Type-A ケーブルを使用して、Azure Percept Audio デバイスを Azure Percept DK キャリア ボードに接続します。 ケーブルの Micro Type-B 側を Audio SoM に、Type-A 側を Percept DK キャリア ボードに接続します。

1. 開発キットの電源をオンにします。

    - Audio SoM の LED L01 が緑色 (点灯) に変わり、デバイスの電源が投入されたことがわかります。
    - LED L02 が緑色 (点滅) に変わり、Audio SoM が認証中であることがわかります。

1. 認証プロセスが完了するまで待機します。これには最大 3 分かかることがあります。

1. 次のいずれかが表示されたら、プロトタイプ作成を開始できます。

    - LED L01 がオフになり、L02 が白色に変わった。 認証は完了しましたが、キーワードを使用した開発キットの構成はまだ済んでいません。
    - 3 つの LED がすべて青色に変わった。 認証が完了し、キーワードを使用した開発キットの構成も済んでいます。

    > [!NOTE]
    > 開発キットが認証されない場合は、サポートにお問い合わせください。

## <a name="next-steps"></a>次のステップ

[コーディングなしの音声ソリューション](./tutorial-no-code-speech.md)を作成します。