---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 11/25/2018
ms.author: crdun
ms.openlocfilehash: deb94cab97bd9a402676cdc5c0239da8d07ed8b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "67181863"
---
前の例では、標準のサインインを示しました。標準のサインインでは、アプリケーションが開始されるたびに、クライアントは ID プロバイダーとバックエンド Azure サービスの両方にアクセスする必要があります。 この方法は非効率であり、多くの顧客が同時にアプリケーションを開始しようとした場合に使用率に関連する問題が発生する場合があります。 よって、Azure サービスから返される承認トークンをキャッシュし、最初にその承認トークンの使用を試してから、プロバイダー ベースのサインインを使用する方が効果的です。

> [!NOTE]
> クライアントによって管理される認証とサービスによって管理される認証のどちらを使用する場合でも、バックエンド Azure サービスが発行したトークンをキャッシュできます。 このチュートリアルでは、サービスによって管理される認証を使用します。
>
>

1. ToDoActivity.java ファイルを開き、次の import ステートメントを追加します。

    ```java
    import android.content.Context;
    import android.content.SharedPreferences;
    import android.content.SharedPreferences.Editor;
    ```

2. 次のメンバーをクラス `ToDoActivity` クラスに追加します。

    ```java
    public static final String SHAREDPREFFILE = "temp";
    public static final String USERIDPREF = "uid";
    public static final String TOKENPREF = "tkn";
    ```

3. ToDoActivity.java ファイル内で、次の `cacheUserToken` メソッドの定義を追加します。

    ```java
    private void cacheUserToken(MobileServiceUser user)
    {
        SharedPreferences prefs = getSharedPreferences(SHAREDPREFFILE, Context.MODE_PRIVATE);
        Editor editor = prefs.edit();
        editor.putString(USERIDPREF, user.getUserId());
        editor.putString(TOKENPREF, user.getAuthenticationToken());
        editor.commit();
    }
    ```

    このメソッドでは、プライベートとマークされた設定ファイルに、ユーザー ID とトークンを格納します。 これにより、キャッシュへのアクセスが保護され、デバイスの他のアプリケーションはトークンにアクセスできなくなります。 設定は、アプリケーション用にサンドボックス化されます。 ただし、他のユーザーは、デバイスにアクセスできるようになると、他の手段によってトークン キャッシュにアクセスできる可能性があります。

   > [!NOTE]
   > トークンによるデータへのアクセスの秘密性が高く、他のユーザーがそのデバイスにアクセスする可能性がある場合は、暗号化によりトークンの保護を強化できます。 ただし、完全に安全なソリューションはこのチュートリアルの範囲を超えており、またセキュリティの要件によって異なります。
   >
   >

4. ToDoActivity.java ファイル内で、次の `loadUserTokenCache` メソッドの定義を追加します。

    ```java
    private boolean loadUserTokenCache(MobileServiceClient client)
    {
        SharedPreferences prefs = getSharedPreferences(SHAREDPREFFILE, Context.MODE_PRIVATE);
        String userId = prefs.getString(USERIDPREF, null);
        if (userId == null)
            return false;
        String token = prefs.getString(TOKENPREF, null);
        if (token == null)
            return false;

        MobileServiceUser user = new MobileServiceUser(userId);
        user.setAuthenticationToken(token);
        client.setCurrentUser(user);

        return true;
    }
    ```

5. *ToDoActivity.java* ファイルで、`authenticate` メソッドと `onActivityResult` メソッドを、トークン キャッシュを使う次のメソッドに置き換えます。 Google 以外のアカウントを使用する場合は、ログイン プロバイダーを変更します。

    ```java
    private void authenticate() {
        // We first try to load a token cache if one exists.
        if (loadUserTokenCache(mClient))
        {
            createTable();
        }
        // If we failed to load a token cache, sign in and create a token cache
        else
        {
            // Sign in using the Google provider.
            mClient.login(MobileServiceAuthenticationProvider.Google, "{url_scheme_of_your_app}", GOOGLE_LOGIN_REQUEST_CODE);
        }
    }

    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        // When request completes
        if (resultCode == RESULT_OK) {
            // Check the request code matches the one we send in the sign-in request
            if (requestCode == GOOGLE_LOGIN_REQUEST_CODE) {
                MobileServiceActivityResult result = mClient.onActivityResult(data);
                if (result.isLoggedIn()) {
                    // sign-in succeeded
                    createAndShowDialog(String.format("You are now signed in - %1$2s", mClient.getCurrentUser().getUserId()), "Success");
                    cacheUserToken(mClient.getCurrentUser());
                    createTable();
                } else {
                    // sign-in failed, check the error message
                    String errorMessage = result.getErrorMessage();
                    createAndShowDialog(errorMessage, "Error");
                }
            }
        }
    }
    ```

6. アプリケーションをビルドし、有効なアカウントを使用して認証をテストします。 最低 2 回アプリケーションを実行します。 最初の実行で、サインインとトークン キャッシュの作成を求めるプロンプトが表示されます。 それ以降、実行ごとに、認証のためのトークン キャッシュの読み込みが試行されます。 サインインする必要がなくなります。
