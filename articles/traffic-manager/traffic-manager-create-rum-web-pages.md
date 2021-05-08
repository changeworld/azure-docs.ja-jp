---
title: Web ページでのリアル ユーザー測定 - Azure Traffic Manager
description: この記事では、Azure Traffic Manager にリアル ユーザー測定を送信するように Web ページを設定する方法について説明します。
services: traffic-manager
documentationcenter: traffic-manager
author: duongau
manager: kumud
ms.service: traffic-manager
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 04/06/2021
ms.author: duau
ms.openlocfilehash: 7ec91945c901f46d7036e8c474f9f5f0714ce65e
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/07/2021
ms.locfileid: "106580368"
---
# <a name="how-to-send-real-user-measurements-to-azure-traffic-manager-using-web-pages"></a>Web ページを使用して Azure Traffic Manager に Real User Measurements を送信する方法

Real User Measurements (RUM) キーを取得し、生成されたコードを Web ページに埋め込むことで、Real User Measurements を Traffic Manager に送信するように Web ページを構成することができます。

## <a name="obtain-a-real-user-measurements-key"></a>Real User Measurements キーの取得

測定値を取得し、クライアント アプリケーションから Traffic Manager に送信すると、その測定値は、**リアル ユーザー測定 (RUM) キー** と呼ばれる一意の文字列を使用して、サービスによって識別されます。 RUM キーを取得するには、Azure portal、REST API、PowerShell、または Azure CLI を使用します。

Azure Portal を使用して RUM キーを取得するには:
1. ブラウザーから Azure Portal にサインインします。 まだアカウントを持っていない場合は、1 か月間の無料試用版にサインアップできます。

1. ポータルの検索バーで、変更の対象となる Traffic Manager プロファイル名を検索し、表示された結果内で Traffic Manager プロファイルを選択します。

1. Traffic Manager プロファイル ページの **[設定]** で **[リアル ユーザー測定]** を選択します。

1. **[キーの生成]** を選択して、新しい RUM キーを作成します。

    :::image type="content" source="./media/traffic-manager-create-rum-web-pages/generate-rum-key.png" alt-text="キーの生成方法のスクリーンショット。"::: 

   **図 1: Real User Measurements キーの生成**

1. ページには、生成された RUM キーと、HTML ページに埋め込む必要がある JavaScript コード スニペットが表示されます。

    :::image type="content" source="./media/traffic-manager-create-rum-web-pages/rum-javascript-code.png" alt-text="生成された JavaScript コードのスクリーンショット。"::: 

    **図 2: Real User Measurements キーと測定 JavaScript**
 
1. **[コピー]** を選択して、JavaScript コードをコピーします。 

> [!IMPORTANT]
> 正常に機能させるには、Real User Measurements 機能用に生成された JavaScript を使用します。 このスクリプトまたは Real User Measurements で使用されるスクリプトを変更すると、予期しないビヘイビアーにつながることがあります。

## <a name="embed-the-code-to-an-html-web-page"></a>HTML Web ページへのコードの埋め込み

RUM キーを取得した後、次の手順は、エンドユーザーがアクセスする HTML ページにこのコピーされた JavaScript を埋め込むことです。 HTML の編集は、多くの方法で行うことができ、さまざまなツールとワークフローを使用できます。 この例では、HTML ページを更新してこのスクリプトを追加する方法を示します。 このガイドを使用し、HTML ソース管理ワークフローに適応できます。

1. テキスト エディターで HTML ページを開きます。

1. 前のセクションでコピーした JavaScript コードを HTML の本文セクションに貼り付けます。 コピーしたコードは 8 と 9 行目にあります。図 3 を参照してください。

    :::image type="content" source="./media/traffic-manager-create-rum-web-pages/real-user-measurement-embed-script.png" alt-text="Web ページに埋め込まれた、生成された JavaScript のスクリーンショット。"::: 

    **図 3: Real User Measurements JavaScript が埋め込まれた単純な HTML**

1. HTML ファイルを保存して、インターネットに接続されている Web サーバーでホストします。

1. Web ブラウザーでこのページを次に表示したときに、参照される JavaScript がダウンロードされ、スクリプトによって測定とレポートの操作が実行されます。

## <a name="next-steps"></a>次のステップ
- [Real User Measurements](traffic-manager-rum-overview.md) について確認する
- [Traffic Manager のしくみ](traffic-manager-overview.md)
- Traffic Manager でサポートされている [トラフィック ルーティング方法](traffic-manager-routing-methods.md) の詳細を確認する。
- [Traffic Manager プロファイルの作成](./quickstart-create-traffic-manager-profile.md)
