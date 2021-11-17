---
title: Service Connector のトラブルシューティング ガイダンス
description: Service Connector のエラー一覧と推奨アクション
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: how-to
ms.date: 10/29/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 8a1379495dacba7d2c0cf21af3a1e5ec2f45ed41
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132283185"
---
# <a name="how-to-troubleshoot-with-service-connector"></a>Service Connector を使用してトラブルシューティングを行う方法

問題が発生した場合は、エラー メッセージを参照して、推奨アクションまたは修正を見つけることができます。 この攻略ガイドでは、Service Connector のトラブルシューティングを行うためのオプションについて説明します。

## <a name="error-message-and-suggested-actions-from-portal"></a>ポータルからのエラー メッセージと推奨アクション

| エラー メッセージ | 推奨アクション |
| --- | --- |
| 不明なリソースの種類 | <ul><li>ソースおよびターゲットのリソースを確認して、サービスの種類が Service Connector でサポートされているかどうかを確認します。</li><li>指定したソースとターゲットの接続の組み合わせが Service Connector でサポートされているかどうかを確認します。</li></ul> |
| 不明なリソースの種類 | <ul><li>ターゲット リソースが存在するかどうかを確認します。</li><li>ターゲット リソース ID が正しいことを確認します。</li></ul> |
| サポートされていないリソース | <ul><li>指定したソースとターゲットの接続の組み合わせで認証の種類がサポートされているかどうかを確認します。</li></ul> |

### <a name="error-typeerror-message-and-suggested-actions-using-azure-cli"></a>エラーの種類、エラー メッセージ、Azure CLI を使用した推奨アクション

### <a name="invalidargumentvalueerror"></a>InvalidArgumentValueError


| エラー メッセージ | 推奨アクション |
| --- | --- |
| ソース リソース ID が無効です: `{SourceId}` | <ul><li>ソース リソース ID が Service Connector でサポートされているかどうかを確認します。</li><li>ソース リソース ID が正しいことを確認します。</li></ul> |
| ターゲット リソース ID が無効です: `{TargetId}` | <ul><li>ターゲット サービスの種類が Service Connector でサポートされているかどうかを確認します。</li><li>ターゲット リソース ID が正しいことを確認します。</li></ul> |
| 接続 ID が無効です: `{ConnectionId}` | <ul><li>接続 ID が正しいことを確認します。</li></ul> |


### <a name="requiredargumentmissingerror"></a>RequiredArgumentMissingError

| エラー メッセージ | 推奨アクション |
| --- | --- |
| `{Argument}` を空白にすることはできません | ユーザーは対話型の入力には引数値を指定する必要があります |
| パラメーター `{Parameter}` に必要なキーがありません。 考えられるすべてのキーは次のとおりです: `{Keys}` | 通常は `--param key1=val1 key2=val2` 形式で認証情報パラメーターの値を指定します。 |
| 必要な引数がありません。引数を指定してください: `{Arguments}` | 必要な引数を指定します。 | 

### <a name="validationerror"></a>ValidationError

| エラー メッセージ | 推奨アクション |
| --- | --- |
| 必要な認証情報は 1 つのみです | ユーザーが指定できる認証情報パラメーターは 1 つのみです。認証情報が指定されていないか、複数の認証情報パラメーターが指定されているかを確認します。 |
| 接続を更新するときに、認証情報引数を指定する必要があります: `{ConnectionName}` | シークレットの種類の接続を更新する場合は、認証情報パラメーターを指定する必要があります。 (これは、ARM API を使用してユーザーのシークレットにアクセスできないためです) |
| 更新するには、クライアントの種類または認証情報のいずれかを指定する必要があります | 接続を更新する場合は、クライアントの種類または認証情報のいずれかを指定する必要があります。 |
| 使用法エラー: {} [KEY=VALUE ...] | 使用可能なキーを確認し、通常は `--param key1=val1 key2=val2` 形式で認証情報パラメーターの値を指定します。 |
| サポートされていないキー `{Key}` がパラメーター `{Parameter}` に指定されています。 考えられるすべてのキーは次のとおりです: `{Keys}` | 使用可能なキーを確認し、通常は `--param key1=val1 key2=val2` 形式で認証情報パラメーターの値を指定します。 |
| プロビジョニングに失敗しました。ターゲット リソースを手動で作成してから、接続を作成してください。 Error details: `{ErrorTrace}` (エラーの詳細: \{0\}) | <ul><li>再試行してください。</li><li>ターゲット リソースを手動で作成してから、接続を作成してください。</li></ul> |

## <a name="next-steps"></a>次のステップ

Service Connector の詳細については、以下のチュートリアルに従ってください。

> [!div class="nextstepaction"]
> [Service Connector の概念について学習する](./concept-service-connector-internals.md)
