---
title: リンター ルール - 未使用の変数がない
description: リンター ルール - 未使用の変数がない
ms.topic: conceptual
ms.date: 10/14/2021
ms.openlocfilehash: 3b59b8d72d12c365952dd8eef2c65a0f851ea561
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/19/2021
ms.locfileid: "130161874"
---
# <a name="linter-rule---no-unused-variables"></a>リンター ルール - 未使用の変数がない

このルールにより、Bicep ファイル内で参照されていない変数が検索されます。

## <a name="returned-code"></a>返されるコード

`no-unused-vars`

## <a name="solution"></a>解決策

テンプレートの混乱を軽減するには、定義されていても使用されていない変数を削除します。 このテストでは、テンプレートのどこでも使用されていない変数が検索されます。

## <a name="next-steps"></a>次のステップ

リンターの詳細については、「[Bicep リンターの使用方法](./linter.md)」を参照してください。
