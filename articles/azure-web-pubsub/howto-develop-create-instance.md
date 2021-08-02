---
title: Azure Web PubSub インスタンスを作成する方法
description: Azure Web PubSub インスタンスを作成するためのオプションとその実行方法の概要
author: yjin81
ms.author: yajin1
ms.service: azure-web-pubsub
ms.topic: how-to
ms.date: 03/17/2021
ms.openlocfilehash: f3ed823d989abcb32ad7680e902fc5d52200e726
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/10/2021
ms.locfileid: "111951044"
---
# <a name="how-to-create-azure-web-pubsub-instance"></a>Azure Web PubSub インスタンスを作成する方法

Azure Web PubSub サービスでアプリケーションを構築するには、Web PubSub インスタンスを作成し、クライアントとサーバーを接続する必要があります。 このハウツーガイドでは、Azure Web PubSub インスタンスを作成するためのオプションについて説明します。

## <a name="create-azure-web-pubsub-instance-with-azure-portal"></a>Azure portal を使用して Azure Web PubSub インスタンスを作成する 

[Azure portal](../azure-portal/index.yml) は、コマンドライン ツールに代えて使用できる、Web ベースの統合コンソールです。 Azure Portal を使用して、ご自分の Azure サブスクリプションを管理できます。 簡単な Web アプリから複雑なクラウド デプロイまで、すべてを構築、管理、監視することができます。 Azure portal で Azure Web PubSub サービス インスタンスを作成することもできます。

1. Azure portal の左上にある [新規] ボタンを選択します。 [新規] 画面で、検索ボックスに「*Web PubSub*」と入力して Enter キーを押します。 (Azure Web PubSub を `Web` カテゴリから検索することもできます)。

:::image type="content" source="media/create-instance-portal/search-web-pubsub-in-portal.png" alt-text="Azure Web PubSub をポータルで検索しているスクリーンショット。":::

2. 検索結果の **[Web PubSub]** を選択し、 **[作成]** を選択します。

3. 次の設定を入力します。

    | 設定      | 説明                                        |
    | ------------ | -------------------------------------------------- |
    | **リソース名** | 新しい Web PubSub サービス インスタンスを識別するグローバルで一意の名前。 有効な文字は、`a-z`、`0-9`、および `-` です。  | 
    | **サブスクリプション** | この新しい Web PubSub インスタンスが作成される Azure サブスクリプション。 | 
    | **[リソース グループ](../azure-resource-manager/management/overview.md)** |  Web PubSub サービス インスタンスを作成する新規または既存のリソース グループの名前。 | 
    | **場所** | 近くの[リージョン](https://azure.microsoft.com/regions/)を選択します。 |
    | **[価格レベル]** | Azure Web PubSub サービスの価格レベルの詳細については[こちら](https://azure.microsoft.com/pricing/details/web-pubsub/)をご覧ください。 |
    | **[ユニット数]** |  ユニット数は、Web PubSub サービス インスタンスで受け入れることができる接続の数を指定します。 各ユニットで最大 1,000 のコンカレント接続がサポートされます。 Standard レベルでのみ構成可能です。 |

:::image type="content" source="media/howto-develop-create-instance/create-web-pubsub-instance-in-portal.png" alt-text="ポータルで Azure Web PubSub インスタンスを作成しているスクリーンショット。":::

4. **[作成]** を選択して Web PubSub サービス インスタンスのデプロイを開始します。

## <a name="create-azure-web-pubsub-instance-with-azure-cli"></a>Azure CLI を使用して Azure Web PubSub インスタンスを作成する

[Azure コマンド ライン インターフェイス (Azure CLI)](/cli/azure) は、Azure リソースを作成および管理するためのコマンド セットです。 Azure CLI は Azure サービス全体で利用でき、オートメーションに重点を置くことで、Azure で迅速に作業できるように設計されています。 GA 後は、Azure CLI で Azure Web PubSub サービス インスタンスを作成することもできます。