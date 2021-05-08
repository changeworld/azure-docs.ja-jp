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
ms.openlocfilehash: 8fba8aa577bcb3b5ef44d57c388a1f1de7494782
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95995619"
---
ユーザーにアクセスを許可する前に 2 つ目の認証要素の入力を求めるようにしたい場合には、Azure AD テナント用の Azure AD Multi-Factor Authentication (MFA) を構成できます。 MFA はユーザー単位で構成できます。または、[条件付きアクセス](../articles/active-directory/conditional-access/overview.md)を介して MFA を利用することもできます。

* ユーザーごとの MFA は、追加料金なしで有効にすることができます。 ユーザーごとに MFA を有効にすると、ユーザーは Azure AD テナントに関連付けられているすべてのアプリケーションに対して 2 要素認証を求められます。 手順については、[オプション 1](#peruser) を参照してください。
* 条件付きアクセスを使用すると、2番目の要素をどのように昇格するかをきめ細かく制御できます。 MFA を VPN のみに割り当て、Azure AD テナントに関連付けられている他のアプリケーションを除外することができます。 手順については、[オプション 2](#conditional) を参照してください。