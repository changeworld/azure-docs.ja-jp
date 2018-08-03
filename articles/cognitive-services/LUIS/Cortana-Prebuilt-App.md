---
title: LUIS からの事前ビルド済み Cortana アプリを使用する | Microsoft Docs
description: Language Understanding Intelligent Services (LUIS) で事前ビルド済みの Cortana パーソナル アシスタント アプリケーションを使用します。
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 12/13/2017
ms.author: v-geberr
ms.openlocfilehash: c7249cb8d8cff29f419412025c69e3b2b76b49d1
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2018
ms.locfileid: "37110659"
---
# <a name="cortana-prebuilt-app"></a>事前ビルド済みの Cortana アプリ

> [!IMPORTANT]
> ビルド済みの Cortana アプリの代わりに、[事前ビルド済みのドメイン](./luis-how-to-use-prebuilt-domains.md)を使用することをお勧めします。 たとえば、**builtin.intent.calendar.create_calendar_entry** の代わりに、事前ビルド済みのドメイン **Calendar** の **Calendar.Add** を使用します。
> 事前ビルド済みのドメインには以下のメリットがあります。 
> * これらは、事前ビルド済みで事前トレーニング済みの意図と、互いに対して正しく機能するよう設計されているエンティティのパッケージを提供します。 事前ビルド済みのドメインは、アプリに直接統合することができます。 たとえば、フィットネス追跡ツールをビルドしようとしている場合は、**Fitness** ドメインを追加し、フィットネス アクティビティを追跡するための意図とエンティティのセット全体を用意できます。これには、体重と食事の計画の追跡、残っている時間や距離の追跡、フィットネス アクティビティ ノートの保存が含まれます。
> * 事前ビルド済みドメインの意図はカスタマイズできます。 たとえば、ホテルのレビューを提供する場合は、ホテル レビューへの要求を認識するために、**Places** ドメインの意図 **Places.GetReviews** をトレーニングしてカスタマイズすることができます。
> * 事前ビルド済みのドメインは拡張できます。 たとえば、事前ビルド済みのドメイン **Places** を、レンストランを検索するボットで使用し、料理の種類を取得するための意図が必要な場合は、意図 **Places.GetCuisine** をビルドし、トレーニングすることができます。

独自のアプリケーションを作成できるだけでなく、LUIS からも、事前ビルド済みアプリとして Microsoft Cortana パーソナル アシスタントの意図とエンティティが提供されています。 一般に公開されているこの LUIS アプリの動作は変更できません。 このアプリケーションの意図とエンティティは、編集したり、その他の LUIS アプリに統合したりすることはできません。 クライアント アプリケーションから、この事前ビルド済みアプリケーションと独自の LUIS アプリケーションの両方にアクセスできるようにする場合は、クライアント アプリケーションが両方の LUIS アプリを参照する必要があります。

事前ビルド済みのパーソナル アシスタント アプリは、英語、フランス語、イタリア語、スペイン語、および中国語のカルチャ (ロケール) で使用できます。

## <a name="get-the-endpoint-for-the-cortana-prebuilt-app"></a>事前ビルド済みの Cortana アプリのエンドポイントを取得する

事前ビルド済みの Cortana アプリには、以下のエンドポイントを使用してアクセスできます。 

| Language | エンドポイント|
|--------| ------------------|
| 英語| https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/c413b2ef-382c-45bd-8ff0-f76d60e2a821|
|    中国語| https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/c27c4af7-d44a-436f-a081-841bb834fa29|
|    フランス語| https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/0355ead1-2d08-4955-ab95-e263766e8392|
|    スペイン語| https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/cb2675e5-fbea-4f8b-8951-f071e9fc7b38|
|    イタリア語| https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/30a0fddc-36f4-4488-b022-03de084c1633|


> [!NOTE]
> エンドポイント URL も、[apps - Get personal assistant applications](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c32) API から取得できます。

## <a name="try-out-the-personal-assistant-app"></a>パーソナル アシスタント アプリを試す
エンドポイントを呼び出すには、エンドポイントにエンドポイント キー引数とクエリ文字列を追加できます。 

たとえば、解釈したい発話が "create an appointment for team meeting" である場合、エンドポイント URL にその発話を追加できます。 

```
https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/c413b2ef-382c-45bd-8ff0-f76d60e2a821?subscription-key={YOUR-SUBSCRIPTION-KEY}&q=create an appointment for team meeting
```

この URL を Web ブラウザーに貼り付け、`{YOUR-SUBSCRIPTION-KEY}` フィールドをエンドポイント キーに置き換えることができます。

ブラウザーでは、事前ビルド済みの Cortana アプリが、`builtin.intent.calendar.create_calendar_entry` を意図として、`builtin.calendar.title` をエンティティ型として、`create an appointment for team meeting` を発話として識別していることを確認できます。

![事前ビルド済みの Cortana アプリを使用する](./media/luis-how-to-prebuilt-cortana/luis-prebuilt-cortana-browser.png)

## <a name="next-steps"></a>次の手順
> [!div class="nextstepaction"]
> [事前ビルド済みの Cortana アプリのリファレンス](./luis-reference-cortana-prebuilt.md)

