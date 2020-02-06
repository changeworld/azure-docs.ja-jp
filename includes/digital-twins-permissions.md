---
title: インクルード ファイル
description: インクルード ファイル
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 01/23/2020
ms.custom: include file
ms.openlocfilehash: a1576e4a97af5de0b936c662de636aae542a19b5
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/24/2020
ms.locfileid: "76748924"
---
>[!NOTE]
>このセクションでは、[Azure AD アプリの登録](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app)手順について説明します。

1. [Azure portal](https://portal.azure.com) で、展開可能な左側のメニューの **[Azure Active Directory]** を開き、 **[アプリの登録]** ペインを開きます。 

    [![Azure Active Directory ペインを選択する](./media/digital-twins-permissions/azure-portal-select-aad-pane.png)](./media/digital-twins-permissions/azure-portal-select-aad-pane.png#lightbox)

1. **[+ 新規登録]** ボタンを選択します。

    [![新規登録ボタンを選択する](./media/digital-twins-permissions/aad-app-register.png)](./media/digital-twins-permissions/aad-app-register.png#lightbox)

1. **[名前]** ボックスに、このアプリ登録の表示名を入力します。 **[リダイレクト URI (省略可能)]** セクションで、左側のドロップダウン メニューから **[パブリック クライアント/ネイティブ (モバイルとデスクトップ)]** を選択し、右側のテキストボックスに「`https://microsoft.com`」と入力します。 **[登録]** を選択します。

    [![登録されたアプリ](./media/digital-twins-permissions/aad-app-reg-create.png)](./media/digital-twins-permissions/aad-app-reg-create.png#lightbox)

1. [アプリが**パブリック クライアント**として登録されている](https://docs.microsoft.com/azure/active-directory/develop/scenario-desktop-app-registration)ことを確認するには、アプリ登録のための **[認証]** ウィンドウを開き、そのウィンドウ内を下へスクロールします。 **[既定のクライアントの種類]** セクションで、 **[アプリケーションは、パブリック クライアントとして扱います]** に対して **[はい]** を選択し、 **[保存]** をクリックします。

    1. **[リダイレクト URI]** は、認証要求で指定されたアドレスと一致する必要があります。

        * ローカル開発環境でホストされているアプリでは、 **[パブリック クライアント (モバイルとデスクトップ)]** を選択します。 **[既定のクライアントの種類]** が [はい] に設定されていることを確認します。
        * Azure App Service でホストされているシングル ページ アプリでは、 **[Web]** を選択します。

        **[パブリック クライアント (モバイルとデスクトップ)]** を選択し、「`http://localhost:8080/`」と入力します。

        [![リダイレクト URI を構成する](./media/digital-twins-permissions/aad-app-configure-redirect-uris.png)](./media/digital-twins-permissions/aad-app-configure-redirect-uris.png#lightbox)

    1. **[アクセス トークン]** チェック ボックスをオンにし、リソースの **Manifest** JSON の **oauth2AllowImplicitFlow** 設定を `true` に構成します。

        [![パブリック クライアントの構成設定](./media/digital-twins-permissions/aad-configure-public-client.png)](./media/digital-twins-permissions/aad-configure-public-client.png#lightbox)

1.  登録済みアプリの **[概要]** ペインを開き、次のエンティティの値を一時ファイルにコピーします。 これらの値は、以下のセクションでサンプル アプリケーションを構成する際に使用します。

    - **アプリケーション (クライアント) ID**
    - **ディレクトリ (テナント) ID**

    [![Azure Active Directory のアプリケーション ID](./media/digital-twins-permissions/aad-app-reg-app-id.png)](./media/digital-twins-permissions/aad-app-reg-app-id.png#lightbox)

1. アプリの登録の **[API アクセス許可]** ウィンドウを開きます。 **[+ アクセス許可の追加]** ボタンを選択します。 **[API アクセス許可の要求]** ペインで、 **[所属する組織で使用している API]** タブを選択し、次のいずれかを選択します。
    
    1. `Azure Digital Twins` **Azure Digital Twins** API を選択します。

        [![API または Azure Digital Twins を検索する](./media/digital-twins-permissions/aad-aap-search-api-dt.png)](./media/digital-twins-permissions/aad-aap-search-api-dt.png#lightbox)

    1. または、`Azure Smart Spaces Service` を検索します。 **Azure Smart Spaces Service** API を選択します。

        [![Azure Smart Spaces の API を検索する](./media/digital-twins-permissions/aad-app-search-api.png)](./media/digital-twins-permissions/aad-app-search-api.png#lightbox)

    > [!IMPORTANT]
    > 表示される Azure AD API の名前と ID は、テナントによって異なります。
    > * テスト テナントと顧客アカウントでは、`Azure Digital Twins` を検索する必要があります。
    > * 他の Microsoft アカウントでは、`Azure Smart Spaces Service` を検索する必要があります。

1. 選択すると、いずれかの API が同じ **[API アクセス許可の要求]** ペインに **Azure Digital Twins** として表示されます。 **[Read]\(読み取り\)** ドロップ ダウンを選択し、 **[Read.Write]** チェック ボックスをオンにします。 **[アクセス許可の追加]** ボタンを選択します

    [![API のアクセス許可を追加する](./media/digital-twins-permissions/aad-app-req-permissions.png)](./media/digital-twins-permissions/aad-app-req-permissions.png#lightbox)

1. 組織の設定によっては、この API への管理者アクセスを許可するために追加の手順を実行する必要があります。 詳細については、管理者にお問い合わせください。 管理者アクセスが承認されると、 **[API アクセス許可]** ペインの **[管理者の同意が必要]** 列にアクセス許可が表示されます。 

    [![管理者の同意の承認](./media/digital-twins-permissions/aad-app-admin-consent.png)](./media/digital-twins-permissions/aad-app-admin-consent.png#lightbox)

    **Azure Digital Twins** が表示されていることを確認します。
