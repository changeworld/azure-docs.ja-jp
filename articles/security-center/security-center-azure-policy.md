---
title: 個別にまたは Azure ポリシーの一部として、azure Security Center セキュリティ ポリシーを設定することができます |Microsoft Docs
description: このドキュメントでは、Azure Security Center で、または Azure Policy では、ポリシーを設定できます。
services: security-center
documentationcenter: na
author: TerryLanfear
manager: mbaldwin
editor: ''
ms.assetid: cd906856-f4f9-4ddc-9249-c998386f4085
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/5/2018
ms.author: terrylan
ms.openlocfilehash: 0b38c6895421b43d6f80e0c34cc23b379a673559
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/07/2018
ms.locfileid: "51261946"
---
# <a name="setting-security-policies-in-security-center-or-in-azure-policy"></a>Azure Policy によって提供される Security Center のセキュリティ ポリシーの設定

この記事は、Azure Security Center でのセキュリティ ポリシーを構成する場合に役立ちます。 設定できるようにしてセキュリティ センターのいずれかまたは特定のサブスクリプションで Azure のポリシーと azure セキュリティ センター ポリシーを統合[Azure Policy](../azure-policy/azure-policy-introduction.md)、管理グループ間で、複数のポリシーを設定できますサブスクリプション.

## <a name="what-are-security-policies"></a>セキュリティ ポリシーとは
セキュリティ ポリシーは、ワークロードの必要な構成を定義し、会社や規制のセキュリティ要件に確実に準拠できるようにします。 Azure Security Center では、Azure サブスクリプションのポリシーを定義でき、ワークロードの種類やデータの機密性に合わせて調整できます。 たとえば、個人を特定できる情報のような、規制されたデータが使用されるアプリケーションには、他のワークロードより高いレベルのセキュリティが必要です。 設定を管理グループまたはサブスクリプション間でのポリシーを設定する [Azure Policy](../azure-policy/azure-policy-introduction.md) します。

> [!NOTE]
> 管理グループの一部または複数のポリシー割り当てがサブスクリプションで以前のセキュリティ ポリシーが構成されている場合これらのポリシー灰色で表示されます Security Center で、Azure を使用して管理グループ レベルでポリシーを管理することができます。ポリシーのページです。 

## <a name="how-security-policies-work"></a>セキュリティ ポリシーのしくみ
Security Center では、Azure サブスクリプションごとに自動で既定のセキュリティ ポリシーが作成されます。 Security Center でポリシーを編集するか、Azure Policy を使用して、以下の操作を行うことができます。
- 新しいポリシー定義を作成する。
- 組織全体または組織内の部署を表す、管理グループおよびサブスクリプションにまたがってポリシーを割り当てる。
- ポリシーのコンプライアンスを監視する。

Azure Policy の詳細については、「[コンプライアンスを強制するポリシーの作成と管理](../azure-policy/create-manage-policy.md)」を参照してください。

Azure ポリシーは、次のコンポーネントで構成されています。

- **ポリシー**は、規則です
- **イニシアティブ**は、ポリシーのコレクションです
- **割り当て**は、特定のスコープ (管理グループ、サブスクリプション、またはリソース グループ) にイニシアティブまたはポリシーを適用することです

リソースは、割り当てられたポリシーに対して評価され、リソースが準拠しているポリシーの数に従ってコンプライアンス比率が計算されます。

## <a name="who-can-edit-security-policies"></a>セキュリティ ポリシーを編集できるユーザーは誰ですか。
Security Center ではロールベースのアクセス制御 (RBAC) が使用されています。RBAC が提供する組み込みのロールは、Azure でユーザー、グループ、サービスに割り当てることができます。 ユーザーが Security Center を開くと、アクセス権のあるリソースに関する情報のみが表示されます。 これは、サブスクリプションまたはリソースが属するリソース グループについて、所有者、共同作成者、閲覧者のいずれかのロールがユーザーに割り当てられていることを意味します。 これらのロールに加え、Security Center には、次の 2 つの固有のロールがあります。

