---
title: ORC フォーマット コネクタのトラブルシューティング
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory と Azure Synapse Analytics で ORC フォーマット コネクタの問題を解決する方法について説明します。
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: 973322c2ec7747fc5233eb897b302bfc910fee1b
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390464"
---
# <a name="troubleshoot-the-orc-format-connector-in-azure-data-factory-and-azure-synapse"></a>Azure Data Factory と Azure Synapse で ORC フォーマット コネクタの問題を解決する

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、Azure Data Factory と Azure Synapse で ORC フォーマット コネクタの一般的問題を解決する方法を提案します。

## <a name="error-code-orcjavainvocationexception"></a>エラー コード:OrcJavaInvocationException

- **メッセージ**: `An error occurred when invoking Java, message: %javaException;.`
- **原因と推奨事項**: このエラーはさまざまな原因により発生する可能性があります。 考えられる原因の分析および関連する推奨事項については、以下の一覧を確認してください。

    | 原因分析                                               | 推奨                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | エラー メッセージに "java.lang.OutOfMemory"、"Java heap space"、および "doubleCapacity" という文字列が含まれている場合は、通常、古いバージョンの Integration Runtime でのメモリ管理の問題です。 | セルフホステッド統合ランタイムを使用している場合は、最新バージョンにアップグレードすることをお勧めします。 |
    | エラー メッセージに "java.lang.OutOfMemory" という文字列が含まれている場合は、統合ランタイムにファイルを処理するのに十分なリソースがありません。 | 統合ランタイムで同時実行を制限してください。 セルフホステッド IR の場合は、8 GB 以上のメモリを備えた強力なマシンにスケールアップしてください。 |
    |エラー メッセージに "NullPointerReference" という文字列が含まれている場合、その原因は一時的なエラーである可能性があります。 | 操作を再試行してください。 問題が解決しない場合は、サポートにお問い合わせください。 |
    | エラー メッセージに "BufferOverflowException" という文字列が含まれている場合、その原因は一時的なエラーである可能性があります。 | 操作を再試行してください。 問題が解決しない場合は、サポートにお問い合わせください。 |
    | エラー メッセージに "java.lang.ClassCastException:org.apache.hadoop.hive.serde2.io.HiveCharWritable can't be cast to org.apache.hadoop.io.Text" (java.lang.ClassCastException:org.apache.hadoop.hive.serde2.io.HiveCharWritable を org.apache.hadoop.io.Text にキャストできません) という文字列が含まれている場合、その原因は Java ランタイム内の型変換の問題である可能性があります。 通常は、ソース データを Java ランタイムで適切に処理できないことを意味します。 | これはデータの問題です。 ORC 形式のデータで、char または varchar の代わりに string を使用してみてください。 |

## <a name="error-code-orcdatetimeexceedlimit"></a>エラー コード:OrcDateTimeExceedLimit

- **メッセージ**: `The Ticks value '%ticks;' for the datetime column must be between valid datetime ticks range -621355968000000000 and 2534022144000000000.`

- **原因**:datetime 値が '0001-01-01 00:00:00' である場合は、[ユリウス暦とグレゴリオ暦](https://en.wikipedia.org/wiki/Proleptic_Gregorian_calendar#Difference_between_Julian_and_proleptic_Gregorian_calendar_dates)の違いが原因で発生する可能性があります。

- **推奨事項**:ティック値を確認し、datetime 値 '0001-01-01 00:00:00' を使用しないようにしてください。

## <a name="next-steps"></a>次のステップ

トラブルシューティングのその他のヘルプについては、次のリソースを参照してください。

- [コネクタのトラブルシューティング ガイド](connector-troubleshoot-guide.md)
- [Data Factory ブログ](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Data Factory の機能のリクエスト](/answers/topics/azure-data-factory.html)
- [Azure のビデオ](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Microsoft Q&A ページ](/answers/topics/azure-data-factory.html)
- [Data Factory の Stack Overflow フォーラム](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Data Factory に関する Twitter 情報](https://twitter.com/hashtag/DataFactory)
