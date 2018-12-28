---
title: 既存の割り当てを更新する方法
description: Azure Blueprint で既存の割り当てを更新するためのメカニズムについて学習します。
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/25/2018
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 2ada0ccc1286677300c005da4fa0e92be94a3ca8
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/12/2018
ms.locfileid: "53314831"
---
# <a name="how-to-update-an-existing-blueprint-assignment"></a>既存のブループリントの割り当てを更新する方法

ブループリントが割り当てられたら、割り当てを更新できます。 既存の割り当てを更新する理由は次のとおりです。

- [リソース ロック](../concepts/resource-locking.md)を追加または削除する
- [動的パラメーター](../concepts/parameters.md#dynamic-parameters)の値を変更する
- 割り当てを、ブループリントの新しい**公開済み**バージョンにアップグレードする

## <a name="updating-assignments"></a>割り当ての更新

1. **[すべてのサービス]** をクリックし、左側のウィンドウで **[ポリシー]** を検索して選択します。 **[ポリシー]** ページで **[ブループリント]** をクリックします。

1. 左側のページから **[割り当てられたブループリント]** を選択します。

1. ブループリントの一覧で、ブループリントの割り当てをクリックします。 **[割り当ての更新]** ボタンをクリックするか、またはブループリントの割り当てを右クリックし、**[割り当ての更新]** を選択します。

   ![割り当てを更新する](../media/update-existing-assignments/update-assignment.png)

1. 元の割り当てからすべての値が事前入力された状態で、**[ブループリントの割り当て]** ページが読み込まれます。 **ブループリント定義のバージョン**、**ロック割り当て**状態、およびブループリント定義に存在する任意の動的パラメーターを変更できます。 変更が完了したら、**[割り当て]** をクリックします。

1. 更新された割り当ての詳細ページで、新しいステータスを確認します。 この例では、割り当てに**ロック**を追加しました。

   ![更新された割り当て - ロック](../media/update-existing-assignments/updated-assignment.png)

1. ドロップダウン リストを使用して、他の**割り当て操作**に関する詳細を確認します。 選択した割り当て操作によって、**管理対象リソース**のテーブルが更新されます。

   ![割り当て操作](../media/update-existing-assignments/assignment-operations.png)

## <a name="rules-for-updating-assignments"></a>割り当ての更新の規則

更新された割り当てのデプロイでは、いくつかの重要な規則に従います。 これらの規則は、既にデプロイされているリソースに対する動作を決定します。 要求された変更と、リソース デプロイまたは更新されているアーティファクト リソースの種類によって、実行されるアクションが決まります。

- ロールの割り当て
  - ロールまたはロールの割り当て先 (ユーザー、グループ、またはアプリ) が変更された場合は、新しいロールの割り当てが作成されます。 以前にデプロイされたロールの割り当ては、そのまま残ります。
- ポリシーの割り当て
  - ポリシーの割り当てのパラメーターが変更された場合は、既存の割り当てが更新されます。
  - ポリシーの割り当ての定義が変更されると、新しいポリシーの割り当てが作成されます。 以前にデプロイされたポリシーの割り当ては、そのまま残ります。
  - ポリシーの割り当てのアーティファクトがブループリントから削除された場合、デプロイ済みのポリシーの割り当てはそのまま残ります。
- Azure Resource Manager のテンプレート
  - テンプレートは Resource Manager によって **PUT** として処理されます。 このアクションはリソースの種類ごとに異なるので、ブループリントによって実行された際のこのアクションの影響を判断するには、含まれる各リソースのドキュメントを参照してください。

## <a name="possible-errors-on-updating-assignments"></a>割り当ての更新時に考えられるエラー

割り当てを更新する際に、実行時に障害を発生させるような変更を行ことがあります。 例としては、既にデプロイされているリソース グループの場所を変更する場合が挙げられます。 [Azure Resource Manager](../../../azure-resource-manager/resource-group-overview.md) によってサポートされている任意の変更を行うことができますが、変更によって Azure Resource Manager でエラーが発生する場合は、割り当てにおいても障害が発生します。

割り当てを更新できる回数に制限はありません。 エラーが発生した場合は、エラーを特定して、割り当てに対して別の更新を行います。  エラー シナリオの例を次に示します。

- パラメーター不良
- オブジェクトが既に存在している
- 変更が Azure Resource Manager でサポートされていない

## <a name="next-steps"></a>次の手順

- [ブループリントのライフサイクル](../concepts/lifecycle.md)を参照する
- [静的および動的パラメーター](../concepts/parameters.md)の使用方法を理解する
- [ブループリントの優先順位](../concepts/sequencing-order.md)のカスタマイズを参照する
- [ブループリントによるリソース ロック](../concepts/resource-locking.md)の使い方
- ブループリントの割り当て中に発生した問題を[一般的なトラブルシューティング](../troubleshoot/general.md)で解決する
