---
title: Azure Percept DK のモデル推論のテレメトリを表示する
description: Azure Percept DK のビジョン モデル推論のテレメトリを Azure IoT Explorer で表示する方法について説明します。
author: NabilaBabar
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/17/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 0f8b56aef3a3e141c80b09e97101f1d78a2c761b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131006262"
---
# <a name="view-your-azure-percept-dks-model-inference-telemetry"></a>Azure Percept DK のモデル推論のテレメトリを表示する

Azure Percept DK のビジョン モデル推論のテレメトリを [Azure IoT Explorer](https://github.com/Azure/azure-iot-explorer/releases) で表示するには、このガイドに従ってください。

## <a name="prerequisites"></a>前提条件

- Azure Percept DK (開発キット)
- [Azure サブスクリプション](https://azure.microsoft.com/free/)
- [Azure Percept DK セットアップ エクスペリエンス](./quickstart-percept-dk-set-up.md): 開発キットを Wi-Fi ネットワークに接続し、IoT ハブを作成して、開発キットを IoT ハブに接続済みであること
- [ビジョン AI モデルが Azure Percept DK にデプロイされていること](./how-to-deploy-model.md)

## <a name="view-telemetry"></a>利用統計情報データを表示する

1. 開発キットの電源をオンにします。

1. [Azure IoT Explorer](https://github.com/Azure/azure-iot-explorer/releases) をダウンロードしてインストールします。 Windows ユーザーの場合は、.msi ファイルを選択します。

    :::image type="content" source="./media/how-to-view-telemetry/azure-iot-explorer-download.png" alt-text="Azure IoT Explorer のダウンロード画面。":::

1. IoT ハブを Azure IoT Explorer に接続します。

    1. [Azure ポータル](https://portal.azure.com)にアクセスします。

    1. **[すべてのリソース]** を選択します。

        :::image type="content" source="./media/how-to-view-telemetry/azure-portal.png" alt-text="Azure portal ホームページ。":::

    1. Azure Percept DK が接続されている IoT ハブを選択します。

        :::image type="content" source="./media/how-to-view-telemetry/iot-hub.png" alt-text="Azure portal の IoT ハブ一覧。":::

    1. IoT ハブ ページの左側で、 **[共有アクセス ポリシー]** を選択します。

        :::image type="content" source="./media/how-to-view-telemetry/shared-access-policies.png" alt-text="共有アクセスポリシーを示す IoT ハブ ページ。":::

    1. **[iothubowner]** をクリックします。

        :::image type="content" source="./media/how-to-view-telemetry/iothubowner.png" alt-text="iothubowner が強調表示されている [共有アクセス ポリシー] 画面。":::

    1. **[接続文字列 - プライマリ キー]** の横にある青色のコピー アイコンをクリックします。

        :::image type="content" source="./media/how-to-view-telemetry/connection-string.png" alt-text="接続文字列のコピー ボタンが強調表示されている [iothubowner] ウィンドウ。":::

    1. Azure IoT Explorer を開き、 **[+ 接続の追加]** をクリックします。

    1. **[接続文字列の追加]** ウィンドウの **[接続文字列]** ボックスに接続文字列を貼り付けて、 **[保存]** をクリックします。

        :::image type="content" source="./media/how-to-view-telemetry/add-connection-string.png" alt-text="接続文字列の貼り付け先ボックスがある [Azure IoT Explorer] ウィンドウ。":::

    1. モデル推論のオブジェクトを Vision SoM の参照先として設定します。

    1. **[テレメトリ]** を選択します。

    1. **[開始]** をクリックすると、デバイスからのテレメトリ イベントが表示されます。

## <a name="next-steps"></a>次のステップ
[Azure Percept DK ビデオ ストリーム](./how-to-view-video-stream.md)を表示する方法を学習します。
