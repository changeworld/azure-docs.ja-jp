---
title: Azure Blockchain Service を使用してトランザクションを送信する
description: Azure Blockchain Service を使用してスマート コントラクトをデプロイし、プライベート トランザクションを送信する方法に関するチュートリアルです。
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: tutorial
ms.service: azure-blockchain
ms.reviewer: jackyhsu
manager: femila
ms.openlocfilehash: 0b5e39e9cf2fc3ffe91db6587bc1ed1bab079e93
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/16/2019
ms.locfileid: "65777337"
---
# <a name="tutorial-send-transactions-using-azure-blockchain-service"></a>チュートリアル:Azure Blockchain Service を使用してトランザクションを送信する

このチュートリアルでは、トランザクション ノードを作成し、コントラクトとトランザクションのプライバシーをテストします。  Truffle を使用してローカル開発環境を作成し、スマート コントラクトをデプロイして、プライベート トランザクションを送信します。

学習内容は次のとおりです。

> [!div class="checklist"]
> * トランザクション ノードを追加する
> * Truffle を使用してスマート コントラクトをデプロイする
> * トランザクションを開始する
> * トランザクションのプライバシーを検証する

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

* 「[Create a blockchain member using the Azure portal (Azure portal を使用してブロックチェーン メンバーを作成する)](create-member.md)」を完了します
* 「[Quickstart: Use Truffle to connect to a consortium network (クイックスタート: Truffle を使用してコンソーシアム ネットワークに接続する)](connect-truffle.md)」を完了します
* Truffle では、[Node.js](https://nodejs.org)、[Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)、[Truffle](https://github.com/trufflesuite/truffle) など、いくつかのツールがインストールされている必要があります。

    Windows 10 で迅速に設定するには、Unix Bash シェル ターミナル用の [Ubuntu on Windows](https://www.microsoft.com/p/ubuntu/9nblggh4msv6) をインストールした後、[Truffle](https://github.com/trufflesuite/truffle) をインストールします。 Ubuntu on Windows のディストリビューションには、Node.js と Git が含まれています。

* [Visual Studio Code](https://code.visualstudio.com/Download) をインストールします
* [Visual Studio Code Solidity 拡張機能](https://marketplace.visualstudio.com/items?itemName=JuanBlanco.solidity)をインストールします

## <a name="create-transaction-nodes"></a>トランザクション ノードを作成する

既定では、トランザクション ノードは 1 つあります。 ここではさらに 2 つ追加します。 ノードの 1 つは、プライベート トランザクションに参加します。 もう 1 つは、プライベート トランザクションには含まれません。

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. 自分の Azure Blockchain メンバーに移動して、 **[Transaction nodes]\(トランザクション ノード\) > [追加]** を選択します。
1. `alpha` という名前の新しいトランザクション ノードの設定を完了します。

    ![トランザクション ノードを作成する](./media/send-transaction/create-node.png)

    | Setting | 値 | 説明 |
    |---------|-------|-------------|
    | Name | `alpha` | トランザクション ノードの名前。 名前は、トランザクション ノードのエンドポイントの DNS アドレスを作成するために使われます。 たとえば、「 `alpha-mymanagedledger.blockchain.azure.com` 」のように入力します。 |
    | パスワード | 強力なパスワード | パスワードは、基本認証でトランザクション ノードのエンドポイントにアクセスするために使われます。

1. **作成** を選択します。

    新しいトランザクション ノードのプロビジョニングには、約 10 分かかります。

1. 手順 2 から 4 を繰り返し、`beta` という名前のトランザクション ノードを追加します。

ノードがプロビジョニングされている間に、チュートリアルを続行できます。 プロビジョニングが完了すると、3 つのトランザクション ノードが作成されます。

## <a name="open-truffle-project"></a>Truffle プロジェクトを開く

1. Bash シェル ターミナルを開きます。
1. Truffle プロジェクト ディレクトリへのパスを、前提条件の「[Quickstart: Use Truffle to connect to a consortium network (クイックスタート: Truffle を使用してコンソーシアム ネットワークに接続する)](connect-truffle.md)」から変更します。 たとえば、次のように入力します。

    ```bash
    cd truffledemo
    ```

1. Truffle の対話型開発コンソールを起動します。

    ``` bash
    truffle develop
    ```

    Truffle では、ローカルの開発用ブロックチェーンが作成されて、対話型コンソールが提供されます。

## <a name="connect-to-transaction-node"></a>トランザクション ノードに接続する

Web3 を使って既定のトランザクション ノードに接続し、アカウントを作成します。 Web3 の接続文字列は、Azure portal から取得できます。

1. Azure portal で既定のトランザクション ノードに移動し、 **[Transaction nodes]\(トランザクション ノード\) > [Sample code]\(サンプル コード\) > [Web3]** の順に選択します。
1. **[HTTPS (Access key 1)]\(HTTPS (アクセス キー 1)\)** から JavaScript をコピーします ![Web3 サンプル コード](./media/send-transaction/web3-code.png)

1. 既定のトランザクション ノードの Web3 JavaScript コードを、Truffle の対話型開発コンソールに貼り付けます。 そのコードでは、Azure Blockchain Service のトランザクション ノードに接続される Web3 オブジェクトが作成されます。

    ```bash
    truffle(develop)> var Web3 = require("Web3");
    truffle(develop)> var provider = new Web3.providers.HttpProvider("https://myblockchainmember.blockchain.azure.com:3200/hy5FMu5TaPR0Zg8GxiPwned");
    truffle(develop)> var web3 = new Web3(provider);
    ```

    Web3 オブジェクトでメソッドを呼び出して、トランザクション ノードと対話することができます。

1. 既定のトランザクション ノードで新しいアカウントを作成します。 パスワード パラメーターは、独自の強力なパスワードに置き換えます。

    ```bash
    web3.eth.personal.newAccount("1@myStrongPassword");
    ```

    返されるアカウント アドレスと使用したパスワードを、次のセクションのために記録しておきます。

1. Truffle 開発環境を終了します。

    ```bash
    .exit
    ```

## <a name="configure-truffle-project"></a>Truffle プロジェクトを構成する

Truffle プロジェクトを構成するには、Azure portal からいくつかのトランザクション ノード情報が必要です。

### <a name="transaction-node-public-key"></a>トランザクション ノードの公開キー

各トランザクション ノードには公開キーがあります。 公開キーを使用すると、ノードにプライベート トランザクションを送信できます。 既定のトランザクション ノードから *alpha* トランザクション ノードにトランザクションを送信するには、*alpha* トランザクション ノードの公開キーが必要です。

トランザクション ノードの一覧から、公開キーを取得できます。 チュートリアルでこの後使用するので、alpha ノードの公開キーをコピーして保存します。

![トランザクション ノードの一覧](./media/send-transaction/node-list.png)

### <a name="transaction-node-endpoint-addresses"></a>トランザクション ノードのエンドポイント アドレス

1. Azure portal で各トランザクション ノードに移動し、 **[Transaction nodes]\(トランザクション ノード\) > [Connection strings]\(接続文字列\)** の順に選択します。
1. 各トランザクション ノードの **HTTPS (アクセス キー 1)** からエンドポイント URL をコピーします。 エンドポイント アドレスは、チュートリアルの後半でスマート コントラクトの構成ファイルに必要になります。

    ![トランザクションのエンドポイント アドレス](./media/send-transaction/endpoint.png)

### <a name="edit-configuration-file"></a>構成ファイルを編集する

1. Visual Studio Code を起動し、 **[ファイル] > [フォルダーを開く]** メニューを使用して、Truffle プロジェクト ディレクトリのフォルダーを開きます。
1. Truffle 構成ファイル `truffle-config.js` を開きます。
1. ファイルの内容を、次の構成情報に置き換えます。 エンドポイント アドレスとアカウント情報を含む変数を追加します。 山かっこのセクションは、前のセクションで収集した値に置き換えます。

``` javascript
var defaultnode = "<default transaction node connection string>";
var alpha = "<alpha transaction node connection string>";
var beta = "<beta transaction node connection string>";

var myAccount = "<account address>";
var myPassword = "<account password>";

var Web3 = require("web3");
```

構成の **module.exports** セクションに構成コードを追加します。

```javascript
module.exports = {
  networks: {
    defaultnode: {
      provider:(() =>  {
      const AzureBlockchainProvider = new Web3.providers.HttpProvider(defaultnode);

      const web3 = new Web3(AzureBlockchainProvider);
      web3.eth.personal.unlockAccount(myAccount, myPassword);

      return AzureBlockchainProvider;
      })(),

      network_id: "*",
      gas: 0,
      gasPrice: 0,
      from: myAccount
    },
    alpha: {
      provider: new Web3.providers.HttpProvider(alpha),
      network_id: "*",
      gas: 0,
      gasPrice: 0
    },
    beta: {
      provider: new Web3.providers.HttpProvider(beta),
      network_id: "*",
      gas: 0,
      gasPrice: 0
    }
  }
}
```

## <a name="create-smart-contract"></a>スマート コントラクトを作成する

**contracts** フォルダーに、`SimpleStorage.sol` という名前の新しいファイルを作成します。 次のコードを追加します。

```solidity
pragma solidity >=0.4.21 <0.6.0;

contract SimpleStorage {
    string public storedData;

    constructor(string memory initVal) public {
        storedData = initVal;
    }

    function set(string memory x) public {
        storedData = x;
    }

    function get() view public returns (string memory retVal) {
        return storedData;
    }
}
```

**migrations** フォルダーに、`2_deploy_simplestorage.js` という名前の新しいファイルを作成します。 次のコードを追加します。

```solidity
var SimpleStorage = artifacts.require("SimpleStorage.sol");

module.exports = function(deployer) {

  // Pass 42 to the contract as the first constructor parameter
  deployer.deploy(SimpleStorage, "42", {privateFor: ["<alpha node public key>"], from:"<Account address>"})  
};
```

山かっこ内の値を置き換えます。

| 値 | 説明
|-------|-------------
| \<alpha node public key\> | alpha ノードの公開キー
| \<Account address\> | 既定のトランザクション ノードで作成されたアカウント アドレス。

この例では、**storeData** の初期値は 42 に設定されます。

**privateFor** では、コントラクトを使用できるノードを定義します。 この例では、既定のトランザクション ノードのアカウントはプライベート トランザクションを **alpha** ノードにキャストできます。 すべてのプライベート トランザクション参加者に対して公開キーを追加する必要があります。 **privateFor:** と **from:** を含めないと、スマート コントラクト トランザクションはパブリックになり、すべてのコンソーシアム メンバーから見えるようになります。

**[ファイル] > [すべて保存]** を選択して、すべてのファイルを保存します。

## <a name="deploy-smart-contract"></a>スマート コントラクトをデプロイする

Truffle を使用して、`SimpleStorage.sol` を既定のトランザクション ノード ネットワークにデプロイします。

```bash
truffle migrate --network defaultnode
```

Truffle では、**SimpleStorage** スマート コントラクトが最初にコンパイルされてからデプロイされます。

出力例:

```
pat@DESKTOP:/mnt/c/truffledemo$ truffle migrate --network defaultnode

2_deploy_simplestorage.js
=========================

   Deploying 'SimpleStorage'
   -------------------------
   > transaction hash:    0x3f695ff225e7d11a0239ffcaaab0d5f72adb545912693a77fbfc11c0dbe7ba72
   > Blocks: 2            Seconds: 12
   > contract address:    0x0b15c15C739c1F3C1e041ef70E0011e641C9D763
   > account:             0x1a0B9683B449A8FcAd294A01E881c90c734735C3
   > balance:             0
   > gas used:            0
   > gas price:           0 gwei
   > value sent:          0 ETH
   > total cost:          0 ETH


   > Saving migration to chain.
   > Saving artifacts
   -------------------------------------
   > Total cost:                   0 ETH


Summary
=======
> Total deployments:   2
> Final cost:          0 ETH
```

## <a name="validate-contract-privacy"></a>コントラクトのプライバシーを検証する

コントラクトのプライバシーのため、コントラクトの値のクエリは、**privateFor** で宣言したノードからのみ行うことができます。 この例では、アカウントがそのノードに存在するので、既定のトランザクション ノードのクエリを実行できます。 Truffle コンソールを使って、既定のトランザクション ノードに接続します。

```bash
truffle console --network defaultnode
```

コントラクト インスタンスの値を返すコマンドを実行します。

```bash
SimpleStorage.deployed().then(function(instance){return instance.get();})
```

既定のトランザクション ノードのクエリが成功した場合、値 42 が返されます。

出力例:

```
pat@DESKTOP-J41EP5S:/mnt/c/truffledemo$ truffle console --network defaultnode
truffle(defaultnode)> SimpleStorage.deployed().then(function(instance){return instance.get();})
'42'
```

コンソールを終了します。

```bash
.exit
```

**alpha** ノードの公開キーを **privateFor** で宣言したので、**alpha** ノードのクエリを実行できます。 Truffle コンソールを使って、**alpha** ノードに接続します。

```bash
truffle console --network alpha
```

コントラクト インスタンスの値を返すコマンドを実行します。

```bash
SimpleStorage.deployed().then(function(instance){return instance.get();})
```

**alpha** ノードのクエリが成功した場合、値 42 が返されます。

出力例:

```
pat@DESKTOP-J41EP5S:/mnt/c/truffledemo$ truffle console --network alpha
truffle(alpha)> SimpleStorage.deployed().then(function(instance){return instance.get();})
'42'
```

コンソールを終了します。

```bash
.exit
```

**beta** ノードの公開キーは **privateFor** で宣言しなかったので、コントラクトのプライバシーのため、**beta** ノードのクエリを実行することはできません。 Truffle コンソールを使って、**beta** ノードに接続します。

```bash
truffle console --network beta
```

コントラクト インスタンスの値を返すコマンドを実行します。

```bash
SimpleStorage.deployed().then(function(instance){return instance.get();})
```

コントラクトがプライベートであるため、**beta** ノードに対するクエリは失敗します。

出力例:

```
pat@DESKTOP-J41EP5S:/mnt/c/truffledemo$ truffle console --network beta
truffle(beta)> SimpleStorage.deployed().then(function(instance){return instance.get();})
Thrown:
Error: Returned values aren't valid, did it run Out of Gas?
    at XMLHttpRequest._onHttpResponseEnd (/mnt/c/truffledemo/node_modules/xhr2-cookies/xml-http-request.ts:345:8)
    at XMLHttpRequest._setReadyState (/mnt/c/truffledemo/node_modules/xhr2-cookies/xml-http-request.ts:219:8)
    at XMLHttpRequestEventTarget.dispatchEvent (/mnt/c/truffledemo/node_modules/xhr2-cookies/xml-http-request-event-target.ts:44:13)
    at XMLHttpRequest.request.onreadystatechange (/mnt/c/truffledemo/node_modules/web3-providers-http/src/index.js:96:13)
```

コンソールを終了します。

```bash
.exit
```

## <a name="send-a-transaction"></a>トランザクションを開始する

`sampletx.js`という名前でファイルを作成します。 それをプロジェクトのルートに保存します。

このスクリプトでは、コントラクトの **storedData** 変数の値が 65 に設定されます。 新しいファイルにコードを追加します。

```javascript
var SimpleStorage = artifacts.require("SimpleStorage");

module.exports = function(done) {
  console.log("Getting deployed version of SimpleStorage...")
  SimpleStorage.deployed().then(function(instance) {
    console.log("Setting value to 65...");
    return instance.set("65", {privateFor: ["<alpha node public key>"], from:"<Account address>"});
  }).then(function(result) {
    console.log("Transaction:", result.tx);
    console.log("Finished!");
    done();
  }).catch(function(e) {
    console.log(e);
    done();
  });
};
```

山かっこ内の値を置き換えて、ファイルを保存します。

| 値 | 説明
|-------|-------------
| \<alpha node public key\> | alpha ノードの公開キー
| \<Account address\> | 既定のトランザクション ノードで作成されたアカウント アドレス。

**privateFor** では、トランザクションを使用できるノードを定義します。 この例では、既定のトランザクション ノードのアカウントはプライベート トランザクションを **alpha** ノードにキャストできます。 すべてのプライベート トランザクション参加者に対して公開キーを追加する必要があります。

Truffle を使い、既定のトランザクション ノードに対してスクリプトを実行します。

```bash
truffle exec sampletx.js --network defaultnode
```

コントラクト インスタンスの値を返すコマンドを実行します。

```bash
SimpleStorage.deployed().then(function(instance){return instance.get();})
```

トランザクションが成功した場合は、値 65 が返されます。

出力例:

```
Getting deployed version of SimpleStorage...
Setting value to 65...
Transaction: 0x864e67744c2502ce75ef6e5e09d1bfeb5cdfb7b880428fceca84bc8fd44e6ce0
Finished!
```

コンソールを終了します。

```bash
.exit
```

## <a name="validate-transaction-privacy"></a>トランザクションのプライバシーを検証する

トランザクションのプライバシーのため、トランザクションを実行できるのは **privateFor** で宣言したノード上だけです。 この例では、**alpha** ノードの公開キーを **privateFor** で宣言したので、トランザクションを実行できます。 Truffle を使って、**alpha** ノードでトランザクションを実行します。

```bash
truffle exec sampletx.js --network alpha
```

コントラクト インスタンスの値を返すコマンドを実行します。

```bash
SimpleStorage.deployed().then(function(instance){return instance.get();})
```

トランザクションが成功した場合は、値 65 が返されます。

出力例:

```
Getting deployed version of SimpleStorage...
Setting value to 65...
Transaction: 0x864e67744c2502ce75ef6e5e09d1bfeb5cdfb7b880428fceca84bc8fd44e6ce0
Finished!
```

コンソールを終了します。

```bash
.exit
```

このチュートリアルでは、コントラクトとトランザクションのプライバシーを説明するため、2 つのトランザクション ノードを追加しました。 既定のノードを使って、プライベート スマート コントラクトをデプロイしました。 コントラクトの値のクエリを実行し、ブロックチェーンでトランザクションを実行して、プライバシーをテストしました。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

必要なくなったら、Azure Blockchain Service で作成した `myResourceGroup` リソース グループを削除することによって、リソースを削除できます。

リソース グループを削除するには:

1. Azure Portal で、左側のナビゲーション ウィンドウの **[リソース グループ]** に移動し、削除するリソース グループを選択します。
1. **[リソース グループの削除]** を選択します。 リソース グループ名を入力して削除を確認し、 **[削除]** を選択します。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure Blockchain Service を使用してブロックチェーン アプリケーションを開発する](develop.md)
