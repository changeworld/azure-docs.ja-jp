---
title: 音声サービスを使用した音声の翻訳 | Microsoft Docs
description: 音声サービスで音声翻訳を使用する方法について説明します。
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/16/2018
ms.author: v-jerkin
ms.openlocfilehash: 6acfcf0ae8ab4c63e6cc943f93da6b947f3d118c
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2018
ms.locfileid: "39071098"
---
# <a name="translate-speech-using-speech-service"></a>音声サービスを使用して音声を翻訳する

[Speech SDK](speech-sdk.md) は、アプリケーション内で音声翻訳を使用する最も簡単な方法です。 この SDK は、このサービスのすべての機能を提供します。 音声翻訳を実行するための基本的なプロセスには、次の手順が含まれます。

1. 音声ファクトリを作成し、音声サービス サブスクリプション キーと[リージョン](regions.md)または承認トークンを指定します。 また、この時点で、ソースおよびターゲットの翻訳言語を構成し、テキストと音声のどちらで出力するかを指定します。

2. ファクトリから認識エンジンを取得します。 翻訳の場合は、翻訳認識エンジンを選択します  (他の認識エンジンは "*音声テキスト変換*" 用です)。使用するオーディオ ソースに基づいて、翻訳認識エンジンにはさまざまな種類があります。

4. 必要に応じて、非同期操作のイベントを関連付けます。 暫定的結果および最終的結果が得られると、認識エンジンによってイベント ハンドラーが呼び出されます。 それ以外の場合、アプリケーションは、最終的な翻訳結果を受け取ります。

5. 認識と翻訳を開始します。

# <a name="sdk-samples"></a>SDK のサンプル

最新のサンプル セットについては、[Cognitive Services Speech SDK のサンプル GitHub リポジトリ](https://aka.ms/csspeech/samples)を参照してください。

# <a name="next-steps"></a>次の手順

- [Speech 試用版サブスクリプションを取得する](https://azure.microsoft.com/try/cognitive-services/)
- [C# で音声を認識する](quickstart-csharp-dotnet-windows.md)
