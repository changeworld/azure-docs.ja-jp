---
title: インクルード ファイル
description: インクルード ファイル
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: include
ms.custom: include file
ms.date: 12/21/2018
ms.author: diberry
ms.openlocfilehash: 0579b1e21e714e25e197cb5abf46793a38978d06
ms.sourcegitcommit: 7862449050a220133e5316f0030a259b1c6e3004
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/22/2018
ms.locfileid: "53755671"
---
クライアント アプリケーションは、発話がアプリケーションにとって無意味または不適切なものでないかどうかを把握する必要があります。 **None** 意図は、発話がクライアント アプリケーションから返答できないものであるかどうかを判断するための作成プロセスの一部として、各アプリケーションに追加されます。

LUIS が発話に対して **None** 意図を返した場合、クライアント アプリケーションは、ユーザーが会話の終了を望んでいるかどうかを確認するか、会話を続行するよう追加の指示を出すことができます。 

> [!CAUTION] 
> **None** 意図は空のままにしないでください。 

1. 左側のパネルから **[Intents]\(意図\)** を選びます。

2. **None** 意図を選択します。 ユーザーが入力する可能性があっても、この人事アプリには関係のない 3 つの発話を追加します。

    | 発話の例|
    |--|
    |吠える犬はうっとうしい|
    |ピザを注文して|
    |海の中のペンギン|