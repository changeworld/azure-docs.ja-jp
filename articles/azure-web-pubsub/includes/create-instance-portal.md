---
title: ポータルを使用して Azure Web PubSub インスタンスを作成する方法
description: ポータルを使用した Azure Web PubSub インスタンスの作成に関するインクルード ファイル
author: yjin81
ms.author: yajin1
ms.service: azure-web-pubsub
ms.topic: include
ms.date: 03/11/2021
ms.openlocfilehash: 72e3c56b4f8fdaeec94bff60be45aadfb9b09e76
ms.sourcegitcommit: 62e800ec1306c45e2d8310c40da5873f7945c657
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2021
ms.locfileid: "108166953"
---
## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure アカウントで Azure portal ([https://portal.azure.com/](https://portal.azure.com/)) にサインインします。

## <a name="create-an-azure-web-pubsub-service-instance"></a>Azure Web PubSub サービス インスタンスを作成する

アプリケーションは Azure 内の Web PubSub サービス インスタンスに接続します。

1. Azure portal の左上にある [新規] ボタンを選択します。 [新規] 画面で、検索ボックスに「*Web PubSub*」と入力して Enter キーを押します。

1. 検索結果の **[Web PubSub]** を選択し、 **[作成]** を選択します。

1. 次の設定を入力します。

    | 設定      | 推奨値  | 説明                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **リソース名** | グローバルに一意の名前 | 新しい Web PubSub サービス インスタンスを識別する名前。 有効な文字は、`a-z`、`0-9`、および `-` です。  | 
    | **サブスクリプション** | 該当するサブスクリプション | この新しい Web PubSub サービス インスタンスが作成されるサブスクリプション。 | 
    | **[リソース グループ](../../azure-resource-manager/management/overview.md)** |  myResourceGroup | Web PubSub サービス インスタンスの作成先となる新しいリソース グループの名前。 | 
    | **場所** | 米国西部 | 近くの[リージョン](https://azure.microsoft.com/regions/)を選択します。 |
    | **[価格レベル]** | 無料 | Azure Web PubSub サービスを無料でお試しください。 |
    | **[ユニット数]** |  適用なし | ユニット数は、Web PubSub サービス インスタンスで受け入れることができる接続の数を示します。 Standard レベルでのみ構成可能です。 |

1. **[作成]** を選択して Web PubSub サービス インスタンスのデプロイを開始します。
