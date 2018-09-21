---
title: インクルード ファイル
description: インクルード ファイル
services: active-directory
documentationcenter: dev-center-name
author: brandwe
manager: mtillman
ms.openlocfilehash: ca7a47fbe2c5ee2a4eb10abf3b9b50a2d28c252e
ms.sourcegitcommit: 06724c499837ba342c81f4d349ec0ce4f2dfd6d6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/19/2018
ms.locfileid: "46466184"
---
## <a name="register-your-application"></a>アプリケーションの登録
次の 2 つのセクションで説明する方法のいずれかを使用して、アプリケーションを登録できます。

### <a name="option-1-express-mode"></a>オプション 1: 簡易モード
次の手順に従って *Microsoft アプリケーション登録ポータル*でアプリケーションを登録する必要があります。
1. [Microsoft アプリケーション登録ポータル](https://apps.dev.microsoft.com/portal/register-app?appType=mobileAndDesktopApp&appTech=ios&step=configure)でアプリケーションを登録します。
2.  アプリケーションの名前とお使いのメール アドレスを入力します
3.  ガイド付きセットアップのオプションがオンになっていることを確認します。
4.  手順に従ってアプリケーション ID を取得し、それをコードに貼り付けます。

### <a name="option-2-advanced-mode"></a>オプション 2: 詳細モード

1.  [Microsoft アプリケーション登録ポータル](https://apps.dev.microsoft.com/portal/register-app)に進みます
2.  アプリケーションの名前を入力します
3.  ガイド付きセットアップのオプションがオフになっていることを確認します
4.  [`Add Platform`] をクリックし、[`Native Application`] を選択し、[`Save`] をクリックします。
5.  Xcode に戻ります。 `ViewController.swift` で、'`let kClientID`' で始まる行をさきほど登録したアプリケーション ID に変更します。

```swift
let kClientID = "Your_Application_Id_Here"
```

<!-- Workaround for Docs conversion bug -->
<ol start="6">
<li>
Ctrl キーを押しながら [<code>Info.plist</code>] をクリックしてコンテキスト メニューを表示し、<code>Open As</code>> <code>Source Code</code>
 をクリックします</li>
<li>
ルート ノード <code>dict</code> の下に次の内容を追加します。
</li>
</ol>

```xml
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleTypeRole</key>
        <string>Editor</string>
        <key>CFBundleURLName</key>
        <string>$(PRODUCT_BUNDLE_IDENTIFIER)</string>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>msal[Your_Application_Id_Here]</string>
        </array>
    </dict>
</array>
```
<ol start="8">
<li>
<i><code>[Your_Application_Id_Here]</code></i> を、さきほど登録したアプリケーション ID に置き換えます
</li>
</ol>
