---
title: ASC for IoT の前提条件 (プレビュー) | Microsoft Docs
description: ASC for IoT サービスの利用を始めるために必要なすべての前提条件の詳細です。
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 790cbcb7-1340-4cc1-9509-7b262e7c3181
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: 213e6a95484b5f6953f8423474953125f8739015
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541524"
---
# <a name="asc-for-iot-prerequisites"></a>ASC for IoT の前提条件

> [!IMPORTANT]
> ASC for IoT は現在、パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

この記事では、ASC for IoT サービスのさまざまな構成要素、利用を始めるために必要なもの、およびサービスの理解に役立つ基本的な概念について説明します。 

## <a name="minimum-requirements"></a>最小要件

- IoT Hub Standard レベル
    - RBAC ロールの**所有者**レベルの特権 
- [Log Analytics ワークスペース](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) 
- Azure Security Center (推奨)
    - Azure Security Center の使用は推奨であり、必須ではありませんが、使用しないと IoT Hub 内の他の Azure リソースを表示できなくなります。 
 
## <a name="working-with-asc-for-iot-service"></a>ASC for IoT サービスの操作

ASC for IoT の分析情報とレポートは、Azure IoT Hub と Azure Security Center を使用して入手できます。 Azure IoT Hub で ASC for IoT を有効にするには、**所有者**レベルの特権を持つアカウントが必要です。 IoT Hub で ASC for IoT を有効にすると、ASC for IoT 分析情報が、Azure IoT Hub では **[セキュリティ]** 機能として、Azure Security Center では **[IoT]** として表示されます。 

## <a name="supported-service-regions"></a>サポートされているサービス リージョン 

ASC for IoT は、現在のところ次の Azure リージョン内の IoT Hub でサポートされています。
  - 米国中央部
  - 北ヨーロッパ
  - 東南アジア

## <a name="wheres-my-iot-hub"></a>自分の IoT Hub はどこにありますか?

開始する前に、ご自分の IoT Hub の場所を確認して、サービスの提供状況を確認してください。 

1. IoT ハブを開きます。 
2. **[Overview]** をクリックします。 
3. 表示される場所が[サポートされているサービス リージョン](#supported-service-regions)のいずれかと一致することを確認します。 


## <a name="supported-platforms-for-agents"></a>エージェントでサポートされているプラットフォーム 

ASC for IoT エージェントは、増え続けているデバイスとプラットフォームをサポートしています。 [サポートされているプラットフォームの一覧](select-deploy-agent.md)を参照して、既存または予定しているデバイス ライブラリを確認してください。  

## <a name="next-steps"></a>次の手順
- [概要](overview.md)
- [サービスを有効にする](quickstart-onboard-iot-hub.md)
- [ASC for IoT についてよく寄せられる質問](resources-frequently-asked-questions.md)
- [ASC for IoT アラートについて](concept-security-alerts.md)
