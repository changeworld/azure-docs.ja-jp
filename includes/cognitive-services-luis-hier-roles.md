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
ms.date: 08/02/2018
ms.author: diberry
ms.openlocfilehash: dd99218765c873120c4117a3ec1712fe0a605e66
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/07/2018
ms.locfileid: "44164589"
---
**質問**: 階層エンティティまたはロール付きシンプル エンティティを含むパターンを使用する必要がありますか? 

**回答**: 場合によります。 パターンと発話の例は、それらがユーザーの発話を表し、意図に特有であるという点で同等です。  

発話に明確なパターンがない場合は、階層エンティティを使用します。 

|階層エンティティ|ロール付きシンプル エンティティ|
|--|--|
|意図でラベル付けされた子エンティティを含む発話の例が必要です|発話の例が必要になり、**ロールを意図でラベル付けすることはできません**|
|パターンで使用できます|パターンで使用する**必要があります**|
|意図の発話の例が**より多く**必要になる可能性があります|意図の発話の例があまり必要にならない** 可能性があります|