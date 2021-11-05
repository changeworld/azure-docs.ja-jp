---
title: リンター ルール - 未使用のパラメーターがない
description: リンター ルール - 未使用のパラメーターがない
ms.topic: conceptual
ms.date: 10/14/2021
ms.openlocfilehash: 2e9c602a28b2ba19d8c0a38b1bba3ecc521e6d77
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131017846"
---
# <a name="linter-rule---no-unused-parameters"></a>リンター ルール - 未使用のパラメーターがない

このルールは、Bicep ファイル内のどこにも参照されていないパラメーターを検索します。

## <a name="returned-code"></a>返されるコード

`no-unused-params`

## <a name="solution"></a>解決策

テンプレートの混乱を軽減するには、定義されていても使用されていないパラメーターを削除します。 このテストでは、テンプレートのどこでも使用されていないパラメーターが検索されます。 使用されていないパラメーターを削除すると、不要な値を指定する必要がないため、テンプレートのデプロイも容易になります。

## <a name="next-steps"></a>次のステップ

リンターの詳細については、「[Bicep リンターの使用方法](./linter.md)」を参照してください。
