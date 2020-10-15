---
title: Azure Defender for IoT を Azure Sentinel に接続する | Microsoft Docs
description: Azure Sentinel に Azure Defender (旧称 Azure Security Center) for IoT からのデータを接続する方法について説明します。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/07/2020
ms.author: yelevin
ms.openlocfilehash: 822d0c742bbd54b5bab0c69e82652743584a0696
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "89659616"
---
# <a name="connect-your-data-from-azure-defender-formerly-azure-security-center-for-iot-to-azure-sentinel"></a>Azure Defender (旧称 Azure Security Center) for IoT からのデータを Azure Sentinel に接続する 


> [!IMPORTANT]
> Azure Defender for IoT データ コネクタは、現在パブリック プレビュー段階です。 この機能はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

Azure Defender for IoT コネクタを使用して、Azure Defender for IoT イベントをすべて Azure Sentinel にストリーミングします。 

## <a name="prerequisites"></a>前提条件

- Azure Sentinel がデプロイされているワークスペースに対する**読み取り**および**書き込み**アクセス許可
- お使いの適切な IoT ハブで **Azure Defender for IoT** を**有効**にする必要があります
- 接続する **Azure IoT Hub** に対する**読み取り**および**書き込み**アクセス許可
- **Azure IoT Hub リソース グループ**に対する**読み取り**および**書き込み**アクセス許可

## <a name="connect-to-azure-defender-for-iot"></a>Azure Defender for IoT に接続する

1. Azure Sentinel で、 **[データ コネクタ]** を選択し、 **[Azure Defender for IoT]** (名称は Azure Security Center for IoT のままの場合があります) をギャラリーから選択します。
1. 右下のペインで、 **[コネクタ ページを開く]** をクリックします。 
1. アラートとデバイス アラートを Azure Sentinel にストリーム配信する各 IoT Hub サブスクリプションの横にある **[接続]** をクリックします。 
    - そのハブで Azure Defender for IoT が有効になっていない場合は、**有効化**に関する警告メッセージが表示されます。 **[Enable]\(有効にする\)** リンクをクリックして、サービスを開始します。 
1. Azure Defender for IoT からのアラートによって Azure Sentinel でインシデントが生成されるようにするかどうかを指定できます。 **[Create incidents]\(インシデントの作成\)** で **[Enable]\(有効にする\)** を選択して、接続されたセキュリティ サービスで生成されたアラートからインシデントを自動的に作成する既定の分析ルールを有効にします。このルールは **[Analytics]**  >  **[アクティブ]** ルールで変更または編集できます。

> [!NOTE]
> 接続を変更した後、ハブの一覧が更新されるまでにしばらく時間がかかる場合があります。 

## <a name="log-analytics-alert-display"></a>Log Analytics のアラートの表示

Log Analytics の適切なスキーマを使用して Azure Defender for IoT のアラートを表示するには:

1. **[ログ]**  >  **[SecurityInsights]**  >  **[SecurityAlert]** を開くか、**SecurityAlert** を検索します。 
2. 次の kql フィルターを使用して、Azure Defender for IoT によって生成されたアラートのみが表示するようにします。

```kusto
SecurityAlert | where ProductName == "Azure Defender for IoT"
``` 

### <a name="service-notes"></a>サービスに関する注意事項

IoT ハブに接続してから約 15 分後に Azure Sentinel でハブ データを利用できるようになります。


## <a name="next-steps"></a>次のステップ

このドキュメントでは、Azure Defender for IoT のデータを Azure Sentinel に接続する方法について説明しました。 Azure Sentinel の詳細については、次の記事をご覧ください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats-built-in.md)の概要。
- [ブックを使用](tutorial-monitor-your-data.md)してデータを監視する。
