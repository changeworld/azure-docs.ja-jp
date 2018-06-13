---
title: Azure Payment Processing Blueprint - 暗号化要件
description: PCI DSS 要件 4
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 43f75ba9-cb4e-49ab-b3f4-09e48310bc18
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: 6de3290fc2147e3c8ed63642b6e8470093898ef6
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/08/2018
ms.locfileid: "33896423"
---
# <a name="encryption-requirements-for-pci-dss-compliant-environments"></a>PCI DSS に準拠する環境の暗号化要件 
## <a name="pci-dss-requirement-4"></a>PCI DSS 要件 4

**オープンなパブリック ネットワークを経由するカード所有者のデータ転送を暗号化する**

> [!NOTE]
> これらの要件は、[PCI データ セキュリティ基準 (DSS) バージョン 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss)の一部として [Payment Card Industry (PCI) Security Standards Council](https://www.pcisecuritystandards.org/pci_security/) によって定義されています。 各要件のテスト手順およびガイダンスについては、PCI DSS をご覧ください。

機密情報は、悪意のあるユーザーが簡単にアクセスできるネットワーク経由で転送する間、暗号化する必要があります。 ワイヤレス ネットワークの構成不備や、従来の暗号化や認証プロトコルの脆弱性は常に悪意のあるユーザーの標的であり、悪意のあるユーザーはこれらの脆弱性を悪用して、カード所有者データ環境へアクセスします。

## <a name="pci-dss-requirement-41"></a>PCI DSS 要件 4.1

**4.1** 次を含む、強力な暗号化とセキュリティ プロトコル (TLS、IPSEC、SSH など) を使用して、オープンなパブリック ネットワーク上転送中のカード所有者の機密データを守ります。
- 信頼できるキーと証明書のみを許容する。
- 使用されるプロトコルが、セキュリティで保護されたバージョンまたは構成のみをサポートする。
- 暗号化の強度が、使用されている暗号化方法に適している。 

> [!NOTE]
> SSL/early TLS を使用する場合は、付録 A2 に記載されている要件を満たす必要があります。
>
> オープンなパブリック ネットワークには次が含まれますが、これらに限りません。
> - インターネット
> - 802.11、Bluetooth などのワイヤレス テクノロジ
> - Global System for Mobile communications (GSM)、Code division multiple access (CDMA) などの携帯電話テクノロジ
> - General Packet Radio Service (GPRS)
> - 衛星通信


**責任:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | 適用不可。 |
| **お客様 <br />(PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore とは、次のように強力な暗号化を実装する PaaS ソリューションです。<br /><br />保存データの暗号化要件を満たすために、[Azure Storage](https://azure.microsoft.com/services/storage/) では以下が使用されます。<br /><br /><ul><li>[Azure Storage Service Encryption (SSE) for Data at Rest](/azure/storage/storage-service-encryption)</li><li>SQL Database: PaaS SQL Database インスタンスを使用して、データベースのセキュリティ対策を実装します。 詳細については、「[PCI Guidance - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database)」を参照してください。</li><li>[Azure Disk Encryption (Bitlocker)](/azure/security/azure-security-disk-encryption)</li></ul>Azure Key Vault を使用して Azure Government、PCI DSS、HIPAA 要件に適合します。|



### <a name="pci-dss-requirement-411"></a>PCI DSS 要件 4.1.1

**4.1.1** カード所有者データを伝送する、またはカード所有者データ環境に接続しているワイヤレス ネットワークで、業界のベスト プラクティス (IEEE 802.11 i など) を使用して強力な暗号化を使用して認証と転送を実行していることを確認します。

**責任:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | 適用不可。 |
| **お客様 <br />(PCI&#8209;DSS&nbsp;Blueprint)** | ソリューションでは、ワイヤレスおよび SNMP は実装されていません。|



## <a name="pci-dss-requirement-42"></a>PCI DSS 要件 4.2

**4.2** 絶対にエンドユーザー メッセージング テクノロジ (たとえば、電子メール、インスタント メッセージング、SMS、チャットなど) を使って保護されていない PAN を送信してはなりません。

**責任:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | 適用不可。 |
| **お客様 <br />(PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore には、保護されていないプライマリ アカウント番号 (PAN) データを送信するメッセージング ソリューションは実装されていません。|



## <a name="pci-dss-requirement-43"></a>PCI DSS 要件 4.3

**4.3** 転送中のカード所有者データを暗号化するためのセキュリティ ポリシーと運用手順を文書化、使用し、すべての関係者に通知します。

**責任:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | 適用不可。 |
| **お客様 <br />(PCI&#8209;DSS&nbsp;Blueprint)** | お客様には、カード所有者データを含む転送を暗号化し文書化する責任があります。|




