---
title: FedRAMP High Impact Level を満たすための Azure Active Directory の構成
description: Azure Active Directory を使用して、組織の FedRAMP High Impact Level を満たす方法の概要。
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: barbaraselden
ms.author: baselden
manager: mtillman
ms.reviewer: martinco
ms.date: 4/26/2021
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: e1cc2d2bf4a51a0d50c47d41982ced9962a977e2
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132302052"
---
# <a name="configure-azure-active-directory-to-meet-fedramp-high-impact-level"></a>FedRAMP High Impact Level を満たすための Azure Active Directory の構成

[Federal Risk and Authorization Management Program](https://www.fedramp.gov/) (FedRAMP) は、クラウド サービス プロバイダー (CSP) に対する評価および認可プロセスです。 具体的には、連邦政府機関で使用するためのクラウド ソリューション オファリング (CSO) を作成する CSP に対するプロセスです。 Azure と Azure Government は、FedRAMP 認定の最高位である、[High Impact Level の Provisional Authority to Operate (P-ATO)](/compliance/regulatory/offering-fedramp) を合同認定委員会から獲得しています。

Azure には、CSO に対して、または連邦機関として、FedRAMP の高評価を達成するためのすべてのコントロール要件を満たす機能が用意されています。 準拠させるために追加の構成またはプロセスを実行することは、組織の責任です。 この責任は、CSO の FedRAMP High 認可を求めている CSP と、Authority to Operate (ATO) を求めている連邦機関のどちらにも当てはまります。 

## <a name="microsoft-and-fedramp"></a>マイクロソフトと FedRAMP 

Microsoft Azure では、他のどの CSP よりも多くの [Fedramp High Impact](../../azure-government/compliance/azure-services-in-fedramp-auditscope.md) レベルのサービスをサポートしています。 また、Azure パブリック クラウド内のこのレベルは、多くの米国政府のお客様のニーズを満たしますが、より厳格な要件を持つ機関では、Azure Government に依存する場合があります。 Azure Government では、職員の高度なスクリーニングなどの追加の保護機能が提供されます。 

Microsoft では、その認可を維持するために、毎年クラウド サービスの再認定が必要とされます。 これを行うために、Microsoft は継続的にセキュリティ コントロールを監視して評価し、サービスのセキュリティが引き続き準拠していることを明示しています。 詳細については、「[Microsoft クラウド サービスの FedRAMP 認可](https://marketplace.fedramp.gov/)」および「[Microsoft FedRAMP 監査レポート](https://aka.ms/MicrosoftFedRAMPAuditDocuments)」を参照してください。 他の FedRAMP レポートを受け取るには、[Azure Federal Documentation](mailto:AzFedDoc@microsoft.com)に電子メールを送信します。

FedRAMP 認可を目指すには複数のパスがあります。 Azure の既存の認可パッケージとここのガイダンスを再利用して、ATO または P-ATO を取得するために必要な時間と労力を大幅に削減することができます。 

## <a name="scope-of-guidance"></a>ガイダンスのスコープ

FedRAMP High ベースラインは、[NIST 800-53 Security Controls Catalog Revision 4](https://csrc.nist.gov/publications/detail/sp/800-53/rev-4/final) から 421 個のコントロールおよびコントロール拡張機能で構成されます。 該当する場合は、[800-53 Revision 5](https://csrc.nist.gov/publications/detail/sp/800-53/rev-5/final) の明確な情報を記載しました。 この記事では、ID に関連するこれらのコントロールのサブセットで、構成する必要があるものについて説明します。 

Azure Active Directory (Azure AD) で構成を担当するコントロールのコンプライアンスの実現に役立つ規範的なガイダンスを提供します。 一部の ID コントロール要件に完全に対処するには、他のシステムの使用が必要になる場合があります。 他のシステムには、Microsoft Sentinel などのセキュリティ情報イベント管理ツールが考えられます。 Azure Active Directory の外部で Azure サービスを使用している場合、他にも考慮する必要があるコントロールがありますが、Azure に既に用意されている機能を使用して、コントロールを満たすことができます。

FedRAMP リソースの一覧を次に示します。

* [Federal Risk and Authorization Management Program](https://www.fedramp.gov/)

* [FedRAMP Security Assessment Framework](https://www.fedramp.gov/assets/resources/documents/FedRAMP_Security_Assessment_Framework.pdf)

* [FedRAMP 認可のための機関ガイド](https://www.fedramp.gov/assets/resources/documents/Agency_Guide_for_Reuse_of_FedRAMP_Authorizations.pdf)

* [Microsoft のクラウドでのコンプライアンスの管理](https://www.microsoft.com/trustcenter/common-controls-hub)

* [Microsoft Government Cloud](https://go.microsoft.com/fwlink/p/?linkid=2087246)

* [Azure コンプライアンス認証](https://aka.ms/azurecompliance)

* [FedRAMP High Azure Policy の組み込みイニシアチブの定義](../../governance/policy/samples/fedramp-high.md)

* [Microsoft 365 コンプライアンス センター](/microsoft-365/compliance/microsoft-365-compliance-center)

* [Microsoft コンプライアンス マネージャー](/microsoft-365/compliance/compliance-manager)

## <a name="next-steps"></a>次の手順

[アクセス制御の構成](fedramp-access-controls.md)

[ID および認証のコントロールを構成する](fedramp-identification-and-authentication-controls.md)

[その他のコントロールの構成](fedramp-other-controls.md)
