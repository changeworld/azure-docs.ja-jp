---
title: インクルード ファイル
description: インクルード ファイル
services: azure-communication-services
author: ddematheu2
manager: chpalm
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 9/1/2020
ms.topic: include
ms.custom: include file
ms.author: dademath
ms.openlocfilehash: c6e8be5462e0caffec7a1c88dae54f3f818ec323
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2021
ms.locfileid: "106498794"
---
[!INCLUDE [Public Preview Notice](../../includes/public-preview-include-android-ios.md)]


Azure Communication Services の **Android 用のグループ通話のヒーロー サンプル** は、Communication Services Calling Android SDK を使用して、音声とビデオを含むグループ通話エクスペリエンスを構築する方法を示しています。 このサンプル クイックスタートでは、サンプルを設定して実行する方法について説明します。 背景情報については、サンプルの概要を参照してください。

## <a name="download-code"></a>コードをダウンロードする

[GitHub](https://github.com/Azure-Samples/communication-services-android-calling-hero) でこのサンプルのプロジェクトを見つけてください。 [Teams の相互運用機能](../../concepts/teams-interop.md)を使用したサンプルのバージョンは、別の[ブランチ](https://github.com/Azure-Samples/communication-services-android-calling-hero/tree/feature/teams_interop)にあります。

## <a name="overview"></a>概要

このサンプルはネイティブ Android アプリケーションであり、音声通話とビデオ通話の両機能を備えた通話エクスペリエンスを実現するために、Azure Communication Services Android SDK が使用されています。 このアプリケーションは、サーバー側コンポーネントを使用してアクセス トークンをプロビジョニングします。それを使用して、Azure Communication Services SDK が初期化されます。 サーバー側コンポーネントを構成する際は、[Azure Functions を使用した信頼できるサービス](../../tutorials/trusted-service-tutorial.md)についてのチュートリアルに従ってください。

サンプルは次のようになります。

:::image type="content" source="../media/calling/landing-page-android.png" alt-text="サンプル アプリケーションのランディング ページを示すスクリーンショット。":::

[Start new call]\(新規通話を開始\) ボタンを押すと、Android アプリケーションによって新規通話が作成され、それに参加します。 このアプリケーションでは、既存の通話の ID を指定することで、Azure Communication Services の既存の通話に参加することもできます。

通話に参加すると、カメラとマイクへのアクセスをアプリケーションに許可するよう求められます。 さらに、表示名を入力するように求められます。

:::image type="content" source="../media/calling/pre-call-android.png" alt-text="サンプル アプリケーションの通話開始前の画面を示すスクリーンショット。":::

表示名とデバイスを構成すると、通話に参加できるようになります。 メインの通話キャンバスが表示され、通話のコア エクスペリエンスが提供されます。

:::image type="content" source="../media/calling/main-app-android.png" alt-text="サンプル アプリケーションのメイン画面を示すスクリーンショット。":::

通話のメイン画面のコンポーネントは以下のとおりです。

- **メディア ギャラリー**: 参加者が表示されるメイン領域。 参加者のカメラが有効になっている場合は、そのビデオ フィードがここに表示されます。 参加者ごとに個別のタイルがあり、その表示名とビデオ ストリーム (存在する場合) が表示されます。 ギャラリーでは複数の参加者がサポートされており、通話への参加者が追加または削除されると更新されます。
- **アクション バー**: 主要な通話コントロールが配置される場所です。 これらのコントロールを使用して、ビデオとマイクのオンとオフを切り替えたり、画面を共有したり、通話を終了したりすることができます。

以下で、サンプルを設定するための前提条件と手順について詳しく説明します。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 詳細については、[アカウントの無料作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)に関するページを参照してください。
- お使いのコンピューターで実行されている [Android Studio](https://developer.android.com/studio)
- Azure Communication Services リソース。 詳細については、[Azure Communication リソースの作成](../../quickstarts/create-communication-resource.md)に関するページを参照してください。
- [認証エンドポイント](../../tutorials/trusted-service-tutorial.md)を実行してアクセス トークンをフェッチする Azure 関数。

## <a name="running-sample-locally"></a>サンプルをローカルで実行する

グループ通話サンプルは、Android Studio を使用してローカルで実行することができます。 開発者は、各自の物理デバイスまたはエミュレーターを使用してアプリケーションをテストできます。

### <a name="before-running-the-sample-for-the-first-time"></a>サンプルを初めて実行する前

1. Android Studio を開き、`Open an Existing Project` を選択します
2. サンプルのダウンロードされたリリース内の `AzureCalling` フォルダーを開きます。
3. app/assets を展開して `appSettings.properties` を更新します。 キー `communicationTokenFetchUrl` の値を、前提条件として設定された認証エンドポイントの URL に設定します。

### <a name="run-sample"></a>サンプルを実行する

Android Studio でサンプルをビルドして実行します。

## <a name="optional-securing-an-authentication-endpoint"></a>(省略可) 認証エンドポイントのセキュリティを確保する

このサンプルはデモンストレーション上、既定では、パブリックにアクセスできるエンドポイントを使用して、Azure Communication Services トークンをフェッチします。 運用のシナリオでは、セキュリティを確保した独自のエンドポイントを使用して独自のトークンをプロビジョニングすることをお勧めします。

別途構成を行うことにより、このサンプルは、**Azure Active Directory** (Azure AD) で保護されたエンドポイントへの接続をサポートし、Azure Communication Services のトークンをフェッチするアプリにユーザー ログインを要求することができます。 以下の手順を参照してください。

1. アプリの Azure Active Directory 認証を有効にします。  
   - [アプリを Azure Active Directory に登録する (Android プラットフォームの設定を使用)](../../../active-directory/develop/tutorial-v2-android.md) 
    - [Azure AD ログインを使用するように App Service または Azure Functions アプリを構成する](../../../app-service/configure-authentication-provider-aad.md)

2. Azure Active Directory の [アプリの登録] から、登録済みアプリの概要ページに移動します。 `Package name`、`Signature hash`、`MSAL Configutaion` を書き留めておきます。

:::image type="content" source="../media/calling/aad-overview-android.png" alt-text="Azure portal における Azure Active Directory の構成。":::

3. `AzureCalling/app/src/main/res/raw/auth_config_single_account.json` を編集し、`isAADAuthEnabled` を設定して Azure Active Directory を有効にします
4. `AndroidManifest.xml` を編集し、`android:path` をキーストア署名ハッシュに設定します。 (省略可能、 現在の値では、バンドルされた debug.keystore からのハッシュが使用されます。 別のキーストアを使用する場合は、これを更新する必要があります)。
   ```
   <activity android:name="com.microsoft.identity.client.BrowserTabActivity">
            <intent-filter>
                <action android:name="android.intent.action.VIEW" />
                <category android:name="android.intent.category.DEFAULT" />
                <category android:name="android.intent.category.BROWSABLE" />
                <data
                    android:host="com.azure.samples.communication.calling"
                    android:path="/Signature hash" <!-- do not remove /. The current hash in AndroidManifest.xml is for debug.keystore. -->
                    android:scheme="msauth" />
            </intent-filter>
        </activity>
   ```
5. MSAL Android の構成を Azure portal からコピーし、`AzureCalling/app/src/main/res/raw/auth_config_single_account.json` に貼り付けます。 "account_mode" : "SINGLE" を含めます
   ```
      {
         "client_id": "",
         "authorization_user_agent": "DEFAULT",
         "redirect_uri": "",
         "account_mode" : "SINGLE",
         "authorities": [
            {
               "type": "AAD",
               "audience": {
               "type": "AzureADMyOrg",
               "tenant_id": ""
               }
            }
         ]
      }
   ```

6. `AzureCalling/app/src/main/res/raw/auth_config_single_account.json` を編集し、キー `communicationTokenFetchUrl` の値を、セキュリティで保護されている認証エンドポイントの URL に設定します。
7. `AzureCalling/app/src/main/res/raw/auth_config_single_account.json` を編集し、`Azure Active Directory` `Expose an API` スコープのキー `aadScopes` の値を設定します

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Communication Services サブスクリプションをクリーンアップして解除する場合は、リソースまたはリソース グループを削除できます。 リソース グループを削除すると、それに関連付けられている他のリソースも削除されます。 詳細については、[リソースのクリーンアップ](../../quickstarts/create-communication-resource.md#clean-up-resources)に関する記事を参照してください。

## <a name="next-steps"></a>次の手順

>[!div class="nextstepaction"]
>[GitHub からサンプルをダウンロードする](https://github.com/Azure-Samples/communication-services-android-calling-hero)

詳細については、次の記事を参照してください。

- [Calling SDK の使用法](../../quickstarts/voice-video-calling/calling-client-samples.md)について理解する
- [通話のしくみ](../../concepts/voice-video-calling/about-call-types.md)の詳細について確認する

### <a name="additional-reading"></a>その他の情報

- [Azure Communication GitHub](https://github.com/Azure/communication) - 他の例や情報は公式 GitHub ページで見つかります。
- [サンプル](./../overview.md) - その他のサンプルと例については、「サンプルの概要」ページを参照してください。
- [Azure Communication の通話機能](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/calling-sdk-features) - Calling Android SDK の詳細 - [Azure Communication Android Calling SDK](https://search.maven.org/artifact/com.azure.android/azure-communication-calling)
