---
title: Azure portal を使用して Azure Video Analyzer 用のダイレクト メソッドを呼び出す方法
description: この記事では、Azure portal を使用して Azure Video Analyzer 用のダイレクト メソッドを呼び出す方法の概要を示します。
ms.topic: how-to
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: e6b2db32db398bfbc5c5431c49db1d2030f5a99e
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131564045"
---
# <a name="use-azure-portal-to-invoke-direct-methods-for-azure-video-analyzer"></a>Azure portal を使用して Azure Video Analyzer 用のダイレクト メソッドを呼び出す

[!INCLUDE [header](includes/edge-env.md)]

IoT Hub には、クラウドからエッジ デバイス上の[ダイレクト メソッド](../../../iot-hub/iot-hub-devguide-direct-methods.md#method-invocation-for-iot-edge-modules)を呼び出す機能が備わっています。 Azure Video Analyzer (Video Analyzer) モジュールでは、ライブ ビデオを分析するためにさまざまなパイプラインを定義し、デプロイし、アクティブにするために使用できる、いくつかの[ダイレクト メソッド](./direct-methods.md)が公開されています。

この記事では、Azure portal を介して Video Analyzer モジュール上でダイレクト メソッド呼び出しを呼び出す方法について学習します。

## <a name="prerequisites"></a>前提条件

* 次に記載されているいずれかの方法を使用して、エッジ デバイスで Video Analyzer モジュールを実行している: [クイック スタート: Video Analyzer](get-started-detect-motion-emit-events.md)、または[ポータル](./deploy-iot-edge-device.md)の使用。
* [Video Analyzer](../overview.md) と[パイプラインの概念](../pipeline.md)を理解している。

## <a name="invoking-direct-methods-via-azure-portal"></a>Azure portal を使用したダイレクト メソッドの呼び出し

Video Analyzer モジュールによって公開されている各[ダイレクト メソッド](./direct-methods.md)は、Azure portal を介して呼び出すことができます。 以下の手順は、1 つのダイレクト メソッドの詳細について示しています。 同様の手順を使用して、他のダイレクト メソッドを呼び出すことができます。 しかし、各ダイレクト メソッドには特定のペイロードが必要です。

`livePipelineList` メソッド呼び出しを使用して、Video Analyzer モジュールに現在デプロイされているすべてのパイプライン トポロジの一覧を取得します。 このダイレクト メソッドを呼び出すには、次の手順を使用します。

1. Azure portal にログインします
1. ポータル ホーム ページで関連するリソース グループを見つけて、IoT Hub を見つけます。または、IoT Hub がわかっている場合は、それを選択します。
    ![ポータル ホーム ページのリソース グループ](./media/use-azure-portal-to-invoke-direct-methods/portal-rg-home.png)
1. [IoT Hub] ページで [デバイスの自動管理] の [IoT Edge] を選択して、さまざまなデバイス ID を一覧表示します。 関連するデバイス ID を選択して、デバイスで実行されているモジュールの一覧を表示します。
    ![IoT Hub ページ](./media/use-azure-portal-to-invoke-direct-methods/iot-hub-page.png)
1. Video Analyzer モジュールを選択して、その構成ページを表示します。<br><br>
    ![Video Analyzer モジュールを選択して、その構成ページを表示する](./media/use-azure-portal-to-invoke-direct-methods/modules.png)
1. [ダイレクト メソッド] メニュー オプションを選択します。 <br><br>
    ![[ダイレクト メソッド] メニュー オプションをクリックする](./media/use-azure-portal-to-invoke-direct-methods/module-details.png)
    > [!NOTE]
    > 現在のページで確認できるように、[接続文字列] セクションに値を追加する必要がある場合があります。 [設定名] セクションは何も非表示にしたり変更したりする必要はありません。 パブリックにしておいてかまいません。

1. **[メソッド名]** フィールドに「*livePipelineList*」と入力します。
1. 以下の JSON をコピーして **[ペイロード]** フィールドに貼り付けます。
    ```json
    {
    "@apiVersion": "1.1"
    }
    ```
1. ページの上部にある **[メソッドの呼び出し]** ボタンを選択します。<br><br>
    ![[メソッドの呼び出し] ボタン](./media/use-azure-portal-to-invoke-direct-methods/direct-method.png)
1. **[結果]** 領域に、状態が 200 であることを示すメッセージが表示されます。<br><br>
    ![接続タイムアウト](./media/use-azure-portal-to-invoke-direct-methods/connection-timeout.png)

## <a name="responses"></a>Responses

| 条件             | 状態コード | 詳細なエラー コード |
|-----------------------|-------------|---------------------|
| Success               | 200         | 該当なし                 |
| 一般的なユーザー エラー   | 400 番台   |                     |
| 一般的なサーバー エラー | 500 番台   |                     |

## <a name="next-steps"></a>次のステップ

ダイレクト メソッドの詳細については、「[ダイレクト メソッド](./direct-methods.md)」ページで見つけることができます。

> [!NOTE]
> パイプラインによって特定のトポロジがアクティブにされるため、パイプラインをアクティブにする前に適切なトポロジを確実に設定してください。

[クイック スタート: モーションの検出とイベントの生成](detect-motion-emit-events-quickstart.md)に関するページは、実行するダイレクト メソッド呼び出しの正確なシーケンスを理解するのに適した参照先です。
