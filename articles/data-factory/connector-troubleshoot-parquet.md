---
title: Parquet 形式コネクタのトラブルシューティング
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory と Azure Synapse Analytics の Parquet 形式コネクタの問題のトラブルシューティングの方法について説明します。
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/13/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: b12666fdd66b3c85702b7222f2f2edca7136e323
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130062759"
---
# <a name="troubleshoot-the-parquet-format-connector-in-azure-data-factory-and-azure-synapse"></a>Azure Data Factory と Azure Synapse で Parquet 形式コネクタのトラブルシューティング

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、Azure Data Factory と Azure Synapse の Parquet 形式コネクタの一般的な問題のトラブルシューティングのための提案を示します。

## <a name="error-code-parquetjavainvocationexception"></a>エラー コード:ParquetJavaInvocationException

- **メッセージ**: `An error occurred when invoking java, message: %javaException;.`

- **原因と推奨事項**: このエラーはさまざまな原因により発生する可能性があります。 考えられる原因の分析および関連する推奨事項については、以下の一覧を確認してください。

    | 原因分析                                               | 推奨                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | エラー メッセージに "java.lang.OutOfMemory"、"Java heap space"、および "doubleCapacity" という文字列が含まれている場合は、通常、古いバージョンの Integration Runtime でのメモリ管理の問題です。 | セルフホステッド IR を使用していて、バージョンが 3.20.7159.1 よりも前の場合は、最新バージョンにアップグレードすることをお勧めします。 |
    | エラー メッセージに "java.lang.OutOfMemory" という文字列が含まれている場合は、統合ランタイムにファイルを処理するのに十分なリソースがありません。 | 統合ランタイムで同時実行を制限してください。 セルフホステッド IR の場合は、8 GB 以上のメモリを持つ強力なマシンにスケールアップします。 |
    | エラー メッセージに "NullPointerReference" という文字列が含まれている場合、一時的なエラーである可能性があります。 | 操作を再試行してください。 問題が解決しない場合は、サポートにお問い合わせください。 |

## <a name="error-code-parquetinvalidfile"></a>エラー コード:ParquetInvalidFile

- **メッセージ**: `File is not a valid Parquet file.`

- **原因**:これは Parquet ファイルの問題です。

- **推奨事項**:入力が有効な Parquet ファイルであるかどうかを確認します。

## <a name="error-code-parquetnotsupportedtype"></a>エラー コード:ParquetNotSupportedType

- **メッセージ**: `Unsupported Parquet type. PrimitiveType: %primitiveType; OriginalType: %originalType;.`

- **原因**: Parquet 形式は、Azure Data Factory および Synapse のパイプラインではサポートされていません。

- **推奨事項**: [コピー アクティビティでサポートされているファイル形式と圧縮コーデック](./supported-file-formats-and-compression-codecs.md)に関する記事に移動して、ソース データを再確認します。

## <a name="error-code-parquetmisseddecimalprecisionscale"></a>エラー コード:ParquetMissedDecimalPrecisionScale

- **メッセージ**: `Decimal Precision or Scale information is not found in schema for column: %column;.`

- **原因**:数値の有効桁数と小数点以下桁数が解析されましたが、そのような情報は提供されませんでした。

- **推奨事項**:ソースは、正しい有効桁数と小数点以下桁数の情報を返しません。 問題の列で情報を確認します。

## <a name="error-code-parquetinvaliddecimalprecisionscale"></a>エラー コード:ParquetInvalidDecimalPrecisionScale

- **メッセージ**: `Invalid Decimal Precision or Scale. Precision: %precision; Scale: %scale;.`

- **原因**:スキーマが無効です。

- **推奨事項**:問題の列の有効桁数と小数点以下桁数を確認してください。

## <a name="error-code-parquetcolumnnotfound"></a>エラー コード:ParquetColumnNotFound

- **メッセージ**: `Column %column; does not exist in Parquet file.`

- **原因**:ソース スキーマが、シンク スキーマと一致していません。

- **推奨事項**:アクティビティのマッピングを確認してください。 ソース列を正しいシンク列にマッピングできることを確認してください。

## <a name="error-code-parquetinvaliddataformat"></a>エラー コード:ParquetInvalidDataFormat

- **メッセージ**: `Incorrect format of %srcValue; for converting to %dstType;.`

- **原因**:データは、mappings.source に指定されている型に変換できません。

- **推奨事項**:ソース データを再確認するか、Copy アクティビティの列マッピングでこの列に適切なデータ型を指定してください。 詳細については、[コピー アクティビティでサポートされるファイル形式と圧縮コーデック](./supported-file-formats-and-compression-codecs.md)に関する記事を参照してください。

## <a name="error-code-parquetdatacountnotmatchcolumncount"></a>エラー コード:ParquetDataCountNotMatchColumnCount

- **メッセージ**: `The data count in a row '%sourceColumnCount;' does not match the column count '%sinkColumnCount;' in given schema.`

- **原因**:ソース列数とシンクの列数が一致しません。

- **推奨事項**:ソース列数が 'mapping' のシンク列数と同じであることを確認してください。

## <a name="error-code-parquetdatatypenotmatchcolumntype"></a>エラー コード:ParquetDataTypeNotMatchColumnType

- **メッセージ**: `The data type %srcType; is not match given column type %dstType; at column '%columnIndex;'.`

- **原因**:ソースからのデータは、シンクで定義されている型に変換できません。

- **推奨事項**:mapping.sink に正しい型を指定してください。

## <a name="error-code-parquetbridgeinvaliddata"></a>エラー コード:ParquetBridgeInvalidData

- **メッセージ**: `%message;`

- **原因**:データ値が制限を超えています。

- **推奨事項**:操作を再試行してください。 問題が解決しない場合は、サポートにお問い合わせください。

