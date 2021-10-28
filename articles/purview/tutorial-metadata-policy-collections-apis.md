---
title: Azure Purview コレクションのメタデータ ポリシーとロールの API について説明します
description: このチュートリアルでは、ユーザー、グループ、またはサービス プリンシパルに対する Azure Purview コレクションのロールベースのアクセス制御を管理する方法について説明します。
author: abandyop
ms.author: arindamba
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 09/24/2021
ms.openlocfilehash: 79848d8f54ea1d019f2a9e6cf93a61cc8b7c49f9
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130245931"
---
# <a name="tutorial-use-rest-apis-to-manage-role-based-access-control-on-azure-purview-collections"></a>チュートリアル: REST API を使用して、Azure Purview コレクションのロールベースのアクセス制御 を管理する 

2021 年 8 月に、Azure Purview のアクセス制御は Azure ID およびアクセス管理 (IAM) (コントロール プレーン) から [Azure Purview コレクション](how-to-create-and-manage-collections.md) (データ プレーン) に移行しました。 この変更によって、企業のデータ キュレーターおよび管理者は、Azure Purview によってスキャンされるデータ ソースに対して、より正確できめ細かいアクセス制御ができるようになります。 また、この変更により、組織はデータの適切なアクセスと適切な使用を監査できます。

このチュートリアルでは、Azure Purview のメタデータ ポリシー API を使用して、ユーザー、グループ、またはサービス プリンシパルをコレクションに追加し、そのコレクション内でそれらのロールを管理または削除する詳細な手順について説明します。 REST API を Azure portal または Azure Purview Studio の代わりに使用して、同じきめ細かなロールベースのアクセス制御を実現することができます。

