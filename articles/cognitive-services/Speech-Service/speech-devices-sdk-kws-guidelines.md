---
title: キーワードの名前付けのガイドライン - Speech Service
titleSuffix: Azure Cognitive Services
description: 有効なキーワードを作成することは、デバイスによって一貫して正確な応答が行われることを保証するうえで不可欠です。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: trbye
ms.openlocfilehash: c00d27e5e7b7a8d614ce94ea4024b6093669757c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399842"
---
# <a name="guidelines-for-creating-an-effective-keyword"></a>有効なキーワードを作成するためのガイドライン

有効なキーワードを作成することは、デバイスによって一貫して正確な応答が行われることを保証するうえで不可欠です。 キーワードをカスタマイズすることは、デバイスを差別化し、ブランドを強化する上で効果的な方法です。 この記事では、有効なキーワードを作成するためのいくつかの基本原則について説明します。

## <a name="choose-an-effective-keyword"></a>有効なキーワードを選択する

キーワードを選択するときには、以下のガイドラインを考慮に入れます。

> [!div class="checklist"]
> * キーワードは、英語の単語または語句にする必要があります。
> * 2 秒以内に読み上げることができる必要があります。
> * 4 音節から 7 音節の単語が最適です。 たとえば、"Hey, Computer" は適切なキーワードです。 "Hey" だけではよくありません。
> * キーワードは、英語の一般的な発音規則に従っている必要があります。
> * 英語の一般的な発音規則に従った一意の単語や造語にすると、誤検知が減る可能性があります。 たとえば "computerama" が適切なキーワードになる可能性があります。
> * 一般的な単語は選択しないでください。 たとえば、"eat" や "go" は、普通の会話の中で頻繁に出てくる単語です。 これらは、デバイスの誤ったトリガーとなる可能性があります。
> * 別の発音をする可能性があるキーワードは使用しないようにします。 ユーザーは、デバイスが応答するようにするために、”正しい” 発音を知っている必要があります。 たとえば "509" は、"five zero nine"、"five oh nine"、または "five hundred and nine" と発音できます。 "R.E.I." は、"R E I" または "Ray" として発音できます。 "Live" は、"/Līv/" または "/liv/" と発音できます。
> * 特殊な文字、記号、または数字は使用しないでください。 たとえば、"Go#" や "20 + cats" は、問題を引き起こすキーワードになる可能性があります。 ただし、"go sharp" や "twenty plus cats" は機能するでしょう。 それでもブランディングにおいて記号を使用して、正しい発音が一般に認知されるように、マーケティングやドキュメントを利用することができます。

> [!NOTE]
> 商標登録された単語をキーワードとして選択する場合は、自身でその商標を所有しているか、商標の所有者からその単語の使用を許可されている必要があります。 Microsoft は、キーワードの選択によって発生する可能性がある法律上のどんな問題に対しても一切の責任を負いません。

## <a name="next-steps"></a>次のステップ

[Speech Studio を使用してカスタム キーワードを作成する](speech-devices-sdk-create-kws.md)方法を確認してください。
