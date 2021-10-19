---
title: 診断設定を使用して IoT コネクタのメトリックをエクスポートする-Azure の医療 Api
description: この記事では、診断設定を使用して IoT Connector のメトリックをエクスポートする方法について説明します
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: how-to
ms.date: 09/30/2021
ms.author: jasteppe
ms.openlocfilehash: 23908c8ab910324cda4cd2802158447a1c97db4e
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2021
ms.locfileid: "129354588"
---
# <a name="export-iot-connector-metrics-through-diagnostic-settings"></a>診断設定を使用して IoT コネクタのメトリックをエクスポートする

> [!IMPORTANT]
> Azure Healthcare APIs は現在プレビュー段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」に記載されています。

この記事では、IoT コネクタのメトリックログをエクスポートする方法について説明します。 メトリック ログ記録を有効にする機能は、Azure portal の [ **[診断設定]**](../../azure-monitor/essentials/diagnostic-settings.md) に相当します。 

## <a name="enable-metrics-logging-for-iot-connector"></a>IoT コネクタのメトリックログを有効にする
1. IoT コネクタのメトリックログ記録を有効にするには、Azure portal で高速医療相互運用性リソース (FHIR&#174;) サービスを選択します。 

2. **[診断設定]** に移動します 

3. **[+ 診断設定を追加する]** を選択します

   :::image type="content" source="media/iot-metrics-export/diagnostic-settings-main.png" alt-text="IoT connector1" lightbox="media/iot-metrics-export/diagnostic-settings-main.png"::: 

4. **[診断設定の名前]** ダイアログ ボックスに名前を入力します。

5. 診断ログへのアクセスに使用する方法を選択します。

    1. 監査や手動での検査のために、**ストレージ アカウントにアーカイブ** します。 使用するストレージ アカウントは既に作成済みである必要があります。
    2. サード パーティのサービスやカスタム分析ソリューションで取り込むために、**イベント ハブにストリーム配信** します。 この手順を構成する前に、イベント ハブの名前空間とイベント ハブのポリシーを作成する必要があります。
    3. Azure Monitor の **Log Analytics ワークスペースにストリーム配信** します。 このオプションを選択する前に、Log Analytics ワークスペースを作成する必要があります。

6. IoT コネクタの **エラー、トラフィック、待機時間** を選択します。  FHIR サービス用にキャプチャする追加のメトリックカテゴリを選択します。

7. **[保存]** を選びます。

   :::image type="content" source="media/iot-metrics-export/diagnostic-setting-add.png" alt-text="IoT connector2" lightbox="media/iot-metrics-export/diagnostic-setting-add.png":::

> [!Note] 
> 選択したリポジトリに最初のメトリック ログが表示されるまでに最大 15 分かかることがあります。  
 
診断ログの使用方法の詳細については、[Azure リソース ログのドキュメント](../../azure-monitor/essentials/platform-logs-overview.md)を参照してください

## <a name="conclusion"></a>まとめ 
メトリック ログにアクセス可能であることは、監視とトラブルシューティングに欠かせません。  IoT コネクタでは、メトリックログを使用してこれらのアクションを実行できます。 

## <a name="next-steps"></a>次の手順

IoT コネクタについてよく寄せられる質問を確認してください。

>[!div class="nextstepaction"]
>[IoT コネクタに関する Faq](../fhir/fhir-faq.md)

(FHIR&#174;) HL7 の登録商標であり、HL7 のアクセス許可と共に使用されます。