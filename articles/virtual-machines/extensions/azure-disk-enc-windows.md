---
title: Windows 用の Azure Disk Encryption | Microsoft Docs
description: 仮想マシン拡張機能を使用して、Azure Disk Encryption を Windows 仮想マシンにデプロイします。
services: virtual-machines-windows
documentationcenter: ''
author: ejarvi
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/12/2018
ms.author: ejarvi
ms.openlocfilehash: 11ec26729b2239279dddc8cd62f6b658a4f7ed20
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39413792"
---
# <a name="azure-disk-encryption-for-windows-microsoftazuresecurityazurediskencryption"></a>Windows 用 Azure Disk Encryption (Microsoft.Azure.Security.AzureDiskEncryption)

## <a name="overview"></a>概要

Azure Disk Encryption は、Bitlocker を利用して、Windows を実行している Azure 仮想マシン上で完全なディスク暗号化を提供します。  このソリューションは Azure Key Vault と統合されており、ディスクの暗号化キーとシークレットは Key Vault サブスクリプションで管理することができます。 

## <a name="prerequisites"></a>前提条件

前提条件の詳細な一覧については、[Azure Disk Encryption の前提条件](
../../security/azure-security-disk-encryption-prerequisites.md)に関するセクションを参照してください。

### <a name="operating-system"></a>オペレーティング システム

現在の Windows バージョンの一覧については、[Azure Disk Encryption の前提条件](../../security/azure-security-disk-encryption-prerequisites.md)に関するセクションを参照してください。

### <a name="internet-connectivity"></a>インターネット接続

Azure Disk Encryption では、Active Directory、Key Vault、Storage、パッケージ管理エンドポイントにアクセスするためにインターネット接続が必要です。  ネットワーク セキュリティ設定の詳細については、[Azure Disk Encryption の前提条件](
../../security/azure-security-disk-encryption-prerequisites.md)に関するセクションを参照してください。

## <a name="extension-schema"></a>拡張機能のスキーマ

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2015-06-15",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientSecret": "[aadClientSecret]",
    },
    "publisher": "Microsoft.Azure.Security",
    "settings": {
      "AADClientID": "[aadClientID]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KeyVaultURL": "[keyVaultURL]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    },
    "type": "AzureDiskEncryption",
    "typeHandlerVersion": "[extensionVersion]"
  }
}
```

### <a name="property-values"></a>プロパティ値

| Name | 値/例 | データ型 |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publisher | Microsoft.Azure.Security | 文字列 |
| type | AzureDiskEncryptionForWindows| 文字列 |
| typeHandlerVersion | 1.0、2.2 (VMSS) | int |
| (省略可能) AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | GUID | 
| (省略可能) AADClientSecret | password | 文字列 |
| (省略可能) AADClientCertificate | thumbprint | 文字列 |
| EncryptionOperation | EnableEncryption | 文字列 | 
| KeyEncryptionAlgorithm | RSA-OAEP | 文字列 |
| KeyEncryptionKeyURL | url | 文字列 |
| KeyVaultURL | url | 文字列 |
| SequenceVersion | uniqueidentifier | 文字列 |
| VolumeType | OS、Data、All | 文字列 |

## <a name="template-deployment"></a>テンプレートのデプロイ
テンプレートのデプロイの例については、「[Create a new encrypted Windows VM from gallery image (ギャラリー イメージから新しい暗号化された Windows VM を作成する)](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image)」を参照してください。

## <a name="azure-cli-deployment"></a>Azure CLI でのデプロイ

手順については、最新の [Azure CLI ドキュメント](/cli/azure/vm/encryption?view=azure-cli-latest)を参照してください。 

## <a name="troubleshoot-and-support"></a>トラブルシューティングとサポート

### <a name="troubleshoot"></a>トラブルシューティング

[Azure Disk Encryption トラブルシューティング ガイド](../../security/azure-security-disk-encryption-tsg.md)を参照してください。

### <a name="support"></a>サポート

この記事についてさらにヘルプが必要な場合は、いつでも [MSDN の Azure フォーラムと Stack Overflow フォーラム](https://azure.microsoft.com/support/community/)で Azure エキスパートに問い合わせることができます。 または、Azure サポート インシデントを送信できます。 その場合は、[Azure サポートのサイト](https://azure.microsoft.com/support/options/)に移動して、[サポートの要求] をクリックします。 Azure サポートの使用方法の詳細については、「 [Microsoft Azure サポートに関する FAQ](https://azure.microsoft.com/support/faq/)」を参照してください。

## <a name="next-steps"></a>次の手順
拡張機能の詳細については、「[Windows 用の仮想マシン拡張機能とその機能](features-windows.md)」を参照してください。