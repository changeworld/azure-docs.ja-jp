---
title: DICOM 変更フィードの概要 - Azure Healthcare APIs
description: この記事では、DICOM 変更フィードの概念について説明します。
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 08/04/2021
ms.author: aersoy
ms.openlocfilehash: fc23c4cedbb6ee94c4f253cf53e0cdde27531fb4
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121779500"
---
# <a name="change-feed-overview"></a>変更フィードの概要

> [!IMPORTANT]
> Azure Healthcare APIs は現在プレビュー段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」に記載されています。

変更フィードは、DICOM サービスで発生したすべての変更のログを提供します。 変更フィードでは、これらの変更の順序指定済み、保証済み、不変、読み取り専用ログが提供されます。 変更フィードでは、DICOM サービスの履歴を確認し、サービス内の作成と削除に対して動作する機能が提供されます。

クライアント アプリケーションでは、ストリーミングまたはバッチ モードで、いつでもこれらのログを読み取ることができます。 変更フィードを使用すると、DICOM サービスで発生する変更イベントを処理する効率的でスケーラブルなソリューションを構築できます。

これらの変更イベントは、非同期的、段階的、または完全に処理できます。 任意の数のクライアント アプリケーションで、個別に変更フィードを並列で、および独自のペースで読み取ることができます。

要求を行う際は、URL の一部としてバージョンを指定してください。 詳細については、DICOM サービスの [API バージョン管理に関するドキュメント](api-versioning-dicom-service.md)を参照してください。

## <a name="api-design"></a>API の設計

API は、変更フィードと対話するための 2 つの `GET` エンドポイントを公開します。 変更フィードを使用する一般的なフローを[以下に示します](#example-usage-flow)。

動詞 | ルート              | 戻り値     | 説明
:--- | :----------------- | :---------- | :---
GET  | /changefeed        | JSON 配列  | [変更フィードを読み取ります](#read-change-feed)
GET  | /changefeed/latest | JSON オブジェクト | [変更フィードの最新のエントリを読み取ります](#get-latest-change-feed-item)

### <a name="object-model"></a>オブジェクト モデル

フィールド               | 型      | 説明
:------------------ | :-------- | :---
Sequence            | INT       | ページング (オフセット経由) または固定に使用できるシーケンス ID
StudyInstanceUid    | string    | 検査インスタンスの UID
SeriesInstanceUid   | string    | シリーズ インスタンスの UID
SopInstanceUid      | string    | SOP インスタンスの UID
アクション              | string    | 実行されたアクションです - `create` または `delete`
Timestamp           | DATETIME  | アクションが実行された日時 (UTC)
State               | string    | [メタデータの現在の状態です](#states)
メタデータ            | object    | オプションとして、インスタンスが存在する場合は、現在の DICOM メタデータです

#### <a name="states"></a>状態

状態    | 説明
:------- | :---
現在の  | このインスタンスが現行のバージョンです。
replaced | このインスタンスは、新しいバージョンに置き換えられました。
deleted  | このインスタンスは削除され、サービスで使用できなくなりました。

### <a name="read-change-feed"></a>変更フィードを読み取る

**ルート**: /changefeed?offset={int}&limit={int}&includemetadata={**true**|false}
```
[
    {
        "Sequence": 1,
        "StudyInstanceUid": "{uid}",
        "SeriesInstanceUid": "{uid}",
        "SopInstanceUid": "{uid}",
        "Action": "create|delete",
        "Timestamp": "2020-03-04T01:03:08.4834Z",
        "State": "current|replaced|deleted",
        "Metadata": {
            "actual": "metadata"
        }
    },
    {
        "Sequence": 2,
        "StudyInstanceUid": "{uid}",
        "SeriesInstanceUid": "{uid}",
        "SopInstanceUid": "{uid}",
        "Action": "create|delete",
        "Timestamp": "2020-03-05T07:13:16.4834Z",
        "State": "current|replaced|deleted",
        "Metadata": {
            "actual": "metadata"
        }
    }
    ...
]
```
#### <a name="parameters"></a>パラメーター

名前            | 型 | 説明
:-------------- | :--- | :---
offset          | INT  | 返される値の前にスキップするレコードの数
limit           | INT  | 返すレコードの数 (既定値: 10、最小: 1、最大: 100)
includemetadata | bool | メタデータを含めるかどうか (既定値: true)

### <a name="get-latest-change-feed-item"></a>最新の変更フィード項目を取得する

**ルート**: /changefeed/latest?includemetadata={**true**|false}

```
{
    "Sequence": 2,
    "StudyInstanceUid": "{uid}",
    "SeriesInstanceUid": "{uid}",
    "SopInstanceUid": "{uid}",
    "Action": "create|delete",
    "Timestamp": "2020-03-05T07:13:16.4834Z",
    "State": "current|replaced|deleted",
    "Metadata": {
        "actual": "metadata"
    }
}
```

#### <a name="parameters"></a>パラメーター

名前            | 型 | 説明
:-------------- | :--- | :---
includemetadata | bool | メタデータを含めるかどうか (既定値: true)

## <a name="usage"></a>使用方法

### <a name="example-usage-flow"></a>使用フロー例:

DICOM サービス内のインスタンスで他の処理を実行するアプリケーション例の使用フローを次に示します。

1. 変更フィードを監視するアプリケーションが起動します。
2. 次の条件で開始時に必要な現在の状態が確認されます。
   * 状態がある場合は、格納されているオフセット (シーケンス) を使用します。
   * 今まで起動しておらず、最初から開始する場合は `offset=0` を使用します。  
   * 今から処理するだけの場合は、`/changefeed/latest` にクエリを実行して最後のシーケンスを取得します。
3. 指定されたオフセット `/changefeed?offset={offset}` を使用して変更フィードに対してクエリを実行します
4. エントリがある場合:
   * 追加の処理が実行されます。  
   * 現在の状態が更新されます。  
   * 上の手順 2 から再度開始されます。
5. エントリがない場合は、構成された時間スリープし、手順 2 に戻って開始します。

### <a name="other-potential-usage-patterns"></a>その他の可能な使用パターン

変更フィードのサポートは、変更されたオブジェクトに基づいてデータを処理するシナリオに適しています。 たとえば、以下について使用できます。

* 作成されたオブジェクトまたは変更されたオブジェクトに基づいて、変更イベントまたはスケジュールの実行に対応する、ML のような接続されたアプリケーション パイプラインを構築する。
* オブジェクトに対して行われた変更に基づいて、ビジネス分析の洞察とメトリックを引き出す。
* 変更フィードをポーリングして、プッシュ通知のイベント ソースを作成する。

## <a name="summary"></a>まとめ

この記事では、変更フィードの REST API 設計と可能な使用シナリオについて説明しました。 変更フィードの詳細については、[変更フィードからの変更のプル](pull-dicom-changes-from-change-feed.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

>[!div class="nextstepaction"]
>[DICOM サービスの概要](dicom-services-overview.md)

