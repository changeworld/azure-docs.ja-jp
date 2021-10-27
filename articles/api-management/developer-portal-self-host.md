---
title: 開発者ポータルをセルフホストする
titleSuffix: Azure API Management
description: API Management 開発者ポータルをセルフホストする方法について説明します。
author: dlepow
ms.author: danlep
ms.date: 04/15/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: dc709f96c44ddf6ee701493e49c006d01533ede8
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130040514"
---
# <a name="self-host-the-api-management-developer-portal"></a>API Management 開発者ポータルをセルフホストする

このチュートリアルでは、[API Management 開発者ポータル](api-management-howto-developer-portal.md)をセルフホストする方法について説明します。 セルフホストを使用すると、実行時にページを動的にカスタマイズするカスタム ロジックおよびウィジェットを使用して、開発者ポータルを柔軟に拡張できます。 さまざまな機能を使用して、API Management インスタンス用の複数のポータルをセルフホストできます。 ポータルをセルフホストする場合、ユーザーが保守を行う必要があり、アップグレードの責任を負うことになります。 

次の手順では、ローカルの開発環境を設定し、開発者ポータルで変更を行い、Azure ストレージ アカウントに対して発行してデプロイする方法を示します。

メディア ファイルがマネージド ポータルに既にアップロードされているか変更されている場合は、この記事の後の方にある「[マネージドからセルフホステッド開発者ポータルへの移行](#move-from-managed-to-self-hosted-developer-portal)」を参照してください。

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="prerequisites"></a>前提条件

ローカルの開発環境を設定するには、次のものが必要です。

- API Management サービス インスタンス。 お持ちでない場合は、「[クイック スタート - Azure API Management インスタンスの作成](get-started-create-service-instance.md)」を参照してください。
- [静的な Web サイト機能](../storage/blobs/storage-blob-static-website.md)が有効になっている Azure ストレージ アカウント。 「[ストレージ アカウントを作成する](../storage/common/storage-account-create.md)」を参照してください。
- マシン上の Git。 [こちらの Git チュートリアル](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)に従ってインストールしてください。
- マシン上の Node.js (LTS バージョン、`v10.15.0` 以降) および npm。 「[Node.js と npm のダウンロードとインストール](https://docs.npmjs.com/downloading-and-installing-node-js-and-npm)」を参照してください。
- Azure CLI。 [Azure CLI のインストール手順](/cli/azure/install-azure-cli-windows)に従ってください。

## <a name="step-1-set-up-local-environment"></a>手順 1: ローカル環境を設定する

ローカル環境を設定するには、リポジトリを複製し、開発者ポータルの最新リリースに切り替えて、npm パッケージをインストールする必要があります。

1. [api-management-developer-portal](https://github.com/Azure/api-management-developer-portal.git) レポジトリを GitHub から複製します。

    ```console
    git clone https://github.com/Azure/api-management-developer-portal.git
    ```
1. レポジトリのローカル コピーに移動します。

    ```console
    cd api-management-developer-portal
    ```

1. ポータルの最新のリリースをチェックアウトします。

    次のコードを実行する前に、[リポジトリのリリース セクション](https://github.com/Azure/api-management-developer-portal/releases)で現在のリリース タグを確認し、`<current-release-tag>` の値を最新のリリース タグに置き換えます。
    
    ```console
    git checkout <current-release-tag>
    ```

1. 使用可能な npm パッケージをインストールします。

    ```console
    npm install
    ```

> [!TIP]
> 常に[最新のポータル リリース](https://github.com/Azure/api-management-developer-portal/releases)を使用し、フォークしたポータルを最新の状態に保つようにします。 ソフトウェア エンジニアはこのリポジトリの `master` ブランチを日常の開発目的で使用します。 これにはソフトウェアの不安定なバージョンがあります。

## <a name="step-2-configure-json-files-static-website-and-cors-settings"></a>手順 2: JSON ファイル、静的な Web サイト、および CORS 設定を構成する

開発者ポータルでは、コンテンツを管理するために API Management REST API が必要です。

### <a name="configdesignjson-file"></a>config.design.json ファイル

`src` フォルダーにアクセスし、`config.design.json` ファイルを開きます。

```json
{
  "environment": "development",
  "managementApiUrl": "https://<service-name>.management.azure-api.net",
  "managementApiAccessToken": "SharedAccessSignature ...",
  "backendUrl": "https://<service-name>.developer.azure-api.net",
  "useHipCaptcha": false
}
```

ファイルを構成します。

1. `managementApiUrl` の値で、`<service-name>` を API Management インスタンスの名前で置き換えます。 [カスタム ドメイン](configure-custom-domain.md)を構成した場合は、代わりにそれを使用します (たとえば、`https://management.contoso.com`)。

    ```json
    {
    ...
    "managementApiUrl": "https://contoso-api.management.azure-api.net"
    ...
    ``` 

1. [SAS トークンを手動で作成して](/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-authentication#ManuallyCreateToken)、API Management インスタンスへの直接の REST API アクセスを有効にします。

1. 生成されたトークンをコピーし、`managementApiAccessToken` 値として貼り付けます。

1. `backendUrl` の値で、`<service-name>` を API Management インスタンスの名前で置き換えます。 [カスタム ドメイン](configure-custom-domain.md)を構成した場合は、代わりにそれを使用します (たとえば、`https://portal.contoso.com`)。

    ```json
    {
    ...
    "backendUrl": "https://contoso-api.developer.azure-api.net"
    ...
    ```

1. 開発者ポータルで CAPTCHA を有効にする場合は、「[CAPTCHA を有効にする](#enable-captcha)」を参照してください。

### <a name="configpublishjson-file"></a>config.publish.json ファイル

`src` フォルダーにアクセスし、`config.publish.json` ファイルを開きます。

```json
{
  "environment": "publishing",
  "managementApiUrl": "https://<service-name>.management.azure-api.net",
  "managementApiAccessToken": "SharedAccessSignature...",
  "useHipCaptcha": false
}
```

ファイルを構成します。

1. 前の構成ファイルの `managementApiUrl` 値と `managementApiAccessToken` 値をコピーして貼り付けます。

1. 開発者ポータルで CAPTCHA を有効にする場合は、「[CAPTCHA を有効にする](#enable-captcha)」を参照してください。

### <a name="configruntimejson-file"></a>config.runtime.json ファイル

`src` フォルダーにアクセスし、`config.runtime.json` ファイルを開きます。

```json
{
  "environment": "runtime",
  "managementApiUrl": "https://<service-name>.management.azure-api.net",
  "backendUrl": "https://<service-name>.developer.azure-api.net"
}
```

ファイルを構成します。

1. 前の構成ファイルの `managementApiUrl` 値をコピーして貼り付けます。

1. `backendUrl` の値で、`<service-name>` を API Management インスタンスの名前で置き換えます。 [カスタム ドメイン](configure-custom-domain.md)を構成した場合は、代わりにそれを使用します (たとえば、 `https://portal.contoso.com`).

    ```json
    {
    ...
    "backendUrl": "https://contoso-api.developer.azure-api.net"
    ...
    ```

### <a name="configure-the-static-website"></a>静的な Web サイトを構成する

インデックスおよびエラー ページへのルートを指定して、ストレージ アカウントの **静的な Web サイト** 機能を構成します。

1. Azure portal でストレージ アカウントに移動し、左側のメニューから **[静的な Web サイト]** を選択します。

1. **[静的な Web サイト]** ページで、 **[有効]** を選択します。

1. **[インデックス ドキュメント名]** フィールドに「*index.html*」と入力します。

1. **[エラー ドキュメントのパス]** フィールドに、「*404/index.html*」と入力します。

1. **[保存]** を選択します。

### <a name="configure-the-cors-settings"></a>CORS 設定を構成する

クロスオリジン リソース共有 (CORS) 設定を構成します。

1. Azure portal でストレージ アカウントに移動し、左側のメニューから **[CORS]** を選択します。

1. **[Blob service]** タブで、次のルールを構成します。

    | ルール | 値 |
    | ---- | ----- |
    | 許可されるオリジン | * |
    | 許可されたメソッド | すべての HTTP 動詞を選択します。 |
    | 許可されるヘッダー | * |
    | 公開されるヘッダー | * |
    | 最長有効期間 | 0 |

1. **[保存]** を選択します。

## <a name="step-3-run-the-portal"></a>手順 3: ポータルを実行する

これで、ローカル ポータル インスタンスを開発モードでビルドして実行できるようになりました。 開発モードでは、すべての最適化がオフになっており、ソース マップが有効になっています。

次のコマンドを実行します。

```console
npm start
```

しばらくすると、既定のブラウザーが自動的に開き、ローカル開発者ポータル インスタンスが表示されます。 既定のアドレスは `http://localhost:8080` ですが、`8080` が既に使用されている場合は、ポートが変更される可能性があります。 プロジェクトのコードベースを変更すると、リビルドがトリガーされ、ブラウザー ウィンドウが最新の情報に更新されます。

## <a name="step-4-edit-through-the-visual-editor"></a>手順 4: ビジュアル エディターを使用して編集する

ビジュアル エディターを使用して、次のタスクを実行します。

- ポリシーをカスタマイズする
- コンテンツを作成する
- Web サイトの構造を整理する
- 外観のスタイルを適用する

「[チュートリアル:開発者ポータルへのアクセスとそのカスタマイズ](api-management-howto-developer-portal-customize.md)」を参照してください。 管理ユーザー インターフェイスの基本的事項が記載され、既定のコンテンツに対する推奨される変更の一覧が示されています。 すべての変更をローカル環境に保存し、**Ctrl + C** キーを押して閉じます。

## <a name="step-5-publish-locally"></a>手順 5: ローカルで発行する

ポータル データは、厳密に型指定されたオブジェクトの形式で生成されます。 次のコマンドでは、それらを静的ファイルに変換し、出力を `./dist/website` ディレクトリに配置します。

```console
npm run publish
```

## <a name="step-6-upload-static-files-to-a-blob"></a>手順 6: 静的ファイルを BLOB にアップロードする

Azure CLI を使用して、ローカルで生成された静的ファイルを BLOB にアップロードし、訪問者がそれらにアクセスできるようにします。

1. Windows コマンド プロンプト、PowerShell、またはその他のコマンド シェルを開きます。

1. 次の Azure CLI コマンドを実行します。
   
    `<account-connection-string>` はストレージ アカウントの接続文字列に置き換えます。 これはストレージ アカウントの **[アクセス キー]** セクションから取得できます。

    ```azurecli
    az storage blob upload-batch --source dist/website \
        --destination '$web' \
        --connection-string <account-connection-string>
    ```


## <a name="step-7-go-to-your-website"></a>手順 7: Web サイトにアクセスする

ご使用の Web サイトは、Azure Storage のプロパティ ( **[静的な Web サイト]** の **[プライマリ エンドポイント]** ) で指定されたホスト名で公開されました。

## <a name="step-8-change-api-management-notification-templates"></a>手順 8: API Management 通知テンプレートを変更する

API Management 通知テンプレート内の開発者ポータルの URL を、セルフホステッド ポータルを指すように置き換えます。 「[Azure API Management で通知と電子メール テンプレートを構成する方法](api-management-howto-configure-notifications.md)」を参照してください。

特に、次の変更を既定のテンプレートに対して実行します。

> [!NOTE]
> 次の「**更新**」セクションの値は、**https:\//portal.contoso.com/** でポータルをホストしていることを前提としています。 

### <a name="email-change-confirmation"></a>電子メールの変更確認

**電子メールの変更確認** 通知テンプレートで、開発者ポータルの URL を更新します。

**元のコンテンツ**

```html
<a id="confirmUrl" href="$ConfirmUrl" style="text-decoration:none">
  <strong>$ConfirmUrl</strong></a>
```

**更新**

```html
<a id="confirmUrl" href="https://portal.contoso.com/signup?$ConfirmQuery" style="text-decoration:none">
  <strong>https://portal.contoso.com/signup?$ConfirmQuery</strong></a>
```

### <a name="new-developer-account-confirmation"></a>新しい開発者アカウントの確認

**新しい開発者アカウントの確認** 通知テンプレートで、開発者ポータルの URL を更新します。

**元のコンテンツ**

```html
<a id="confirmUrl" href="$ConfirmUrl" style="text-decoration:none">
  <strong>$ConfirmUrl</strong></a>
```

**更新**

```html
<a id="confirmUrl" href="https://portal.contoso.com/signup?$ConfirmQuery" style="text-decoration:none">
  <strong>https://portal.contoso.com/signup?$ConfirmQuery</strong></a>
```

### <a name="invite-user"></a>ユーザーの招待

**ユーザーの招待** 通知テンプレートで、開発者ポータルの URL を更新します。

**元のコンテンツ**

```html
<a href="$ConfirmUrl">$ConfirmUrl</a>
```

**更新**

```html
<a href="https://portal.contoso.com/confirm-v2/identities/basic/invite?$ConfirmQuery">https://portal.contoso.com/confirm-v2/identities/basic/invite?$ConfirmQuery</a>
```

### <a name="new-subscription-activated"></a>アクティブ化された新しいサブスクリプション

**アクティブ化された新しいサブスクリプション** 通知テンプレートで、開発者ポータルの URL を更新します。

**元のコンテンツ**

```html
Thank you for subscribing to the <a href="http://$DevPortalUrl/products/$ProdId"><strong>$ProdName</strong></a> and welcome to the $OrganizationName developer community. We are delighted to have you as part of the team and are looking forward to the amazing applications you will build using our API!
```

**更新**

```html
Thank you for subscribing to the <a href="https://portal.contoso.com/product#product=$ProdId"><strong>$ProdName</strong></a> and welcome to the $OrganizationName developer community. We are delighted to have you as part of the team and are looking forward to the amazing applications you will build using our API!
```

**元のコンテンツ**

```html
Visit the developer <a href="http://$DevPortalUrl/developer">profile area</a> to manage your subscription and subscription keys
```

**更新**

```html
Visit the developer <a href="https://portal.contoso.com/profile">profile area</a> to manage your subscription and subscription keys
```

**元のコンテンツ**

```html
<a href="http://$DevPortalUrl/docs/services?product=$ProdId">Learn about the API</a>
```

**更新**

```html
<a href="https://portal.contoso.com/product#product=$ProdId">Learn about the API</a>
```

**元のコンテンツ**

```html
<p style="font-size:12pt;font-family:'Segoe UI'">
  <strong>
    <a href="http://$DevPortalUrl/applications">Feature your app in the app gallery</a>
  </strong>
</p>
<p style="font-size:12pt;font-family:'Segoe UI'">You can publish your application on our gallery for increased visibility to potential new users.</p>
<p style="font-size:12pt;font-family:'Segoe UI'">
  <strong>
    <a href="http://$DevPortalUrl/issues">Stay in touch</a>
  </strong>
</p>
<p style="font-size:12pt;font-family:'Segoe UI'">
      If you have an issue, a question, a suggestion, a request, or if you just want to tell us something, go to the <a href="http://$DevPortalUrl/issues">Issues</a> page on the developer portal and create a new topic.
</p>
```

**更新**

```html
<!--Remove the entire block of HTML code above.-->
```

### <a name="password-change-confirmation"></a>パスワードの変更確認

**パスワードの変更確認** 通知テンプレートで、開発者ポータルの URL を更新します。

**元のコンテンツ**

```html
<a href="$DevPortalUrl">$DevPortalUrl</a>
```

**更新**

```html
<a href="https://portal.contoso.com/confirm-password?$ConfirmQuery">https://portal.contoso.com/confirm-password?$ConfirmQuery</a>
```

### <a name="all-templates"></a>すべてのテンプレート

フッターにリンクがあるすべてのテンプレートで、開発者ポータルの URL を更新します。

**元のコンテンツ**

```html
<a href="$DevPortalUrl">$DevPortalUrl</a>
```

**更新**

```html
<a href="https://portal.contoso.com/">https://portal.contoso.com/</a>
```

## <a name="move-from-managed-to-self-hosted-developer-portal"></a>マネージドからセルフホステッド開発者ポータルへの移行

時間の経過と共に、ビジネス要件が変わることもあります。 マネージド バージョンの API Management 開発者ポータルがニーズを満たさなくなる状況になる可能性があります。 たとえば、新しい要件によって、サードパーティのデータ プロバイダーと統合するカスタム ウィジェットの構築を強いられることもあります。 マネージド バージョンとは異なり、セルフホステッド バージョンのポータルでは、十分な柔軟性と拡張性が提供されます。

### <a name="transition-process"></a>切り替えプロセス

同じ API Management サービス インスタンス内でマネージド バージョンからセルフホステッド バージョンに切り替えることができます。 このプロセスでは、マネージド バージョンのポータルで行った変更が保持されます。 ポータルのコンテンツを事前にバックアップしてください。 バックアップ スクリプトは API Management 開発者ポータルの [GitHub リポジトリ](https://github.com/Azure/api-management-developer-portal)の `scripts` フォルダーにあります。

変換プロセスは、この記事の前の手順で示したような、一般的なセルフホステッド ポータルの設定とほぼ同じです。 この構成手順には例外が 1 つあります。 `config.design.json` ファイルのストレージ アカウントは、マネージド バージョンのポータルのストレージ アカウントと同じである必要があります。 SAS URL を取得する方法については、「[チュートリアル: Linux VM のシステム割り当て ID を使用して SAS 資格情報で Azure Storage にアクセスする](../active-directory/managed-identities-azure-resources/tutorial-linux-vm-access-storage-sas.md#get-a-sas-credential-from-azure-resource-manager-to-make-storage-calls)」を参照してください。

> [!TIP]
> `config.publish.json` ファイルでは、別のストレージ アカウントを使用することをお勧めします。 この方法により、ポータルのホスティング サービスをより細かく制御し、管理を簡素化することができます。

## <a name="enable-captcha"></a>CAPTCHA を有効にする

セルフホステッド ポータルを設定するときに、`useHipCaptcha` 設定を使用して CAPTCHA を無効にしている場合があります。 CAPTCHA による通信はエンドポイントを介して行われ、これによってマネージド開発者ポータルのホスト名に対してのみ、クロスオリジン リソース共有 (CORS) が実行されます。 開発者ポータルがセルフホステッド型の場合、別のホスト名が使用されるため、CAPTCHA では通信が許可されません。

### <a name="update-the-json-config-files"></a>JSON 構成ファイルを更新する

セルフホステッド ポータルで CAPTCHA を有効にするには、次のようにします。

1. カスタム ドメイン (たとえば `api.contoso.com`) を API Management サービスの **開発者ポータル** エンドポイントに割り当てます。

    このドメインは、マネージド バージョンのポータルと CAPTCHA エンドポイントに適用されます。 手順については、「[Azure API Management インスタンスのカスタム ドメイン名を構成する](configure-custom-domain.md)」を参照してください。

1. セルフホステッド ポータルの[ローカル環境](#step-1-set-up-local-environment)の `src` フォルダーにアクセスします。

1. `json` 構成ファイルを更新します。

    | ファイル | 新しい値 | Note |
    | ---- | --------- | ---- |
    | `config.design.json`| `"backendUrl": "https://<custom-domain>"` | `<custom-domain>` を最初の手順で設定したカスタム ドメインで置き換えます。 |
    |  | `"useHipCaptcha": true` | 値を `true` に変更します。 |
    | `config.publish.json`| `"backendUrl": "https://<custom-domain>"` | `<custom-domain>` を最初の手順で設定したカスタム ドメインで置き換えます。 |
    |  | `"useHipCaptcha": true` | 値を `true` に変更します。 |
    | `config.runtime.json` | `"backendUrl": "https://<custom-domain>"` | `<custom-domain>` を最初の手順で設定したカスタム ドメインで置き換えます。 |

1. ポータルを[発行](#step-5-publish-locally)します。

1. 新しく発行されたポータルを[アップロード](#step-6-upload-static-files-to-a-blob)してホストします。

1. カスタム ドメインを通じてセルフホステッド ポータルを公開します。

ポータル ドメインの 1 番目と 2 番目のレベルは、最初の手順で設定したドメインと一致している必要があります。 たとえば、「 `portal.contoso.com` 」のように入力します。 実際の手順は、選択したホスティングプ ラットフォームによって異なります。 Azure ストレージ アカウントを使用している場合は、「[カスタム ドメインを Azure Blob Storage エンドポイントにマップする](../storage/blobs/storage-custom-domain-name.md)」の手順を参照してください。

## <a name="next-steps"></a>次のステップ

- [セルフホストの代替アプローチ](developer-portal-alternative-processes-self-host.md)について説明します。
