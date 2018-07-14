---
title: Knowledge Exploration Service API の Web API インターフェイス | Microsoft Docs
description: Web API インターフェイスを使用して、Cognitive Services の Knowledge Exploration Service (KES) API でリッチなセマンティック検索エクスペリエンスを作成できます。
services: cognitive-services
author: bojunehsu
manager: stesp
ms.service: cognitive-services
ms.component: knowledge-exploration
ms.topic: article
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 16c5680eb4f249a5d37e6b90eea92cfff7090eef
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35372957"
---
# <a name="web-api-interface"></a>Web API インターフェイス
Knowledge Exploration Service によって作成されたモデル ファイルは、一連の Web API を使用してホストおよびアクセスできます。  API は、[`host_service`](CommandLine.md#host_service-command) コマンドを使用してローカル コンピューターにホストすることも、[`deploy_service`](CommandLine.md#deploy_service-command) コマンドを使用して Azure クラウド サービスにデプロイすることもできます。  どちらの手法も、次の API エンドポイントを公開します。
* [*interpret*](interpretMethod.md) - 自然言語のクエリ文字列を解釈します。 注釈付きの解釈を返し、ユーザーの入力内容を予測する検索ボックスの高性能な自動補完を実現します。
* [*evaluate*](evaluateMethod.md) - 構造化されたクエリ式の出力を評価して返します。
* [*calchistogram* ](calchistogramMethod.md) - 構造化されたクエリ式によって返されるオブジェクトの属性値のヒストグラムを計算します。

これらの API メソッドを一緒に使用すると、リッチなセマンティック検索エクスペリエンスを作成できます。  自然言語クエリ文字列を指定すると、*interpret* メソッドは、基になる文法とインデックス データに基づいて、構造化されたクエリ式を持つ入力クエリの注釈付きバージョンを提供します。  *evaluate* メソッドは、構造化されたクエリ式を評価し、表示用に一致するインデックス オブジェクトを返します。  *calchistogram* メソッドは、属性値の分布を計算して、フィルター処理および調整を可能にします。

**例**

学術的刊行物のドメインでは、ユーザーが文字列 "latent s" を入力すると、*interpret* メソッドはランク付けされた解釈のセットを提供でき、ユーザーがキーワード "latent semantic analysis"、タイトル "latent structure analysis"、または "latent s" で始まるその他の式を検索できることを示します。  この情報を使用して、ユーザーを目的の検索結果に迅速にガイドできます。

このドメインでは、*evaluate* メソッドを使用して学術インデックスから一致する刊行物のセットを取得でき、*calchistogram* メソッドを使用して一致する刊行物の属性値の分布を計算して、検索結果をフィルター処理および調整するために使用できます。

例を読みやすくするために、REST API 呼び出しには URL エンコードされていない文字 (スペースなど) が含まれていることにご注意ください。 コードでは、適切な URL エンコードを適用する必要があります。
