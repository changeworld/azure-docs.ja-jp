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
ms.tgt_pltfrm: ios
ms.workload: identity
ms.date: 09/19/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: d8d7d5649ca1dc215f85f928d111ff1367c60bb5
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/24/2018
ms.locfileid: "49988270"
---
## <a name="test-querying-the-microsoft-graph-api-from-your-ios-application"></a>iOS アプリケーションから Microsoft Graph API にクエリ実行をテストする

シミュレーターでコードを実行するには、**コマンド** + **R**キーを押します。

![シミュレーターでアプリケーションをテストする](media/active-directory-develop-guidedsetup-ios-test/iostestscreenshot.png)

テストの準備ができたら、**[Call Microsoft Graph API]\(Microsoft Graph API の呼び出し\)** を選択します。 メッセージが表示されたら、ユーザー名とパスワードを入力します。

### <a name="provide-consent-for-application-access"></a>アプリケーションによるアクセスに同意する

アプリケーションに初めてサインインするとき、アプリケーションがプロファイルにアクセスし、サインインできることへの同意を求められます。

![アプリケーションによるアクセスに同意する](media/active-directory-develop-guidedsetup-ios-test/iosconsentscreen.png)

### <a name="view-application-results"></a>アプリケーションの結果を表示する
サインインした後、**[ログ]** セクションに、Microsoft Graph API の呼び出しによって返されたユーザー プロファイル情報が表示されます。 

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>スコープと委任されたアクセス許可の詳細

Microsoft Graph API には、ユーザーのプロファイルを読み取るための **user.read** スコープが必要です。 このスコープは既定で、登録ポータルに登録されているすべてのアプリケーションで自動的に追加されます。 Microsoft Graph の他の API や、バックエンド サーバーのカスタム API には、追加のスコープが必要な場合があります。 Microsoft Graph API には、ユーザーの予定表を表示するための **Calendars.Read** スコープが必要です。

アプリケーションのコンテキストでユーザーの予定表にアクセスするには、**Calendars.Read** の委任されたアクセス許可をアプリケーション登録情報に追加します。 次に、**Calendars.Read** スコープを **acquireTokenSilent** 呼び出しに追加します。

>[!NOTE]
>スコープの数を増やすと、ユーザーは追加の同意を求められることがあります。

<!--end-collapse-->

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]
