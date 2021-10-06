---
title: Azure Purview コレクションのメタデータ ポリシーおよびロール API のクイックスタート - Purview コレクションのロールベースのアクセス制御を管理する
description: このチュートリアルでは、Azure Purview API を介して、企業内のユーザー、グループ、またはサービス プリンシパルに対するこれらのコレクションのロールベースのアクセス制御を管理する方法について説明します。
author: abandyop
ms.author: arindamba
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 09/24/2021
ms.openlocfilehash: 52c38e40d96b6d6f650a8b57fe2d5826a9a81842
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2021
ms.locfileid: "129219685"
---
# <a name="tutorial-use-rest-apis-to-manage-role-based-access-control-rbac-on-azure-purview-collections"></a>チュートリアル: REST API を使用して、Azure Purview コレクションでのロールベースのアクセス制御 (RBAC) を管理する 

2021 年 8 月、Purview のアクセス制御は Azure IAM (コントロール プレーン) から [Azure Purview コレクション](how-to-create-and-manage-collections.md) (データ プレーン) に移動しました。 この変更により、エンタープライズ データ キュレーターと管理者は、Purview によってスキャンされたデータ ソースに対してより正確できめ細かなアクセス制御を行うことができ、組織はデータの適切なアクセスと適切な使用を監査できます。

このチュートリアルでは、 **[Azure Purview メタデータ ポリシー API](/rest/api/purview/metadatapolicydataplane/Metadata-Policy.yml)** を使用して、ユーザー、グループ、またはサービス プリンシパルをコレクションに追加し、そのコレクション内でそれらのロールを管理または削除する詳細な手順について説明します。 REST API は、Azure portal または Purview Studio の代わりに使用して、同じきめ細かなロールベースのアクセス制御を実現することができます。

