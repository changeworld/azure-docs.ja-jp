---
title: Advanced SIEM Information Model (ASIM) パーサー | Microsoft Docs
description: この記事では、KQL 関数をクエリ時パーサーとして使用して、Advanced SIEM Information Model (ASIM) を実装する方法について説明します
services: sentinel
cloud: na
documentationcenter: na
author: oshezaf
manager: rkarlin
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/09/2021
ms.author: ofshezaf
ms.custom: ignite-fall-2021
ms.openlocfilehash: 38b276bda8242f1cfef39c58925552ba35625baa
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132712834"
---
# <a name="advanced-siem-information-model-asim-parsers-public-preview"></a>Advanced SIEM Information Model (ASIM) パーサー (パブリック プレビュー)

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft Sentinel では、解析と[正規化](normalization.md)はクエリ時に行われます。 パーサーは [KQL ユーザー定義関数](/azure/data-explorer/kusto/query/functions/user-defined-functions)として構築され、**CommonSecurityLog**、カスタム ログテーブル、Syslog などの既存のテーブルのデータを正規化されたスキーマに変換します。 パーサーをワークスペース関数として保存すると、すべての Microsoft Sentinel テーブルと同じように使用できます。

> [!TIP]
> また、Microsoft Sentinel の正規化パーサーと正規化されたコンテンツに関する詳しいウェビナーを[こちら](https://www.youtube.com/watch?v=zaqblyjQW6k)でご視聴いただけます。スライドは[こちら](https://1drv.ms/b/s!AnEPjr8tHcNmjGtoRPQ2XYe3wQDz?e=R3dWeM)でご確認いただけます。 詳細については、「[次のステップ](#next-steps)」を参照してください。
>

> [!IMPORTANT]
> 現在、ASIM はプレビュー段階です。 [Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)には、ベータ版、プレビュー版、またはまだ一般提供されていない Azure 機能に適用される追加の法律条項が含まれています。
>

## <a name="source-agnostic-and-source-specific-parsers"></a>ソースに依存しないパーサーとソース固有のパーサー

ASIM には、**ソースに依存しない** パーサーと **ソース固有の** パーサーという 2 つのレベルのパーサーが含まれます。

### <a name="source-agnostic-parsers"></a>ソースに依存しないパーサー

**ソースに依存しないパーサー** は、同じスキーマに正規化されたすべてのソースを結合し、正規化されたフィールドを使用してすべてのソースを照会するために使用できます。 ソースに依存しないパーサーの名前は `im<schema>` で、`<schema>` は提供する特定のスキーマを表しています。

たとえば、次のクエリはソースに依存しない DNS パーサーを使用して、`ResponseCodeName`、`SrcIpAddr`、`TimeGenerated` の正規化フィールドを使用して DNS イベントを照会します。

```kusto
imDns
  | where isnotempty(ResponseCodeName)
  | where ResponseCodeName =~ "NXDOMAIN"
  | summarize count() by SrcIpAddr, bin(TimeGenerated,15m)
```

ソースに依存しないパーサーは、`union` KQL 演算子を使用して、複数のソース固有の正規化されたパーサーを組み合わせることができます。 ソース固有の正規化されたパーサーの名前は `vim<schema><vendor><product>` です。 したがって、`imDns` パーサーは次のようになります。

```kusto
union isfuzzy=true
vimDnsEmpty,
vimDnsCiscoUmbrella,
vimDnsInfobloxNIOS,
vimDnsMicrosoftOMS
```

> [!NOTE]
> **[ログ]** ページで `im` から始まる ASIM ソースに依存しないパーサーを使用する場合、時間の範囲セレクターは `custom` に設定されます。 時間の範囲は自分で設定できます。 または、パーサー パラメーターを使用して時間の範囲を指定します。
>
> あるいは、パラメーターをサポートしていない `ASim` パーサーを使用します。また、時間の範囲ピッカーは既定では `custom` に設定されません。
>

### <a name="source-specific-parsers"></a>ソース固有のパーサー

**ソース固有** の正規化パーサーをソースに依存しないパーサーに追加すると、ソースに依存しないパーサーを使用する組み込みクエリにカスタム ソースを含めることができます。

ソース固有のパーサーを使用すると、カスタム データの分析、ブック、洞察などの組み込みコンテンツからすぐに値を取得できます。

ソース固有のパーサーを個別に使用することもできます。 たとえば、Infoblox 固有のブックでは、`vimDnsInfobloxNIOS` パーサーを使用します。

## <a name="optimizing-parsing-using-parameters"></a><a name="optimized-parsers"></a>パラメーターを使用した解析の最適化

パーサーを使用すると、主に解析後に結果をフィルター処理する必要が生じ、クエリのパフォーマンスに影響を与える可能性があります。 このため、多くのパーサーには省略可能なフィルター パラメーターが含まれています。これにより、解析前にフィルター処理を行い、クエリのパフォーマンスを向上させることができます。 クエリの最適化と事前フィルター処理と共に ASIM パーサーを用いると、正規化を使用しない場合と比較して、パフォーマンスが向上する場合が多いです。

パーサーを呼び出す際に、1 つ以上の名前付きパラメーターを追加して、フィルター パラメーターを使用します。 たとえば、次のクエリの開始により、存在しないドメインに対する DNS クエリだけが返されます。

```kusto
imDns(responsecodename='NXDOMAIN')
```

前の例は次のクエリに似ていますが、はるかに効率的です。

```kusto
imDns | where ResponseCodeName == 'NXDOMAIN'
```

各スキーマには、スキーマ ドキュメントに記載されているフィルター パラメーターの標準セットがあります。フィルター パラメーターは完全に省略可能であり、現在は DNS スキーマでのみ完全にサポートされています。 その他のスキーマでは、事前にフィルター処理を最適化せずに標準のフィルター処理パラメーターをサポートしています。

## <a name="writing-source-specific-parsers"></a>ソース固有のパーサーの記述

パーサーは、ワークスペース関数として保存された KQL クエリです。 保存すると、組み込みテーブルのように使用できます。 パーサー クエリには、以下のパーツが含まれています。

**Filter** > **Parse** > **Prepare fields**

### <a name="filtering"></a>フィルター処理

#### <a name="filtering-the-relevant-records"></a>関連レコードのフィルター処理

多くの場合、テーブルには複数の種類のイベントが含まれます。 次に例を示します。
* Syslog テーブルには、複数のソースからのデータが含まれています。
* カスタム テーブルには、複数のイベントの種類を提供し、さまざまなスキーマに適合する 1 つのソースの情報を含めることができます。

したがって、パーサーはまず、ターゲット スキーマに関連するレコードのみをフィルター処理する必要があります。

KQL でのフィルタリングは、`where` 演算子を使って行われます。 例えば、**Sysmon event 1** はプロセス作成をレポートしており、**ProcessEvent** スキーマに正規化されなければなりません。 **Sysmon event 1** イベントは `Event` テーブルの一部であり、以下のフィルターを使用する必要があります。

```kusto
Event | where Source == "Microsoft-Windows-Sysmon" and EventID == 1
```

#### <a name="filtering-based-on-parser-parameters"></a>パーサー パラメーターに基づくフィルター処理

[パラメーター化されたパーサー](#optimized-parsers)を使用する場合は、そのスキーマのリファレンス アーティクルに記載されているように、関連するスキーマのフィルター パラメーターをパーサーが受け入れていることを確認します。

関数アーティクルはスキーマごとに同じです。 たとえば、DNS クエリのパラメーター化されたパーサー シグネチャの場合は、次のようになります。

```kusto
let DNSQuery_MS=(
    starttime:datetime=datetime(null), 
    endtime:datetime=datetime(null), 
    srcipaddr:string='*', 
    domain_has_any:dynamic=dynamic([]),
    responsecodename:string='*', 
    dnsresponsename:string='*', 
    response_has_any_prefix:dynamic=dynamic([]),
    eventtype:string='lookup'
    )
```

パラメーター値に基づいてフィルターを追加します。 フィルター処理を行う場合は、次の条件を確認します。

- **物理フィールド を使用して解析する前にフィルター処理します**。 フィルター処理された結果が十分に正確ではない場合は、解析後にテストを繰り返して結果を微調整します。 詳細については[フィルター処理の最適化](#optimization)に関するページを参照してください。
 - **パラメーターが定義されておらず既定値 が残っている場合は、フィルター処理を行いません**。 次の例では、文字列パラメーターのフィルター処理を実装する方法を示します。既定値は通常は '\*' で、リスト パラメーターの場合は、既定値は通常は空のリストです。

``` kusto
srcipaddr=='*' or ClientIP==srcipaddr
array_length(domain_has_any) == 0 or Name has_any (domain_has_any)
```

> [!TIP]
> 同じ型の既存のパーサーは、パラメーター のフィルター処理の実装を始める場合に最適です。
>

#### <a name="filtering-optimization"></a><a name="optimization"></a>フィルター処理の最適化


パーサーのパフォーマンスを確保するために、次のフィルター選択に関する推奨事項を確認してください。

-   解析されたフィールドではなく、常に組み込みのフィルター処理を行います。 解析されたフィールドを使用してフィルター処理する方が簡単な場合もありますが、パフォーマンスに大きな影響を与えます。
-   最適なパフォーマンスを提供する演算子を使用します。 特に、`==`、`has` と、`startswith` を参照してください。 また、`contains` や `matches regex` などの演算子を使うと、パフォーマンスが劇的に向上します。

パフォーマンスのフィルター選択に関する推奨事項は、常に従うのが困難な場合もあります。 例えば、`has` を使うと `contains` よりも精度は低下します。 他のケースでは、`SyslogMessage` のような組み込みフィールドの一致は、`DvcAction` のような抽出されたフィールドの比較よりも精度が低くなります。 このような場合は、組み込みフィールドに対してパフォーマンス最適化演算子を使用して事前にフィルター処理し、解析後により正確な条件を使用してフィルターを繰り返すことを推奨します。

例として、次の [Infoblox DNS](https://aka.ms/AzSentinelInfobloxParser) パーサーのスニペットを参照してください。 パーサーは、まず SyslogMessage フィールド `has` に単語 `client` があるかどうかを確認します。 ただし、この用語は、メッセージ内の別の場所で使用される場合があります。 そのため、`Log_Type` フィールドを解析した後、パーサーは `client` という単語がフィールドの値に確実に存在することを再度確認します。

```kusto
Syslog | where ProcessName == "named" and SyslogMessage has "client"
…
      | extend Log_Type = tostring(Parser[1]),
      | where Log_Type == "client"
```

> [!NOTE]
> パーサーを使用しるクエリによって既に時間がフィルター処理されるので、パーサーでは時間でフィルター処理すべきではありません。
>

### <a name="parsing"></a>解析

クエリで関連するレコードを選択すると、そのレコードの解析が必要になる場合があります。 通常、イベント情報の多くを 1 つのテキスト フィールドに伝える場合は、解析が必要です。

解析を実行する KQL 演算子を、以下にパフォーマンスが最適化されている順に示します。 最初の方法では最適なパフォーマンスが得られますが、最後のパフォーマンスは最も最適化されていません。

|演算子  |説明  |
|---------|---------|
|[split](/azure/data-explorer/kusto/query/splitfunction)     |    区切り記号で区切られた値の文字列を解析します     |
|[parse_csv](/azure/data-explorer/kusto/query/parsecsvfunction)     |     CSV (コンマ区切り値) 行として書式設定された値の文字列を解析します。    |
|[parse](/azure/data-explorer/kusto/query/parseoperator)     |    パターンを使用して、任意の文字列から複数の値を解析します。これは、パフォーマンス向上のための単純なパターンでも、正規表現でもかまいません。     |
|[extract_all](/azure/data-explorer/kusto/query/extractallfunction)     | 正規表現を使用して、任意の文字列から単一の値を解析します。 `parse` が正規表現を使用している場合、`extract_all` と同様の性能を発揮します。        |
|[extract](/azure/data-explorer/kusto/query/extractfunction)     |    正規表現を使用して、任意の文字列から単一の値を抽出します。 <br><br>1 つの値が必要な場合、`extract` を使用すると、`parse` や `extract_all` よりもパフォーマンスが向上します。 しかし、同じソース文字列に対して `extract` の複数のアクティブ化を使用することは、1 つの `parse` または `extract_all` に比べて効率が悪いため、避ける必要があります。      |
|[parse_json](/azure/data-explorer/kusto/query/parsejsonfunction)  | JSON 形式で設定された文字列の値を解析します。 JSON の一部の値だけが必要な場合は、`parse`、`extract`、`extract_all` を使用するとパフォーマンスが向上します。        |
|[parse_xml](/azure/data-explorer/kusto/query/parse-xmlfunction)     |    XML 形式で設定された文字列の値を解析します。 XML の一部の値だけが必要な場合は、`parse`、`extract`、`extract_all` を使用するとパフォーマンスが向上します。     |
| | |

解析フェーズでは文字列の解析に加えて、以下のような元の値の処理が必要になる場合があります。

- **書式設定と型変換**。 抽出されたソース フィールドは、ターゲット スキーマ フィールドに合うように書式設定しなければならない場合があります。 たとえば、日付と時刻を表す文字列を datetime フィールドに変換することが必要な場合もあります。     このような場合には、`todatetime` や `tohex` などの機能が有効です。

- **値の検索**。 抽出されたソース フィールドの値は、ターゲット スキーマ フィールドに指定された値のセットにマッピングされる必要があります。 たとえば、いくつかのソースは DNS 応答コードを数値でレポートしますが、スキーマでは、より一般的なテキストの応答コードが要求されます。 関数 `iff` と `case` は、いくつかの値をマップする場合に役立ちます。

    たとえば、Microsoft DNS パーサーは、次のように、`iff` ステートメントを使用して、イベント ID と応答コードに基づいて `EventResult` フィールドを割り当てます。

    ```kusto
    extend EventResult = iff(EventId==257 and ResponseCode==0 ,'Success','Failure')
    ```

    複数の値の場合は、同じ DNS パーサーで示されているように、`datatable` と `lookup` を使用します。

    ```kusto
    let RCodeTable = datatable(ResponseCode:int,ResponseCodeName:string) [ 0, 'NOERROR', 1, 'FORMERR'....];
    ...
     | lookup RCodeTable on ResponseCode
     | extend EventResultDetails = case (
         isnotempty(ResponseCodeName), ResponseCodeName,
         ResponseCode between (3841 .. 4095), 'Reserved for Private Use',
         'Unassigned')
    ```

> [!NOTE]
> この変換では、`lookup` のみを使用することはできません。複数の値が `Reserved for Private Use` または `Unassigned` にマッピングされるため、クエリでは検索とケースの両方が使用されます。
> それでも、クエリですべての値に `case` を使用するよりもはるかに効率的です。
>

### <a name="prepare-fields-in-the-result-set"></a>結果セットのフィールドを準備する

パーサーでは、正規化されたフィールドが確実に使用されるようにするために、結果セット フィールドを準備する必要があります。 ガイドラインとして、正規化されていない元のフィールドは、混乱を招く場合など、説得力のある理由がない限り、結果セットから削除することはできません。

フィールドを準備するには、次の KQL 演算子を使用します。

|演算子  | 説明  | パーサーで使用する場合  |
|---------|---------|---------|
|**extend**     | 計算フィールドを作成し、レコードに追加します        |  `Extend` は、正規化されたフィールドが元のデータから解析または変換される場合に使用されます。 詳細については、上記「[解析](#parsing)」のセクションを参照してください。     |
|**project-rename**     | フィールドの名前を変更します        |     フィールドが実際のイベントに存在し、名前変更だけが必要な場合は、`project-rename` を使用します。 <br><br>名前が変更されたフィールドは組み込みフィールドと同様に動作し、フィールドに対する操作のパフォーマンスがはるかに向上します。   |
|**project-away**     |      フィールドを削除します。   |結果セットから削除したい特定のフィールドには `project-away` を使用します。         |
|**project**     |  以前に存在した、またはステートメントの一部として作成されたフィールドを選択します。 その他のフィールドはすべて削除します。       | パーサーでは、正規化されていない他のフィールドを削除してはいけないため、パーサーでの使用は推奨されません。 <br><br>解析時に使用される一時的な値など、特定のフィールドを削除する必要がある場合は、`project-away` を使用して結果から削除します。      |
| | | |

### <a name="handle-parsing-variants"></a>解析バリアントを処理する

多くの場合、イベント ストリーム内のイベントには、さまざまな解析ロジックを必要とするバリアントが含まれます。

異なる解析ロジックを必要とするイベントの別のバリアントを処理する、さまざまなサブパーサーからパーサーを構築するのが魅力的に感じる場合が多くあります。 これらのサブパーサーは、それ自体がクエリとなり、`union` 演算子で統合されます。 この方法は便利ですが、パーサーのパフォーマンスに大きな影響を与えるため、推奨 *しません*。

バリアントを処理する場合は、次のガイドラインに従います。

|シナリオ  |処理  |
|---------|---------|
|異なるバリアントは *異なる* イベント タイプを表し、一般的に異なるスキーマにマッピングされます     |  個別のパーサーを使用する       |
|異なるバリアントは、*同じ* イベント タイプを表していますが、構造は異なります。     |   解析前にイベントを区別するメソッドがある場合など、バリアントが既知の場合は、[Infoblox DNS パーサー](https://aka.ms/AzSentinelInfobloxParser)で示されているように、`case` 演算子を使用して、実行する正しい `extract_all` を選択し、フィールド マッピングを行います。      |
|`union` がやむを得ない場合     |  `union` を使用する場合は、必ず次のガイドラインを使用してください。<br><br>- 各サブクエリの組み込みフィールドを使用して事前にフィルター処理します。 <br>- フィルターが相互に排他的である必要があります。 <br>- あまり重要ではない情報を解析し、サブクエリの数を減らすことを検討してください。       |
| | |

## <a name="add-your-parser-to-the-schema-source-agnostic-parser"></a><a name="include"></a>スキーマのソースに依存しないパーサーに自分のパーサーを追加する

正規化を使用すると、独自のコンテンツと組み込みコンテンツをカスタム データと一緒に使用できます。

たとえば、DNS クエリ アクティビティ ログを受信するカスタム コネクタがある場合は、DNS クエリ アクティビティ ログで正規化された DNS コンテンツを確実に利用できます。

そのためには、ソースに依存しない関連するパーサーを修正して、作成したソース固有のパーサーを含めます。 たとえば、ソースに依存しない `imDns` パーサーを変更して、自分のパーサーを `union` ステートメントのパーサーの一覧に追加することで含めます。 パーサーでパラメーターをサポートしている場合は、次の vimDnsYyyXxx パーサーのように、パラメーター シグネチャと一緒に含めます。 そうでない場合は、以下の vimDnsWwwZzz パーサーのように、シグネチャなしで含めます。

```kusto
let DnsGeneric=(starttime:datetime=datetime(null), endtime:datetime=datetime(null)... ){
  union isfuzzy=true
    vimDnsEmpty, 
    vimDnsCiscoUmbrella (starttime, endtime, srcipaddr...),
    vimDnsInfobloxNIOS (starttime, endtime, srcipaddr...),
    vimDnsMicrosoftOMS (starttime, endtime, srcipaddr...),
    vimDnsYyyXxx (starttime, endtime, srcipaddr...),
    vimDnsWwwZzz
  };
  DnsGeneric( starttime, endtime, srcipaddr...)
```


## <a name="deploy-parsers"></a>パーサーをデプロイする

パーサーを手動でデプロイするには、Azure Monitor の [ログ] ページにコピーして、変更を保存します。 このメソッドはテストする際に役立ちます。 詳細については、「[関数を作成する](../azure-monitor/logs/functions.md)」を参照してください。

ただし、多数のパーサーをデプロイするには、ARM テンプレートを使用することをお勧めします。 たとえば、ソースに依存しないパーサーといくつかのソース固有のパーサーを含む完全な正規化ソリューションをデプロイする場合や、ソースのスキーマごとに複数のパーサーをデプロイする場合は、ARM テンプレートを使用できます。

詳細は、「[ジェネリック パーサーの ARM テンプレート](https://github.com/Azure/Azure-Sentinel/tree/master/Tools/ARM-Templates/ParserQuery)」を参照してください。 このテンプレートを開始点として使用し、テンプレートのデプロイ プロセス中に関連するポイントにパーサーを貼り付け、パーサーをデプロイします。 たとえば、[DNS パーサーの ARM テンプレート](https://github.com/Azure/Azure-Sentinel/tree/master/Parsers/ASimDns/ARM)を参照してください。

> [!TIP]
> ARM テンプレートにはさまざまなリソースを含め、パーサーをコネクタ、分析ルール、またはウォッチリストと共にデプロイして、いくつかの便利なオプションを指定できます。 たとえば、パーサーは、そのパーサーと共にデプロイされるウォッチリストを参照できます。
>


## <a name="next-steps"></a><a name="next-steps"></a>次のステップ

この記事では、Advanced SIEM Information Model (ASIM) パーサーについて説明します。

詳細については、次を参照してください。

- Microsoft Sentinel の正規化パーサーと正規化されたコンテンツに関する詳しいウェビナーを[こちら](https://www.youtube.com/watch?v=zaqblyjQW6k)でご視聴いただけます。スライドは[こちら](https://1drv.ms/b/s!AnEPjr8tHcNmjGtoRPQ2XYe3wQDz?e=R3dWeM)でご確認いただけます。
- [Advanced SIEM Information Model の概要](normalization.md)
- [Advanced SIEM Information Model のスキーマ](normalization-about-schemas.md)
- [Advanced SIEM Information Model のコンテンツ](normalization-content.md)
