---
title: Azure Sentinel を使用した構成のための Azure Security Center for IoT ガイド (プレビュー) | Microsoft Docs
description: この操作ガイドでは、お使いの Azure Security Center for IoT ソリューションからデータを受信するように Azure Sentinel を構成する方法について説明します。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/18/2020
ms.author: mlottner
ms.openlocfilehash: f6e7eddd6ddbcec61c3d8d173891cbc9abaaf08f
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/19/2020
ms.locfileid: "77465656"
---
> [!IMPORTANT]
> Azure Sentinel の Azure Security Center for IoT データ コネクタは、現在パブリック プレビューです。
> この機能はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

# <a name="connect-your-data-from-azure-security-center-for-iot-to-azure-sentinel-preview"></a>Azure Security Center for IoT からのデータを Azure Sentinel に接続する (プレビュー) 

このガイドでは、Azure Security Center for IoT からのデータを Azure Sentinel に接続する方法について学習します。  

> [!div class="checklist"]
> * 前提条件 
> * 接続の設定
> * Log Analytics のアラートの表示 

Azure Security Center for IoT からのアラートを接続して Azure Sentinel に直接ストリーム配信します。

## <a name="prerequisites"></a>前提条件

- ワークスペースに対する**読み取り**アクセス許可と**書き込み**アクセス許可が必要です。
- お使いの適切な IoT ハブで **Azure Security Center for IoT** を**有効**にする必要があります。
- 接続先の **Azure IoT Hub** に対する**読み取り**アクセス許可と**書き込み**アクセス許可の両方が必要です。
- さらに、**Azure IoT Hub リソース グループ**に対する**読み取り**アクセス許可と**書き込み**アクセス許可が必要です。

> [!NOTE]
> 一般的な Azure リソース アラートを送信するには、お使いのサブスクリプションで Azure Security Center Standard レベルのライセンスが実行されている必要があります。 Azure Security Center for IoT に必要な Free レベルのライセンスでは、Azure Security Center for IoT 関連のアラートのみが Azure Sentinel に転送されます。 

## <a name="connect-to-azure-security-center-for-iot"></a>Azure Security Center for IoT に接続する

1. Azure Sentinel で、 **[データ コネクタ]** を選択し、 **[Azure Security Center for IoT]** タイルをクリックします。
1. 右下のペインで、 **[コネクタ ページを開く]** をクリックします。 
1. アラートとデバイス アラートを Azure Sentinel にストリーム配信する各 IoT Hub サブスクリプションの横にある **[接続]** をクリックします。 
    - そのハブで Azure Security Center for IoT が有効になっていない場合は、有効化に関する警告メッセージが表示されます。 **[Enable]\(有効にする\)** リンクをクリックして、サービスを開始します。 
1. Azure Security Center for IoT からのアラートによって Azure Sentinel でインシデントが生成されるようにするかどうかを指定できます。 **[Create incidents]\(インシデントの作成\)** で **[Enable]\(有効にする\)** を選択して、接続されたセキュリティ サービスで生成されたアラートからインシデントを自動的に作成する既定の分析ルールを有効にします。このルールは **[Analytics]**  >  **[アクティブ]** ルールで変更または編集できます。

> [!NOTE]
>接続を変更した後、ハブ リストが更新されるまでに 10 秒以上かかる場合があります。 

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

このドキュメントでは、Azure Security Center for IoT を Azure Sentinel に接続する方法について学習しました。 脅威の検出とセキュリティ データ アクセスの詳細については、次の記事を参照してください。

- Azure Sentinel を使用して[データと潜在的な脅威を可視化する](https://docs.microsoft.com/azure/sentinel/quickstart-get-visibility)方法を学習します。

- [IoT セキュリティ データにアクセス](how-to-security-data-access.md)する方法を学習します。