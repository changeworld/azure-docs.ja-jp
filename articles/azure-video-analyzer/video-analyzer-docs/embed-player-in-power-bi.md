---
title: Power BI でのプレーヤー ウィジェットの埋め込み - Azure Video Analyzer
description: Azure Video Analyzer を使用して、継続的なビデオ記録やイベントベースの記録を行うことができます。 この記事では、Microsoft Power BI にビデオを埋め込むことで、ユーザーに合わせてカスタマイズ可能な UI を提供する方法について説明します。
ms.service: azure-video-analyzer
ms.topic: how-to
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: bb367735097200da273ab908d4a3205d62798d5b
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131560209"
---
# <a name="embed-player-widget-in-power-bi"></a>Power BI にプレーヤー ウィジェットを埋め込む


Azure Video Analyzer を使用すると、ビデオと関連する推論メタデータを Video Analyzer クラウド リソースに[記録](detect-motion-record-video-clips-cloud.md)できます。 Video Analyzer には、[プレーヤー ウィジェット](player-widget.md)が用意されています。この簡単に埋め込むことができるウィジェットを利用すると、クライアント アプリはビデオと推論メタデータを再生できます。

ダッシュボードは、ビジネスを監視し、最も重要なすべてのメトリックを一目で監視する方法です。 Power BI ダッシュボードは、IoT Hub のテレメトリを含む複数のデータ ソースとビデオを組み合わせる強力なツールです。 このチュートリアルでは、[Microsoft Power BI](https://powerbi.microsoft.com/) Web サービスを使用して 1 つまたは複数のプレーヤー ウィジェットをダッシュボードに追加する方法について説明します。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/power-bi/embed-block-diagram.svg" alt-text="Microsoft Power BI に Azure Video Analyzer player ウィジェットを埋め込むためのブロック図。":::

## <a name="suggested-pre-reading"></a>先に読んでおくことが推奨される記事

- Azure Video Analyzer [プレーヤー ウィジェット](player-widget.md)
- [Power BI ダッシュボード](/power-bi/create-reports/service-dashboards)の概要

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 まだお持ちでない場合は、[無料のアカウントを作成してください](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- Complete either [モーションの検出、ビデオの録画](detect-motion-record-video-clips-cloud.md)または[継続的なビデオ記録](continuous-video-recording.md)のいずれかを実行します。ビデオ シンクを使用したパイプラインが必要です。

  > [!NOTE]
  > 続行するには、Video Analyzer アカウントに少なくとも 1 つのビデオが記録されている必要があります。 ビデオの一覧を確認します。そのためには、Azure Video Analyzer アカウント > ビデオ > Video Analyzer セクションの順にログインします。

- [Power BI](https://powerbi.microsoft.com/) アカウント。

## <a name="create-a-token"></a>トークンを作成する

1. 手順に従って[トークンを作成](access-policies.md#creating-a-token)します。
2. _発行者、対象ユーザー、キーの種類、アルゴリズム、キー Id、RSA キーモジュラス、RSA キー指数、トークン_ に対して生成された値を必ず保存してください。 これらの値は、以下のアクセス ポリシーを作成するときに必要になります。

## <a name="get-embed-code-for-player-widget"></a>プレーヤー ウィジェットの埋め込みコードを取得する

1. ご自分の資格情報を使用して [Azure portal](https://portal.azure.com/) にサインインします。 前提条件を完了するために使用する Video Analyzer アカウントを見つけて、[Video Analyzer アカウント] ウィンドウを開きます。
2. 手順に従って[アクセス ポリシーを作成](access-policies.md#creating-an-access-policy)します。
3. **[Video Analyzer]** セクションで **[ビデオ]** を選択します。
4. 一覧から任意のビデオを選択します。
5. **[ウィジェット]** 設定をクリックします。 右側にウィンドウ **[Use widget in your application](アプリケーションでウィジェットを使用する)** が開きます。 **[オプション 2 - HTML を使用する]** まで下にスクロールし、コードをコピーし、テキスト エディターに貼り付けます。 [閉じる] ボタンをクリックします。

   :::image type="content" source="./media/power-bi/widget-code.png" alt-text="コピー ウィジェット HTML コードを AVA ポータルからコピーし、後で使用するために保存するスクリーンショット。":::

6. 手順 5 でコピーした HTML コードを編集して、次の値を置き換えます
   - トークン **AVA-API-JWT-TOKEN** - 「トークンを作成する」の手順で保存したトークンの値に置き換えます。 角かっこを必ず削除してください。
   - 省略可能 – このコードで他の UI 変更を行うことができます。たとえば、「プレーヤーウィジェットの例」から「連続ビデオ記録」にヘッダーを変更します。

## <a name="add-widget-in-power-bi-dashboard"></a>Power BI ダッシュボードでのウィジェットの追加

1. ブラウザーで [Power BI サービス](http://app.powerbi.com/)を開きます。 ナビゲーション ウィンドウで、で、 **[マイ ワークスペース]** を選択します

   :::image type="content" source="./media/power-bi/powerbi-ws.png" alt-text="Power BI ワークスペースのホームページのスクリーンショット。":::

2. **[新規]**  >  **[ダッシュボード]** の順にクリックして新しいダッシュボードを作成するか、既存のダッシュボードを開きます。 **[編集]** ドロップダウン矢印を選択してから **[タイルの追加]** を選択します。 **[Web コンテンツ]**  >  **[次へ]** の順に選択します。
3. **[Web コンテンツ タイルの追加]** で、前のセクションの **埋め込みコード** を入力します。 **[適用]** をクリックします。

   :::image type="content" source="./media/power-bi/embed-code.png" alt-text="Power BI ダッシュボード タイルに html コードを埋め込むスクリーンショット。":::

4. ビデオでダッシュボードにピン留めされたプレーヤー ウィジェットが表示されます。

   :::image type="content" source="./media/power-bi/one-player.png" alt-text="1 つのビデオ プレーヤー ウィジェットが追加されたスクリーンショット。":::

5. Azure Video Analyzer の [ビデオ] セクションからさらにビデオを追加するには、このセクションの手順に従います。

> [!NOTE]
> 同じ Video Analyzer アカウントから複数のビデオを追加するには、1 セットのアクセス ポリシーとトークンで十分です。

1 つの Power BI ダッシュボードにピン留めされた複数のビデオのサンプルを次に示します。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/power-bi/two-players.png" alt-text="例として追加された 2 つのビデオ プレーヤー ウィジェットのスクリーンショット。":::

## <a name="next-steps"></a>次の手順

- [Power BI での AI 推論イベントのリアルタイム視覚化](visualize-ai-events-power-bi.md)
- [ウィジェット API](https://github.com/Azure/video-analyzer/tree/main/widgets) の詳細を確認する
