---
title: Azure Blockchain Workbench でのアプリケーションの使用
description: Azure Blockchain Workbench でのアプリケーション コントラクトの使用方法。
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 4/26/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 35a45947c2766ccc5e64f4c3523ce163a9680680
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2018
---
# <a name="using-applications-in-azure-blockchain-workbench"></a>Azure Blockchain Workbench でのアプリケーションの使用

Blockchain Workbench では、コントラクトを作成し、それらのコントラクトに対してアクションを実行できます。 また、状態やトランザクション履歴などのコントラクト詳細を表示することもできます。

## <a name="prerequisites"></a>前提条件

* Blockchain Workbench のデプロイ。 デプロイについて詳しくは、[Azure Blockchain Workbench のデプロイ](blockchain-workbench-deploy.md)に関するページをご覧ください
* Blockchain Workbench 内にデプロイされたブロックチェーン アプリケーション。 「[Azure Blockchain Workbench でブロックチェーン アプリケーションを作成する]()」をご覧ください

ブラウザーで [Blockchain Workbench を開きます](blockchain-workbench-deploy.md#blockchain-workbench-web-url)。

![Blockchain Workbench](media/blockchain-workbench-use/workbench.png)

Blockchain Workbench のメンバーとしてサインインする必要があります。 アプリケーションが表示されない場合は、Blockchain Workbench のメンバーではあるものの、まだどのアプリケーションのメンバーにもなっていないことになります。 アプリケーションにメンバーを割り当てることができるのは、Blockchain Workbench の管理者です。

## <a name="create-new-contract"></a>新しいコントラクトの作成 

新しいコントラクトを作成するには、**AllowedInstanceRoles** ロールのメンバーになる必要があります。 

1. Blockchain Workbench のアプリケーション セクションで、作成するコントラクトを含んだアプリケーション タイルを選択します。 アクティブなコントラクトの一覧が表示されます。

2. 新しい契約を作成するには、**[新しいコントラクト]** を選択します。

    ![[新しいコントラクト] ボタン](media/blockchain-workbench-use/contract-list.png)

3. **[新しいコントラクト]** ウィンドウが表示されます。 初期パラメーター値を指定します。 **[作成]** を選択します。

    ![[新しいコントラクト] ウィンドウ](media/blockchain-workbench-use/new-contract.png)

    新しく作成されたコントラクトが、その他のアクティブなコントラクトと共に一覧に表示されます。

    ![アクティブなコントラクトの一覧](media/blockchain-workbench-use/active-contracts.png)

## <a name="take-action-on-contract"></a>コントラクトに対してアクションを実行する

1. Blockchain Workbench のアプリケーション セクションで、アクションを実行するコントラクトを含んだアプリケーション タイルを選択します。

    ![アプリケーションの一覧](media/blockchain-workbench-use/apps-list.png)

2. 一覧からコントラクトを選択します。

    ![コントラクトの一覧](media/blockchain-workbench-use/select-contract.png)

    コントラクトの詳細が各セクションに表示されます。 

    ![コントラクト詳細](media/blockchain-workbench-use/contract-details.png)

    | セクション  | [説明]  |
    |---------|---------|
    | 状態 | コントラクト ステージ内での現在の進捗状況が一覧表示されます |
    | 詳細 | コントラクトの現在の値です |
    | アクションを表示します。 | 直近のアクションに関する詳細です |
    | アクティビティ | コントラクトのトランザクション履歴です |
    
3. **[アクション]** セクションで、**[アクションの実行]** を選択します。

4. コントラクトの現在の状態に関する詳細がウィンに表示されます。 ドロップダウン リストから、実行するアクションを選択します。 

    ![アクションの実行](media/blockchain-workbench-use/take-action.png)

5. **[実行]** を選択してアクションを実行します。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure Blockchain Workbench のトラブルシューティング方法](blockchain-workbench-troubleshooting.md)