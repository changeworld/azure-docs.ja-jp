---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/04/2021
ms.author: trbye
ms.custom: devx-track-js
ms.openlocfilehash: a31faa0bc55d09ccf545abcb21c5651aa5ec6969
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2021
ms.locfileid: "105105053"
---
Speech Service の中核となる機能の 1 つは、人間の音声を認識して文字起こしをする機能です (多くの場合、音声テキスト変換と呼ばれます)。 このサンプルでは、アプリや製品で Speech SDK を使用して、高品質の音声テキスト変換を実行する方法について説明します。

## <a name="react-sample-on-github"></a>GitHub の React サンプル

ブラウザー ベースの JavaScript 環境で Speech SDK を使用する方法については、GitHub の [React サンプル](https://github.com/Azure-Samples/AzureSpeechReactSample)にアクセスしてください。 このサンプルでは、認証トークンの交換と管理を行うための設計パターンの例と、音声変換のためにマイクまたはファイルからオーディオをキャプチャする方法を紹介しています。

また、[Node.js クイックスタート](../../../get-started-speech-to-text.md?pivots=programming-language-nodejs&tabs=script%2cbrowser%2cwindowsinstall)で使用されている設計パターンは、ブラウザー環境でも使用できます。