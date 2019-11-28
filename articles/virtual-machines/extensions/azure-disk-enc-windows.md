---
title: Windows 用の Azure Disk Encryption
description: 仮想マシン拡張機能を使用して、Azure Disk Encryption を Windows 仮想マシンにデプロイします。
services: virtual-machines-windows
documentationcenter: ''
author: ejarvi
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/12/2018
ms.author: ejarvi
ms.openlocfilehash: 8435663dcf92e2617ea2fe9218649e94243272d2
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/14/2019
ms.locfileid: "74073205"
---
# <a name="azure-disk-encryption-for-windows-microsoftazuresecurityazurediskencryption"></a>Windows 用 Azure Disk Encryption (Microsoft.Azure.Security.AzureDiskEncryption)

## <a name="overview"></a>概要

Azure Disk Encryption は、BitLocker を利用して、Windows を実行している Azure 仮想マシン上で完全なディスク暗号化を提供します。  このソリューションは Azure Key Vault と統合されており、ディスクの暗号化キーとシークレットは Key Vault サブスクリプションで管理することができます。 

## <a name="prerequisites"></a>前提条件

前提条件の完全な一覧については、「[Linux VM に対する Azure Disk Encryption](../linux/disk-encryption-overview.md)」の特に次のセクションを参照してください。

- [Linux VM に対する Azure Disk Encryption](../windows/disk-encryption-overview.md#supported-vms-and-operating-systems)
- [ネットワーク要件](../windows/disk-encryption-overview.md#networking-requirements)
- [グループ ポリシーの要件](../windows/disk-encryption-overview.md#group-policy-requirements)

## <a name="extension-schemata"></a>拡張機能のスキーマ

Windows AzureDiskEncryption 拡張機能には 2 つのスキーマがあります。v2.2 は、Azure Active Directory (AAD) のプロパティを使用しない、推奨される新しいスキーマで、v1.1 は、AAD のプロパティを必要とする、前のスキーマです。 使用する拡張機能に対応するスキーマのバージョンを使う必要があります。スキーマ v2.2 は AzureDiskEncryption 拡張機能バージョン 2.2 用で、スキーマ v1.1 は AzureDiskEncryption 拡張機能バージョン 1.1 用です。

### <a name="schema-v22-no-aad-recommended"></a>スキーマ v2.2:AAD なし (推奨)

v2.2 スキーマはすべての新しい VM で推奨されており、Azure Active Directory のプロパティを必要としません。

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2015-06-15",
  "location": "[location]",
  "properties": {
    "publisher": "Microsoft.Azure.Security",
    "settings": {
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KekVaultResourceId": "[keyVaultResourceID]",
      "KeyVaultURL": "[keyVaultURL]",
      "KeyVaultResourceId": "[keyVaultResourceID]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    },
  "type": "AzureDiskEncryption",
  "typeHandlerVersion": "[extensionVersion]"
  }
}
```


### <a name="schema-v11-with-aad"></a>スキーマ v1.1: AAD を使用 

1\.1 スキーマでは、`aadClientID` と、`aadClientSecret` または `AADClientCertificate` のいずれかを必要とします。新しい VM には推奨されません。

`aadClientSecret`コマンドを使用します。

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2015-06-15",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientSecret": "[aadClientSecret]"
    },    
    "publisher": "Microsoft.Azure.Security",
    "settings": {
      "AADClientID": "[aadClientID]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KekVaultResourceId": "[keyVaultResourceID]",
      "KeyVaultURL": "[keyVaultURL]",
      "KeyVaultResourceId": "[keyVaultResourceID]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    },
  "type": "AzureDiskEncryption",
  "typeHandlerVersion": "[extensionVersion]"
  }
}
```

`AADClientCertificate`コマンドを使用します。

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2015-06-15",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientCertificate": "[aadClientCertificate]"
    },    
    "publisher": "Microsoft.Azure.Security",
    "settings": {
      "AADClientID": "[aadClientID]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KekVaultResourceId": "[keyVaultResourceID]",
      "KeyVaultURL": "[keyVaultURL]",
      "KeyVaultResourceId": "[keyVaultResourceID]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    },
  "type": "AzureDiskEncryption",
  "typeHandlerVersion": "[extensionVersion]"
  }
}
```


### <a name="property-values"></a>プロパティ値

| 名前 | 値/例 | データ型 |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publisher | Microsoft.Azure.Security | string |
| type | AzureDiskEncryptionForLinux | string |
| typeHandlerVersion | 1.1、2.2 | string |
| (1.1 スキーマ) AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | guid | 
| (1.1 スキーマ) AADClientSecret | password | string |
| (1.1 スキーマ) AADClientCertificate | thumbprint | string |
| DiskFormatQuery | {"dev_path":"","name":"","file_system":""} | JSON 辞書 |
| EncryptionOperation | EnableEncryption、EnableEncryptionFormatAll | string | 
| KeyEncryptionAlgorithm | 'RSA-OAEP'、'RSA-OAEP-256'、'RSA1_5' | string |
| KeyEncryptionKeyURL | url | string |
| KeyVaultURL | url | string |
| (省略可能) Passphrase | password | string | 
| SequenceVersion | uniqueidentifier | string |
| VolumeType | OS、Data、All | string |

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
