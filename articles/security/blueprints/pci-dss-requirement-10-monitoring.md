---
title: "Azure Payment Processing Blueprint - 監視要件"
description: "PCI DSS 要件 10"
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 293a1673-54bc-478c-9400-231074004eee
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: 5fa1d17e68ce04b1f67081479518279be6cca099
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2017
---
# <a name="monitoring-requirements-for-pci-dss-compliant-environments"></a>PCI DSS に準拠する環境の監視要件 
## <a name="pci-dss-requirement-10"></a>PCI DSS 要件 10

**ネットワーク リソースおよびカード会員データへのすべてのアクセスの追跡と監視**

> [!NOTE]
> これらの要件は、[PCI データ セキュリティ基準 (DSS) バージョン 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss) の一部として [Payment Card Industry (PCI) Security Standards Council](https://www.pcisecuritystandards.org/pci_security/) によって定義されています。 各要件のテスト手順およびガイダンスについては、PCI DSS をご覧ください。

ログ メカニズムおよびユーザー アクティビティの追跡機能は、データのセキュリティ侵害の防止と検出、またその影響を最小限に抑えるためにクリティカルです。 すべての環境においてログが存在することにより、問題発生時の徹底的な追跡、アラート、および分析が可能になります。 システム アクティビティ ログがなければ、セキュリティ侵害の原因を判別することは不可能ではないまでも非常に困難です。

## <a name="pci-dss-requirement-101"></a>PCI DSS 要件 10.1

**10.1** システム コンポーネントへのすべてのアクセスを各ユーザにリンクする監査証跡を実装します。

**責任:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | Microsoft Azure は、管理ツールおよび診断ツールへのアクセスを、対応する職責を持つ承認されたユーザーのみに制限します。 Microsoft Azure は、実稼働環境で使用されるツールへのアクセスを最低限の特権に基づいて制限します。 Microsoft Azure は、プラットフォーム環境の Microsoft Azure システム コンポーネントに対する個々のユーザーのすべてのアクセスのログを記録して保持します。<br /><br />Microsoft Azure プラットフォーム コンポーネント (OS、CloudNet、Fabric など) は、セキュリティ イベントのログと収集を行うように構成されます。 Microsoft Azure プラットフォームでの管理者のアクティビティはログに記録されます。 |
| **お客様<br /> (PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore には、すべてのシステムとユーザー アクティビティの広範囲のログがあります (CHD ログを含む)。 詳しくは、[PCI ガイダンスのログ](payment-processing-blueprint.md#logging-and-auditing)に関する記事をご覧ください。|



## <a name="pci-dss-requirement-102"></a>PCI DSS 要件 10.2

**10.2** 次のイベントを再現するために、すべてのシステム コンポーネントの自動監査証跡を実装します。
- **10. 2.1.** カード会員データへのすべての個人アクセス
- **10.2.2** ルートまたは管理者特権を持つ個人によって行われたすべてのアクション
- **10.2.3** すべての監査証跡へのアクセス
- **10.2.4** 無効な論理アクセス試行
- **10.2.5** 識別と認証メカニズムの使用および変更 (新しいアカウントの作成、特権の昇格を含むがこれらに限定されない)、およびルートまたは管理者特権を持つアカウントの変更、追加、削除のすべて
- **10.2.6** 監査ログの初期化、停止、または一時停止
- **10.2.7** システムレベル オブジェクトの作成および削除

**責任:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | Microsoft Azure は、管理ツールおよび診断ツールへのアクセスを、対応する職責を持つ承認されたユーザーのみに制限します。 Microsoft Azure は、実稼働環境で使用されるツールへのアクセスを最低限の特権に基づいて制限します。 Microsoft Azure は、プラットフォーム環境の Microsoft Azure システム コンポーネントに対する個々のユーザーのすべてのアクセスのログを記録して保持します。<br /><br />Microsoft Azure プラットフォーム コンポーネント (OS、CloudNet、Fabric など) は、セキュリティ イベントのログと収集を行うように構成されます。 Microsoft Azure プラットフォームでの管理者のアクティビティはログに記録されます。 |
| **お客様<br /> (PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore には、すべてのシステムとユーザー アクティビティの広範囲のログがあります (CHD ログを含む)。 詳しくは、[PCI ガイダンスのログ](payment-processing-blueprint.md#logging-and-auditing)に関する記事をご覧ください。|



## <a name="pci-dss-requirement-103"></a>PCI DSS 要件 10.3

**10.3** イベントごとに、すべてのシステム コンポーネントについて少なくとも以下の監査証跡エントリを記録します。
- **10.3.1** ユーザー識別
- **10.3.2** イベントの種類
- **10.3.3** 日付と時刻
- **10.3.4** 成功または失敗を示す情報
- **10.3.5** イベントの発生元
- **10.3.6** 影響を受けるデータ、システム コンポーネント、またはリソースの ID または名前

**責任:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | Microsoft Azure は、NTP Stratum 1 タイム サーバーを Global Positioning System (GPS) 衛星と同期して、Microsoft Azure 環境でサーバーとネットワーク デバイスを同期する手順を確立しました。 同期は 5 分ごとに自動的に実行されます。 Microsoft Azure は、サービス ホストが正常に同期していることを保証します。 |
| **お客様<br /> (PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore は、10.3 コントロールで必要となる、ユーザー ID、イベントの種類、日付タイムスタンプ、イベントの成功または失敗、イベント発生元、リソース名を記録します。|



## <a name="pci-dss-requirement-104"></a>PCI DSS 要件 10.4

**10.4** 時刻同期テクノロジを使用してすべての重要なシステム クロックと時刻を同期し、時刻を取得、配布、保存するために以下の要件が実施されていることを確認します。 
> [!NOTE]
> 時刻同期テクノロジの 1 つの例は、ネットワーク タイム プロトコル (NTP) です。

**責任:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | Microsoft Azure は、NTP Stratum 1 タイム サーバーを Global Positioning System (GPS) 衛星と同期して、Microsoft Azure 環境でサーバーとネットワーク デバイスを同期する手順を確立しました。 同期は 5 分ごとに自動的に実行されます。 Microsoft Azure は、サービス ホストが正常に同期していることを保証します。 |
| **お客様<br /> (PCI&#8209;DSS&nbsp;Blueprint)** | PaaS サービスの時刻同期は Azure によって実行されます。|



### <a name="pci-dss-requirement-1041"></a>PCI DSS 要件 10.4.1

**10.4.1** 重要なシステムの時刻が正確で一致しています。

**責任:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | [要件 10.4](#pci-dss-requirement-10-4) の「Microsoft Azure」のセクションをご覧ください。 |
| **お客様<br /> (PCI&#8209;DSS&nbsp;Blueprint)** | PaaS サービスの時刻同期は Azure によって実行されます。|



### <a name="pci-dss-requirement-1042"></a>PCI DSS 要件 10.4.2

**10.4.2** 時刻データが保護されています。

**責任:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | [要件 10.4](#pci-dss-requirement-10-4) の「Microsoft Azure」のセクションをご覧ください。 |
| **お客様<br /> (PCI&#8209;DSS&nbsp;Blueprint)** | PaaS サービスの時刻同期は Azure によって実行されます。|



### <a name="pci-dss-requirement-1043"></a>PCI DSS 要件 10.4.3

**10.4.3** 業界で認知されている時刻ソースから時刻設定を受信しています。

**責任:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | [要件 10.4](#pci-dss-requirement-10-4) の「Microsoft Azure」のセクションをご覧ください。 |
| **お客様<br /> (PCI&#8209;DSS&nbsp;Blueprint)** | PaaS サービスの時刻同期は Azure によって実行されます。|



## <a name="pci-dss-requirement-105"></a>PCI DSS 要件 10.5

**10.5** 変更できないよう監査証跡をセキュリティで保護します。

**責任:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | FIM および IDS ツールが Microsoft Azure 環境に実装されています。 Microsoft Azure は EWS を使用して、運用環境でのイベントのリアルタイム分析をサポートします。 MA および AIMS は、システムのセキュリティを侵害する可能性のあるイベントについてほぼリアルタイムでアラートを生成します。 <br /><br />サービス、ユーザー、およびセキュリティ イベントのログ記録 (Web サーバー ログや FTP サーバー ログなど) が有効になっており、一元的に保存されています。 Azure は、監査ログに対するアクセスを、職責に基づいて承認されたユーザーのみに制限します。 イベント ログは Azure のセキュアなアーカイブ インフラストラクチャにアーカイブされ、180 日間保存されます。 |
| **お客様<br /> (PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore は、すべての要素の監査を OMS に提供します。 外部ソースへのバックアップは [Azure Backup](https://azure.microsoft.com/services/backup/) を使用して行うことができます。|



### <a name="pci-dss-requirement-1051"></a>PCI DSS 要件 10.5.1

**10.5.1** 監査証跡の表示を、仕事関連のニーズを持つユーザーに制限します。

**責任:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | [要件 10.5](#pci-dss-requirement-10-5) の「Microsoft Azure」のセクションをご覧ください。 |
| **お客様<br /> (PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore は、すべての要素の監査を OMS に提供します。 外部ソースへのバックアップは [Azure Backup](https://azure.microsoft.com/services/backup/) を使用して行うことができます。|



### <a name="pci-dss-requirement-1052"></a>PCI DSS 要件 10.5.2

**10.5.2** 監査証跡ファイルを許可されない変更から保護します。

**責任:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | [要件 10.5](#pci-dss-requirement-10-5) の「Microsoft Azure」のセクションをご覧ください。 |
| **お客様<br /> (PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore は、すべての要素の監査を OMS に提供します。 外部ソースへのバックアップは [Azure Backup](https://azure.microsoft.com/services/backup/) を使用して行うことができます。|



### <a name="pci-dss-requirement-1053"></a>PCI DSS 要件 10.5.3

**10.5.3** 監査証跡ファイルは、一元管理されている変更が困難なログ サーバーまたはメディアにすぐにバックアップします。

**責任:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | [要件 10.5](#pci-dss-requirement-10-5) の「Microsoft Azure」のセクションをご覧ください。 |
| **お客様<br /> (PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore は、すべての要素の監査を OMS に提供します。 外部ソースへのバックアップは [Azure Backup](https://azure.microsoft.com/services/backup/) を使用して行うことができます。|



### <a name="pci-dss-requirement-1054"></a>PCI DSS 要件 10.5.4

**10.5.4** 外部に公開されているテクノロジのログは、一元管理されている安全な内部ログ サーバーまたはメディア デバイス上に書き込みます。

**責任:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | [要件 10.5](#pci-dss-requirement-10-5) の「Microsoft Azure」のセクションをご覧ください。 |
| **お客様<br /> (PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore は、すべての要素の監査を OMS に提供します。 外部ソースへのバックアップは [Azure Backup](https://azure.microsoft.com/services/backup/) を使用して行うことができます。|



### <a name="pci-dss-requirement-1055"></a>PCI DSS 要件 10.5.5

**10.5.5** ログに対してファイル整合性監視または変更検出ソフトウェアを使用して、既存のログ データを変更するとアラートが生成されるようにする (ただし、新しいデータの追加ではアラートを生成させません)。

**責任:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | [要件 10.5](#pci-dss-requirement-10-5) の「Microsoft Azure」のセクションをご覧ください。 |
| **お客様<br /> (PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore は、すべての要素の監査を OMS に提供します。 外部ソースへのバックアップは [Azure Backup](https://azure.microsoft.com/services/backup/) を使用して行うことができます。|



## <a name="pci-dss-requirement-106"></a>PCI DSS 要件 10.6

**10.6** すべてのシステム コンポーネントのログとセキュリティ イベントを調べ、異常なアクティビティまたは疑わしいアクティビティを特定します。
 
> [!NOTE]
> ログの収集、解析、およびアラートのツールを使用して、この要件に準拠することができます。

**責任:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | FIM および IDS ツールが Microsoft Azure 環境に実装されています。 Microsoft Azure は EWS を使用して、運用環境でのイベントのリアルタイム分析をサポートします。 MA および AIMS は、システムのセキュリティを侵害する可能性のあるイベントについてほぼリアルタイムでアラートを生成します。 <br /><br />サービス、ユーザー、およびセキュリティ イベントのログ記録 (Web サーバー ログや FTP サーバー ログなど) が有効になっており、一元的に保存されています。 Azure は、監査ログに対するアクセスを、職責に基づいて承認されたユーザーのみに制限します。 イベント ログは Azure のセキュアなアーカイブ インフラストラクチャにアーカイブされ、180 日間保存されます。 |
| **お客様<br /> (PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore は、異常の監視、レポート、および防止のために、[Azure Security Center](https://azure.microsoft.com/services/security-center/) を使用します。 [Azure Advisor](/azure/advisor/advisor-security-recommendations) では、すべての Azure リソースに関して一貫性のある推奨事項が一元的にまとめて示されます。|



### <a name="pci-dss-requirement-1061"></a>PCI DSS 要件 10.6.1

**10.6.1** 毎日 1 回以上次の項目を確認します。
- すべてのセキュリティ イベント
- CHD またはSAD (あるいは両方) を保存、処理、または送信するすべてのシステム コンポーネントのログ
- すべての重要なシステム コンポーネントのログ
- すべてのサーバーとセキュリティ機能を実行するシステム コンポーネント (ファイアウォール、侵入検知システム/侵入防止システム (IDS/IPS)、認証サーバー、eコマース リダイレクト サーバーなど) のログ

**責任:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | [要件 10.6](#pci-dss-requirement-10-6) の「Microsoft Azure」のセクションをご覧ください。 |
| **お客様<br /> (PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore は、異常の監視、レポート、および防止のために、[Azure Security Center](https://azure.microsoft.com/services/security-center/) を使用します。 [Azure Advisor](/azure/advisor/advisor-security-recommendations) では、すべての Azure リソースに関して一貫性のある推奨事項が一元的にまとめて示されます。|



### <a name="pci-dss-requirement-1062"></a>PCI DSS 要件 10.6.2

**10.6.2** 組織の年間リスク アセスメントによって決定されたポリシーおよびリスク管理戦略に基づいて、他のシステム コンポーネントすべてのログを定期的にレビューします。

**責任:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | [要件 10.6](#pci-dss-requirement-10-6) の「Microsoft Azure」のセクションをご覧ください。 |
| **お客様<br /> (PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore は、異常の監視、レポート、および防止のために、[Azure Security Center](https://azure.microsoft.com/services/security-center/) を使用します。 [Azure Advisor](/azure/advisor/advisor-security-recommendations) では、すべての Azure リソースに関して一貫性のある推奨事項が一元的にまとめて示されます。|



### <a name="pci-dss-requirement-1063"></a>PCI DSS 要件 10.6.3

**10.6.3** レビュー プロセスで特定された例外と異常を追跡調査します。

**責任:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | [要件 10.6](#pci-dss-requirement-10-6) の「Microsoft Azure」のセクションをご覧ください。 |
| **お客様<br /> (PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore は、異常の監視、レポート、および防止のために、[Azure Security Center](https://azure.microsoft.com/services/security-center/) を使用します。 [Azure Advisor](/azure/advisor/advisor-security-recommendations) では、すべての Azure リソースに関して一貫性のある推奨事項が一元的にまとめて示されます。|



## <a name="pci-dss-requirement-107"></a>PCI DSS 要件 10.7

**10.7** 監査証跡の履歴を少なくとも 1 年間保持し、少なくとも 3 か月はすぐに分析できる状態にしておきます (オンライン、アーカイブ、バックアップから復元可能など)。

**責任:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | Microsoft Azure は、監査ログを 1 年間保持しており、最新の 3 か月分は内部ポータルからすぐにアクセスできます。 |
| **お客様<br /> (PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore には、すべてのシステムとユーザー アクティビティの広範囲のログがあります (CHD ログを含む)。 詳しくは、[PCI ガイダンスのログ](payment-processing-blueprint.md#logging-and-auditing)に関する記事をご覧ください。|



## <a name="pci-dss-requirement-108"></a>PCI DSS 要件 10.8

**10.8** **サービス プロバイダーのみの追加要件:** 重要なセキュリティ制御システムの障害を適切なタイミングで検出および報告するためのプロセスを実装します。以下の障害を含むがこれらに限定されません。
- ファイアウォール
- IDS/IPS
- FIM
- ウイルス対策
- 物理アクセス制御
- 論理アクセス制御
- 監査ログ メカニズム
- セグメント化制御 (使用されている場合) 

> [!NOTE]
> この要件は、2018 年 1 月 31 日まではベスト プラクティスに指定され、その後は要件になります。



**責任:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | Microsoft Azure は EWS を使用して、運用環境でのイベントのリアルタイム分析をサポートします。 MA および AIMS は、システムのセキュリティを侵害する可能性のあるイベントについてほぼリアルタイムでアラートを生成します。 |
| **お客様<br /> (PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore には、すべてのシステムとユーザー アクティビティの広範囲のログがあります (CHD ログを含む)。 詳しくは、[PCI ガイダンスのログ](payment-processing-blueprint.md#logging-and-auditing)に関する記事をご覧ください。|



### <a name="pci-dss-requirement-1081"></a>PCI DSS 要件 10.8.1

**10.8.1** **サービス プロバイダーのみの追加要件:** すべての重要なセキュリティ制御の障害に適切なタイミングでに対応します。 セキュリティ制御の障害に対応するためのプロセスには以下を含む必要があります。
- セキュリティ機能の復元
- セキュリティ障害の期間 (開始と終了の日付時刻) の識別および文書化
- 根本原因を含む障害の原因の識別と文書化、および根本原因に対処する改善案の文書化
- 障害中に発生したすべてのセキュリティ問題の識別および対処
- セキュリティ障害の結果、さらなる処置が必要かどうか判断するためのリスク アセスメントの実施
- 障害原因の再発防止策の実装 (セキュリティ制御の監視の再開) 

> [!NOTE]
> この要件は、2018 年 1 月 31 日まではベスト プラクティスに指定され、その後は要件になります。


**責任:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | Microsoft Azure は EWS を使用して、運用環境でのイベントのリアルタイム分析をサポートします。 MA および AIMS は、システムのセキュリティを侵害する可能性のあるイベントについてほぼリアルタイムでアラートを生成します。 |
| **お客様<br /> (PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore には、すべてのシステムとユーザー アクティビティの広範囲のログがあります (CHD ログを含む)。 詳しくは、[PCI ガイダンスのログ](payment-processing-blueprint.md#logging-and-auditing)に関する記事をご覧ください。|



## <a name="pci-dss-requirement-109"></a>PCI DSS 要件 10.9

**10.9** ネットワーク リソースとカード会員データへのすべてのアクセスを監視するためのセキュリティ ポリシーと運用手順が文書化され、使用され、すべての関係者に通知されます。


**責任:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | 適用不可。 |
| **お客様<br /> (PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore は、CHD の管理と保護の方法いついてユース ケースと説明を提供します。|




