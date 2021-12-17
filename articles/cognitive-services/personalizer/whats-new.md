---
title: 新機能 - Personalizer
titleSuffix: Azure Cognitive Services
description: この記事には、Personalizer に関するニュースが含まれています。
author: jeffmend
ms.author: jeffme
ms.manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 05/28/2021
ms.openlocfilehash: 4c4d4151342211712f63d254630cc480dd761235
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2021
ms.locfileid: "122830241"
---
# <a name="whats-new-in-personalizer"></a>Personalizer の新機能

サービス内の新機能について説明します。 これらの項目には、リリース ノート、ビデオ、ブログの投稿、およびその他の種類の情報が含まれる可能性があります。 このページをブックマークして、常にサービスの最新情報を確認してください。

## <a name="release-notes"></a>リリース ノート

### <a name="may-2021----build-conference"></a>2021 年 5 月 - //Build Conference

* 自動最適化 (プレビュー): 時間の経過と共に少ない作業量で継続的に改善するために使用する Personalizer ループを構成できます。 Personalizer は、自動的にオフライン評価を実行し、より優れた機械学習設定を検出して適用します。 詳細については、「[Personalizer の自動最適化 (プレビュー)](concept-auto-optimization.md)」を参照してください。
* マルチスロット Personalizer (プレビュー): 並べて表示されるレイアウト、カルーセル、および (または) サイドラインがある場合、Personalizer は、同じページで製品やコンテンツを推奨する場所ごとに使用するのが簡単です。 現在、Personalizer は優先度付け API のスロットの一覧を取得し、それぞれにアクションを割り当てて、各スロットに対して送信する報酬スコアから学習できるようになりました。 詳細については、「[マルチスロット パーソナル化 (プレビュー)](concept-multi-slot-personalization.md)」を参照してください。
* 現在、Personalizer はより多くの地域で提供されるようになりました。
* コードサンプル (GitHub) とドキュメントを更新しました。 次のリンクを使用して、更新されたサンプルを表示します。
  * [C#/.NET](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/Personalizer)
  * [JavaScript](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/javascript/Personalizer)
  * [Python のサンプル](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/python/Personalizer)

### <a name="july-2020"></a>2020 年 7 月

* 新しいチュートリアル - [チャット ボットでの Personalizer の使用](tutorial-use-personalizer-chat-bot.md)

### <a name="june-2020"></a>2020 年 6 月

* 新しいチュートリアル - [Web アプリでの Personalizer の使用](tutorial-use-personalizer-web-app.md)

### <a name="may-2020---build-conference"></a>2020 年 5 月 - //Build Conference

**パブリック プレビュー** では、次のものを使用できます。

 * 学習動作としての[見習いモード](concept-apprentice-mode.md)。

### <a name="march-2020"></a>2020 年 3 月

* TLS 1.2 は現在、このサービスへのすべての HTTP 要求に適用されるようになりました。 詳細については、[Azure Cognitive Services のセキュリティ](../cognitive-services-security.md)に関するページを参照してください。

### <a name="november-2019---ignite-conference"></a>2019 年 11 月 - Ignite カンファレンス

* Personalizer の一般提供 (GA)
* ライフサイクル全体を対象とした Azure Notebooks [チュートリアル](tutorial-use-azure-notebook-generate-loop-data.md)

### <a name="may-2019---build-conference"></a>2019 年 5 月 - //Build Conference

Build 2019 Conference では、次のプレビュー機能が公開されました。

* [優先度付けと報酬の学習ループ](what-is-personalizer.md)

## <a name="videos"></a>ビデオ

### <a name="2019-build-videos"></a>2019 Build のビデオ

* [Cognitive Services Personalizer を使用して Xbox のような適切なエクスペリエンスとコンテンツを提供する](https://azure.microsoft.com/resources/videos/build-2019-deliver-the-right-experiences-and-content-with-cognitive-services-personalizer/)

## <a name="service-updates"></a>サービスの更新情報

[Cognitive Services に対する Azure 更新プログラムのお知らせ](https://azure.microsoft.com/updates/?product=cognitive-services)

## <a name="next-steps"></a>次のステップ

* [クイック スタート: C# でフィードバック ループを作成する](./quickstart-personalizer-sdk.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp)
* [対話型デモを使用する](https://personalizationdemo.azurewebsites.net/)