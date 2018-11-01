---
author: PatAltimore
ms.service: active-directory-b2c
ms.topic: include
ms.date: 11/03/2016
ms.author: patricka
ms.openlocfilehash: 511b05e6cae769a5b39ae81a3e67efd05d374511
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/25/2018
ms.locfileid: "50133038"
---
アプリケーションへのサインアップのみを可能にする場合は、**サインアップ** ポリシーを使用します。 このポリシーは、サインアップ中に顧客が体験するエクスペリエンスと、正常なサインアップの直後にアプリケーションが受信するトークンの内容を記述します。

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]

**[サインアップ ポリシー]** をクリックします。

ブレードの上部にある **[+追加]** をクリックします。

**[名前]** によって、アプリケーションで使用されるサインアップ ポリシー名が決定されます。 たとえば、「**SiUp**」と入力します。

**[Identity providers]\(ID プロバイダー\)** をクリックし、**[Email signup]\(電子メール サインアップ\)** を選択します。 既に構成されている場合は、ソーシャル ID プロバイダーを選択することもできます。 Click **OK**.

**[サインアップ属性]** をクリックします。 ここで、サインアップ中にコンシューマーから収集する属性を選択します。 たとえば、**[国/リージョン]**、**[表示名]**、および **[Postal Code] \(郵便番号)** を選択します。 Click **OK**.

**[アプリケーション クレーム]** をクリックします。 ここで、サインアップ エクスペリエンスの成功後にアプリケーションに戻されるトークンで返される要求を選択します。 たとえば、**[表示名]**、**[ID プロバイダー]**、**[Postal Code] \(郵便番号)**、**[User is new] \(ユーザーは新規)**、および **[User's Object ID] \(ユーザーのオブジェクト ID)** を選択します。

**Create** をクリックしてください。 作成したポリシーが **[Sign-up policies]\(サインアップ ポリシー\)** ブレードに "**B2C_1_SiUp**" と表示されます (**B2C\_1\_** フラグメントが自動的に追加されます)。

**[B2C_1_SiUp]** をクリックしてポリシーを開きます。

**[アプリケーション]** ドロップダウンで **[Contoso B2C app] \(Contoso B2C アプリ)** を、また **[Reply URL / Redirect URI] \(応答 URL/リダイレクト URI)** ドロップダウンで `https://localhost:44321/` を選択します。

**[今すぐ実行]** をクリックします。 新しいブラウザー タブが開き、アプリケーションへのサインアップのコンシューマー エクスペリエンスを確認できます。

> [!NOTE]
> ポリシーの作成と更新が有効になるまで、最大で 1 分間かかります。
>