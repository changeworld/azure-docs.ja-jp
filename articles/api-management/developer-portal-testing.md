---
title: セルフホステッド開発者ポータルをテストする
titleSuffix: Azure API Management
description: セルフホステッド型の API Management ポータルの単体テストとエンド ツー エンドのテストを設定する方法について説明します。
author: dlepow
ms.author: danlep
ms.date: 03/25/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: 62c5ab98e067bb735bcfa1510cae405b17461cac
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128646441"
---
# <a name="test-the-self-hosted-developer-portal"></a>セルフホステッド開発者ポータルをテストする

この記事では、[セルフホステッド ポータル](developer-portal-self-host.md)の単体テストとエンド ツー エンドのテストを設定する方法について説明します。

## <a name="unit-tests"></a>単体テスト

単体テストは、小規模な機能を検証するためのアプローチです。 これはアプリケーションの他の部分から隔離されて実行されます。

### <a name="example-scenario"></a>サンプル シナリオ

このシナリオでは、パスワード入力コントロールをテストします。 少なくとも次のものを含むパスワードのみを受け入れます。

- 1 つの文字

- 1 つの数値

- 1 つの特殊文字
 
そのため、これらの要件を検証するテストは次のようになります。

```typescript
const passwordInput = new PasswordInput();

passwordInput.value = "";
expect(passwordInput.isValid).to.equal(false);

passwordInput.value = "password";
expect(passwordInput.isValid).to.equal(false);

passwordInput.value = "p@ssw0rd";
expect(passwordInput.isValid.to.equal(true);
```
 
### <a name="project-structure"></a>プロジェクト構造

一般的には、検証する必要があるコンポーネントの横に単体テストを保持します。

```console
component.ts
component.spec.ts
```

### <a name="mock-http-requests"></a>HTTP 要求の模擬テストを実行する

コンポーネントで HTTP 要求が行われることが予想される場合もあります。 コンポーネントは、さまざまな種類の応答に適切に対応する必要があります。 特定の HTTP 応答をシミュレートするには、`MockHttpClient` を使用します。 これは、プロジェクトの他の多くのコンポーネントで使用される `HttpClient` インターフェイスを実装します。

```typescript
const httpClient = new MockHttpClient();

httpClient.mock()
    .get("/users/jane")
    .reply(200, {
        firstName: "Jane",
        lastName: "Doe"
    });
```

## <a name="end-to-end-tests"></a>エンド ツー エンドのテスト

エンド ツー エンドのテストでは、ユーザーが実行することが想定される正確な手順を実行する特定のユーザー シナリオを実行します。Azure API Management 開発者ポータルなどの Web アプリケーションでは、ユーザーはコンテンツをスクロールしてオプションを選択し、特定の結果に到達します。 

