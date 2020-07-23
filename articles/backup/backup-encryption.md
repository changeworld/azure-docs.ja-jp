---
title: Azure Backup での暗号化
description: Azure Backup の暗号化機能を利用して、バックアップ データを保護し、ビジネスのセキュリティ ニーズを満たす方法について説明します。
ms.topic: conceptual
ms.date: 04/30/2020
ms.custom: references_regions
ms.openlocfilehash: 099e736bfb321f0f92bd3a57f9c24e88293b42bb
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2020
ms.locfileid: "86538753"
---
# <a name="encryption-in-azure-backup"></a>Azure Backup での暗号化

クラウドに保存されているバックアップ データは、Azure Storage 暗号化を使用して自動的に暗号化されます。これは、セキュリティとコンプライアンスのコミットメントを満たすのに役立ちます。 この保存データは、利用可能な最強のブロック暗号の 1 つである 256 ビット AES 暗号化を使って暗号化され、FIPS 140-2 に準拠しています。

保存データの暗号化に加えて、転送中のすべてのバックアップ データが HTTPS 経由で転送されます。 これは、Azure バックボーン ネットワークにとどまります。

詳細については、「[保存データ向け Azure ストレージの暗号化](../storage/common/storage-service-encryption.md)」をご覧ください。 暗号化に関する質問の回答については、[Azure Backup の FAQ](./backup-azure-backup-faq.md#encryption) のページを参照してください。

## <a name="encryption-of-backup-data-using-platform-managed-keys"></a>プラットフォーム マネージド キーを使用したバックアップ データの暗号化

既定では、すべてのデータが、プラットフォーム マネージド キーを使用して暗号化されます。 この暗号化は、ユーザー側から明示的なアクションを実行しなくても有効になり、Recovery Services コンテナーにバックアップ中のすべてのワークロードに適用されます。

## <a name="encryption-of-backup-data-using-customer-managed-keys"></a>カスタマー マネージド キーを使用したバックアップ データの暗号化

Azure Virtual Machines のバックアップ時に、ご自身で所有および管理しているキーを使って、データを暗号化できるようになりました。 Azure Backup を使用すると、自分のバックアップの暗号化に、Azure Key Vault に格納されているご自身の RSA キーを使用でききます。 バックアップの暗号化に使用される暗号化キーは、ソースに使用されているものと異なることがあります。 データは、AES 256 ベースのデータ暗号化キー (DEK) を使用して保護され、このキーは、ご自身のキーを使用して保護されます。 これにより、データとキーを完全に制御できます。 暗号化を許可するには、Recovery Services コンテナーに、Azure Key Vault の暗号化キーへのアクセスが許可されている必要があります。 キーの無効化とアクセスの取り消しは、必要に応じていつでも可能です。 ただし、コンテナーに対する項目の保護を試みるには、事前にキーを使って暗号化を有効にしておく必要があります。

カスタマー マネージド キーを使用してバックアップ データを暗号化する方法の詳細については、[こちら](encryption-at-rest-with-cmk.md)を参照してください。

## <a name="backup-of-managed-disk-vms-encrypted-using-customer-managed-keys"></a>カスタマー マネージド キー使用して暗号化されたマネージド ディスク VM のバックアップ

Azure Backup を使用すると、[ストレージ サービス暗号化](../storage/common/storage-service-encryption.md)にキーを使用する Azure VM をバックアップすることもできます。 ディスクの暗号化に使用されるキーは Azure Key Vault に格納され、ご自身で管理します。 カスタマー マネージド キーを使用した Storage Service Encryption (SSE) は、Azure Disk Encryption (ADE) とは異なります。ADE は BitLocker (Windows の場合) および DM-Crypt (Linux の場合) を利用してゲスト内暗号化を実行する一方で、SSE はストレージ サービス内のデータを暗号化して、VM の任意の OS またはイメージを使用できるようにするからです。 詳細については、[カスタマー マネージド キーを使用したマネージド ディスクの暗号化](../virtual-machines/windows/disk-encryption.md#customer-managed-keys)に関するセクションをご覧ください。

## <a name="infrastructure-level-encryption-for-backup-data"></a>バックアップ データのインフラストラクチャ レベルの暗号化

カスタマー マネージド キーを使用して Recovery Services コンテナー内のデータを暗号化するだけでなく、ストレージ インフラストラクチャで追加の暗号化レイヤーを構成することもできます。 このインフラストラクチャの暗号化はプラットフォームによって管理され、これに加えてカスタマー マネージド キーを使用して保存時の暗号化を行うことで、バックアップ データを 2 レイヤーで暗号化することができます。 インフラストラクチャの暗号化は、保存時の暗号化に独自のキーを使用する場合にのみ構成できることに注意してください。 インフラストラクチャの暗号化では、データの暗号化にプラットフォーム マネージド キーを使用します。

>[!NOTE]
>現在、インフラストラクチャの暗号化は限定プレビュー段階であり、米国東部、米国西部 2、米国中南部、US Gov アリゾナ、および US GOV バージニア リージョンでのみご利用いただけます。 これらのリージョンのいずれかでこの機能を使用する場合は、[こちらのフォーム](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapUN0VHNEpJS0ZUWklUNVdJSTEzR0hIOVRMVC4u)に記入し、[AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) にお問い合わせください。

## <a name="backup-of-vms-encrypted-using-ade"></a>ADE を使用して暗号化された VM のバックアップ

Azure Backup を使用すると、Azure Disk Encryption を使って暗号化された OS またはデータ ディスクを含む Azure 仮想マシンをバックアップすることもできます。 ADE は、Windows VM の場合は BitLocker を、Linux VM の場合は DM-Crypt 機能を使用して、ゲスト内暗号化を実行します。 詳細については、[Azure Backup での暗号化された仮想マシンのバックアップと復元](./backup-azure-vms-encryption.md)に関するページをご覧ください。

## <a name="next-steps"></a>次のステップ

- [暗号化された Azure VM をバックアップおよび復元する](backup-azure-vms-encryption.md)
