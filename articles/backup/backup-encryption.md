---
title: Azure Backup での暗号化
description: Azure Backup の暗号化機能が、バックアップ データを保護し、ビジネスのセキュリティ ニーズを満たすためにどのように役立つかについて説明します。
ms.topic: conceptual
ms.date: 05/25/2021
ms.custom: references_regions
ms.openlocfilehash: 2e32bb880e42aad72a526e7515ba9156de75de9a
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130233058"
---
# <a name="encryption-in-azure-backup"></a>Azure Backup での暗号化

クラウドに保存されているバックアップ データは、Azure Storage 暗号化を使用して自動的に暗号化されます。これは、セキュリティとコンプライアンスのコミットメントを満たすのに役立ちます。 この保存データは、利用可能な最強のブロック暗号の 1 つである 256 ビット AES 暗号化を使って暗号化され、FIPS 140-2 に準拠しています。 保存データの暗号化に加えて、転送中のすべてのバックアップ データが HTTPS 経由で転送されます。 これは、Azure バックボーン ネットワークにとどまります。

## <a name="levels-of-encryption-in-azure-backup"></a>Azure Backup での暗号化のレベル

Azure Backup には、次の 2 つのレベルの暗号化が含まれています。

- **Recovery Services コンテナーでのデータの暗号化**
  - **プラットフォーム マネージド キーの使用**: 既定では、すべてのデータが、プラットフォーム マネージド キーを使用して暗号化されます。 この暗号化を有効にするためにユーザーが明示的なアクションを実行する必要はありません。 これは、Recovery Services コンテナーにバックアップ中のすべてのワークロードに適用されます。
  - **カスタマー マネージド キーの使用**: Azure Virtual Machines をバックアップするときに、自分が所有および管理している暗号化キーを使用してデータを暗号化することを選択できます。 Azure Backup を使用すると、自分のバックアップの暗号化に、Azure Key Vault に格納されているご自身の RSA キーを使用でききます。 バックアップの暗号化に使用される暗号化キーは、ソースに使用されているものと異なることがあります。 データは、AES 256 ベースのデータ暗号化キー (DEK) を使用して保護され、このキーは、ご自身のキーを使用して保護されます。 これにより、データとキーを完全に制御できます。 暗号化を許可するには、Azure Key Vault の暗号化キーに Recovery Services コンテナーへのアクセス権を付与する必要があります。 キーの無効化とアクセスの取り消しは、必要に応じていつでも可能です。 ただし、コンテナーに対する項目の保護を試みるには、事前にキーを使って暗号化を有効にしておく必要があります。 [こちら](encryption-at-rest-with-cmk.md)を参照してください。
  - **インフラストラクチャ レベルの暗号化**: カスタマー マネージド キーを使用して Recovery Services コンテナー内のデータを暗号化するだけでなく、ストレージ インフラストラクチャで追加の暗号化レイヤーを構成することもできます。 このインフラストラクチャ暗号化は、プラットフォームによって管理されます。 これにより、カスタマー マネージド キーを使用した保存時の暗号化と共に、バックアップ データの 2 層の暗号化が可能になります。 インフラストラクチャ暗号化は、最初に、保存時の暗号化に独自のキーを使用することを選択した場合にのみ構成できます。 インフラストラクチャの暗号化では、データの暗号化にプラットフォーム マネージド キーを使用します。
- **バックアップされているワークロードに固有の暗号化**  
  - **Azure 仮想マシンのバックアップ**: Azure Backup では、[プラットフォーム マネージド キー](../virtual-machines/disk-encryption.md#platform-managed-keys)だけでなく、自分が所有および管理している [カスタマー マネージド キー](../virtual-machines/disk-encryption.md#customer-managed-keys)を使用して暗号化されたディスクでの VM のバックアップがサポートされます。 さらに、その OS またはデータ ディスクが [Azure Disk Encryption](backup-azure-vms-encryption.md#encryption-support-using-ade) を使用して暗号化されている Azure 仮想マシンをバックアップすることもできます。 ADE は、Windows VM では BitLocker、Linux VM では DM-Crypt を使用してゲスト内暗号化を実行します。

## <a name="next-steps"></a>次のステップ

- [保存データに対する Azure Storage 暗号化](../storage/common/storage-service-encryption.md)
- 暗号化について質問がある場合は [Azure Backup の FAQ](./backup-azure-backup-faq.yml)
