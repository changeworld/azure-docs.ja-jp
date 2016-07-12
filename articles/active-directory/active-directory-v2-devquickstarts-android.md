<properties
	pageTitle="Azure AD v2.0 Android アプリ | Microsoft Azure"
	description="サインインに個人の Microsoft アカウントと会社/学校アカウントの両方を使用し、サード パーティのライブラリを使用して Graph API を呼び出す Android アプリの構築方法を説明します。"
	services="active-directory"
	documentationCenter=""
	authors="brandwe"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/31/2016"
	ms.author="brandwe"/>

#  サード パーティのライブラリと Graph API と v2.0 エンドポイントを使用して Android アプリにサインインを追加する

Microsoft の ID プラットフォームには、OAuth2 や OpenID Connect といったオープンな標準が使用されています。そのため開発者は、好きなライブラリを活用して Microsoft のサービスと連携させることができます。Microsoft では、そのプラットフォームを他のライブラリから使用する開発者のために、サード パーティのライブラリから Microsoft の ID プラットフォームに接続するための構成方法を紹介するチュートリアルを作成しています。この記事もそうしたチュートリアルの一つです。Microsoft の ID プラットフォームには、[RFC6749 OAuth2 仕様](https://tools.ietf.org/html/rfc6749)を実装するほとんどのライブラリから接続できます。

ユーザーは、このアプリケーションを通じて所属組織にサインインした後、Graph API を使用して組織内のユーザーを検索することができます。

ここで紹介する構成サンプルは OAuth2 や OpenID Connect に精通している読者を想定しており、それ以外の方にとっては、あまり参考にならない可能性があります。その場合は、[対応プロトコルについて簡単に解説したこちらの記事](active-directory-v2-protocols-oauth-code.md)に目を通すことをお勧めします。


> [AZURE.NOTE]
	Microsoft のプラットフォーム機能のうち、これらの標準に含まれている式が使われているいくつかの機能 (条件付きアクセスや Intune ポリシー管理) については、オープン ソースの Microsoft Azure Identity Libraries を使用する必要があります。


> [AZURE.NOTE]
	Azure Active Directory のシナリオおよび機能のすべてが v2.0 エンドポイントでサポートされているわけではありません。v2.0 エンドポイントを使用する必要があるかどうかを判断するには、[v2.0 の制限事項](active-directory-v2-limitations.md)に関するページをお読みください。


## ダウンロード
このチュートリアルのコードは、[GitHub](git@github.com:Azure-Samples/active-directory-android-native-oidcandroidlib-v2.git) で管理されています。追加の参考資料として、[アプリのスケルトン (.zip) をダウンロード](git@github.com:Azure-Samples/active-directory-android-native-oidcandroidlib-v2.git/archive/skeleton.zip)したり、スケルトンを複製したりすることができます:

```
git clone --branch skeleton git@github.com:Azure-Samples/active-directory-android-native-oidcandroidlib-v2.git
```

または単にダウンロードして作業してもかまいません。

```
git@github.com:Azure-Samples/active-directory-android-native-oidcandroidlib-v2.git
```

## アプリを登録します
[apps.dev.microsoft.com](https://apps.dev.microsoft.com) で新しいアプリを作成するか、この[詳細な手順](active-directory-v2-app-registration.md)に従います。次のことを確認します。

- アプリに割り当てられた**アプリケーション ID** をメモしておきます。これは後で必要になります。
- アプリ用の**モバイル** プラットフォームを追加します。
- ポータルから**リダイレクト URI** をメモしておきます。既定値の `https://login.microsoftonline.com/common/oauth2/nativeclient`を使用する必要があります。


## サード パーティ製ライブラリ nxoauth2 のダウンロードとワークスペースの起動

このチュートリアルでは、Google の OpenID Connect コードに基づく OAuth2 ライブラリ OIDCAndroidLib を GitHub からクローンして使用します。このライブラリは、ネイティブ アプリケーション プロファイルを実装し、エンド ユーザーの承認エンドポイントをサポートしています。これらの条件さえ整えば、Microsoft の ID プラットフォームと連携させることができます。

*  複製

まず、OIDCAndroidLib リポジトリをローカル コンピューターにクローンします。

```
git@github.com:kalemontes/OIDCAndroidLib.git
```

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/emotes-url.png)

### Android Studio 環境の設定

*  プロジェクトを作成します。新しい AndroidStudio プロジェクトを作成し、既定のウィザードの指示に従います。

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample1.PNG)

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample2.PNG)

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample3.PNG)

*  プロジェクト モジュールのセットアップを行います。モジュールを設定する方法としては、クローンしたリポジトリをプロジェクトの場所に移動するのがおそらく最も簡単です。最初にプロジェクトを作成してから直接プロジェクトの場所にクローンしてもかまいません。

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample4_1.PNG)

次に、コンテキスト メニューまたはショートカット キー (`Ctrl + Alt + Maj + S`) を使用してプロジェクト モジュールの設定を開きます。

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample4.PNG)

既定のアプリ モジュールを削除します。必要なのはプロジェクト コンテナーの設定だけです。

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample5.PNG)