- セキュリティ閲覧者: Security Center に対する閲覧権限を持ちます。推奨事項、アラート、ポリシー、および正常性を確認できますが、変更を加えることはできません。
- セキュリティ管理者: セキュリティ閲覧者と同じ閲覧権限を持ちますが、セキュリティ ポリシーの更新と推奨事項とアラートの解除を実行することもできます。

## <a name="edit-security-policies"></a>セキュリティ ポリシーの編集
Security Center では、各 Azure サブスクリプションおよび管理グループの既定のセキュリティ ポリシーを編集できます。 セキュリティ ポリシーを変更するには、サブスクリプションまたはそれが含まれる管理グループの所有者、共同作成者、セキュリティ管理者のいずれかである必要があります。 Security Center でセキュリティ ポリシーを表示するには:

> [!NOTE]
> すべてのポリシー設定を管理グループの一部または複数のポリシー割り当てがサブスクリプションでは、Security Center に灰色で表示されます。 これらのポリシーを編集する [Azure Policy](../azure-policy/azure-policy-introduction.md) します。 

1. **Security Center** ダッシュボードの **[ポリシーとコンプライアンス]** で、**[セキュリティ ポリシー]** を選択します。 **[ポリシー管理]** が開きます。

    ![[ポリシー管理] ウィンドウ](./media/security-center-azure-policy/security-center-policies-fig10.png)

  [ポリシー管理] には、管理グループ、サブスクリプション、およびワークスペースの数、および管理グループの構造が表示されます。

  > [!NOTE]
  > Security Center のダッシュボードの **[サブスクリプションの対象範囲]** に表示されるサブスクリプション数が、**[ポリシー管理]** に表示されるサブスクリプション数より多く表示される場合があります。 [サブスクリプションの対象範囲] は、Standard、Free、および "対象外の" サブスクリプションの数を示します。 "対象外の" サブスクリプションは、Security Center が有効になっておらず、**[ポリシー管理]** に表示されません。
  >
  >

  表には次の列が表示されます。

 - [ポリシー イニシアティブ割り当て] - サブスクリプションまたは管理グループに割り当てられている Security Center の組み込みポリシーおよびイニシアティブ。
 - [コンプライアンス] - 管理グループ、サブスクリプション、またはワークスペースの全体的なコンプライアンス スコア。 スコアは、割り当ての加重平均です。 加重平均では、1 つの割り当てにおけるポリシーの数と、割り当てが適用されるリソースの数が考慮されます。

 たとえば、サブスクリプションに 2 つの VM と 5 つのポリシーが割り当てられたイニシアティブがある場合、このサブスクリプションには 10 のアセスメントがあります。 VM の 1 つが 2 つのポリシーに準拠していない場合、サブスクリプションの割り当ての全体的なコンプライアンス スコアは 80% になります。

 - [カバレッジ] - 管理グループ、サブスクリプション、またはワークスペースが実行されている価格レベル (Free または Standard) を識別します。  Security Center の価格レベルの詳細については、[価格](security-center-pricing.md)に関するページを参照してください。
 - [設定] - サブスクリプションには **[設定の編集]** リンクがあります。 **[設定の編集]** を選択すると、データ収集、価格レベル、メール通知などのサブスクリプション設定を更新できます。

2. セキュリティ ポリシーを有効にするサブスクリプションまたは管理グループを選択します。 **[セキュリティ ポリシー]** が開きます。

3.  **[セキュリティ ポリシー]** で、Security Center で監視するコントロールを選択し、**[オン]** を選択して推奨事項を提供します。  Security Center でそのコントロールを監視しない場合は、**[オフ]** を選択します。

    ![ポリシー コンポーネント](./media/security-center-azure-policy/security-policy.png)

