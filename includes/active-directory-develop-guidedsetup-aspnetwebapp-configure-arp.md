---
title: インクルード ファイル
description: インクルード ファイル
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: b18b6e2fd5bb2071f57033dbe74ef87da028720b
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2018
ms.locfileid: "48843313"
---
## <a name="configure-your-aspnet-web-app-with-the-applications-registration-information"></a>アプリケーションの登録情報を使用した ASP.NET Web アプリの構成

この手順では、SSL を使用するようにプロジェクトを構成した後、SSL URL を使用してアプリケーションの登録情報を構成します。 その後、*web.config* を介して、アプリケーションの登録情報をソリューションに追加します。

1.  ソリューション エクスプローラーで、プロジェクト選択して [`Properties`] \(プロパティ) ウィンドウを確認します ([プロパティ] ウィンドウが表示されない場合は F4 キーを押します)。
2.  `SSL Enabled` を `True` に変更します。
3.  上の画像にある [`SSL URL`] の値をコピーしてこのページの最上部にある [`Redirect URL`] \(リダイレクト URL) フィールドに貼り付け、*[更新]* をクリックします。<br/><br/>![プロジェクトのプロパティ](media/active-directory-develop-guidedsetup-aspnetwebapp-configure/vsprojectproperties.png)<br />
4.  `configuration\appSettings` セクションのルート フォルダーにあるファイル `web.config` に、以下のコードを追加します。

```xml
<add key="ClientId" value="[Enter the application Id here]" />
<add key="RedirectUri" value="[Enter the Redirect URL here]" />
<add key="Tenant" value="common" />
<add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" /> 
```
