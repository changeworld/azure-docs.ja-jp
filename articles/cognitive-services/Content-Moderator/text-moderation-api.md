---
title: Azure Content Moderator - テキストのモデレート | Microsoft Docs
description: 混入が考えられる不要なテキスト、PII、および条件のカスタム一覧に対して、テキストのモデレートを使用します。
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/30/2018
ms.author: sajagtap
ms.openlocfilehash: 5783a7a06d75a409969abad011de3bbd31dec292
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35374528"
---
# <a name="text-moderation"></a>テキストのモデレート

Content Moderator のコンピューター支援型テキストのモデレートと [human-in-the-loop (HITL) ](Review-Tool-User-Guide/human-in-the-loop.md)機能を使用して、テキスト コンテンツをモデレートします。

企業ではテキストのモデレート サービスを使用し、ポリシーとしきい値に基づいてコンテンツのブロック、承認、または見直しのいずれかを行います。 テキストのモデレート サービスは、パートナー、従業員、およびコンシューマーによるテキスト コンテンツの生成を伴う環境で、人によるモデレートを強化するために使用できます。 これらには、チャット ルーム、ディスカッション掲示板、チャットボット、e コマース カタログ、ドキュメントなどが含まれます。 

API は、不適切な表現の入力テキスト (最大 1024 文字) のスキャン、混入が考えられる不要なテキスト (プレビュー) の分類、テキストの自動修正、潜在的に個人を特定できる情報 (PII) の検出を行います。 また、カスタム用語一覧に対するマッチングも行います。 自動修正機能は、意図的な単語のスペルミスを検出するのに役立ちます。 コンテンツが処理されると、サービスは詳細な応答を返します。 応答を使用して、レビュー ツールで人によるレビューを作成したり、レビューの取り下げなどを行ったりします。

サービスの応答には次の情報が含まれています。

- 不適切な表現: 複数の言語における、組み込みの不適切な表現一覧との、用語ベースでのマッチング
- 分類: 3 つのカテゴリへのコンピューター支援型の分類
- 個人を特定できる情報 (PII)
- 自動修正済みテキスト
- 元のテキスト
- 言語

## <a name="profanity"></a>不適切な表現

