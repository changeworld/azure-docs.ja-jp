---
title: Azure Payment Processing Blueprint - テスト要件
description: PCI DSS 要件 11
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 9753853b-ad80-4be4-9416-2583b8fd2029
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: db9f1022ecb3b727f08bb6f232a8df55476e0755
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2018
---
# <a name="testing-requirements-for-pci-dss-compliant-environments"></a>PCI DSS に準拠する環境のテスト要件 
## <a name="pci-dss-requirement-11"></a>PCI DSS 要件 11

**セキュリティ システムとプロセスを定期的にテストする**

> [!NOTE]
> これらの要件は、[PCI データ セキュリティ基準 (DSS) バージョン 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss) の一部として [Payment Card Industry (PCI) Security Standards Council](https://www.pcisecuritystandards.org/pci_security/) によって定義されています。 各要件のテスト手順およびガイダンスについては、PCI DSS をご覧ください。

脆弱性は、悪意のある個人と研究者によって絶えず検出され、新しいソフトウェアによって導入されています。 システム コンポーネント、プロセス、カスタム ソフトウェアを頻繁にテストして、セキュリティ コントロールが変化する環境を反映し続けるようにする必要があります。

## <a name="pci-dss-requirement-111"></a>PCI DSS 要件 11.1

**11.1** ワイヤレス アクセス ポイント (802.11) の存在をテストし、すべての承認済みおよび承認されていないワイヤレス アクセス ポイントを四半期ごとに検出および特定するプロセスを実装します。

> [!NOTE]
> プロセスで使用できる方法には、ワイヤレス ネットワークのスキャン、システム コンポーネントとインフラストラクチャの物理/論理検査、ネットワーク アクセス制御 (NAC)、ワイヤレス IDS/IPS がありますが、これらに限定されません。
どの方法を使用するかにかかわらず、承認されているデバイスと承認されていないデバイスの両方を検出および特定するのに十分である必要があります。


**責任:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | Azure は、Azure ネットワーク環境でのワイヤレス接続を許可または許容しません。 社内のセキュリティ チームは、悪意のあるワイヤレス信号を四半期ごとにスキャンし、悪意のある信号を調査して削除します。 お客様は、Azure 環境でのワイヤレス テクノロジのデプロイを許可されていません。 |
| **お客様 <br />(PCI&#8209;DSS&nbsp;Blueprint)** | ソリューションでは、ワイヤレスと SNMP は実装されていません。|



### <a name="pci-dss-requirement-1111"></a>PCI DSS 要件 11.1.1

**11.1.1** 文書化されたビジネス上の正当な理由など、承認されたワイヤレス アクセス ポイントのインベントリを管理します。

**責任:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | 「[要件 11.1](#pci-dss-requirement-11-1)」の「Microsoft Azure」のセクションをご覧ください。 |
| **お客様 <br />(PCI&#8209;DSS&nbsp;Blueprint)** | ソリューションでは、ワイヤレスおよび SNMP は実装されていません。|



### <a name="pci-dss-requirement-1112"></a>PCI DSS 要件 11.1.2

**11.1.2** 承認されていないワイヤレス アクセス ポイントが検出された場合のインシデント対応手順を実装します。


**責任:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | 「[要件 11.1](#pci-dss-requirement-11-1)」の「Microsoft Azure」のセクションをご覧ください。 |
| **お客様 <br />(PCI&#8209;DSS&nbsp;Blueprint)** | ソリューションでは、ワイヤレスおよび SNMP は実装されていません。|



## <a name="pci-dss-requirement-112"></a>PCI DSS 要件 11.2

**11.2** 内部および外部ネットワークの脆弱性スキャンを、少なくとも四半期に 1 回と、ネットワークの大幅な変更 (新しいシステム コンポーネントのインストール、ネットワーク トポロジの変更、ファイアウォール ルールの変更、製品のアップグレードなど) 後に実行します。 

> [!NOTE]
> 四半期ごとのスキャン プロセスの複数のスキャン レポートを結合して、すべてのシステムがスキャンされ、該当するすべての脆弱性に対処済みであることを示すことができます。 修復されていない脆弱性が対処中であることを確認するために、追加のドキュメントが必要な場合があります。
> 初期の PCI DSS コンプライアンスでは、1) 最新のスキャン結果が合格したスキャンであったこと、2) エンティティが四半期ごとのスキャンを要求するポリシーと手順を文書化していること、3) スキャン結果で示された脆弱性が再スキャンで示されるように修正されたことを監査機関が検証する場合、4 四半期のスキャン合格が完了している必要はありません。 初期 PCI DSS レビューの後の年では、4 四半期のスキャン合格が必要です。


**責任:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | Azure は、四半期ごとに内部および外部脆弱性スキャンを実行します。 スキャンは、資格のある担当者によって実行されます。 |
| **お客様 <br />(PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore は侵入テストされ、脆弱性が "現状のまま" でスキャンされます。 侵入テスト結果は、nmap や pentest-tools.com などの一般的なツールを使用して複製できます。侵入テストの結果では、利用可能な項目のない、不確定の攻撃対象領域が示されます。 さらに、[Azure Security Center](https://azure.microsoft.com/services/security-center/) と [Azure Advisor](/azure/advisor/advisor-security-recommendations) は脆弱性情報と修復を提供します。|



### <a name="pci-dss-requirement-1121"></a>PCI DSS 要件 11.2.1

**11.2.1** 四半期ごとに内部脆弱性スキャンを実行します。 脆弱性に対処し、再スキャンを実行して、"リスクの高い" すべての脆弱性がエンティティの脆弱性順位 (要件 6.1) に従って解決されたことを確認します。 スキャンは、資格のある担当者が実行する必要があります。

**責任:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | Microsoft Azure は、スコープ内の基になるインフラストラクチャで脆弱性のスキャンを実行します。 Microsoft Azure は、適切な脆弱性スキャン ツールを使用して、サーバー オペレーティング システム、データベース、ネットワーク デバイスで脆弱性スキャンを実装します。 Azure Web アプリは、適切な産業用スキャン ソリューションを使用してスキャンされます。 脆弱性スキャンは、四半期ごとに実行されます。<br /><br />すべての "リスクの高い" 脆弱性 (要件 6.1 で識別される) が解決されるまで、必要に応じてすべてのシステムに対して再スキャンが実行されます。 |
| **お客様 <br />(PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore は侵入テストされ、脆弱性が "現状のまま" でスキャンされます。 侵入テスト結果は、nmap や pentest-tools.com などの一般的なツールを使用して複製できます。侵入テストの結果では、利用可能な項目のない、不確定の攻撃対象領域が示されます。 さらに、[Azure Security Center](https://azure.microsoft.com/services/security-center/) と [Azure Advisor](/azure/advisor/advisor-security-recommendations) は脆弱性情報と修復を提供します。|



### <a name="pci-dss-requirement-1122"></a>PCI DSS 要件 11.2.2

**11.2.2** Payment Card Industry Security Standards Council (PCI SSC) によって承認された認定スキャン ベンダー (ASV) を介して、四半期ごとに外部脆弱性スキャンを実行します。 スキャンに合格するまで、必要に応じて再スキャンを実行します。 

> [!NOTE]
> Payment Card Industry Security Standards Council (PCI SSC) によって承認された認定スキャン ベンダー (ASV) によって、四半期ごとに外部脆弱性スキャンを実行する必要があります。
> スキャンに関するお客様の責任、スキャンの準備などについては、PCI SSC Web サイトに公開されている ASV プログラム ガイドをご覧ください。

**責任:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | Microsoft Azure は、外部からアクセスできるスコープ内の基になるインフラストラクチャで脆弱性の外部スキャンを実行します。 スキャンは、認定スキャン ベンダー (ASV) によって実行されます。<br /><br />Microsoft Azure は、MSRC/OSSC 月次修正プログラムの通知をサブスクライブし、脆弱性を少なくとも四半期に 1 回スキャンします。 識別された脆弱性が評価され、リスクのレベルに基づいて確立されたタイムラインに従って修復されます。<br /><br />Microsoft Azure 環境の優先度の高いコンポーネントに対して、対象となる包括的なセキュリティ脆弱性スキャンが四半期ごとに実行されて、セキュリティの脆弱性が識別されます。 |
| **お客様 <br />(PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore をデプロイする場合、デモのお客様は、Payment Card Industry Security Standards Council によって承認された認定スキャン ベンダー (ASV) を使用して、カード所有者データ環境 (CDE) 内のすべての PaaS インスタンスに対して外部脆弱性スキャンの実行を四半期ごとに実行し、必要に応じて再スキャンを実行する責任を負います。<br /><br />|



### <a name="pci-dss-requirement-1123"></a>PCI DSS 要件 11.2.3

**11.2.3** 重大な変更を加えた後で、内部および外部スキャンを実行し、必要に応じて再スキャンを実行します。 スキャンは、資格のある担当者が実行する必要があります。

**責任:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | 結果は関係者にレポートされ、修復は開示を通じて Azure Security Team によって追跡されます。 Azure のテスト結果は、NDA のもとでお客様と共有できます。 |
| **お客様 <br />(PCI&#8209;DSS&nbsp;Blueprint)** | お客様は、CDE 内のすべての PaaS インスタンスに対して四半期ごとに内部および外部脆弱性スキャンを実行し、必要に応じて再スキャンを実行する責任を負います。 スコープ内の環境が大幅に変更された後は、スキャンを実行する必要があります。<br /><br />スキャンは、ASV または組織から独立した人員が実行する必要があります。|



## <a name="pci-dss-requirement-113"></a>PCI DSS 要件 11.3

**11.3** 以下を含む侵入テストの方法を実装します。
- 業界で受け入れられている侵入テスト アプローチ (たとえば、NIST SP800-115) に基づく
- CDE 境界全体と重要なシステムの対象範囲を含む
- ネットワークの内部と外部両方からのテストを含む
- 任意のセグメント化およびスコープの縮小制御を検証するテストを含む
- 少なくとも要件 6.5 に一覧表示されている脆弱性を含むようにアプリケーション レイヤー侵入テストを定義する
- ネットワーク機能とオペレーティング システムをサポートするコンポーネントを含むようにネットワーク レイヤー侵入テストを定義する
- 過去 12 か月間に発生した脅威と脆弱性のレビューと考慮事項を含む
- 侵入テストの結果と修復アクティビティの結果のリテンション期間を指定する

**責任:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | Microsoft Azure は、CREST 認定テスターを使用した OWASP (Open Web Application Security Project) Top 10 に基づいて、サード パーティ製侵入テストを使用してサービスを検証します。 テストの結果は、セキュリティに関する慣例へのコンプライアンスを確保するために定期的に監査およびレビューされるリスク レジスタによって追跡されます。 <br /><br />マイクロソフトは、マイクロソフトが管理するインフラストラクチャ、サービス、アプリケーションに対して Red Teaming も使用します。 Red Teaming およびライブ サイト侵入テストでは、エンド ユーザーのお客様のデータは意図的に対象から外されています。 テストの対象は、Microsoft Azure のインフラストラクチャおよびプラットフォームと、マイクロソフト自身のアプリケーションとデータです。 Azure でホストされているお客様のテナント、アプリケーション、データが対象になることはありません。<br /><br />Microsoft Azure は、システム アセスメント プランを作成し、コントロール アセスメントを実施するために、独立監査機関を採用しています。 コントロール アセスメントは年 1 回実行され、結果は関係者にレポートされます。 |
| **お客様 <br />(PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore は侵入テストされ、脆弱性が "現状のまま" でスキャンされます。 侵入テスト結果は、nmap や pentest-tools.com などの一般的なツールを使用して複製できます。侵入テストの結果では、利用可能な項目のない、不確定の攻撃対象領域が示されます。 さらに、[Azure Security Center](https://azure.microsoft.com/services/security-center/) と [Azure Advisor](/azure/advisor/advisor-security-recommendations) は脆弱性情報と修復を提供します。|



### <a name="pci-dss-requirement-1131"></a>PCI DSS 要件 11.3.1

**11.3.1** "*外部*" 侵入テストを、少なくとも年 1 回と、重要なインフラストラクチャまたはアプリケーションのアップグレードまたは変更 (オペレーティング システムのアップグレード、環境へのサブネットワークの追加、環境への Web サーバーの追加など) 後に実行します。

**責任:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | 「[要件 11.3](#pci-dss-requirement-11-3)」の「Microsoft Azure」のセクションをご覧ください。 |
| **お客様 <br />(PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore は侵入テストされ、脆弱性が "現状のまま" でスキャンされます。 侵入テスト結果は、nmap や pentest-tools.com などの一般的なツールを使用して複製できます。侵入テストの結果では、利用可能な項目のない、不確定の攻撃対象領域が示されます。 さらに、[Azure Security Center](https://azure.microsoft.com/services/security-center/) と [Azure Advisor](/azure/advisor/advisor-security-recommendations) は脆弱性情報と修復を提供します。|



### <a name="pci-dss-requirement-1132"></a>PCI DSS 要件 11.3.2

**11.3.2** "*内部*" 侵入テストを、少なくとも年 1 回と、重要なインフラストラクチャまたはアプリケーションのアップグレードまたは変更 (オペレーティング システムのアップグレード、環境へのサブネットワークの追加、環境への Web サーバーの追加など) 後に実行します。

**責任:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | Microsoft Azure は、独立監査機関と契約して、Microsoft Azure 境界の侵入テストを実行します。 レッド チームの演習も定期的に実行され、結果はセキュリティの向上のために使用されます。 |
| **お客様 <br />(PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore は侵入テストされ、脆弱性が "現状のまま" でスキャンされます。 侵入テスト結果は、nmap や pentest-tools.com などの一般的なツールを使用して複製できます。侵入テストの結果では、利用可能な項目のない、不確定の攻撃対象領域が示されます。 さらに、[Azure Security Center](https://azure.microsoft.com/services/security-center/) と [Azure Advisor](/azure/advisor/advisor-security-recommendations) は脆弱性情報と修復を提供します。|



### <a name="pci-dss-requirement-1133"></a>PCI DSS 要件 11.3.3

**11.3.3** 侵入テスト中に見つかった利用可能な脆弱性が修正され、修正を確認するためにテストが繰り返されます。

**責任:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | Microsoft Azure プラットフォーム コンポーネントで既知のセキュリティ脆弱性を監視するための手順が確立されています。 <br /><br /><br /><br />Azure 運用環境の優先度の高いコンポーネントに対して、対象となる包括的なセキュリティ脆弱性スキャンが四半期ごとに実行されて、セキュリティの脆弱性を識別します。 結果は関係者にレポートされ、修復は開示を通じてチームによって追跡されます。 |
| **お客様 <br />(PCI&#8209;DSS&nbsp;Blueprint)** | 脆弱性の情報と修復を提供する [Azure Security Center](https://azure.microsoft.com/services/security-center/) と [Azure Advisor](/azure/advisor/advisor-security-recommendations) を使用して、Contoso Webstore デモ CDE のすべての未解決の問題が修復されたことを確認します。|



### <a name="pci-dss-requirement-1134"></a>PCI DSS 要件 11.3.4

**11.3.4** セグメント化を使用して CDE を他のネットワークから分離する場合は、侵入テストを少なくとも年 1 回とセグメント化コントロール/メソッドの変更後に実行して、セグメント化メソッドが動作し有効であることを確認し、すべてのスコープ外システムを CDE 内のシステムから分離します。

**責任:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | Microsoft Azure プラットフォーム コンポーネントで既知のセキュリティ脆弱性を監視するための手順が確立されています。 <br /><br /><br /><br />Azure 運用環境の優先度の高いコンポーネントに対して、対象となる包括的なセキュリティ脆弱性スキャンが四半期ごとに実行されて、セキュリティの脆弱性を識別します。 結果は関係者にレポートされ、修復は開示を通じてチームによって追跡されます。 |
| **お客様 <br />(PCI&#8209;DSS&nbsp;Blueprint)** | 脆弱性の情報と修復を提供する [Azure Security Center](https://azure.microsoft.com/services/security-center/) と [Azure Advisor](/azure/advisor/advisor-security-recommendations) を使用して、Contoso Webstore デモ CDE のすべての未解決の問題が修復されたことを確認します。|



### <a name="pci-dss-requirement-11341"></a>PCI DSS 要件 11.3.4.1

**11.3.4.1** "*サービス プロバイダーのみに対する追加の要件:*" セグメント化を使用する場合は、セグメント化コントロールの侵入テストを少なくとも 6 か月に 1 回とセグメント化コントロール/メソッドの変更後に実行して、PCI DSS スコープを確認します。

> [!NOTE]
> この要件は、2018 年 1 月 31 日まではベスト プラクティスで、その後は要件になります。


**責任:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | 「[要件 11.3.4](#pci-dss-requirement-11-3-4)」の「Microsoft Azure」のセクションをご覧ください。 |
| **お客様 <br />(PCI&#8209;DSS&nbsp;Blueprint)** | 脆弱性の情報と修復を提供する [Azure Security Center](https://azure.microsoft.com/services/security-center/) と [Azure Advisor](/azure/advisor/advisor-security-recommendations) を使用して、Contoso Webstore デモ CDE のすべての未解決の問題が修復されたことを確認します。|



## <a name="pci-dss-requirement-114"></a>PCI DSS 要件 11.4

**11.4** 侵入検出や侵入防止の手法を使用して、ネットワークへの侵入の検出や防止を行います。 カード所有者データ環境の境界およびカード所有者データ環境内の重要ポイントですべてのトラフィックを監視し、侵害が疑われる場合は担当者に通知します。
すべての侵入検出および防止のエンジン、ベースライン、署名を最新に保ちます。

**責任:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | Microsoft Azure は運用環境内のイベントのリアルタイム解析を実施し、IDS システムはシステムを侵害する可能性のあるイベントについてほぼリアルタイムでアラートを生成します。 |
| **お客様 <br />(PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore は PaaS サービスであり、ネットワーク侵入の検出と防止は Azure の責任です。 [Azure Security Center](https://azure.microsoft.com/services/security-center/) と [Azure Advisor](/azure/advisor/advisor-security-recommendations) は、侵入アラートと修復を提供します。|



## <a name="pci-dss-requirement-115"></a>PCI DSS 要件 11.5

**11.5** 変更検出メカニズム (ファイルの整合性監視ツールなど) をデプロイして、重要なシステム ファイル、構成ファイル、コンテンツ ファイルの無許可の変更 (変更、追加、削除を含む) を担当者に通知します。また、重要なファイルの比較を少なくとも毎週実行するようにソフトウェアを構成します。 

> [!NOTE]
> 変更検出の目的では、通常、重要なファイルとは、定期的には変更されず、その変更がシステムの侵害や侵害のリスクを示す可能性があるファイルです。 ファイルの整合性監視製品などの変更検出メカニズムは、通常は関連オペレーティング システムの重要なファイルでは事前に構成されています。 カスタム アプリケーション用などのその他の重要なファイルは、エンティティ (つまり、マーチャントまたはサービス プロバイダー) によって評価および定義される必要があります。

**責任:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | Microsoft Azure は、オンライン サービス ダッシュボードを通じて、サービスのセキュリティや可用性に影響する可能性のある潜在的な変更とイベントを保守し、お客様に通知します。 Microsoft Azure のお客様のセキュリティに関するコミットメントとセキュリティに関する義務に対する変更は、適切なタイミングで Microsoft Azure Web サイトで更新されます。<br /><br />Microsoft Azure 運用環境上のソフトウェアのインストールまたは変更は承認された管理担当者に制限され、変更管理手順に従います。 |
| **お客様 <br />(PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore デモは PaaS サービスであり、変更検出は Log Analytics を使用して実装されています。 詳しくは、[PCI ガイダンス - インストール済み管理ソリューション](payment-processing-blueprint.md#management-solutions)に関するページをご覧ください。<br /><br />|



### <a name="pci-dss-requirement-1151"></a>PCI DSS 要件 11.5.1

**11.5.1** 変更検出ソリューションによって生成されたアラートに応答するプロセスを実装します。

**責任:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | Azure 監視イベント ルールは、リスクの高い操作とアセットに対し、より高いレベルの監視を提供します。 確立されたセキュリティ標準へのコンプライアンスのために、Azure が管理するネットワーク デバイスが監視されます。 |
| **お客様 <br />(PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore の変更アラートは、Log Analytics の実装によって提供されます。 詳しくは、[PCI ガイダンス - インストール済み管理ソリューション](payment-processing-blueprint.md#management-solutions)に関するページをご覧ください。<br /><br /><br /><br />|



## <a name="pci-dss-requirement-116"></a>PCI DSS 要件 11.6

**11.6** セキュリティ ポリシーおよびセキュリティの監視とテストに関する運用手順が文書化され、使用され、すべての関係者に通知されます。

**責任:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | 適用不可。 |
| **お客様 <br />(PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore の変更アラートは、Log Analytics の実装によって提供されます。 詳しくは、[PCI ガイダンス - インストール済み管理ソリューション](payment-processing-blueprint.md#management-solutions)に関するページをご覧ください。<br /><br /><br /><br />|




