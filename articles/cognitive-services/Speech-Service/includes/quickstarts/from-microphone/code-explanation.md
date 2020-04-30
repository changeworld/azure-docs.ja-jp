---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/02/2020
ms.author: trbye
ms.openlocfilehash: fdcb5bb57afe145b57cb1223242d4d5625a2dfb7
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "81400735"
---
音声構成オブジェクトを作成するには、音声リソースのサブスクリプション キーとリージョンが必要です。 音声認識エンジン オブジェクトをインスタンス化するには、この構成オブジェクトが必要です。

認識エンジン インスタンスは、複数の音声認識方法を公開します。 この例では、音声は 1 回だけ認識されます。 この機能は、認識のために 1 つの語句が送信されていること、そして語句が識別されると音声の認識を停止することを音声サービスに知らせます。 結果が生成されると、コードによって、認識理由がコンソールに書き込まれます。

> [!TIP]
> Speech SDK は、既定で言語に `en-us` を使用して認識します。ソース言語の選択については、「[音声テキスト変換のソース言語を指定する](../../../how-to-specify-source-language.md)」を参照してください。