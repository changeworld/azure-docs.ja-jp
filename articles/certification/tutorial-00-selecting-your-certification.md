---
title: Azure Certified Device プログラム - チュートリアル - 認定プログラムを選択する
description: デバイスに適した認定プログラムを選択するためのステップ バイ ステップ ガイド
author: Chuckb1300
ms.author: cbroad
ms.service: certification
ms.topic: tutorial
ms.date: 03/19/2021
ms.custom: template-tutorial
ms.openlocfilehash: b6ab8a4f971a065764731abddf72e26c628ba6cb
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105975556"
---
# <a name="tutorial-select-your-certification-program"></a>チュートリアル: 認定プログラムを選択する

Azure Certified Device プログラムに興味を持っていただきありがとうございます。 認定デバイスのエコシステムに皆様をご案内できることを嬉しく思います。 まず、貴社のデバイスの機能に最適な認定プログラムを把握しておく必要があります。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * デバイスに最も適した認定プログラムを選択する

## <a name="selecting-a-certification-program-for-your-device"></a>デバイスの認定プログラムを選択する

すべてのデバイスは、[**Azure Certified Device**](./program-requirements-azure-certified-device.md) 認定の骨子となっているベースライン要件を満たす必要があります。 他の 3 つの認定バッジは、このプログラムをベースとして、それぞれ異なる顧客価値を提供します。 増分となる 3 つのバッジ (IoT Plug and Play、Edge Managed、Edge Secured Core *プレビュー) の中から、1 つまたは複数を選択することができます。

1. 以下の表で、認定プログラムの各要件を確認します。 プログラムごとの詳しい要件は、ヘッダーを選択してご覧いただけます。

    |要件|[IoT プラグ アンド プレイ](./program-requirements-edge-secured-core.md)|[Edge Managed](./program-requirements-edge-managed.md)|[Edge Secured-Core](./program-requirements-edge-secured-core.md)|
    ---|---|---|---
    | プロセッサ | Any|MPU または CPU|MPU または CPU|
    | OS | Any|[レベル 1 OS](../iot-edge/support.md?view=iotedge-2018-06&preserve-view=true))|[レベル 1 OS](../iot-edge/support.md?view=iotedge-2018-06&preserve-view=true)|
    | IoT Edge ランタイム | サポートされていません |必須|必須|
    | Defender for IoT | サポートされていません|必須|必須|
    | ADU または Windows Update | サポートされていません|必須|必須|

1. デバイスの要件を把握したら、プログラムの技術的な要件を確認します。 たとえば、Azure Certified Device 認定のほか、そのベースライン認定に、3 つの増分バッジのいずれかを組み合わせたものが考えられます。 

## <a name="next-steps"></a>次のステップ

デバイスの認定を開始する準備はこれで完了です。 次の記事に進んで、プロジェクトを開始しましょう。
> [!div class="nextstepaction"]
>[チュートリアル: プロジェクトを作成する](tutorial-01-creating-your-project.md)
