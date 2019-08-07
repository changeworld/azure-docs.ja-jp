---
title: IoT Hub で Azure Security Center for IoT サービスを有効にする | Microsoft Docs
description: IoT Hub で Azure Security Center for IoT サービスを有効にする方法について説明します。
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
ms.date: 05/16/2019
ms.author: mlottner
ms.openlocfilehash: a794ccea13323f38b20906458e216f85652bfc3e
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596951"
---
# <a name="quickstart-onboard-azure-security-center-for-iot-service-in-iot-hub"></a>クイック スタート:IoT Hub で Azure Security Center for IoT サービスをオンボードする

この記事では、既存の IoT Hub 上で Azure Security Center for IoT サービスを有効にする方法について説明します。 現在 IoT Hub がない場合、作業を開始するには、「[Azure portal を使用して IoT Hub を作成する](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal)」を参照してください。 

> [!NOTE]
> Azure Security Center for IoT では、現在、Standard レベルの IoT Hub だけがサポートされています。
> Azure Security Center for IoT は、単一ハブのソリューションです。 複数のハブが必要な場合は、複数の Azure Security Center for IoT ソリューションが必要になります。 

## <a name="prerequisites-for-enabling-the-service"></a>サービスを有効にするための前提条件

- Log Analytics ワークスペース
  - 既定では、Azure Security Center for IoT によって、2 種類の情報 (**セキュリティ アラート**と**レコメンデーション**) が Log Analytics ワークスペースに保存されます。 
  - 追加の情報の種類として**未加工のイベント**のストレージを追加することもできます。 Log Analytics に**未加工のイベント**を保存すると、追加のストレージ コストがかかることに注意してください。 
- IoT Hub (Standard レベル)
- すべての[サービスの前提条件](service-prerequisites.md)を満たしていること 

|サポートされている Azure サービス リージョン | ||
|---|---|---|
| 米国中部 |East US |米国東部 2 |
| 米国中西部 |米国西部 |米国西部 2 |
| 米国中南部|米国中北部 | カナダ中部|
| カナダ東部| 北ヨーロッパ|ブラジル南部|
| フランス中部| 英国西部|英国南部|
|西ヨーロッパ|北ヨーロッパ| 西日本|
|東日本 | オーストラリア南東部|オーストラリア東部|
|東アジア| 東南アジア| 韓国中部|
|韓国南部| インド中部| インド南部|
|

## <a name="enable-azure-security-center-for-iot-on-your-iot-hub"></a>IoT Hub 上で Azure Security Center for IoT を有効にする 

IoT Hub でセキュリティを有効にするには、以下の手順を実行します。 

1. Azure portal で **[IoT Hub]** を開きます。 
1. **[セキュリティ]** メニューの **[Secure your IoT solution]\(IoT ソリューションのセキュリティ保護\)** をクリックします。
1. **[有効にする]** は既定のオンのままにします。 
1. Log Analytics ワークスペースを選択します。
1. Log Analytics ワークスペースの詳細を指定します。 
   - **ツイン コレクション**のトグルを**オン**のままにして、**ツイン コレクション**を有効にすることを選択します。
   - Log Analytics で **[Store raw device security events]\(生デバイス セキュリティ イベントの保存\)** を選択して、ストレージの既定の情報の種類に加えて**生イベント**を保存することを選択します。 **生イベント**のトグルは、**オン**のままにしておきます。 
    
1. **[Save]** をクリックします。 

お疲れさまでした。 IoT Hub 上で Azure Security Center for IoT を有効にすることができました。 

## <a name="next-steps"></a>次の手順

次の記事に進んで、ソリューションを構成してください。

> [!div class="nextstepaction"]
> [ソリューションを構成する](quickstart-configure-your-solution.md)


