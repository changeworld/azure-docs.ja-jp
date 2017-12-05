---
title: "Azure Payment Processing Blueprint - 物理アクセス要件"
description: "PCI DSS 要件 9"
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 91595a69-e9ce-4f9c-8388-10224165d9c0
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: 89f7b20a130e988bfe4964d50ae97de788ca4623
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2017
---
# <a name="physical-access-requirements-for-pci-dss-compliant-environments"></a>PCI DSS に準拠する環境の物理アクセス要件 
## <a name="pci-dss-requirement-9"></a>PCI DSS 要件 9

**カード所有者データへの物理アクセスを制限する**

> [!NOTE]
> これらの要件は、[PCI データ セキュリティ基準 (DSS) バージョン 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss)の一部として [Payment Card Industry (PCI) Security Standards Council](https://www.pcisecuritystandards.org/pci_security/) によって定義されています。 各要件のテスト手順およびガイダンスについては、PCI DSS をご覧ください。

ユーザーに、デバイスまたはデータへアクセスする、またはシステムまたはハードコピーを削除する機会を与える、カード所有者データが保存されているデータまたはシステムへの物理アクセスは、適切に制限する必要があります。 要件 9 の目的では、"オンサイト従業員" とは、企業の施設内に物理的に存在する正社員およびパートタイム従業員、派遣社員、下請業者、コンサルタントを指します。 "訪問者" とは、ベンダー、オンサイト従業員の関係者、サービス作業員などの、通常は 1 日を超えない短時間、施設に滞在する必要がある人員を指します。 "メディア" とは、カード所有者データを含むすべての書類や電子媒体を指します。

## <a name="pci-dss-requirement-91"></a>PCI DSS 要件 9.1

**9.1** カード所有者データ環境のシステムへの物理アクセスを制限および監視するために適切な施設立ち入り制御を使用します。

**責任:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | Microsoft Azure は、データ センターへの物理アクセス セキュリティを実装、適用および監視します。 |
| **お客様<br /> (PCI&#8209;DSS&nbsp;Blueprint)** | 適用不可。|



### <a name="pci-dss-requirement-911"></a>PCI DSS 要件 9.1.1

**9.1.1** 機密情報のある領域への個人の物理アクセスを監視するビデオ カメラまたはアクセス制御メカニズム (またはその両方) を使用します。 収集したデータを確認し、その他のエントリとの相関関係を確認します。 法律によって制限されている場合を除いて、最低 3 か月間保管します。

> [!NOTE]
> "機密領域" とは、カード所有者データを保存、処理、または送信するシステムが格納されたデータ センター、サーバー ルームまたは領域を指します。 これには、小売店舗のレジなどの POS 端末のみが存在する公共領域は含まれません。

**責任:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | Microsoft Azure は、データ センターの生体認証アクセス 制御メカニズムおよび CCTV を実装、適用および監視します。 |
| **お客様<br /> (PCI&#8209;DSS&nbsp;Blueprint)** | 適用不可。|



### <a name="pci-dss-requirement-912"></a>PCI DSS 要件 9.1.2

**9.1.2** アクセスが公開されているネットワーク ジャックへのアクセスを制限する物理的および論理的制御を実装します。 

たとえば、公共の場所あるいは訪問者がアクセス可能な領域にあるネットワーク ジャックを無効にしたり、ネットワーク アクセスが明示的に承認されたときにのみ有効化します。 または、アクティブなネットワークのジャックがある領域では、訪問者に常に従業員が付き沿うプロセスを実装できます。

**責任:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | Microsoft Azure プラットフォーム内でパブリックにアクセスできるネットワーク ジャックがないことを確認します。 |
| **お客様<br /> (PCI&#8209;DSS&nbsp;Blueprint)** | 適用不可。|



### <a name="pci-dss-requirement-913"></a>PCI DSS 要件 9.1.3

**9.1.3** ワイヤレス アクセス ポイント、ゲートウェイ、ハンドヘルド デバイス、ネットワーク ハードウェアまたは通信ハードウェア、および電話回線への物理的なアクセスを制限します。

**責任:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | Microsoft Azure ネットワーク ハードウェアへの物理アクセスは、アクセス リスト、複数の形式の認証、入口の物理的な制限、および機器にアクセスするためにビジネス ニーズが承認されることを要件とするなど、厳重に管理する必要があります。 |
| **お客様<br /> (PCI&#8209;DSS&nbsp;Blueprint)** | 適用不可。|



## <a name="pci-dss-requirement-92"></a>PCI DSS 要件 9.2

**9.2** オンサイト従業員と訪問者を簡単に区別できる手続きを策定します。
- オンサイト従業員と訪問者を識別します (バッジを割り当てるなど)。
- アクセス要件の変更
- 有効期限が切れたオンサイト従業員および訪問者 ID (ID バッジなど) を回収するか、無効化します。

**責任:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | Microsoft Azure は、データ センターを訪問する従業員または下請け業者の物理アクセス セキュリティを実装、適用および監視します。 |
| **お客様<br /> (PCI&#8209;DSS&nbsp;Blueprint)** | 適用不可。|



## <a name="pci-dss-requirement-93"></a>PCI DSS 要件 9.3

**9.3** 次のように、機密領域へのオンサイト従業員による物理的アクセスを制御します。
- アクセスは、各職務を基に承認される必要があります。
- 有効期限が切れると、アクセスが拒否され、鍵、アクセス カードなどの物理アクセス メカニズムの返却を求めるか、無効化します。

**責任:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | Microsoft データ センターへのアクセスの承認は、最小特権の原則に基づいて、データ センター チームによって承認されたアクセス リストを使用して制御されます。 アクセス制御リストは、四半期に 1 回、レビューおよび確認、更新されます。<br /><br />Microsoft Azure データ センターでは、境界ゲート、電子アクセス バッジ リーダー、生体認証リーダー、マントラップ/ポータル、およびアンチパスバック デバイスなどの物理アクセス デバイスを使用します。 アクセス バッジ デバイスは、継続的に監視されます。 |
| **お客様<br /> (PCI&#8209;DSS&nbsp;Blueprint)** | 適用不可。|



## <a name="pci-dss-requirement-94"></a>PCI DSS 要件 9.4

**9.4** 訪問者を識別および承認する手続きを実行します。 手続きには、以下を含める必要があります。

**責任:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | Microsoft Azure では、情報処理施設から物理的に分離され、認証された従業員が監視する、セキュリティ保護された搬入口に事前に承認された納品物が納品されることを確認します。 |
| **お客様<br /> (PCI&#8209;DSS&nbsp;Blueprint)** | 適用不可。|



### <a name="pci-dss-requirement-941"></a>PCI DSS 要件 9.4.1

**9.4.1** 訪問者は、施設に入る前に認証され、カード所有者データが処理または保管されている領域では常時、従業員が付き沿います。


**責任:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | Microsoft Azure では、情報処理施設から物理的に分離され、認証された従業員が監視する、セキュリティ保護された搬入口に事前に承認された納品物が納品されることを確認します。 |
| **お客様<br /> (PCI&#8209;DSS&nbsp;Blueprint)** | 適用不可。|



### <a name="pci-dss-requirement-942"></a>PCI DSS 要件 9.4.2

**9.4.2** 訪問者は識別され、オンサイト従業員と視覚的に区別できる、有効期限の付いたバッジやその他の ID が訪問者に手渡されます。

**責任:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | マイクロソフトのデータ センターにアクセスする際は、事前に承認を受ける必要があり、許可された訪問者は到着時に物理的なセキュリティ チェックを受け、施設に入る前に有効な ID 証明を提示する必要があります。 バッジは、従業員を明確に区別できるものを使用します。 請負業者および訪問者は、臨時バッジを受け取り、施設を退出する際に返却する必要があります。 |
| **お客様<br /> (PCI&#8209;DSS&nbsp;Blueprint)** | 適用不可。|



### <a name="pci-dss-requirement-943"></a>PCI DSS 要件 9.4.3

**9.4.3** 訪問者は、施設を退出する前に、または有効期限日にバッジまたは ID を返却するよう求められます。

**責任:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | 訪問者は、マイクロソフトの施設を退出する前に、バッジを返却するよう求められます。 |
| **お客様<br /> (PCI&#8209;DSS&nbsp;Blueprint)** | 適用不可。|



### <a name="pci-dss-requirement-944"></a>PCI DSS 要件 9.4.4

**9.4.4** 訪問者ログを使用して、コンピューター室およびカード所有者データが格納または送信されるデータ センターなどの施設での訪問者のアクティビティの物理的な監査証跡を管理します。
訪問者の氏名、所属会社、物理アクセスを承認したオンサイト従業員を記録します。
法律によって他の制限がない限り、この記録を 3 か月以上保管します。

**責任:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | Microsoft Azure は、コンピューター室およびカード所有者データが格納または送信されるデータ センターなどの施設での訪問者のアクティビティの物理的な監査証跡として、訪問者ログを管理します。 |
| **お客様<br /> (PCI&#8209;DSS&nbsp;Blueprint)** | 適用不可。|



## <a name="pci-dss-requirement-95"></a>PCI DSS 要件 9.5

**9.5** すべてのメディアを物理的に保護します。

**責任:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | 適用不可。 |
| **お客様<br /> (PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore は、Azure SQL Database にすべてのデータを格納します。 PaaS SQL Database インスタンスを使用して、データベースのセキュリティ対策を実装します。 詳細については、[PCI ガイダンス - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database) を参照してください。|



### <a name="pci-dss-requirement-951"></a>PCI DSS 要件 9.5.1

**9.5.1** メディアのバックアップを安全な場所、可能な場合は、代替またはバックアップ サイト、または専門業者のストレージ施設に保管します。 年 1 回以上、この場所のセキュリティを確認します。

**責任:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | 適用不可。 |
| **お客様<br /> (PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore は、Azure SQL Database にすべてのデータを格納します。 PaaS SQL Database インスタンスを使用して、データベースのセキュリティ対策を実装します。 詳細については、[PCI ガイダンス - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database) を参照してください。|



## <a name="pci-dss-requirement-96"></a>PCI DSS 要件 9.6

**9.6** 次を含む、任意の種類のメディアの内部または外部への配布に対する厳密な制御を維持します。

**責任:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | 適用不可。 |
| **お客様<br /> (PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore は、Azure SQL Database にすべてのデータを格納します。 PaaS SQL Database インスタンスを使用して、データベースのセキュリティ対策を実装します。 詳細については、[PCI ガイダンス - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database) を参照してください。|



### <a name="pci-dss-requirement-961"></a>PCI DSS 要件 9.6.1

**9.6.1** データの機密性を判定できるように、メディアを分類します。

**責任:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | 適用不可。 |
| **お客様<br /> (PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore は、Azure SQL Database にすべてのデータを格納します。 PaaS SQL Database インスタンスを使用して、データベースのセキュリティ対策を実装します。 詳細については、[PCI ガイダンス - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database) を参照してください。|



### <a name="pci-dss-requirement-962"></a>PCI DSS 要件 9.6.2

**9.6.2** 正確に追跡できるセキュリティ保護された宅急便またはその他の配送方法でメディアを発送します。

**責任:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | 適用不可。 |
| **お客様<br /> (PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore は、Azure SQL Database にすべてのデータを格納します。 PaaS SQL Database インスタンスを使用して、データベースのセキュリティ対策を実装します。 詳細については、[PCI ガイダンス - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database) を参照してください。|



### <a name="pci-dss-requirement-963"></a>PCI DSS 要件9.6.3

**9.6.3** セキュリティ保護された場所からメディアを移動する場合は、必ず管理者の承認を受けます (個人にメディアを配布する場合を含む)。

**責任:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | 適用不可。 |
| **お客様<br /> (PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore は、Azure SQL Database にすべてのデータを格納します。 PaaS SQL Database インスタンスを使用して、データベースのセキュリティ対策を実装します。 詳細については、[PCI ガイダンス - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database) を参照してください。|



## <a name="pci-dss-requirement-97"></a>PCI DSS 要件 9.7

**9.7** ストレージおよびメディアのアクセスを厳重に管理します。

**責任:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | 適用不可。 |
| **お客様<br /> (PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore は、Azure SQL Database にすべてのデータを格納します。 PaaS SQL Database インスタンスを使用して、データベースのセキュリティ対策を実装します。 詳細については、[PCI ガイダンス - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database) を参照してください。|



### <a name="pci-dss-requirement-971"></a>PCI DSS 要件 9.7.1

**9.7.1** すべてのメディアを適切に管理し、年 1 回以上、メディアの目録を作成します。


**責任:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | 適用不可。 |
| **お客様<br /> (PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore は、Azure SQL Database にすべてのデータを格納します。 PaaS SQL Database インスタンスを使用して、データベースのセキュリティ対策を実装します。 詳細については、[PCI ガイダンス - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database) を参照してください。|



## <a name="pci-dss-requirement-98"></a>PCI DSS 要件 9.8

**9.8** 業務上不要になった場合、または次のような法的な理由がある場合は、メディアを廃棄します。

**責任:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | 適用不可。 |
| **お客様<br /> (PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore は、Azure SQL Database にすべてのデータを格納します。 PaaS SQL Database インスタンスを使用して、データベースのセキュリティ対策を実装します。 詳細については、[PCI ガイダンス - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database) を参照してください。|



### <a name="pci-dss-requirement-981"></a>PCI DSS 要件 9.8.1

**9.8.1** カード所有者データを再構築できないようハード コピー資料をシュレッダーにかける、焼却、または溶解します。 破棄される資料に使用されているストレージ コンテナーをセキュリティ保護します。

**責任:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | 適用不可。 |
| **お客様<br /> (PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore は、Azure SQL Database にすべてのデータを格納します。 PaaS SQL Database インスタンスを使用して、データベースのセキュリティ対策を実装します。 詳細については、[PCI ガイダンス - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database) を参照してください。|



### <a name="pci-dss-requirement-982"></a>PCI DSS 要件 9.8.2

**9.8.2** 電子メディアに保存されているカード所有者データを、カード所有者データを再構築できないように破壊します。

**責任:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | データの破壊手法は、サブスクリプション、ストレージ、仮想マシン、データベースなどの破壊されるデータ物体の種類によって異なります。 Microsoft Azure のマルチ テナント環境では、顧客のデータが他の顧客データに "流れ込ん" だり、顧客がデータを削除したときに、他の顧客 (多くの場合はデータを以前に所有していた顧客) がその削除されたデータにアクセスできないように注意する必要があります。<br /><br />Microsoft Azure は、意図しないデータ漏洩に対処するための原則が記載されたメディアのサニタイズに関する NIST 800-88 ガイドラインに従っています。 これらのガイドラインは、電子的および物理的サニタイズについて規定しています。 |
| **お客様<br /> (PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore は、配置時に、使用されるリソース グループを削除することによって完全削除できます。|



## <a name="pci-dss-requirement-99"></a>PCI DSS 要件 9.9

**9.9** カードと直接、物理的に接触することによる改ざんや置換からカードを守るため、支払いカード データを取得するデバイスを保護します。

> [!NOTE]
> これらの要件は、POS でのカード処理 (カードのスワイプまたはディップ) で使用するカード読み取りデバイスに適用されます。 この要件は、コンピューターのキーボードや POS キーパッドなどの手動のキー入力コンポーネントには適用されません。 

**責任:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | 適用不可。 |
| **お客様<br /> (PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore は、OMS を使用して、すべてのシステム変更をログに記録します。<br /><br />[Operations Management Suite (OMS)](/azure/operations-management-suite/) は、変更の詳細なログ記録を提供します。 変更をレビュー、検証して正確性を確認できます。 具体的なガイダンスについては、[PCI ガイダンス - Operations Management Suite](payment-processing-blueprint.md#logging-and-auditing) を参照してください。|



### <a name="pci-dss-requirement-991"></a>PCI DSS 要件 9.9.1

**9.9.1** デバイスの一覧を最新の状態に維持します。 一覧には、以下を含める必要があります。
- デバイスの製造元およびモデル
- デバイスの場所 (たとえば、サイトまたはデバイスが配置されている施設の所在地)
- デバイスのシリアル番号または、一意に特定できる他の方法

**責任:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | 適用不可。 |
| **お客様<br /> (PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore では、参照アーキテクチャおよび使用されるすべてのサービスの一覧が展開ドキュメントに記載されています。|



### <a name="pci-dss-requirement-992"></a>PCI DSS 要件 9.9.2

**9.9.2** 定期的に、改ざん (デバイスへのカード スキマーの追加など) または置換 (たとえば、シリアル番号またはその他のデバイスの特長を調査して、不正なデバイスと置き換えられていないか確認します) されていないかデバイスの表面を検査します。

> [!NOTE]
> デバイスが改ざんまたは置換されている兆候としては、デバイスに想定外の部品やケーブルが接続されている、またはセキュリティ ラベルが変更されている、ケースが壊れているまたは色が異なる、またはシリアル番号や他の外部マーキングが変更されていることが挙げられます。

**責任:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | 適用不可。 |
| **お客様<br /> (PCI&#8209;DSS&nbsp;Blueprint)** | 適用不可。|



### <a name="pci-dss-requirement-993"></a>PCI DSS 要件 9.9.3

**9.9.3** デバイスの改ざんや交換に注意を促すトレーニングを従業員に提供します。 トレーニングには、以下を含める必要があります。
- 修理またはメンテナンス作業員だと偽って外部の人間がデバイスにアクセスして改造または修理する前に、身元を確認する。
- 承認されていないデバイスを設置、交換、または返品しない。
- デバイスの周りで見られる疑わしい行動 (たとえば、見知らぬ人員によるデバイスの取り外しや、デバイス内部へのアクセス) を認識する。
- 疑わしい行動やデバイスの改ざんまたは置換の兆候があったら、適切な人員 (マネージャーやセキュリティ担当者) に通報する。

**責任:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | 適用不可。 |
| **お客様<br /> (PCI&#8209;DSS&nbsp;Blueprint)** | 適用不可。|



## <a name="pci-dss-requirement-910"></a>PCI DSS 要件 9.10

**9.10** カード所有者データへの物理アクセスを制限するためのセキュリティ ポリシーと運用手順を文書化、使用し、すべての関係者に通知します。

**責任:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | 適用不可。 |
| **お客様<br /> (PCI&#8209;DSS&nbsp;Blueprint)** | 適用不可。|




