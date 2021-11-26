---
title: データ ライフサイクルを自動管理してコストを最適化する
titleSuffix: Azure Storage
description: Azure Storage ライフサイクル管理ポリシーを使用して、ホット、クール、アーカイブの各階層間でデータを移動するための自動ルールを作成します。
author: tamram
ms.author: tamram
ms.date: 08/18/2021
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.reviewer: yzheng
ms.custom: devx-track-azurepowershell, references_regions
ms.openlocfilehash: 466ac390f115a9fea54c99a4d64d103e90fcee8d
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132724048"
---
# <a name="optimize-costs-by-automatically-managing-the-data-lifecycle"></a>データ ライフサイクルを自動管理してコストを最適化する

データ セットには一意のライフサイクルがあります。 ライフサイクルの早い段階で、一部のデータに頻繁にアクセスされます。 しかし、データが古くなるにつれてアクセスの必要性が急激に低下することがよくあります。 使用されずにクラウドに留まるデータや、いったん格納されるとめったにアクセスされないデータもあります。 データ セットには、作成後数日や数か月で期限切れになるものがある一方で、ライフサイクル全体にわたってアクティブに読み取りと変更が行われるデータ セットもあります。 Azure Storage のライフサイクル管理にはルールベースのポリシーが用意されており、これを使用すると、最適なアクセス層に BLOB データを移行したり、データ ライフサイクルの最後にデータを期限切れにしたりすることができます。

ライフサイクル管理ポリシーを使用すると、以下を行えます。

- パフォーマンスを最適化するため、BLOB がアクセスされたときに BLOB をクールからホットに直ちに移行します。
- コストを最適化するため、BLOB、BLOB のバージョン、BLOB のスナップショットが一定期間アクセスも変更もされていない場合、それらのオブジェクトをよりクールなストレージ層に移行します。 このシナリオでは、ライフサイクル管理ポリシーによって、オブジェクトをホットからクールに、ホットからアーカイブに、またはクールからアーカイブに移動できます。
- BLOB、BLOB のバージョン、BLOB のスナップショットを、それらのライフサイクルの最後に削除します。
- ストレージ アカウント レベルで、1 日 1 回実行される規則を定義する。
- 名前のプレフィックスまたは [BLOB インデックス タグ](storage-manage-find-blobs.md)をフィルターとして使用して、コンテナーまたは BLOB のサブセットにルールを適用します。

データがライフサイクルの初期段階には頻繁にアクセスされるものの、2 週間後にはたまにしかアクセスされなくなるというシナリオについて考えてみましょう。 1 か月を超えると、そのデータ セットにはほとんどアクセスされなくなります。 このシナリオの初期段階ではホット ストレージが最適です。 ときどきアクセスされるデータにはクール ストレージが適しています。 1 か月以上が経過したデータに最も適しているのは、アーカイブ ストレージです。 ライフサイクル管理ポリシー ルールを使用して、経過時間に基づいてデータを適切なストレージ層に移動することで、ニーズに合う最もコストのかからないソリューションを設計できます。

汎用 v2、Premium ブロック BLOB、Blob Storage のアカウントでは、ブロック BLOB と追加 BLOB でライフサイクル管理ポリシーがサポートされています。 ライフサイクル管理は、 *$logs* コンテナーや *$web* コンテナーなどのシステム コンテナーには影響を与えません。

> [!IMPORTANT]
> データ セットが読み取り可能である必要がある場合は、BLOB をアーカイブ層に移動するポリシーを設定しないでください。 アーカイブ層の BLOB は、最初にリハイドレートされていない限り読み取ることができません。これは時間とコストがかかる場合があるプロセスです。 詳細については、「[アーカイブ層からの BLOB のリハイドレートの概要](archive-rehydrate-overview.md)」を参照してください。

## <a name="lifecycle-management-policy-definition"></a>ライフサイクル管理ポリシーの定義

ライフサイクル管理ポリシーは、JSON ドキュメントに記述されたルールのコレクションです。 次のサンプル JSON に、完全なルール定義を示します。

```json
{
  "rules": [
    {
      "name": "rule1",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {...}
    },
    {
      "name": "rule2",
      "type": "Lifecycle",
      "definition": {...}
    }
  ]
}
```

1 つのポリシーは、次の表で説明するように、ルールのコレクションです。

| パラメーター名 | パラメーターのタイプ | Notes |
|----------------|----------------|-------|
| `rules`        | ルール オブジェクトの配列 | ポリシーには少なくとも 1 つのルールが必要です。 ポリシーでは最大 100 のルールを定義できます。|

