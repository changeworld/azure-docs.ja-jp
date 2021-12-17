---
title: Azure SQL Database で Intel SGX エンクレーブと構成証明を計画する
description: Azure SQL Database でのセキュリティで保護されたエンクレーブが設定された Always Encrypted のデプロイを計画します。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviwer: vanto
ms.date: 07/14/2021
ms.openlocfilehash: 18700a89b94a1fb739e279230ab6f2b172ab6671
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132306512"
---
# <a name="plan-for-intel-sgx-enclaves-and-attestation-in-azure-sql-database"></a>Azure SQL Database で Intel SGX エンクレーブと構成証明を計画する

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure SQL Database の[セキュリティで保護されたエンクレーブが設定された Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-enclaves) では、[Intel Software Guard Extensions (Intel SGX)](https://itpeernetwork.intel.com/microsoft-azure-confidential-computing/) のエンクレーブが使用され、[Microsoft Azure Attestation](/sql/relational-databases/security/encryption/always-encrypted-enclaves#secure-enclave-attestation) が必要です。

## <a name="plan-for-intel-sgx-in-azure-sql-database"></a>Azure SQL Database で Intel SGX を計画する

Intel SGX は、ハードウェアベースの高信頼実行環境テクノロジです。 Intel SGX は、[仮想コア モデル](service-tiers-sql-database-vcore.md)と [DC シリーズ](service-tiers-sql-database-vcore.md?#dc-series)のハードウェアの世代を使用するデータベースで使用できます。 そのため、データベース内で、セキュリティで保護されたエンクレーブが設定された Always Encrypted を使用できるようにするには、データベースの作成時に DC シリーズのハードウェアの世代を選択するか、DC シリーズのハードウェアの世代を使用するように既存のデータベースを更新する必要があります。

> [!NOTE]
> Intel SGX は、DC シリーズ以外のハードウェアの世代では使用できません。 たとえば、Intel SGX は Gen5 ハードウェアでは使用できず、[DTU モデル](service-tiers-dtu.md)を使用するデータベースでは使用できません。

> [!IMPORTANT]
> データベースに対して DC シリーズのハードウェアの世代を構成する前に、DC シリーズのリージョン別の提供状況を確認し、そのパフォーマンスの制限を理解するようにしてください。 詳細については、[DC シリーズ](service-tiers-sql-database-vcore.md#dc-series)に関する記事を参照してください。

## <a name="plan-for-attestation-in-azure-sql-database"></a>Azure SQL Database での構成証明を計画する

<<<<<<< HEAD
[Microsoft Azure Attestation](../../attestation/overview.md) (プレビュー) は、DC シリーズのハードウェアの世代を使用した Azure SQL Database 内の Intel SGX エンクレーブを含む、高信頼実行環境 (TEE) を証明するためのソリューションです。

Azure SQL Database 内で Intel SGX エンクレーブを証明するために Azure Attestation を使用するには、次のことを行う必要があります。

1. [構成証明プロバイダー](../../attestation/basic-concepts.md#attestation-provider)を作成し、構成証明ポリシーを使用して構成します。 
=======
[Microsoft Azure Attestation](../../attestation/overview.md) は、DC シリーズのハードウェアの世代を使用した Azure SQL データベース内の Intel SGX エンクレーブを含む、高信頼実行環境 (TEE) を証明するためのソリューションです。
>>>>>>> repo_sync_working_branch

Azure SQL Database で Intel SGX エンクレーブの構成証明に Azure Attestation を使用するには、[構成証明プロバイダー](../../attestation/basic-concepts.md#attestation-provider)を作成し、Microsoft 提供の構成証明ポリシーを使用して構成する必要があります。 「[Azure Attestation を使用して Always Encrypted の構成証明を構成する](always-encrypted-enclaves-configure-attestation.md)」をご覧ください

## <a name="roles-and-responsibilities-when-configuring-sgx-enclaves-and-attestation"></a>SGX エンクレーブと構成証明を構成する場合のロールと責任

Azure SQL Database での Always Encrypted に対する Intel SGX エンクレーブと構成証明をサポートするように環境を構成するには、さまざまな種類のコンポーネントを設定する必要があります。これには、Microsoft Azure Attestation、Azure SQL Database、エンクレーブ構成証明をトリガーするアプリケーションがあります。 それぞれの種類のコンポーネントの構成は、以下の異なるロールのいずれかを引き受けるユーザーによって実行されます。

- 構成証明管理者 - Microsoft Azure Attestation 内で構成証明プロバイダーを作成し、構成証明ポリシーを作成し、Azure SQL 論理サーバーに構成証明プロバイダーへのアクセスを付与し、ポリシーを指す構成証明 URL をアプリケーション管理者に共有します。
- Azure SQL Database 管理者 - DC シリーズのハードウェアの世代を選択することでデータベースでの SGX エンクレーブを有効にし、構成証明プロバイダーにアクセスする必要がある Azure SQL 論理サーバーの ID を構成証明管理者に提供します。
- アプリケーション管理者 - 構成証明管理者から取得した構成証明 URL でアプリケーションを構成します。

(実際の機密データを扱う) 運用環境では、構成証明を構成するときに組織がロールの分離に従うことが重要です。その場合、さまざまなユーザーがそれぞれ異なるロールを引き受けます。 特に、Always Encrypted を組織にデプロイする目的が、Azure SQL Database 管理者が機密データにアクセスできないようにすることにより攻撃対象領域を減らすことである場合、Azure SQL Database 管理者は構成証明ポリシーを制御できません。

## <a name="next-steps"></a>次の手順

- [Azure SQL Database に対して Intel SGX を有効にする](always-encrypted-enclaves-enable-sgx.md)

## <a name="see-also"></a>関連項目

- [チュートリアル: Azure SQL Database でのセキュリティで保護されたエンクレーブが設定された Always Encrypted の概要](always-encrypted-enclaves-getting-started.md)
