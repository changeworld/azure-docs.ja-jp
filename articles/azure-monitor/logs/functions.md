---
title: Azure Monitor ログ クエリでの関数
description: この記事では、Azure Monitor で関数を使用して、あるクエリを別のログ クエリから呼び出す方法について説明します。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/19/2021
ms.openlocfilehash: fecede1d582232ebc75878a687a24818031f0d80
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/20/2021
ms.locfileid: "107752855"
---
# <a name="functions-in-azure-monitor-log-queries"></a>Azure Monitor ログ クエリでの関数
関数は、コマンドのようにして他のログ クエリで使用できる、Azure Monitor 内のログ クエリです。 関数を使用すると、開発者はさまざまな顧客にソリューションを提供することができ、お客様は独自の環境でクエリ ロジックを再利用できます。 この記事では、関数の使用方法と独自のものを作成する方法について詳しく説明します。

## <a name="types-of-functions"></a>関数の種類
Azure Monitor には、次の 2 種類の関数があります。

- **ソリューション関数:** Azure Monitor に含まれている事前組み込み関数。 これらは、すべての Log Analytics ワークスペースで使用でき、変更することはできません。
- **ワークスペース関数:** 特定の Log Analytics ワークスペースにインストールされ、ユーザーが変更および制御できる関数。

## <a name="viewing-functions"></a>関数を表示する
Log Analytics ワークスペースの左側のウィンドウにある **[Functions]\(関数\)** タブから、現在のワークスペースのソリューション関数とワークスペース関数を表示できます。 一覧に含まれる関数をフィルター処理するには **[フィルター]** ボタンを使用し、それらのグループ化を変更するには **[Group by]\(以下によるグループ化\)** を使用します。 特定の関数を見つけるには、 **[検索]** ボックスに文字列を入力します。 関数の上にカーソルを合わせると、説明やパラメーターなど、それに関する詳細が表示されます。

:::image type="content" source="media/functions/view-functions.png" alt-text="関数を表示する" lightbox="media/functions/view-functions.png":::

## <a name="use-a-function"></a>関数を使用する
クエリで関数を使用するには、コマンドで入力する場合と同様に、任意のパラメーターの値を使用してその名前を入力します。 関数の出力は、結果として返されるようにすることも、別のコマンドにパイプされるようにすることもできます。

現在のクエリに関数を追加するには、その名前をダブルクリックするか、その上にカーソルを合わせて **[Use in editor]\(エディターで使用\)** を選択します。 ワークスペース内の関数は、クエリに入力するときに IntelliSense にも含まれます。 

クエリにパラメーターが必要な場合は、構文 `function_name(param1,param2,...)` を使用して指定します。

:::image type="content" source="media/functions/function-use.png" alt-text="関数を使用する" lightbox="media/functions/function-use.png":::

## <a name="create-a-function"></a>関数を作成する
エディターで現在のクエリから関数を作成するには、 **[保存]** 、 **[Save as function]\(関数として保存\)** の順に選択します。 

:::image type="content" source="media/functions/function-save.png" alt-text="関数を作成する" lightbox="media/functions/function-save.png":::

Azure portal の Log Analytics で **[保存]** をクリックした後、次の表の情報を指定して関数を作成します。

