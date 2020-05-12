---
title: Azure Backup での暗号化
description: Azure Backup の暗号化機能を利用して、バックアップ データを保護し、ビジネスのセキュリティ ニーズを満たす方法について説明します。
ms.topic: conceptual
ms.date: 04/30/2020
ms.openlocfilehash: 1f7e0f26df0f8bd70a10b36f658dcfebe897b475
ms.sourcegitcommit: 3beb067d5dc3d8895971b1bc18304e004b8a19b3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82761432"
---
# <a name="encryption-in-azure-backup"></a>Azure Backup での暗号化

クラウドに保存されているバックアップ データは、Azure Storage 暗号化を使用して自動的に暗号化されます。これは、セキュリティとコンプライアンスのコミットメントを満たすのに役立ちます。 この保存データは、利用可能な最強のブロック暗号の 1 つである 256 ビット AES 暗号化を使って暗号化され、FIPS 140-2 に準拠しています。

保存データの暗号化に加えて、転送中のすべてのバックアップ データが HTTPS 経由で転送されます。 これは、Azure バックボーン ネットワークにとどまります。

詳細については、「[保存データ向け Azure ストレージの暗号化](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)」をご覧ください。 暗号化に関する質問の回答については、[Azure Backup の FAQ](https://docs.microsoft.com/azure/backup/backup-azure-backup-faq#encryption) のページを参照してください。

## <a name="encryption-of-backup-data-using-platform-managed-keys"></a>プラットフォーム マネージド キーを使用したバックアップ データの暗号化

既定では、すべてのデータが、プラットフォーム マネージド キーを使用して暗号化されます。 この暗号化は、ユーザー側から明示的なアクションを実行しなくても有効になり、Recovery Services コンテナーにバックアップ中のすべてのワークロードに適用されます。

## <a name="encryption-of-backup-data-using-customer-managed-keys"></a>カスタマー マネージド キーを使用したバックアップ データの暗号化

Azure Virtual Machines のバックアップ時に、ご自身で所有および管理しているキーを使って、データを暗号化できるようになりました。 Azure Backup を使用すると、自分のバックアップの暗号化に、Azure Key Vault に格納されているご自身の RSA キーを使用でききます。 バックアップの暗号化に使用される暗号化キーは、ソースに使用されているものと異なることがあります。 データは、AES 256 ベースのデータ暗号化キー (DEK) を使用して保護され、このキーは、ご自身のキーを使用して保護されます。 これにより、データとキーを完全に制御できます。 暗号化を許可するには、Recovery Services コンテナーに、Azure Key Vault の暗号化キーへのアクセスが許可されている必要があります。 キーの無効化とアクセスの取り消しは、必要に応じていつでも可能です。 ただし、コンテナーに対する項目の保護を試みるには、事前にキーを使って暗号化を有効にしておく必要があります。

>[!NOTE]
>この機能の利用は、現在、制限されています。 カスタマー マネージド キーを使用して、ご自身のバックアップ データを暗号化することをご希望の方は、[こちらのアンケート](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapURE9TTDRIUEUyNFhNT1lZS1BNVDdZVllHWi4u)にご記入のうえ、メール (AskAzureBackupTeam@microsoft.com) でお送りください。 この機能は、Azure Backup サービスが承認した場合に使用できることに注意してください。

## <a name="backup-of-managed-disk-vms-encrypted-using-customer-managed-keys"></a>カスタマー マネージド キー使用して暗号化されたマネージド ディスク VM のバックアップ

Azure Backup を使用すると、サーバー側の暗号化にキーを使用する Azure VM をバックアップすることもできます。 ディスクの暗号化に使用されるキーは Azure Key Vault に格納され、ご自身で管理します。 カスタマー マネージド キーを使用したサーバー側の暗号化 (SSE) は、Azure Disk Encryption (ADE) とは異なります。ADE は BitLocker (Windows の場合) および DM-Crypt (Linux の場合) を利用してゲスト内暗号化を実行する一方で、SSE はストレージ サービス内のデータを暗号化して、VM の任意の OS またはイメージを使用できるようにするからです。 詳細については、[カスタマー マネージド キーを使用したマネージド ディスクの暗号化](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption#customer-managed-keys)に関するセクションをご覧ください。

## <a name="backup-of-vms-encrypted-using-ade"></a>ADE を使用して暗号化された VM のバックアップ

Azure Backup を使用すると、Azure Disk Encryption を使って暗号化された OS またはデータ ディスクを含む Azure 仮想マシンをバックアップすることもできます。 ADE は、Windows VM の場合は BitLocker を、Linux VM の場合は DM-Crypt 機能を使用して、ゲスト内暗号化を実行します。 詳細については、[Azure Backup での暗号化された仮想マシンのバックアップと復元](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption)に関するページをご覧ください。

## <a name="next-steps"></a>次のステップ

- [暗号化された Azure VM をバックアップおよび復元する](backup-azure-vms-encryption.md)
