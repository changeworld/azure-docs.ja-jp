---
title: Bicep の子リソース
description: Bicep で子リソースの名前と種類を設定する方法について説明します。
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: 879c325ee64307e7c548efa4ef7ba34eb68cc896
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2021
ms.locfileid: "122444199"
---
# <a name="set-name-and-type-for-child-resources-in-bicep"></a>Bicep での子リソースの名前と種類の設定

子リソースとは、別のリソースのコンテキスト内でのみ存在するリソースのことです。 たとえば[仮想マシン拡張機能](/azure/templates/microsoft.compute/virtualmachines/extensions)は、[仮想マシン](/azure/templates/microsoft.compute/virtualmachines)なしでは存在できません。 この拡張機能リソースが仮想マシンの子です。

各親リソースは、子リソースとして特定のリソースの種類のみを受け取ります。 子リソースのリソースの種類には、親リソースのリソースの種類が含まれます。 たとえば、`Microsoft.Web/sites/config` と `Microsoft.Web/sites/extensions` は、どちらも `Microsoft.Web/sites` の子リソースです。 許容されるリソースの種類は、親リソースの[テンプレート スキーマ](https://github.com/Azure/azure-resource-manager-schemas)で指定されます。

Bicep では、親リソースの内側または外側に子リソースを指定できます。 リソースの名前とリソースの種類に指定する値は、子リソースが親リソースの内側で定義されているか、外側で定義されているかによって変わります。

## <a name="within-parent-resource"></a>親リソースの内側

次の例は、親リソースの resources プロパティ内に追加された子リソースを示しています。

```bicep
resource <parent-resource-symbolic-name> '<resource-type>@<api-version>' = {
  <parent-resource-properties>

  resource <child-resource-symbolic-name> '<child-resource-type>' = {
    <child-resource-properties>
  }
}
```

入れ子になったリソース宣言は、親リソースの構文の最上位レベルに記述する必要があります。 各レベルが親リソースの子の種類である限りは、宣言を任意の深さで入れ子にすることができます。

親リソースの type 内に type と name の値を定義するときは、スラッシュを使わず 1 つのセグメントとして書式設定します。 次の例は、ファイル サービスとファイル共有を持つストレージ アカウントを示しています。 ファイル サービスの名前は **defaul** に設定され、その型は **fileServices** に設定されます。 ファイル共有の名前は **exampleshare** に設定され、その種類は **shares** に設定されます。

```bicep
resource storage 'Microsoft.Storage/storageAccounts@2021-02-01' = {
  name: 'examplestorage'
  location: resourceGroup().location
  kind: 'StorageV2'
  sku: {
    name: 'Standard_LRS'
  }

  resource service 'fileServices' = {
    name: 'default'

    resource share 'shares' = {
      name: 'exampleshare'
    }
  }
}
```

完全なリソースの種類は、`Microsoft.Storage/storageAccounts/fileServices` と `Microsoft.Storage/storageAccounts/fileServices/shares` です。 `Microsoft.Storage/storageAccounts/` は親リソースの種類とバージョンから想定されるため不要です。 必要に応じて、入れ子になったリソースを使用すれば、構文 `<segment>@<version>` を使用して、API バージョンを宣言できます。 入れ子になったリソースで API バージョンが省略される場合は、親リソースの API バージョンが使用されます。 入れ子になったリソースに API バージョンが指定される場合は、指定されたその API バージョンが使用されます。

子リソース名は **default** と **exampleshare** に設定されますが、完全な名前には親名が含まれます。 **examplestorage** または **default** は親リソースから想定されているので、指定されません。

入れ子になったリソースは、その親リソースのプロパティにアクセスできます。 同じ親リソースの本体内で宣言されたその他のリソースは、シンボリック名を使用することで互いに参照することができます。 親リソースは、それに含まれるリソースのプロパティにアクセスできない場合があり、この試行により循環依存関係が発生します。

親リソースの外部で入れ子になったリソースを参照するには、それを含むリソース名と `::` 演算子で修飾する必要があります。 たとえば、子リソースからプロパティを出力します。

```bicep
output childAddressPrefix string = VNet1::VNet1_Subnet1.properties.addressPrefix
```

## <a name="outside-parent-resource"></a>親リソースの外側

次の例は、親リソースの外側の子リソースを示しています。 親リソースが同じテンプレート内にデプロイされていない場合、または複数の子リソースを作成するために [loop](loop-resources.md) を使う場合は、このアプローチを使用することがあります。 値を親のシンボリック名に設定して、子の親プロパティを指定します。 この構文では、完全なリソースの種類を宣言する必要がありますが、子リソースの名前は子リソースの名前のみです。

```bicep
resource <parent-resource-symbolic-name> '<resource-type>@<api-version>' = {
  name: 'myParent'
  <parent-resource-properties>
}

resource <child-resource-symbolic-name> '<child-resource-type>@<api-version>' = {
  parent: <parent-resource-symbolic-name>
  name: 'myChild'
  <child-resource-properties>
}
```

親リソースの外側に type と name を定義するときは、スラッシュを使って親の種類と名前を含めるように書式設定します。

次の例は、ルート レベルで定義されているストレージ アカウント、ファイル サービス、およびファイル共有を示しています。

```bicep
resource storage 'Microsoft.Storage/storageAccounts@2021-02-01' = {
  name: 'examplestorage'
  location: resourceGroup().location
  kind: 'StorageV2'
  sku: {
    name: 'Standard_LRS'
  }
}

resource service 'Microsoft.Storage/storageAccounts/fileServices@2021-02-01' = {
  name: 'default'
  parent: storage
}

resource share 'Microsoft.Storage/storageAccounts/fileServices/shares@2021-02-01' = {
  name: 'exampleshare'
  parent: service
}
```

子リソースのシンボリック名の参照は、親を参照する場合と同じように機能します。

## <a name="next-steps"></a>次の手順

* Bicep ファイル作成の詳細については、「[Bicep ファイルの構造と構文の詳細](./file.md)」を参照してください。
* リソースを参照する際のリソース名の形式については、[reference 関数](./bicep-functions-resource.md#reference)の説明を参照してください。
