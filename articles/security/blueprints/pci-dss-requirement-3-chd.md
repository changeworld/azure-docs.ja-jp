---
title: Azure Payment Processing Blueprint - CHD 要件
description: PCI DSS 要件 3
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 9736f7c8-c632-4b86-8b8a-6e4f45c1a7aa
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: 7ff8a412b16025afe2640b73b51d9b2a2fdca354
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/08/2018
---
# <a name="chd-requirements-for-pci-dss-compliant-environments"></a>PCI DSS に準拠する環境の CHD 要件
## <a name="pci-dss-requirement-3"></a>PCI DSS 要件 3

**保存されたカード所有者のデータを保護する**

> [!NOTE]
> これらの要件は、[PCI データ セキュリティ基準 (DSS) バージョン 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss)の一部として [Payment Card Industry (PCI) Security Standards Council](https://www.pcisecuritystandards.org/pci_security/) によって定義されています。 各要件のテスト手順およびガイダンスについては、PCI DSS をご覧ください。

暗号化、切り捨て、マスク、ハッシュなどの保護方法は、カード所有者データの保護の重要なコンポーネントです。 侵入者が他のセキュリティ コントロールを回避し、暗号化されたデータにアクセスできても、正しい暗号化キーがなければ、そのデータを読み取り、使用することはできません。 保存されたデータを保護するための効果的な別の方法として考えられるのは、リスクを軽減する方法です。 たとえば、リスクを最小限にする方法として、カード所有者データが絶対に必要でないかぎり保存しない、完全な PAN が不要な場合はカード所有者データを切り捨てる、電子メールやインスタント メッセージングなどのエンド ユーザー メッセージング技術を使用して保護されていない PAN を送信しない、などがあります。
「強力な暗号化」および他の PCI DSS 用語の定義については、「PCI DSS and PA-DSS Glossary of Terms, Abbreviations, and Acronyms」(PCI DSS と PA-DSS の用語、略語、および頭字語) を参照してください。

## <a name="pci-dss-requirement-31"></a>PCI DSS 要件 3.1

**3.1** すべてのカード所有者データ (CHD) の保存について少なくとも以下のものを含むデータ保存および廃棄ポリシー、手順、プロセスを実装することで、保存するカード所有者データを最小限に抑える。
- 保存するデータ量とリテンション期間を、法律上、規則上、および業務上の要件のために必要な範囲に限定する
- カード所有者データの特定の保存要件
- 必要なくなった場合にデータを安全に削除するためのプロセス
- 定義されたリテンション期間を超えて保存されたカード所有者データを特定して、安全に破棄する四半期ごとのプロセス

**責任:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | Azure は、削除対象に指定されたお客様のデータが、安全な破棄ポリシーで指定されている NIST 800-88 に準拠したプロトコルを使用して使用停止にされることを確実にする責任を負います。 |
| **お客様 <br />(PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore は、保存された CHD を削除または破棄しません。 ただし、すべてのデータは暗号化され、プライマリ アカウント番号 (PAN) データは保存されません。<br /><br />|



## <a name="pci-dss-requirement-32"></a>PCI DSS 要件 3.2

**3.2** 承認後に機密認証データを保存しない (暗号化されている場合でも)。 機密認証データを受け取った場合、認証プロセスが完了し次第、すべてのデータを復元不可能にする。 
- 業務上の正当な理由がある 
- データが安全に保存されている
機密認証データには、次の要件 3.2.1 から 3.2.3 に示すデータが含まれます。


**責任:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | 適用不可。 |
| **お客様 <br />(PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore は、保存された CHD を削除または破棄しません。サンプル データはデモ目的でのみ保存されます。 ただし、すべてのデータは暗号化され、プライマリ アカウント番号 (PAN) データは保存されません。<br /><br />|



### <a name="pci-dss-requirement-321"></a>PCI DSS 要件 3.2.1

**3.2.1** (カードの裏面にある磁気ストライプや、チップおよび他の場所に格納された同等のデータの) トラックの完全な内容を承認後に保存しない。 このデータは、フル トラック、トラック、トラック 1、トラック 2、磁気ストライプ データとも呼ばれます。 

> [!NOTE]
> 通常の取引過程では、磁気ストライプからの以下のデータ要素を保存する必要が生じる場合があります。 
> - カード所有者名 
> - プライマリ アカウント番号 (PAN) 
> - 有効期限 
> - サービス コード 
>
> リスクを最小限に抑えるため、取引に必要なこれらのデータ要素のみを保存します。

**責任:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | 適用不可。 |
| **お客様 <br />(PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore は CHD の完全な内容を保存しません。|



### <a name="pci-dss-requirement-322"></a>PCI DSS 要件 3.2.2

**3.2.2** カードを提示しないトランザクションを検証するために使用された、カード検証コードまたは値 (ペイメント カードの表面または裏面に印字されている 3 桁または 4 桁の数字) を承認後に保存しない。

**責任:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | 適用不可。 |
| **お客様 <br />(PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore は、カード検証コード サンプルを含むすべてのデータを暗号化します。|



### <a name="pci-dss-requirement-323"></a>PCI DSS 要件 3.2.3

**3.2.3** 暗証番号 (PIN) または暗号化された PIN ブロックを承認後に保存しない。

**責任:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | 適用不可。 |
| **お客様 <br />(PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore は PIN 情報を保存しません。|



## <a name="pci-dss-requirement-33"></a>PCI DSS 要件 3.3

**3.3** 表示時に PAN をマスクして (先頭 6 桁と末尾 4 桁が最大表示桁数)、業務上の正当な理由がある関係者だけが完全な PAN を見ることができるようにする。 

> [!NOTE]
> カード所有者データの表示 (法律上、またはペイメント カード ブランドによる販売時点管理 (POS) レシート要件など) に関するこれより厳しい要件がある場合は、その要件より優先されることはありません。

**責任:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | 適用不可。 |
| **お客様 <br />(PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore は、Transparent Data Encryption、Always Encrypted 列、および動的データ マスクを使用してプライマリ アカウント番号 (PAN) をマスクします。 詳細については、「[PCI Guidance - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database)」を参照してください。|



## <a name="pci-dss-requirement-34"></a>PCI DSS 要件 3.4

**3.4** 次のいずれかの手法を使用して、すべての保存場所 (ポータブル デジタル メディア、バックアップ メディア、ログ内を含む) で PAN を読み取り不可にする。
- 強力な暗号化をベースにした一方向ハッシュ (PAN 全体をハッシュする必要がある)
- 切り捨て (PAN の切り捨てられたセグメントの置き換えにはハッシュを使用できない)
- インデックス トークンとパッド (パッドは安全に保存する必要がある)
- 関連するキー管理プロセスおよび手順を伴う、強力な暗号化 

> [!NOTE]
> 悪意のある個人が切り捨てられた PAN とハッシュ化された PAN の両方を取得した場合、元の PAN データを比較的容易に再現することができます。 ハッシュ化および切り捨てされた PAN の同じバージョンがエンティティの環境に存在する場合、ハッシュ化および切り捨てされたバージョンを関連付けて元の PAN を再構築されることがないように、追加のコントロールを導入する必要があります。

**責任:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | 適用不可。 |
| **お客様 <br />(PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore は、すべてのクレジット カード データを暗号化し、Azure Key Vault を使用してキーを管理することで、CHD が取得されるのを防ぎます。<br /><br />詳細については、「[PCI ガイダンス - 暗号化](payment-processing-blueprint.md#encryption-and-secrets-management)」を参照してください。|



### <a name="pci-dss-requirement-341"></a>PCI DSS 要件 3.4.1

**3.4.1** (ファイルまたは列レベルのデータベース暗号化ではなく) ディスク暗号化が使用される場合、論理アクセスはネイティブなオペレーティング システムの認証およびアクセス制御メカニズムとは別に管理する必要がある (たとえば、ローカル ユーザー アカウント データベースや一般的なネットワーク ログイン資格情報を使用しないなどの方法で)。 暗号化解除キーをユーザー アカウントと関連付けてはならない。 

> [!NOTE]
> この要件は、暗号化およびキー管理に関する他のすべての PCI DSS 要件に加えて適用されます。

**責任:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | 適用不可。 |
| **お客様 <br />(PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore は保存されたすべてのデータを暗号化し、トラフィックを隔離して DevOps 機能の特権昇格を防ぎます。<br /><br />App Service 環境はセキュリティで保護され、ロック ダウンされているため、必要な DevOps の変更やリリースを許可するメカニズム (Kudu を使用して Web アプリを監視する機能など) が必要です。<br /><br />仮想マシンは、次の構成を使用してジャンプボックス (要塞ホスト) として作成されます。<br /><br /><ul><li>[マルウェア対策拡張機能](/azure/security/azure-security-antimalware)</li><li>[Log Analytics 監視拡張機能](/azure/virtual-machines/virtual-machines-windows-extensions-oms)</li><li>[VM 診断拡張機能](/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)</li><li>[BitLocker で暗号化されたディスク](/azure/security/azure-security-disk-encryption)</li></ul>Azure Key Vault の使用により、Azure Government、PCI DSS、HIPAA 要件に準拠できます。|



## <a name="pci-dss-requirement-35"></a>PCI DSS 要件 3.5

**3.5** カード所有者データを漏洩と悪用から保護するために使用されるキーを保護するための手順をドキュメント化し、実施する。 

> [!NOTE]
> この要件は、保存されたカード所有者データを暗号化するキーに適用されます。また、データ暗号化キーを保護するために使用されるキー暗号化キーにも適用されます。そのようなキー暗号化キーは、少なくともデータ暗号化キーと同じ強度を持つ必要があります。

**責任:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | **Key Vault を使用するお客様の場合:**<br /><br />Microsoft Azure は、お客様の Key Vault が互いに論理的に分離され、Key Vault サービスの管理プレーンから論理的に分離されることを確実にします。 Key Vault は、Microsoft がお客様の Key Vault に持続的にアクセスすることのないように設計されています。 <br /><br />キーは、業界標準のアルゴリズム、キーの長さ、ハードウェア セキュリティ モジュール (HSM) を使用して、Microsoft Azure によってセキュリティで保護されます。<br /><br />Microsoft Azure Key Vault に格納されるキーは、別のキーを保護するために使用される可能性があります。 |
| **お客様 <br />(PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore は、デモ CHD を保護するための、保護されたキー ソリューションのデプロイを説明および支援するドキュメントを用意します。|



### <a name="pci-dss-requirement-351"></a>PCI DSS 要件 3.5.1

**3.5.1** *サービス プロバイダーのみに対する追加の要件:* 以下を含む、暗号化アーキテクチャのドキュメント化された説明を保持する。
- カード所有者データの保護に使用されるすべてのアルゴリズム、プロトコル、およびキーについての詳細 (キーの強度と有効期限を含む)
- 各キーの主要な使用法の説明
- キー管理で使用されるすべての HSM および他の SCD のインベントリ 

> [!NOTE]
> この要件は、2018 年 1 月 31 日まではベスト プラクティスであり、その後は要件になります。

**責任:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | **Key Vault を使用するお客様の場合:**<br /><br />Microsoft Azure は、お客様の Key Vault が互いに論理的に分離され、Key Vault サービスの管理プレーンから論理的に分離されることを確実にします。 Key Vault は、Microsoft がお客様の Key Vault に持続的にアクセスすることのないように設計されています。 <br /><br />キーは、業界標準のアルゴリズム、キーの長さ、ハードウェア セキュリティ モジュール (HSM) を使用して、Microsoft Azure によってセキュリティで保護されます。<br /><br />Microsoft Azure Key Vault に格納されるキーは、別のキーを保護するために使用される可能性があります。 |
| **お客様 <br />(PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore は、デモ CHD を保護するための、保護されたキー ソリューションのデプロイを説明および支援するドキュメントを用意します。|



### <a name="pci-dss-requirement-352"></a>PCI DSS 要件 3.5.2

**3.5.2** 暗号化キーへのアクセスを、必要最小限の管理者に制限する。


**責任:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | **Key Vault を使用するお客様の場合:**<br /><br />Key Vault はきめ細かいアクセス ポリシーをサポートしており、Key Vault 所有者は、特定の操作を実行するための特定の機能へのアクセス権を特定のエンティティに付与できます。 |
| **お客様 <br />(PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore のキー管理は、1 つのユーザー アカウント (admin##@contosowebstore.com) に分離されます。|



### <a name="pci-dss-requirement-353"></a>PCI DSS 要件 3.5.3

**3.5.3** カード所有者データの暗号化と暗号化解除に使用されるシークレット キーおよび秘密キーは、以下のいずれかの形式 (複数可) で常に保存する。
- 少なくともデータ暗号化キーと同じ強度を持ち、データ暗号化キーとは別の場所に保存されているキー暗号化キーで暗号化された状態で
- 安全な暗号化デバイス (ハードウェア (ホスト) セキュリティ モジュール (HSM) や PTS 承認の加盟店デバイスなど) 内で
- 業界承認の方式に従う、少なくとも 2 つの全長キー コンポーネントまたはキー共有として 

> [!NOTE]
> 公開キーがこれらの形式で保存されていることは要求されていません。

**責任:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | **Key Vault を使用するお客様の場合:**<br /><br />キーは、お客様によって指定された特定の Key Vault に格納されます。<br /><br />Key Vault には複数のアプリケーションから同時かつグローバルにアクセスできるため、キーをコピーして複数の場所に保存する必要が減ります。 |
| **お客様 <br />(PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore は、すべてのキー管理に Azure Key Vault を使用します。 詳細については、「[PCI ガイダンス - 暗号化](payment-processing-blueprint.md#encryption-and-secrets-management)」を参照してください。|



### <a name="pci-dss-requirement-354"></a>PCI DSS 要件 3.5.4

**3.5.4** 暗号化キーを最小限の場所に保存する。


**責任:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | **Key Vault を使用するお客様の場合:**<br /><br />キーは、お客様によって指定された特定の Key Vault に格納されます。 <br /><br />Key Vault には複数のアプリケーションから同時かつグローバルにアクセスできるため、キーをコピーして複数の場所に保存する必要が減ります。 |
| **お客様 <br />(PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore は、すべてのキー管理に Azure Key Vault を使用します。詳細については、「[PCI Guidance - 暗号化](payment-processing-blueprint.md#encryption-and-secrets-management)」を参照してください。|



## <a name="pci-dss-requirement-36"></a>PCI DSS 要件 3.6

**3.6** カード所有者データの暗号化に使用される暗号化キーの管理プロセスおよび手順をすべてドキュメント化し、実装する。これには、以下が含まれる。 

> [!NOTE]
> キー管理には多数の業界標準があり、NIST (http://csrc.nist.gov) などさまざまなリソースから入手可能です。

**責任:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | 適用不可。 |
| **お客様 <br />(PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore は、すべてのキー管理に Azure Key Vault を使用します。 詳細については、「[PCI ガイダンス - 暗号化](payment-processing-blueprint.md#encryption-and-secrets-management)」を参照してください。|



### <a name="pci-dss-requirement-361"></a>PCI DSS 要件 3.6.1

**3.6.1** 強力な暗号化キーの生成

**責任:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | **Key Vault を使用するお客様の場合:** <br /><br />Key Vault でキーを生成する際、Azure はお客様の仕様書どおりにキーを生成する責任を負います。 キーは HSM を使用して生成されます。 |
| **お客様 <br />(PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore は、すべてのキー管理に Azure Key Vault を使用します。 詳細については、「[PCI ガイダンス - 暗号化](payment-processing-blueprint.md#encryption-and-secrets-management)」を参照してください。|



### <a name="pci-dss-requirement-362"></a>PCI DSS 要件 3.6.2

**3.6.2** 安全な暗号化キーの配布

**責任:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | **Key Vault を使用するお客様の場合:**<br /><br />Bring Your Own Key (BYOK) ツールがお客様のキーをカプセル化し、特定の Azure サブスクリプションに関連付けられた特定のセキュリティ ボルトに送信します。 キーは、指定されたリージョン内の、定義されたサブスクリプションの Key Vault にしかインポートできません。 このプロセスでは、ハードウェア製造元から提供された暗号化手順を使用します。 お客様は、転送が成功したことを示す通知を受け取ります。 |
| **お客様 <br />(PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore は、すべてのキー管理に Azure Key Vault を使用します。 詳細については、「[PCI ガイダンス - 暗号化](payment-processing-blueprint.md#encryption-and-secrets-management)」を参照してください。|



### <a name="pci-dss-requirement-363"></a>PCI DSS 要件 3.6.3

**3.6.3** 安全な暗号化キーの保存

**責任:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | **Key Vault を使用するお客様の場合:**<br /><br />キーは HSM に格納され、ハードウェア製造元の暗号化セキュリティを使用して保護されます。 キーのメタデータは、暗号化された状態で、各 Key Vault に固有の Azure Storage に保存されます。 <br /><br /> |
| **お客様 <br />(PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore は、すべてのキー管理に Azure Key Vault を使用します。 詳細については、「[PCI ガイダンス - 暗号化](payment-processing-blueprint.md#encryption-and-secrets-management)」を参照してください。|



### <a name="pci-dss-requirement-364"></a>PCI DSS 要件 3.6.4

**3.6.4** 関連アプリケーション ベンダーまたはキー所有者によって定義され、業界のベストプラクティスおよびガイドライン (NIST Special Publication 800-57 など) に基づいた、暗号化期間の終了時点に到達したキーの暗号化キーの変更 (たとえば、定義された期間が経過した後、または、特定のキーによって一定量の暗号化テキストが作成された後など)

**責任:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | **Key Vault を使用するお客様の場合:**<br /><br />Key Vault は、お客様によって定義された、キーを更新またはロールする機能をサポートします。 |
| **お客様 <br />(PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore は、すべてのキー管理に Azure Key Vault を使用します。 詳細については、「[PCI ガイダンス - 暗号化](payment-processing-blueprint.md#encryption-and-secrets-management)」を参照してください。|



### <a name="pci-dss-requirement-365"></a>PCI DSS 要件 3.6.5

**3.6.5** キーの整合性が脆弱になった場合 (クリア テキストのキー コンポーネントの知識を持つ従業員が離職した場合など) またはキーが侵害された可能性がある場合に必要とみなされる、キーの破棄または取り替え (アーカイブ、破壊、無効化など)。 

> [!NOTE]
> 破棄または置換された暗号化キーを保持する必要がある場合、(キー暗号化キーを使用するなどの方法によって) これらのキーが安全にアーカイブされる必要があります。 アーカイブされた暗号化キーは、暗号化解除/検証の目的のためにのみ使用できます。

**責任:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | **Key Vault を使用するお客様の場合:**<br /><br />Key Vault は、お客様によって定義された、キーを破棄または置換する機能をサポートします。 |
| **お客様 <br />(PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore は、すべてのキー管理に Azure Key Vault を使用します。 詳細については、「[PCI ガイダンス - 暗号化](payment-processing-blueprint.md#encryption-and-secrets-management)」を参照してください。|



### <a name="pci-dss-requirement-366"></a>PCI DSS 要件 3.6.6

**3.6.6** 手動によるクリア テキスト暗号化キーの管理操作を使用する場合、これらの操作を、知識の分割とデュアル コントロールを使用して管理する必要がある。 

> [!NOTE]
> 手動によるキー管理操作の例には、キーの生成、転送、読み込み、保存、破棄などがありますが、これらに限定されません。

**責任:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | 適用不可。 |
| **お客様 <br />(PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore は、すべてのキー管理に Azure Key Vault を使用します。 詳細については、「[PCI ガイダンス - 暗号化](payment-processing-blueprint.md#encryption-and-secrets-management)」を参照してください。|



### <a name="pci-dss-requirement-367"></a>PCI DSS 要件 3.6.7

**3.6.7** 暗号化キーの不正置換の防止

**責任:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | **Key Vault を使用するお客様の場合:**<br /><br />Key Vault は論理的に分離されており、ディレクトリにまたがる承認をサポートしていません。 その結果、不正な置換が防止されます。 |
| **お客様 <br />(PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore は、すべてのキー管理に Azure Key Vault を使用します。 詳細については、「[PCI ガイダンス - 暗号化](payment-processing-blueprint.md#encryption-and-secrets-management)」を参照してください。|



### <a name="pci-dss-requirement-368"></a>PCI DSS 要件 3.6.8

**3.6.8** 暗号化キーの管理者は、自身のキー管理責任を理解し受諾していることを公式に認める必要がある。

**責任:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | 適用不可。 |
| **お客様 <br />(PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore のキー管理は、1 つのユーザー アカウント (admin##@contosowebstore.com) に分離されます。|



## <a name="pci-dss-requirement-37"></a>PCI DSS 要件 3.7

**3.7** 保存されたカード所有者データを保護するためのセキュリティ ポリシーと操作手順がドキュメント化され、影響を受ける関係者全員に知られていることを確実にする。

**責任:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | 適用不可。 |
| **お客様 <br />(PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore は、すべてのキー管理に Azure Key Vault を使用します。 詳細については、「[PCI ガイダンス - 暗号化](payment-processing-blueprint.md#encryption-and-secrets-management)」を参照してください。|




