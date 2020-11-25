---
title: Xamarin Android のコード構成とトラブルシューティング (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Xamarin Android を Microsoft Authentication Library for .NET (MSAL.NET) と共に使用する際の考慮事項について説明します。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 320d48535c4792a4d610888c6a7030568ccf16bc
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/25/2020
ms.locfileid: "95995184"
---
# <a name="configuration-requirements-and-troubleshooting-tips-for-xamarin-android-with-msalnet"></a>MSAL.NET を使用した Xamarin Android の構成要件とトラブルシューティングのヒント

Microsoft Authentication Library for .NET (MSAL.NET) で Xamarin Android を使用する場合、コードでいくつかの構成変更を行う必要があります。 以下のセクションで必要な変更について説明した後、最もよくある問題のいくつかを回避するのに役立つ[トラブルシューティング](#troubleshooting)のセクションが続きます。

## <a name="set-the-parent-activity"></a>親アクティビティを設定する

Xamarin Android で、対話後にトークンが戻るように親アクティビティを設定します。

```csharp
var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .ExecuteAsync();
```

MSAL.NET 4.2 以降では、この機能を [PublicClientApplication][PublicClientApplication] のレベルで設定することもできます。 これを行うには、コールバックを使用します。

```csharp
// Requires MSAL.NET 4.2 or later
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => parentActivity)
  .Build();
```

[CurrentActivityPlugin](https://github.com/jamesmontemagno/CurrentActivityPlugin) を使用すると、[`PublicClientApplication`][PublicClientApplication] ビルダー コードは、次のコード スニペットのようになります。

```csharp
// Requires MSAL.NET 4.2 or later
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```

## <a name="ensure-that-control-returns-to-msal"></a>MSAL に確実に制御が戻るようにする

認証フローの対話部分が終了したら、MSAL に制御を戻すために [`Activity`][Activity].[`OnActivityResult()`][OnActivityResult] メソッドをオーバーライドします。

オーバーライドで、MSAL.NET の `AuthenticationContinuationHelper`.`SetAuthenticationContinuationEventArgs()` メソッドを呼び出して、認証フローの対話部の最後で MSAL に制御を戻します。

```csharp
protected override void OnActivityResult(int requestCode,
                                         Result resultCode,
                                         Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);

    // Return control to MSAL
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode,
                                                                            resultCode,
                                                                            data);
}
```

## <a name="update-the-android-manifest"></a>Android マニフェストを更新する

*AndroidManifest.xml* ファイルには、次の値が含まれている必要があります。

```XML
  <!--Intent filter to capture System Browser or Authenticator calling back to our app after sign-in-->
  <activity
        android:name="microsoft.identity.client.BrowserTabActivity">
     <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="msauth"
                android:host="Enter_the_Package_Name"
                android:path="/Enter_the_Signature_Hash" />
     </intent-filter>
  </activity>
```

`android:host=` 値を、Azure portal で登録したパッケージ名に置き換えます。 `android:path=` 値を、Azure portal で登録したキー ハッシュに置き換えます。 署名ハッシュは、URL エンコードであっては "*なりません*"。 署名ハッシュの先頭にスラッシュ (`/`) があることを確認します。

または、*AndroidManifest.xml* を手動で編集するのではなく、[コード でアクティビティを作成](/xamarin/android/platform/android-manifest#the-basics)します。 コードでアクティビティを作成するには、まず、`Activity` 属性と `IntentFilter` 属性を含むクラスを作成します。

XML ファイルの値を表すクラスの例を次に示します。

```csharp
  [Activity]
  [IntentFilter(new[] { Intent.ActionView },
        Categories = new[] { Intent.CategoryBrowsable, Intent.CategoryDefault },
        DataHost = "auth",
        DataScheme = "msal{client_id}")]
  public class MsalActivity : BrowserTabActivity
  {
  }
```

### <a name="xamarinforms-43x-manifest"></a>Xamarin.Forms 4.3.x マニフェスト

Xamarin.Forms 4.3.x では、`package` 属性を `com.companyname.{appName}` に設定するコードが *AndroidManifest.xml* 内に生成されます。 `DataScheme` を `msal{client_id}` として使用する場合は、値を `MainActivity.cs` 名前空間の値と同じになるように変更することをお勧めします。

## <a name="use-the-embedded-web-view-optional"></a>埋め込み Web ビューを使用する (省略可能)

既定では、MSAL.NET ではシステム Web ブラウザーが使用されます。 このブラウザーを使用すると、Web アプリケーションやその他のアプリを使用してシングル サインオン (SSO) を取得できます。 まれなケースですが、システムに埋め込み Web ビューを使用させることもできます。

このコード例は、埋め込み Web ビューを設定する方法を示しています。

```csharp
bool useEmbeddedWebView = !app.IsSystemWebViewAvailable;

var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .WithEmbeddedWebView(useEmbeddedWebView)
 .ExecuteAsync();
```

詳細については、「[MSAL.NET で Web ブラウザーを使用する](msal-net-web-browsers.md)」と「[Xamarin Android のシステム ブラウザーに関する考慮事項](msal-net-system-browser-android-considerations.md)」を参照してください。

## <a name="troubleshooting"></a>トラブルシューティング

### <a name="general-tips"></a>一般的なヒント

- 既存の MSAL.NET NuGet パッケージを、[最新バージョンの MSAL.NET](https://www.nuget.org/packages/Microsoft.Identity.Client/) に更新します。
- Xamarin.Forms が最新バージョンであることを確認します。
- Xamarin.Android.Support.v4 が最新バージョンになっていることを確認します。
- すべての Xamarin.Android.Support パッケージで最新バージョンがターゲットになっていることを確認します。
- アプリケーションをクリーニングしてリビルドします。
- Visual Studio で、並列してビルドするプロジェクトの最大数を **1** に設定します。 これを行うには、 **[オプション]**  >  **[プロジェクトとソリューション]**  >  **[ビルドして実行]**  >  **[Maximum number of parallel projects builds]\(並列プロジェクトのビルドの最大数\)** を選択します。
- コマンド ラインからビルドしているときに、コマンドで `/m` を使用している場合は、コマンドからこの要素を削除してみてください。

### <a name="error-the-name-authenticationcontinuationhelper-doesnt-exist-in-the-current-context"></a>エラー:現在のコンテキストに AuthenticationContinuationHelper という名前が存在しません

現在のコンテキストに `AuthenticationContinuationHelper` が存在しないことを示すエラーが発生した場合は、Visual Studio による *Android.csproj\** ファイルの更新が間違っている可能性があります。 `<HintPath>` 要素のファイル パスに `monoandroid90` ではなく `netstandard13` が誤って含まれている場合があります。

この例には、正しいファイル パスが含まれています。

```xml
<Reference Include="Microsoft.Identity.Client, Version=3.0.4.0, Culture=neutral, PublicKeyToken=0a613f4dd989e8ae,
           processorArchitecture=MSIL">
  <HintPath>..\..\packages\Microsoft.Identity.Client.3.0.4-preview\lib\monoandroid90\Microsoft.Identity.Client.dll</HintPath>
</Reference>
```

## <a name="next-steps"></a>次のステップ

詳細については、[Microsoft ID プラットフォームを使用する Xamarin モバイル アプリケーション](https://github.com/azure-samples/active-directory-xamarin-native-v2#android-specific-considerations)のサンプルを参照してください。 次の表は、README ファイルの関連情報をまとめたものです。

| サンプル | プラットフォーム | 説明 |
| ------ | -------- | ----------- |
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin.iOS、Android、UWP | MSAL を使用して、Azure AD 2.0 エンドポイントを介して Microsoft の個人用アカウントおよび Azure AD を認証する方法を示すシンプルな Xamarin.Forms アプリ。 このアプリでは、Microsoft Graph にアクセスする方法と、結果として生成されるトークンについても示されてます。 <br>![認証フローの図](media/msal-net-xamarin-android-considerations/topology.png) |

<!-- REF LINKS -->
[PublicClientApplication]: /dotnet/api/microsoft.identity.client.publicclientapplication
[OnActivityResult]: /dotnet/api/android.app.activity.onactivityresult
[Activity]: /dotnet/api/android.app.activity
