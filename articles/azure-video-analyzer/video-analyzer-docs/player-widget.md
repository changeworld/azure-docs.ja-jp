---
title: Azure Video Analyzer プレーヤー ウィジェットを使用する
description: この参照記事では、Video Analyzer プレーヤー ウィジェットをアプリケーションに追加する方法について説明します。
ms.service: azure-video-analyzer
ms.topic: reference
ms.date: 06/01/2021
ms.openlocfilehash: b70bfc9a10e357c6f1e64c1737fdb4c049b505f5
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2021
ms.locfileid: "123037445"
---
# <a name="use-the-azure-video-analyzer-player-widget"></a>Azure Video Analyzer プレーヤー ウィジェットを使用する

このチュートリアルでは、アプリケーション内でプレーヤー ウィジェットを使用する方法について説明します。 このコードは簡単に埋め込めるウィジェットであり、ユーザーはこれを使用して、ビデオを再生したり、セグメント化されたビデオ ファイルの各部分を参照したりすることができます。 これを行うために、ウィジェットが埋め込まれた静的な HTML ページと、それを機能させるすべての部分を生成します。

このチュートリアルでは、次のことについて説明します。

> [!div class="checklist"]
> * トークンを作成する
> * ビデオのリストを作成する
> * [ビデオ アプリケーション リソース](./terminology.md#video)を再生するためのベース URL を取得する
> * プレーヤーでページを作成する
> * ストリーミング エンドポイントとトークンをプレーヤーに渡す

## <a name="prerequisites"></a>前提条件

このチュートリアルには、次のものが必要です。

* アクティブなサブスクリプションが含まれる Azure アカウント。 まだお持ちでない場合は、[無料のアカウントを作成してください](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* HTML ファイル用の [Visual Studio Code](https://code.visualstudio.com/) またはその他のエディター。
* [継続的なビデオ記録と再生](./use-continuous-video-recording.md)または[モーションの検出とエッジ デバイス上でのビデオの記録](./detect-motion-record-video-clips-cloud.md)。

さらに、次のリソースについて理解しておくと便利です。

- [Web コンポーネント](https://developer.mozilla.org/docs/Web/Web_Components)
- [TypeScript](https://www.typescriptlang.org)

## <a name="create-a-token"></a>トークンを作成する

このセクションでは、この記事で後ほど使用する JSON Web Token (JWT) を作成します。 JWT トークンを生成し、アクセス ポリシーを作成するために必要なすべてのフィールドを提供するサンプル アプリケーションを使用します。

> [!NOTE] 
> RSA または ECC 証明書に基づいて JWT トークンを生成する方法について理解している場合は、このセクションを省略できます。

1. [AVA C# サンプル リポジトリ](https://github.com/Azure-Samples/video-analyzer-iot-edge-csharp)を複製します。 次に、*src/jwt-token-issuer* フォルダーに移動し、*JWTTokenIssuer* アプリケーションを見つけます。

    > [!NOTE] 
    > 対象ユーザーの値を構成する方法の詳細については、「[アクセス ポリシー](./access-policies.md)」を参照してください。

2. Visual Studio Code を開き、*JWTTokenIssuer* アプリケーションをダウンロードしたフォルダーに移動します。 このフォルダーには、 *\*.csproj* ファイルが含まれています。
3. エクスプローラー ペインで *program.cs* ファイルに移動します。
4. 77 行目で、対象ユーザーをご使用の Azure Video Analyzer エンドポイントに変更し、/videos/\* と続けます。 つまり、次のようになります。

   ```
   https://{Azure Video Analyzer Account ID}.api.{Azure Long Region Code}.videoanalyzer.azure.net/videos/*
   ```

   > [!NOTE] 
   > Video Analyzer エンドポイントは、Azure portal の Video Analyzer リソースの概要セクションで確認できます。 この値は、この記事で後述する「[ビデオ リソースの一覧](#list-video-resources)」で `clientApiEndpointUrl` として参照されます。

   :::image type="content" source="media/player-widget/client-api-url.png" alt-text="プレーヤー ウィジェットのエンドポイントを示すスクリーンショット。":::
    
5. 78 行目で、発行者をご使用の証明書の発行者の値に変更します (例: `https://contoso.com`)。
6. ファイルを保存します。 "**Required assets to build and debug are missing from 'jwt token issuer'. Add them?** " (ビルドとデバッグに必要なアセットが 'JWT トークン発行者' にありません。追加しますか?) というメッセージが表示される場合があります。 **[はい]** を選択します。
   
   :::image type="content" source="media/player-widget/visual-studio-code-required-assets.png" alt-text="必要なアセット示す Visual Studio Code のプロンプトのスクリーンショット。":::
   
7. コマンド プロンプト ウィンドウを開き、*JWTTokenIssuer* ファイルが格納されているフォルダーに移動します。 `dotnet build` と `dotnet run` の 2 つのコマンドを続けて実行します。 Visual Studio Code に C# の拡張機能がある場合は、F5 キーを押して *JWTTokenIssuer* アプリケーションを実行することもできます。

アプリケーションがビルドされ、実行されます。 ビルドが完了すると、自己署名証明書が作成され、その証明書から JWT トークンの情報が生成されます。 *JWTTokenIssuer* がビルドされたディレクトリのデバッグ フォルダーにある *JWTTokenIssuer.exe* ファイルを実行することもできます。 アプリケーションを実行するメリットは、次のように入力オプションを指定できるという点です。

- `JwtTokenIssuer [--audience=<audience>] [--issuer=<issuer>] [--expiration=<expiration>] [--certificatePath=<filepath> --certificatePassword=<password>]`

*JWTTokenIssuer* によって、JWT と、次の必須コンポーネントが作成されます。

- `Issuer`, `Audience`, `Key Type`, `Algorithm`, `Key Id`, `RSA Key Modulus`, `RSA Key Exponent`, `Token`

**後で使用するためにこれらの値をコピーして保存しておいてください。**

## <a name="create-an-access-policy"></a>アクセス ポリシーを作成します。

アクセス ポリシーでは、特定のビデオ ストリームへのアクセス許可とアクセス期間を定義します。 このチュートリアルでは、Azure portal で Video Analyzer のアクセス ポリシーを構成します。  

1. Azure portal にサインインし、ご自身の Video Analyzer アカウントがあるリソース グループに移動します。
1. Video Analyzer リソースを選択します。
1. **Video Analyzer** の **[アクセス ポリシー]** を選択します。

   :::image type="content" source="./media/player-widget/portal-access-policies.png" alt-text="[アクセス ポリシー] オプションを示すスクリーンショット。":::
   
1. **[新規]** を選択し、次の情報を入力します。

   - **[アクセス ポリシー名]** : 任意の名前を選択できます。

   - **[発行者]** : この値は JWT 発行者と一致している必要があります。 

   - **[対象ユーザー]** : JWT の対象ユーザー。 `${System.Runtime.BaseResourceUrlPattern}` が既定値です。 対象ユーザーと `${System.Runtime.BaseResourceUrlPattern}` の詳細については、「[アクセス ポリシー](./access-policies.md)」を参照してください。

   - **[Key type]** :RSA 

   - **[アルゴリズム]** : サポートされている値は RS256、RS384、RS512 です。

   - **[キー ID]** : この ID はご使用の証明書から生成されます。 詳細については、「[トークンを作成する](#create-a-token)」を参照してください。

   - **[RSA Key Modulus]\(RSA キーの係数\)** : この値はご使用の証明書から生成されます。 詳細については、「[トークンを作成する](#create-a-token)」を参照してください。

   - **[RSA Key Exponent]\(RSA キーの指数\)** : この値はご使用の証明書から生成されます。 詳細については、「[トークンを作成する](#create-a-token)」を参照してください。

   :::image type="content" source="./media/player-widget/access-policies-portal.png" alt-text="アクセス ポリシー ポータルを示すスクリーンショット。"::: 
   
   > [!NOTE] 
   > これらの値は、前の手順で作成した *JWTTokenIssuer* アプリケーションから取得されます。

1. **[保存]** を選択します。

## <a name="list-video-resources"></a>ビデオ リソースの一覧

次は、ビデオ リソースの一覧を生成します。 以前に使用したアカウント エンドポイントへの REST 呼び出しを行い、生成したトークンを使用して認証します。

GET 要求を REST API に送信する方法は多数ありますが、ここでは JavaScript 関数を使用します。 次のコードでは [XMLHttpRequest](https://www.w3schools.com/xml/ajax_xmlhttprequest_create.asp) が、ページの `clientApiEndpointUrl` フィールドと `token` フィールドに保管する値と共に使用され、同期 `GET` 要求が送信されます。 次に、ビデオの結果リストが取得され、ページに設定した `videoList` テキスト領域に保管されます。

```javascript
function getVideos()
{
    var xhttp = new XMLHttpRequest();
    var getUrl = document.getElementById("clientApiEndpointUrl").value + "/videos?api-version=2021-05-01-preview";
    xhttp.open("GET", getUrl, false);
    xhttp.setRequestHeader("Authorization", "Bearer " + document.getElementById("token").value);
    xhttp.send();
    document.getElementById("videoList").value = xhttp.responseText.toString();
}
```
   > [!NOTE]
   >`clientApiEndPoint` とトークンは、「[トークンを作成する](#create-a-token)」で収集されます。

## <a name="add-the-video-analyzer-player-component"></a>Video Analyzer プレーヤー コンポーネントを追加する

これで、クライアント API エンドポイントの URL、トークン、ビデオ名が得られたので、プレーヤーをページに追加できます。

1. ページに直接パッケージを追加して、プレーヤー モジュール自体を組み込みます。 アプリケーションに NPM パッケージの方向を含めることも、次のように実行時に動的に埋め込むこともできます。
   ```html
   <script async type="module" src="https://unpkg.com/@azure/video-analyzer-widgets"></script>
   ```
1. ドキュメントに `AVA-Player` 要素を追加します。
   ```html
   <ava-player width="720px" id="avaPlayer"></ava-player>
   ```
1. ページ上の Video Analyzer プレーヤー ウィジェットへのリンクを取得します。
   ```javascript
   const avaPlayer = document.getElementById("avaPlayer");
   ```
1. 使用している値でプレーヤーを構成するには、次に示すように、それらをオブジェクトとして設定する必要があります。
   ```javascript
   avaPlayer.configure( {
      token: document.getElementById("token").value,
      clientApiEndpointUrl: document.getElementById("clientApiEndpointUrl").value,
      videoName: document.getElementById("videoName").value
   } );
   ```
1. ビデオをプレーヤーに読み込み、開始します。
   ```javascript
   avaPlayer.load();
   ```

## <a name="put-it-all-together"></a>すべてをまとめた配置

前の Web 要素を組み合わせると、次の静的 HTML ページが表示されます。 このページでは、アカウント エンドポイントとトークンを使用してビデオを表示できます。

```html
<html>
<head>
<title>Video Analyzer Player Widget Demo</title>
</head>
<script async type="module" src="https://unpkg.com/@azure/video-analyzer-widgets"></script>
<body>
<script>
    function getVideos()
    {
        var xhttp = new XMLHttpRequest();
        var getUrl = document.getElementById("clientApiEndpointUrl").value + "/videos?api-version=2021-05-01-preview";
        xhttp.open("GET", getUrl, false);
        xhttp.setRequestHeader("Authorization", "Bearer " + document.getElementById("token").value);
        xhttp.send();
        document.getElementById("videoList").value = xhttp.responseText.toString();
    }
    function playVideo() {
        const avaPlayer = document.getElementById("avaPlayer");
        avaPlayer.configure( {
            token: document.getElementById("token").value,
            clientApiEndpointUrl: document.getElementById("clientApiEndpointUrl").value,
            videoName: document.getElementById("videoName").value
        } );
        avaPlayer.load();
    }
</script>
Client API endpoint URL: <input type="text" id="clientApiEndpointUrl" /><br><br>
Token: <input type="text" id="token" /><br><br>
<button type="submit" onclick="getVideos()">Get Videos</button><br><br>
<textarea rows="20" cols="100" id="videoList"></textarea><br><br>
Video name: <input type="text" id="videoName" /><br><br>
<button type="submit" onclick="playVideo()">Play Video</button><br><br>
<ava-player width="720px" id="avaPlayer"></ava-player>
</body>
</html>
```

## <a name="host-the-page"></a>ページをホストする

このページはローカルでテストできますが、ホストされているバージョンをテストすることをお勧めします。 ページを簡単にホストできない場合は、こちらの Azure Storage で[静的な Web サイト](../../storage/blobs/storage-blob-static-website.md)を使用して行う手順に従ってください。 次の手順は、[これらのより完全な手順](../../storage/blobs/storage-blob-static-website-how-to.md)から要点を抜粋したものです。 手順は、このチュートリアルで使用しているファイルに合わせて更新されています。

1. ストレージ アカウントを作成します。
1. **[データ管理]** で、 **[静的な Web サイト]** を選択します。
1. ストレージ アカウントで静的な Web サイトを有効にします。
1. **[インデックス ドキュメント名]** には、「**index.html**」と入力します。
1. **[エラー ドキュメントのパス]** には、「**404.html**」と入力します。
1. **[保存]** を選択します。
1. 表示される **[プライマリ エンドポイント]** をメモします。 これがご自身の Web サイトになります。
1. **[プライマリ エンドポイント]** の上で、 **[$web]** を選択します。
1. 上部にある **[アップロード]** ボタンを使用して、静的 HTML ページを **index.html** としてアップロードします。

## <a name="play-a-video"></a>ビデオを再生する

これでページがホストされたので、そこに移動し、ビデオを再生する手順を実行できるようになりました。

1. **[Client API endpoint URL]\(クライアント API のエンドポイント URL\)** と **[トークン]** の値を入力します。
1. **[Get videos]\(ビデオを取得する\)** を選択します。
1. ビデオの一覧からビデオ名を選択し、それを **[ビデオ名]** フィールドに入力します。
1. **[ビデオを再生する]** を選択します。

## <a name="additional-details"></a>追加情報

次のセクションには、注意する必要がある重要な追加の詳細がいくつか含まれています。

### <a name="refresh-the-access-token"></a>アクセス トークンを更新する

プレーヤーでは、以前に生成したアクセス トークンを使用して、再生承認トークンを取得します。 トークンは定期的に期限切れになり、更新する必要があります。 新しいプレーヤーのアクセス トークンを生成した後、アクセス トークンを更新する方法は 2 つあります。

* ウィジェット メソッド `setAccessToken` をアクティブに呼び出す。
    ```typescript
    avaPlayer.setAccessToken('<NEW-ACCESS-TOKEN>');
    ```
* このイベントをリッスンして、`TOKEN_EXPIRED` イベント時に処理する。
    ```typescript
    avaPlayer.addEventListener(PlayerEvents.TOKEN_EXPIRED, () => {
        avaPlayer.setAccessToken('<YOUR-NEW-TOKEN>');
    });
    ```

`TOKEN_EXPIRED` イベントは、トークンの有効期限が切れる 5 秒前に発生します。 イベント リスナーを設定する場合は、プレーヤー ウィジェットで `load` 関数を呼び出す前に設定してください。

### <a name="configuration-details"></a>構成の詳細

前述のプレーヤーの構成はシンプルですが、構成値に対してより幅広いオプションを使用できます。 サポートされているフィールドを次に示します。

| 名前   | Type             | Description                         |
| ------ | ---------------- | ----------------------------------- |
| `token`  | string | ウィジェットの JWT トークン |
| `videoName` | string | ビデオ リソースの名前  |
| `clientApiEndpointUrl` | string | クライアント API のエンドポイント URL |

### <a name="alternate-ways-to-load-the-code-into-your-application"></a>アプリケーションにコードを読み込む別の方法

アプリケーションにコードを取り込むために使用されるパッケージは、[NPM パッケージ](https://www.npmjs.com/package/@azure/video-analyzer-widgets)です。 前述の例では、最新バージョンが実行時にリポジトリから直接読み込まれていました。 ただし、次を使用してローカルでパッケージをダウンロードしてインストールすることもできます。

```bash
npm install @azure/video-analyzer/widgets
```

または、こちらを TypeScript に使用して、アプリケーション コード内にインポートすることもできます。

```typescript
import { Player } from '@video-analyzer/widgets';
```

プレーヤー ウィジェットを動的に作成する必要がある場合は、これを JavaScript に使用できます。
```javascript
<script async type="module" src="https://unpkg.com/@azure/video-analyzer-widgets@latest/dist/global.min.js"></script>
```

この方法を使用してインポートする場合は、インポートの完了後に、プログラムによってプレーヤー オブジェクトを作成する必要があります。 前述の例では、`ava-player` HTML タグを使用して、ページにモジュールを追加しました。 コードを使用してプレーヤー オブジェクトを作成するには、JavaScript で次を実行します。

```javascript
const avaPlayer = new ava.widgets.player();
```

または TypeScript で次を実行します。

```typescript
const avaPlayer = new Player();
```

次に、それを HTML に追加する必要があります。

```javascript
document.firstElementChild.appendChild(avaPlayer);
```

## <a name="next-steps"></a>次の手順

* [ウィジェット API](https://github.com/Azure/video-analyzer/tree/main/widgets) の詳細を確認します。
