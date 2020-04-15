---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/02/2020
ms.author: dapine
ms.openlocfilehash: 3125b6203f04b4cabd8cd0822a97317185cbf194
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2020
ms.locfileid: "80638132"
---
音声構成オブジェクトを作成するには、音声リソースのサブスクリプション キーとリージョンが必要です。 音声認識エンジン オブジェクトをインスタンス化するには、この構成オブジェクトが必要です。

認識エンジン インスタンスは、複数の音声認識方法を公開します。 この例では、音声は 1 回だけ認識されます。 この機能は、認識のために 1 つの語句が送信されていること、そして語句が識別されると音声の認識を停止することを音声サービスに知らせます。 結果が生成されると、コードによって、認識理由がコンソールに書き込まれます。

> [!TIP]
> Speech SDK は、既定で言語に `en-us` を使用して認識します。ソース言語の選択については、「[音声テキスト変換のソース言語を指定する](../../../how-to-specify-source-language.md)」を参照してください。