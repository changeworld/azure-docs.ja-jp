---
title: Azure API for FHIR のカスタマー マネージド キーを構成する
description: Azure API for FHIR で Cosmos DB を通じてサポートされる Bring Your Own Key 機能
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.date: 09/28/2020
ms.author: matjazl
ms.openlocfilehash: 535bb5d21beafaabb50769a6c03478dbd1f942d4
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92051342"
---
# <a name="configure-customer-managed-keys"></a>顧客管理キーの構成

新しい Azure API for FHIR アカウントを作成すると、既定では、Microsoft マネージド キーを使用してデータが暗号化されます。 これで、独自のキーを使用して第 2 のデータ暗号化レイヤーを追加できるようになります。このキーは、自分で選んで管理することが可能です。

Azure では通常、これを実現するために、お客様の Azure Key Vault (AKV) にある暗号化キーが使用されます。 現在、その機能が提供される例として、Azure SQL、Azure Storage、Cosmos DB があります。 Azure API for FHIR は、このサポートを Cosmos DB から利用します。 アカウントを作成するときに、AKV キーの URI を指定することもできます。 このキーは、DB アカウントがプロビジョニングされていると、Cosmos DB に渡されます。 FHIR 要求が送信されると、Cosmos DB はキーを取得し、それを使用してデータの暗号化または暗号化解除を実行します。 まずは、次のリンク先を参照してください。

- [Azure サブスクリプション用の Azure Cosmos DB リソース プロバイダーを登録する](https://docs.microsoft.com/azure/cosmos-db/how-to-setup-cmk#register-resource-provider) 
- [AKV インスタンスを構成する](https://docs.microsoft.com/azure/cosmos-db/how-to-setup-cmk#configure-your-azure-key-vault-instance)
-  [AKV インスタンスにアクセス ポリシーを追加する](https://docs.microsoft.com/azure/cosmos-db/how-to-setup-cmk#add-an-access-policy-to-your-azure-key-vault-instance)
- [AKV でキーを生成する](https://docs.microsoft.com/azure/cosmos-db/how-to-setup-cmk#generate-a-key-in-azure-key-vault)

Azure portal で Azure API for FHIR アカウントを作成すると、[追加の設定] タブの [データベースの設定] に [データの暗号化] 構成オプションが表示されます。既定では、[サービス マネージド キー] オプションが選択されます。 ここで、[カスタマー マネージド キー] オプションを選択することで、自分の AKV キーを指定できます。 ここには、コピーしたキーの URI を入力できます。

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-create.png" alt-text="Azure API for FHIR の作成":::

または、キーの選択から、自分のキーを選択することもできます。

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-keypicker.png" alt-text="Azure API for FHIR の作成":::

既存の FHIR アカウントの場合は、次に示すように、[データベース] ブレードでキー暗号化の選択肢 (サービスまたはカスタマー マネージド キー) が表示されます。 この構成オプションは、選択後に変更することはできません。 ただし、キーを変更したり更新したりすることはできます。

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-database.png" alt-text="Azure API for FHIR の作成":::

さらに、指定したキーの新しいバージョンを作成することができます。その後、サービスが中断されることなく、既存のデータはその新しいバージョンで暗号化されます。 また、キーへのアクセス権を削除することで、データへのアクセス権を削除することもできます。
