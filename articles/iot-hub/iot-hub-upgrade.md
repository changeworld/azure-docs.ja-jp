---
title: Azure IoT Hub をアップグレードする | Microsoft Docs
description: IoT Hub の価格とスケール レベルを変更して、メッセージング機能とデバイス管理機能を強化します。
author: kgremban
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/02/2018
ms.author: kgremban
ms.openlocfilehash: e1342ed574d84ed5b4edd5060c2d6d3ec8bca1a8
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/09/2018
ms.locfileid: "40003113"
---
# <a name="how-to-upgrade-your-iot-hub"></a>IoT Hub のアップグレード方法

IoT ソリューションの成長と共に、Azure IoT Hub をスケールアップできます。 Azure IoT Hub は、使用する機能が異なる顧客に対応するために、基本 (B) と標準 (S) という 2 つのレベルで提供されています。 各レベルには 3 つのサイズ (1、2、および 3) があり、サイズによって 1 日に送信できるメッセージの数が決まります。 

デバイスの数が増え、さらに機能が必要になったら、次の 3 つの方法で、ニーズに合うように IoT Hub を調整できます。

* IoT Hub 内のユニットを追加する。 たとえば、B1 レベルの IoT Hub に 1 ユニットを追加すると、1 日あたり 400,000 のメッセージが追加されます。 
* IoT Hub のサイズを変更する。 たとえば、B2 レベルから B1 レベルに移行すると、各ユニットがサポートできる 1 日あたりのメッセージの量が増加します。
* 上位レベルにアップグレードする。 たとえば、B1 レベルから S1 レベルへのアップグレードでは、メッセージング機能は同じですが、標準レベルの高度な機能を使用できます。

これらの変更は、すべてが既存の操作を中断せずに実行できます。

IoT Hub をダウングレードする場合は、ユニットを削除し、IoT hub のサイズを縮小できます。 ただし、下位レベルにダウングレードすることはできません。 たとえば、S2 レベルから S1 レベルに移動できますが、S2 レベルから B1 レベルに移動することはできません。 1 つのレベル内の[エディション](https://azure.microsoft.com/pricing/details/iot-hub/)は IoT Hub あたり 1 種類だけ選択できます。 たとえば、S1 と B3 や S1 と S2 などの異なるエディションのユニットの組み合わせではなく、S1 のユニットを複数持つ IoT Hub を作成できます。

これらの例は、ソリューションが変化したときに IoT Hub をどのように調整するかを理解するためものです。 各レベルの機能の詳細については、常に「[IoT Hub の価格](https://azure.microsoft.com/pricing/details/iot-hub/)」を参照してください。 

## <a name="upgrade-your-existing-iot-hub"></a>既存の IoT Hub をアップグレードする 

1. [Azure Portal](https://portal.azure.com/) にサインインし、IoT Hub に移動します。 
2. **[価格とスケール]** を選択します。 

   ![価格とスケール](./media/iot-hub-upgrade/pricing-scale.png)

3. Hub のレベルを変更するには、**[価格とスケール レベル]** を選択します。 新しいレベルを選択し、**[選択]** をクリックします。

   ![価格とスケール](./media/iot-hub-upgrade/select-tier.png)

4. Hub のユニット数を変更するには、**[IoT Hub ユニット]** の下に新しい値を入力します。 
5. **[保存]** を選択して変更を保存します。 

IoT Hub が調整されますが、構成は変更されません。 Basic レベルの IoT Hub のパーティションの最大制限は 8 で、Standard レベルの場合は 32 であることにご注意ください。 ほとんどの IoT Hub では、4 つのパーティションのみ必要です。 IoT Hub の作成時にパーティション制限が選択され、device-to-cloud メッセージがこれらのメッセージの同時閲覧者数に関連付けられます。 Basic レベルから Standard レベルに移行してもこの値は変わりません。 

## <a name="next-steps"></a>次の手順

[適切な IoT Hub のレベルを選択する方法](iot-hub-scaling.md)に関する記事で詳細を確認します。 

