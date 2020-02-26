---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/14/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 3417bf0bd4ae1e0aa670f9fbfcc1fbbfeb372972
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/19/2020
ms.locfileid: "77471539"
---
ユーザーにアクセスを許可する前に 2 つ目の認証要素の入力を求めるようにしたい場合には、Azure AD テナント用の Azure Multi-Factor Authentication (MFA) を構成できます。 MFA はユーザー単位で構成できます。または、[条件付きアクセス](../articles/active-directory/conditional-access/overview.md)を介して MFA を利用することもできます。

* ユーザーごとの MFA は、追加料金なしで有効にすることができます。 ユーザーごとに MFA を有効にすると、ユーザーは Azure AD テナントに関連付けられているすべてのアプリケーションに対して 2 要素認証を求められます。 手順については、[オプション 1](#peruser) を参照してください。
* 条件付きアクセスを使用すると、2番目の要素をどのように昇格するかをきめ細かく制御できます。 MFA を VPN のみに割り当て、Azure AD テナントに関連付けられている他のアプリケーションを除外することができます。 手順については、[オプション 2](#conditional) を参照してください。