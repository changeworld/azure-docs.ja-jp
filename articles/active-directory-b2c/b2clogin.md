---
title: b2clogin.com の使用 | Microsoft Docs
description: login.microsoftonline.com の代わりに b2clogin.com を使用する方法について説明します。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 04/29/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: c41c02acaeffa170d55f3c59f34a4b1ecae1c523
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/04/2018
ms.locfileid: "34712455"
---
# <a name="using-b2clogincom"></a>b2clogin.com の使用

>[!IMPORTANT]
>この機能はパブリック プレビューです 
>

`login.microsoftonline.com` の代わりに `<YourTenantName>.b2clogin.com` を使用して、Azure AD B2C サービスを使用できるようになりました。  これには次のような多くの利点があります。
* 同じクッキー ヘッダーのサイズ制限を他の Microsoft 製品と共有することはできなくなります
* お使いの URL 内で Microsoft へのすべての参照を削除できます (`<YourTenantName>.onmicrosoft.com` を自分のテナント ID と置き換えることができます)

 b2clogin.com を利用するには、次のうちのいくつかを設定する必要があります。

1. **[今すぐ実行のエンドポイント]** では、`login.microsoftonline.com` の代わりに `<YourTenantName>.b2clogin.com` を使用していることが確認されます。
2. MSAL を使用している場合は、`validateauthority=false` を設定する必要があります。  これは、トークン発行者が `<YourTenantName>.b2clogin.com` になるからです。