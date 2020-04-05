---
title: Azure Monitor ブックのアクセス制御
description: ロールベースのアクセス制御を備えた作成済みのブックやパラメーター化されたカスタム ブックを使用して、複雑なレポート作成を簡素化します
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 20116ab105e4eb12875ba3cb279fb261eb5c70e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658423"
---
# <a name="access-control"></a>アクセス制御

ブックのアクセス制御は、次の 2 つのことを意味します。

* ブック内のデータを読み取るために必要なアクセス。 このアクセスは、ブックで使用されるリソースの標準 [Azure ロール](https://docs.microsoft.com/azure/role-based-access-control/overview)によって制御されます。 ブックでは、これらのリソースへのアクセスを指定または構成しません。 ユーザーは、通常、これらのリソースに対する[監視閲覧者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader)ロールを使用して、これらのリソースへのアクセスを取得します。

* ブックを保存するために必要なアクセス

    - プライベートな `("My")` ブックを保存するには、追加の特権は必要ありません。 すべてのユーザーはプライベート ブックを保存でき、そのユーザーのみがそのブックを表示することができます。
    - 共有ブックを保存するには、ブックの保存先のリソース グループに対する書き込み権限が必要です。 通常、これらの特権は "[監視共同作成者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor)" ロールによって指定されますが、"*ブック共同作成者*" ロールを使用して設定することもできます。
    
## <a name="standard-roles-with-workbook-related-privileges"></a>ブック関連の特権を持つ標準ロール

[監視閲覧者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader)には、リソースからデータを読み取るために監視ツール (ブックを含む) によって使用される標準の /read 特権が含まれています。

[監視共同作成者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor)には、項目を保存するためにさまざまな監視ツールによって使用される一般的な `/write` 特権が含まれます (共有ブックを保存するための `workbooks/write` 特権を含みます)。
"ブック共同作成者" は、共有ブックを保存するために、オブジェクトに "workbooks/write" 特権を追加します。
自分だけが閲覧できるプライベート ブックをユーザーが保存するために特別な特権は必要ありません。

カスタムのロールベースのアクセス制御の場合:

共有ブックを保存するには、`microsoft.insights/workbooks/write` を追加します。 詳細については、[ブック共同作成者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor)ロールに関するページを参照してください。

## <a name="next-steps"></a>次のステップ

* ブックの豊富な視覚化オプションの学習を[開始](workbooks-visualizations.md)します。
