---
title: "ストレージの Azure Resource Manager ポリシー | Microsoft Docs"
description: "ストレージ アカウントのデプロイを管理するための Azure Resource Manager のポリシーについて説明します。"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/19/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: ce31bbcb96a6afe19cf6c25dd9a68d6c2d4d080c
ms.openlocfilehash: f2141304f6db6d137065e06558e10c721b11f892


---
# <a name="apply-azure-resource-policies-to-storage-accounts"></a>ストレージ アカウントへの Azure リソース ポリシーの適用
Azure Resource Manager のポリシーを使用して、組織内のリソースをどのようにデプロイするかについて一貫性のある規則を定義します。 カスタマイズされたポリシーを作成し、組織のリソースを管理するために必要な規則に組織内のユーザーが違反するのを防ぐことができます。 このトピックでは、Azure Storage アカウントの規則を定義するいくつかのポリシーを取り上げます。 ポリシーについて詳しくは、[リソース ポリシーを使用したリソースの管理](resource-manager-policy.md)に関する記事をご覧ください。

このトピックの例では、ポリシーの規則のハード コーディングされた値を示します。 ただし、パラメーターを使用して、ポリシーを割り当てるときに使用される値を渡すことができます。 詳しくは、[ポリシーのパラメーター](resource-manager-policy.md#parameters)に関する記事をご覧ください。

## <a name="define-permitted-storage-account-types"></a>許可されているストレージ アカウントの種類の定義

次のポリシーは、デプロイ可能な[ストレージ アカウントの種類](../storage/storage-redundancy.md)を制限します。

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "not": {
          "field": "Microsoft.Storage/storageAccounts/sku.name",
          "in": [
            "Standard_LRS",
            "Standard_GRS"
          ]
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="define-permitted-access-tier"></a>許可されているアクセス層の定義

次のポリシーは、ストレージ アカウントに対して指定可能な[アクセス層](../storage/storage-blob-storage-tiers.md)の種類を指定します。

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "field": "kind",
        "equals": "BlobStorage"
      },
      {
        "not": {
          "field": "Microsoft.Storage/storageAccounts/accessTier",
          "equals": "cool"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="ensure-encryption-is-enabled"></a>暗号化が有効になっていることの確認

次のポリシーは、[ストレージ サービスの暗号化](../storage/storage-service-encryption.md)にすべてのストレージ アカウントを必要とします。

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "not": {
          "field": "Microsoft.Storage/storageAccounts/enableBlobEncryption",
          "equals": "true"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="create-and-assign-policies"></a>ポリシーの作成と割り当て

(上記の例で示すように) ポリシーの規則を定義した後は、ポリシーを作成し、スコープに割り当てる必要があります。 スコープには、サブスクリプション、リソース グループ、またはリソースを使用できます。 ポリシーの作成および割り当て方法の例については、「[ポリシーの作成と割り当て](resource-manager-policy.md#create-and-assign-a-policy)」を参照してください。 

## <a name="next-steps"></a>次のステップ
* 企業が Resource Manager を使用してサブスクリプションを効果的に管理する方法については、「[Azure enterprise scaffold - prescriptive subscription governance (Azure エンタープライズ スキャフォールディング - サブスクリプションの規範的な管理)](resource-manager-subscription-governance.md)」を参照してください。




<!--HONumber=Jan17_HO3-->