4. **[保存]** を選択します。

## <a name="available-security-policy-definitions"></a>利用可能なセキュリティ ポリシーの定義

既定のセキュリティ ポリシーで利用可能なポリシー定義については、次の表を参照してください。

| ポリシー | 有効なポリシーによって実行される内容 |
| --- | --- |
| システムの更新プログラム |Windows Update または Windows Server Update Services から、利用できる毎日のセキュリティ更新プログラムと重要な更新プログラムの一覧を取得します。 取得される一覧は、その仮想マシンに対して構成されているサービスによって異なります。この一覧によって、不足している更新プログラムを適用するよう推奨されます。 Linux システムの場合、ポリシーはディストリビューション提供のパッケージ管理システムを利用し、使用できる更新プログラムが含まれているパッケージを特定します。 また、[Azure Cloud Services](../cloud-services/cloud-services-how-to-configure-portal.md) 仮想マシンのセキュリティ更新プログラムと重要な更新プログラムについても確認します。 |
| セキュリティ構成 |オペレーティング システム構成を毎日分析し、攻撃に対する仮想マシンの脆弱性を引き起こすおそれのある問題を特定します。 また、このポリシーによって、これらの脆弱性に対応するための構成の変更が推奨されます。 監視対象の具体的な構成の詳細については、[推奨されるベースラインの一覧](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335)を参照してください。 (現時点では、Windows Server 2016 は完全にはサポートされていません)。 |
| エンドポイント保護 |ウイルス、スパイウェア、およびその他の悪意のあるソフトウェアを特定して削除するため、すべての Windows 仮想マシン (VM) にエンドポイント保護を設定することをお勧めします。 |
| ディスクの暗号化 |静止データの保護を強化するために、ディスク暗号化はすべての仮想マシンで有効にすることをお勧めします。 |
| ネットワーク セキュリティ グループ |パブリック エンドポイントがある VM への受信トラフィックと送信トラフィックを制御するように、 [ネットワーク セキュリティ グループ](../virtual-network/security-overview.md) を構成することを推奨します。 サブネットで構成されたネットワーク セキュリティ グループは、指定しない限り、すべての仮想マシンのネットワーク インターフェイスによって継承されます。 このポリシーは、ネットワーク セキュリティ グループが構成されているかどうかをチェックするほか、受信セキュリティ規則を評価して、受信トラフィックを許可する規則を特定します。 |
| Web アプリケーション ファイアウォール |次のいずれかに当てはまる場合に、Web アプリケーション ファイアウォールを仮想マシンに設定することをお勧めします。 <ul><li>[インスタンスレベル パブリック IP](../virtual-network/virtual-networks-instance-level-public-ip.md) が使用されていて、関連するネットワーク セキュリティ グループの受信セキュリティ規則が、ポート 80/443 にアクセスできるように構成されている。</li><li>負荷分散された IP が使用されていて、関連する負荷分散規則と受信ネットワーク アドレス変換 (NAT) 規則が、ポート 80/443 にアクセスできるように構成されている。 詳細については、「 [Azure Resource Manager によるロード バランサーのサポート](../load-balancer/load-balancer-arm.md)」を参照してください。</li> |
| 次世代のファイアウォール |Azure に組み込まれているネットワーク セキュリティ グループの適用範囲外までネットワーク保護を拡張します。 次世代ファイアウォールの利用が推奨されるデプロイが Security Center によって検出されると、仮想アプライアンスを設定できるようになります。 |
| SQL の監査と脅威の検出 |コンプライアンスのために、また高度な脅威検出の調査のために、Azure データベースへのアクセスの監査を有効にすることを推奨します。 |
| SQL の暗号化 |Azure SQL データベース、関連付けられたバックアップ、トランザクション ログ ファイルに対し、保存中の暗号化を有効にすることを推奨します。 セキュリティ侵害を受けた場合でも、データが読み取られることはありません。 |
| 脆弱性評価 |VM に脆弱性評価ソリューションをインストールすることをお勧めします。 |
| ストレージ暗号化 |現在この機能は、Azure Blob Storage と Azure Files で利用できます。 [Storage サービスの暗号化] を有効にした後、暗号化されるのは新しいデータのみであり、このストレージ アカウントにある既存のファイルは暗号化されません。 |
| JIT ネットワーク アクセス |ジャスト イン タイム ネットワーク アクセスが有効になっている場合、Security Center ではネットワーク セキュリティ グループの規則の作成により、Azure VM への受信トラフィックがロックダウンされます。 ユーザーは VM 上の受信トラフィックをロックダウンする必要があるポートを選択します。 詳細については、「[Manage virtual machine access using just in time (ジャスト イン タイムを使用して仮想マシンへのアクセスを管理する)](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)」を参照してください。 |

