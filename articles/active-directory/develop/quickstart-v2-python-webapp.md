---
title: 'クイックスタート: Python Web アプリに "Microsoft アカウントでサインイン" を追加する | Azure'
titleSuffix: Microsoft identity platform
description: このクイックスタートでは、Python Web アプリでのユーザーのサインイン、Microsoft ID プラットフォームからのアクセス トークンの取得、および Microsoft Graph API の呼び出しを行う方法について説明します。
services: active-directory
author: abhidnya13
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: portal
ms.workload: identity
ms.date: 11/22/2021
ROBOTS: NOINDEX
ms.author: abpati
ms.custom: aaddev, devx-track-python, "scenarios:getting-started", "languages:Python", mode-api
ms.openlocfilehash: 871377543dc91859cf608620436ad311f742f824
ms.sourcegitcommit: 3f20f370425cb7d51a35d0bba4733876170a7795
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/27/2022
ms.locfileid: "137803705"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-python-web-app"></a>クイック スタート: Python Web アプリに Microsoft でサインインを追加する

このクイックスタートでは、Python Web アプリケーションでユーザーをサインインし、アクセス トークンを取得して Microsoft Graph API を呼び出す方法を示すコード サンプルをダウンロードして実行します。 個人用 Microsoft アカウントまたは Azure Active Directory (Azure AD) 組織のアカウントを持つユーザーは、アプリケーションにサインインできます。

図については、「[このサンプルのしくみ](#how-the-sample-works)」を参照してください。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [Python 2.7 以降](https://www.python.org/downloads/release/python-2713)または [Python 3 以降](https://www.python.org/downloads/release/python-364/)
- [Flask](http://flask.pocoo.org/)、[Flask-Session](https://pypi.org/project/Flask-Session/)、[要求](https://requests.kennethreitz.org/en/master/)
- [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python)

#### <a name="step-1-configure-your-application-in-azure-portal"></a>手順 1:Azure portal でのアプリケーションの構成

このクイックスタートのコード サンプルを動作させるには、次のことを行います。

1. 応答 URL を `http://localhost:5000/getAToken` として追加する
1. クライアント シークレットを作成します。
1. Microsoft Graph API の委任されたアクセス許可 User.ReadBasic.All を追加します。

> [!div class="nextstepaction"]
> [これらの変更を行います]()

> [!div class="alert alert-info"]
> ![構成済み](./media/quickstart-v2-aspnet-webapp/green-check.png) アプリケーションはこの属性で構成されています

#### <a name="step-2-download-your-project"></a>手順 2:プロジェクトのダウンロード

プロジェクトをダウンロードし、ルート フォルダーに近いローカル フォルダー (例: **C:\Azure-Samples**) に ZIP ファイルを展開します。
> [!div class="nextstepaction"]
> [コード サンプルをダウンロードします](https://github.com/Azure-Samples/ms-identity-python-webapp/archive/master.zip)

> [!NOTE]
> `Enter_the_Supported_Account_Info_Here`

#### <a name="step-3-run-the-code-sample"></a>手順 3:コード サンプルの実行

1. MSAL Python ライブラリ、Flask フレームワーク、サーバー側のセッション管理用の Flask-Sessions、および requests を、次のように pip を使用してインストールする必要があります。

    ```shell
    pip install -r requirements.txt
    ```

2. シェルまたはコマンド ラインから `app.py` を実行します。

    ```shell
    python app.py
    ```

   > [!IMPORTANT]
   > このクイック スタート アプリケーションは、クライアント シークレットを使用して、それ自体を機密クライアントとして識別します。 クライアント シークレットはプロジェクト ファイルにプレーン テキストとして追加されるため、セキュリティ上の理由から、アプリケーションを運用アプリケーションと見なす前に、クライアント シークレットの代わりに証明書を使用することをお勧めします。 証明書の使用方法の詳細については、[これらの手順](./active-directory-certificate-credentials.md)を参照してください。

## <a name="more-information"></a>詳細情報

### <a name="how-the-sample-works"></a>このサンプルのしくみ
![このクイック スタートで生成されたサンプル アプリの動作の紹介](media/quickstart-v2-python-webapp/python-quickstart.svg)

### <a name="getting-msal"></a>MSAL の取得
MSAL は、ユーザーをサインインさせるために使用されたり、Microsoft ID プラットフォームによって保護されている API にアクセスするためのトークンを要求するために使用されたりするライブラリです。
MSAL Python は、Pip を使用してアプリケーションに追加できます。

```Shell
pip install msal
```

### <a name="msal-initialization"></a>MSAL の初期化
MSAL を使用するファイルの先頭に次のコードを追加すると、MSAL Python への参照を追加できます。

```Python
import msal
```

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>次のステップ

複数のパートで構成されるシナリオ シリーズで、ユーザーのサインインを行う Web アプリの詳細について確認します。

> [!div class="nextstepaction"]
> [シナリオ: ユーザーをサインインさせる Web アプリ](scenario-web-app-sign-user-overview.md)
