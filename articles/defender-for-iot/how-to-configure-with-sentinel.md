---
title: Defender for IoT 用に Azure Sentinel を構成する (プレビュー)
description: Defender for IoT ソリューションからデータを受信するように Azure Sentinel を構成する方法について説明します。
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: 12f65d0e7f9c380f77fe4189d26fdeafd426295b
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2020
ms.locfileid: "92090796"
---
# <a name="connect-your-data-from-defender-for-iot-to-azure-sentinel-preview"></a>Defender for IoT からのデータを Azure Sentinel に接続する (プレビュー)

Azure Sentinel の Azure Defender for IoT データ コネクタは、現在パブリック プレビュー段階にあります。 この機能はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/en-us/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

このガイドでは、Defender for IoT からのデータを Azure Sentinel に接続する方法について学習します。

> [!div class="checklist"]
> * 前提条件
> * 接続の設定
> * Log Analytics のアラートの表示

Defender for IoT からのアラートを接続して Azure Sentinel に直接ストリーム配信します。

Microsoft では、最初のクラウドネイティブ SIEM である Azure Sentinel と、IoT と OT のネイティブ セキュリティを備えた最初の SIEM である Azure Defender for IoT をより緊密に統合することで、IT と産業ネットワーク全体でセキュリティを統合するためのより簡単なアプローチを提供しています。 Azure Sentinel の機械学習とこの統合を組み合わせることで、組織では、IT と OT の境界を越えることの多いマルチステージ攻撃を迅速に検出できます。 さらに、Azure Defender for IoT と Azure Sentinel のセキュリティ オーケストレーション、オートメーション、および応答 (SOAR) 機能との統合により、OT 向けに最適化された組み込みのプレイブックを使って応答と防止を自動化できます。 

## <a name="prerequisites"></a>前提条件

- ワークスペースに対する **読み取り** アクセス許可と **書き込み** アクセス許可が必要です。
- お使いの適切な IoT ハブで **Defender for IoT** を **有効** にする必要があります。
- 接続先の **Azure IoT Hub** に対する **読み取り** アクセス許可と **書き込み** アクセス許可の両方が必要です。
- さらに、 **Azure IoT Hub リソース グループ** に対する **読み取り** アクセス許可と **書き込み** アクセス許可が必要です。


## <a name="connect-to-defender-for-iot"></a>Defender for IoT に接続する

1. Azure Sentinel で、 **[データ コネクタ]** を選択し、 **[Defender for IoT]** タイルをクリックします。
1. 右側のペインの下部で、 **[コネクタ ページを開く]** をクリックします。
1. アラートとデバイス アラートを Azure Sentinel にストリーム配信する各 IoT Hub サブスクリプションの横にある **[接続]** をクリックします。
    - そのハブで Defender for IoT が有効になっていない場合は、有効化に関する警告メッセージが表示されます。 **[有効]** リンクをクリックし、サービスを開始して有効にします。
1. Defender for IoT からのアラートによって Azure Sentinel でインシデントが生成されるようにするかどうかを指定できます。 **[インシデントの作成]** で **[有効]** を選択して、生成されたアラートからインシデントを自動的に作成するルールを有効にします。  このルールは、 **[分析]**  >  **[アクティブ]** ルールで変更または編集できます。

> [!NOTE]
>接続を変更した後、ハブ リストが更新されるまでに 10 秒以上かかる場合があります。

## <a name="using-log-analytics-for-alert-display"></a>アラート表示に Log Analytics を使用する

Log Analytics の適切なスキーマを使用して Defender for IoT のアラートを表示するには、次のようにします。

1. **[ログ]**  >  **[SecurityInsights]**  >  **[SecurityAlert]** を開くか、 **SecurityAlert** を検索します。
1. 次の kql フィルターを使用して、Defender for IoT によって生成されたアラートのみが表示されるようにします。

```kusto
SecurityAlert | where ProductName == "Defender for IoT"
```

### <a name="service-notes"></a>サービスに関する注意事項

IoT ハブに接続してから約 15 分後に Azure Sentinel でハブ データを利用できるようになります。

## <a name="next-steps"></a>次のステップ

このドキュメントでは、Defender for IoT を Azure Sentinel に接続する方法について学習しました。 脅威の検出とセキュリティ データ アクセスの詳細については、次の記事を参照してください。

- Azure Sentinel を使用して[データと潜在的な脅威を可視化する](https://docs.microsoft.com/azure/sentinel/quickstart-get-visibility)方法を学習します。

- [IoT セキュリティ データにアクセス](how-to-security-data-access.md)する方法を学習します。
