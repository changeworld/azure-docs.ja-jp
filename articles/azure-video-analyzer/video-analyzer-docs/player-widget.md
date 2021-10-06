---
title: Azure Video Analyzer プレーヤー ウィジェットを使用する
description: この参照記事では、Video Analyzer プレーヤー ウィジェットをアプリケーションに追加する方法について説明します。
ms.service: azure-video-analyzer
ms.topic: reference
ms.date: 06/01/2021
ms.openlocfilehash: ffc17e756a303723fe1d21c6ba221fed31147eaa
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128620575"
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
* [継続的なビデオ記録と再生](./use-continuous-video-recording.md)または[モーションの検出とエッジ デバイス上でのビデオの記録](./detect-motion-record-video-clips-cloud.md)
* [トークン](./access-policies.md#creating-a-token)の作成
* [アクセス ポリシー](./access-policies.md#creating-an-access-policy)の作成

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
   >`clientApiEndPoint` とトークンは、「[トークンを作成する](./access-policies.md#creating-a-token)」で収集されます

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
   
## <a name="add-the-zone-drawer-component"></a>ゾーン ドロワー コンポーネントを追加する

1. ドキュメントに AVA-Zone-Drawer 要素を追加します。
   ```html
   <ava-zone-drawer width="720px" id="zoneDrawer"></ava-zone-drawer>
   ```
1. ページ上の Video Analyzer ゾーン ドロワーへのリンクを取得します。
   ```javascript
   const zoneDrawer = document.getElementById("zoneDrawer");
   ```
1. ゾーン ドロワーをプレーヤーに読み込みます。
   ```javascript
   zoneDrawer.load();
   ```
1. ゾーンを作成して保存するには、ここでイベント リスナーを追加する必要があります。
   ```javascript
   zoneDrawer.addEventListener('ZONE_DRAWER_ADDED_ZONE', (event) => {
            console.log(event);
            document.getElementById("zoneList").value = JSON.stringify(event.detail);
        });

        zoneDrawer.addEventListener('ZONE_DRAWER_SAVE', (event) => {
            console.log(event);
            document.getElementById("zoneList").value = JSON.stringify(event.detail);
        });
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
    
        const zoneDrawer = document.getElementById("zoneDrawer");
        zoneDrawer.load();

        zoneDrawer.addEventListener('ZONE_DRAWER_ADDED_ZONE', (event) => {
            console.log(event);
            document.getElementById("zoneList").value = JSON.stringify(event.detail);
        });

        zoneDrawer.addEventListener('ZONE_DRAWER_SAVE', (event) => {
            console.log(event);
            document.getElementById("zoneList").value = JSON.stringify(event.detail);
        });
    }
</script>
Client API endpoint URL: <input type="text" id="clientApiEndpointUrl" /><br><br>
Token: <input type="text" id="token" /><br><br>
<button type="submit" onclick="getVideos()">Get Videos</button><br><br>
<textarea rows="20" cols="100" id="videoList"></textarea><br><br>
Video name: <input type="text" id="videoName" /><br><br>
<button type="submit" onclick="playVideo()">Play Video</button><br><br>
<textarea rows="5" cols="100" id="zoneList"></textarea><br><br>
<ava-zone-drawer width="720px" id="zoneDrawer">
    <ava-player id="avaPlayer"></ava-player>
</ava-zone-drawer>
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

| 名前   | 種類             | 説明                         |
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
import { Player } from '@azure/video-analyzer-widgets';
import { ZoneDrawer } from '@azure/video-analyzer-widgets';
```

プレーヤー ウィジェットを動的に作成する必要がある場合は、これを JavaScript に使用できます。
```javascript
<script async type="module" src="https://unpkg.com/@azure/video-analyzer-widgets@latest/dist/global.min.js"></script>
```


この方法を使用してインポートする場合は、インポートの完了後に、プログラムによってゾーン ドロワーとプレーヤー オブジェクトを作成する必要があります。  前述の例では、`ava-player` HTML タグを使用して、ページにモジュールを追加しました。 コードを使用してゾーン ドロワー オブジェクトとプレーヤー オブジェクトを作成するには、JavaScript で次を実行します。


```javascript
const zoneDrawer = new window.ava.widgets.zoneDrawer();
document.firstElementChild.appendChild(zoneDrawer);
const playerWidget = new window.ava.widgets.player();
zoneDrawer.appendChild(playerWidget);
```

または TypeScript で次を実行します。

```typescript
const avaPlayer = new Player();
const zoneDrawer = new ZoneDrawer();
```

次に、それを HTML に追加する必要があります。

```javascript
document.firstElementChild.appendChild(zoneDrawer);
zoneDrawer.appendChild(playerWidget);
```

## <a name="next-steps"></a>次の手順

* [ウィジェット API](https://github.com/Azure/video-analyzer/tree/main/widgets) の詳細を確認します。
