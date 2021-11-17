---
title: デバイス ビルダー向け Defender for IoT を使用して Microsoft Sentinel を構成する
description: この記事では、デバイス ビルダー向け Defender for IoT ソリューションからデータを受信するように Microsoft Sentinel を構成する方法について説明します。
ms.topic: how-to
ms.date: 11/09/2021
ms.openlocfilehash: e64b0db2af1c1b742c3997e616eea43f5c969522
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132306170"
---
# <a name="connect-your-data-from-defender-for-iot-for-device-builders-to-microsoft-sentinel-public-preview"></a>デバイス ビルダー向け Defender for IoT からのデータを Microsoft Sentinel に接続する (パブリック プレビュー)

Defender for IoT コネクタを使用して、Defender for IoT イベントをすべて Microsoft Sentinel にストリーミングします。 

この統合により、組織では、IT と OT の境界を越えることの多いマルチステージ攻撃を迅速に検出できます。 さらに、Defender for IoT と Microsoft Sentinel のセキュリティ オーケストレーション、オートメーション、および応答 (SOAR) 機能との統合により、OT 向けに最適化された組み込みのプレイブックを使って応答と防止を自動化できます。 

## <a name="prerequisites"></a>必須コンポーネント

- Microsoft Sentinel がデプロイされているワークスペースに対する **読み取り** および **書き込み** アクセス許可
- お使いの適切な IoT Hub で **Defender for IoT** を **有効** にする
- 接続する **サブスクリプション** に対する **共同作成者** アクセス許可

## <a name="connect-to-defender-for-iot"></a>Defender for IoT に接続する

1. Microsoft Sentinel で、 **[データ コネクタ]** を選択し、 **[Defender for IoT]** (旧称は Azure Security Center for IoT) をギャラリーから選択します。

1. 右側のペインの下部で、 **[コネクタ ページを開く]** をクリックします。

1. アラートとデバイス アラートを Microsoft Sentinel にストリーム配信する各 IoT Hub サブスクリプションの横にある **[接続]** をクリックします。
    - サブスクリプション内の少なくとも 1 つの IoT Hub で Defender for IoT が有効になっていない場合、エラーメッセージが表示されます。 エラーを削除するには、IoT Hub 内で Defender for IoT を有効にします。

1. Defender for IoT からのアラートによって Microsoft Sentinel でインシデントが自動的に生成されるようにするかどうかを指定できます。 **[インシデントの作成]** で **[有効]** を選択して、生成されたアラートからインシデントを自動的に作成する既定の分析ルールを有効にします。 このルールは、 **[分析]**  >  **[アクティブ]** ルールで変更または編集できます。

> [!NOTE]
> 接続を変更した後、**サブスクリプション** リストが更新されるまでに 10 秒以上かかる場合があります。 

## <a name="log-analytics-alert-view"></a>Log Analytics のアラートの表示

Log Analytics の適切なスキーマを使用して Defender for IoT のアラートを表示するには、次のようにします。

1. **[ログ]**  >  **[SecurityInsights]**  >  **[SecurityAlert]** を開くか、**SecurityAlert** を検索します。

1. 次の kql フィルターを使用して、Defender for IoT によって生成されたアラートのみが表示されるようにします。

```kusto
SecurityAlert | where ProductName == "Azure Security Center for IoT"
```

### <a name="service-notes"></a>サービスに関する注意事項

**サブスクリプション** に接続してから約 15 分後に Microsoft Sentinel でハブ データを利用できるようになります。

## <a name="next-steps"></a>次のステップ

このドキュメントでは、Defender for IoT を Microsoft Sentinel に接続する方法について学習しました。 脅威の検出とセキュリティ データ アクセスの詳細については、次の記事を参照してください。

- Microsoft Sentinel の使用方法については、[クイックスタート: Microsoft Sentinel の概要](../../sentinel/get-visibility.md)に関するページを参照してください。
- [IoT セキュリティ データにアクセス](how-to-security-data-access.md)する方法を学習します。