## <a name="error-code-parquetunsupportedinterpretation"></a>エラー コード:ParquetUnsupportedInterpretation

- **メッセージ**: `The given interpretation '%interpretation;' of Parquet format is not supported.`

- **原因**:このシナリオはサポートされていません。

- **推奨事項**:'ParquetInterpretFor' を 'sparkSql' にしないでください。

## <a name="error-code-parquetunsupportfilelevelcompressionoption"></a>エラー コード:ParquetUnsupportFileLevelCompressionOption

- **メッセージ**: `File level compression is not supported for Parquet.`

- **原因**:このシナリオはサポートされていません。

- **推奨事項**:ペイロード内の 'CompressionType' を削除してください。

## <a name="error-code-usererrorjniexception"></a>エラー コード:UserErrorJniException

- **メッセージ**: `Cannot create JVM: JNI return code [-6][JNI call failed: Invalid arguments.]`

- **原因**:無効な (グローバル) 引数が設定されているため、Java 仮想マシン (JVM) を作成できません。

- **推奨事項**:セルフホステッド IR の *各ノード* をホストするマシンにログインします。 次のように、システム変数が正しく設定されていることを確認します`_JAVA_OPTIONS "-Xms256m -Xmx16g" with memory bigger than 8 G`。 すべての IR ノードを再起動してから、パイプラインを再実行します。

## <a name="arithmetic-overflow"></a>算術オーバーフロー

- **現象**:Parquet ファイルをコピーするときに、次のエラー メッセージが発生しました。`Message = Arithmetic Overflow., Source = Microsoft.DataTransfer.Common`

- **原因**:現時点では、Oracle から Parquet にファイルをコピーするときにサポートされている値は、小数点以下の桁数が 38 桁以内で整数部分の桁数が 20 以下のものだけです。 

- **解決方法**:回避策として、この問題のある列を VARCHAR2 に変換できます。

## <a name="no-enum-constant"></a>列挙型定数はありません

- **現象**:Parquet 形式にデータをコピーするときに、次のエラー メッセージが発生しました。`java.lang.IllegalArgumentException:field ended by &apos;;&apos;`、または `java.lang.IllegalArgumentException:No enum constant org.apache.parquet.schema.OriginalType.test`。

- **原因**: 

    この問題は、列名に空白またはサポートされていない特殊文字 (;{}()\n\t= など) があることによって発生する可能性があります。Parquet はこのような形式をサポートしていないためです。 

    たとえば、*contoso(test)* のような列名は [コード](https://github.com/apache/parquet-mr/blob/master/parquet-column/src/main/java/org/apache/parquet/schema/MessageTypeParser.java) `Tokenizer st = new Tokenizer(schemaString, " ;{}()\n\t");` の丸かっこで囲まれた型を解析します。 このような "test" 型がないため、エラーがスローされます。

    サポートされている型を確認するには、GitHub の [apache/parquet-mr サイト](https://github.com/apache/parquet-mr/blob/master/parquet-column/src/main/java/org/apache/parquet/schema/OriginalType.java)にアクセスしてください。

- **解決方法**: 

    次の点を確認してください。
    - シンク列名に空白が含まれています。
    - 空白がある最初の行が、列名として使用されています。
    - 型 OriginalType はサポートされています。 次の特殊文字を使用しないようにしてください: `,;{}()\n\t=`。 

## <a name="error-code-parquetdatetimeexceedlimit"></a>エラー コード: ParquetDateTimeExceedLimit

- **メッセージ**: `The Ticks value '%ticks;' for the datetime column must be between valid datetime ticks range -621355968000000000 and 2534022144000000000.`

- **原因**:datetime 値が '0001-01-01 00:00:00' である場合は、ユリウス暦とグレゴリオ暦の違いが原因で発生する可能性があります。 詳細については、「[ユリウス暦と予期的グレゴリオ暦の日付の違い](https://en.wikipedia.org/wiki/Proleptic_Gregorian_calendar#Difference_between_Julian_and_proleptic_Gregorian_calendar_dates)」を参照してください。

- **解決方法**: ティック値を確認し、datetime 値 '0001-01-01 00:00:00' を使用しないようにしてください。

## <a name="error-code-parquetinvalidcolumnname"></a>エラー コード: ParquetInvalidColumnName

- **メッセージ**: `The column name is invalid. Column name cannot contain these character:[,;{}()\n\t=]`

- **原因**: 列名に無効な文字が含まれています。

- **解決方法**: シンク列名が有効になるように列マッピングを追加または変更します。

## <a name="the-file-created-by-the-copy-data-activity-extracts-a-table-that-contains-a-varbinary-max-column"></a>データのコピー アクティビティによって作成されたファイルで、varbinary (max) 列を含むテーブルが抽出される

- **現象**: データのコピー アクティビティによって作成された Parquet ファイルで、varbinary (max) 列を含むテーブルが抽出されます。

- **原因**: この問題は、大きな列を読み取っている Parquet-mr ライブラリのバグが原因で発生します。 

- **解決方法**: ファイルあたりの行数を 1000 行に制限して、より小さいファイル (サイズ < 1G) を生成してみてください。

## <a name="next-steps"></a>次のステップ

トラブルシューティングのその他のヘルプについては、次のリソースを参照してください。

- [コネクタのトラブルシューティング ガイド](connector-troubleshoot-guide.md)
- [Data Factory ブログ](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Data Factory の機能のリクエスト](/answers/topics/azure-data-factory.html)
- [Azure のビデオ](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Microsoft Q&A ページ](/answers/topics/azure-data-factory.html)
- [Data Factory の Stack Overflow フォーラム](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Data Factory に関する Twitter 情報](https://twitter.com/hashtag/DataFactory)
