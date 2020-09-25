---
title: クイック スタート:サービスを有効にする
description: Azure IoT Hub で Defender for IoT セキュリティ サービスをオンボードし、有効にする方法について説明します。
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/06/2020
ms.author: mlottner
ms.openlocfilehash: 3f84f3121d9982205ecf51ec64cfe332b6a5ad42
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/22/2020
ms.locfileid: "90945926"
---
# <a name="quickstart-onboard-azure-defender-for-iot-service-in-iot-hub"></a>クイック スタート:IoT Hub での Azure Defender for IoT サービスのオンボード

この記事では、既存の IoT Hub 上で Defender for IoT サービスを有効にする方法について説明します。 現在 IoT Hub がない場合、作業を開始するには、「[Azure portal を使用して IoT Hub を作成する](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal)」を参照してください。

> [!NOTE]
> Defender for IoT では、現在、Standard レベルの IoT Hub だけがサポートされています。

## <a name="prerequisites-for-enabling-the-service"></a>サービスを有効にするための前提条件

- Log Analytics ワークスペース
  - 既定では、Defender for IoT によって、2 種類の情報 (**セキュリティ アラート**と**レコメンデーション**) が Log Analytics ワークスペースに保存されます。
  - 追加の情報の種類として**未加工のイベント**のストレージを追加することもできます。 Log Analytics に**未加工のイベント**を保存すると、追加のストレージ コストがかかることに注意してください。
- IoT Hub (Standard レベル)
- すべての[サービスの前提条件](service-prerequisites.md)を満たしていること

## <a name="enable-defender-for-iot-on-your-iot-hub"></a>IoT Hub で Defender for IoT を有効にする

IoT Hub でセキュリティを有効にするには、次の手順を実行します。

1. Azure portal で **[IoT Hub]** を開きます。
1. **[セキュリティ]** メニューの **[Secure your IoT solution]\(IoT ソリューションのセキュリティ保護\)** をクリックします。

お疲れさまでした。 IoT Hub で Defender for IoT を有効にすることができました。

### <a name="geolocation-and-ip-address-handling"></a>位置情報と IP アドレスの処理

IoT ソリューションのセキュリティを確保するために、IoT デバイス、IoT Edge、IoT Hub を出入りする受信接続と送信接続の IP アドレスは既定で収集され、保存されます。 疑わしい IP ソースからの異常な接続を検出するためには、この情報が欠かせません。 たとえば、既知のボットネットの IP ソースやご利用の位置情報とは異なる外部の IP ソースから接続を確立しようとするケースが考えられます。 Defender for IoT サービスでは、一連の IP アドレス データをいつでも柔軟に有効にしたり無効にしたりすることができます。

一連の IP アドレス データを有効または無効にするには、次の手順に従います。

1. ご利用の IoT Hub を開き、 **[セキュリティ]** メニューから **[設定]** メニューを選択します。
1. **[データ コレクション]** 画面を選択し、位置情報または IP 処理設定に適宜変更を加えます。

### <a name="log-analytics-creation"></a>Log Analytics の作成

Defender for IoT を有効にすると、IoT デバイス、IoT Edge、IoT ハブに関する生のセキュリティ イベント、アラート、レコメンデーションを格納するための既定の Azure Log Analytics ワークスペースが作成されます。 毎月 Azure Log Analytics サービスに取り込まれるデータのうち、お客様 1 人あたり最初の 5 GB は無料です。 Azure Log Analytics ワークスペースに取り込まれたすべてのデータ (GB) は、最初の 31 日間無料で保持されます。 [Log Analytics](https://azure.microsoft.com/pricing/details/monitor/) 価格の詳細情報。

Log Analytics のワークスペースの構成を変更するには、次の手順に従います。

1. ご利用の IoT Hub を開き、 **[セキュリティ]** メニューから **[設定]** メニューを選択します。
1. **[データ コレクション]** 画面を選択し、Log Analytics 設定のワークスペース構成に適宜変更を加えます。

### <a name="customize-your-iot-security-solution"></a>IoT セキュリティ ソリューションをカスタマイズする

既定では、Defender for IoT ソリューションを有効にすると、ご利用の Azure サブスクリプションにあるすべての IoT Hub のセキュリティが自動的に確保されます。

特定の IoT Hub に対して Defender for IoT サービスを有効または無効にするには、次の手順に従います。

1. ご利用の IoT Hub を開き、 **[セキュリティ]** メニューから **[設定]** メニューを選択します。
1. **[データ コレクション]** 画面を選択し、ご利用の Azure サブスクリプションに存在する IoT ハブのセキュリティ設定に適宜変更を加えます。

## <a name="next-steps"></a>次のステップ

次の記事に進んで、ソリューションを構成してください。

> [!div class="nextstepaction"]
> [ソリューションを構成する](quickstart-configure-your-solution.md)
