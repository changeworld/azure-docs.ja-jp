---
title: ベスト プラクティス - QnA Maker - Azure Cognitive Services | Microsoft Docs
description: 以下のベスト プラクティスを利用してナレッジ ベースを改善し、アプリケーション/チャット ボットのエンド ユーザーにより良い情報を提供します。
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: 7a85ebbc3892a90e98e73a73425c1f8ec1de0b35
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/01/2018
ms.locfileid: "35378690"
---
# <a name="best-practices"></a>ベスト プラクティス
[ナレッジ ベース開発ライフサイクル](../Concepts/development-lifecycle-knowledge-base.md)では、ナレッジ ベースを始まりから終わりまで管理する方法を紹介しています。 以下のベスト プラクティスを利用してナレッジ ベースを改善し、アプリケーション/チャット ボットのエンド ユーザーにより良い情報を提供します。

## <a name="extraction"></a>抽出
QnA Maker は継続的に、コンテンツから QnA を抽出するアルゴリズムを改善し、サポートされるファイルと HTML ページ形式を拡大しています。 抽出元になるドキュメントの種類に基づく抽出については、[ガイドライン](../Concepts/data-sources-supported.md)に従ってください。 

一般的に、FAQ ページは独立させ、他の情報と組み合わせないでください。 製品マニュアルにはわかりやすい見出しを付けてください。索引ページを付けることもお勧めします。 

## <a name="rankingmatching"></a>優先度付け/照合
QnA Maker でサポートされている優先度付け機能を効果的に活用してください。 それにより、ユーザーからの問い合わせに適切な回答が与えられる可能性が上がります。

### <a name="add-alternate-questions"></a>代わりの質問を追加する
[代わりの質問](../How-To/edit-knowledge-base.md)を用意すること (別の表現で言い換えること) で、ユーザーからの問い合わせに一致する可能性が向上します。 代わりの質問は、同じ質問にさまざまな表現が使われるときに役立ちます。 たとえば、*"Is parking available?"* (駐車できますか) を *"Do you have car park?"* (駐車場はありますか) にするなど、文章の構造を変えます。あるいは、*"Hi"* を *"Yo"* や *"Hey there!"* にするなど、言葉遣いを変えたり、俗語を使ったりします。

### <a name="use-metadata-filters"></a>メタデータ フィルターを使用する
[メタデータ](../How-To/edit-knowledge-base.md)を利用すれば、フィルターに基づいてユーザーからの問い合わせの結果を絞り込むことができます。 レッジ ベースの回答は、問い合わせが同じであっても、メタデータ タグに基づいて異なる場合があります。 たとえば、*"where is parking located"* (駐車場はどこですか) という問いに対し、レストランの支店の場所が違えば、つまり、*Location: Seattle* (所在地: シアトル) のときと、*Location: Redmond* (所在地: レドモンド) のときで答えが変わります。

### <a name="use-synonyms"></a>同義語を使用する
英語では同義語をある程度サポートしていますが、さまざま言い方があるキーワードに同義語を追加するには、[言葉の変更](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fd)機能を利用します。たとえば、*buy* ->  に *purchase* を追加したり、*netbanking* ->  に *net banking* を追加したりします。 同義語は QnA Maker サービスレベルで追加し、サービス内の全ナレッジ ベースで共有してください。

### <a name="use-distinct-words-to-differentiate-questions"></a>質問を区別する目的で明確な言葉を使う
QnA Maker の照合/優先度付けアルゴリズムはユーザーからの問い合わせとナレッジ ベースに登録されている質問を一致させるアルゴリズムですが、このアルゴリズムはそれぞれの質問が異なるニーズに対処する場合に最も効果を発揮します。 複数の質問で同じ言葉が繰り返し使われていると、その言葉が含まれる問い合わせが届いたとき、正しい回答が選ばれる可能性が下がります。

## <a name="collaborate"></a>協力
QnA Maker では、ユーザーはナレッジ ベースに[協力](../How-to/collaborate-knowledge-base.md)できます。 ーザーがナレッジ ベースにアクセスするには、Azure QnA Maker リソース グループにアクセスする許可が必要です。 ナレッジ ベースの編集と保守管理を外注する組織もありますが、その場合でも Azure リソースへのアクセスを保護できます。 この編集者/承認者モデルは、異なるサブスクリプションで同じ [QnA Maker サービス](../How-to/set-up-qnamaker-service-azure.md)を 2 つ設定し、その 1 つを編集/テスト サイクルに指定することで実現できます。 テストが完了したら、[インポート/エクスポート](../Tutorials/migrate-knowledge-base.md) プロセスを利用し、承認者の QnA Maker サービスにナレッジ ベースコンテンツを転送できます。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [ナレッジ ベースを編集する](../How-to/edit-knowledge-base.md)

