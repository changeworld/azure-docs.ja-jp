---
title: リンター ルール - 未使用のパラメーターがない
description: リンター ルール - 未使用のパラメーターがない
ms.topic: conceptual
ms.date: 10/14/2021
ms.openlocfilehash: d02d13c44fd6cc6a174f629c8f3e32777bd643af
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/19/2021
ms.locfileid: "130161543"
---
# <a name="linter-rule---no-unused-parameters"></a>リンター ルール - 未使用のパラメーターがない

このルールでは、Bicep ファイル内のどこも参照していないパラメーターが検出されます。

## <a name="returned-code"></a>返されるコード

`no-unused-params`

## <a name="solution"></a>解決策

テンプレートの混乱を軽減するには、定義されていても使用されていないパラメーターを削除します。 このテストでは、テンプレートのどこでも使用されていないパラメーターが検索されます。 使用されていないパラメーターを削除すると、不要な値を指定する必要がないため、テンプレートのデプロイも容易になります。

## <a name="next-steps"></a>次のステップ

リンターの詳細については、「[Bicep リンターの使用方法](./linter.md)」を参照してください。
