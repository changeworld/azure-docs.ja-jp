---
title: Azure Blockchain Service 上でスマート コントラクトを使用する
description: Azure Blockchain Service を使用してスマート コントラクトをデプロイし、トランザクションを介して関数を実行する方法に関するチュートリアルです。
services: azure-blockchain
author: PatAltimore
ms.author: patricka
ms.date: 07/31/2019
ms.topic: tutorial
ms.service: azure-blockchain
ms.reviewer: chrisseg
ms.openlocfilehash: 1843bd66e11a6686c9ae81fb8e30c7b030e889b7
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705126"
---
# <a name="tutorial-use-smart-contracts-on-azure-blockchain-service"></a>チュートリアル:Azure Blockchain Service 上でスマート コントラクトを使用する

このチュートリアルでは、Azure Blockchain Development Kit for Ethereum を使用してスマート コントラクトを作成、デプロイし、コンソーシアム ブロックチェーン ネットワーク上のトランザクションを介してスマート コントラクト関数を実行します。

Azure Blockchain Development Kit for Ethereum を使用して、以下のことを行います。

> [!div class="checklist"]
> * Azure Blockchain Service コンソーシアム ブロックチェーン メンバーに接続する
> * スマート コントラクトを作成する
> * スマート コントラクトをデプロイする
> * トランザクションを介してスマート コントラクト関数を実行する
> * コントラクトの状態を照会する

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