[Purview のアクセス許可ガイド](catalog-permissions.md#roles)では、Azure Purview の各組み込みロールについて詳細に説明されており、ユーザーに付与されたアクセス許可のレベルにロールをマップします。


## <a name="metadata-policy-api-reference-summary"></a>メタデータ ポリシー API リファレンスの概要
次の表は、 **[Azure Purview メタデータ ポリシー API リファレンス](/rest/api/purview/metadatapolicydataplane/Metadata-Policy.yml)** の概要を示しています。 これらの API を実行する前に、{pv-acc-name} をお使いの Purview アカウントに置き換えてください。 たとえば、お使いの Purview アカウント名が "FabrikamPurviewAccount" である場合、API エンドポイントは "FabrikamPurviewAccount.purview.azure.com" になります

|**API 関数**|**REST メソッド**|**API エンドポイント**|**説明**|
|:-|:-|:-|:-|
|すべてのメタデータ ロールの読み取り|GET|https://{pv-acc-name}.purview.azure.com /policystore/metadataroles?&api-version=2021-07-01| Purview アカウントからすべてのメタデータ ロールを読み取ります|
|コレクション名によるメタデータ ポリシーの読み取り|GET|https://{pv-acc-name}.purview.azure.com /policystore/collections/{collectionName}/metadataPolicy?&api-version=2021-07-01| 指定されたコレクション名 (ポリシーの作成時に Purview によって生成される 6 文字のランダムな名前) で MetadataPolicy を読み取ります|
|PolicyID によるメタデータ ポリシーの読み取り|GET|https://{pv-acc-name}.purview.azure.com /policystore/metadataPolicies/{policyId}?&api-version=2021-07-01| 指定された PolicyID で MetadataPolicy を読み取ります。 ポリシー ID は GUID 形式です。|
|すべてのメタデータ ポリシーの読み取り|GET|https://{pv-acc-name}.purview.azure.com /policystore/metadataPolicies?&api-version=2021-07-01| Purview アカウントからすべてのメタデータ ポリシーを読み取ります。 この API によって生成される JSON 出力リストから、使用する特定のポリシーを選択できます。|
|メタデータ ポリシーの更新または PUT|PUT|https://{pv-acc-name}.purview.azure.com /policystore/metadataPolicies/{policyId}?&api-version=2021-07-01| 指定された PolicyID で MetadataPolicy を更新します。 ポリシー ID は GUID 形式です。|

## <a name="purview-catalog-collections-api-reference-summary"></a>Purview カタログ コレクション API リファレンスの概要
次の表は、**Purview コレクション API** の概要を示しています。 各 API の詳細なドキュメントを取得するには、以下の各 API の"操作" をクリックします。

| **操作** | **説明** |
|:-|:-|
| [コレクションの作成または更新](/rest/api/purview/accountdataplane/collections/create-or-update-collection) | コレクション エンティティを作成または更新します。 |
| [コレクションの削除](/rest/api/purview/accountdataplane/collections/delete-collection) |コレクション エンティティを削除します。 |
| [コレクションの取得](/rest/api/purview/accountdataplane/collections/get-collection) |1 つのコレクションを取得する|
| [コレクション パスの取得](/rest/api/purview/accountdataplane/collections/get-collection-path) |コレクション パスを表す親名と表示名チェーンを取得します。|
| [子コレクション名の一覧表示](/rest/api/purview/accountdataplane/collections/list-child-collection-names) |コレクション内の子コレクション名を一覧表示します。|
| [コレクションの一覧表示](/rest/api/purview/accountdataplane/collections/list-collections) |アカウント内のコレクションを一覧表示します。|


- API を使用する場合、API を正常に実行するには、Purview で、その API を実行するサービス プリンシパル (SP)、ユーザーまたはグループに[コレクション管理者](how-to-create-and-manage-collections.md#check-permissions) ロールが割り当てられている必要があります。
- {collectionName} を必要とするすべての Purview API では、"name" ("friendlyName" ではありません) が必要です。 {collectionName} を実際のコレクション名文字列 (6 桁の英数字) に置き換えます。 これは、コレクションの作成時に指定するフレンドリ表示名とは異なることに注意してください。 この {collectionName} が手元にない場合は、[コレクションの一覧表示](/rest/api/purview/accountdataplane/collections/list-collections) API を使用して、JSON 出力から 6 文字のコレクション名を選択します。
- サンプル JSON: 

```json
{
    "name": "74dhe7", 
    "friendlyName": "Friendly Name",
    "parentCollection": {
        "type": "CollectionReference",
        "referenceName": "{your_purview_account_name}"
    },
    "systemData": {
        "createdBy": "{guid}",
        "createdByType": "Application",
        "createdAt": "2021-08-26T21:21:51.2646627Z",
        "lastModifiedBy": "7f8d47e2-330c-42f0-8744-fcfb1ecb3ea0",
        "lastModifiedByType": "Application",
        "lastModifiedAt": "2021-08-26T21:21:51.2646628Z"
    },
    "collectionProvisioningState": "Succeeded"
}
```

### <a name="policy-json-description"></a>ポリシー JSON の説明
コレクション API から受け取った JSON 出力の一部の重要な識別子の詳細。

**Name**: ポリシーの名前。 

**Id**: ポリシーの一意識別子

**Version**: ポリシーの最新バージョン番号。 \*\*(バージョン番号は、Update-Metadata-Policy API が呼び出されるたびに増分されます。 必ず、Get-Policy-by-Policy-ID API を呼び出して、ポリシーの最新のコピーをフェッチしてください。 この更新は、ポリシーの更新 (PUT) API を呼び出す前に毎回実行する必要があるため、常に最新バージョンの JSON を使用できます)

**DecisionRules**: このポリシーの規則と効果を一覧表示します。 メタデータ ポリシーの場合、効果は常に "許可" です。

## <a name="use-purview-rest-apis-to-add-or-remove-usergroupserviceprincipal-to-a-collection-or-role"></a>Purview REST API を使用して、コレクションまたはロールでユーザー、グループまたはサービス プリンシパルを追加または削除する
API の詳細な使用方法は、サンプル JSON 出力と共に提供されます。 Purview メタデータ ポリシー API を最もよく理解するために、以下の手順を順番に実行することを強くお勧めします。

## <a name="get-all-metadata-roles"></a>すべてのメタデータ ロールの取得
```ruby
GET https://{your_purview_account_name}.purview.azure.com/policystore/metadataroles?api-version=2021-07-01
```
使用可能なすべてのメタデータ アクセス許可ロールを一覧表示します。

出力 JSON では、ロールとそれに関連付けられたアクセス許可をこの形式で記述します。

### <a name="default-metadata-roles"></a>既定のメタデータ ロール

|**ロール ID**|**アクセス許可**|**ロールの説明**|
|:-|:-|:-|
|purviewmetadatarole\_builtin\_data-source-administrator|Microsoft.Purview/accounts/scan/read Microsoft.Purview/accounts/scan/write Microsoft.Purview/accounts/collection/read|コレクションの読み取り、書き込み、データソースの登録、スキャンのトリガーを行うためのアクセスを他のユーザーに許可します。|
|purviewmetadatarole\_builtin\_collection-administrator|Microsoft.Purview/accounts/collection/read Microsoft.Purview/accounts/collection/write|ユーザーと SPN のコレクションへの追加、削除、管理権限、アクセスの許可、取り消しなど、コレクション全体への管理者レベルのフル アクセス。 場合によっては、コレクション管理者がコレクションの作成者と異なる可能性があります。|
|purviewmetadatarole\_builtin\_purview-reader|Microsoft.Purview/accounts/data/read Microsoft.Purview/accounts/collection/read|スキャン バインディングを除き、データ処理とすべてのメタデータ (分類、秘密度ラベル、分析情報、コレクション内の資産の読み取り) への読み取りアクセスのみを許可します。|
|purviewmetadatarole\_builtin\_data-curator|Microsoft.Purview/accounts/data/read Microsoft.Purview/accounts/data/write Microsoft.Purview/accounts/collection/read|スキャン バインディングを除き、データ処理とすべてのメタデータ (分類、秘密度ラベル、分析情報、コレクション内の資産の読み取り) へのフル アクセスを許可します。|
|purviewmetadatarole\_builtin\_data-share-contributor|Microsoft.Purview/accounts/share/read Microsoft.Purview/accounts/share/write|共同作成者としてデータ共有へのアクセスを許可します|

```json
{
  "values": [
    {
      "id": "purviewmetadatarole_builtin_data-curator",
      "name": "data-curator",
      "type": "Microsoft.Purview/role",
      "properties": {
        "provisioningState": "Provisioned",
        "roleType": "BuiltIn",
        "friendlyName": "Data Curator",
        "cnfCondition": [
          [
            {
              "attributeName": "request.azure.dataAction",
              "attributeValueIncludedIn": [
                "Microsoft.Purview/accounts/data/read",
                "Microsoft.Purview/accounts/data/write",
                "Microsoft.Purview/accounts/collection/read"
              ]
            }
          ]
        ],
        "version": 1
      }
    },
    {
      "id": "purviewmetadatarole_builtin_data-source-administrator",
      "name": "data-source-administrator",
      "type": "Microsoft.Purview/role",
      "properties": {
        "provisioningState": "Provisioned",
        "roleType": "BuiltIn",
        "friendlyName": "Data Source Administrator",
        "cnfCondition": [
          [
            {
              "attributeName": "request.azure.dataAction",
              "attributeValueIncludedIn": [
                "Microsoft.Purview/accounts/scan/read",
                "Microsoft.Purview/accounts/scan/write",
                "Microsoft.Purview/accounts/collection/read"
              ]
            }
          ]
        ],
        "version": 1
      }
    },
    {
      "id": "purviewmetadatarole_builtin_collection-administrator",
      "name": "collection-administrator",
      "type": "Microsoft.Purview/role",
      "properties": {
        "provisioningState": "Provisioned",
        "roleType": "BuiltIn",
        "friendlyName": "Collection Administrator",
        "cnfCondition": [
          [
            {
              "attributeName": "request.azure.dataAction",
              "attributeValueIncludedIn": [
                "Microsoft.Purview/accounts/collection/read",
                "Microsoft.Purview/accounts/collection/write"
              ]
            }
          ]
        ],
        "version": 1
      }
    },
    {
      "id": "purviewmetadatarole_builtin_purview-reader",
      "name": "purview-reader",
      "type": "Microsoft.Purview/role",
      "properties": {
        "provisioningState": "Provisioned",
        "roleType": "BuiltIn",
        "friendlyName": "Purview Reader",
        "cnfCondition": [
          [
            {
              "attributeName": "request.azure.dataAction",
              "attributeValueIncludedIn": [
                "Microsoft.Purview/accounts/data/read",
                "Microsoft.Purview/accounts/collection/read"
              ]
            }
          ]
        ],
        "version": 1
      }
    },
    {
      "id": "purviewmetadatarole_builtin_data-share-contributor",
      "name": "data-share-contributor",
      "type": "Microsoft.Purview/role",
      "properties": {
        "provisioningState": "Provisioned",
        "roleType": "BuiltIn",
        "friendlyName": "Data share contributor",
        "cnfCondition": [
          [
            {
              "attributeName": "request.azure.dataAction",
              "attributeValueIncludedIn": [
                "Microsoft.Purview/accounts/share/read",
                "Microsoft.Purview/accounts/share/write"
              ]
            }
          ]
        ],
        "version": 1
      }
    }
  ]
}
```

## <a name="get-all-metadata-policies"></a>すべてのメタデータ ポリシーを取得する
```ruby
GET https://{your_purview_account_name}.purview.azure.com/policystore/metadataPolicies?api-version=2021-07-01
```
ルート コレクションから始まるコレクション階層全体で使用可能なすべてのメタデータ ポリシーと、そのすべての子ポリシーをツリー形式で一覧表示します。 階層は、最上位のルート コレクションで始まり、その後に子コレクションが続きます。 各子コレクションには、次のレベルの各子がカプセル化されます。
例:

```json
{
  "values": [
    {
      "name": "policy_FabrikamPurview",
      "id": "9b2f1cb9-584c-4a16-811e-9232884b5cac",
      "version": 30,
      "properties": {
        "description": "",
        "decisionRules": [
          {
            "kind": "decisionrule",
            "effect": "Permit",
            "dnfCondition": [
              [
                {
                  "attributeName": "resource.purview.collection",
                  "attributeValueIncludes": "fabrikampurview"
                },
                {
                  "fromRule": "permission:fabrikampurview",
                  "attributeName": "derived.purview.permission",
                  "attributeValueIncludes": "permission:fabrikampurview"
                }
              ]
            ]
          }
        ],
        "attributeRules": [
          {
            "kind": "attributerule",
            "id": "purviewmetadatarole_builtin_collection-administrator:fabrikampurview",
            "name": "purviewmetadatarole_builtin_collection-administrator:fabrikampurview",
            "dnfCondition": [
              [
                {
                  "attributeName": "principal.microsoft.id",
                  "attributeValueIncludedIn": [
                    "2f656762-e440-4b62-9eb6-a991d17d64b0",
                    "04314867-60a4-4e5a-ae16-8e5856f415d9",
                    "8988fe5c-5736-4179-9435-0a64c273b90b",
                    "6d563253-1d5b-48f2-baaa-5489f22ddce9",
                    "26f98046-5b02-4fa9-b709-e0519c658891",
                    "73fc02dc-becd-468b-a2a3-82238e722dae"
                  ]
                },
                {
                  "fromRule": "purviewmetadatarole_builtin_collection-administrator",
                  "attributeName": "derived.purview.role",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator"
                }
              ],
              [
                {
                  "fromRule": "purviewmetadatarole_builtin_collection-administrator",
                  "attributeName": "derived.purview.role",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator"
                },
                {
                  "attributeName": "principal.microsoft.groups",
                  "attributeValueIncludedIn": [
                    "ffd851fa-86ec-431b-95ea-8b84d5012383",
                    "cf84b126-4384-4952-91f1-7f705b25e569",
                    "5046aba1-5b81-411c-8fec-b84600f3f08b",
                    "b055a5c6-a04e-4d1a-8524-001ad81bfb28",
                    "cc194892-92fa-4ce3-96ae-1f98bef8211c"
                  ]
                }
              ]
            ]
          },
          {
            "kind": "attributerule",
            "id": "purviewmetadatarole_builtin_data-curator:fabrikampurview",
            "name": "purviewmetadatarole_builtin_data-curator:fabrikampurview",
            "dnfCondition": [
              [
                {
                  "attributeName": "principal.microsoft.id",
                  "attributeValueIncludedIn": [
                    "2f656762-e440-4b62-9eb6-a991d17d64b0",
                    "649f56ab-2dd2-40de-a731-3d3f28e7af92",
                    "c29a5809-f9ec-49fd-b762-2d4d64abb93e",
                    "04314867-60a4-4e5a-ae16-8e5856f415d9",
                    "73fc02dc-becd-468b-a2a3-82238e722dae",
                    "517a27d2-39ba-4c91-a032-dd9ecf8ad6f1",
                    "6d563253-1d5b-48f2-baaa-5489f22ddce9"
                  ]
                },
                {
                  "fromRule": "purviewmetadatarole_builtin_data-curator",
                  "attributeName": "derived.purview.role",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_data-curator"
                }
              ],
              [
                {
                  "fromRule": "purviewmetadatarole_builtin_data-curator",
                  "attributeName": "derived.purview.role",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_data-curator"
                },
                {
                  "attributeName": "principal.microsoft.groups",
                  "attributeValueIncludedIn": [
                    "b055a5c6-a04e-4d1a-8524-001ad81bfb28",
                    "cc194892-92fa-4ce3-96ae-1f98bef8211c",
                    "5046aba1-5b81-411c-8fec-b84600f3f08b"
                  ]
                }
              ]
            ]
          },
          {
            "kind": "attributerule",
            "id": "purviewmetadatarole_builtin_data-source-administrator:fabrikampurview",
            "name": "purviewmetadatarole_builtin_data-source-administrator:fabrikampurview",
            "dnfCondition": [
              [
                {
                  "attributeName": "principal.microsoft.id",
                  "attributeValueIncludedIn": [
                    "2f656762-e440-4b62-9eb6-a991d17d64b0",
                    "04314867-60a4-4e5a-ae16-8e5856f415d9",
                    "517a27d2-39ba-4c91-a032-dd9ecf8ad6f1",
                    "6d563253-1d5b-48f2-baaa-5489f22ddce9"
                  ]
                },
                {
                  "fromRule": "purviewmetadatarole_builtin_data-source-administrator",
                  "attributeName": "derived.purview.role",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_data-source-administrator"
                }
              ],
              [
                {
                  "fromRule": "purviewmetadatarole_builtin_data-source-administrator",
                  "attributeName": "derived.purview.role",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_data-source-administrator"
                },
                {
                  "attributeName": "principal.microsoft.groups",
                  "attributeValueIncludedIn": [
                    "b055a5c6-a04e-4d1a-8524-001ad81bfb28",
                    "cc194892-92fa-4ce3-96ae-1f98bef8211c",
                    "d34eb741-be5e-4098-90d7-eca8d4a5153f",
                    "664ec992-9af0-4773-88f2-dc39edc46f6f",
                    "5046aba1-5b81-411c-8fec-b84600f3f08b"
                  ]
                }
              ]
            ]
          },
          {
            "kind": "attributerule",
            "id": "permission:fabrikampurview",
            "name": "permission:fabrikampurview",
            "dnfCondition": [
              [
                {
                  "fromRule": "purviewmetadatarole_builtin_collection-administrator:fabrikampurview",
                  "attributeName": "derived.purview.permission",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator:fabrikampurview"
                }
              ],
              [
                {
                  "fromRule": "purviewmetadatarole_builtin_purview-reader:fabrikampurview",
                  "attributeName": "derived.purview.permission",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_purview-reader:fabrikampurview"
                }
              ],
              [
                {
                  "fromRule": "purviewmetadatarole_builtin_data-curator:fabrikampurview",
                  "attributeName": "derived.purview.permission",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_data-curator:fabrikampurview"
                }
              ],
              [
                {
                  "fromRule": "purviewmetadatarole_builtin_data-source-administrator:fabrikampurview",
                  "attributeName": "derived.purview.permission",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_data-source-administrator:fabrikampurview"
                }
              ]
            ]
          }
        ],
        "collection": {
          "type": "CollectionReference",
          "referenceName": "fabrikampurview"
        }
      }
    },
    {
      "name": "policy_b2zpf1",
      "id": "12b0bb28-2acc-413e-8fe1-179ff9cc54c3",
      "version": 0,
      "properties": {
        "description": "",
        "decisionRules": [
          {
            "kind": "decisionrule",
            "effect": "Permit",
            "dnfCondition": [
              [
                {
                  "attributeName": "resource.purview.collection",
                  "attributeValueIncludes": "b2zpf1"
                },
                {
                  "fromRule": "permission:b2zpf1",
                  "attributeName": "derived.purview.permission",
                  "attributeValueIncludes": "permission:b2zpf1"
                }
              ]
            ]
          }
        ],
        "attributeRules": [
          {
            "kind": "attributerule",
            "id": "purviewmetadatarole_builtin_collection-administrator:b2zpf1",
            "name": "purviewmetadatarole_builtin_collection-administrator:b2zpf1",
            "dnfCondition": [
              [
                {
                  "attributeName": "principal.microsoft.id",
                  "attributeValueIncludedIn": [
                    "2f656762-e440-4b62-9eb6-a991d17d64b0"
                  ]
                },
                {
                  "fromRule": "purviewmetadatarole_builtin_collection-administrator",
                  "attributeName": "derived.purview.role",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator"
                }
              ],
              [
                {
                  "fromRule": "purviewmetadatarole_builtin_collection-administrator:ukx7pq",
                  "attributeName": "derived.purview.permission",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator:ukx7pq"
                }
              ]
            ]
          },
          {
            "kind": "attributerule",
            "id": "permission:b2zpf1",
            "name": "permission:b2zpf1",
            "dnfCondition": [
              [
                {
                  "fromRule": "purviewmetadatarole_builtin_collection-administrator:b2zpf1",
                  "attributeName": "derived.purview.permission",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator:b2zpf1"
                }
              ],
              [
                {
                  "fromRule": "permission:ukx7pq",
                  "attributeName": "derived.purview.permission",
                  "attributeValueIncludes": "permission:ukx7pq"
                }
              ]
            ]
          }
        ],
        "collection": {
          "type": "CollectionReference",
          "referenceName": "b2zpf1"
        },
        "parentCollectionName": "ukx7pq"
      }
    },
    {
      "name": "policy_7wte2n",
      "id": "a72084e4-ccab-4aec-a364-08ab001e4999",
      "version": 0,
      "properties": {
        "description": "",
        "decisionRules": [
          {
            "kind": "decisionrule",
            "effect": "Permit",
            "dnfCondition": [
              [
                {
                  "attributeName": "resource.purview.collection",
                  "attributeValueIncludes": "7wte2n"
                },
                {
                  "fromRule": "permission:7wte2n",
                  "attributeName": "derived.purview.permission",
                  "attributeValueIncludes": "permission:7wte2n"
                }
              ]
            ]
          }
        ],
        "attributeRules": [
          {
            "kind": "attributerule",
            "id": "purviewmetadatarole_builtin_collection-administrator:7wte2n",
            "name": "purviewmetadatarole_builtin_collection-administrator:7wte2n",
            "dnfCondition": [
              [
                {
                  "attributeName": "principal.microsoft.id",
                  "attributeValueIncludedIn": [
                    "2f656762-e440-4b62-9eb6-a991d17d64b0"
                  ]
                },
                {
                  "fromRule": "purviewmetadatarole_builtin_collection-administrator",
                  "attributeName": "derived.purview.role",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator"
                }
              ],
              [
                {
                  "fromRule": "purviewmetadatarole_builtin_collection-administrator:ukx7pq",
                  "attributeName": "derived.purview.permission",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator:ukx7pq"
                }
              ]
            ]
          },
          {
            "kind": "attributerule",
            "id": "permission:7wte2n",
            "name": "permission:7wte2n",
            "dnfCondition": [
              [
                {
                  "fromRule": "purviewmetadatarole_builtin_collection-administrator:7wte2n",
                  "attributeName": "derived.purview.permission",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator:7wte2n"
                }
              ],
              [
                {
                  "fromRule": "permission:ukx7pq",
                  "attributeName": "derived.purview.permission",
                  "attributeValueIncludes": "permission:ukx7pq"
                }
              ]
            ]
          }
        ],
        "collection": {
          "type": "CollectionReference",
          "referenceName": "7wte2n"
        },
        "parentCollectionName": "ukx7pq"
      }
    }
  ]
}
```

## <a name="get-selected-metadata-policy"></a>選択したメタデータ ポリシーを取得する 
特定のコレクションのメタデータ ポリシーの JSON 構造をフェッチするには、2 つの API があり、{collectionName} または {PolicyID} を指定します。
どちらの API (次の 2 つのセクションで説明します) も同じ目的を果たし、両方の JSON 出力はまったく同じです。

### <a name="get-metadatapolicy-of-the-collection-by-collectionname"></a>collectionName でコレクションの metadataPolicy を取得する
```ruby
GET https://{your_purview_account_name}.purview.azure.com/policystore/collections/{collectionName}/metadataPolicy?api-version=2021-07-01
```
1. Purview アカウント名は、{your_purview_account_name} です。 これをお使いの Purview アカウント名に置き換えます。
1. 前の "すべてのメタデータ ポリシーの取得" API の JSON 出力で、セクション { "type": "CollectionReference", "referenceName": "7xkdg2"} を探します
1. API URL の "{collectionName}" を "referenceName": "{6-char-collection-name}" の値に置き換えます。 したがって、6-character-collection-name が "7xkdg2" である場合、API URL は https://{your_purview_account_name}.purview.azure.com/policystore/collections/7xkdg2/metadataPolicy?api-version=2021-07-01 のようになります
1. 次に、この API を実行します。 

```json
{
  "name": "policy_qu45fs",
  "id": "c6639bb2-9c41-4be0-912b-775750e725de",
  "version": 0,
  "properties": {
    "description": "",
    "decisionRules": [
      {
        "kind": "decisionrule",
        "effect": "Permit",
        "dnfCondition": [
          [
            {
              "attributeName": "resource.purview.collection",
              "attributeValueIncludes": "qu45fs"
            },
            {
              "fromRule": "permission:qu45fs",
              "attributeName": "derived.purview.permission",
              "attributeValueIncludes": "permission:qu45fs"
            }
          ]
        ]
      }
    ],
    "attributeRules": [
      {
        "kind": "attributerule",
        "id": "purviewmetadatarole_builtin_collection-administrator:qu45fs",
        "name": "purviewmetadatarole_builtin_collection-administrator:qu45fs",
        "dnfCondition": [
          [
            {
              "attributeName": "principal.microsoft.id",
              "attributeValueIncludedIn": [
                "2f656762-e440-4b62-9eb6-a991d17d64b0"
              ]
            },
            {
              "fromRule": "purviewmetadatarole_builtin_collection-administrator",
              "attributeName": "derived.purview.role",
              "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator"
            }
          ],
          [
            {
              "fromRule": "purviewmetadatarole_builtin_collection-administrator:fabrikampurview",
              "attributeName": "derived.purview.permission",
              "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator:fabrikampurview"
            }
          ]
        ]
      },
      {
        "kind": "attributerule",
        "id": "permission:qu45fs",
        "name": "permission:qu45fs",
        "dnfCondition": [
          [
            {
              "fromRule": "purviewmetadatarole_builtin_collection-administrator:qu45fs",
              "attributeName": "derived.purview.permission",
              "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator:qu45fs"
            }
          ],
          [
            {
              "fromRule": "permission:fabrikampurview",
              "attributeName": "derived.purview.permission",
              "attributeValueIncludes": "permission:fabrikampurview"
            }
          ]
        ]
      }
    ],
    "collection": {
      "type": "CollectionReference",
      "referenceName": "qu45fs"
    },
    "parentCollectionName": "fabrikampurview"
  }
}
```

### <a name="get-metadatapolicy-of-the-collection-by-policyid"></a>policyID でコレクションの metadataPolicy を取得する
```ruby
GET https://{your_purview_account_name}.purview.azure.com/policystore/metadataPolicies/{policyId}?api-version=2021-07-01
```
1. Purview アカウント名は、{your_purview_account_name} です。 お使いの Purview アカウント名に置き換えます。
1. 前の "すべてのメタデータ ポリシーの取得" API の JSON 出力で、セクション {.... "name": "policy_qu45fs", "id": "{policy-guid}", "version": N ....} を探します
1. API URL の "{policyId}" を "id" の値に置き換えます。 したがって、"{policy-guid}" が "c6639bb2-9c41-4be0-912b-775750e725de" である場合、API URL は、 https://{your_purview_account_name}.purview.azure.com/policystore/metadataPolicies/c6639bb2-9c41-4be0-912b-775750e725de?api-version=2021-07-01 のようになります
1. 次に、この API を実行します。 この API 呼び出しと前の API 呼び出しの出力が同じであることに注意してください。 このチュートリアルで前述したように、どちらか 1 つを選択できます。

```json
{
  "name": "policy_qu45fs",
  "id": "c6639bb2-9c41-4be0-912b-775750e725de",
  "version": 0,
  "properties": {
    "description": "",
    "decisionRules": [
      {
        "kind": "decisionrule",
        "effect": "Permit",
        "dnfCondition": [
          [
            {
              "attributeName": "resource.purview.collection",
              "attributeValueIncludes": "qu45fs"
            },
            {
              "fromRule": "permission:qu45fs",
              "attributeName": "derived.purview.permission",
              "attributeValueIncludes": "permission:qu45fs"
            }
          ]
        ]
      }
    ],
    "attributeRules": [
      {
        "kind": "attributerule",
        "id": "purviewmetadatarole_builtin_collection-administrator:qu45fs",
        "name": "purviewmetadatarole_builtin_collection-administrator:qu45fs",
        "dnfCondition": [
          [
            {
              "attributeName": "principal.microsoft.id",
              "attributeValueIncludedIn": [
                "2f656762-e440-4b62-9eb6-a991d17d64b0"
              ]
            },
            {
              "fromRule": "purviewmetadatarole_builtin_collection-administrator",
              "attributeName": "derived.purview.role",
              "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator"
            }
          ],
          [
            {
              "fromRule": "purviewmetadatarole_builtin_collection-administrator:fabrikampurview",
              "attributeName": "derived.purview.permission",
              "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator:fabrikampurview"
            }
          ]
        ]
      },
      {
        "kind": "attributerule",
        "id": "permission:qu45fs",
        "name": "permission:qu45fs",
        "dnfCondition": [
          [
            {
              "fromRule": "purviewmetadatarole_builtin_collection-administrator:qu45fs",
              "attributeName": "derived.purview.permission",
              "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator:qu45fs"
            }
          ],
          [
            {
              "fromRule": "permission:fabrikampurview",
              "attributeName": "derived.purview.permission",
              "attributeValueIncludes": "permission:fabrikampurview"
            }
          ]
        ]
      }
    ],
    "collection": {
      "type": "CollectionReference",
      "referenceName": "qu45fs"
    },
    "parentCollectionName": "fabrikampurview"
  }
}
```


## <a name="update-policy-addremove-usergroup-from-collection"></a>ポリシーの更新: コレクションでユーザーまたはグループを追加または削除する
```ruby
PUT https://{your_purview_account_name}.purview.azure.com/policystore/metadataPolicies/{policyId}?api-version=2021-07-01
```

この手順では、前の手順で取得したポリシー JSON を更新し、PUT REST メソッドを使用して、それを Purview サービスにプッシュします。
ユーザー、グループ、または SP (サービス プリンシパル) の **追加** または **削除** のいずれを実行するかにかかわらず、同じ API プロセスに従います。

1. JSON の "attributeValueIncludedIn" 配列で、ユーザー、グループ、サービス プリンシパルのオブジェクト ID {guid} を指定します。
1. Get-Policy-by-ID API の JSON 出力で、前の手順の "attributeValueIncludedIn" 配列を探して、この配列でユーザー、グループ、サービス プリンシパルのオブジェクト ID を **追加** または **削除** します。 ユーザーまたはグループのオブジェクト ID をフェッチする方法がわからない場合は、チュートリアル「[Get-AzureADUser](/powershell/module/azuread/get-azureaduser)」を参照してください
1. JSON には、4 つのロールそれぞれにマップされた複数のセクションがあることに注意してください。 コレクション管理者のアクセス許可ロールの場合、"purviewmetadatarole_builtin_collection-administrator" という名前の "ID" を持つセクションを使用します。 同様に、他のロールの場合も対応するセクションを使用します。
1. 追加または削除の操作をより深く理解するには、前の API からの JSON 出力と次の JSON 出力の違いを注意深く調べます。 次の JSON 出力では、"3a3a3a3a-2c2c-4b4b-1c1c-2a3b4c5d6e7f" というユーザー ID がコレクション管理者として追加されていることがわかります。

```json
{
  "name": "policy_qu45fs",
  "id": "c6639bb2-9c41-4be0-912b-775750e725de",
  "version": 0,
  "properties": {
    "description": "",
    "decisionRules": [
      {
        "kind": "decisionrule",
        "effect": "Permit",
        "dnfCondition": [
          [
            {
              "attributeName": "resource.purview.collection",
              "attributeValueIncludes": "qu45fs"
            },
            {
              "fromRule": "permission:qu45fs",
              "attributeName": "derived.purview.permission",
              "attributeValueIncludes": "permission:qu45fs"
            }
          ]
        ]
      }
    ],
    "attributeRules": [
      {
        "kind": "attributerule",
        "id": "purviewmetadatarole_builtin_collection-administrator:qu45fs",
        "name": "purviewmetadatarole_builtin_collection-administrator:qu45fs",
        "dnfCondition": [
          [
            {
              "attributeName": "principal.microsoft.id",
              "attributeValueIncludedIn": [
                "2f656762-e440-4b62-9eb6-a991d17d64b0",
                "3a3a3a3a-2c2c-4b4b-1c1c-2a3b4c5d6e7f"
              ]
            },
            {
              "fromRule": "purviewmetadatarole_builtin_collection-administrator",
              "attributeName": "derived.purview.role",
              "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator"
            }
          ],
          [
            {
              "fromRule": "purviewmetadatarole_builtin_collection-administrator:fabrikampurview",
              "attributeName": "derived.purview.permission",
              "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator:fabrikampurview"
            }
          ]
        ]
      },
      {
        "kind": "attributerule",
        "id": "permission:qu45fs",
        "name": "permission:qu45fs",
        "dnfCondition": [
          [
            {
              "fromRule": "purviewmetadatarole_builtin_collection-administrator:qu45fs",
              "attributeName": "derived.purview.permission",
              "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator:qu45fs"
            }
          ],
          [
            {
              "fromRule": "permission:fabrikampurview",
              "attributeName": "derived.purview.permission",
              "attributeValueIncludes": "permission:fabrikampurview"
            }
          ]
        ]
      }
    ],
    "collection": {
      "type": "CollectionReference",
      "referenceName": "qu45fs"
    },
    "parentCollectionName": "fabrikampurview"
  }
}
```
## <a name="add-root-collection-admin"></a>ルート コレクション管理者を追加する
既定では、Purview アカウントを作成したユーザーがルート コレクション管理者 (コレクション階層の最上位レベルの管理者) です。 しかし、場合によっては、組織は API を使用してルート コレクション管理者を変更する必要があります。 たとえば、現在のルート コレクション管理者が組織に存在しなくなる可能性があります。 このような場合、組織内のだれも Azure portal にアクセスできなくなる可能性があります。そのため、API を使用して新しいコレクション管理者を割り当て、コレクションのアクセス許可を管理することは避けられず、Purview アカウントへのアクセスを回復する唯一の方法になります。

```ruby
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Purview/accounts/{accountName}/addRootCollectionAdmin?api-version=2021-07-01
```
新しいルート コレクション管理者のオブジェクト ID を渡すだけで済みます。 前述のとおり、オブジェクト ID は、任意のユーザー、グループ、またはサービス プリンシパルのオブジェクト ID になります。
```json
{"objectId": "{guid}"}
```
成功すると、すべての REST API から HTTP 応答 200 OK が返されます。

> [!NOTE]
> この API を呼び出すユーザーがアカウントに対する書き込みアクションを実行するには、Purview アカウントに対して所有者または UAA アクセス許可を持っている必要があります。


##  <a name="purview-rest-api-combined-archive"></a>Purview REST API の結合アーカイブ
Purview API をすぐに使い始めることができるように、1 か所 ([azure-purview-rest-api-specs.zip](https://github.com/Azure/Azure-Purview-API-PowerShell/blob/main/azure-purview-rest-api-specs.zip)) にまとめて収集されたすべての Purview REST API のアーカイブを[ダウンロード](https://github.com/Azure/Azure-Purview-API-PowerShell/blob/main/azure-purview-rest-api-specs.zip)します。これらの API テンプレートを使用して、独自のコード、スクリプト、自動化、[AutoRest](https://github.com/Azure/autorest)、または Purview API の Postman コレクションを理解して構築することができます。

## <a name="powershell-utility-to-run-purview-apis"></a>Purview API を実行する PowerShell ユーティリティ
PowerShell ユーティリティ[Purview-API-PowerShell](https://aka.ms/purview-api-ps) を使用して Purview REST API を実行することを選択できます。 これは、PowerShell ギャラリーから簡単にインストールできます。 すべて同じコマンドが実行されますが、Windows PowerShell からです。

> [!div class="nextstepaction"] 
> [Purview-API-PowerShell](https://aka.ms/purview-api-ps) 