API が、いずれかの[サポートされる言語](Text-Moderation-API-Languages.md)で何らかの不適切な用語を検出した場合、それらの用語が応答に含められます。 また、応答には、元のテキストの場所 (`Index`) も含まれます。 以下のサンプル JSON にある `ListId` は、[カスタム用語一覧](try-terms-list-api.md)で検出された用語を参照しています (利用可能な場合)。

    "Terms": [
    {
        "Index": 118,
        "OriginalIndex": 118,
        "ListId": 0,
        "Term": "crap"
    }

> [!NOTE]
> **language** パラメーターについては、`eng` を割り当てるか、パラメーターを空のままにして、コンピューター支援による**分類**応答を表示します (プレビュー機能)。 **この機能でサポートされているのは英語のみです**。
>
> **不適切用語**の検出については、この記事の一覧に示したサポート対象言語の [ISO 639-3 コード](http://www-01.sil.org/iso639-3/codes.asp)を使用するか、空のままにします。

## <a name="classification"></a>分類

Content Moderator のコンピューター支援型**テキスト分類機能**では**英語のみ**をサポートし、潜在的に不要なコンテンツを検出します。 フラグが付いたコンテンツは、コンテキストによっては不適切と見なされる可能性があります。 これは、各カテゴリに属する可能性を伝えるだけでなく、人によるコンテンツのレビューを推奨している可能性もあります。 この機能では、トレーニング済みのモデルを使用して、侮辱、軽蔑、差別と受け取られる可能性のある言葉を識別します。 これには、俗語、省略語、不快な言葉、およびレビュー備えて意図的にスペルミスを含めた用語が含まれます。 

JSON の次の抽出箇所は、出力例を示しています。

> [!NOTE]
> コンピューター支援型の 'Classification' 機能はプレビューです。

    "Classification": {
        "ReviewRecommended": true,
        "Category1": {
            "Score": 1.5113095059859916E-06
            },
        "Category2": {
            "Score": 0.12747249007225037
            },
        "Category3": {
            "Score": 0.98799997568130493
        }
    }

### <a name="explanation"></a>説明

- `Category1` は、一定の状況において性的に露骨、または成人向けであると考えられる言葉が存在する可能性を表しています。
- `Category2` は、一定の状況において性的な連想を起こさせる、または成人向けと考えられる言葉が存在する可能性を表しています。
- `Category3` は、一定の状況において不快であると考えられる言葉が存在する可能性を表しています。
- `Score` は 0 から 1 の間です。 このモデルでは、スコアが高いほど、そのカテゴリに該当する可能性が高いと予測しています。 このプレビューは、人がコーディングした結果ではなく、統計モデルに依存しています。 独自のコンテンツを使用してテストを行い、実際の要件に合うように各カテゴリをどのように設定するかを決めることをお勧めします。
- `ReviewRecommended` は、内部スコアのしきい値に応じて true または false のどちらかになります。 ユーザーは、この値を使用するか、独自のコンテンツ ポリシーに基づいてカスタムしきい値を決めるかを見極める必要があります。

## <a name="personally-identifiable-information-pii"></a>個人を特定できる情報 (PII)

PII 機能は、この情報の潜在的な存在を検出します。

- 電子メール アドレス
- 米国の住所
- IP アドレス
- 米国の電話番号
- 英国の電話番号
- 社会保障番号 (SSN)

次の例は、サンプルの応答を示しています。

    "PII": {
        "Email": [{
            "Detected": "abcdef@abcd.com",
            "SubType": "Regular",
            "Text": "abcdef@abcd.com",
            "Index": 32
            }],
        "IPA": [{
            "SubType": "IPV4",
            "Text": "255.255.255.255",
            "Index": 72
            }],
        "Phone": [{
            "CountryCode": "US",
            "Text": "6657789887",
            "Index": 56
            }, {
            "CountryCode": "US",
            "Text": "870 608 4000",
            "Index": 212
            }, {
            "CountryCode": "UK",
            "Text": "+44 870 608 4000",
            "Index": 208
            }, {
            "CountryCode": "UK",
            "Text": "0344 800 2400",
            "Index": 228
            }, {
            "CountryCode": "UK",
            "Text": "0800 820 3300",
            "Index": 245
            }],
        "Address": [{
            "Text": "1 Microsoft Way, Redmond, WA 98052",
            "Index": 89
            }],
        "SSN": [{
            "Text": "999999999",
            "Index": 56
            }, {
            "Text": "999-99-9999",
            "Index": 267
            }]
        }

## <a name="auto-correction"></a>自動修正

次のような入力テキストについて考えます ('lzay' および 'f0x' は意図的です)。

    The qu!ck brown f0x jumps over the lzay dog.

自動修正を要求した場合、応答には修正されたバージョンのテキストが含まれます。

    The quick brown fox jumps over the lazy dog.

## <a name="creating-and-managing-your-custom-lists-of-terms"></a>カスタム用語一覧の作成と管理

既定のグローバル用語一覧は、ほとんどのケースで有効に機能しますが、ビジネス ニーズに固有の用語に対してスクリーニングしたい場合があります。 たとえば、ユーザーの投稿から競合するブランドの名前を検出したい場合があります。 許可されているテキスト コンテンツのしきい値は、既定の一覧とは異なる場合があります。

> [!NOTE]
> 上限は**用語の一覧が 5 つ**で、各一覧では**用語が 10,000 個を超えてはいけません**。
>

次の例では、一致するリスト ID を示しています。

    "Terms": [
    {
        "Index": 118,
        "OriginalIndex": 118,
        "ListId": 231.
        "Term": "crap"
    }

Content Moderator は、[用語一覧の API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f) にカスタム用語一覧を管理する操作を提供します。 [用語一覧の API コンソール](try-terms-list-api.md) を起動して、REST API コード サンプルを使用してください。 また、Visual Studio および C# に精通している場合は、[用語一覧に関する .NET のクイックスタート](term-lists-quickstart-dotnet.md)を確認してください。

## <a name="next-steps"></a>次の手順

[用語一覧の API コンソール](try-text-api.md)を試験運用して、REST API コード サンプルを使用してください。 また、Visual Studio および C# に精通している場合は、[テキストのモデレートの .NET クイックスタート](text-moderation-quickstart-dotnet.md)を確認してください。
