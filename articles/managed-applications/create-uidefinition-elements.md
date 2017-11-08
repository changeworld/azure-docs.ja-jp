---
title: "Azure マネージ アプリケーションの UI 定義作成機能 | Microsoft Docs"
description: "Azure マネージ アプリケーションの UI 定義を作成するときに使う機能について説明します。"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/12/2017
ms.author: tomfitz
ms.openlocfilehash: 23e407bf93bc51116ca45339bffcb801d69290f0
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2017
---
# <a name="createuidefinition-elements"></a>CreateUiDefinition の要素
この記事では、CreateUiDefinition でサポートされるすべての要素のスキーマとプロパティについて説明します。 これらの要素は、[Azure マネージ アプリケーションを作成する](publish-service-catalog-app.md)ときに使用します。 ほとんどの要素のスキーマは、次のようになっています。

```json
{
  "name": "element1",
  "type": "Microsoft.Common.TextBox",
  "label": "Some text box",
  "defaultValue": "foobar",
  "toolTip": "Keep calm and visit the [Azure Portal](portal.azure.com).",
  "constraints": {},
  "options": {},
  "visible": true
}
```

| プロパティ | 必須 | 説明 |
| -------- | -------- | ----------- |
| name | あり | 要素の特定のインスタンスを参照するための内部的な識別子。 要素名が最もよく使用されるのは `outputs` で、指定した要素の出力値がテンプレートのパラメーターにマッピングされます。 これを使って要素の出力値を別の要素の `defaultValue` にバインドすることもできます。 |
| type | あり | レンダリングする要素の UI コントロール。 サポートされる型の一覧については、「[要素](#elements)」を参照してください。 |
| label | あり | 要素の表示テキスト。 要素の種類によっては複数のラベルが含まれるため、この値は複数の文字列を含んだオブジェクトになる場合があります。 |
| defaultValue | いいえ | 要素の既定値。 要素の種類によっては複合的な既定値がサポートされるため、この値はオブジェクトになる場合があります。 |
| toolTip | いいえ | 要素のヒントに表示するテキスト。 `label` と同様、要素によっては複数のヒント文字列がサポートされます。 マークダウン構文を使ってインライン リンクを埋め込むことができます。
| constraints | いいえ | 要素の検証動作をカスタマイズする目的で使う 1 つまたは複数のプロパティ。 constraints でサポートされるプロパティは要素の種類によって異なります。 要素の種類によっては検証動作のカスタマイズがサポートされていません。その場合、constraints プロパティは存在しません。 |
| options | いいえ | 要素の動作をカスタマイズする補足的なプロパティ。 `constraints` と同様、サポートされるプロパティは要素の種類によって異なります。 |
| visible | いいえ | 要素が表示されるかどうかを示します。 `true` の場合、要素と該当する子要素が表示されます。 既定値は `true` です。 このプロパティの値を動的に制御するには[論理関数](create-uidefinition-functions.md#logical-functions)を使います。

## <a name="elements"></a>要素

それぞれの要素のドキュメントには、UI サンプル、スキーマ、要素の (通常、検証およびサポートされるカスタマイズに関連した) 動作についての備考、サンプル出力が記載されています。

- [Microsoft.Common.DropDown](microsoft-common-dropdown.md)
- [Microsoft.Common.FileUpload](microsoft-common-fileupload.md)
- [Microsoft.Common.OptionsGroup](microsoft-common-optionsgroup.md)
- [Microsoft.Common.PasswordBox](microsoft-common-passwordbox.md)
- [Microsoft.Common.Section](microsoft-common-section.md)
- [Microsoft.Common.TextBox](microsoft-common-textbox.md)
- [Microsoft.Compute.CredentialsCombo](microsoft-compute-credentialscombo.md)
- [Microsoft.Compute.SizeSelector](microsoft-compute-sizeselector.md)
- [Microsoft.Compute.UserNameTextBox](microsoft-compute-usernametextbox.md)
- [Microsoft.Network.PublicIpAddressCombo](microsoft-network-publicipaddresscombo.md)
- [Microsoft.Network.VirtualNetworkCombo](microsoft-network-virtualnetworkcombo.md)
- [Microsoft.Storage.MultiStorageAccountCombo](microsoft-storage-multistorageaccountcombo.md)
- [Microsoft.Storage.StorageAccountSelector](microsoft-storage-storageaccountselector.md)

## <a name="next-steps"></a>次のステップ
* マネージ アプリケーションの概要については、「[Azure マネージ アプリケーションの概要](overview.md)」を参照してください。
* UI 定義の作成の概要については、「[CreateUiDefinition の基本概念](create-uidefinition-overview.md)」を参照してください。