| 設定 | 説明 |
|:---|:---|
| 関数名  | 関数の名前。 これには、スペースや特殊文字を含めることはできません。 アンダースコア (_) はソリューション関数用に予約されているため、この文字を先頭に付けないでください。 |
| 従来のカテゴリ | 関数のフィルター処理とグループ化に役立つように、定義済みのカテゴリを使用します。   |
| Save as computer group (コンピューター グループとして保存) | クエリを[コンピューター グループ](computer-groups.md)として保存します。  |
| パラメーター | 使用時に値を必要とする関数内の各変数に対し、パラメーターを追加します。 詳細については、「[関数パラメーター](#function-parameters)」を参照してください。 |

:::image type="content" source="media/functions/function-details.png" alt-text="関数の詳細" lightbox="media/functions/function-details.png":::

## <a name="function-parameters"></a>関数のパラメーター 
関数にパラメーターを追加すると、呼び出し時に特定の変数の値を指定できできます。 これにより、複数の異なるクエリで同じ関数を使用し、それぞれでパラメーターに異なる値を指定できます。 パラメーターは、次のプロパティによって定義されます。

| 設定 | 説明 |
|:---|:---|
| Type  | 値のデータ型。 |
| 名前  | パラメーターの名前。 これは、パラメーター値に置き換えるためにクエリで使用する必要がある名前です。  |
| 既定値 | 値が指定されない場合にパラメーターに使用される値。 |

パラメーターは、その作成時に、既定値のないパラメーターが既定値のあるパラメーターの前に配置されるように順序付けられます。

## <a name="working-with-function-code"></a>関数コードを操作する
関数のコードを表示することで、その動作を理解したり、ワークスペース関数のコードを変更したりすることができます。 エディターで現在のクエリに関数コードを追加するには、 **[Load the function code]\(関数コードの読み込み\)** を選択します。 空のクエリまたは既存のクエリの最初の行に追加すると、その関数名がタブに追加されます。ワークスペース関数の場合は、これによって関数の詳細を編集するためのオプションが有効になります。

:::image type="content" source="media/functions/function-code.png" alt-text="関数コードを読み込む" lightbox="media/functions/function-code.png":::

## <a name="edit-a-function"></a>関数を編集する
関数のプロパティまたはコードを編集するには、新しいクエリを作成してから、その関数の名前の上にカーソルを合わせ、 **[load function code]\(関数コードの読み込み\)** を選択します。 コードに必要な変更を加え、 **[保存]** 、 **[関数の詳細を編集]** の順に選択します。 **[保存]** をクリックする前に、関数のプロパティとパラメーターに必要な変更を加えます。

:::image type="content" source="media/functions/function-edit.png" alt-text="関数を編集する" lightbox="media/functions/function-edit.png":::
## <a name="example"></a>例
次のサンプル関数では、特定の日付以降で、特定のカテゴリに一致する、Azure アクティビティ ログ内のすべてのイベントが返されます。 

次のクエリを、ハードコードされた値を使用して開始します。 これにより、クエリが想定どおりに動作するかどうかが確認されます。

```Kusto
AzureActivity
| where CategoryValue == "Administrative"
| where TimeGenerated > todatetime("2021/04/05 5:40:01.032 PM")
```

:::image type="content" source="media/functions/example-query.png" alt-text="関数の例 - 最初のクエリ" lightbox="media/functions/example-query.png":::

次に、ハードコードされた値をパラメーター名に置き換えてから、 **[保存]** 、 **[Save as function]\(関数として保存\)** の順に選択して関数を保存します。

```Kusto
AzureActivity
| where CategoryValue == CategoryParam
| where TimeGenerated > DateParam
```

:::image type="content" source="media/functions/example-save-function.png" alt-text="関数の例 - 関数を保存する" lightbox="media/functions/example-save-function.png":::

 関数プロパティに次の値を入力します。

| プロパティ | 値 |
|:---|:---|
| 関数名 | AzureActivityByCategory |
| 従来のカテゴリ | デモ関数 |

関数を保存する前に、次のパラメーターを定義します。

| Type | 名前 | 既定値 |
|:---|:---|:---|
| string   | CategoryParam | "Administrative" |
| DATETIME | DateParam     | |

:::image type="content" source="media/functions/example-function-properties.png" alt-text="関数の例 - 関数プロパティ" lightbox="media/functions/example-function-properties.png":::

新しいクエリを作成し、その上にカーソルを合わせて新しい関数を表示します。 パラメーターの順序を確認します。これが、関数の使用時に指定する必要のある順序であるためです。

:::image type="content" source="media/functions/example-view-details.png" alt-text="関数の例 - 詳細を表示する" lightbox="media/functions/example-view-details.png":::

**[Use in editor]\(エディターで使用\)** を選択してクエリに新しい関数を追加し、次にパラメーターに値を追加します。 CategoryParam の値は、既定値があるため指定する必要はないことに注意してください。

:::image type="content" source="media/functions/example-use-function.png" alt-text="関数の例 - 関数を使用する" lightbox="media/functions/example-use-function.png":::



## <a name="next-steps"></a>次のステップ
Azure Monitor ログ クエリの記述に関するその他のレッスンを参照してください。

- [文字列操作](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#string-operations)