ポリシー内の各ルールには、次の表で説明するように、いくつかのパラメーターがあります。

| パラメーター名 | パラメーターのタイプ | Notes | 必須 |
|--|--|--|--|
| `name` | String | ルール名には最大 256 の英数字を含めることができます。 ルール名は大文字と小文字が区別されます。 名前は、ポリシー内で一意にする必要があります。 | True |
| `enabled` | Boolean | ルールを一時的に無効にすることを許可する省略可能なブール値。 設定されていない場合、既定値は true です。 | False |
| `type` | 列挙型の値 | 現在の有効な種類は `Lifecycle` です。 | True |
| `definition` | ライフサイクル ルールを定義するオブジェクト | 各定義は、フィルター セットとアクション セットで構成されます。 | True |

## <a name="lifecycle-management-rule-definition"></a>ライフサイクル管理ルールの定義

ポリシー内の各ルール定義には、フィルター セットとアクション セットが含まれます。 [フィルター セット](#rule-filters)は、コンテナー内の特定のオブジェクト セットまたはオブジェクト名にルール アクションを制限します。 [アクション セット](#rule-actions)は、階層化または削除アクションをオブジェクトのフィルター処理されたセットに適用します。

### <a name="sample-rule"></a>ルールのサンプル

次のサンプル ルールは、`sample-container` 内に存在し、`blob1` で始まるオブジェクトに対してアクションを実行するようにアカウントをフィルター処理します。

- BLOB を最後に変更されたときから 30 日後にクール層に階層化する
- BLOB を最後に変更されたときから 90 日後にアーカイブ層に階層化する
- BLOB を最後に変更されたときから 2,555 日 (7 年) 後に削除する
- 以前のバージョンの BLOB を作成から 90 日後に削除する

```json
{
  "rules": [
    {
      "enabled": true,
      "name": "sample-rule",
      "type": "Lifecycle",
      "definition": {
        "actions": {
          "version": {
            "delete": {
              "daysAfterCreationGreaterThan": 90
            }
          },
          "baseBlob": {
            "tierToCool": {
              "daysAfterModificationGreaterThan": 30
            },
            "tierToArchive": {
              "daysAfterModificationGreaterThan": 90
            },
            "delete": {
              "daysAfterModificationGreaterThan": 2555
            }
          }
        },
        "filters": {
          "blobTypes": [
            "blockBlob"
          ],
          "prefixMatch": [
            "sample-container/blob1"
          ]
        }
      }
    }
  ]
}
```

### <a name="rule-filters"></a>ルール フィルター

フィルターを使用すると、ルール アクションをストレージ アカウント内の BLOB のサブセットに限定できます。 複数のフィルターが定義されている場合、すべてのフィルターで論理 `AND` が実行されます。

フィルターには次のものが含まれます。

| フィルター名 | フィルターの種類 | Notes | 必須 |
|-------------|-------------|-------|-------------|
| blobTypes   | 定義済みの列挙型の値の配列。 | 現在のリリースでは `blockBlob` および `appendBlob` をサポートしています。 `appendBlob` では削除のみがサポートされています。既定の層はサポートされていません。 | はい |
| prefixMatch | プレフィックスを照合する文字列の配列。 各ルールで大文字と小文字を区別するプレフィックスを最大 10 個定義できます。 プレフィックス文字列はコンテナー名で始まる必要があります。 たとえば、`https://myaccount.blob.core.windows.net/sample-container/blob1/...` の下にあるすべての BLOB をルールに一致させたい場合、prefixMatch は `sample-container/blob1` です。 | prefixMatch を定義していない場合、ルールはストレージ アカウント内のすべての BLOB に適用されます。 | いいえ |
| blobIndexMatch | 照合する BLOB インデックス タグ キーと値条件で構成されるディクショナリ値の配列。 各ルールには、最大 10 個の BLOB インデックス タグ条件を定義できます。 たとえば、ルールとして `https://myaccount.blob.core.windows.net/` の下にあるすべての BLOB を `Project = Contoso` に一致させたい場合、blobIndexMatch は `{"name": "Project","op": "==","value": "Contoso"}` になります。 | blobIndexMatch を定義していない場合、ルールはストレージ アカウント内のすべての BLOB に適用されます。 | いいえ |

BLOB インデックス機能と、既知の問題および制限事項の詳細については、[BLOB インデックスを使用して Azure Blob Storage でデータを管理および検索する](storage-manage-find-blobs.md)ことに関するページを参照してください。

### <a name="rule-actions"></a>ルールのアクション

実行条件が満たされている場合、アクションはフィルター処理された BLOB に適用されます。

ライフサイクル管理では、BLOB、以前の BLOB バージョン、および BLOB スナップショットの階層化と削除がサポートされています。 ベース BLOB、以前の BLOB バージョン、または BLOB スナップショットの各ルールに対して 1 つ以上のアクションを定義します。

| アクション                      | ベース BLOB                                  | スナップショット      | Version
|-----------------------------|--------------------------------------------|---------------|---------------|
| tierToCool                  | `blockBlob` でサポート                  | サポートされています     | サポートされています     |
| enableAutoTierToHotFromCool | `blockBlob` でサポート                  | サポートされていません | サポートされていません |
| tierToArchive               | `blockBlob` でサポート                  | サポートされています     | サポートされています     |
| delete                      | `blockBlob` および `appendBlob` に対してサポートされています | サポートされています     | サポートされています     |

> [!NOTE]
> 同じ BLOB に複数のアクションを定義した場合、ライフサイクル管理によって最も低コストのアクションが BLOB に適用されます。 たとえば、`delete` アクションは `tierToArchive` アクションよりも低コストです。 `tierToArchive` アクションは `tierToCool` アクションよりも低コストです。

実行条件は、古さに基づいています。 ベース BLOB では、最終変更時刻を使用し、BLOB バージョンではバージョン作成時刻を使用し、BLOB スナップショットでは、スナップショットの作成時刻を使用して古さが追跡されます。

| アクションの実行条件 | 条件値 | 説明 |
|--|--|--|
| daysAfterModificationGreaterThan | 古さを日数で示す整数値 | ベース BLOB のアクションの条件 |
| daysAfterCreationGreaterThan | 古さを日数で示す整数値 | BLOB バージョンおよび BLOB スナップショットのアクションの条件 |
| daysAfterLastAccessTimeGreaterThan | 古さを日数で示す整数値 | アクセス追跡が有効になっているときのベース BLOB アクションの条件 |

## <a name="examples-of-lifecycle-policies"></a>ライフサイクル ポリシーの例

次の例では、ライフサイクル ポリシー ルールを使用して一般的なシナリオに対処する方法を示します。

### <a name="move-aging-data-to-a-cooler-tier"></a>古いデータをよりクールな層に移動する

次の例は、プレフィックス `sample-container/blob1` または `container2/blob2` が付いたブロック BLOB を移行する方法を示しています。 このポリシーでは、30 日以上変更されていない BLOB をクール ストレージに移行し、90 日間変更されていない BLOB をアーカイブ層に移行します。

```json
{
  "rules": [
    {
      "name": "agingRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "sample-container/blob1", "container2/blob2" ]
        },
        "actions": {
          "baseBlob": {
            "tierToCool": { "daysAfterModificationGreaterThan": 30 },
            "tierToArchive": { "daysAfterModificationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

### <a name="move-data-based-on-last-accessed-time"></a>最終アクセス時刻に基づいてデータを移動する

最終アクセス時刻の追跡を有効にすると、BLOB データの階層化と保持を管理するためのフィルターとして、BLOB が最後に読み書きされた時刻の記録を保持することができます。 最終アクセス時刻の追跡を有効にする方法については、「[オプションであるアクセス時間の追跡を有効にする](lifecycle-management-policy-configure.md#optionally-enable-access-time-tracking)」を参照してください。

最終アクセス時刻追跡機能が有効になっている場合、BLOB が読み取られるか書き込まれるときに `LastAccessTime` という名前の BLOB プロパティが更新されます。 [Get Blob](/rest/api/storageservices/get-blob) 操作はアクセス操作と見なされます。 [BLOB プロパティの取得](/rest/api/storageservices/get-blob-properties)、[BLOB メタデータの取得](/rest/api/storageservices/get-blob-metadata)、および [BLOB タグの取得](/rest/api/storageservices/get-blob-tags)は、アクセス操作ではないので、最終アクセス時刻を更新しません。

読み取りアクセス待ち時間への影響を最小限に抑えるために、過去 24 時間の最初の読み取りのみが最終アクセス時刻を更新します。 同じ 24 時間内のその後の読み取りでは、最終アクセス時刻は更新されません。 読み取り間で BLOB が変更された場合、最終アクセス時刻は 2 つの値のうち新しい方になります。

次の例では、BLOB は、30 日間アクセスされない場合に、クール ストレージに移動されます。 `enableAutoTierToHotFromCool` プロパティはブール値であり、BLOB がクールに階層化された後で再びアクセスされた場合に、BLOB を自動的にクールからホットに階層化するかどうかを示します。

```json
{
  "enabled": true,
  "name": "last-accessed-thirty-days-ago",
  "type": "Lifecycle",
  "definition": {
    "actions": {
      "baseBlob": {
        "enableAutoTierToHotFromCool": true,
        "tierToCool": {
          "daysAfterLastAccessTimeGreaterThan": 30
        }
      }
    },
    "filters": {
      "blobTypes": [
        "blockBlob"
      ],
      "prefixMatch": [
        "mylifecyclecontainer/log"
      ]
    }
  }
}
```

### <a name="archive-data-after-ingest"></a>取り込み後にデータをアーカイブする

クラウド内でアイドル状態に留まり、あったとしてもまれにしかアクセスされないデータもあります。 次のライフサイクル ポリシーは、取り込み直後にデータをアーカイブするように構成されます。 この例では、`archivecontainer` という名前のコンテナー内のブロック BLOB をアーカイブ層に移行します。 この移行は、最終変更時刻の 0 日後に BLOB を処理することによって実現されます。

```json
{
  "rules": [
    {
      "name": "archiveRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "archivecontainer" ]
        },
        "actions": {
          "baseBlob": {
              "tierToArchive": { "daysAfterModificationGreaterThan": 0 }
          }
        }
      }
    }
  ]
}

