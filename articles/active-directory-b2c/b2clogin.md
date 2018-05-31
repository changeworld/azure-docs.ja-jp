---
title: b2clogin.com の使用 | Microsoft Docs
description: login.microsoftonline.com の代わりに b2clogin.com を使用する方法について説明します。
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 04/29/2018
ms.author: davidmu
ms.openlocfilehash: d2737e995b91caa2dcc55027baa2b552e64af23e
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2018
ms.locfileid: "33660422"
---
#<a name="using-b2clogincom"></a>b2clogin.com の使用

>[!IMPORTANT]
>この機能はパブリック プレビューです 
>

`login.microsoftonline.com` の代わりに `<YourTenantName>.b2clogin.com` を使用して、Azure AD B2C サービスを使用できるようになりました。  これには次のような多くの利点があります。
* 同じクッキー ヘッダーのサイズ制限を他の Microsoft 製品と共有することはできなくなります
* お使いの URL 内で Microsoft へのすべての参照を削除できます (`<YourTenantName>.onmicrosoft.com` を自分のテナント ID と置き換えることができます)

 b2clogin.com を利用するには、次のうちのいくつかを設定する必要があります。

1. **[今すぐ実行のエンドポイント]** では、`login.microsoftonline.com` の代わりに `<YourTenantName>.b2clogin.com` を使用していることが確認されます。
2. MSAL を使用している場合は、`validateauthority=false` を設定する必要があります。  これは、トークン発行者が `<YourTenantName>.b2clogin.com` になるからです。