今度は、クローンしたリポジトリのモジュールを現在のプロジェクトにインポートする必要があります。

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample6.PNG) ![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample7.PNG)

> 以上の手順を `oidlib-sample` モジュールについても行います。

oidclib の `oidlib-sample` モジュールへの依存関係を確認します。

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample8.PNG)

[OK] をクリックして gradle sync の完了を待ちます。

この時点での settings.gradle の内容は次のとおりです。

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample8_1.PNG)

*  サンプル アプリをビルドして、サンプルが正しく実行されていることを確認します。

これをそのまま Azure Active Directory で使用することはできません。先にエンドポイントの構成を行う必要があります。こうすることで Android Studio の問題を確実に回避したうえで、サンプル アプリのカスタマイズに着手することができます。

Android Studio で `oidlib-sample` をターゲットとしてビルド、実行します。

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample9.png)

*  クリーンアップ

プロジェクトからモジュールを削除したときに残った `app ` ディレクトリは、AndroidStudio では安全のために残されますが、削除しても問題ありません。

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample12.PNG)

また、プロジェクトからモジュールを削除するときに残った実行構成も、[Edit Configurations (構成の編集)] メニューを開いて削除してください。

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample10.PNG) ![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample11.PNG)

## サンプルのエンドポイントの構成

`oidlib-sample` が正常に実行されることを確認したら、いくつかのエンドポイントを編集して、Azure Active Directory と連携させましょう。

* クライアントの構成

`oidc_clientconf.xml` ファイルを開いて次の変更を加えます。

1. OAuth2 のフローのみを使用してトークンを取得し、Graph API を呼び出すため、クライアントは OAuth2 のみに設定しましょう。OIDC の使用例については、また別の機会に紹介します。

```xml
    <bool name="oidc_oauth2only">true</bool>
```

2. 登録ポータルから取得したクライアント ID を構成します。

```xml
    <string name="oidc_clientId">86172f9d-a1ae-4348-aafa-7b3e5d1b36f5</string>
    <string name="oidc_clientSecret"></string>
```

3. 登録ポータルから取得したリダイレクト URI を構成します。

```xml
    <string name="oidc_redirectUrl">https://login.microsoftonline.com/common/oauth2/nativeclient</string>
```

4. Graph API にアクセスするために必要なスコープを構成します。

```xml
    <string-array name="oidc_scopes">
        <item>openid</item>
        <item>User.ReadBasic.All</item>
        <item>offline_access</item>
    </string-array>
```

ここでは、`oidc_scopes` を設定します。このアプリケーションに関して要求するスコープは `User.ReadBasic.All` です。ディレクトリ内のすべてのユーザーの基本的なプロファイルを読み取ることができます。Microsoft Graph で使用できるすべてのスコープについて詳しくは、[こちら](https://graph.microsoft.io/docs/authorization/permission_scopes)を参照してください。

OpenID Connect のスコープとしての `openid` または `offline_access` について詳しい解説が必要な場合は、[対応プロトコルについて簡単に解説したこちらの記事](active-directory-v2-protocols-oauth-code.md)を参照してください。

* クライアント エンドポイントの構成

`oidc_endpoints.xml` ファイルを開いて次の変更を加えます。

```xml
<!-- Stores OpenID Connect provider endpoints. -->
<resources>
    <string name="op_authorizationEnpoint">https://login.microsoftonline.com/common/oauth2/v2.0/authorize</string>
    <string name="op_tokenEndpoint">https://login.microsoftonline.com/common/oauth2/v2.0/token</string>
    <string name="op_userInfoEndpoint">https://www.example.com/oauth2/userinfo</string>
    <string name="op_revocationEndpoint">https://www.example.com/oauth2/revoketoken</string>
</resources>
```

プロトコルとして OAuth2 を使用している場合、これらのエンドポイントは決して変わりません。

> [AZURE.NOTE] 
Azure Active Directory では、`userInfoEndpoint` と `revocationEndpoint` のエンドポイントが現在サポートされていません。そのため、これらのエンドポイントについては既定値 (example.com) をそのまま使用しています。サンプル コードにはこの点が記載されていないのでご注意ください。


## Graph API 呼び出しの構成

`HomeActivity.java` ファイルを開いて次の変更を加えます。

```Java
   //TODO: set your protected resource url
    private static final String protectedResUrl = "https://graph.microsoft.com/v1.0/me/";
```

これは、単純な Graph API 呼び出しによって情報を取得する例です。

## 最後に

必要な変更は、これですべて完了です。 アプリケーション `oidlib-sample` を実行し、サインイン ボタンをクリックしてください。O

本人確認に成功したら、[Request Protected Resource (保護されたリソースの要求)] ボタンを押して、Graph API 呼び出しをテストしてください。

## Microsoft 製品のセキュリティ更新プログラムの取得

セキュリティの問題が発生したときに通知を受け取ることをお勧めします。そのためには、[このページ](https://technet.microsoft.com/security/dd252948)にアクセスし、セキュリティ アドバイザリ通知の受信登録をしてください。

<!---HONumber=AcomDC_0629_2016-->