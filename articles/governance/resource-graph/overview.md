---
title: Azure Resource Graph の概要
description: Azure Resource Graph サービスによってリソースの複雑なクエリの大規模な実行がどのように実現されるかについて理解します。
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/30/2019
ms.topic: overview
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: d76a5b32403bd14f18181580f891925130808922
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2019
ms.locfileid: "60002886"
---
# <a name="overview-of-the-azure-resource-graph-service"></a>Azure Resource Graph サービスの概要

Azure Resource Graph とは Azure 内のサービスで、環境を効果的に管理できるよう、全てのサブスクリプションおよび管理グループに渡る大規模なクエリに対する能力をもつ、高効率および高性能のリソース探索を提供することにより、 Azure Resource Management を拡張するよう設計されています。 これらのクエリでは、次の機能を提供します。

- 複雑なフィルター処理、グループ化、およびリソースのプロパティでの並べ替えによりリソースのクエリを行う機能。
- ガバナンスの要件に基づいてリソースを繰り返し探索し、ポリシー定義に、結果の式を変換する機能。
- 広大なクラウド環境にポリシーを適用することの影響を評価する機能。
- [リソースのプロパティに加えられた変更について詳しく説明する](./how-to/get-resource-changes.md)機能 (プレビュー)。

このドキュメントでは、それぞれの機能について詳しく見ていきます。

> [!NOTE]
> Azure Resource Graph は、Azure portal の新しい "すべてのリソース" の参照エクスペリエンスと Azure Policy の[変更履歴](../policy/how-to/determine-non-compliance.md#change-history-preview)によって使用されます。
> "_差分表示_"。大規模な環境を管理するお客様をサポートするために設計されています。

## <a name="how-does-resource-graph-complement-azure-resource-manager"></a>Resource Graph が Azure Resource Manager をどのように補完するか

Azure Resource Manager は、現在、複数のリソース フィールド (具体的にはリソース名、ID、種類、リソース グループ、サブスクリプション、場所) を公開する制限付きリソース キャッシュにデータを送信します。 以前は、各種のリソース プロパティを操作する場合、個々のリソース プロバイダーを呼び出し、リソースごとにプロパティの詳細を要求する必要がありました。

Azure Resource Graph を使用することにより、各リソースプロバイダーへの個別の呼び出しを行う必要なく、リソースプロバイダーが返すこれらのプロパティにアクセスすることができます。 サポートされるリソースの種類については、[完全モード デプロイでのリソース](../../azure-resource-manager/complete-mode-deletion.md)に関する表で "**はい**" を探してください。

Azure Resource Graph では、次のことができます。

- 各リソース プロバイダーへの個別の呼び出しを行う必要なく、リソース プロバイダーから返されるプロパティにアクセスする。
- リソースに加えられた変更の過去 14 日間分の履歴を表示して、どのプロパティがいつ変更されたかを確認する。 (プレビュー)

## <a name="the-query-language"></a>クエリ言語

これで Azure Resource Graph についてはよく理解したので、クエリを作成する方法に進みましょう。

Azure Resource Graph のクエリ言語が Azure Data Explorer で使用される [Kusto クエリ言語](../../data-explorer/data-explorer-overview.md)に基づくことを理解することが重要です。

最初に、Azure Resource Graph と共に使用することができる操作および機能の詳細については、[Resource Graph のクエリ言語](./concepts/query-language.md)を参照してください。
リソースをブラウズするためには、[リソースを探索する](./concepts/explore-resources.md)を参照してください。

## <a name="permissions-in-azure-resource-graph"></a>Azure Resource Graph でのアクセス許可

Resource Graph を使用するためには、最低限、照会したいリソースに読み取りアクセスできる適切な権限が、[ロール ベースのアクセス制御](../../role-based-access-control/overview.md) (RBAC) を通じて付与される必要があります。 Azure のオブジェクトまたはオブジェクト グループに対する `read` 以上のアクセス許可がないと、結果は返されません。

> [!NOTE]
> Resource Graph では、プリンシパルがログイン中に利用できるサブスクリプションが使用されます。 アクティブなセッション中に追加された新しいサブスクリプションのリソースを表示するには、プリンシパルがコンテキストを更新する必要があります。 ログアウトしてから再度ログインすると、このアクションが自動的に実行されます。

## <a name="throttling"></a>Throttling

すべてのお客様に最適なエクスペリエンスと応答時間が提供されるよう、Resource Graph へのクエリはスロットルされされます。 大規模かつ頻繁なクエリに Resource Graph API を使用する場合は、Resource Graph のページからポータルの "フィードバック" を使用してください。 必ずビジネス ケースを明記し、チームが連絡できるように [Microsoft からフィードバックについてメールをお送りする場合があります] チェック ボックスをオンにしてください。

## <a name="running-your-first-query"></a>最初のクエリを送信する

Resource Graph は、Azure CLI、Azure PowerShell、および Azure SDK for .NET をサポートします。 どの言語も、クエリの構造は同じです。 [Azure CLI ](first-query-azurecli.md#add-the-resource-graph-extension)および[ Azure PowerShell ](first-query-powershell.md#add-the-resource-graph-module)において、Resource Graph を有効にする方法について説明します。

## <a name="next-steps"></a>次の手順

- [Azure CLI](first-query-azurecli.md) を使用して最初のクエリを実行します。
- [Azure PowerShell](first-query-powershell.md) を使用して最初のクエリを実行します。
- [初歩的なクエリ](./samples/starter.md)から開始します。
- [高度なクエリ](./samples/advanced.md)について理解を深めます。