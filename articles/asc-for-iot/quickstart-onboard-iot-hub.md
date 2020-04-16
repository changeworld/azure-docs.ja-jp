---
title: クイック スタート:サービスを有効にする
description: Azure IoT Hub で Azure Security Center for IoT セキュリティ サービスをオンボードし、有効にする方法について説明します。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 670e6d2b-e168-4b14-a9bf-51a33c2a9aad
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/08/2019
ms.author: mlottner
ms.openlocfilehash: 0d3e4ad63baf6883aa8fadaca5b1f2d28fa14881
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/14/2020
ms.locfileid: "81310748"
---
# <a name="quickstart-onboard-azure-security-center-for-iot-service-in-iot-hub"></a>クイック スタート:IoT Hub で Azure Security Center for IoT サービスをオンボードする

この記事では、既存の IoT Hub 上で Azure Security Center for IoT サービスを有効にする方法について説明します。 現在 IoT Hub がない場合、作業を開始するには、「[Azure portal を使用して IoT Hub を作成する](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal)」を参照してください。

> [!NOTE]
> Azure Security Center for IoT では、現在、Standard レベルの IoT Hub だけがサポートされています。

## <a name="prerequisites-for-enabling-the-service"></a>サービスを有効にするための前提条件

- Log Analytics ワークスペース
  - 既定では、Azure Security Center for IoT によって、2 種類の情報 (**セキュリティ アラート**と**レコメンデーション**) が Log Analytics ワークスペースに保存されます。
  - 追加の情報の種類として**未加工のイベント**のストレージを追加することもできます。 Log Analytics に**未加工のイベント**を保存すると、追加のストレージ コストがかかることに注意してください。
- IoT Hub (Standard レベル)
- すべての[サービスの前提条件](service-prerequisites.md)を満たしていること

## <a name="enable-azure-security-center-for-iot-on-your-iot-hub"></a>IoT Hub 上で Azure Security Center for IoT を有効にする

IoT Hub でセキュリティを有効にするには、次の手順を実行します。

1. Azure portal で **[IoT Hub]** を開きます。
1. **[セキュリティ]** メニューの **[Secure your IoT solution]\(IoT ソリューションのセキュリティ保護\)** をクリックします。

お疲れさまでした。 IoT Hub 上で Azure Security Center for IoT を有効にすることができました。

### <a name="geolocation-and-ip-address-handling"></a>位置情報と IP アドレスの処理

IoT ソリューションのセキュリティを確保するために、IoT デバイス、IoT Edge、IoT Hub を出入りする受信接続と送信接続の IP アドレスは既定で収集され、保存されます。 疑わしい IP ソースからの異常な接続を検出するためには、この情報が欠かせません。 たとえば、既知のボットネットの IP ソースやご利用の位置情報とは異なる外部の IP ソースから接続を確立しようとするケースが考えられます。 Azure Security Center for IoT サービスでは、一連の IP アドレス データをいつでも柔軟に有効にしたり無効にしたりすることができます。

一連の IP アドレス データを有効または無効にするには、次の手順に従います。

1. ご利用の IoT ハブを開き、 **[セキュリティ]** メニューから **[概要]** を選択します。
1. **[設定]** 画面を選択し、位置情報または IP 処理設定に適宜変更を加えます。

### <a name="log-analytics-creation"></a>Log Analytics の作成

Azure Security Center for IoT を有効にすると、IoT デバイス、IoT Edge、IoT ハブに関する生のセキュリティ イベント、アラート、推奨事項を格納するための既定の Azure Log Analytics ワークスペースが作成されます。 毎月 Azure Log Analytics サービスに取り込まれるデータのうち、お客様 1 人あたり最初の 5 GB は無料です。 Azure Log Analytics ワークスペースに取り込まれたすべてのデータ (GB) は、最初の 31 日間無料で保持されます。 [Log Analytics](https://azure.microsoft.com/pricing/details/monitor/) 価格の詳細情報。

Log Analytics のワークスペースの構成を変更するには、次の手順に従います。

1. ご利用の IoT ハブを開き、 **[セキュリティ]** メニューから **[概要]** を選択します。
1. **[設定]** 画面を選択し、Log Analytics 設定のワークスペース構成に適宜変更を加えます。

### <a name="customize-your-iot-security-solution"></a>IoT セキュリティ ソリューションをカスタマイズする

既定では、Azure Security Center for IoT ソリューションを有効にすると、ご利用の Azure サブスクリプションにあるすべての IoT ハブのセキュリティが自動的に確保されます。

特定の IoT ハブに対して Azure Security Center for IoT サービスを有効または無効にするには、次の手順に従います。

1. ご利用の IoT ハブを開き、 **[セキュリティ]** メニューから **[概要]** を選択します。
1. **[設定]** 画面を選択し、ご利用の Azure サブスクリプションに存在する IoT ハブのセキュリティ設定に適宜変更を加えます。

## <a name="next-steps"></a>次のステップ

次の記事に進んで、ソリューションを構成してください。

> [!div class="nextstepaction"]
> [ソリューションを構成する](quickstart-configure-your-solution.md)
