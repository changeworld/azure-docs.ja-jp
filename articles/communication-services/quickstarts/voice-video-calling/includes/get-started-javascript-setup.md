---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: mikben
ms.openlocfilehash: 4a493d5f0d34cd4621d55c0371036c03e267c466
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105108286"
---
## <a name="setting-up"></a>設定

### <a name="create-a-new-nodejs-application"></a>新しい Node.js アプリケーションを作成する

ターミナルまたはコマンド ウィンドウを開き、自分のアプリ用に新しいディレクトリを作成し、そこに移動します。

```console
mkdir calling-quickstart && cd calling-quickstart
```

既定の設定で `npm init -y` を実行して、**package.json** ファイルを作成します。

```console
npm init -y
```

### <a name="install-the-package"></a>パッケージをインストールする

`npm install` コマンドを使用して、JavaScript 用の Azure Communication Services 通話 SDK をインストールします。

```console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save
```

このクイックスタートでは、次のバージョンの webpack をお勧めします。

```console
"webpack": "^4.42.0",
"webpack-cli": "^3.3.11",
"webpack-dev-server": "^3.10.3"
```

`--save` オプションを使用すると、**package.json** ファイル内の依存関係としてライブラリが表示されます。

### <a name="set-up-the-app-framework"></a>アプリのフレームワークを設定する

このクイックスタートでは、webpack を使用してアプリケーション資産をバンドルします。 次のコマンドを実行して、webpack、webpack-cli、および webpack-dev-server npm パッケージをインストールし、**package.json** 内の開発依存関係として表示します。

```console
npm install webpack@4.42.0 webpack-cli@3.3.11 webpack-dev-server@3.10.3 --save-dev
```

自分のプロジェクトのルート ディレクトリに、**index.html** ファイルを作成します。 このファイルを使用して、ユーザーが電話をかけることができるようにする基本的なレイアウトを構成します。
