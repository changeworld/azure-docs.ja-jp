---
title: イマーシブ リーダーとは
titleSuffix: Azure Cognitive Services
description: イマーシブ リーダーは、学習障碍者を支援したり、新しい読者や言語学習者の読解力を支援したりするように設計されているツールです。
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: overview
ms.date: 01/4/2020
ms.author: metang
ms.custom: cog-serv-seo-aug-2020
keywords: 読者, 言語学習者, 絵の表示, 読解力の向上, コンテンツの読み取り, 翻訳
ms.openlocfilehash: 1911d3ce62434f1dc24966798e9ace259ae072bc
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104801572"
---
# <a name="what-is-immersive-reader"></a>Immersive Reader とは

[イマーシブ リーダー](https://www.onenote.com/learningtools)は、新しい読者、言語学習者、ディスレクシア (失読症) などの学習障碍者の読解力向上のために実証済みの手法を実装する、包括的に設計されたツールです。 イマーシブ リーダー クライアント ライブラリを使用すると、Microsoft Word および Microsoft One Note で使用されるものと同じテクノロジを利用して、Web アプリケーションを改良できます。 

このドキュメントには、次のような記事が記載されています。  

* **[クイックスタート](quickstarts/client-libraries.md)** は、サービスへの要求の実行方法を説明する概要手順です。
* **[攻略ガイド](how-to-create-immersive-reader.md)** には、より具体的またはカスタマイズした方法でサービスを使用するための手順が記載されています。

## <a name="use-immersive-reader-to-improve-reading-accessibility"></a>イマーシブ リーダーを使用して読みのアクセシビリティを向上させる 

イマーシブ リーダーは、あらゆるユーザーの読む力を支援するように設計されています。 イマーシブ リーダーの主な機能について、いくつか説明します。

### <a name="isolate-content-for-improved-readability"></a>読みやすくするためにコンテンツを分離する

イマーシブ リーダーは、読みやすくするためにコンテンツを分離します。 

  ![イマーシブ リーダーを使用して、読みやすくするためにコンテンツを分離する](./media/immersive-reader.png)

### <a name="display-pictures-for-common-words"></a>一般的な単語の絵を表示する

一般的に使用される用語については、イマーシブ リーダーによって絵が表示されます。

  ![イマーシブ リーダーの絵辞書](./media/picture-dictionary.png)

### <a name="highlight-parts-of-speech"></a>品詞を強調表示する

イマーシブ リーダーを使用して、動詞、名詞、代名詞などを強調表示すると、学習者が品詞と文法を理解するのに役立ちます。

  ![イマーシブ リーダーで品詞を示す](./media/parts-of-speech.png)

### <a name="read-content-aloud"></a>コンテンツを読み上げる

音声合成 (またはテキスト読み上げ) は、イマーシブ リーダー サービスに組み込まれています。読者は、読み上げられるテキストを選択できます。 

  ![イマーシブ リーダーでテキストを読み上げる](./media/read-aloud.png)

### <a name="translate-content-in-real-time"></a>コンテンツをリアルタイムで翻訳する

イマーシブ リーダーでは、テキストをさまざまな言語にリアルタイムで翻訳できます。 これは、新しい言語を学習する読者の読解力を向上させるのに役立ちます。

  ![イマーシブ リーダーでテキストを翻訳する](./media/translation.png)

### <a name="split-words-into-syllables"></a>単語を音節に分割する

イマーシブ リーダーを使用すると、単語を音節に分割して読みやすくしたり、新しい単語を発音したりすることができます。

  ![イマーシブ リーダーを使用して単語を音節に分割する](./media/syllabification.png)

## <a name="how-does-immersive-reader-work"></a>イマーシブ リーダーのしくみ

イマーシブ リーダーは、スタンドアロンの Web アプリケーションです。 イマーシブ リーダー クライアント ライブラリを使用して呼び出すと、既存の Web アプリケーション上の `iframe` 内に表示されます。 Web アプリケーションがイマーシブ リーダー サービスを呼び出すときに、リーダーに表示するコンテンツを指定します。 イマーシブ リーダー クライアント ライブラリは、`iframe` の作成およびスタイル設定と、イマーシブ リーダー バックエンド サービスとの通信を処理します。 イマーシブ リーダー サービスは、品詞、テキスト読み上げ、翻訳などのためにコンテンツを処理します。

## <a name="get-started-with-immersive-reader"></a>イマーシブ リーダーを使ってみる

イマーシブ リーダー クライアント ライブラリは、C#、JavaScript、Java (Android)、Kotlin (Android)、および Swift (iOS) で利用できます。 以下を使ってみてください。

* [クイック スタート: イマーシブ リーダー クライアント ライブラリを使用する](quickstarts/client-libraries.md)
