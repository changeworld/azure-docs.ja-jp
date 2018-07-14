---
title: 認識モードを選択する方法 | Microsoft Docs
description: 最適な認識モードを選択する方法。
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/15/2017
ms.author: zhouwang
ms.openlocfilehash: 4f02b683dde16b537ae5554e6435c068f0fcb808
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35373792"
---
# <a name="speech-recognition-modes"></a>音声認識モード

Microsoft の *Speech to Text* API は、複数のモードの音声認識をサポートします。 お使いのアプリケーションに最も適した認識結果を生成するモードを選択してください。

| Mode | 説明 |
|---|---|
| *interactive* | 対話型のユーザー アプリケーション シナリオ用の「コマンドと制御」認識。 ユーザーはアプリケーションへのコマンドを意図した短いフレーズを話します。 |
| *dictation* | ディクテーション シナリオ用の連続的認識。 ユーザーはテキストとして表示されたより長い文を話します。 ユーザーはより形式ばった口調になります。 |
| *conversation* | 人間間の会話の文字起こし用の連続的認識。 ユーザーはよりくだけた口調になり、長い文と短いフレーズが交互に繰り返されることがあります。

> [!NOTE]
> これらのモードは、[REST API](../GetStarted/GetStartedREST.md) を使用するときに適用されます。 [クライアント ライブラリ](../GetStarted/GetStartedClientLibraries.md)は異なるパラメーターを使用して認識モードを指定します。 詳しくは、選択したクライアント ライブラリをご覧ください。

詳しくは、[認識モード](../concepts.md#recognition-modes)に関するページをご覧ください。
