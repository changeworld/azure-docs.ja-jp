---
title: テンプレート関数 - 日付
description: Azure Resource Manager テンプレート (ARM テンプレート) で日付の操作に使用する関数について説明します。
ms.topic: conceptual
ms.date: 09/09/2021
ms.openlocfilehash: ed7c9d44458acb6733618ff84cf98b9bed2e2a36
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124793419"
---
# <a name="date-functions-for-arm-templates"></a>ARM テンプレート用の日付関数

Resource Manager では、Azure Resource Manager テンプレート (ARM テンプレート) で日付を操作するために、次の関数が提供されています。

* [dateTimeAdd](#datetimeadd)
* [utcNow](#utcnow)

## <a name="datetimeadd"></a>dateTimeAdd

`dateTimeAdd(base, duration, [format])`

ベースの値に期間を加算します。 ISO 8601 形式である必要があります。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | 種類 | 説明 |
|:--- |:--- |:--- |:--- |
| base | はい | string | 加算する期間の開始日時の値。 [ISO 8601 タイムスタンプの形式](https://en.wikipedia.org/wiki/ISO_8601)を使用します。 |
| duration | はい | string | ベースに加算する時間の値。 負の値を指定することができます。 [ISO 8601 期間の形式](https://en.wikipedia.org/wiki/ISO_8601#Durations)を使用します。 |
| format | いいえ | string | 日時の結果の出力形式。 指定しない場合、ベース値の形式が使用されます。 [標準書式指定文字列](/dotnet/standard/base-types/standard-date-and-time-format-strings)または[カスタム書式指定文字列](/dotnet/standard/base-types/custom-date-and-time-format-strings)を使用します。 |

### <a name="return-value"></a>戻り値

ベースに期間の値を加算した結果の datetime 値。

### <a name="examples"></a>例

次のテンプレート例は、時間の値を加算するさまざまな方法を示します。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/date/datetimeadd.json":::

以前のテンプレートがベースの日時 `2020-04-07 14:53:14Z`でデプロイされている場合、出力は次のようになります。

| 名前 | 種類 | 値 |
| ---- | ---- | ----- |
| add3YearsOutput | String | 4/7/2023 2:53:14 PM |
| subtract9DaysOutput | String | 3/29/2020 2:53:14 PM |
| add1HourOutput | String | 4/7/2020 3:53:14 PM |

次のテンプレート例は、Automation スケジュールの開始日時を設定する方法を示します。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/date/datetimeadd-automation.json":::

## <a name="utcnow"></a>utcNow

`utcNow(format)`

指定された形式で現在 (UTC) の datetime 値を返します。 形式を指定しないと、ISO 8601 (`yyyyMMddTHHmmssZ`) 形式が使われます。 **この関数は、パラメーターの既定値でのみ使用できます。**

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | 種類 | 説明 |
|:--- |:--- |:--- |:--- |
| format |いいえ |string |文字列に変換する URI エンコードされた値。 [標準書式指定文字列](/dotnet/standard/base-types/standard-date-and-time-format-strings)または[カスタム書式指定文字列](/dotnet/standard/base-types/custom-date-and-time-format-strings)を使用します。 |

### <a name="remarks"></a>解説

この関数は、パラメーターの既定値に対する式の中でのみ使用できます。 この関数をテンプレートのその他の場所で使用すると、エラーが返されます。 テンプレートの他の場所でこの関数を使用することは、呼び出しのたびに異なる値が返されるため、許可されていません。 同じパラメーターで同じテンプレートをデプロイしても、同じ結果が生成される保証はありません。

[エラー発生時に以前の正常なデプロイにロールバックするオプション](rollback-on-error.md)を使用し、以前のデプロイに utcNow を使用するパラメーターが含まれている場合、パラメーターは再評価されません。 代わりに、以前のデプロイのパラメーター値が、ロールバック デプロイで自動的に再利用されます。

既定値に対する utcNow 関数に依存するテンプレートを再デプロイするときは注意が必要です。 再デプロイを行うときに、パラメーターの値を指定しないと、関数が再評価されます。 新しいリソースを作成するのではなく、既存のリソースを更新する場合は、以前のデプロイのパラメーター値を渡します。

### <a name="return-value"></a>戻り値

現在の UTC の datetime 値。

### <a name="examples"></a>例

次の例のテンプレートでは、datetime 値のさまざまな形式を示します。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/date/utcnow.json":::

前の例からの出力はデプロイごとに変わりますが、次のようになります。

| 名前 | 種類 | 値 |
| ---- | ---- | ----- |
| utcOutput | string | 20190305T175318Z |
| utcShortOutput | string | 03/05/2019 |
| utcCustomOutput | string | 3 5 |

次の例では、タグ値を設定するときに関数からの値を使用する方法を示します。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/date/utcnow-tag.json":::

## <a name="next-steps"></a>次のステップ

* ARM テンプレートのセクションの説明については、「[ARM テンプレートの構造と構文について](./syntax.md)」を参照してください。
