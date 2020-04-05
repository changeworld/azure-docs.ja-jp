---
title: インクルード ファイル
description: インクルード ファイル
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 02/03/2020
ms.custom: include file
ms.openlocfilehash: cfe3eb4c0ac1378b7c519b3b34094945612d8508
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2020
ms.locfileid: "77029075"
---
>[!NOTE]
>このセクションでは、[Azure AD アプリの登録](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app)手順について説明します。

1. [Azure portal](https://portal.azure.com) で、展開可能な左側のメニューの **[Azure Active Directory]** を開き、 **[アプリの登録]** ペインを開きます。 

    [![Azure Active Directory ペインを選択する](./media/digital-twins-permissions/azure-portal-select-aad-pane.png)](./media/digital-twins-permissions/azure-portal-select-aad-pane.png#lightbox)

1. **[+ 新規登録]** ボタンを選択します。

    [![新規登録ボタンを選択する](./media/digital-twins-permissions/aad-app-register.png)](./media/digital-twins-permissions/aad-app-register.png#lightbox)

1. **[名前]** ボックスに、このアプリ登録の表示名を入力します。 

    1. **[リダイレクト URI (省略可能)]** セクションのテキスト ボックスに「`https://microsoft.com`」と入力します。     

    1. Azure Active Directory アプリでどのアカウントとテナントがサポートされるかを確認します。

    1. **[登録]** を選択します。

    [![登録されたアプリ](./media/digital-twins-permissions/aad-app-reg-create.png)](./media/digital-twins-permissions/aad-app-reg-create.png#lightbox)

1. **[認証]** ブレードで、重要な認証構成設定を指定します。 

    1. **[+ プラットフォームを追加]** を選択して、 **[リダイレクト URI]** を追加し、 **[アクセス トークン]** を構成します。

    1. アプリが**パブリック クライアント**であることを指定するには、 **[はい]** を選択します。

    1. Azure Active Directory アプリでどのアカウントとテナントがサポートされるかを確認します。

    [![パブリック クライアントの構成設定](./media/digital-twins-permissions/aad-configure-public-client.png)](./media/digital-twins-permissions/aad-configure-public-client.png#lightbox)

1. 適切なプラットフォームを選択したら、ユーザー インターフェイスの右側のサイド パネルで **[リダイレクト URI]** と **[アクセス トークン]** を構成します。

    1. **[リダイレクト URI]** は、認証要求で指定されたアドレスと一致する必要があります。

        * ローカル開発環境でホストされているアプリでは、 **[パブリック クライアント (モバイルとデスクトップ)]** を選択します。 **[パブリック クライアント]** は必ず **[はい]** に設定してください。
        * Azure App Service でホストされているシングルページ アプリでは、 **[Web]** を選択します。

    1. **[ログアウト URL]** が適切かどうかを確認します。

    1. **[アクセス トークン]** または **[ID トークン]** をオンにすることによって、暗黙的な許可のフローを有効にします。
                
    [![リダイレクト URI を構成する](./media/digital-twins-permissions/aad-app-configure-redirect-uris.png)](./media/digital-twins-permissions/aad-app-configure-redirect-uris.png#lightbox)

    **[構成]** 、 **[保存]** の順にクリックします。

1.  登録済みアプリの **[概要]** ペインを開き、次のエンティティの値を一時ファイルにコピーします。 これらの値は、以下のセクションでサンプル アプリケーションを構成する際に使用します。

    - **アプリケーション (クライアント) ID**
    - **ディレクトリ (テナント) ID**

    [![Azure Active Directory のアプリケーション ID](./media/digital-twins-permissions/aad-app-reg-app-id.png)](./media/digital-twins-permissions/aad-app-reg-app-id.png#lightbox)

1. アプリの登録の **[API アクセス許可]** ウィンドウを開きます。 **[+ アクセス許可の追加]** ボタンを選択します。 **[API アクセス許可の要求]** ペインで、 **[所属する組織で使用している API]** タブを選択し、次のいずれかを選択します。
    
    1. [https://login.microsoftonline.com/consumers/](`Azure Digital Twins`) **Azure Digital Twins** API を選択します。

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
