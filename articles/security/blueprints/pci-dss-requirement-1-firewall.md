---
title: "Azure Payment Processing Blueprint - ファイアウォールの要件"
description: "PCI DSS 要件 1"
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: b1935d88-acae-42f9-bc25-bb0766f876ab
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: 995ecd5ef876695145fc6313aba2a46d2cc085cc
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2017
---
# <a name="firewall-requirements-for-pci-dss-compliant-environments"></a>PCI DSS に準拠する環境のファイアウォール要件 
## <a name="pci-dss-requirement-1"></a>PCI DSS 要件 1

**カード所有者データを保護するファイアウォール構成のインストールおよび維持**

> [!NOTE]
> これらの要件は、[PCI データ セキュリティ基準 (DSS) バージョン 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss)の一部として [Payment Card Industry (PCI) Security Standards Council](https://www.pcisecuritystandards.org/pci_security/) によって定義されています。 各要件のテスト手順およびガイダンスについては、PCI DSS を参照してください。

ファイアウォールは、企業のネットワーク (内部) と信頼されていないネットワーク (外部) 間で許可されるコンピューター トラフィックと、企業内部にある信頼済みネットワーク内の機密性の高い領域の送受信トラフィックを制御するデバイスです。 カード所有者データ環境は、企業の信頼済みネットワーク内の機密性の高い領域の一例です
ファイアウォールは、すべてのネットワーク トラフィックを検査し、指定されたセキュリティ基準を満たしていない伝送をブロックします。
すべてのシステムは、信頼されていないネットワークからの不正なアクセスから保護する必要があり、これには E コマースとしてインターネット経由でシステムに侵入する場合、従業員によるデスクトップ ブラウザ経由のインターネットへのアクセス、従業員による電子メールへのアクセス、企業間接続などの専用接続、ワイヤレス ネットワーク経由、その他のソース経由のアクセスが含まれます。 多くの場合、信頼されていないネットワークから送受信される、一見重要ではないように見える経路が、保護されていない重要なシステムへの侵入口になります。 ファイアウォールは、あらゆるコンピューター ネットワークの主要な保護メカニズムです。
多くの場合、要件 1 に定義されているファイアウォールの最小要件を満たしている限り、他のシステム コンポーネントがファイアウォール機能を提供できます。 カード所有者データ環境内で使用されている他のシステム コンポーネントがファイアウォール機能を提供する場合は、これらのデバイスを要件 1 のスコープと評価に含める必要があります。

## <a name="pci-dss-requirement-11"></a>PCI DSS 要件 1.1

**1.1** 以下 (1.1.1 ～ 1.1.7 参照) を含む、ファイアウォールおよびルーター構成標準を規定し実装します。


**責任:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | 適用不可。 |
| **お客様<br /> (PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore は PaaS 分離を使用した CDE のファイアウォール機能を提供し、App Service Environment を実装することによりデータの CDE イングレスおよびエグレスが保護されます。<br /><br />[App Service Environment (ASE)](/azure/app-service-web/app-service-app-service-environment-intro) は、コンプライアンス上の理由で使用される Premium サービス プランです。 ASE コントロールと構成の詳細については、[PCI ガイダンス - App Service Environment](payment-processing-blueprint.md#app-service-environment) を参照してください。|



### <a name="pci-dss-requirement-111"></a>PCI DSS 要件 1.1.1

**1.1.1** すべてのネットワーク接続およびファイアウォールやルーターの構成への変更を承認およびテストする公式のプロセス。


**責任:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | 適用不可。 |
| **お客様<br /> (PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore インスタンスが、すべての変更を正しく管理するための CI/CD DevOps モデルを確立します。 [Operations Management Suite (OMS)](/azure/operations-management-suite/) は、変更の詳細なログ記録を提供します。 変更をレビュー、検証して正確性を確認できます。 具体的なガイダンスについては、[PCI ガイダンス - Operations Management Suite](payment-processing-blueprint.md#logging-and-auditing) を参照してください。<br /><br />[Azure Security Center](https://azure.microsoft.com/services/security-center/) は、すべての Azure リソースのセキュリティ状態を一元的に提供します。 セキュリティ制御が適切かつ正しく構成されているかを一目で確認し、注意が必要なリソースを素早く特定できます。|



### <a name="pci-dss-requirement-112"></a>PCI DSS 要件 1.1.2

**1.1.2** カード所有者データ環境とすべてのワイヤレス ネットワークを含む、他のネットワークの間のすべての接続を特定できる最新のネットワーク図

**責任:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | 適用不可。 |
| **お客様<br /> (PCI&#8209;DSS&nbsp;Blueprint)** | ソリューションのインストール パターンの一部として提供される Contoso Webstore 参照アーキテクチャとデザイン ドキュメントを参照してください。|



### <a name="pci-dss-requirement-113"></a>PCI DSS 要件 1.1.3

**1.1.3** システムおよびネットワークにわたるすべてのカード所有者データの流れを示す最新の図

**責任:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | 適用不可。 |
| **お客様<br /> (PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore DFD と脅威モデルを参照してください。|



### <a name="pci-dss-requirement-114"></a>PCI DSS 要件 1.1.4

**1.1.4** 各インターネット接続と、非武装地帯 (DMZ) と内部ネットワーク ゾーン間のファイアウォールの要件

**責任:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | Microsoft Azure では、ゲートウェイ、ネットワーク ACL などの境界保護デバイスと、アプリケーション ファイアウォールを使用して、外部および内部境界の通信をプラットフォーム レベルで制御しています。 お客様は、これらの仕様と要件を構成します。 Microsoft Azure は、プラットフォームに入ってくる通信をフィルター処理します。 |
| **お客様<br /> (PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore は PaaS 分離を使用して DMZ を提供し、App Service Environment を実装することによりデータの CDE イングレスおよびエグレスが保護されます。<br /><br />[App Service Environment (ASE)](/azure/app-service-web/app-service-app-service-environment-intro) は、コンプライアンス上の理由で使用される Premium サービス プランです。 ASE コントロールと構成の詳細については、[PCI ガイダンス - App Service Environment](payment-processing-blueprint.md#app-service-environment) を参照してください。|



### <a name="pci-dss-requirement-115"></a>PCI DSS 要件 1.1.5

**1.1.5** ネットワーク コンポーネントを管理するグループ、ロール、および責任の説明

**責任:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | 適用不可。 |
| **お客様<br /> (PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore は、[Azure Role-Based Access Control (RBAC)](/azure/active-directory/role-based-access-control-configure) を使用してユーザー ロールを分離します。 RBAC は、Azure に正確に対象を絞ったアクセス管理を可能にします。 サブスクリプション アクセスおよび Azure Key Vault のアクセスのための特定の構成が存在する必要があります。|



### <a name="pci-dss-requirement-116"></a>PCI DSS 要件 1.1.6

**1.1.6** 業務の妥当性、およびすべてのサービス、プロトコル、許容されるポートの使用の承認のドキュメント。これには、セキュリティ上安全でないと考えられるプロトコルに実装されるセキュリティ機能に関するドキュメントが含まれます。

**責任:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | 適用不可。 |
| **お客様<br /> (PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore では、RA 設計全体で必要なポートとプロトコルのみを開きます。 データ フローの詳細については、DFD と脅威のモデルを参照してください。|



### <a name="pci-dss-requirement-117"></a>PCI DSS 要件 1.1.7

**1.1.7** 最低でも 6 か月ごとに、ファイアウォールおよびルーターのルール セットをレビューする要件

**責任:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | 適用不可。 |
| **お客様<br /> (PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore は、ファイアウォール ルール セットに、不要な、または使用されていないルールが含まれていないかレビューします。 デモは、仕様として、最低限の特権とパス フットプリントで展開されています。|



## <a name="pci-dss-requirement-12"></a>PCI DSS 要件 1.2

**1.2** カード所有者のデータ環境のシステム コンポーネントと、信頼されていないネットワークの間の接続を制限するファイアウォールやルーター構成を構築します。 

> [!NOTE]
> "信頼されていないネットワーク" とは、レビュー対象の企業に属するネットワークの外にあり、および/または企業の制御または管理能力を超えるネットワークです。

**責任:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | 適用不可。 |
| **お客様<br /> (PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore の CDE は、RA およびデプロイ ドキュメントに定義されています。 仕様として、信頼されていないネットワークは拒否されます。|



### <a name="pci-dss-requirement-121"></a>PCI DSS 要件 1.2.1

**1.2.1** カード所有者データ環境に必要なものだけに受信および送信トラフィックを限定し、その他のすべてのトラフィックを明示的に拒否します。

**責任:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | 適用不可。 |
| **お客様<br /> (PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore の CDE は、RA およびデプロイ ドキュメントに定義されています。 仕様として、信頼されていないネットワークは拒否されます。 Contoso Webstore デモでは、IP アドレスの指定した範囲のみが Microsoft Azure サービスへのアクセスを許可される Microsoft Azure アプリケーション ファイアウォールを構成します。 Contoso Webstore がすべての CED 境界でファイアウォールをすべて拒否する。 すべての構成は、デプロイの初期セットアップ時に実行されます。

> [!NOTE]
> このソリューションでは、CDE を分離するために App Service Environment (ASE) が使用されてきますが、ASE の送信接続を許可する DMZ 分離を実装するのは ASE であるため、資格のあるセキュリティ査定機関 (QSA) がこのソリューションを評価することが不可欠となります。 PCI DSS では、必要のないすべての送受信接続をブロックすることが要件です。 ASE が正しく動作するために、ASE は必要に応じて「[App Service Environment でのネットワークの考慮事項](/azure/app-service/app-service-environment/network-info)」に定義されているように送信接続を確立します。 お客様は、ソリューションを運用環境にデプロイする前に、QSA と共に送信接続を評価して要件に適合していることを確認する必要があります。 |



### <a name="pci-dss-requirement-122"></a>PCI DSS 要件 1.2.2

**1.2.2** ルーター構成ファイルをセキュリティで保護し、同期します。

**責任:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | 適用不可。 |
| **お客様<br /> (PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore は、Microsoft Azure のネイティブ ネットワーク コントロールに同期された構成を提供します。|



### <a name="pci-dss-requirement-123"></a>PCI DSS 要件 1.2.3

**1.2.3** すべてのワイヤレス ネットワークと環境の間、カード所有者データ環境の間に境界ファイアウォールをインストールし、業務目的に必要なトラフィックでワイヤレス環境とカード所有者データ環境の間で認証されたトラフィックのみを許可し、それ以外は拒否するようファイアウォールを構成します。

**責任:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | 適用不可。 |
| **お客様<br /> (PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore に、ワイヤレス ソリューションがインストールされておらず、有効になっている機能もない必要があります。|



## <a name="pci-dss-requirement-13"></a>PCI DSS 要件 1.3

**1.3** インターネットとカード所有者データ環境にあるシステム コンポーネントの間の直接のパブリック アクセスを禁止します。

**責任:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | Microsoft Azure では、ファイアウォール、ロード バランサー、ACL などのネットワークベースおよびホスト ベースの境界保護デバイスを採用しています。 これらのデバイスでは、VLAN 分離、NAT、およびインターネットから顧客トラフィックや管理トラフィックを分離するためのパケット フィルタ リングなどのメカニズムを使用します。 |
| **お客様<br /> (PCI&#8209;DSS&nbsp;Blueprint)** | デプロイ時に、Contoso Webstore で、CDE 内の要塞 Azure VM を含むサイトへのアクセスに、指定された IP アドレス範囲のみが使用されるよう、Azure アプリケーション ファイアウォールを構成します。|



### <a name="pci-dss-requirement-131"></a>PCI DSS 要件 1.3.1

**1.3.1** パブリックにアクセスできる承認されたサービス、プロトコル、およびポートを提供するシステム コンポーネントのみに受信トラフィックを制限するよう DMZ を実装します。


**責任:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | 適用不可。 |
| **お客様<br /> (PCI&#8209;DSS&nbsp;Blueprint)** | 権限を持つサービスだけが、CDE に接続できるよう Contoso Webstore の DMZ が実装されている必要があります。|



### <a name="pci-dss-requirement-132"></a>PCI DSS 要件 1.3.2

**1.3.2** 受信インターネット トラフィックが DMZ 内の IP アドレスに制限されている必要があります。

**責任:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | 適用不可。 |
| **お客様<br /> (PCI&#8209;DSS&nbsp;Blueprint)** | 権限を持つサービスだけが、CDE に接続できるよう Contoso Webstore の DMZ が実装されている必要があります。|



### <a name="pci-dss-requirement-133"></a>PCI DSS 要件 1.3.3

**1.3.3** ネットワークに入ってから偽造されたソース IP アドレスを検知してブロックするなりすまし対策が実装されている必要があります。 (たとえば、内部送信元アドレスを持つインターネットからのトラフィックをブロックします。)

**責任:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | Microsoft Azure で、信頼されているプラットフォーム コンポーネントへの送受信トラフィックの制限と、なりすましトラフィックを防止するネットワーク フィルター処理が実装されている必要があります。 |
| **お客様<br /> (PCI&#8209;DSS&nbsp;Blueprint)** | 適用不可。|



### <a name="pci-dss-requirement-134"></a>PCI DSS 要件 1.3.4

**1.3.4** カード所有者データ環境からインターネットへの承認されていない送信トラフィックをブロックする必要があります。


**責任:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | 適用不可。 |
| **お客様<br /> (PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore アーキテクチャは、スコープ内の環境からインターネットへの承認されていない送信トラフィックをブロックします。 これは、Microsoft Azure で承認されているポートとプロトコルの送信トラフィックの ACL を構成することによって行います。 これらのコントロールには、SQL Server データベースの CDE へのアクセスが含まれます。 <br /><br />PaaS SQL Database インスタンスを使用して、データベースのセキュリティ対策を実装します。 詳細については、[PCI ガイダンス - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database) を参照してください。|



### <a name="pci-dss-requirement-135"></a>PCI DSS 要件 1.3.5

**1.3.5** ネットワークへの "確立された" 接続のみを許可します。


**責任:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | Microsoft Azure で、信頼されているプラットフォーム コンポーネントへの送受信トラフィックの制限と、なりすましトラフィックを防止するネットワーク フィルター処理が実装されている必要があります。 Microsoft Azure ネットワークは、管理トラフィックとお客様のトラフィックを隔離するよう分離されています。 |
| **お客様<br /> (PCI&#8209;DSS&nbsp;Blueprint)** | 適用不可。|



### <a name="pci-dss-requirement-136"></a>PCI DSS 要件 1.3.6

**1.3.6** DMZ やその他の信頼されていないネットワークから分離された内部ネットワーク ゾーンにカード会員データを格納するシステム コンポーネント (データベースなど) を配置します。


**責任:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | Microsoft Azure ネットワークは、顧客トラフィックと管理トラフィックを隔離するためにネットワーク分離および NAT を使用しています。 |
| **お客様<br /> (PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore アーキテクチャは、スコープ内の環境からインターネットへの承認されていない送信トラフィックをブロックします。 これは、Microsoft Azure で承認されているポートとプロトコルの送信トラフィックの ACL を構成することによって行います。 これらのコントロールには、SQL Server データベースの CDE へのアクセスが含まれます。 <br /><br />PaaS SQL Database インスタンスを使用して、データベースのセキュリティ対策を実装します。 詳細については、[PCI ガイダンス - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database) を参照してください。|



### <a name="pci-dss-requirement-137"></a>PCI DSS 要件 1.3.7

**1.3.7** プライベート IP アドレスおよびルーティング情報を承認されていないユーザーに公開してはなりません。 

> [!NOTE]
> IP アドレスの不明瞭化方法には以下が含まれますが、これらに限定されません。
> - NLB ネットワーク アドレス変換 (NAT)。
> - カード会員データを格納しているサーバーをプロキシ サーバーまたはファイアウォールの背後に配置する。
> - 登録アドレスを使用するプライベート ネットワークのルーター広告を削除するかフィルター処理する。
> - 登録済みアドレスではなく RFC1918 アドレス空間を内部で使用する。


**責任:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | Microsoft Azure ネットワークでは、顧客トラフィックと管理トラフィックを隔離するためにネットワーク分離およびネットワーク アドレス変換 (NAT) を使用しています。 Azure デバイスは、一意な UUID で識別され、Kerberos を使用して特定されています。 Azure マネージ ネットワーク デバイスは RFC 1918 の IP アドレスによって特定されます。 |
| **お客様<br /> (PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore では、すべてのカード会員データはファイアウォール/プロキシ サーバーの背後に配置され、RFC1918 アドレス空間が内部的に使用されています。|



## <a name="pci-dss-requirement-14"></a>PCI DSS 要件 1.4

**1.4** ネットワーク外でインターネットに接続する携帯コンピューティング デバイス (会社または従業員個人所有の) に個人用ファイアウォール ソフトウェアまたは同等の機能のソフトウェアをインストールし、CDE にアクセスする場合も使用します。 ファイアウォール (または同等) の構成には、特定の定義された構成設定が含まれます。
- パーソナル ファイアウォール (または同等の機能) が有効化され実行されている必要があります。
- パーソナル ファイアウォール (または同等の機能) が、ポータブル コンピューティング デバイスのユーザーによって変更できないよう設定します。

**責任:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | 適用不可。 |
| **お客様<br /> (PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore はエンド ユーザー デバイスの保護は提供しません。 [Microsoft Intune](https://www.microsoft.com/cloud-platform/microsoft-intune) を使用して、従業員による会社のデータへのアクセスを管理できます。|



## <a name="pci-dss-requirement-15"></a>PCI DSS 要件 1.5

**1.5** セキュリティ ポリシーとファイアウォールを管理するための運用手順が文書化され、使用され、すべての関係者に通知します。

**責任:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | 適用不可。 |
| **お客様<br /> (PCI&#8209;DSS&nbsp;Blueprint)** | デプロイ時に、Contoso Webstore で、CDE 内の要塞 Azure VM を含むサイトへのアクセスに、指定された IP アドレス範囲のみが使用されるよう、Azure アプリケーション ファイアウォールを構成します。|




