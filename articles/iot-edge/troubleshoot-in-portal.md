---
title: Azure portal からのトラブルシューティング - Azure IoT Edge | Microsoft Docs
description: IoT Edge のデバイスとモジュールを監視するには、Azure portal でトラブルシューティング ページを使用します
author: kgremban
ms.author: kgremban
ms.date: 05/26/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: e0761262463d69fe80f7dc15ed1a5944dcc235bb
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121726533"
---
# <a name="troubleshoot-iot-edge-devices-from-the-azure-portal"></a>Azure portal から IoT Edge デバイスのトラブルシューティングを行う

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

IoT Edge では、Azure portal でモジュールの監視とトラブルシューティングを効率的に行う方法が提供されます。 トラブルシューティング ページは、IoT Edge エージェントのダイレクト メソッドのラッパーであるため、デプロイされたモジュールからログを簡単に取得したり、リモートで再起動したりできます。

## <a name="prerequisites"></a>前提条件

ポータルでこのトラブルシューティングのすべての機能を使用するには、長期サポート ブランチを使用している場合は IoT Edge バージョン 1.1.3 以降、最新の安定したブランチを使用している場合はバージョン 1.2.1 以降が必要です。 これらのバージョンで、IoT Edge ホスト コンポーネントと edgeAgent モジュールの両方が必要です。

## <a name="access-the-troubleshooting-page"></a>トラブルシューティング ページにアクセスする

ポータルのトラブルシューティング ページには、IoT Edge デバイスの詳細ページまたは IoT Edge モジュールの詳細ページからアクセスできます。

1. [Azure Portal](https://portal.azure.com) にサインインし、IoT Hub に移動します。

1. 左側のウィンドウで、メニューの **[IoT Edge]** を選択します。

1. デバイスの一覧から、監視する IoT Edge デバイスを選択します。

1. デバイスの詳細ページから、メニューの **[トラブルシューティング]** を選択するか、検査する特定のモジュールのランタイム状態を選択できます。

   ![デバイスの詳細ページで、[トラブルシューティング] またはモジュールのランタイム状態を選択します](./media/troubleshoot-in-portal/troubleshoot-from-device-details.png)

1. デバイスの詳細ページからモジュールの名前を選択して、モジュールの詳細ページを開くこともできます。 そこから、メニューの **[トラブルシューティング]** を選択できます。

   ![モジュールの詳細ページから [トラブルシューティング] を選択します](./media/troubleshoot-in-portal/troubleshoot-from-module-details.png)

## <a name="view-module-logs-in-the-portal"></a>ポータルでモジュール ログを表示する

**[トラブルシューティング]** ページでは、IoT Edge デバイス上で実行中の任意のモジュールのログを表示およびダウンロードできます。

このページのログの上限は 1,500 行で、それより長いログは切り捨てられます。 ログが大きすぎる場合、モジュール ログの取得は失敗します。 その場合は、時間範囲フィルターを変更して取得するデータを少なくするか、ダイレクト メソッドを使用して [IoT Edge デプロイからログを取得](how-to-retrieve-iot-edge-logs.md)し、より大きなログ ファイルを収集します。

ドロップダウン メニューを使用して、検査するモジュールを選択します。

![ドロップダウン メニューからモジュールを選択します](./media/troubleshoot-in-portal/select-module.png)

既定では、このページには最後の 15 分間のログが表示されます。 **[時間の範囲]** フィルターを選択すると、さまざまなログが表示されます。 スライダーを使用して過去 60 分以内の時間枠を選択するか、 **[代わりに時間を入力する]** をオンにして特定の期間を選択します。

![時間範囲を選択します](./media/troubleshoot-in-portal/select-time-range.png)

検査する時間範囲内でトラブルシューティングするモジュールのログを取得したら、 **[検索]** フィルターを使用してログから特定の行を取得できます。 警告またはエラーをフィルター処理するか、検索する特定の用語または語句を指定できます。 **[検索]** 機能では、より複雑な検索を行うために、プレーンテキスト検索または [.NET の正規表現](/dotnet/standard/base-types/regular-expression-language-quick-reference)がサポートされています。

モジュール ログはテキスト ファイルとしてダウンロードできます。 ダウンロードするログ ファイルには、ログに適用したアクティブなフィルターが反映されます。

>[!TIP]
>ポータルからの要求に応じてログを収集すると、デバイスの CPU 使用率が一時的に急増します。 これは想定の動作であり、タスクの完了後は使用率が安定します。

## <a name="restart-modules"></a>モジュールを再起動する

**[トラブルシューティング]** ページには、モジュールを再起動する機能が含まれています。 このオプションを選択すると、選択したモジュールを再起動するコマンドが IoT Edge エージェントに送信されます。 モジュールを再起動しても、再起動前のログを取得する機能には影響しません。

![トラブルシューティング ページからモジュールを再起動します](./media/troubleshoot-in-portal/restart-module.png)

## <a name="next-steps"></a>次のステップ

[IoT Edge デバイスのトラブルシューティング](troubleshoot.md)に関するその他のヒントと、[一般的な問題と解決策](troubleshoot-common-errors.md)に関するページを参照してください。 

その他に質問がある場合は、[サポート リクエスト](https://portal.azure.com/#create/Microsoft.Support)を作成して対応を要請してください。