ユーザーの移動を複製するには、[Puppeteer](https://github.com/puppeteer/puppeteer) のようなブラウザー操作ヘルパー ライブラリを使用します。 これにより、ユーザーの操作をシミュレートし、想定されるシナリオを自動化できます。 さらに Puppeteer では、テストのあらゆる段階でのページまたはコンポーネントのスクリーンショットを自動的に取得します。 これらを後で前の結果と比較して、差異や不具合の可能性を検出します。

### <a name="example-scenario"></a>サンプル シナリオ

このシナリオでは、ユーザーのサインイン フローを検証する必要があります。 このシナリオでは次の手順が必要です。

1. ブラウザーを開き、サインイン ページに移動します。

1. メール アドレスを入力します。

1. パスワードを入力します。

1. **[サインイン]** を選択します。

1. ユーザーがホーム ページにリダイレクトされたことを確認します。

1. ページに **[プロファイル]** メニュー項目が表示されていることを確認します。 これは、正常にサインインしたことの指標の 1 つです。

テストを自動的に実行するには、まったく同じ手順でスクリプトを作成します。

```typescript
// 1. Open browser and navigate to the sign-in page.
const page = await browser.newPage();
await page.goto("https://contoso.com/signin");

// 2. Enter email.
await this.page.type("#email", "john.doe@contoso.com");

// 3. Enter password.
await this.page.type("#password", "p@s$w0rd");

// 4. Click Sign-in.
await this.page.click("#signin");

// 5. Verify that user got redirected to Home page.
expect(page.url()).to.equal("https://contoso.com");

// 6. Verify that the page includes the Profile menu item.
const profileMenuItem = await this.page.$("#profile");
expect(profileMenuItem).not.equals(null);
```

> [!NOTE]
> "#email"、"#password"、"#signin" などの文字列は、ページ上の HTML 要素を識別する CSS に似たセレクターです。 詳細については、[セレクター レベル 3](https://www.w3.org/TR/selectors-3/) の W3C 仕様を参照してください。

### <a name="ui-component-maps"></a>UI コンポーネント マップ

ユーザー フローでは多くの場合、同じページまたはコンポーネントを経由します。 良い例として、すべてのページに存在する Web サイトのメイン メニューがあります。 

同じセレクターが毎回のテストで構成および更新されないようにするために、UI コンポーネント マップを作成します。 たとえば、前の例の手順 2 から 6 を、次のわずか 2 行に置き換えることができます。

```typescript
const signInWidget = new SigninBasicWidget(page);
await signInWidget.signInWithBasic({ email: "...", password: "..." });
```

### <a name="test-configuration"></a>テスト構成

シナリオによっては、事前に作成されたデータまたは構成が必要になる場合があります。 たとえば、ソーシャル メディア アカウントを使用したユーザー サインインの自動化が必要になる場合があります。 このデータをすばやく簡単に作成することは困難です。

このため、テスト シナリオに特別な構成ファイルを追加することができます。 テスト スクリプトによって、このファイルから必要なデータを取得できます。 ビルドおよびテストのパイプラインに応じて、テストでは名前が指定されたセキュリティで保護されたストアからシークレットをプルできます。

プロジェクトの `src` フォルダーに格納される `validate.config.json` の例を次に示します。

```json
{
    "environment": "validation",
    "urls": {
        "home": "https://contoso.com",
        "signin": "https://contoso.com/signin",
        "signup": "https://contoso.com/signup/"
    },
    "signin": {
        "firstName": "John",
        "lastName": "Doe",
        "credentials": {
            "basic": {
                "email": "johndoe@contoso.com",
                "password": "< password >"
            },
            "aadB2C": {
                "email": "johndoe@contoso.com",
                "password": "< password >"
            }
        }
    },
    "signup": {
        "firstName": "John",
        "lastName": "Doe",
        "credentials": {
            "basic": {
                "email": "johndoe@contoso.com",
                "password": "< password >"
            }
        }
    }
}

```

### <a name="headless-vs-normal-tests"></a>ヘッドレスと通常のテストの比較

Chrome や Microsoft Edge などの最新のブラウザーでは、ヘッドレス モードと通常モードの両方で自動化を実行できます。 ヘッドレス モードでは、ブラウザーはグラフィカル ユーザー インターフェイスを使用せずに動作します。 それでもなお、同じページとドキュメント オブジェクト モデル (DOM) の操作を実行します。 ブラウザー UI はデリバリー パイプラインには通常必要ありません。 その場合、ヘッドレス モードでテストを実行する方が優れたオプションです。

テスト スクリプトを開発するときは、ブラウザーで何が起こっているかを実際に確認すると便利です。 このときは、通常モードを使用することをお勧めします。

モードを切り替えるには、`constants.ts` ファイルの `headless` オプションを変更します。 これはプロジェクトの `tests` フォルダーにあります。

```typescript
export const LaunchOptions = {
    headless: false
};
```

もう 1 つの便利なオプションは `slowMo` です。 これはそれぞれのアクション間でテストの実行を一時停止します。

```typescript
export const LaunchOptions = {
    slowMo: 200 // milliseconds
};
```

## <a name="run-tests"></a>テストの実行

このプロジェクトでテストを実行するには、次の 2 つの組み込みの方法があります。

**npm コマンド**

```console
npm run test
```

**テスト エクスプローラー**

VS Code 用のテスト エクスプローラー拡張機能 (たとえば、[Mocha テスト エクスプローラー](https://marketplace.visualstudio.com/items?itemName=hbenl.vscode-mocha-test-adapter)) には、ソース コードのすべての変更に対して自動的にテストを実行するための便利な UI とオプションがあります。

:::image type="content" source="media/developer-portal-testing/visual-studio-code-test-explorer.png" alt-text="Visual Studio Code テスト エクスプローラーのスクリーンショット":::

## <a name="next-steps"></a>次の手順

開発者ポータルの詳細については、次を参照してください。

- [Azure API Management 開発者ポータルの概要](api-management-howto-developer-portal.md)

- [開発者ポータルのセルフホスト](developer-portal-self-host.md)