## <a name="management-groups"></a>管理グループ
組織に多数のサブスクリプションがある場合は、これらのサブスクリプションのアクセス、ポリシー、およびコンプライアンスを効率的に管理する方法が必要になることがあります。 Azure 管理グループの範囲は、サブスクリプションを上回ります。 "管理グループ" と呼ばれるコンテナーにサブスクリプションを整理して、管理グループに管理ポリシーを適用できます。 管理グループ内のすべてのサブスクリプションは、管理グループに適用されたポリシーを自動的に継承します。 各ディレクトリには、"ルート" 管理グループと呼ばれる 1 つの最上位管理グループがあります。 このルート管理グループは階層に組み込まれており、すべての管理グループとサブスクリプションはルート管理グループにまとめられます。 ルート管理グループにより、グローバル ポリシーと RBAC の割り当てをディレクトリ レベルで適用できます。 Azure Security Center で使用する管理グループを設定するには、「[Gain tenant-wide visibility for Azure Security Center (Azure Security Center のテナント全体の可視性の獲得)](security-center-management-groups.md)」の指示に従ってください。 

> [!NOTE]
> 管理グループとサブスクリプションの階層を理解することが重要です。 管理グループ、ルート管理、および管理グループへのアクセスの詳細については、「[Organize your resources with Azure Management Groups (Azure 管理グループを使用してリソースを整理する)](../governance/management-groups/index.md#root-management-group-for-each-directory)」を参照してください。
>
>


## <a name="next-steps"></a>次の手順
この記事では、Security Center でのセキュリティ ポリシーの構成方法について説明しました。 Security Center の詳細については、次の記事を参照してください。

* [Azure Security Center 計画および運用ガイド](security-center-planning-and-operations-guide.md): Azure Security Center に関する設計上の考慮事項を計画および理解する方法について説明します。
* [Azure Security Center でのセキュリティ ヘルスの監視](security-center-monitoring.md): Azure リソースの正常性を監視する方法について説明しています。
* [Azure Security Center でのセキュリティの警告の管理と対応](security-center-managing-and-responding-alerts.md): セキュリティの警告の管理と対応の方法について説明しています。
* [Azure Security Center を使用したパートナー ソリューションの監視](security-center-partner-solutions.md): パートナー ソリューションの正常性状態を監視する方法について説明しています。
* [Gain tenant-wide visibility for Azure Security Center (Azure Security Center のテナント全体の可視性の獲得)](security-center-management-groups.md): Azure Security Center の管理グループを設定する方法について学習します。 
* [Azure Security Center のよく寄せられる質問 (FAQ)](security-center-faq.md): このサービスの使用に関してよく寄せられる質問に対する回答を確認します。
* [Azure セキュリティ ブログ](https://blogs.msdn.com/b/azuresecurity/): Azure のセキュリティとコンプライアンスについてのブログ記事を確認できます。

Azure Policy について詳しくは、「[Azure Policy とは](../azure-policy/azure-policy-introduction.md)」をご覧ください。
