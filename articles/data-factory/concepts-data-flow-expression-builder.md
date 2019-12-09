---
title: Azure Data Factory のマッピング データ フローの式ビルダー
description: Azure Data Factory のマッピング データ フローの式ビルダー
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/17/2019
ms.openlocfilehash: 3664a7c311e15ce3aa61fc71f98a46e3f2618143
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184659"
---
# <a name="mapping-data-flow-expression-builder"></a>マッピング データ フローの式ビルダー



Azure Data Factory のマッピング データ フローには、データ変換の式を入力できる式ボックスが表示されます。 このボックス内で、データ フローからの列、フィールド、変数、パラメーター、関数を使用します。 式を作成するには、式ビルダーを使用します。式ビルダーは、変換内の式テキスト ボックスをクリックすると起動します。 変換の列を選択したときに、[計算列] のオプションが表示されることもあります。 このオプションをクリックすると、起動した式ビルダーも表示されます。

![式ビルダー](media/data-flow/xpb1.png "式ビルダー")

式ビルダー ツールでは、テキスト エディター オプションが既定で設定されています。 オートコンプリート機能では、Azure Data Factory のデータ フロー全体からオブジェクト モデルが読み取られ、そのモデルが構文チェックされ、強調表示されます。

![式ビルダーのオートコンプリート](media/data-flow/expb1.png "式ビルダーのオートコンプリート")

## <a name="build-schemas-in-output-schema-pane"></a>[Output Schema]\(出力スキーマ\) ウィンドウにスキーマを作成する

![複合列の追加](media/data-flow/complexcolumn.png "列の追加")

左側の [Output Schema]\(出力スキーマ\) ウィンドウに、変更中の列とスキーマに追加している列が表示されます。 ここでは、単純なデータ構造と複雑なデータ構造を対話形式で作成できます。 [列の追加] を使用してフィールドを追加し、[Add subcolumn]\(サブ列の追加\) を使用して階層を構築します。

![サブ列の追加](media/data-flow/addsubcolumn.png "サブ列の追加")

## <a name="data-preview-in-debug-mode"></a>デバッグ モードでのデータのプレビュー

![式ビルダー](media/data-flow/exp4b.png "式データのプレビュー")

データ フロー式を処理しているときに、Azure Data Factory のデータ フロー デザイン サーフェイスからデバッグ モードをオンにします。これにより、作業しながら、作成中の式のデータの結果をプレビューできます。 式に対して、リアルタイムのライブ デバッグが可能です。

![デバッグ モード](media/data-flow/debugbutton.png "[デバッグ] ボタン")

[更新] ボタンをクリックして、式の結果をソースのライブ サンプルに対してリアルタイムで更新します。

![式ビルダー](media/data-flow/exp5.png "式データのプレビュー")

## <a name="comments"></a>説明

単一行および複数行コメントの構文を使用して、式にコメントを追加します。

![説明](media/data-flow/comments.png "説明")

## <a name="regular-expressions"></a>正規表現

Azure Data Factory の Data Flow の式言語では ([詳細なリファレンス ドキュメントはこちら](https://aka.ms/dataflowexpressions))、正規表現構文を含む関数を使用できます。 正規表現関数を使用する場合、式ビルダーでは、円記号 (\\) をエスケープ文字シーケンスとして解釈することが試みられます。 正規表現で円記号を使用するときは、正規表現全体をティック (\`) 内に入れるか、円記号を二重で使用します。

ティックの使用例

```
regex_replace('100 and 200', `(\d+)`, 'digits')
```

円記号を二重で使用した場合

```
regex_replace('100 and 200', '(\\d+)', 'digits')
```

## <a name="addressing-array-indexes"></a>配列インデックスのアドレス指定

配列を返す式関数では、角かっこ [] を使用して、返される配列オブジェクト内の特定のインデックスをアドレス指定します。 この配列は、1 ベースです。

![式ビルダーの配列](media/data-flow/expb2.png "式データのプレビュー")

## <a name="handling-names-with-special-characters"></a>特殊文字を使用した名前の処理

特殊文字またはスペースが含まれている列名がある場合は、その名前を中かっこを使って囲みます。
* ```{[dbo].this_is my complex name$$$}```

## <a name="keyboard-shortcuts"></a>キーボード ショートカット

* ```Ctrl-K Ctrl-C```:行全体をコメントにします
* ```Ctrl-K Ctrl-U```:コメントを解除します
* ```F1```:エディターのヘルプ コマンドを提供します
* ```Alt-Down Arrow```:現在の行を下に移動します
* ```Alt-Up Arrow```:現在の行を上に移動します
* ```Cntrl-Space```:コンテキスト ヘルプを表示します

## <a name="manual-comments"></a>手動コメント

* ```/* This is my comment */```

* ```/* This is a```
*   ```multi-line comment */```
   
* ```// This is a single line comment```

式の先頭にコメントを配置すると、変換式を文書化するための変換テキスト ボックスにコメントが表示されます。

![説明](media/data-flow/comments2.png "説明")

## <a name="convert-to-dates-or-timestamps"></a>日付またはタイムスタンプに変換する

```toString(toTimestamp('12/31/2016T00:12:00', 'MM/dd/yyyy\'T\'HH:mm:ss'), 'MM/dd /yyyy\'T\'HH:mm:ss')```

タイムスタンプ出力に文字列リテラルを含めるには、toString() 内に変換をラップする必要があることに注意してください

## <a name="handling-column-names-with-special-characters"></a>特殊文字を含む列名の処理

特殊文字またはスペースが含まれている列名がある場合は、その名前を中かっこを使って囲みます。

```{[dbo].this_is my complex name$$$}```

## <a name="next-steps"></a>次の手順

[データ変換式の作成を開始する](data-flow-expression-functions.md)
