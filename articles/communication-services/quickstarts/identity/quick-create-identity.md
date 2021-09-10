---
title: クイック スタート - テスト用の Azure Communication Services ID をすばやく作成する
titleSuffix: An Azure Communication Services quickstart
description: サンプルとトラブルシューティングに使用するため、 Azure portal で ID とアクセス トークン ツールを使用する方法について説明します。
author: manoskow
manager: chpalm
services: azure-communication-services
ms.author: manoskow
ms.date: 07/19/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: d61fb99787bc2e934ce2f1abc9bedff0c21bc64d
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/31/2021
ms.locfileid: "123251108"
---
# <a name="quickstart-quickly-create-azure-communication-services-access-tokens-for-testing"></a>クイック スタート - テスト用の Azure Communication Services アクセス トークン をすばやく作成する

[Azure portal ](https://portal.azure.com) のCommunication Services 拡張機能では、Communication Services の ID とアクセス トークン生成できます。 これにより、認証サービスの作成を省略し、サンプル アプリと簡単な開発シナリオを簡単にテストできるようになります。 この機能は小規模な検証とテストを目的とします。実稼働シナリオでは使用できません。 実稼働コードについては、[アクセス トークンの作成に関するクイックスタート](../access-tokens.md)を参照してください。

このツールは、単純なユーザー エクスペリエンスで ```Identity SDK``` の動作を示します。 このツールを使用して作成されたトークンと ID は、 ```Identity SDK``` を使用して作成された場合と同じ動作と規則に従います。  例えば、アクセス トークンの有効時間は 24 時間です。

## <a name="prerequisites"></a>前提条件

- [Azure Communication Services のリソース](../create-communication-resource.md)。

## <a name="create-the-access-tokens"></a>アクセス トークンの作成

[Azure portal](https://portal.azure.com) で、Communication Services リソース内で **ID とユーザーアクセス トークン** のブレードまで移動します。 

アクセス トークンのスコープを選択します。 なし、1 つ、または複数を選択できます。 **[生成]** をクリックします。

![ID とアクセス トークンのスコープを選択します。](../media/quick-create-identity-choose-scopes.png)

ID と、対応するユーザー アクセス トークンが生成されます。 これらの文字列をコピーし、[サンプル アプリ](../../samples/overview.md)やそのほかのテスト シナリオで使用できます。

![ID とアクセス トークンが生成され、有効期限が表示されます。](../media/quick-create-identity-generated.png)

## <a name="next-steps"></a>次のステップ


次のことも実行できます。

 - [認証について学習する](../../concepts/authentication.md)
 - [クライアントとサーバーのアーキテクチャについて学習する](../../concepts/client-and-server-architecture.md)
