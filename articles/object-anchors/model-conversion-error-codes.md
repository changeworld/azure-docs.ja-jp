---
title: モデル変換のエラー コード
description: Azure Object Anchors サービスのモデル変換のエラー コード。
author: jastenze
manager: vriveras
ms.author: jastenze
ms.date: 04/20/2021
ms.topic: overview
ms.service: azure-object-anchors
ms.openlocfilehash: e47e4e9f784e51c646da22063ee954d9b60a4e11
ms.sourcegitcommit: abf31d2627316575e076e5f3445ce3259de32dac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2021
ms.locfileid: "114202673"
---
# <a name="model-conversion-error-codes"></a>モデル変換のエラー コード

モデル変換エラーの一般的なモードについては、`Azure.MixedReality.ObjectAnchors.Conversion.AssetConversionOperation` の `Value` フィールドから得られる `Azure.MixedReality.ObjectAnchors.Conversion.AssetConversionProperties` オブジェクトに、`ConversionErrorCode` 型の ErrorCode フィールドが格納されます。 一般的なエラー モードを列挙するこの型は、エラー メッセージのローカリゼーションや、障害復旧、エラーの修正方法に関するユーザー向けのヒントに使用されます。

| エラー コード                    | 説明                       |  対応策                       |
| ---                      | ---                               | ---                               |
| INVALID_ASSET_URI | 変換ジョブの開始時に指定された URI にアセットが見つかりませんでした。 | アセット変換ジョブをトリガーする際に、変換対象のアセットがアップロードされたサービスから取得したアップロード URI を指定します。 |
| INVALID_JOB_ID | 作成するアセット変換ジョブ用に指定された ID が、既定値であるすべてゼロの GUID に設定されていました。 | アセット変換ジョブの作成時に GUID を指定する場合、既定値であるすべてゼロの GUID は避けてください。 |
| INVALID_GRAVITY | アセット変換ジョブの作成時に指定された重力ベクトルが、完全にゼロ設定されたベクトルでした。 | アセットの変換を開始する際に、アップロードしたアセットに対応する重力ベクトルを指定します。 |
| INVALID_SCALE | 指定されたスケール ファクターが 0 以外の正の値ではありませんでした。 | アセットの変換を開始する際に、アップロードしたアセットの測定単位スケール (メートル単位) に対応するスカラー値を指定します。 |
| ASSET_SIZE_TOO_LARGE | アセットから生成された中間 .PLY ファイルまたはそのシリアル化データが大きすぎます。 | 変換対象のアセットを送信する際は、あらかじめアセット サイズのガイドラインを参照し、それらを遵守してください (aka.ms/aoa/faq)。 |
| ASSET_DIMENSIONS_OUT_OF_BOUNDS | アセットのサイズが物理的なサイズの制限を超えていました。 ジョブの作成時にアセットに対して設定されたスケールが適切でない可能性があります。 | 変換対象のアセットを送信する際は、あらかじめアセット サイズのガイドラインを参照し、それらを遵守すると共に、指定したスケールが、アップロードしたアセットに対応していることを確認してください (aka.ms/aoa/faq)。 |
| ZERO_FACES | アセットから生成された中間 .PLY ファイルには面が含まれていないと判断されたため、変換の対象としては無効となりました。 | アセットが有効なメッシュであることを確認してください。 |
| INVALID_FACE_VERTICES | アセットから生成された中間 .PLY ファイルに、存在しない頂点を参照する面が含まれていました。 | アセット ファイルが正しく構築されていることを確認してください。 |
| ZERO_TRAJECTORIES_GENERATED | アップロードしたアセットから生成されたカメラの軌道が空でした。 | 変換対象のアセットを送信する際は、あらかじめアセットのガイドラインを参照し、それらを遵守してください (aka.ms/aoa/faq)。 |
| TOO_MANY_RIG_POSES | 中間 .PLY ファイル内のリグ ポーズの数がサービス制限を超えていました。 | 変換対象のアセットを送信する際は、あらかじめアセット サイズのガイドラインを参照し、それらを遵守してください (aka.ms/aoa/faq)。 |
| SERVICE_ERROR | 不明なサービス エラーが発生しました。 | 問題が解消しない場合は、Object Anchors サービス チームのメンバーにお問い合わせください (https://github.com/Azure/azure-object-anchors/issues )。 |
| ASSET_CANNOT_BE_CONVERTED | 指定されたアセットが破損しているか、その形式に誤りがあるか、その指定の形式では変換できませんでした。 | アセットは、指定されたタイプの正しく構築されたファイルであることが必要です。変換対象のアセットを送信する際は、あらかじめアセット サイズのガイドラインを参照し、それらを遵守してください (aka.ms/aoa/faq)。 |

実際のアセット変換ジョブの外で発生するエラーは、例外としてスローされます。 中でも、サービス呼び出しで不成功の HTTP 応答コード (4xx または 5xx) や予期しない HTTP 応答コードが返された場合にスローされることのある `Azure.RequestFailedException` が代表的です。 これらの例外の詳細については、その例外の `Status`、`ErrorCode`、`Message` フィールドを調査してください。

| 例外                  | 原因                       |
| ---                      | ---                               |
| ArgumentException |  <ul><li>無効な構成、またはすべてゼロのアカウント ID を使用して ObjectAnchorsConversionClient で要求を構築したときに発生します。</li><li>無効な空白のアカウント ドメインを使用して ObjectAnchorsConversionClient を初期化しようとしたときに発生します。</li><li>サポートされていないサービス バージョンが ObjectAnchorsConversionClientOptions を通じて ObjectAnchorsConversionClient に提供されたときに発生します。</li></ul> |
| ArgumentNullException | <ul><li>無効な null 値のアカウント ドメインを使用して ObjectAnchorsConversionClient を初期化しようとしたときに発生します。</li><li>無効な null 値の資格情報を使用して ObjectAnchorsConversionClient を初期化しようとしたときに発生します。</li></ul> |
| RequestFailedException | <ul><li>無効な HTTP 状態コード (実行予定/実行中/実行済みジョブの状態とは関係がない) によって発生したその他すべての問題 (アカウントが見つからない、フロントエンドで無効なアップロード URI が検出された、フロントエンド サービスのエラーなど) に対して発生します。</li></ul> |
| UnsupportedAssetFileTypeException | <ul><li>Azure Object Anchors 変換サービスによってサポートされない拡張機能または特定のファイルタイプを含むアセットを使用してジョブを送信しようとしたときに発生します。</li></ul> |