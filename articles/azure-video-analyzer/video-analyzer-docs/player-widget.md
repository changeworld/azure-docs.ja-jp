---
title: Azure Video Analyzer プレーヤーウィジェットの使用方法
description: この参照記事では、Video Analyzer プレーヤー ウィジェットをアプリケーションに追加する方法について説明します。
ms.service: azure-video-analyzer
ms.topic: reference
ms.date: 05/11/2021
ms.openlocfilehash: 12565f2ca1a86d3a9e57fef652abab9564cb7b5e
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/25/2021
ms.locfileid: "110387864"
---
# <a name="using-the-azure-video-analyzer-player-widget"></a>Azure Video Analyzer プレーヤーウィジェットの使用方法

このチュートリアルでは、アプリケーション内で Azure Video Analyzer プレーヤー ウィジェットを使用する方法について説明します。  このコードは簡単に埋め込めるウィジェットであり、エンドユーザーはこれを使用して、ビデオを再生したり、セグメント化されたビデオ ファイルの各部分を移動したりすることができます。  これを行うために、ウィジェットを埋め込み、すべての部分が機能するようにして、静的 HTML ページを生成します。

このチュートリアルでは次のことを行います。

> [!div class="checklist"]
> * トークンを作成する
> * ビデオのリストを作成する
> * [ビデオ アプリケーション リソース](./terminology.md#video)を再生するためのベース URL を取得する
> * プレーヤーでページを作成する
> * ストリーミング エンドポイントとトークンをプレーヤーに渡す

## <a name="suggested-pre-reading"></a>先に読んでおくことが推奨される記事

- [Web コンポーネント](https://developer.mozilla.org/en-US/docs/Web/Web_Components)
- [TypeScript](https://www.typescriptlang.org)

## <a name="prerequisites"></a>前提条件

このチュートリアルの前提条件は次のとおりです。
* アクティブなサブスクリプションが含まれる Azure アカウント。 まだお持ちでない場合は、[無料のアカウントを作成してください](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 
* HTML ファイル用の [Visual Studio Code](https://code.visualstudio.com/) またはその他のエディター。
します。
* [継続的なビデオ記録と再生](./use-continuous-video-recording.md)または[モーションの検出とエッジ デバイス上でのビデオの記録](./detect-motion-record-video-clips-cloud.md)

## <a name="create-a-token"></a>トークンを作成する

このセクションでは、このドキュメントで後ほど使用する JWT トークンを作成します。  JWT トークンを生成し、アクセス ポリシーを作成するために必要なすべてのフィールドを提供するサンプル アプリケーションを使用します。

> [!NOTE] 
> RSA または ECC 証明書に基づいて JWT トークンを生成する方法についてよく理解している場合は、このセクションを省略できます。

1. [ここ](https://github.com/Azure-Samples/video-analyzer-iot-edge-csharp/tree/main/src/jwt-token-issuer/readme.md)にある JWTTokenIssuer アプリケーションをダウンロードします。

   > [!NOTE] 
   > 対象ユーザーの値を構成する方法の詳細については、こちらの[記事](./access-policies.md)を参照してください。

2. Visual Studio Code を起動し、* .sln ファイルが格納されているフォルダーを開きます。
3. エクスプローラー ペインで program.cs ファイルに移動
4. 行 77 を変更 - 対象ユーザーを Video Analyzer エンドポイントと /videos/* に変更して、次のようにします。

   ```
   https://{Azure Video Analyzer Account ID}.api.{Azure Long Region Code}.videoanalyzer.azure.net/videos/*
   ```
5. 行 78 を変更 - 発行者を証明書の発行者の値に変更します。  例: https://contoso.com

   > [!NOTE] 
   > Video Analyzer エンドポイントは、Azure Video Analyzer リソースの概要セクションで確認できます。 [JSON ビュー] リンクをクリックする必要があります。 

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/player-widget/endpoint.png" alt-text="プレーヤー ウィジェット - エンドポイント":::
6. ファイルを保存します。
7. `F5` を押して、JWTTokenIssuer アプリケーションを実行します。

これにより、アプリケーションがビルドされ、実行されます。  ビルド後、openssl を使用して証明書を作成することで実行されます。  デバッグ フォルダーにある JWTTokenIssuer.exe ファイルを実行することもできます。  アプリケーションを実行するメリットは、次のように入力オプションを指定できるという点です。

- JwtTokenIssuer [--audience=<audience>] [--issuer=<issuer>] [--expiration=<expiration>] [--certificatePath=<filepath> --certificatePassword=<password>]

JWTTokenIssuer によって、JWT トークンと、次の必要なコンポーネントが作成されます。

- `kty`; `alg`; `kid`; `n`; `e`

後で使用できるように、これらの値を必ずコピーしてください。

## <a name="create-an-access-policy"></a>アクセス ポリシーを作成します。

アクセス ポリシーには、Video Analyzer の特定のビデオ ストリームへのアクセス許可とアクセス期間を定義します。  このチュートリアルでは、Azure portal で Video Analyzer のアクセス ポリシーを構成します。  

1. Azure portal にログインし、ご自分の Video Analyzer アカウントがあるリソース グループに移動します。
1. Video Analyzer リソースを選択します。
1. Video Analyzer の [アクセス ポリシー] を選択します

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/player-widget/portal-access-policies.png" alt-text="プレーヤー ウィジェット - ポータル アクセス ポリシー":::    
1. [新規] をクリックし、次を入力します。

   > [!NOTE] 
   > これらの値は、前の手順で作成した JWTTokenIssuer アプリケーションから取得されます。

   - アクセス ポリシー名 - 任意の名前

   - 発行者 - JWT トークン発行者と一致する必要があります 

   - 対象ユーザー - JWT トークンの対象ユーザー -- ${System.Runtime.BaseResourceUrlPattern} が既定値です。  対象ユーザーと ${System.Runtime.BaseResourceUrlPattern} の詳細については、この[記事](./access-policies.md)を参照してください

   - キーの種類 - kty -- RSA 

   - アルゴリズム - サポートされる値は RS256、RS384、RS512 です

   - キー ID - kid -- 証明書から生成

   - N 値 - RSA の場合、N 値は Modulus です

   - E 値 - RSA の場合、E 値は Public Exponent です

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/player-widget/access-policies-portal.png" alt-text="プレーヤー ウィジェット - ポータルのアクセス ポリシー":::     
1. `save` をクリックします。

## <a name="list-video-analyzer-video-resources"></a>Video Analyzer ビデオ リソースの一覧表示

次に、ビデオ リソースのリストを生成する必要があります。  これは、上記で使用したアカウント エンドポイントへの REST 呼び出しによって行われ、生成したトークンを使用して認証されます。

このため、GET 要求を REST API に送信する方法は多数ありますが、ここでは JavaScript 関数を使用します。  次のコードでは [XMLHttpRequest](https://www.w3schools.com/xml/ajax_xmlhttprequest_create.asp) が、ページの `clientApiEndpointUrl` フィールドと `token` フィールドに保管される値と共に使用され、同期 `GET` 要求が送信されます。  次に、ビデオの結果リストが取得され、ページに設定した `videoList` テキスト領域に保管されます。

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

## <a name="add-the-video-analyzer-player-component"></a>Video Analyzer Player コンポーネントを追加する

これで、クライアント API エンドポイントの URL、トークン、ビデオ名を得られたため、プレーヤーをページに追加できます。

1. ページに直接パッケージを追加して、プレーヤー モジュール自体を組み込みます。  アプリケーションに NPM パッケージの方向を含めることも、次のように実行時に動的に埋め込むこともできます。
   ```html
   <script async type="module" src="https://unpkg.com/@azure/video-analyzer-widgets"></script>
   ```
1. ドキュメントに AVA-Player 要素を追加します。
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
1. ビデオをプレーヤーに読み込み、開始する
   ```javascript
   avaPlayer.load();
   ```

## <a name="put-it-all-together"></a>すべてをまとめた配置

上記の Web 要素を組み合わせると、次の静的 HTML ページが表示されます。このページでは、アカウント エンドポイントとトークンを使用してビデオを表示できます。

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

このページはローカルでテストできますが、ホストされているバージョンをテストすることもできます。  ページを簡単にホストできない場合は、次に示されている、Storage で[静的な Web サイト](../../storage/blobs/storage-blob-static-website.md)を使用して行う手順に従ってください。  以下は、このチュートリアルで使用しているファイル用に更新された[より完全な手順](../../storage/blobs/storage-blob-static-website-how-to.md)から要点を抜粋したものです。

1. ストレージ アカウントの作成
1. 左側の `Data management` で、`Static website` をクリックします。
1. `Enable` Storage アカウントの静的 Web サイト
1. `Index document name` の場合は、`index.html` に入力します
1. `Error document path` の場合は、`404.html` に入力します
1. 上部の `Save` を選択します
1. `Primary endpoint` が表示されます。これが、使用する Web サイトになります
1. `Primary endpoint` の上の `$web` をクリックします
1. 上部にある `Upload` ボタンを使用して、静的 HTML ページを `index.html` としてアップロードします

## <a name="play-a-video"></a>ビデオを再生する

これでページがホストされたので、そこに移動し、手順を実行できるようになりました。

1. `Client API endpoint URL` と `Token` に配置します
1. [`Get videos`]\(保存\) をクリックします
1. ビデオ リストからいずれかのビデオ名を選択し、`Video name` 入力フィールドに入力します
1. [`Play video`]\(保存\) をクリックします

## <a name="additional-details"></a>追加情報

### <a name="refreshing-the-access-token"></a>アクセス トークンの更新

プレーヤーは、以前に生成したアクセス トークンを使用して、再生承認トークンを取得します。  トークンは定期的に期限切れになるため、更新する必要があります。  新しいプレーヤーのアクセス トークンを生成した後、アクセス トークンを更新する方法は 2 つあります。

* ウィジェット メソッド `setAccessToken` をアクティブに呼び出す
    ```typescript
    avaPlayer.setAccessToken('<NEW-ACCESS-TOKEN>');
    ```
* このイベントをリッスンして、`TOKEN_EXPIRED` イベント時に処理する
    ```typescript
    avaPlayer.addEventListener(PlayerEvents.TOKEN_EXPIRED, () => {
        avaPlayer.setAccessToken('<YOUR-NEW-TOKEN>');
    });
    ```

`TOKEN_EXPIRED` イベントは、トークンの有効期限が切れる 5 秒前に発生します。  イベント リスナーを設定する場合は、プレーヤー ウィジェットで `load` 関数を呼び出す前に設定することをお勧めします。

### <a name="configuration-details"></a>構成の詳細

上記のプレーヤーに単純な構成を行いましたが、構成値に対する幅広いオプションがサポートされています。  次のフィールドがサポートされています。

| Name   | 型             | Description                         |
| ------ | ---------------- | ----------------------------------- |
| token  | string | ウィジェットの JWT トークン |
| videoName | string | ビデオ リソースの名前  |
| clientApiEndpointUrl | string | クライアント API のエンドポイント URL |

### <a name="alternate-ways-to-load-the-code-into-your-application"></a>アプリケーションにコードを読み込む別の方法

アプリケーションにコードを取り込むために使用される NPM パッケージは[ここ](https://www.npmjs.com/package/video-analyzer-widgets)にあります。  上記の例では、最新バージョンが実行時にリポジトリから直接読み込まれていましたが、次を使用してローカルでパッケージをダウンロードしてインストールすることもできます。

```bash
npm install @azure/video-analyzer/widgets
```

または、次を使用して、アプリケーション コード内にインポートすることもできます。

```typescript
import { Player } from '@video-analyzer/widgets';
```

この方法を使用してインポートする場合は、インポートの完了後に、プログラムによってプレーヤー オブジェクトを作成する必要があります。  上記の例では、`ava-player` HTML タグを使用して、このページにモジュールを追加しました。  コードを使用してプレーヤー オブジェクトを作成するには、JavaScript、

```javascript
const avaPlayer = new ava.widgets.player();
```

または Typescript で以下を実行できます。

```typescript
const avaPlayer = new Player();
```

次に、それを HTML に追加する必要があります。

```javascript
document.firstElementChild.appendChild(avaPlayer);
```

## <a name="next-steps"></a>次の手順

* [ウィジェット API](https://github.com/Azure/video-analyzer/widgets) の詳細を確認する
