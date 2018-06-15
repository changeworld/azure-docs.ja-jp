---
title: PingAccess を使うようにアプリケーション プロキシ アプリケーションを構成する方法 | Microsoft Docs
description: PingAccess を使用して、アプリケーション プロキシの利点をヘッダー ベースの認証が使われているアプリケーションにまで拡大する方法について説明します。
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/21/2018
ms.author: barbkess
ms.reviewer: harshja
ms.openlocfilehash: aba88472a7fdc4f3e4dfb53e6268376d7d3068dc
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/01/2018
ms.locfileid: "34590139"
---
# <a name="how-to-configure-an-application-proxy-application-to-use-pingaccess"></a>PingAccess を使うようにアプリケーション プロキシ アプリケーションを構成する方法

PingAccess との連携によって、ヘッダー ベースの認証を使うアプリケーションで、アプリケーション プロキシの利点を活かせるようになりました。 ご利用のアプリケーションでヘッダーが使われていない場合は、[シングル サインオンに関するドキュメント](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)を参照してください。その他の選択肢について詳しく説明されています。

## <a name="overview-of-steps-and-recommended-documents"></a>大まかな手順と推奨されるドキュメント

PingAccess を使ったアプリケーションの構成は、次の 4 つの手順で行います。

1.  アプリケーション プロキシ コネクタを構成する

2.  Azure AD アプリケーション プロキシ アプリケーションを作成する

3.  PingAccess をダウンロードして構成する

4.  PingAccess でアプリケーションを構成する

それぞれの手順について詳しくは、[ヘッダーを使ったシングル サインオンに関するドキュメント](manage-apps/application-proxy-configure-single-sign-on-with-ping-access.md)を参照してください。
