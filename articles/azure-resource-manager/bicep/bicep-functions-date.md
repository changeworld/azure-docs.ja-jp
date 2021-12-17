---
title: Bicep 関数 - date
description: date で作業する際に Bicep ファイルで使用する関数について説明します。
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 09/30/2021
ms.openlocfilehash: 0eb995db6584dd056fa0b1563aec8c117fddeb66
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2021
ms.locfileid: "129357268"
---
# <a name="date-functions-for-bicep"></a>Bicep の Date 関数

この記事では、日付を操作するための Bicep 関数について説明します。

## <a name="datetimeadd"></a>dateTimeAdd

`dateTimeAdd(base, duration, [format])`

ベースの値に期間を加算します。 ISO 8601 形式である必要があります。

名前空間: [sys](bicep-functions.md#namespaces-for-functions)。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| base | はい | string | 加算する期間の開始日時の値。 [ISO 8601 タイムスタンプの形式](https://en.wikipedia.org/wiki/ISO_8601)を使用します。 |
| duration | はい | string | ベースに加算する時間の値。 負の値を指定することができます。 [ISO 8601 期間の形式](https://en.wikipedia.org/wiki/ISO_8601#Durations)を使用します。 |
| format | いいえ | string | 日時の結果の出力形式。 指定しない場合、ベース値の形式が使用されます。 [標準書式指定文字列](/dotnet/standard/base-types/standard-date-and-time-format-strings)または[カスタム書式指定文字列](/dotnet/standard/base-types/custom-date-and-time-format-strings)を使用します。 |

### <a name="return-value"></a>戻り値

ベースに期間の値を加算した結果の datetime 値。

### <a name="examples"></a>例

次の例は、時間の値を加算するさまざまな方法を示します。

```bicep
param baseTime string = utcNow('u')

var add3Years = dateTimeAdd(baseTime, 'P3Y')
var subtract9Days = dateTimeAdd(baseTime, '-P9D')
var add1Hour = dateTimeAdd(baseTime, 'PT1H')

output add3YearsOutput string = add3Years
output subtract9DaysOutput string = subtract9Days
output add1HourOutput string = add1Hour
```

以前の例がベースの日時 `2020-04-07 14:53:14Z`でデプロイされている場合、出力は次のようになります。

| Name | Type | 値 |
| ---- | ---- | ----- |
| add3YearsOutput | String | 4/7/2023 2:53:14 PM |
| subtract9DaysOutput | String | 3/29/2020 2:53:14 PM |
| add1HourOutput | String | 4/7/2020 3:53:14 PM |

次の例は、Automation スケジュールの開始日時を設定する方法を示します。

```bicep
param omsAutomationAccountName string = 'demoAutomation'
param scheduleName string = 'demSchedule1'
param baseTime string = utcNow('u')

var startTime = dateTimeAdd(baseTime, 'PT1H')

...

resource scheduler 'Microsoft.Automation/automationAccounts/schedules@2015-10-31' = {
  name: concat(omsAutomationAccountName, '/', scheduleName)
  properties: {
    description: 'Demo Scheduler'
    startTime: startTime
    interval: 1
    frequency: 'Hour'
  }
}
```

## <a name="utcnow"></a>utcNow

`utcNow(format)`

指定された形式で現在 (UTC) の datetime 値を返します。 形式を指定しないと、ISO 8601 (`yyyyMMddTHHmmssZ`) 形式が使われます。 **この関数は、パラメーターの既定値でのみ使用できます。**

名前空間: [sys](bicep-functions.md#namespaces-for-functions)。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| format |いいえ |string |文字列に変換する URI エンコードされた値。 [標準書式指定文字列](/dotnet/standard/base-types/standard-date-and-time-format-strings)または[カスタム書式指定文字列](/dotnet/standard/base-types/custom-date-and-time-format-strings)を使用します。 |

### <a name="remarks"></a>解説

この関数は、パラメーターの既定値に対する式の中でのみ使用できます。 この関数を Bicep ファイルのその他の場所で使用すると、エラーが返されます。 Bicep ファイルの他の場所でこの関数を使用することは、呼び出しのたびに異なる値が返されるため、許可されていません。 同じパラメーターで同じ　Bicep ファイルをデプロイしても、同じ結果が生成される保証はありません。

[エラー発生時に以前の正常なデプロイにロールバックするオプション](../templates/rollback-on-error.md)を使用し、以前のデプロイに utcNow を使用するパラメーターが含まれている場合、パラメーターは再評価されません。 代わりに、以前のデプロイのパラメーター値が、ロールバック デプロイで自動的に再利用されます。

既定値に対する utcNow 関数に依存する Bicep ファイルを再デプロイするときは注意が必要です。 再デプロイを行うときに、パラメーターの値を指定しないと、関数が再評価されます。 新しいリソースを作成するのではなく、既存のリソースを更新する場合は、以前のデプロイのパラメーター値を渡します。

### <a name="return-value"></a>戻り値

現在の UTC の datetime 値。

### <a name="examples"></a>例

次の例では、datetime 値のさまざまな形式を示します。

```bicep
param utcValue string = utcNow()
param utcShortValue string = utcNow('d')
param utcCustomValue string = utcNow('M d')

output utcOutput string = utcValue
output utcShortOutput string = utcShortValue
output utcCustomOutput string = utcCustomValue
```

前の例からの出力はデプロイごとに変わりますが、次のようになります。

| 名前 | Type | 値 |
| ---- | ---- | ----- |
| utcOutput | string | 20190305T175318Z |
| utcShortOutput | string | 03/05/2019 |
| utcCustomOutput | string | 3 5 |

次の例では、タグ値を設定するときに関数からの値を使用する方法を示します。

```bicep
param utcShort string = utcNow('d')
param rgName string

resource myRg 'Microsoft.Resources/resourceGroups@2020-10-01' = {
  name: rgName
  location: 'westeurope'
  tags: {
    createdDate: utcShort
  }
}

output utcShortOutput string = utcShort
```

## <a name="next-steps"></a>次のステップ

* Bicep ファイルのセクションの説明は、<bpt id="p1">[</bpt>Bicep ファイルの構造と構文<ept id="p1">](./file.md)</ept>に関する記事をご覧ください。
