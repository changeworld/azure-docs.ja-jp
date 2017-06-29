---
title: "Azure Active Directory B2C: アプリケーションの登録 | Microsoft Docs"
description: "Azure Active Directory B2C にアプリケーションを登録する方法"
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: 20e92275-b25d-45dd-9090-181a60c99f69
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 6/13/2017
ms.author: parakhj
ms.translationtype: Human Translation
ms.sourcegitcommit: ef1e603ea7759af76db595d95171cdbe1c995598
ms.openlocfilehash: 4649bec5290e09b8a18e3e12719e63bf5e57c0b0
ms.contentlocale: ja-jp
ms.lasthandoff: 06/16/2017



---
# <a name="azure-active-directory-b2c-register-your-application"></a>Azure Active Directory B2C: アプリケーションを登録する

> [!IMPORTANT]
> Azure Portal の Azure AD B2C ブレードから作成したアプリケーションは、同じ場所から管理する必要があります。 PowerShell または別のポータルを使用して B2C アプリケーションを編集すると、そのアプリケーションはサポート対象外となり、Azure AD B2C で動作しなくなる可能性があります。 詳しくは[下記](#faulted-apps)をお読みください。
>

## <a name="prerequisite"></a>前提条件

コンシューマーのサインアップおよびサインインを受け付けるアプリケーションを作成するには、最初にアプリケーションを Azure Active Directory B2C テナントに登録する必要があります。 「 [Azure AD B2C テナントを作成する](active-directory-b2c-get-started.md)」に書かれている手順に従って独自のテナントを取得してください。 この記事のすべての手順を実行したら、B2C 機能ブレードがスタート画面にピン留めされているはずです。

[!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="navigate-to-the-b2c-features-blade"></a>B2C 機能ブレードに移動する

B2C 機能ブレードがスタート画面にピン留めされている場合、B2C テナントの全体管理者として [Azure ポータル](https://portal.azure.com/) にサインインするとすぐに、B2C 機能ブレードが表示されます。

また、[Azure Portal](https://portal.azure.com/) の左側のナビゲーション ウィンドウで **[その他のサービス]** をクリックし、"**Azure AD B2C**" を検索して、このブレードにアクセスすることもできます。

> [!IMPORTANT]
> B2C 機能ブレードにアクセスするには、B2Cテナントのグローバル管理者である必要があります。 他のテナントのグローバル管理者やテナントのユーザーはアクセスできません。  Azure Portal の右上隅にあるテナント スイッチャーを使用して、B2C テナントに切り替えることができます。
>
>

## <a name="register-a-web-application"></a>Web アプリケーションの登録

1. Azure ポータルの B2C 機能ブレードで、 **[アプリケーション]**をクリックします。
1. ブレードの上部にある **[+追加]** をクリックします。
1. アプリケーションの **[名前]** には、コンシューマーがアプリケーションの機能を把握できるような名前を設定します。 たとえば、「Contoso B2C app」などと入力します。
1. **[Include web app / web API (Web アプリ/Web API を含める)]** スイッチを **[はい]** に切り替えます。
1. **[応答 URL]** に[適切な](#choosing-a-web-app/api-reply-url)値 (アプリケーションが要求したトークンを Azure AD B2C が返すエンドポイント) を入力します。 たとえば、「 `https://localhost:44316/`」のように入力します。
1. **[作成]** をクリックして、アプリケーションを登録します。
1. 作成したアプリケーションをクリックし、後でコードの作成時に使用するために、グローバルに一意の **アプリケーション クライアント ID** をメモしておきます。
1. Web アプリケーションで、Azure AD B2C によってセキュリティ保護された Web API も呼び出す場合は、次の手順が必要になります。
    1. **[キー]** ブレードに移動して **[キーの生成]** ボタンをクリックし、**アプリケーション シークレット**を作成します。
    1. **[API アクセス]**、**[追加]** の順にクリックし、Web API とスコープ (アクセス許可) を選択します。

> [!NOTE]
> **アプリケーション シークレット** は重要なセキュリティ資格情報であり、適切にセキュリティで保護する必要があります。
>

## <a name="register-a-web-api"></a>Web API の登録

1. Azure ポータルの B2C 機能ブレードで、 **[アプリケーション]**をクリックします。
1. ブレードの上部にある **[+追加]** をクリックします。
1. アプリケーションの **[名前]** には、コンシューマーがアプリケーションの機能を把握できるような名前を設定します。 たとえば、「Contoso B2C api」などと入力します。
1. **[Include web app / web API (Web アプリ/Web API を含める)]** スイッチを **[はい]** に切り替えます。
1. **[応答 URL]** に[適切な](#choosing-a-web-app/api-reply-url)値 (アプリケーションが要求したトークンを Azure AD B2C が返すエンドポイント) を入力します。 たとえば、「 `https://localhost:44316/`」のように入力します。
1. **アプリケーション ID/URI** を入力します。 これは Web API に使用される ID です。 たとえば「notes」と入力します。 その下に完全な識別子 URI が生成されます。
1. **[作成]** をクリックして、アプリケーションを登録します。
1. 作成したアプリケーションをクリックし、後でコードの作成時に使用するために、グローバルに一意の **アプリケーション クライアント ID** をメモしておきます。
1. **[Published scopes (公開スコープ)]** をクリックします。 ここで、他のアプリケーションに付与できるアクセス許可 (スコープ) を定義します。
1. 必要に応じてスコープを追加します。 既定では、"user_impersonation" スコープが定義されます。 これで他のアプリケーションが、サインイン ユーザーの代わりにこの API にアクセスできるようになります。 不要であれば、これは削除してもかまいません。
1. [ **Save**] をクリックします。

## <a name="register-a-mobilenative-application"></a>モバイル/ネイティブ アプリケーションの登録

1. Azure ポータルの B2C 機能ブレードで、 **[アプリケーション]**をクリックします。
1. ブレードの上部にある **[+追加]** をクリックします。
1. アプリケーションの **[名前]** には、コンシューマーがアプリケーションの機能を把握できるような名前を設定します。 たとえば、「Contoso B2C app」などと入力します。
1. **[Include native client (ネイティブ クライアントを含める)]** スイッチを **[はい]** に切り替えます。
1. カスタム スキームを含めた**リダイレクト URI** を入力します。 たとえば、com.onmicrosoft.contoso.appname://redirect/path です。 [正常に機能するリダイレクト URI](#choosing-a-native-application-redirect-uri) を選択し、アンダースコアなどの特殊文字は含めないようにしてください。
1. **[保存]** をクリックして、アプリケーションを登録します。
1. 作成したアプリケーションをクリックし、後でコードの作成時に使用するために、グローバルに一意の **アプリケーション クライアント ID** をメモしておきます。
1. ネイティブ アプリケーションで、Azure AD B2C によってセキュリティ保護された Web API も呼び出す場合は、次の手順が必要になります。
    1. **[キー]** ブレードに移動して **[キーの生成]** ボタンをクリックし、**アプリケーション シークレット**を作成します。
    1. **[API アクセス]**、**[追加]** の順にクリックし、Web API とスコープ (アクセス許可) を選択します。

> [!NOTE]
> **アプリケーション シークレット** は重要なセキュリティ資格情報であり、適切にセキュリティで保護する必要があります。
>

## <a name="limitations"></a>制限事項

### <a name="choosing-a-web-appapi-reply-url"></a>Web アプリ/API の応答 URL の選択

現時点では、Azure AD B2C に登録されるアプリは、限られた応答 URL 値に制限されています。 Web アプリと Web サービスの応答 URL は `https` スキームで始まる必要があり、すべての応答 URL 値で 1 つの DNS ドメインを共有する必要があります。 たとえば、次の応答 URL のいずれかを使用する Web アプリを登録することはできません。

`https://login-east.contoso.com`

`https://login-west.contoso.com`

登録システムによって、既存の応答 URL の DNS 名全体と、追加しようとしている応答 URL の DNS 名が比較されます。 次のいずれかの条件に当てはまる場合、DNS 名を追加する要求は失敗します。

* 新しい応答 URL の DNS 名全体が、既存の応答 URL の DNS 名と一致しない場合。
* 新しい応答 URL の DNS 名全体が、既存の応答 URL のサブドメインではない場合。

たとえば、アプリで次の応答 URL を使用しているとします。

`https://login.contoso.com`

次のように、これに追加できます。

`https://login.contoso.com/new`

この場合、DNS は完全に一致します。 または、次を行うことができます。

`https://new.login.contoso.com`

この場合、login.contoso.com の DNS サブドメインを参照しています。 応答 URL として login-east.contoso.com と login-west.contoso.com を使用するアプリが必要な場合は、これらの応答 URL を次の順番で追加する必要があります。

`https://contoso.com`

`https://login-east.contoso.com`

`https://login-west.contoso.com`

後の 2 つの応答 URL を追加できるのは、これらが 1 つ目の contoso.com という応答 URL のサブドメインであるためです。

### <a name="choosing-a-native-application-redirect-uri"></a>ネイティブ アプリケーションのリダイレクト URI の選択

モバイル/ネイティブ アプリケーションのリダイレクト URI を選択する際には、2 つの重要な考慮事項があります。

* **一意**: リダイレクト URI のスキームは、すべてのアプリケーションで一意である必要があります。 この例 (com.onmicrosoft.contoso.appname://redirect/path) では、スキームとして com.onmicrosoft.contoso.appname を使用しています。 このパターンに従うことをお勧めします。 2 つのアプリケーションで同じスキームを共有している場合、ユーザーには [アプリの選択] ダイアログが表示されます。 ユーザーが不適切な選択を行った場合、ログインは失敗します。
* **完全**: リダイレクト URI には、スキームとパスが必要です。 パスには、ドメインの後に 1 つ以上のスラッシュを含める必要があります (たとえば、//contoso/ は機能しますが、//contoso は失敗します)。

リダイレクト URI にアンダースコアなどの特殊文字がないことを確認します。

### <a name="faulted-apps"></a>アプリの障害

B2C アプリケーションは次の手段で編集しないでください。

* 他のアプリケーション管理ポータル ([Azure クラシック ポータル](https://manage.windowsazure.com/)や[アプリケーション登録ポータル](https://apps.dev.microsoft.com/)など)
* Graph API または PowerShell

前に説明したように、B2C アプリケーションを編集し、それを Azure Portal の Azure AD B2C 機能ブレードでもう一度編集しようとすると、アプリに障害が発生し、Azure AD B2C で使用できなくなります。 この場合、アプリケーションを削除し、もう一度作成する必要があります。

アプリケーションを削除するには、[アプリケーション登録ポータル](https://apps.dev.microsoft.com/)に移動し、そこでアプリケーションを削除します。 アプリケーションを表示するためには、そのアプリケーションの所有者である必要があります (テナントの管理者であるだけでは不十分です)。

## <a name="next-steps"></a>次のステップ

アプリケーションを Azure AD B2C に登録したので、いずれかの[クイック スタート チュートリアル](active-directory-b2c-overview.md#get-started)を実行して使い始めることができます。
