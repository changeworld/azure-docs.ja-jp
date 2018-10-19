---
title: Azure Resource Graph の概要
description: Azure Resource Graph は、リソースの複雑なクエリを大規模に実行するための Azure サービスです。
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: overview
ms.service: resource-graph
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: d68183f4d0a928ac72f3f73ea5225ad174820cb7
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2018
ms.locfileid: "47162102"
---
# <a name="what-is-azure-resource-graph"></a>Azure Resource Graph とは

Azure Resource Graph とは Azure 内のサービスで、環境を効果的に管理できるよう、全てのサブスクリプションおよび管理グループに渡る大規模なクエリに対する能力をもつ、高効率および高性能のリソース探索を提供することにより、 Azure Resource Management を拡張するよう設計されています。 これらのクエリでは、次の機能を提供します。

- 複雑なフィルター処理、グループ化、およびリソースのプロパティでの並べ替えによりリソースのクエリを行う機能。
- ガバナンスの要件に基づいてリソースを繰り返し探索し、ポリシー定義に、結果の式を変換する機能。
- 広大なクラウド環境にポリシーを適用することの影響を評価する機能。

本文書では、各機能の詳細を熟考します。

> [!NOTE]
> Azure Resource Graph は、Azure portal の新しいブラウズ「すべてのリソース」エクスペリエンスによって使用されます。 大規模な環境を管理する必要があるお客様をサポートするために設計されています。

## <a name="how-does-resource-graph-complement-azure-resource-manager"></a>Resource Graph が Azure Resource Manager をどのように補完するか

Azure Resource Manager は、現在、複数のリソース フィールドに公開する制限付きリソース キャッシュに、具体的には – リソース名、ID、種類、リソース グループ、サブスクリプション、および場所にデータを送信します。 今日では、さらなるリソース プロパティを使用して作業を行いたい場合、個々のリソース プロバイダーに呼び出しを行い、各リソースのプロパティ詳細を要求する必要があります。

Azure Resource Graph を使用することにより、各リソースプロバイダーへの個別の呼び出しを行う必要なく、リソースプロバイダーが返すこれらのプロパティにアクセスすることができます。

## <a name="the-query-language"></a>クエリ言語

これで Azure Resource Graph についてはよく理解したので、クエリを作成する方法に進みましょう。

Azure Resource Graph のクエリ言語が [Azure Data Explorer のクエリ言語](../../data-explorer/data-explorer-overview.md)に基づくことを理解することが重要です。

最初に、Azure Resource Graph と共に使用することができる操作および機能の詳細については、[Resource Graph のクエリ言語](./concepts/query-language.md)を参照してください。 リソースをブラウズするためには、[リソースを探索する](./concepts/explore-resources.md)を参照してください。

## <a name="permissions-in-azure-resource-graph"></a>Azure Resource Graph でのアクセス許可

Resource Graph を使用するためには、[ロール ベースのアクセス制御](../../role-based-access-control/overview.md) (RBAC) を通じて、少なくともクエリを行いたいリソースへの読み取り許可の権限を付与される必要があります。 `read`管理グループ、サブスクリプション、リソースグループまたは個別のリソースについて許可を取得していない場合、Resource Graph クエリの結果に返されません。

## <a name="running-your-first-query"></a>最初のクエリを送信する

Resource Graph は、Azure CLI および Azure PowerShell の両方をサポートします。 どの言語を使用しているかに関わらず、クエリ コンポーネントは同様の構造です。 どちらの SDK においても、 Azure Resource Graph のサポートはまだデフォルトで利用可能ではありませんので、必要なコマンドを提供するために拡張機能またはモジュールを読み込む必要があります。
[Azure CLI ](first-query-azurecli.md#add-the-resource-graph-extension)および[ Azure PowerShell ](first-query-powershell.md#add-the-resource-graph-module)において、Resource Graph を有効にする方法について説明します。

## <a name="next-steps"></a>次の手順

- [Azure CLI ](first-query-azurecli.md)を使用して最初のクエリを実行します
- [Azure PowerShell ](first-query-powershell.md) を使用して最初のクエリを実行します
- [Starter クエリ](./samples/starter.md)を使用して開始します
- [高度なクエリ](./samples/advanced.md)について理解を深めます