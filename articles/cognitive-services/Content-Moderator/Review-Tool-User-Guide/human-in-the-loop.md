---
title: レビュー ツールの概念の説明 - Content Moderator
titleSuffix: Azure Cognitive Services
description: AI と目視レビューを組み合わせたモデレート作業を調整する Web サイトである Content Moderator レビュー ツールについて説明します。
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.date: 03/15/2019
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.author: sajagtap
ms.openlocfilehash: 7f20b9c824045ac2f8c13df3ed8f776195de611a
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/26/2019
ms.locfileid: "68564518"
---
# <a name="content-moderator-review-tool"></a>Content Moderator レビュー ツール

Azure Content Moderator は機械学習によるコンテンツ モデレーションと目視レビューを組み合わせたサービスを提供し、[レビュー ツール](https://contentmoderator.cognitive.microsoft.com) Web サイトはこれらのサービスへの詳細なアクセスを実現する、ユーザー フレンドリなフロント エンドです。

![ブラウザーでのレビュー ツールのダッシュボード](./images/0-dashboard.png)

## <a name="what-it-does"></a>実行内容

[レビュー ツール](https://contentmoderator.cognitive.microsoft.com)では、マシンによるモデレーション API と組み合わせて使用した場合に、コンテンツ モデレーション プロセス内で次のタスクを実行できます。

- 1 つのツール セットを使用して、複数の形式のコンテンツ (テキスト、画像、および動画) をモデレートする。
- モデレーション API の結果が出力されたときに目視[レビュー](../review-api.md#reviews)が作成されるよう自動化する。
- コンテンツ カテゴリまたはエクスペリエンス レベルによって編成された複数のレビュー チームにコンテンツ レビューを割り当てまたはエスカレートする。
- 既定またはカスタムのロジック フィルター ([ワークフロー](../review-api.md#workflows)) を使用することで、コードを記述せずに、コンテンツを並べ替えたり、追跡したりする。
- [コネクタ](./configure.md#connectors)を使用することで、Content Moderator API シリーズに加えて、Microsoft PhotoDNA、Text Analytics、および Face の各 API を利用してコンテンツを処理する。
- 任意の API またはビジネス プロセスのワークフローを作成するための独自のコネクタを作成する。
- コンテンツ モデレーション プロセスの主要なパフォーマンス メトリックを取得します。

## <a name="review-tool-dashboard"></a>レビュー ツールのダッシュボード

**[ダッシュボード]** タブでは、ツール内で実行されたコンテンツ レビューの主要なメトリックを確認できます。 画像、テキスト、および動画コンテンツのレビューの合計数、完了した数、および保留中の数が表示されます。 レビューを完了したユーザーとチームの内訳のほか、適用されているモデレート タグを確認することもできます。

![ダッシュボードの表示](images/0-dashboard.png)

## <a name="review-tool-credentials"></a>レビュー ツールの資格情報

[レビュー ツール](https://contentmoderator.cognitive.microsoft.com)にサインアップすると、ご使用のアカウントの Azure リージョンの選択を求められます。 これは、[レビュー ツール](https://contentmoderator.cognitive.microsoft.com)により、Azure Content Moderator サービスの無料試用版キーが生成されるためです。REST 呼び出しまたはクライアント SDK からいずれかのサービスにアクセスする場合に、このキーが必要になります。 ご自分のキーと API エンドポイント URL を確認するには、 **[設定]**  >  **[資格情報]** を選択します。

![Content Moderator 資格情報](images/settings-6-credentials.png)

## <a name="next-steps"></a>次の手順

レビュー ツールのリソースにアクセスしたり、設定を変更したりする方法については、「[レビュー ツールの構成](./configure.md)」を参照してください。