Azure Purview の組み込みロールの詳細については、[Azure Purview のアクセス許可のガイド](catalog-permissions.md#roles)を参照してください。 このガイドでは、ユーザーに付与されるアクセス許可のレベルに対してロールがマップされています。

## <a name="metadata-policy-api-reference-summary"></a>メタデータ ポリシー API リファレンスの概要
次の表は、[Azure Purview メタデータ ポリシー API リファレンス](/rest/api/purview/metadatapolicydataplane/Metadata-Policy)の概要を示しています。 

これらの API を実行する前に、{pv-acc-name} をお使いの Azure Purview アカウントに置き換えてください。 たとえば、お使いの Azure Purview アカウント名が *FabrikamPurviewAccount* の場合、API エンドポイントは *FabrikamPurviewAccount.purview.azure.com* になります。

| API&nbsp;関数 | REST&nbsp;メソッド | API&nbsp;エンドポイント | 説明 | 
| :- | :- | :- | :- | 
| すべてのメタデータ ロールの読み取り| GET| https://{pv-acc-name}.purview.azure.com /policystore/metadataroles?&api-version=2021-07-01| Azure Purview アカウントからすべてのメタデータ ロールを読み取ります。| 
| コレクション名によるメタデータ ポリシーの読み取り| GET| https://{pv-acc-name}.purview.azure.com /policystore/collections/{collectionName}/metadataPolicy?&api-version=2021-07-01| 指定したコレクション名 (ポリシーの作成時に Azure Purview によって生成される 6 文字のランダムな名前) を使用して、メタデータ ポリシーを読み取ります。| 
| PolicyID によるメタデータ ポリシーの読み取り| GET| https://{pv-acc-name}.purview.azure.com /policystore/metadataPolicies/{policyId}?&api-version=2021-07-01| 指定したポリシー ID を使用してメタデータ ポリシーを読み取ります。 ポリシー ID は GUID 形式です。| 
| すべてのメタデータ ポリシーの読み取り| GET| https://{pv-acc-name}.purview.azure.com /policystore/metadataPolicies?&api-version=2021-07-01| Azure Purview アカウントからすべてのメタデータ ポリシーを読み取ります。 この API によって生成される JSON 出力リストから、使用する特定のポリシーを選択できます。| 
| メタデータ ポリシーの更新または PUT| PUT| https://{pv-acc-name}.purview.azure.com /policystore/metadataPolicies/{policyId}?&api-version=2021-07-01| 指定したポリシー ID を使用してメタデータ ポリシーを更新します。 ポリシー ID は GUID 形式です。|
| | | 

## <a name="azure-purview-catalog-collections-api-reference-summary"></a>Azure Purview カタログ コレクション API リファレンスの概要
次の表は、Azure Purview コレクション API の概要を示しています。 各 API に関する完全なドキュメントについては、左側の列で API 操作を選択してください。

| Operation | 説明 | 
| :- | :- | 
| [コレクションの作成または更新](/rest/api/purview/accountdataplane/collections/create-or-update-collection) | コレクション エンティティを作成または更新します。 | 
| [コレクションを削除する](/rest/api/purview/accountdataplane/collections/delete-collection) | コレクション エンティティを削除します。 | 
| [コレクションの取得](/rest/api/purview/accountdataplane/collections/get-collection) | コレクションを取得します。| 
| [コレクション パスの取得](/rest/api/purview/accountdataplane/collections/get-collection-path) | コレクション パスを表す親名と表示名チェーンを取得します。| 
| [子コレクション名の一覧表示](/rest/api/purview/accountdataplane/collections/list-child-collection-names) | コレクション内の子コレクション名を一覧表示します。| 
| [コレクションの一覧表示](/rest/api/purview/accountdataplane/collections/list-collections) | アカウント内のコレクションを一覧表示します。| 


- API を使用する場合、API を正常に実行するには、その API を実行するサービス プリンシパル、ユーザーまたはグループに Azure Purview で[コレクション管理者](how-to-create-and-manage-collections.md#check-permissions)ロールが割り当てられている必要があります。

- {collectionName} を必要とするすべての Azure Purview API では、 *"name"* ( *"friendlyName"* ではありません) を使用する必要があります。 {collectionName} を実際のコレクション名文字列 (6 桁の英数字) に置き換えます。 
   > [!NOTE]
   > この名前は、コレクションの作成時に指定するフレンドリ表示名とは異なります。 {collectionName} が手元にない場合は、[List Collections API](/rest/api/purview/accountdataplane/collections/list-collections) を使用して、JSON 出力から 6 文字のコレクション名を選択します。

JSON ファイルの例を次に示します。 

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

コレクション API から受け取る JSON 出力の一部の重要な識別子を次に示します。

**Name**: ポリシーの名前。 

**Id**: ポリシーの一意識別子。

**Version**: ポリシーの最新バージョン番号。 
   > [!IMPORTANT]
   > バージョン番号は、Update-Metadata-Policy API が呼び出されるたびに増分されます。 必ず Get-Policy-by-Policy-ID API を呼び出して、ポリシーの最新のコピーを取り込んでください。 この更新は、ポリシーの更新 (PUT) API を呼び出す前に毎回実行してください。そうすれば、常に最新バージョンの JSON を使用できます。

**DecisionRules**: このポリシーの規則と効果を一覧表示します。 メタデータ ポリシーの場合、効果は常に "*許可*" です。

## <a name="add-or-remove-users-from-a-collection-or-role"></a>コレクションまたはロールに対するユーザーの追加または削除

Azure Purview REST API を使用して、ユーザー、グループ、またはサービス プリンシパルをコレクションまたはロールに対して追加または削除します。 API の詳細な使用方法が、サンプルの JSON 出力と共に提供されています。 Azure Purview のメタデータ ポリシー API の理解を深めるには、次のセクションの手順を順番に実行することを強くお勧めします。

## <a name="get-all-metadata-roles"></a>すべてのメタデータ ロールの取得

使用可能なすべてのメタデータ アクセス許可ロールを一覧表示するには、次のコマンドを実行します。

```ruby
GET https://{your_purview_account_name}.purview.azure.com/policystore/metadataroles?api-version=2021-07-01
```

出力 JSON では、ロールとそれに関連付けられたアクセス許可をこの形式で記述します。

既定のメタデータ ロールを次の表に一覧表示します。

| ロール ID | アクセス許可 | 役割の説明 | 
| :- | :- | :- | 
| purviewmetadatarole\_builtin\_data-source-administrator| Microsoft.Purview/accounts/scan/read Microsoft.Purview/accounts/scan/write Microsoft.Purview/accounts/collection/read| コレクションの読み取り、書き込み、データソースの登録、スキャンのトリガーを行うためのアクセスを他のユーザーに許可します。| 
| purviewmetadatarole\_builtin\_collection-administrator| Microsoft.Purview/accounts/collection/read Microsoft.Purview/accounts/collection/write| コレクション全体に対する管理者レベルのフル アクセス (コレクションに対するユーザーとサービス プリンシパル名 (SPN) の追加または削除、管理権限、アクセスの許可または取り消しを含む)。 場合によっては、コレクション管理者はコレクションの作成者と異なる可能性があります。| 
| purviewmetadatarole\_builtin\_purview-reader| Microsoft.Purview/accounts/data/read Microsoft.Purview/accounts/collection/read| データ処理と、スキャン バインディングを除くコレクション内のすべてのメタデータ (分類、秘密度ラベル、分析情報、読み取り資産) への読み取りアクセスのみを許可します。| 
| purviewmetadatarole\_builtin\_data-curator| Microsoft.Purview/accounts/data/read Microsoft.Purview/accounts/data/write Microsoft.Purview/accounts/collection/read| データ処理と、スキャン バインディングを除くコレクション内のすべてのメタデータ (分類、秘密度ラベル、分析情報、読み取り資産) へのフル アクセスを許可します。| 
| purviewmetadatarole\_builtin\_data-share-contributor| Microsoft.Purview/accounts/share/read Microsoft.Purview/accounts/share/write| 共同作成者としてデータ共有へのアクセスを許可します。 | 

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
前のコマンドは、コレクション階層全体で使用可能なすべてのメタデータ ポリシーを、一番上のルート コレクションからそのすべての子ポリシーまでツリー形式で一覧表示します。 それぞれの子コレクションには、その次のレベルの子が含まれています。

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

## <a name="get-the-selected-metadata-policy"></a>選択したメタデータ ポリシーを取得する 

2 つの API のいずれかを使用して、{collectionName} または {PolicyID} のいずれかを指定することによって、特定のコレクションのメタデータ ポリシーの JSON 構造を取り込むことができます。

次の 2 つのセクションで説明するように、どちらの API も同じ目的を果たし、両方の JSON 出力はまったく同じです。

### <a name="get-the-metadata-policy-of-the-collection-by-using-the-collection-name"></a>コレクション名を使用してコレクションのメタデータ ポリシーを取得する

```ruby
GET https://{your_purview_account_name}.purview.azure.com/policystore/collections/{collectionName}/metadataPolicy?api-version=2021-07-01
```

1. Azure Purview アカウント名は、{your_purview_account_name} です。 これをお使いの Azure Purview アカウント名に置き換えます。

1. 前の「すべてのメタデータ ポリシーを取得する」の API の JSON 出力で、次のセクションを探します。 

    { "type": "CollectionReference", "referenceName": "7xkdg2"}

1. API URL の "{collectionName}" を "referenceName": "{6-char-collection-name}" の値に置き換えます。 たとえば、6 文字のコレクション名が "7xkdg2" の場合、API URL は次のような形式になります。 

   https://{your_purview_account_name}.purview.azure.com/policystore/collections/7xkdg2/metadataPolicy?api-version=2021-07-01

1. 次の API を実行します。 

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

### <a name="get-the-metadata-policy-of-the-collection-by-using-the-policy-id"></a>ポリシー ID を使用してコレクションのメタデータ ポリシーを取得する

```ruby
GET https://{your_purview_account_name}.purview.azure.com/policystore/metadataPolicies/{policyId}?api-version=2021-07-01
```

1. Azure Purview アカウント名は、{your_purview_account_name} です。 これをお使いの Azure Purview アカウント名に置き換えます。

1. 前の「すべてのメタデータ ポリシーを取得する」の API の JSON 出力で、次のセクションを探します。 

    {.... "name": "policy_qu45fs", "id": "{policy-guid}", "version": N ....}

1. API URL の "{policyId}" を "id" の値に置き換えます。 たとえば、"{policy-guid}" が "c6639bb2-9c41-4be0-912b-775750e725de" の場合、API URL は次のような形式になります。
 
   https://{your_purview_account_name}.purview.azure.com/policystore/metadataPolicies/c6639bb2-9c41-4be0-912b-775750e725de?api-version=2021-07-01

1. ここで、次の API を実行します。 

   > [!NOTE]
   > この API 呼び出しと前の API 呼び出しの出力は同じです。 前述のように、どちらかを選択できます。

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


## <a name="update-the-collection-policy"></a>コレクション ポリシーを更新する

```ruby
PUT https://{your_purview_account_name}.purview.azure.com/policystore/metadataPolicies/{policyId}?api-version=2021-07-01
```

このセクションでは、ユーザー、グループ、またはサービス プリンシパルをコレクションに追加または削除することで、前の手順で取得したポリシー JSON を更新します。 次に、PUT REST メソッドを使用して、これを Azure Purview サービスにプッシュします。

ユーザー、グループ、またはサービス プリンシパルの追加または削除のいずれを実行する場合でも、同じ API プロセスに従います。

1. JSON の "attributeValueIncludedIn" 配列で、ユーザー、グループ、サービス プリンシパルのオブジェクト ID {guid} を指定します。

1. Get-Policy-by-ID API の JSON 出力で、前の手順の "attributeValueIncludedIn" 配列を探して、この配列でユーザー、グループ、サービス プリンシパルのオブジェクト ID を追加または削除します。 ユーザー、グループ、またはサービス プリンシパルのオブジェクト ID を取り込む方法が不明な場合は、[Get-AzureADUser](/powershell/module/azuread/get-azureaduser) を参照してください。

1. JSON マッピングには、4 つの各ロールのために複数のセクションがあります。 コレクション管理者のアクセス許可ロールの場合、"purviewmetadatarole_builtin_collection-administrator" という名前の ID を持つセクションを使用します。 同様に、他のロールの場合も対応するセクションを使用します。

1. 追加または削除の操作をより深く理解するには、前の API からの JSON 出力と次の出力の違いを注意深く調べます。 次の JSON 出力では、"3a3a3a3a-2c2c-4b4b-1c1c-2a3b4c5d6e7f" というユーザー ID がコレクション管理者として追加されています。

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
## <a name="add-the-root-collection-administrator-role"></a>ルート コレクション管理者ロールを追加する
既定では、Azure Purview アカウントを作成したユーザーがルート コレクション管理者 (コレクション階層の最上位レベルの管理者) です。 しかし、場合によっては、組織は API を使用してルート コレクション管理者を変更する必要があります。 たとえば、現在のルート コレクション管理者が組織に存在しなくなる可能性があります。 このような場合、Azure portal に組織の誰もアクセスできなくなる可能性があります。 このため、API を使用して新しいルート コレクション管理者を割り当て、コレクションのアクセス許可を管理することが、Azure Purview アカウントへのアクセス権を回復する唯一の方法になります。

```ruby
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Purview/accounts/{accountName}/addRootCollectionAdmin?api-version=2021-07-01
```
前のコマンドを実行するのに必要なのは、新しいルート コレクション管理者のオブジェクト ID を渡すだけです。 前述のとおり、オブジェクト ID は、任意のユーザー、グループ、またはサービス プリンシパルのものを使用できます。

```json
{"objectId": "{guid}"}
```

> [!NOTE]
> この API を呼び出すユーザーは、アカウントに対して書き込みアクションを実行するために、Azure Purview アカウントに対する所有者または User Account and Authentication (UAA) アクセス許可を持っている必要があります。

## <a name="additional-resources"></a>その他のリソース

[PowerShell ユーティリティ](https://aka.ms/purview-api-ps)を使用して Azure Purview REST API を実行することを選択できます。 これは、PowerShell ギャラリーから簡単にインストールできます。 このユーティリティを使用すると、すべての同じコマンドを、Windows PowerShell から実行できます。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"] 
> [Purview-API-PowerShell](https://aka.ms/purview-api-ps) 
