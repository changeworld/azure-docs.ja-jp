---
title: データ収集ルールを使用して VM 分析情報のゲストの正常性での監視を構成する (プレビュー)
description: VM 分析情報のゲストの正常性における既定の監視を、Resource Manager テンプレートを使用して大規模に変更する方法について説明します。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/15/2020
ms.openlocfilehash: 889a04d68de45a6270ae0c38615d841a526ad86a
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2021
ms.locfileid: "106490694"
---
# <a name="configure-monitoring-in-vm-insights-guest-health-using-data-collection-rules-preview"></a>データ収集ルールを使用して VM 分析情報のゲストの正常性での監視を構成する (プレビュー)
[VM 分析情報のゲストの正常性](vminsights-health-overview.md)を使用すると、一定間隔でサンプリングされる一連のパフォーマンス測定値によって定義される、仮想マシンの正常性を表示できます。 この記事では、データ収集ルールを使用して、複数の仮想マシンにわたって既定の監視を変更する方法について説明します。


## <a name="monitors"></a>モニター
仮想マシンの正常性状態は、そのモニターそれぞれからの[正常性のロールアップ](vminsights-health-overview.md#health-rollup-policy)によって決まります。 次の表に示すように、VM 分析情報のゲストの正常性には、2 種類のモニターがあります。

| モニター | 説明 |
|:---|:---|
| ユニット モニター | リソースまたはアプリケーションの一部の側面を測定します。 パフォーマンス カウンターを調べて、リソースのパフォーマンスまたはその可用性を判断することなどが含まれます。 |
| 集計モニター | 複数のモニターをグループ化して、1 つの集計された正常性状態を示します。 集計モニターには、1 つ以上のユニット モニターとその他の集計モニターを含めることができます。 |

VM 分析情報のゲストの正常性とその構成で使用されるモニターのセットを直接変更することはできません。 [オーバーライド](#overrides)を作成すると、それを通して既定の構成の動作を変更できます。 オーバーライドは、データ収集ルールで定義されます。 必要な監視構成を実現するために、それぞれが複数のオーバーライドを含むデータ収集ルールを複数作成することができます。

## <a name="monitor-properties"></a>モニターのプロパティ
次の表では、各モニターに対して構成できるプロパティについて説明します。

| プロパティ | モニター | 説明 |
|:---|:---|:---|
| Enabled | Aggregate<br>ユニット | true の場合、状態モニターが計算され、仮想マシンの正常性の一因となります。 これで、アラートが有効になっているというアラートをトリガーできます。 |
| アラート | Aggregate<br>ユニット | true の場合、モニターが異常な状態に移行すると、そのモニターに対するアラートがトリガーされます。 false の場合、モニターの状態は、アラートをトリガーする可能性がある仮想マシンの正常性の一因になります。 |
| 警告 | ユニット | 警告状態の条件。 ない場合は、モニターが警告状態になることはありません。 |
| Critical | ユニット | クリティカル状態の条件。 ない場合は、モニターがクリティカル状態になることはありません。 |
| 評価の頻度 | ユニット | 正常性状態が評価される頻度。 |
| ルックバック | ユニット | ルックバック ウィンドウのサイズ (秒単位)。 詳細な説明については、「[monitorConfiguration 要素](#monitorconfiguration-element)」を参照してください。 |
| 評価の種類 | ユニット | サンプル セットのどの値を使用するかを定義します。 詳細な説明については、「[monitorConfiguration 要素](#monitorconfiguration-element)」を参照してください。 |
| 最小サンプル | ユニット | 値の計算に使用する値の最小数。 |
| 最大サンプル | ユニット | 値の計算に使用する値の最大数。 |


## <a name="default-configuration"></a>既定の構成
次の表に、各モニターの既定の構成を示します。 この既定の構成を直接変更することはできませんが、特定の仮想マシンのモニター構成を変更する[オーバーライド](#overrides)を定義することはできます。


| Monitor | Enabled | アラート | 警告 | Critical | 評価の頻度 | ルックバック | 評価の種類 | 最小サンプル | 最大サンプル数 |
|:---|:---|:---|:---|:---|:---|:---|:---|:---|:---|
| CPU 使用率  | True | False | なし | \> 90%    | 60 秒 | 240 秒 | Min | 2 | 3 |
| 使用可能なメモリ | True | False | なし | \< 100 MB | 60 秒 | 240 秒 | Max | 2 | 3 |
| ファイル システム      | True | False | なし | \< 100 MB | 60 秒 | 120 秒 | Max | 1 | 1 |


## <a name="overrides"></a>上書き
1 つの *オーバーライド* で、モニターのプロパティを 1 つ以上変更します。 たとえば、オーバーライドを使用すると、既定で有効になっているモニターを無効にすること、モニターの警告条件を定義すること、モニターのクリティカルのしきい値を変更することができます。 

オーバーライドは、[データ収集ルール (DCR)](../agents/data-collection-rule-overview.md) で定義されます。 異なるオーバーライドのセットを使用して複数の DCR を作成し、それらを複数の仮想マシンに適用できます。 「[Azure Monitor エージェント用のデータ収集の構成 (プレビュー)](../agents/data-collection-rule-azure-monitor-agent.md#data-collection-rule-associations)」で説明しているように、関連付けを作成して DCR を仮想マシンに適用します。


## <a name="multiple-overrides"></a>複数のオーバーライド

1 つのモニターに複数のオーバーライドを用意できます。 オーバーライドで異なるプロパティが定義されている場合、結果として得られる構成は、すべてのオーバーライドの組み合わせとなります。

たとえば、`memory|available` モニターでは、既定で、警告のしきい値を指定したり、アラートを有効にしたりしません。 このモニターに、以下のオーバーライドが適用されたとします。

- オーバーライド 1 では、`alertConfiguration.isEnabled` プロパティ値を `true` と定義しています
- オーバーライド 2 では、しきい値の条件が `< 250` である `monitorConfiguration.warningCondition` を定義しています。

結果として得られる構成では、使用できるメモリが 250 MB 未満のときに警告の正常性状態になり、重要度 2 のアラートが作成されます。また、使用できるメモリが 100 MB 未満のときにクリティカルの正常性状態になり、アラートの重要度 1 が作成されます (または、既に存在する場合は、既存のアラートを重要度 2 から 1 に変更します)。

2 つのオーバーライドで同じモニターに対して同じプロパティを定義している場合、1 つの値が優先されます。 オーバーライドは、その[スコープ](#scopes-element)に基づいて、最も一般的なものから最も限定的なものへと適用されます。 これは、最も限定的なオーバーライドが適用される可能性が最も高くなることを意味します。 具体的な順序は次のとおりです。

1. グローバル 
2. サブスクリプション
3. Resource group
4. 仮想マシン。 

同じスコープ レベルの複数のオーバーライドで、同じモニターに対して同じプロパティが定義されている場合、それらは DCR に現れる順序で適用されます。 オーバーライドが異なる DCR 内にある場合は、DCR リソース ID のアルファベット順に適用されます。


## <a name="data-collection-rule-configuration"></a>データ収集ルールの構成
以降のセクションでは、オーバーライドを定義するデータ収集ルールの JSON 要素について説明します。 完全な例は、「[サンプルデータ収集ルール](#sample-data-collection-rule)」に記載されています。

## <a name="extensions-structure"></a>extensions の構造
ゲストの正常性は Azure Monitor エージェントの拡張機能として実装されているため、オーバーライドは、データ収集ルールの `extensions` 要素で定義されます。 

```json
"extensions": [
    {
        "name": "Microsoft-VMInsights-Health",
        "streams": [
            "Microsoft-HealthStateChange"
        ],
        "extensionName": "HealthExtension",
        "extensionSettings": {   }
    }
]
```

| 要素 | 必須 | 説明 |
|:---|:---|:---|
| `name` | はい | その拡張機能のユーザー定義文字列。 |
| `streams` | Yes | ゲストの正常性データの送信先となるストリームの一覧。 これには **Microsoft-HealthStateChange** が含まれている必要があります。  |
| `extensionName` | はい | 拡張機能の名前。 これは **HealthExtension** である必要があります。 |
| `extensionSettings` | Yes | 既定の構成に適用される `healthRuleOverride` 要素の配列。 |


## <a name="extensionsettings-element"></a>extensionSettings 要素
拡張機能の設定が格納されます。

```json
"extensionSettings": {
    "schemaVersion": "1.0",
    "contentVersion": "",
    "healthRuleOverrides": [ ]
}
```

| 要素 | 必須 | 説明 |
|:---|:---|:---|
| `schemaVersion` | はい | 要素の予期されるスキーマを表すために Microsoft によって定義された文字列。 現在は 1.0 に設定されている必要があります |
| `contentVersion` | いいえ | 必要に応じて、異なるバージョンの正常性構成を追跡するためにユーザーが定義した文字列。 |
| `healthRuleOverrides` | Yes | 既定の構成に適用される `healthRuleOverride` 要素の配列。 |

## <a name="healthrulesoverrides-element"></a>healthRulesOverrides 要素
それぞれが 1 つのオーバーライドを定義する `healthRuleOverride` 要素が、1 つ以上含まれます。

```json
"healthRuleOverrides": [
    {
        "scopes": [ ],
        "monitors": [ ],
        "monitorConfiguration": { },
        "alertConfiguration": {  },
        "isEnabled": true|false
    }
]
```

| 要素 | 必須 | 説明 |
|:---|:---|:---|
| `scopes` | はい | このオーバーライドを適用できる仮想マシンを指定する 1 つ以上のスコープの一覧。 DCR が仮想マシンに関連付けられている場合でも、仮想マシンは、オーバーライドを適用するスコープ内に存在している必要があります。 |
| `monitors` | Yes | このオーバーライドをどのモニターが受け取るかを定義する 1 つ以上の文字列の一覧。  |
| `monitorConfiguration` | いいえ | 正常性状態とその計算方法を含む、モニターの構成。 |
| `alertConfiguration` | いいえ | モニターに対するアラートの構成。 |
| `isEnabled` | いいえ | モニターを有効にするかどうかを制御します。 無効にされたモニターは、再度有効にされない限り、特殊な *無効* の正常性状態および無効にされた状態に切り替わります。 省略した場合、モニターは階層内の親モニターからその状態を継承します。 |


## <a name="scopes-element"></a>scopes 要素
各オーバーライドには、どの仮想マシンにオーバーライドを適用する必要があるかを定義するスコープが 1 つ以上あります。 スコープにできるのは、サブスクリプション、リソース グループ、または 1 つ仮想マシンです。 オーバーライドが、特定の仮想マシンに関連付けられている DCR 内に存在する場合でも、仮想マシンがそのオーバーライドのいずれかのスコープ内にある場合にのみ、その仮想マシンにオーバーライドが適用されます。 これにより、少数の DCR を一連の VM に広範に関連付けることができますが、DCR 自体の中で各オーバーライドの割り当てをきめ細かく制御できます。 scopes 要素を使用して、一連の仮想マシンの異なるサブセットに対して正常性モニターのオーバーライドを指定するのと同時に、DCR のセットを少数作成し、それらを、ポリシーを使用してこれらの仮想マシンに関連付けることもできます。

次の表は、異なるスコープの例を示しています。

| Scope | 例 |
|:---|:---|
| 1 つの仮想マシン | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rg-name/providers/Microsoft.Compute/virutalMachines/my-vm` |
| リソース グループ内のすべての仮想マシン | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rg-name` |
| サブスクリプション内のすべての仮想マシン | `/subscriptions/00000000-0000-0000-0000-000000000000/` |
| データ収集ルールが関連付けられているすべての仮想マシン | `*` |


## <a name="monitors-element"></a>monitors 要素
正常性の階層の中で、どのモニターがこのオーバーライドを受け取るかを定義する、1 つ以上の文字列の一覧。 各要素には、このオーバーライドを受け取る 1 つ以上のモニターと一致するモニターの名前または種類名を指定できます。 

```json
"monitors": [
    "<monitor name>"
 ],
```



次の表に、現在使用できるモニターの名前を示します。

| 型名 | 名前 | 説明 |
|:----------|:-----|:------------|
| root | root | 仮想マシンの正常性を表す最上位レベルのモニター。 |
| cpu-utilization | cpu-utilization | CPU 使用率モニター。 |
| logical-disks | logical-disks | Windows 仮想マシン上にあるすべての監視対象ディスクの、正常性状態についての集計モニター。 |
| logical-disks\|\* | logical-disks\|C:<br>logical-disks\|D: | Windows 仮想マシン上の特定のディスクの正常性を追跡する集計モニター。 |
| logical-disks\|\*\|free-space | logical-disks\|C:\|free-space<br>logical-disks\|D:\|free-space | Windows 仮想マシン上のディスク空き領域モニター。 |
| filesystems | filesystems | Linux 仮想マシン上にあるすべてのファイルシステムの正常性の集計モニター。 |
| filesystems\|\* | filesystems\|/<br>filesystems\|/mnt | Linux 仮想マシン上のファイルシステムの正常性を追跡する集計モニター。 |
| filesystems\|\*\|free-space | filesystems\|/\|free-space<br>filesystems\|/mnt\|free-space | Linux 仮想マシン ファイルシステム上のディスク空き領域モニター。 |
| メモリ | メモリ | 仮想マシン メモリの正常性についての集計モニター。 |
| memory\|available | memory\|available | 仮想マシンで使用可能なメモリを追跡するモニター。 |


## <a name="alertconfiguration-element"></a>alertConfiguration 要素
モニターからアラートを作成する必要があるかどうかを指定します。

```json
"alertConfiguration": {
    "isEnabled": true|false
}
```

| 要素 | Mandatory | 説明 | 
|:---|:---|:---|
| `isEnabled` | いいえ | true に設定すると、モニターがクリティカルまたは警告の状態に切り替わるときにアラートが生成され、正常な状態に戻るとアラートが解消されます。 false の場合や省略された場合、アラートは生成されません。  |


## <a name="monitorconfiguration-element"></a>monitorConfiguration 要素
ユニット モニターにのみ適用されます。 正常性状態とその計算方法を含めて、モニターの構成を定義します。

パラメーターでは、しきい値と比較するメトリック値を計算するためのアルゴリズムを定義します。 モニターでは、基になるメトリックのデータのサンプル 1 つを操作するのではなく、評価時間から得た `lookbackSec` 前のウィンドウ内で受信した、複数のメトリック サンプルを評価します。 その期間内に受信したすべてのサンプルが考慮に入れられて、サンプルの数が `maxSamples` よりも大きい場合は、`maxSamples` より上の古いサンプルが無視されます。 

ルックバック間隔内にあるサンプルが `minSamples` よりも少ない場合、モニターは、基になるメトリックの正常性に関して情報に基づいて判断するのに十分なデータがないことを示す、*不明* の正常性状態に切り替わります。 `minSamples` より多い数のサンプルが使用できる場合は、evaluationType パラメーターで指定された集計関数がセットに対して実行され、1 つの値が計算されます。


```json
"monitorConfiguration": {
        "evaluationType" : "<type-of-evaluation>",
        "lookbackSecs": <lookback-number-of-seconds>,
        "evaluationFrequencySecs": <evaluation-frequency-number-of-seconds>,
        "minSamples": <minimum-samples>,
        "maxSamples": <maximum-samples>,
        "warningCondition": {  },
        "criticalCondition": {  }
    }
}
```

| 要素 | Mandatory | 説明 | 
|:---|:---|:---|
| `evaluationFrequencySecs` | いいえ | 正常性状態の評価の頻度を定義します。 各モニターは、エージェントが起動した時点で評価され、その後は、このパラメーターによって定義された定期的な間隔で評価されます。 |
| `lookbackSecs`   | いいえ | ルックバック ウィンドウのサイズ (秒単位)。 |
| `evaluationType` | いいえ | `min` –サンプル セット全体から最小値を取得します<br>`max` –サンプル セット全体から最大値を取得します<br>`avg` –サンプル セットの値の平均を取得します<br>`all` –セット内のすべての単一値をしきい値と比較します。 セット内のすべてのサンプルがしきい値の条件を満たす場合にのみ、モニターの状態が切り替わります。 |
| `minSamples`     | いいえ | 値の計算に使用する値の最小数。 |
| `maxSamples`     | いいえ | 値の計算に使用する値の最大数。 |
| `warningCondition`  | いいえ | 警告条件のしきい値と比較ロジック。 |
| `criticalCondition` | いいえ | クリティカル条件のしきい値と比較ロジック。 |


## <a name="warningcondition-element"></a>warningCondition 要素
警告条件のしきい値と比較ロジックを定義します。 この要素が含まれていない場合、モニターが警告の正常性状態に切り替わることはありません。

```json
"warningCondition": {
    "isEnabled": true|false,
    "operator": "<comparison-operator>",
    "threshold": <threshold-value>
},
```

| プロパティ | Mandatory | 説明 | 
|:---|:---|:---|
| `isEnabled` | いいえ | 条件を有効にするかどうかを指定します。 **false** に設定した場合、しきい値や演算子のプロパティが設定されていても、条件が無効にされます。 |
| `threshold` | いいえ | 評価された値を比較するためのしきい値を定義します。 |
| `operator`  | いいえ | しきい値の式で使用する比較演算子を定義します。 使用できる値は >、<、>=、<=、== です。 |


## <a name="criticalcondition-element"></a>criticalCondition 要素
クリティカル条件のしきい値と比較ロジックを定義します。 この要素が含まれていない場合、モニターがクリティカルの正常性状態に切り替わることはありません。

```json
"criticalCondition": {
    "isEnabled": true|false,
    "operator": "<comparison-operator>",
    "threshold": <threshold-value>
},
```

| プロパティ | Mandatory | 説明 | 
|:---|:---|:---|
| `isEnabled` | いいえ | 条件を有効にするかどうかを指定します。 **false** に設定した場合、しきい値や演算子のプロパティが設定されていても、条件が無効にされます。 |
| `threshold` | いいえ | 評価された値を比較するためのしきい値を定義します。 |
| `operator`  | いいえ | しきい値の式で使用する比較演算子を定義します。 使用できる値は >、<、>=、<=、== です。 |

## <a name="sample-data-collection-rule"></a>データ収集ルールのサンプル
ゲストの監視を有効にするデータ収集ルールのサンプルについては、「[Resource Manager テンプレートを使用して仮想マシンを有効にする](vminsights-health-enable.md#enable-a-virtual-machine-using-resource-manager-template)」を参照してください。


## <a name="next-steps"></a>次のステップ

- [データ収集ルール](../agents/data-collection-rule-overview.md)の詳細を確認します。
