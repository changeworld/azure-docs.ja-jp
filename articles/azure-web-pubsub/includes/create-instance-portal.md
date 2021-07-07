---
title: ポータルを使用して Azure Web PubSub インスタンスを作成する方法
description: ポータルを使用した Azure Web PubSub インスタンスの作成に関するインクルード ファイル
author: yjin81
ms.author: yajin1
ms.service: azure-web-pubsub
ms.topic: include
ms.date: 03/11/2021
ms.openlocfilehash: e147d6f954009f8ed4267f8b947438e2e60da0b4
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/26/2021
ms.locfileid: "110486844"
---
## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure アカウントで Azure portal ([https://portal.azure.com/](https://portal.azure.com/)) にサインインします。

## <a name="create-an-azure-web-pubsub-service-instance"></a>Azure Web PubSub サービス インスタンスを作成する

アプリケーションは Azure 内の Web PubSub サービス インスタンスに接続します。

1. Azure portal の左上にある [新規] ボタンを選択します。 [新規] 画面で、検索ボックスに「*Web PubSub*」と入力して Enter キーを押します。 (Azure Web PubSub を `Web` カテゴリから検索することもできます)。

:::image type="content" source="../media/create-instance-portal/search-web-pubsub-in-portal.png" alt-text="Azure Web PubSub をポータルで検索しているスクリーンショット。":::

2. 検索結果の **[Web PubSub]** を選択し、 **[作成]** を選択します。

3. 次の設定を入力します。

    | 設定      | 推奨値  | 説明                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **リソース名** | グローバルに一意の名前 | 新しい Web PubSub サービス インスタンスを識別する名前。 有効な文字は、`a-z`、`0-9`、および `-` です。  | 
    | **サブスクリプション** | 該当するサブスクリプション | この新しい Web PubSub サービス インスタンスが作成されるサブスクリプション。 | 
    | **[リソース グループ](../../azure-resource-manager/management/overview.md)** |  myResourceGroup | Web PubSub サービス インスタンスの作成先となる新しいリソース グループの名前。 | 
    | **場所** | 米国西部 | 近くの[リージョン](https://azure.microsoft.com/regions/)を選択します。 |
    | **[価格レベル]** | 無料 | Azure Web PubSub サービスを無料でお試しください。 |
    | **[ユニット数]** |  適用なし | ユニット数は、Web PubSub サービス インスタンスで受け入れることができる接続の数を示します。 Standard レベルでのみ構成可能です。 |

4. **[作成]** を選択して Web PubSub サービス インスタンスのデプロイを開始します。
