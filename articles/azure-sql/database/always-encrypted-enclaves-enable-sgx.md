---
title: Always Encrypted に対して Intel SGX を有効にする
description: SGX 対応のハードウェアの世代を選択することによって、Azure SQL Database でのセキュリティで保護されたエンクレーブが設定された Always Encrypted に対して Intel SGX を有効にする方法について説明します。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviwer: vanto
ms.date: 07/14/2021
ms.openlocfilehash: b967ad27b3f20b75e9b7571a865228508bca9112
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/14/2021
ms.locfileid: "113727258"
---
# <a name="enable-intel-sgx-for-always-encrypted-for-your-azure-sql-database"></a>Azure SQL Database の Always Encrypted に対して Intel SGX を有効にする 

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]


Azure SQL Database の[セキュリティで保護されたエンクレーブが設定された Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-enclaves) では、[Intel Software Guard Extensions (Intel SGX)](https://itpeernetwork.intel.com/microsoft-azure-confidential-computing/) のエンクレーブが使用されます。 Intel SGX を使用できるようにするには、データベースで[仮想コア モデル](service-tiers-vcore.md)と [DC シリーズ](service-tiers-sql-database-vcore.md#dc-series)のハードウェアの世代を使用する必要があります。

Intel SGX エンクレーブを有効にするように DC シリーズのハードウェアの世代を構成することは、Azure SQL Database 管理者の責任です。 「[SGX エンクレーブと構成証明を構成する場合のロールと責任](always-encrypted-enclaves-plan.md#roles-and-responsibilities-when-configuring-sgx-enclaves-and-attestation)」を参照してください。

> [!NOTE]
> Intel SGX は、DC シリーズ以外のハードウェアの世代では使用できません。 たとえば、Intel SGX は Gen5 ハードウェアでは使用できず、[DTU モデル](service-tiers-dtu.md)を使用するデータベースでは使用できません。

> [!IMPORTANT]
> データベースに対して DC シリーズのハードウェアの世代を構成する前に、DC シリーズのリージョン別の提供状況を確認し、そのパフォーマンスの制限を理解するようにしてください。 詳細については、[DC シリーズ](service-tiers-sql-database-vcore.md#dc-series)に関するセクションをご覧ください。

特定のハードウェアの世代を使用するように新規または既存のデータベースを構成する方法の詳細については、「[ハードウェアの世代を選択する](service-tiers-sql-database-vcore.md#selecting-a-hardware-generation)」を参照してください。
   
## <a name="next-steps"></a>次の手順

- [Azure SQL Database サーバー用に Azure Attestation を構成する](always-encrypted-enclaves-configure-attestation.md)

## <a name="see-also"></a>関連項目

- [チュートリアル: Azure SQL Database でのセキュリティで保護されたエンクレーブが設定された Always Encrypted の概要](always-encrypted-enclaves-getting-started.md)
