---
title: KeyVaultCertificateSelector UI 要素
description: Azure portal の Microsoft.KeyVault.KeyVaultCertificateSelector UI 要素について説明します。
author: tfitzmac
ms.topic: conceptual
ms.date: 10/27/2020
ms.author: tomfitz
ms.openlocfilehash: 48aafceff80e4a570900e5a8e1190698e12946b1
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2020
ms.locfileid: "92754247"
---
# <a name="microsoftkeyvaultkeyvaultcertificateselector-ui-element"></a>Microsoft.KeyVault.KeyVaultCertificateSelector UI 要素

キー コンテナー証明書を選択するためのコントロールです。

## <a name="ui-sample"></a>UI サンプル

ユーザーに対して、使用可能な証明書を選択するためのオプションが表示されます。

:::image type="content" source="./media/managed-application-elements/microsoft-keyvault-keyvaultcertificateselector-select.png" alt-text="Microsoft.KeyVault.KeyVaultCertificateSelector":::

**[証明書を選択してください]** を選択した後、ユーザーはキー コンテナーとキー コンテナーの証明書を指定できます。

:::image type="content" source="./media/managed-application-elements/microsoft-keyvault-keyvaultcertificateselector-certificate.png" alt-text="Microsoft.KeyVault.KeyVaultCertificateSelector 証明書の選択":::

コントロールが更新され、選択したキー コンテナーと証明書の名前が表示されます。

:::image type="content" source="./media/managed-application-elements/microsoft-keyvault-keyvaultcertificateselector-result.png" alt-text="Microsoft.KeyVault.KeyVaultCertificateSelector 選択した証明書の表示":::

## <a name="schema"></a>スキーマ

```json
{
  "name": "keyVaultCertificateSelection",
  "type": "Microsoft.KeyVault.KeyVaultCertificateSelector",
  "visible": true,
  "toolTip": "Select certificate",
  "label": "Key Vault certificates selection"
}
```

## <a name="sample-output"></a>サンプル出力

```json
{
  "keyVaultName": "azuretestkeyvault1",
  "keyVaultId": "/subscriptions/{subscription-id}/resourceGroups/resourcegroup1/providers/Microsoft.KeyVault/vaults/azuretestkeyvault1",
  "certificateName": "certificate1",
  "certificateUrl": "https://azuretestkeyvault1.vault.azure.net/secrets/certificate1/{id}",
  "certificateThumbprint": "1B721E84DDDD1BB69282B4A54F18C6ADB1C174F2"
}
```

## <a name="next-steps"></a>次のステップ

* UI 定義の作成の概要については、「[CreateUiDefinition の基本概念](create-uidefinition-overview.md)」を参照してください。
* UI 要素の共通プロパティの説明については、「[CreateUiDefinition の要素](create-uidefinition-elements.md)」を参照してください。
