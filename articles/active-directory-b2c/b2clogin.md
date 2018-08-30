---
title: b2clogin.com の使用 | Microsoft Docs
description: login.microsoftonline.com の代わりに b2clogin.com を使用する方法について説明します。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/29/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 6ad0a5d59b28bf48742c9e1be89b51d2301dd582
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/29/2018
ms.locfileid: "43189292"
---
# <a name="using-b2clogincom"></a>b2clogin.com の使用

>[!IMPORTANT]
>この機能はパブリック プレビューです 
>

`login.microsoftonline.com` の代わりに `<YourTenantName>.b2clogin.com` を使用して、Azure AD B2C サービスを使用できるようになりました。  これには次のような多くの利点があります。
* 同じ Cookie ヘッダーのサイズ制限を他の Microsoft 製品と共有することはできなくなります。
* お使いの URL 内で Microsoft へのすべての参照を削除できます (`<YourTenantName>.onmicrosoft.com` を自分のテナント ID と置き換えることができます)。 たとえば、「 `https://<tenantname>.b2clogin.com/tfp/<tenantID>/<policyname>/v2.0/.well-known/openid-configuration`」のように入力します。

 b2clogin.com を利用するには、次のうちのいくつかを設定する必要があります。

1. **[今すぐ実行のエンドポイント]** では、`login.microsoftonline.com` の代わりに `<YourTenantName>.b2clogin.com` を使用していることが確認されます。
2. MSAL を使用している場合は、`validateauthority=false` を設定する必要があります。  これは、トークン発行者が `<YourTenantName>.b2clogin.com` になるからです。
