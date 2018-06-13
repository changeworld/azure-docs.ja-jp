---
title: Azure Payment Processing Blueprint - パスワード要件
description: PCI DSS 要件 2
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 0df24870-6156-4415-a608-dd385b6ae807
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: d3ca83294702a138713f31e34b0e94b1ccb87be0
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/08/2018
ms.locfileid: "33894162"
---
# <a name="password-requirements-for-pci-dss-compliant-environments"></a>PCI DSS に準拠する環境のパスワード要件 
## <a name="pci-dss-requirement-2"></a>PCI DSS 要件 2

**システム パスワードおよびその他のセキュリティのパラメーターに、ベンダーから提供された既定値を使用しないでください。**

> [!NOTE]
> これらの要件は、[PCI データ セキュリティ基準 (DSS) バージョン 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss)の一部として [Payment Card Industry (PCI) Security Standards Council](https://www.pcisecuritystandards.org/pci_security/) によって定義されています。 各要件のテスト手順およびガイダンスについては、PCI DSS をご覧ください。

(企業の外部および内部にいる) 悪意のあるユーザーは多くの場合、システムを危険にさらすベンダー既定のパスワードおよびその他のベンダー既定の設定を利用します。 これらのパスワードと設定はハッカー コミュニティでよく知られており、公開情報を使用して簡単に特定できます。

## <a name="pci-dss-requirement-21"></a>PCI DSS 要件 2.1
 
**2.1** ネットワークにシステムをインストール**する前に**必ずベンダーから提供された既定値を変更し、不要な既定のアカウントを無効にするか削除します。
これはすべての既定のパスワードに適用され、これには、セキュリティ サービス、セキュリティ サービスを提供するアプリケーションおよびシステム アカウント、point-of-sale (POS) 端末、Simple Network Management Protocol (SNMP) のコミュニティ文字列などで使用されるパスワードが含まれます (ただし、これらに限定されません)。

**責任:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | AADUX ポータル内でお客様が入力する新しいパスワードには、Microsoft Azure Active Directory パスワード ポリシーの要件が適用されます。 お客様がセルフ サービスを使ってパスワードを変更するには、以前のパスワードの認証が必要です。 次回ログイン時に管理者リセット パスワードを変更する必要があります。 |
| **お客様 <br />(PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore では、すべてのユーザーに強力なパスワードを使用することが求められます。 デモではサンプルまたはゲスト アカウントは有効化されません。<br /><br />ソリューションでは、ワイヤレスおよび SNMP は実装されていません。|



### <a name="pci-dss-requirement-211"></a>PCI DSS 要件 2.1.1

**2.1.1** カード所有者データ環境に接続している、またはカード所有者データを伝送しているワイヤレス環境の場合、インストール時に、既定のワイヤレス ベンダー規定パスワードを変更してください。これには、既定のワイヤレス暗号化キー、パスワード、SNMP コミュニティ文字列が含まれます (ただし、これらに限定されません)。

**責任:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | 適用不可。 |
| **お客様 <br />(PCI&#8209;DSS&nbsp;Blueprint)** | ソリューションでは、ワイヤレスおよび SNMP は実装されていません。|



## <a name="pci-dss-requirement-22"></a>PCI DSS 要件 2.2

**2.2** すべてのシステム コンポーネントの構成基準を作成します。 これらの標準がすべての既知のセキュリティ脆弱性に対処し、業界で受け入れられているセキュリティ強化標準に準拠していることを確認します。
業界で受け入れられているセキュリティ強化標準には、以下が含まれます (ただし、これらに限定されません)。
- Center for Internet Security (CIS)
- International Organization for Standardization (ISO)
- SysAdmin Audit Network Security (SANS) Institute
- National Institute of Standards Technology (NIST)

**責任:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | Microsoft Azure の OSSC 技術セキュリティ サービス チームは、業界で受け入れられた強化標準に準拠した Microsoft Azure 環境内のシステムのセキュリティ構成の標準を開発します。 これらの構成はシステム ベースラインに記載されており、関連する構成の変更は影響を受けるチーム (IPAK チームなど) に伝達されます。 セキュリティの構成標準への準拠を監視する手続きが実装されています。 Microsoft Azure 環境内のシステムのセキュリティ構成標準は、業界で受け入れられているセキュリティ強化標準に準拠しており、年 1 回以上レビューされます。 |
| **お客様 <br />(PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore は、カード所有者データ環境 (CDE) のスコープ内のすべてのサービスのセキュリティを強化します。 <br /><br />Contoso Webstore はまた、すべての Azure リソースのセキュリティ状態を一元的に把握できる [Azure Security Center](https://azure.microsoft.com/services/security-center/) を実装しています。 セキュリティ制御が適切かつ正しく構成されているかを一目で確認し、注意が必要なリソースを素早く特定できます。<br /><br />Contoso Webstore は、Operations Management Suite を使用して、すべてのシステム変更のログを記録します。 Log Analytics は、広範にわたる変更のログ記録を提供します。 正確性を確保するために、変更内容を確認および検証できます。 具体的なガイダンスについては、[PCI ガイダンス - Operations Management Suite](payment-processing-blueprint.md#logging-and-auditing) を参照してください。|



### <a name="pci-dss-requirement-221"></a>PCI DSS 要件 2.2.1

**2.2.1** 同じサーバーに共存する機能ごとに異なるセキュリティ レベルが必要にならないよう、サーバーの主要な機能は各サーバーに 1 つのみ実装します。 (たとえば、Web サーバー、データベース サーバー、および DNS を別々のサーバーに実装します。) 

> [!NOTE]
> 仮想化テクノロジを使用している場合は、仮想システムコンポーネントの主要な機能は各システムコンポーネントに 1 つのみ実装します。

**責任:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | 適用不可。 |
| **お客様 <br />(PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore のサービスは、PaaS サービスとして配置されます。 すべてのサービスは、ネットワークのセグメント化を使用して分離され、セグメント化されます。<br /><br />Contoso Webstore はまた、[App Service Environment (ASE)](/azure/app-service-web/app-service-app-service-environment-intro) を使用してキー プラクティスを適用します。 詳細については、[PCI ガイダンス - App Service Environment](payment-processing-blueprint.md#app-service-environment) を参照してください。|



### <a name="pci-dss-requirement-222"></a>PCI DSS 要件 2.2.2

**2.2.2** 必須のサービス、プロトコル、デーモンなど、システムの機能に必要なもののみを有効にします。

**責任:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | Microsoft Azure は、四半期に 1 回以上、不要な機能、ポート、プロトコルおよびサービスを特定し、削除するためのソフトウェアおよびハードウェアの構成のレビューを受けます。 |
| **お客様 <br />(PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore のサービスは、PaaS サービスとして配置されます。 すべてのサービスは、ネットワークのセグメント化を使用して分離され、セグメント化されます。<br /><br />Contoso Webstore はまた、[App Service Environment (ASE)](/azure/app-service-web/app-service-app-service-environment-intro) を使用してキー プラクティスを適用します。 詳細については、[PCI ガイダンス - App Service Environment](payment-processing-blueprint.md#app-service-environment) を参照してください。|



### <a name="pci-dss-requirement-223"></a>PCI DSS 要件 2.2.3

**2.2.3** セキュリティ保護されていないと考えられる必要なサービス、プロトコル、またはデーモンの追加のセキュリティ機能を実装します。 

> [!NOTE]
> SSL/early TLS を使用する場合は、付録 A2 に記載されている要件を満たす必要があります。


**責任:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | 適用不可。 |
| **お客様 <br />(PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore のサービスは、PaaS サービスとして配置されます。 すべてのサービスは、ネットワークのセグメント化を使用して分離され、セグメント化されます。 展開には、CDE のスコープ内のすべてのサービスのセキュリティ強化も提供されています。 <br /><br />Contoso Webstore はまた、すべての Azure リソースのセキュリティ状態を一元的に把握できる [Azure Security Center](https://azure.microsoft.com/services/security-center/) を実装しています。 セキュリティ制御が適切かつ正しく構成されているかを一目で確認し、注意が必要なリソースを素早く特定できます。<br /><br />Contoso Webstore はまた、[App Service Environment (ASE)](/azure/app-service-web/app-service-app-service-environment-intro) を使用してキー プラクティスを適用します。 詳細については、[PCI ガイダンス - App Service Environment](payment-processing-blueprint.md#app-service-environment) を参照してください。|



### <a name="pci-dss-requirement-224"></a>PCI DSS 要件 2.2.4

**2.2.4** 誤用や悪用が起きないよう、システムのセキュリティ パラメーターを構成します。

**責任:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | Azure では、承認されているユーザーのみが、多要素アクセス制御と文書化されビジネス ニーズを使用して、Azure プラットフォームのセキュリティ コントロールを構成することができます。 |
| **お客様 <br />(PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore では、セキュリティ パラメーターを管理し、正しく展開するために AAD および AD RBAC を使用しています。 詳細については、[PCI ガイダンスの ID 管理](payment-processing-blueprint.md#identity-management)に関する記事を参照してください。|



### <a name="pci-dss-requirement-225"></a>PCI DSS 要件 2.2.5

**2.2.5** スクリプト、ドライバー、機能、サブシステム、ファイル システム、および不要な Web サーバーなど、不要な機能をすべて削除します。

**責任:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | 適用不可。 |
| **お客様 <br />(PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore では、境界を確立する方法について説明したドキュメントが提供されています。 Contoso Webstore の脅威モデルとデータ フロー ダイアグラムは、すべての使用されているサービスと有効になっているコントロールを示しています。|



## <a name="pci-dss-requirement-23"></a>PCI DSS 要件 2.3

**2.3** 強力な暗号を使用して、コンソール以外からのすべての管理アクセスを暗号化します。 

> [!NOTE]
> SSL/early TLS を使用する場合は、付録 A2 に記載されている要件を満たす必要があります。

**責任:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | Microsoft Azure では、ハイパーバイザー インフラストラクチャへのアクセスに強力な暗号化が適用されます。 また、Microsoft Azure では、お客様が Microsoft Azure の管理ポータルからサービス/IaaS コンソールにアクセスする際にも強力な暗号化を使用します。 |
| **お客様 <br />(PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore は、ソリューション内で強力なパスワードを実装する方法を示すだけでなく、ソリューション全体でその暗号化が実装されていることを検証するためのすべてのテストを実行します。<br /><br />Contoso Webstore はまた、[App Service Environment (ASE)](/azure/app-service-web/app-service-app-service-environment-intro) を使用してキー プラクティスを適用します。 詳細については、[PCI ガイダンス - App Service Environment](payment-processing-blueprint.md#app-service-environment) を参照してください。|



## <a name="pci-dss-requirement-24"></a>PCI DSS 要件 2.4

**2.4** PCI DSS のスコープ内のシステム コンポーネントのインベントリを管理します。

**責任:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | 適用不可。 |
| **お客様 <br />(PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore デモ PaaS ソリューションのインベントリは、提供されているドキュメントで確認できます。 詳しくは、[PCI ガイダンス - インストール済み管理ソリューション](payment-processing-blueprint.md#management-solutions)に関するページをご覧ください。|



## <a name="pci-dss-requirement-25"></a>PCI DSS 要件 2.5

**2.5** セキュリティ ポリシーとベンダー既定設定およびその他のセキュリティ パラメーターを管理するためのセキュリティ ポリシーと運用手順が文書化され、使用され、すべての関係者に通知されていることを確認してください。

**責任:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | 適用不可。 |
| **お客様 <br />(PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore は、セキュリティ パラメーターに関する洞察につながるドキュメントを提供し、サービス要素を文書化します。 |



## <a name="pci-dss-requirement-26"></a>PCI DSS 要件 2.6

**2.6** 共有ホスティング プロバイダーは、各企業のホスティング環境とカード所有者データを保護する必要があります。 これらのプロバイダーは、*付録 A: 共有ホスティング プロバイダーの追加 PCI DSS 要件*で説明されている特定の要件を満たす必要があります。

**責任:&nbsp;&nbsp;`Not Applicable`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | 適用不可。 Microsoft Azure は、共有ホスティング プロバイダーではありません。 |
| **お客様 <br />(PCI&#8209;DSS&nbsp;Blueprint)** | 適用不可。 Microsoft Azure は、共有ホスティング プロバイダーではありません。|