* 「[Quickstart: Azure portal を使用したブロックチェーン メンバーの作成](create-member.md)と「[クイックスタート: Azure CLI を使用して Azure Blockchain Service ブロックチェーン メンバーを作成する](create-member-cli.md)」を完了していること
* [Visual Studio Code](https://code.visualstudio.com/Download)
* [Azure Blockchain Development Kit for Ethereum 拡張機能](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain)
* [Node.JS](https://nodejs.org)
* [Git](https://git-scm.com)
* [Python](https://www.python.org/downloads/release/python-2715/)。 python.exe をパスに追加します。 Azure Blockchain Development Kit では、パス内に Python が必要です。
* [Truffle](https://www.trufflesuite.com/docs/truffle/getting-started/installation)
* [Ganache CLI](https://github.com/trufflesuite/ganache-cli)

### <a name="verify-azure-blockchain-development-kit-environment"></a>Azure Blockchain Development Kit の環境を検証する

Azure Blockchain Development Kit では、自分の開発環境の前提条件が満たされていることが確認されます。 開発環境を検証するには:

VS Code コマンド パレットで、 **[Azure Blockchain: Show Welcome Page]\(Azure Blockchain: ウェルカム ページを表示\)** を選択します。

Azure Blockchain Development Kit によって、完了までに約 1 分かかる検証スクリプトが実行されます。 出力を表示するには、 **[ターミナル] > [新しいターミナル]** の順に選択します。 ターミナルのメニュー バーで、 **[出力]** タブを選択し、ドロップダウン リストの **[Azure Blockchain]** を選択します。 検証が成功すると、次の画像のように表示されます。

![有効な開発環境](./media/send-transaction/valid-environment.png)

 必要なツールがない場合は、 **[Azure Blockchain Development Kit - Preview]\(Azure Blockchain Development Kit - プレビュー\)** という名前の新しいタブに、インストールが必要なアプリと、ツールをダウンロードするためのリンクの一覧が表示されます。

![開発キットで必要なアプリ](./media/send-transaction/required-apps.png)

## <a name="connect-to-consortium-member"></a>コンソーシアム メンバーに接続する

Azure Blockchain Development Kit VS Code 拡張機能を使用して、コンソーシアム メンバーに接続できます。 コンソーシアムに接続したら、スマート コントラクトをコンパイル、ビルドし、Azure Blockchain Service コンソーシアム メンバーにデプロイできます。

Azure Blockchain Service コンソーシアム メンバーにアクセスできない場合は、前提条件の [Azure portal を使用したブロックチェーン メンバーの作成](create-member.md)に関するクイックスタートと「[クイックスタート: Azure CLI を使用して Azure Blockchain Service ブロックチェーン メンバーを作成する](create-member-cli.md)」を完了してください。

1. Visual Studio Code (VS Code) のエクスプローラー ウィンドウで、 **[Azure Blockchain]** 拡張機能を展開します。
1. **[Connect to Consortium]\(コンソーシアムに接続\)** を選択します。

   ![コンソーシアムに接続する](./media/send-transaction/connect-consortium.png)

    Azure 認証を求められた場合は、プロンプトに従って、ブラウザーを使用して認証します。
1. コマンド パレットのドロップダウンで **[Connect to Azure Blockchain Service consortium]\(Azure Blockchain Service コンソーシアムに接続\)** を選択します。
1. Azure Blockchain Service コンソーシアム メンバーに関連付けられているサブスクリプションとリソース グループを選択します。
1. 一覧からコンソーシアムを選択します。

コンソーシアムおよびブロックチェーン メンバーは、Visual Studio エクスプローラーのサイド バーに一覧表示されます。

![エクスプローラーに表示されているコンソーシアム](./media/send-transaction/consortium-node.png)

## <a name="create-a-smart-contract"></a>スマート コントラクトを作成する

Azure Blockchain Development Kit for Ethereum では、プロジェクト テンプレートと Truffle ツールを使用して、コントラクトのスキャフォールディング、ビルド、およびデプロイを支援します。

1. VS Code コマンド パレットで、 **[Azure Blockchain: New Solidity Project]\(Azure Blockchain: 新しい Solidity プロジェクト\)** を選択します。
1. **[Create basic project]\(基本プロジェクトの作成\)** を選択します。
1. `HelloBlockchain` という名前の新しいフォルダーを作成し、**新しいプロジェクトのパスを選択**します。

Azure Blockchain Development Kit によって、新しい Solidity プロジェクトが作成され初期化されます。 基本プロジェクトには、サンプルの **HelloBlockchain** スマート コントラクトと、ビルドして Azure Blockchain Service のコンソーシアム メンバーにデプロイするために必要なすべてのファイルが含まれています。 プロジェクトが作成されるまでに数分かかる場合があります。 Azure Blockchain の出力を選択すると、VS Code のターミナル パネルで進行状況を監視できます。

プロジェクト構造は、次の例のようになります。

   ![Solidity プロジェクト](./media/send-transaction/solidity-project.png)

## <a name="build-a-smart-contract"></a>スマート コントラクトをビルドする

スマート コントラクトは、プロジェクトの **contracts** ディレクトリに配置されます。 スマート コントラクトのコンパイルは、ブロックチェーンにデプロイする前に行います。 プロジェクト内のすべてのスマート コントラクトをコンパイルするには、 **[Build Contracts]\(コントラクトのビルド\)** コマンドを使用します。

1. VS Code エクスプローラーのサイドバーで、プロジェクトの **[contracts]** フォルダーを展開します。
1. **HelloBlockchain.sol** を右クリックし、メニューの **[Build Contracts]\(コントラクトのビルド\)** を選択します。

    ![コントラクトをビルドする](./media/send-transaction/build-contracts.png)

Azure Blockchain Development Kit では、Truffle を使用してスマート コントラクトがコンパイルされます。

![コンパイルの出力](./media/send-transaction/compile-output.png)

## <a name="deploy-a-smart-contract"></a>スマート コントラクトをデプロイする

Truffle では、移行スクリプトを使用して、コントラクトが Ethereum ネットワークにデプロイされます。 移行は、プロジェクトの **migrations** ディレクトリに配置されている JavaScript ファイルです。

1. スマート コントラクトをデプロイするには、**HelloBlockchain.sol** を右クリックし、メニューの **[Deploy Contracts]\(コントラクトのデプロイ\)** を選択します。
1. **[From truffle-config.js]\(truffle-config.js から\)** で Azure Blockchain コンソーシアム ネットワークを選択します。 プロジェクトを作成したときに、コンソーシアムのブロックチェーン ネットワークがプロジェクトの Truffle 構成ファイルに追加されました。
1. **[Generate mnemonic]\(ニーモニックの生成\)** を選択します。 ファイル名を選択し、ニーモニック ファイルをプロジェクト フォルダーに保存します。 たとえば、「 `myblockchainmember.env` 」のように入力します。 ニーモニック ファイルは、ブロックチェーン メンバーの Ethereum 秘密キーを生成するために使用されます。

Azure Blockchain Development Kit では、Truffle を使用して移行スクリプトが実行され、コントラクトがブロックチェーンにデプロイされます。

![正常にデプロイされたコントラクト](./media/send-transaction/deploy-contract.png)

## <a name="call-a-contract-function"></a>コントラクト関数を呼び出す

**HelloBlockchain** コントラクトの **SendRequest** 関数は、**RequestMessage** 状態変数を変更するものです。 ブロックチェーン ネットワークの状態の変更は、トランザクションを介して行われます。 トランザクションを介して **SendRequest** 関数を実行するスクリプトを作成できます。

1. Truffle プロジェクトのルートに新しいファイルを作成し、`sendrequest.js` という名前を付けます。 そのファイルに次の Web3 JavaScript コードを追加します。

    ```javascript
    var HelloBlockchain = artifacts.require("HelloBlockchain");
        
    module.exports = function(done) {
      console.log("Getting the deployed version of the HelloBlockchain smart contract")
      HelloBlockchain.deployed().then(function(instance) {
        console.log("Calling SendRequest function for contract ", instance.address);
        return instance.SendRequest("Hello, blockchain!");
      }).then(function(result) {
        console.log("Transaction hash: ", result.tx);
        console.log("Request complete");
        done();
      }).catch(function(e) {
        console.log(e);
        done();
      });
    };
    ```

1. Azure Blockchain Development Kit によってプロジェクトが作成されると、Truffle 構成ファイルがコンソーシアムのブロックチェーン ネットワーク エンドポイントの詳細と共に生成されます。 プロジェクトの **truffle-config.js** を開きます。 構成ファイル内の一覧には、2 つのネットワークが含まれています。一方は development という名前で、もう一方はコンソーシアムと同じ名前です。
1. VS Code のターミナル ウィンドウで Truffle を使用して、コンソーシアムのブロックチェーン ネットワーク上でスクリプトを実行します。 ターミナル ウィンドウのメニュー バーで **[ターミナル]** タブを選択し、ドロップダウンで **[PowerShell]** を選択します。

    ```PowerShell
    truffle exec sendrequest.js --network <blockchain network>
    ```

    \<blockchain network\> を、**truffle-config.js** 内で定義されているブロックチェーン ネットワークの名前に置き換えます。

Truffle によってブロックチェーン ネットワーク上でスクリプトが実行されます。

![スクリプト出力](./media/send-transaction/execute-transaction.png)

トランザクションを介してコントラクトの関数を実行した場合、そのトランザクションはブロックが作成されるまで処理されません。 トランザクションを介して実行される関数は、戻り値ではなくトランザクション ID を返します。

## <a name="query-contract-state"></a>コントラクトの状態を照会する

スマート コントラクト関数は、状態変数の現在の値を返すことができます。 状態変数の値を返す関数を追加してみましょう。

1. **HelloBlockchain.sol** で、**getMessage** 関数を **HelloBlockchain** スマート コントラクトに追加します。

    ``` solidity
    function getMessage() public view returns (string memory)
    {
        if (State == StateType.Request)
            return RequestMessage;
        else
            return ResponseMessage;
    }
    ```

    この関数は、コントラクトの現在の状態を基に、状態変数に格納されているメッセージを返します。

1. **HelloBlockchain.sol** を右クリックし、メニューの **[Build Contracts]\(コントラクトのビルド\)** を選択して、スマート コントラクトへの変更をコンパイルします。
1. デプロイするには、**HelloBlockchain.sol** を右クリックし、メニューの **[Deploy Contracts]\(コントラクトのデプロイ\)** を選択します。
1. 次に、**getMessage** 関数を呼び出すスクリプトを作成します。 Truffle プロジェクトのルートに新しいファイルを作成し、`getmessage.js` という名前を付けます。 そのファイルに次の Web3 JavaScript コードを追加します。

    ```javascript
    var HelloBlockchain = artifacts.require("HelloBlockchain");
    
    module.exports = function(done) {
      console.log("Getting the deployed version of the HelloBlockchain smart contract")
      HelloBlockchain.deployed().then(function(instance) {
        console.log("Calling getMessage function for contract ", instance.address);
        return instance.getMessage();
      }).then(function(result) {
        console.log("Request message value: ", result);
        console.log("Request complete");
        done();
      }).catch(function(e) {
        console.log(e);
        done();
      });
    };
    ```

1. VS Code のターミナル ウィンドウで Truffle を使用して、ブロックチェーン ネットワーク上でスクリプトを実行します。 ターミナル ウィンドウのメニュー バーで **[ターミナル]** タブを選択し、ドロップダウンで **[PowerShell]** を選択します。

    ```bash
    truffle exec getmessage.js --network <blockchain network>
    ```

    \<blockchain network\> を、**truffle-config.js** 内で定義されているブロックチェーン ネットワークの名前に置き換えます。

このスクリプトは、getMessage 関数を呼び出すことによって、スマート コントラクトを照会します。 **RequestMessage** 状態変数の現在の値が返されます。

![スクリプト出力](./media/send-transaction/execute-get.png)

値が "**Hello, blockchain!** " ではないことに注意してください。 返される値は、プレースホルダーです。 コントラクトを変更してデプロイすると、コントラクトは新しいコントラクト アドレスを取得し、状態変数にはスマート コントラクト コンストラクターの値が割り当てられます。 Truffle サンプルの **2_deploy_contracts.js** 移行スクリプトは、スマート コントラクトをデプロイし、引数としてプレースホルダー値を渡します。 コンストラクターは、**RequestMessage** 状態変数をプレースホルダーの値に設定し、この値が返されます。

1. **RequestMessage** 状態変数を設定して値を照会するには、**sendrequest.js** スクリプトと **getmessage.js** スクリプトをもう一度実行します。

    ![スクリプト出力](./media/send-transaction/execute-set-get.png)

    **sendrequest.js** によって **RequestMessage** 状態変数が "**Hello, blockchain!** " に設定されます。 また、**getmessage.js** によってコントラクトに対して **RequestMessage** 状態変数の値が照会され、"**Hello, blockchain!** " が返されます。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

必要なくなったら、前提条件の "*ブロックチェーン メンバーの作成*" に関するクイックスタートで作成した `myResourceGroup` リソース グループを削除することで、リソースを削除できます。

リソース グループを削除するには:

1. Azure Portal で、左側のナビゲーション ウィンドウの **[リソース グループ]** に移動し、削除するリソース グループを選択します。
1. **[リソース グループの削除]** を選択します。 リソース グループ名を入力して削除を確認し、 **[削除]** を選択します。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure Blockchain Development Kit を使用してサンプル Solidity プロジェクトを作成しました。 スマート コントラクトをビルドしてデプロイし、Azure Blockchain Service 上でホストされているブロックチェーン コンソーシアム ネットワーク上のトランザクションを介して関数を呼び出しました。

> [!div class="nextstepaction"]
> [Azure Blockchain Service を使用してブロックチェーン アプリケーションを開発する](develop.md)
