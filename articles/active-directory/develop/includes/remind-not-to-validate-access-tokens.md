---
title: Microsoft Graph のアクセス トークンを検証しない
description: Microsoft Graph のアクセス トークンは非透過的と見なす必要があり、顧客コードによって検証してはいけないことを警告するインクルード ファイル。 Microsoft Graph アクセス トークンを検証するのは Microsoft Graph のみです。
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: include
ms.date: 06/25/2021
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: e3dad40f1e4c1e8c463217720a190fd0e961334f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128909508"
---
> [!WARNING]
> この例のトークンを含めて、自分が所有していないすべての API について、トークンの検証や読み取りを行わないでください。 Microsoft サービスのトークンには、JWT として検証されない特殊な形式を使用できます。また、コンシューマー (Microsoft アカウント) ユーザーに対して暗号化される場合もあります。 トークンの読み取りは便利なデバッグおよび学習ツールですが、コード内でこれに対する依存関係を取得したり、自分で制御する API 用ではないトークンについての詳細を想定したりしないでください。
