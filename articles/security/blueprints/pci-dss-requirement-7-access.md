---
title: Azure Payment Processing Blueprint - アクセス要件
description: PCI DSS 要件 7
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: ac3afee9-0471-465d-a115-67488a1635a6
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: fb16a7973022421525e13313f53f615120caa38a
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/08/2018
ms.locfileid: "33895040"
---
# <a name="access-requirements-for-pci-dss-compliant-environments"></a>PCI DSS に準拠する環境のアクセス要件 
## <a name="pci-dss-requirement-7"></a>PCI DSS 要件 7

**業務上の知る必要によってカード所有者データへのアクセスを制限する**

> [!NOTE]
> これらの要件は、[Payment Card Industry (PCI) セキュリティ スタンダード協議会](https://www.pcisecuritystandards.org/pci_security/) によって、[PCI データ セキュリティ基準 (DSS) バージョン 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss) の一部として定義されています。 各要件のテスト手順およびガイダンスについては、PCI DSS をご覧ください。

承認された職員のみが重要なデータにアクセスできることを保証するために、知る必要性と職責に基づいてアクセスを制限するシステムとプロセスを用意する必要があります。

"知る必要性" とは、職務を遂行するために必要な最小限の量のデータと権限のみを持つアクセス権を与えることです。

## <a name="pci-dss-requirement-71"></a>PCI DSS 要件 7.1

**7.1** システム コンポーネントとカード所有者データへのアクセスは、職務上そのようなアクセスを必要とする個人のみに制限する。

**責任:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | Azure は、Azure 職員による Azure システム コンポーネントへのアクセス、アクセス制御の有効性の検証、Just-In-Time 管理アクセスの提供、不要になったときのアクセス許可の取り消し、および Azure プラットフォームにアクセスするスタッフが業務上アクセスする必要があることの保証に関する既存の ISMS ポリシーを適用します。 お客様の環境への Azure のアクセスは厳しく制限され、お客様の承認があった場合のみ許可されます。<br /><br />データセンターへの物理的なアクセスを承認された従業員、ベンダー、請負業者、および訪問者に制限する手順が確立されています。 データ センターの内部施設への一時的なアクセスを必要とする職員には、セキュリティ検証とチェックインが必要です。 物理アクセス ログは、Azure チームによって、四半期ごとに確認されます。 |
| **お客様 <br />(PCI&#8209;DSS&nbsp;Blueprint)** | お客様は、システム コンポーネントとカード所有者データへのアクセスを、職務上そのようなアクセスを必要とする個人のみに制限する責任があります。 これには、Azure Management Portal へのアクセスの制限と、PaaS サービスを作成、変更、または削除する権限を持つアカウントまたは役割の指定が含まれます。|



### <a name="pci-dss-requirement-711"></a>PCI DSS 要件 7.1.1

**7.1.1** 各役割が必要とするアクセス権を定義する。
- 各役割がその職務権限でアクセスする必要があるシステム コンポーネントとデータ リソース
- リソースにアクセスするために必要な特権 (ユーザー、管理者など) のレベル

**責任:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | 適用不可。 |
| **お客様 <br />(PCI&#8209;DSS&nbsp;Blueprint)** | お客様は、ユーザー ID 承認プロセスの定義と文書化、最小限の特権の定義、カード所有者データへのアクセスの制限、一意の ID の使用、職務の分離の提供、および不要になったときのユーザー アクセスの取り消しを行う責任があります。|



### <a name="pci-dss-requirement-712"></a>PCI DSS 要件 7.1.2

**7.1.2** 特権のあるユーザー ID へのアクセスを、職責を果たすために必要な最小限の特権に制限する。

**責任:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | Microsoft Azure では、情報セキュリティ ポリシーを含む、適用可能な企業と組織のセキュリティ ポリシーを採用しています。 これらのポリシーは、承認され、公開され、Microsoft Azure に伝達されています。 Microsoft Azure の情報セキュリティ ポリシーでは、Microsoft Azure 資産へのアクセス権は、業務上の正当な理由、資産の所有者の承認、および "知る必要性" と "最小限の特権" の原則に基づいて付与されます。 このポリシーは、アクセスのプロビジョニング、アクセスの承認、アクセス権限の承認の削除、アクセスの定期的なレビューを含むアクセス管理ライフサイクルの要件にも対応します。 |
| **お客様 <br />(PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore では、デプロイ時に 3 つのアカウント (admin、sqladmin、および edna) を作成します (edna はデモの実行中にこの Web アプリにログインする既定のユーザーです)。 ユーザーの役割は、文書化されているデモ シナリオに基づく職務に制限されます。|



### <a name="pci-dss-requirement-713"></a>PCI DSS 要件 7.1.3

**7.1.3** 個々 の職員の職階と職務権限に基づいてアクセス権を割り当てる。

**責任:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | 適用不可。 |
| **お客様 <br />(PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore では、デプロイ時に 3 つのアカウント (admin、sqladmin、および edna) を作成します (edna はデモの実行中にこの Web アプリにログインする既定のユーザーです)。 ユーザーの役割は、文書化されているデモ シナリオに基づく職務に制限されます。|



### <a name="pci-dss-requirement-714"></a>PCI DSS 要件 7.1.4

**7.1.4** 必要な特権を指定している、承認された関係者による文書化された承認が必要。

**責任:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | 適用不可。 |
| **お客様 <br />(PCI&#8209;DSS&nbsp;Blueprint)** | お客様は、システム コンポーネントとカード所有者データへのアクセスを、職務上そのようなアクセスを必要とする個人のみに制限する責任があります。 これには、Azure Management Portal へのアクセスの制限と、PaaS サービスを作成、変更、または削除する権限を持つアカウントまたは役割の指定が含まれます。|



## <a name="pci-dss-requirement-72"></a>PCI DSS 要件 7.2

**7.2** システム コンポーネントに対するユーザーのアクセスを、知る必要性に基づいて制限し、明確に許可されない限り、"すべて拒否" が設定されたアクセス制御システムを確立する。
このアクセス制御システムには、以下が含まれている必要があります。
- 7.2.1 すべてのシステム コンポーネントが対象。
- 7.2.2 職階と職務権限に基づく個人に対する特権の割り当て。
- 7.2.3 "すべて拒否" という既定の設定。

**責任:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | 適用不可。 |
| **お客様 <br />(PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore では、Azure Active Directory を使用して、指定されたユーザーのみにアクセスを制限します。 詳細については、[PCI ガイダンスの ID 管理](payment-processing-blueprint.md#identity-management)に関する記事を参照してください。|



## <a name="pci-dss-requirement-73"></a>PCI DSS 要件 7.3

**7.3** カード所有者データへのアクセスを制限するためのセキュリティ ポリシーと運用手順を文書化し、使用し、すべての関係者に通知する。

**責任:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | 適用不可。 |
| **お客様 <br />(PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore のドキュメントに、ユースケース、CHD の使用者、および CHD の使用方法が説明されています。|




