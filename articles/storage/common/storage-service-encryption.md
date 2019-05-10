---
title: 保存データに対する Azure Storage 暗号化 | Microsoft Docs
description: Azure Storage では、クラウドに永続化される前にデータを自動的に暗号化することによって、データが保護されます。 Azure Storage 内のすべてのデータは、256 ビット AES 暗号化を使用して透過的に暗号化および暗号化解除され、FIPS 140-2 に準拠しています。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/30/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 6eb7de7810ce23aed4031cca9f038da7149a6f9c
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2019
ms.locfileid: "65153085"
---
# <a name="azure-storage-encryption-for-data-at-rest"></a>保存データに対する Azure Storage 暗号化

Azure Storage では、データはクラウドに永続化されるときに自動的に暗号化されます。 暗号化によってデータは保護され、組織のセキュリティおよびコンプライアンス コミットメントを満たすのに役立ちます。 Azure Storage 内のデータは、利用可能な最強のブロック暗号の 1 つである 256 ビット [AES 暗号化](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)を使って透過的に暗号化および暗号化解除され、FIPS 140-2 に準拠しています。 Azure Storage 暗号化は、Windows での BitLocker 暗号化に似ています。

Azure Storage 暗号化は、新規と既存のすべてのストレージ アカウントに対して有効にされ、無効にすることはできません。 データは既定で保護されるので、Azure Storage 暗号化を利用するために、コードまたはアプリケーションを変更する必要はありません。 

ストレージ アカウントは、そのパフォーマンス レベル (Standard または Premium) またはデプロイ モデル (Azure Resource Manager またはクラシック) に関係なく、暗号化されます。 Azure Storage のすべての冗長性オプションで暗号化がサポートされており、ストレージ アカウントのすべてのコピーが暗号化されます。 BLOB、ディスク、ファイル、キュー、テーブルなど、すべての Azure Storage リソースが暗号化されます。 すべてのオブジェクト メタデータも暗号化されます。

暗号化により、Azure Storage のパフォーマンスが影響を受けることはありません。 Azure Storage 暗号化に対する追加コストはありません。

Azure Storage 暗号化の基になっている暗号化モジュールについて詳しくは、「[Cryptography API: Next Generation (暗号化 API: 次世代)](https://docs.microsoft.com/windows/desktop/seccng/cng-portal)」を参照してください。

## <a name="key-management"></a>キー管理

Microsoft のマネージド キーを利用してストレージ アカウントを暗号化することも、独自のキーと Azure Key Vault で暗号化を管理することもできます。

### <a name="microsoft-managed-keys"></a>Microsoft のマネージド キー

既定では、ストレージ アカウントには Microsoft のマネージド暗号化キーが使われます。 ストレージ アカウントの暗号化の設定は、次の図のように、[Azure portal](https://portal.azure.com) の **[暗号化]** セクションで確認できます。

![Microsoft のマネージド キーで暗号化されたアカウントを表示する](media/storage-service-encryption/encryption-microsoft-managed-keys.png)

### <a name="customer-managed-keys"></a>カスタマー マネージド キー

カスタマー マネージド キーを使用して Azure Storage 暗号化を管理できます。 カスタマー マネージド キーを使うと、アクセス制御の作成、ローテーション、無効化、取り消しを、いっそう柔軟に行うことができます。 また、データを保護するために使われる暗号化キーを監査することもできます。 

キーの管理およびキーの使用状況の監査には、Azure Key Vault を使います。 独自のキーを作成してキー コンテナーに格納することも、Azure Key Vault API を使ってキーを生成することもできます。 ストレージ アカウントとキー コンテナーは同じリージョンに存在していることが必要です。ただし、サブスクリプションは異なっていてもかまいません。 Azure Key Vault の詳細については、「[Azure Key Vault とは](../../key-vault/key-vault-overview.md)」をご覧ください。

カスタマー マネージド キーへのアクセスを取り消すには、[Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/) および [Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault) に関する記事をご覧ください。 アクセスを取り消すと、Azure Storage が暗号化キーにアクセスできなくなるため、ストレージ アカウント内の全データへのアクセスが事実上ブロックされます。

Azure Storage でカスタマー マネージド キーを使う方法については、次の記事をご覧ください。

- [Azure portal から Azure Storage 暗号化用にカスタマー マネージド キーを構成する](storage-encryption-keys-portal.md)
- [PowerShell から Azure Storage 暗号化用にカスタマー マネージド キーを構成する](storage-encryption-keys-powershell.md)
- [Azure CLI から Azure Storage 暗号化でカスタマー マネージド キーを使用する](storage-encryption-keys-cli.md)

> [!NOTE]  
> [Azure マネージド ディスク](../../virtual-machines/windows/managed-disks-overview.md)に対しては、カスタマー マネージド キーはサポートされていません。

## <a name="azure-storage-encryption-versus-disk-encryption"></a>Azure Storage 暗号化とディスク暗号化

Azure Storage 暗号化では、すべての Azure ストレージ アカウントとそれに含まれるリソースが暗号化され、それには Azure 仮想マシンのディスクをバックアップするページ BLOB が含まれます。 さらに、Azure 仮想マシンのディスクは [Azure Disk Encryption](../../security/azure-security-disk-encryption-overview.md) で暗号化することもできます。 Azure Disk Encryption では、業界標準である [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) (Windows 上) および [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) (Linux 上) が使われており、Azure Key Vault と統合されたオペレーティング システム ベースの暗号化ソリューションが提供されます。

## <a name="next-steps"></a>次の手順

- [Azure Key Vault とは](../../key-vault/key-vault-overview.md)
