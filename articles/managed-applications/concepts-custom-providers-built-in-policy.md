---
title: ポリシーを使用して Azure カスタム プロバイダーの関連付けをデプロイする
description: Azure Policy サービスを使用して、カスタム プロバイダーの関連付けをデプロイする方法について学びます。
author: msHich
ms.service: managed-applications
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: hich
ms.openlocfilehash: 94f7128a473266824242f52a9c50f09b2c24f1d4
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74932236"
---
# <a name="deploy-associations-for-a-custom-provider-using-azure-policy"></a>Azure ポリシーを使用してカスタム プロバイダーの関連付けをデプロイする

リソースとカスタム プロバイダーの関連付けをデプロイするために、Azure ポリシーを使用できます。 この記事では、関連付けをデプロイする組み込みのポリシーと、そのポリシーを使用する方法について説明します。

## <a name="built-in-policy-to-deploy-associations"></a>関連付けをデプロイする組み込みポリシー

カスタム プロバイダーの関連付けのデプロイは、リソースをカスタム プロバイダーに関連付けるための関連付けのデプロイに使用できる、組み込みのポリシーです。 このポリシーは、次の 3 つのパラメーターを受け取ります。

- カスタム プロバイダー ID - この ID は、リソースの関連付けが必要なカスタム プロバイダーのリソース ID です。
- 関連付けるリソースの種類 - これらのリソースの種類は、カスタム プロバイダーに関連付けられるリソースの種類の一覧です。 同じポリシーを使用して、複数のリソースの種類をカスタム プロバイダーに関連付けることができます。
- 関連付けの名前のプレフィックス - この文字列は、作成される関連付けリソースの名前に追加されるプレフィックスです。 既定値は "DeployedByPolicy" です。

このポリシーでは、DeployIfNotExists 評価を使用します。 この評価は、リソースプロバイダーでリソースの作成または更新要求が処理され、成功を示す状態コードが返された後で実行されます。 その後、テンプレート デプロイを使用して関連付けリソースがデプロイされます。
関連付けの詳細については、[Azure カスタム プロバイダー リソースのオンボード](./concepts-custom-providers-resourceonboarding.md)に関するページを参照してください

## <a name="how-to-use-the-deploy-associations-built-in-policy"></a>関連付けのデプロイの組み込みポリシーを使用する方法 

### <a name="prerequisites"></a>前提条件
カスタム プロバイダーが、操作を実行するためにポリシーのスコープへのアクセス許可を必要とする場合、関連付けリソースのポリシーのデプロイは、アクセス許可を付与しなければ機能しません。

### <a name="policy-assignment"></a>ポリシー割り当て
組み込みポリシーを使用するには、ポリシー割り当てを作成し、カスタム プロバイダー ポリシーの関連付けのデプロイに割り当てます。 次に、ポリシーは準拠していないリソースを特定し、それらのリソースの関連付けをデプロイします。

![組み込みポリシーを割り当てる](media/builtin-policy/assign-builtin-policy-customprovider.png)

## <a name="getting-help"></a>ヘルプの表示

Azure カスタム リソース プロバイダーの開発に関する質問がある場合、[Stack Overflow](https://stackoverflow.com/questions/tagged/azure-custom-providers) で確認してみてください。 同様の質問が既に回答されていることもあるため、投稿する前にまず確認してください。 ```azure-custom-providers``` タグを追加して早く応答を受け取りましょう。

## <a name="next-steps"></a>次の手順

この記事では、組み込みのポリシーを使用して関連付けをディプロイする方法について学びました。 詳細については、次の記事を参照してください。

- [概念: Azure カスタム プロバイダー リソースのオンボード](./concepts-custom-providers-resourceonboarding.md)
- [チュートリアル:カスタム プロバイダーを使用したリソースのオンボード](./tutorial-custom-providers-resource-onboarding.md)
- [チュートリアル:Azure でカスタム アクションとカスタム リソースを作成する](./tutorial-custom-providers-101.md)
- [クイック スタート:カスタム リソース プロバイダーの作成とカスタム リソースのデプロイ](./create-custom-provider.md)
- [方法: カスタム アクションを Azure REST API に追加する](./custom-providers-action-endpoint-how-to.md)
- [方法: カスタム リソースを Azure REST API に追加する](./custom-providers-resources-endpoint-how-to.md)
