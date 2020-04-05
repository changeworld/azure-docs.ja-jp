---
title: Azure Security Center for IoT を Azure Sentinel に接続する | Microsoft Docs
description: Azure Sentinel に Azure Security Center for IoT からのデータを接続する方法について説明します。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/18/2020
ms.author: yelevin
ms.openlocfilehash: 3af51110a4c4604444573f62be65077c786db606
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588639"
---
# <a name="connect-your-data-from-azure-security-center-for-iot-to-azure-sentinel"></a>Azure Security Center for IoT からのデータを Azure Sentinel に接続する 


> [!IMPORTANT]
> Azure Security Center for IoT データ コネクタは、現在パブリック プレビュー段階です。 この機能はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

Azure Security Center for IoT コネクタを使用して、Azure Security Center for IoT イベントをすべて Azure Sentinel にストリーミングします。 

## <a name="prerequisites"></a>前提条件

- Azure Sentinel がデプロイされているワークスペースに対する**読み取り**および**書き込み**アクセス許可
- お使いの適切な IoT ハブで **Azure Security Center for IoT** を**有効**にする必要があります
- 接続する **Azure IoT Hub** に対する**読み取り**および**書き込み**アクセス許可
- **Azure IoT Hub リソース グループ**に対する**読み取り**および**書き込み**アクセス許可

> [!NOTE]
> Azure Sentinel に IoT リソース アラートをストリーミングするには、サブスクリプションで Azure Security Center **Standard** レベルのライセンスを有効にする必要がありますが、Azure Sentinel で Azure Security Center for IoT アラートを表示するには、サブスクリプションで Azure Security Center **Free レベル**のライセンスを有効にするだけ済みます。 

## <a name="connect-to-azure-security-center-for-iot"></a>Azure Security Center for IoT に接続する

1. Azure Sentinel で、 **[データ コネクタ]** を選択し、 **[Azure Security Center for IoT]** タイルをクリックします。
1. 右下のペインで、 **[コネクタ ページを開く]** をクリックします。 
1. アラートとデバイス アラートを Azure Sentinel にストリーム配信する各 IoT Hub サブスクリプションの横にある **[接続]** をクリックします。 
    - そのハブで Azure Security Center for IoT が有効になっていない場合は、**有効化**に関する警告メッセージが表示されます。 **[Enable]\(有効にする\)** リンクをクリックして、サービスを開始します。 
1. Azure Security Center for IoT からのアラートによって Azure Sentinel でインシデントが生成されるようにするかどうかを指定できます。 **[Create incidents]\(インシデントの作成\)** で **[Enable]\(有効にする\)** を選択して、接続されたセキュリティ サービスで生成されたアラートからインシデントを自動的に作成する既定の分析ルールを有効にします。このルールは **[Analytics]**  >  **[アクティブ]** ルールで変更または編集できます。

> [!NOTE]
> 接続を変更した後、ハブの一覧が更新されるまでにしばらく時間がかかる場合があります。 

## <a name="log-analytics-alert-display"></a>Log Analytics のアラートの表示

Log Analytics の適切なスキーマを使用して Azure Security Center for IoT のアラートを表示するには:

1. **[ログ]**  >  **[SecurityInsights]**  >  **[SecurityAlert]** を開くか、**SecurityAlert** を検索します。 
2. 次の kql フィルターを使用して、Azure Security Center for IoT によって生成されたアラートのみが表示するようにします。

```kusto
SecurityAlert | where ProductName == "Azure Security Center for IoT"
``` 

### <a name="service-notes"></a>サービスに関する注意事項

IoT ハブに接続してから約 15 分後に Azure Sentinel でハブ データを利用できるようになります。


## <a name="next-steps"></a>次のステップ

このドキュメントでは、Azure Security Center for IoT のデータを Azure Sentinel に接続する方法について説明しました。 Azure Sentinel の詳細については、次の記事をご覧ください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats-built-in.md)の概要。
- [ブックを使用](tutorial-monitor-your-data.md)してデータを監視する。
