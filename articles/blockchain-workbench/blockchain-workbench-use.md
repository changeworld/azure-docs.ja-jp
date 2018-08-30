---
title: Azure Blockchain Workbench でのアプリケーションの使用
description: Azure Blockchain Workbench でのアプリケーション コントラクトの使用方法。
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/16/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: e17a9275792e3a7fdbea6e3b95e596eaa15f4359
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "43105813"
---
# <a name="using-applications-in-azure-blockchain-workbench"></a>Azure Blockchain Workbench でのアプリケーションの使用

Blockchain Workbench では、コントラクトを作成し、それらのコントラクトに対してアクションを実行できます。 また、状態やトランザクション履歴などのコントラクト詳細を表示することもできます。

## <a name="prerequisites"></a>前提条件

* Blockchain Workbench のデプロイ。 デプロイについて詳しくは、[Azure Blockchain Workbench のデプロイ](blockchain-workbench-deploy.md)に関するページをご覧ください
* Blockchain Workbench 内にデプロイされたブロックチェーン アプリケーション。 「[Azure Blockchain Workbench でブロックチェーン アプリケーションを作成する](blockchain-workbench-create-app.md)」をご覧ください

ブラウザーで [Blockchain Workbench を開きます](blockchain-workbench-deploy.md#blockchain-workbench-web-url)。

![Blockchain Workbench](media/blockchain-workbench-use/workbench.png)

Blockchain Workbench のメンバーとしてサインインする必要があります。 アプリケーションが表示されない場合は、Blockchain Workbench のメンバーではあるものの、まだどのアプリケーションのメンバーにもなっていないことになります。 アプリケーションにメンバーを割り当てることができるのは、Blockchain Workbench の管理者です。

## <a name="create-new-contract"></a>新しいコントラクトの作成 

新しいコントラクトを作成するには、コントラクト **イニシエーター**として指定されているメンバーになる必要があります。 コントラクトのアプリケーション ロールとイニシエーターの定義については、[構成概要ページのワークフローに関するセクション](blockchain-workbench-configuration-overview.md#workflows)を参照してください。 アプリケーション ロールにメンバーを割り当てる方法については、[アプリケーションにメンバーを追加する](blockchain-workbench-manage-users.md#add-member-to-application)方法に関するページを参照してください。

1. Blockchain Workbench のアプリケーション セクションで、作成するコントラクトを含んだアプリケーション タイルを選択します。 アクティブなコントラクトの一覧が表示されます。

2. 新しい契約を作成するには、**[新しいコントラクト]** を選択します。

    ![[新しいコントラクト] ボタン](media/blockchain-workbench-use/contract-list.png)

3. **[新しいコントラクト]** ウィンドウが表示されます。 初期パラメーター値を指定します。 **作成**を選択します。

    ![[新しいコントラクト] ウィンドウ](media/blockchain-workbench-use/new-contract.png)

    新しく作成されたコントラクトが、その他のアクティブなコントラクトと共に一覧に表示されます。

    ![アクティブなコントラクトの一覧](media/blockchain-workbench-use/active-contracts.png)

## <a name="take-action-on-contract"></a>コントラクトに対してアクションを実行する

コントラクトの状態によっては、コントラクトの次の状態に遷移するアクションをメンバーは行うことができます。 アクションは[状態](blockchain-workbench-configuration-overview.md#states)内で[遷移](blockchain-workbench-configuration-overview.md#transitions)として定義されています。 遷移に許可されるアプリケーションまたはインスタンス ロールに属するメンバーはアクションを行うことができます。 

1. Blockchain Workbench のアプリケーション セクションで、アクションを実行するコントラクトを含んだアプリケーション タイルを選択します。
2. 一覧からコントラクトを選択します。 コントラクトの詳細が各セクションに表示されます。 

    ![コントラクト詳細](media/blockchain-workbench-use/contract-details.png)

    | セクション  | 説明  |
    |---------|---------|
    | Status | コントラクト ステージ内での現在の進捗状況が一覧表示されます |
    | 詳細 | コントラクトの現在の値です |
    | アクションを表示します。 | 直近のアクションに関する詳細です |
    | アクティビティ | コントラクトのトランザクション履歴です |
    
3. **[アクション]** セクションで、**[アクションの実行]** を選択します。

4. コントラクトの現在の状態に関する詳細がウィンに表示されます。 ドロップダウン リストから、実行するアクションを選択します。 

    ![アクションの選択](media/blockchain-workbench-use/choose-action.png)

5. **[アクションの実行]** を選択し、アクションを開始します。
6. アクションにパラメーターが必要な場合、アクションの値を指定します。

    ![アクションの実行](media/blockchain-workbench-use/take-action.png)

7. **[アクションの実行]** を選択し、アクションを実行します。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure Blockchain Workbench のトラブルシューティング方法](blockchain-workbench-troubleshooting.md)
