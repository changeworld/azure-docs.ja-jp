---
title: クイック スタート:ダイス ローラー
description: Azure Fluid Relay サービスを使用してダイス ローラー アプリをすばやく作成する
author: hickeys
ms.service: azure-fluid
ms.topic: quickstart
ms.date: 09/09/2021
ms.author: hickeys
ms.openlocfilehash: f3a25a47cc1bee143f562e936f7927f48d452b4c
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/07/2021
ms.locfileid: "129662168"
---
# <a name="quickstart-dice-roller"></a>クイック スタート:ダイス ローラー

> [!NOTE]
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。

このクイック スタートでは、Azure Fluid Relay サービスを使用するダイス ローラー アプリを作成するプロセスについて説明します。 このクイック スタートは次の 2 つのパートに分かれています。 パート 1 では、アプリ自体を作成し、ローカルの Fluid サーバーで実行します。 パート 2 では、ローカル開発サーバーではなく、Azure Fluid Relay サービスで実行するようにアプリを再構成します。

このクイック スタートで使用するサンプル コードは[こちら](https://github.com/microsoft/FluidHelloWorld/tree/main-azure)で入手できます。

## <a name="set-up-your-development-environment"></a>開発環境を設定する

このクイック スタートの手順を実行するには、Azure アカウントに [Azure Fluid Relay サービスがプロビジョニング](../how-tos/provision-fluid-azure-portal.md)されている必要があります。 アカウントがない場合は、[Azure を無料で試す](https://azure.com/free)ことができます。

お使いのコンピューターに次のソフトウェアがインストールされている必要もあります。

- テキスト エディター: [Visual Studio Code](https://code.visualstudio.com/) をお勧めします。
- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/en/download) バージョン 12.17 以降

## <a name="getting-started-locally"></a>ローカルで始める

最初に、GitHub からサンプル アプリをダウンロードする必要があります。 新しいコマンド ウィンドウを開き、コードをダウンロードするフォルダーに移動し、Git を使用して [FluidHelloWorld リポジトリ](https://github.com/microsoft/FluidHelloWorld)を複製します。 複製プロセスでは、プロジェクト ファイルが格納された FluidHelloWorld という名前のサブフォルダーが作成されます。

```cli
git clone -b main-azure https://github.com/microsoft/FluidHelloWorld.git
```

新しく作成したフォルダーに移動し、依存関係をインストールして、アプリを起動します。

```cli
cd FluidHelloWorld
npm install
...
npm start
```


`npm start` コマンドを実行すると、2 つのことが行われます。 まず、Fluid サーバーがローカル プロセスで起動されます。 このサーバーは開発のみを目的とします。 後で Azure でホストされる実稼働サーバーにアップグレードします。 次に、新しいブラウザー タブに、ダイス ローラー アプリの新しいインスタンスのページが開きます。 同じ URL で新しいタブを開き、ダイス ローラー アプリのインスタンスをさらに作成できます。 このアプリの各インスタンスは、ローカルの Fluid サービスを使用するように既定で構成されています。 アプリの任意のインスタンスで **[Roll]** ボタンをクリックし、ダイスの状態がすべてのクライアントで変化する点に注目してください。

## <a name="upgrading-to-azure-fluid-relay"></a>Azure Fluid Relay へのアップグレード

Azure Fluid Relay サービスで実行するには、ローカル サーバーではなく Azure サービスに接続するようにアプリの構成を更新する必要があります。

### <a name="configure-and-create-an-azure-client"></a>Azure クライアントの構成と作成

Azure クライアントを構成するには、`app.js` の `serviceConfig` オブジェクトの値を Azure Fluid Relay サービスの構成値に置き換える必要があります。 これらの値は、Azure portal の Fluid Relay リソースの [アクセス キー] セクションで確認できます。

```javascript
const serviceConfig = {
    connection: {
        tenantId: LOCAL_MODE_TENANT_ID, // REPLACE WITH YOUR TENANT ID
        tokenProvider: new InsecureTokenProvider("" /* REPLACE WITH YOUR PRIMARY KEY */, { id: "userId" }),
        orderer: "http://localhost:7070", // REPLACE WITH YOUR ORDERER ENDPOINT
        storage: "http://localhost:7070", // REPLACE WITH YOUR STORAGE ENDPOINT
    }
};
```

> [!WARNING]
> 開発時には、`InsecureTokenProvider` を使用して認証トークンの生成と署名を行うことができます。これを、Azure Fluid Relay サービスが受け入れます。 ただし、名前が示すように、これは安全ではないため、運用環境では使用しないでください。 Azure Fluid Relay リソースの作成プロセスではシークレット キーが提供され、これを、セキュリティで保護された要求への署名に使用できます。 **このシークレットが公開されないように、これを ITokenProvider の別の実装に置き換える必要があります。そうすれば、運用環境に公開される前に、開発者が提供する安全なバックエンド サービスからトークンがフェッチされます。**

### <a name="build-and-run-the-client-only"></a>クライアントのみの構築と実行

ローカル サーバーではなく Azure を使用するようにアプリに指定したので、クライアント アプリと共にローカルの Fluid サーバーを起動する必要がありません。 こちらのコマンドを使用すれば、サーバーを起動せずに、クライアントを起動できます。 

```bash
npm run start:client
```

🥳**お疲れさまでした**🎉 Fluid のコラボレーションの世界を開くための最初のステップを完了しました。