```

> [!NOTE]
> 効率を高めるためには、BLOB をアーカイブ層に直接アップロードすることをお勧めします。 アーカイブ層は、[Put BLOB](/rest/api/storageservices/put-blob) または [Put Block List](/rest/api/storageservices/put-block-list) 操作の *x-ms-access-tier* ヘッダーで指定できます。 *x-ms-access-tier* ヘッダーは、REST バージョン 2018-11-09 以降または最新の BLOB ストレージ クライアント ライブラリでサポートされています。

### <a name="expire-data-based-on-age"></a>古さに基づいてデータを期限切れにする

データの中には、作成後数日または数か月後に失効することが想定されているものがあります。 データを古さに基づいて削除することでデータを期限切れに設定するように、ライフサイクル管理ポリシーを構成できます。 次の例は、365 日より古いすべてのブロック BLOB を削除するポリシーを示しています。

```json
{
  "rules": [
    {
      "name": "expirationRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ]
        },
        "actions": {
          "baseBlob": {
            "delete": { "daysAfterModificationGreaterThan": 365 }
          }
        }
      }
    }
  ]
}
```

### <a name="delete-data-with-blob-index-tags"></a>BLOB インデックス タグを使用してデータを削除する

一部のデータは、削除対象として明示的にマークされている場合のみ有効期限切れになる必要があります。 期限切れにするには、データに BLOB インデックスのキー/値属性でタグ付けして、ライフサイクル管理ポリシーを構成します。 次の例は、`Project = Contoso` でタグ付けされたすべてのブロック BLOB を削除するポリシーを示しています。 BLOB インデックスの詳細については、[BLOB インデックスを使用して Azure Blob Storage でデータを管理および検索する (プレビュー) ](storage-manage-find-blobs.md)ことに関するページを参照してください。

```json
{
    "rules": [
        {
            "enabled": true,
            "name": "DeleteContosoData",
            "type": "Lifecycle",
            "definition": {
                "actions": {
                    "baseBlob": {
                        "delete": {
                            "daysAfterModificationGreaterThan": 0
                        }
                    }
                },
                "filters": {
                    "blobIndexMatch": [
                        {
                            "name": "Project",
                            "op": "==",
                            "value": "Contoso"
                        }
                    ],
                    "blobTypes": [
                        "blockBlob"
                    ]
                }
            }
        }
    ]
}
```

### <a name="manage-versions"></a>バージョンの管理

有効期間全体にわたって定期的に変更およびアクセスされるデータの場合は、BLOB ストレージのバージョン管理を有効にすることで、オブジェクトの以前のバージョンを自動的に管理できます。 ポリシーを作成して、以前のバージョンを階層化または削除することができます。 バージョンの古さは、バージョンの作成時刻を評価することによって決定されます。 このポリシー ルールは、バージョンの作成後 90 日以上経過したコンテナー `activedata` 内の以前のバージョンをクール層に階層化し、365 日またはそれ以前のバージョンを削除します。

```json
{
  "rules": [
    {
      "enabled": true,
      "name": "versionrule",
      "type": "Lifecycle",
      "definition": {
        "actions": {
          "version": {
            "tierToCool": {
              "daysAfterCreationGreaterThan": 90
            },
            "delete": {
              "daysAfterCreationGreaterThan": 365
            }
          }
        },
        "filters": {
          "blobTypes": [
            "blockBlob"
          ],
          "prefixMatch": [
            "activedata"
          ]
        }
      }
    }
  ]
}
```

## <a name="feature-support"></a>機能サポート

次の表は、アカウントでのこの機能のサポートと、特定の機能を有効にした場合のサポートへの影響を示しています。

| ストレージ アカウントの種類 | Blob Storage (既定のサポート) | Data Lake Storage Gen2 <sup>1</sup> | NFS 3.0 <sup>1</sup> | SFTP <sup>1</sup> |
|--|--|--|--|--|
| Standard 汎用 v2 | ![はい](../media/icons/yes-icon.png) |![はい](../media/icons/yes-icon.png)              | ![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png) |
| Premium ブロック BLOB          | ![はい](../media/icons/yes-icon.png)|![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png) |

<sup>1</sup> Data Lake Storage Gen2、ネットワーク ファイル システム (NFS) 3.0 プロトコル、セキュア ファイル転送プロトコル (SFTP) のサポートでは、すべて階層型名前空間が有効になっているストレージ アカウントが必要です。

## <a name="regional-availability-and-pricing"></a>リージョン別の可用性と料金

ライフサイクル管理機能は、すべての Azure リージョンで使用できます。

ライフサイクル管理ポリシーは無料です。 お客様に課金されるのは、[Set Blob Tier](/rest/api/storageservices/set-blob-tier) API 呼び出しの標準的な操作のコストに対してです。 削除操作は無料です。

BLOB の最終アクセス時刻が更新されるたびに、[その他の操作](https://azure.microsoft.com/pricing/details/storage/blobs/)のカテゴリで請求が行われます。

価格の詳細については、「[ブロック BLOBの料金](https://azure.microsoft.com/pricing/details/storage/blobs/)」を参照してください。

## <a name="faq"></a>よく寄せられる質問

**新しいポリシーを作成しましたが、アクションがすぐに実行されないのはなぜですか。**

ライフサイクル ポリシーは、プラットフォームによって 1 日に 1 回実行されます。 ポリシーを構成した後、アクションによっては、初回実行時に最大 24 時間かかる場合があります。

**既存のポリシーを更新した場合、アクションの実行にはどのくらいの時間がかかりますか。**

更新されたポリシーは、有効になるまで最大 24 時間かかります。 ポリシーが有効になると、アクションが実行されるまでに最大で 24 時間かかることがあります。 このため、ポリシーのアクションが完了するまでに最大 48 時間かかる可能性があります。 更新によってルールが無効になるか、削除されるとき、enableAutoTierToHotFromCool が使用された場合でも、ホット層に自動的に階層化されます。 たとえば、最後のアクセスに基づき、enableAutoTierToHotFromCool を含むルールが設定されます。 ルールが無効になるか、削除されるとき、BLOB が現在クール層にあり、その後アクセスされる場合、ホット層に戻ります。それがライフサイクル管理外のアクセスで適用されるためです。 ライフサイクル管理ルールが無効になっているか、削除されているため、BLOB がホット層からクール層に移動することはありません。 autoTierToHotFromCool を回避する唯一の方法は、最終アクセス時刻の追跡をオフにすることです。

**アーカイブ済み BLOB を手動でリハイドレートしました。これが一時的にアーカイブ層に戻されないようにするにはどうすればよいですか。**

あるアクセス層から別のアクセス層に BLOB が移動されても、その BLOB の最終変更時刻は変わりません。 アーカイブ済み BLOB を手動でホット層にリハイドレートすると、その BLOB は、ライフサイクル管理エンジンによりアーカイブ層に戻されます。 この BLOB に影響を与えるルールを一時的に無効にして、再びアーカイブされないようにします。 BLOB が安全にアーカイブ層に移動されたら、ルールを再度有効にします。 BLOB を別の場所にコピーして、ホット層またはクール層から永続的に離れないようにすることもできます。

## <a name="next-steps"></a>次のステップ

- [ライフサイクル管理ポリシーを構成する](lifecycle-management-policy-configure.md)
- [BLOB データのホット、クール、アーカイブ アクセス層](access-tiers-overview.md)
- [BLOB インデックスを使用して Azure Blob Storage でデータを管理および検索する](storage-manage-find-blobs.md)
