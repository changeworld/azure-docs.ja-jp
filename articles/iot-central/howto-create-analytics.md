---
title: Azure IoT Central アプリケーションのカスタム分析を作成する | Microsoft Docs
description: オペレーターとして、Azure IoT Central アプリケーションのカスタム分析を作成する方法。
services: iot-central
author: tanmaybhagwat
ms.author: tanmayb
ms.date: 04/16/2018
ms.topic: article
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: 2f19998429daab9000e35b520673c417b4bc828a
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/16/2018
ms.locfileid: "34199937"
---
# <a name="how-to-use-analytics-to-analyze-your-device-data"></a>分析を使用してデバイス データを分析する方法

Microsoft Azure IoT Central には、大量のデバイス データを理解するための豊富な分析機能が用意されています。 分析を使用すると、アプリケーションで[デバイス セット](howto-use-device-sets.md)のデータを表示したり、分析したりできます。 デバイス セットは、ユーザーが定義したデバイスのグループです。 分析を少数のデバイスまたは 1 つのデバイスに絞り込むことができます。

オペレーターとして、左側のナビゲーション メニューで **[分析]** を選択し、デバイス セットを選択してから、測定を選択してグラフを表示します。 データをさらに詳細にスライスするために使用できるいくつかのツールを次に示します。

* **[測定]:** 温度や湿度などの、表示する測定を選択します。
* **[集計]:** 測定のための集計関数を選択します。 たとえば、**Minimum** または **Average**。
* **[Split-by] (分割基準):** デバイスのプロパティまたはデバイス名でデータを分割してドリル ダウンします。 たとえば、デバイスの場所で分割します。

     ![分析のメイン ページ](media\howto-create-analytics\analytics-main.png)

* **[Time-range] (時間範囲):** 事前に定義されたいずれかの時間範囲から時間範囲を選択するか、またはカスタムの時間範囲を作成できます。  ![分析の時間範囲](media\howto-create-analytics\analytics-time-range.png)

## <a name="change-the-visualizations"></a>視覚化を変更する

次の 3 つのモードのいずれかを選択することによって、視覚化の要件を満たすようにグラフを変更できます。

* **[Stacked] (積み上げ):** すべての測定のグラフが積み上げられ、各グラフに独自の Y 軸があります。 積み上げグラフは、複数の測定が選択されており、これらの測定の個別のビューを表示したい場合に役立ちます。
* **[Unstacked] (非積み上げ):** すべての測定のグラフが 1 つの Y 軸に対してプロットされますが、Y 軸の値は強調表示された測定に基づいて変更されます。 非積み上げグラフは、複数の測定を重ね合わせ、同じ時間範囲のこれらの測定にまたがるパターンを表示したい場合に役立ちます。
* **[Shared Y-axis] (共有 Y 軸):** すべてのグラフが同じ Y 軸を共有し、この軸の値は変更されません。 共有 Y 軸グラフは、分割基準を使用してデータをスライスしながら、1 つの測定を表示したい場合に役立ちます。

## <a name="next-steps"></a>次の手順

ここでは、Azure IoT Central アプリケーションのカスタム分析を作成する方法について説明しました。推奨される次の手順は以下のとおりです。

> [!div class="nextstepaction"]
> [Node.js アプリケーションを準備して接続する](howto-connect-nodejs.md)