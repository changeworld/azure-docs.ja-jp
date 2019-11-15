---
title: インクルード ファイル
description: インクルード ファイル
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 11/05/2019
ms.author: diberry
ms.openlocfilehash: bbe40083159d433c0b5746834e1c530b23b03851
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2019
ms.locfileid: "73648091"
---
クライアント アプリケーションは、発話がアプリケーションにとって無意味または不適切なものでないかどうかを把握する必要があります。 **None** 意図は、発話がクライアント アプリケーションから返答できないものであるかどうかを判断するための作成プロセスの一部として、各アプリケーションに追加されます。

LUIS が発話に対して **None** 意図を返した場合、クライアント アプリケーションは、ユーザーが会話の終了を望んでいるかどうかを確認するか、会話を続行するよう追加の指示を出すことができます。 

**None** 意図を空のままにした場合、主題の領域の範囲外で予測する必要のある発話は、既存の主題の領域の意図のいずれかで予測されます。 その結果、チャット ボットなどのクライアント アプリケーションでは、正しくない予測に基づいて正しくない操作が実行されます。 

1. 左側のパネルから **[Intents]\(意図\)** を選びます。

1. **None** 意図を選択します。 ユーザーが入力する可能性があるがアプリには関係のない 3 つの発話を追加します。

    |`None` の発話例|
    |--|
    |`Barking dogs are annoying`|
    |`Order a pizza for me`|
    |`Penguins in the ocean`|
