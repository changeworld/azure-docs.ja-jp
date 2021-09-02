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
ms.openlocfilehash: ab38a17f96bf10d5ae6745ede5c89c90eb47fda7
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2021
ms.locfileid: "113432073"
---
> [!WARNING]
> この例のトークンを含めて、自分が所有していないすべての API について、トークンの検証や読み取りを行わないでください。  Microsoft サービスのトークンは、JWT として検証されない特殊な形式を使用できます。また、コンシューマー (Microsoft アカウント) ユーザーに対して暗号化される場合もあります。 トークンの読み取りは便利なデバッグおよび学習ツールですが、コード内でこれに対する依存関係を取得したり、自分で制御する API 用ではないトークンについての詳細を想定したりしないでください。
