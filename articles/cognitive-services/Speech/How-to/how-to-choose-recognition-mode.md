---
title: Bing Speech 認識モードを選択する方法 | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Bing Speech で最適な認識モードを選択する方法。
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX
ms.openlocfilehash: a39b357a26823e322d4e902f2d99b67488bbf2df
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "46950881"
---
# <a name="bing-speech-recognition-modes"></a>Bing Speech の認識モード

Bing Speech to Text API では、複数の音声認識モードがサポートされています。 お使いのアプリケーションに最も適した認識結果を生成するモードを選択してください。

| Mode | 説明 |
|---|---|
| *interactive* | 対話型のユーザー アプリケーション シナリオ用の「コマンドと制御」認識。 ユーザーはアプリケーションへのコマンドを意図した短いフレーズを話します。 |
| *dictation* | ディクテーション シナリオ用の連続的認識。 ユーザーはテキストとして表示されたより長い文を話します。 ユーザーはより形式ばった口調になります。 |
| *conversation* | 人間間の会話の文字起こし用の連続的認識。 ユーザーはよりくだけた口調になり、長い文と短いフレーズが交互に繰り返されることがあります。

> [!NOTE]
> これらのモードは、[REST API](../GetStarted/GetStartedREST.md) を使用するときに適用されます。 [クライアント ライブラリ](../GetStarted/GetStartedClientLibraries.md)は異なるパラメーターを使用して認識モードを指定します。 詳しくは、選択したクライアント ライブラリをご覧ください。

詳しくは、[認識モード](../concepts.md#recognition-modes)に関するページをご覧ください。
