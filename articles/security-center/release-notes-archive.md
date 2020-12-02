---
title: Azure Security Center の最新情報のアーカイブ
description: 6 か月前以前の、Azure Security Center の新機能と変更点の説明。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/11/2020
ms.author: memildin
ms.openlocfilehash: 26192c742ab2e010c18c02226252dbb480b72cee
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/20/2020
ms.locfileid: "94967161"
---
# <a name="archive-for-whats-new-in-azure-security-center"></a>Azure Security Center の最新情報のアーカイブ

「[What's new in Azure Security Center? (Azure Security Center の最新情報)](release-notes.md)」という主要リリース ノート ページには、過去 6 か月間の更新情報が含まれていますが、このページにはそれ以前の項目が含まれています。

このページでは、以下に関する情報を提供します。

- 新機能
- バグの修正
- 非推奨の機能




## <a name="may-2020"></a>2020 年 5 月

5 月の更新プログラムには次のものが含まれます。
- [アラート抑制ルール (プレビュー)](#alert-suppression-rules-preview)
- [仮想マシンに対する脆弱性評価の一般提供の開始](#virtual-machine-vulnerability-assessment-is-now-generally-available)
- [Just-In-Time (JIT) 仮想マシン (VM) へのアクセスの変更](#changes-to-just-in-time-jit-virtual-machine-vm-access)
- [別のセキュリティ コントロールへのカスタム推奨事項の移動](#custom-recommendations-have-been-moved-to-a-separate-security-control)
- [推奨事項をコントロールまたは単純なリストで表示するためのトグルの追加](#toggle-added-to-view-recommendations-in-controls-or-as-a-flat-list)
- [拡張されたセキュリティ コントロール "セキュリティのベスト プラクティスの実装"](#expanded-security-control-implement-security-best-practices)
- [カスタム メタデータを使用したカスタム ポリシーの一般提供の開始](#custom-policies-with-custom-metadata-are-now-generally-available)
- [ファイルレス攻撃検出へのクラッシュ ダンプ分析機能の移行](#crash-dump-analysis-capabilities-migrating-to-fileless-attack-detection)


### <a name="alert-suppression-rules-preview"></a>アラート抑制ルール (プレビュー)

この新機能 (現在プレビュー段階) は、アラート疲れを減らすのに役立ちます。 ルールを使用して、無害であることが判明している、または組織内の通常の活動に関連しているアラートを自動的に非表示にします。 これにより、最も重要な脅威に集中できます。 

有効にした抑制ルールと一致するアラートは、引き続き生成されますが、その状態は "無視" に設定されます。 状態は Azure portal でも確認できますが、Security Center のセキュリティ アラートにアクセスして確認することもできます。

抑制ルールでは、アラートが自動的に無視される条件を定義します。 通常は、次のような場合に抑制ルールを使用します。

- ユーザーが擬陽性と判断したアラートを抑制する

- あまりにも頻繁にトリガーされるため有用ではないアラートを抑制する

詳しくは、「[Azure Security Center の脅威防止からのアラートの抑制](alerts-suppression-rules.md)」を参照してください。


### <a name="virtual-machine-vulnerability-assessment-is-now-generally-available"></a>仮想マシンに対する脆弱性評価の一般提供の開始

Security Center の Standard レベルに、追加料金なしで仮想マシンの統合脆弱性評価が追加されました。 この拡張機能には Qualys が利用されていますが、結果は Security Center に直接レポートされます。 Qualys ライセンスも Qualys アカウントも必要ありません。すべてが Security Center 内でシームレスに処理されます。

この新しいソリューションでは、お客様の仮想マシンを継続的にスキャンし、脆弱性を検出してその結果を Security Center に表示できます。 

ソリューションをデプロイするには、新しいセキュリティの推奨事項を使用します。

"組み込みの脆弱性評価ソリューションを仮想マシンで有効にする (Qualys を利用)"

詳しくは、[仮想マシンに対する Security Center の統合脆弱性評価](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)に関するページを参照してください。



### <a name="changes-to-just-in-time-jit-virtual-machine-vm-access"></a>Just-In-Time (JIT) 仮想マシン (VM) へのアクセスの変更

Security Center には、VM の管理ポートを保護するオプションの機能が備わっています。 これにより、最も一般的な形式のブルート フォース攻撃を防ぐことができます。

この更新では、この機能に次の変更が加えられました。

- VM での JIT の有効化を勧める推奨事項の名前が変更されました。 以前の "仮想マシンに Just-In-Time ネットワーク アクセス制御を適用する必要があります" から次のように変わりました: "仮想マシンの管理ポートは、Just-In-Time ネットワーク アクセス制御によって保護されている必要があります"。

- この推奨事項は、オープンな管理ポートがある場合にのみトリガーされます。

詳しくは、[JIT アクセス機能](security-center-just-in-time.md)に関するページを参照してください。


### <a name="custom-recommendations-have-been-moved-to-a-separate-security-control"></a>別のセキュリティ コントロールへのカスタム推奨事項の移動

強化されたセキュア スコアと共に導入されたセキュリティ コントロールの 1 つが、"セキュリティのベスト プラクティスの実装" です。 お客様のサブスクリプションで作成されたカスタム推奨事項は自動的に、このコントロールに配置されていました。 

カスタム推奨事項は見つけやすいように、専用のセキュリティ コントロールである "カスタム推奨事項" に移行しました。 このコントロールは、お客様のセキュア スコアに影響しません。

セキュリティ コントロールの詳細については、「[Azure Security Center の強化されたセキュア スコア (プレビュー)](secure-score-security-controls.md)」を参照してください。


### <a name="toggle-added-to-view-recommendations-in-controls-or-as-a-flat-list"></a>推奨事項をコントロールまたは単純なリストで表示するためのトグルの追加

セキュリティ コントロールは、関連するセキュリティ推奨事項の論理グループです。 これらはお客様の脆弱な攻撃対象領域を反映します。 コントロールは、セキュリティに関する一連の推奨事項と、これらの推奨事項を実装するための指示内容です。

組織が個々の攻撃対象領域をどの程度セキュリティで保護しているかをすぐに確認するには、各セキュリティ コントロールのスコアを確認します。

既定では、推奨事項がセキュリティ コントロールに表示されます。 この更新プログラムから、それらをリストとしても表示できるようになりました。 影響を受けるリソースの正常性状態で並べ替えられた単純なリストとして表示するには、新しいトグル "コントロールによるグループ化" を使用します。 このトグルはポータル内のリストの上にあります。

セキュリティ コントロール (およびこのトグル) は、新しいセキュア スコア エクスペリエンスの一部です。 ポータル内からフィードバックをお送りいただけますとさいわいです。

セキュリティ コントロールの詳細については、「[Azure Security Center の強化されたセキュア スコア (プレビュー)](secure-score-security-controls.md)」を参照してください。

:::image type="content" source="./media/secure-score-security-controls/recommendations-group-by-toggle.gif" alt-text="推奨設定の &quot;コントロールによるグループ化&quot; トグル":::

### <a name="expanded-security-control-implement-security-best-practices"></a>拡張されたセキュリティ コントロール "セキュリティのベスト プラクティスの実装" 

強化されたセキュア スコアと共に導入されたセキュリティ コントロールの 1 つが、"セキュリティのベスト プラクティスの実装" です。 このコントロールに推奨事項があっても、セキュア スコアに影響はありません。 

この更新により、3 つの推奨事項が当初配置されていたコントロールからこのベスト プラクティス コントロールに移動されました。 この操作が行われたのは、これら 3 つの推奨事項のリスクが当初考えていたよりも低いことが判明したためです。

さらに、2 つの新しい推奨事項が導入され、このコントロールに追加されました。

移動された 3 つの推奨事項は次のとおりです。

- **ご利用のサブスクリプションに対して読み取りアクセス許可があるアカウントでは、MFA を有効にする必要があります** (当初は "MFA の有効化" コントロール内)
- **読み取りアクセス許可がある外部アカウントは、ご使用のサブスクリプションから削除する必要があります** (当初は "アクセスとアクセス許可の管理" コントロール内)
- **お使いのサブスクリプションに、最大 3 人の所有者を指定する必要があります** (当初は "アクセスとアクセス許可の管理" コントロール内)

コントロールに追加された 2 つの新しい推奨事項は次のとおりです。

- **ゲスト構成拡張機能が Windows 仮想マシンにインストールされている必要がある (プレビュー)** - [Azure Policy ゲスト構成](../governance/policy/concepts/guest-configuration.md)を使用することで、サーバーとアプリケーションの設定に対して仮想マシンが可視化されます (Windows のみ)。

- **お使いのマシンで Windows Defender Exploit Guard を有効にする必要がある (プレビュー)** - Windows Defender Exploit Guard では、Azure Policy ゲスト構成エージェントが利用されます。 Exploit Guard には、さまざまな攻撃ベクトルに対してデバイスをロックダウンし、マルウェア攻撃でよく使用される動作をブロックするよう設計された 4 つのコンポーネントがありますが、企業がセキュリティ リスクと生産性の要件のバランスをとれるようになっています (Windows のみ)。

Windows Defender Exploit Guard の詳細については、「[Exploit Guard ポリシーの作成と展開](/mem/configmgr/protect/deploy-use/create-deploy-exploit-guard-policy)」を参照してください。

セキュリティ コントロールの詳細については、[強化されたセキュリティ スコア (プレビュー)](secure-score-security-controls.md) に関するページを参照してください。



### <a name="custom-policies-with-custom-metadata-are-now-generally-available"></a>カスタム メタデータを使用したカスタム ポリシーの一般提供の開始

カスタム ポリシーは、Security Center の推奨事項エクスペリエンス、セキュア スコア、規制コンプライアンス標準ダッシュボードに含まれました。 この機能の一般提供が開始され、お客様は Security Center で組織のセキュリティ評価の範囲を拡大できるようになりました。 

Azure Policy でカスタム イニシアチブを作成し、それにポリシーを追加して Azure Security Center にオンボードし、推奨事項として視覚化します。

さらに、カスタム推奨事項のメタデータを編集するオプションも追加されました。 メタデータ オプションには、重要度、修復手順、脅威情報などが含まれます。  

詳しくは、「[詳細情報でのカスタム推奨事項の拡張](custom-security-policies.md#enhance-your-custom-recommendations-with-detailed-information)」を参照してください。



### <a name="crash-dump-analysis-capabilities-migrating-to-fileless-attack-detection"></a>ファイルレス攻撃検出へのクラッシュ ダンプ分析機能の移行 

Microsoft は Windows クラッシュ ダンプ (CDA) 検出機能を[ファイルレス攻撃検出](defender-for-servers-introduction.md#what-are-the-benefits-of-azure-defender-for-servers)に移行しています。 ファイルレス攻撃検出分析では、Windows マシンで次のセキュリティ アラートの高度なバージョンを利用できます。コード インジェクションの検出、Windows モジュールの偽装の検出、シェルコードの検出、疑わしいコード セグメントの検出。

この移行のメリットをいくつか紹介します。

- **プロアクティブかつタイムリーなマルウェア検出** - CDA のアプローチでは、クラッシュが発生して初めて分析を実行し、悪意のあるアーティファクトを検出する必要がありました。 ファイルレス攻撃検出を使用すると、メモリ内の脅威が活動しているのをプロアクティブに特定できます。 

- **強化されたアラート** - ファイルレス攻撃検出のセキュリティ アラートには、CDA では利用できない強化が加わっています (例: アクティブなネットワーク接続情報)。 

- **アラートの集計** - CDA では、単一のクラッシュ ダンプ内で複数の攻撃パターンを検出した場合に、複数のセキュリティ アラートをトリガーしていました。 ファイルレス攻撃検出では、同じプロセスから特定された攻撃パターンをすべて単一のアラートにまとめて、複数のアラートを関連付ける必要をなくしています。

- **Log Analytics ワークスペースでの要件の減少** - 機密データを含んでいる可能性があるクラッシュ ダンプは、Log Analytics ワークスペースにアップロードされなくなります。






## <a name="april-2020"></a>2020 年 4 月

4 月の更新プログラムには次のものが含まれます。
- [動的コンプライアンス パッケージの一般提供の開始](#dynamic-compliance-packages-are-now-generally-available)
- [Azure Security Center Free レベルへの ID に関する推奨事項の追加](#identity-recommendations-now-included-in-azure-security-center-free-tier)


### <a name="dynamic-compliance-packages-are-now-generally-available"></a>動的コンプライアンス パッケージの一般提供の開始

Azure Security Center の規制コンプライアンス ダッシュボードに、追加の業界および規制の標準を追跡するための **動的コンプライアンス パッケージ** が追加されました (一般提供開始)。

動的コンプライアンス パッケージは、Security Center のセキュリティ ポリシー ページからサブスクリプションまたは管理グループに追加できます。 標準またはベンチマークをオンボードすると、評価として、マップされた関連するすべてのコンプライアンス データと共に、標準が規制コンプライアンス ダッシュボードに表示されます。 オンボードされた標準の概要レポートがダウンロードできるようになります。

次のような標準を追加できるようになりました。

- **NIST SP 800-53 R4**
- **SWIFT CSP CSCF-v2020**
- **UK OFFICIAL および UK NHS**
- **カナダ連邦の PBMM**
- **Azure CIS 1.1.0 (新規)** (Azure CIS 1.1.0 のより完全な表現)

さらに、**Azure セキュリティ ベンチマーク** が最近追加されました。これは Microsoft が作成したもので、一般的なコンプライアンス フレームワークに基づくセキュリティとコンプライアンスのベスト プラクティスに関する Azure 固有のガイドラインです。 追加の標準が使用可能になると、ダッシュボードでサポートされます。  
 
詳しくは、「[規制コンプライアンス ダッシュボードでの動的コンプライアンス パッケージへの更新](update-regulatory-compliance-packages.md)」を参照してください。


### <a name="identity-recommendations-now-included-in-azure-security-center-free-tier"></a>Azure Security Center Free レベルへの ID に関する推奨事項の追加

Azure Security Center Free レベルで、ID とアクセスのセキュリティに関する推奨事項の一般提供が開始されました。 これは、クラウドのセキュリティ体制管理 (CSPM) 機能を無料化する取り組みの一環です。 これまで、これらの推奨事項を利用できるのは、Standard 価格レベルだけでした。

ID とアクセスに関する推奨事項の例を次に示します。

- "ご利用のサブスクリプションに対して所有者アクセス許可があるアカウントでは、MFA を有効にする必要があります。"
- "お使いのサブスクリプションに、最大 3 人の所有者を指定する必要があります。"
- "非推奨のアカウントは、ご利用のサブスクリプションから削除する必要があります。"

Free 価格レベルのサブスクリプションをお持ちのお客様は、ID とアクセスのセキュリティについて評価されることがなかったため、この変更により、セキュア スコアに影響が生じます。

詳しくは、「[ID とアクセスの推奨事項](recommendations-reference.md#recs-identity)」を参照してください。

詳しくは、[ID とアクセスの監視](security-center-identity-access.md)に関するページを参照してください。



## <a name="march-2020"></a>2020 年 3 月

3 月の更新プログラムには次のものが含まれます。

- [ワークフロー自動化の一般提供の開始](#workflow-automation-is-now-generally-available)
- [Azure Security Center と Windows Admin Center の統合](#integration-of-azure-security-center-with-windows-admin-center)
- [Azure Kubernetes Service の保護](#protection-for-azure-kubernetes-service)
- [Just-In-Time エクスペリエンスの改善](#improved-just-in-time-experience)
- [Web アプリケーションに関する 2 つのセキュリティ推奨事項の非推奨化](#two-security-recommendations-for-web-applications-deprecated)


### <a name="workflow-automation-is-now-generally-available"></a>ワークフロー自動化の一般提供の開始

Azure Security Center のワークフロー自動化機能の一般提供が開始されました。 これを使用すると、セキュリティ アラートと推奨事項が出されたときにロジック アプリを自動でトリガーできます。 さらに、クイック修復オプションが利用可能なすべての推奨事項とアラートでは、手動のトリガーが使用可能です。

すべてのセキュリティ プログラムには、インシデント対応のための複数のワークフローが含まれています。 これらのプロセスには、直接の利害関係者への通知、変更管理プロセスの開始、および特定の修復手順の適用が含まれます。 これらのプロシージャの手順をできるだけ多く自動化することがセキュリティの専門家によって推奨されています。 自動化を行うと、オーバーヘッドが減少します。また、迅速かつ一貫した方法で、定義済みの要件に従ってプロセスの手順が実行されるようにして、セキュリティを向上させることができます。

ワークフローを実行するための Security Center の自動および手動機能の詳細については、「[ワークフローの自動化](workflow-automation.md)」を参照してください。

詳しくは、[ロジック アプリの作成](../logic-apps/logic-apps-overview.md)に関するページを参照してください。


### <a name="integration-of-azure-security-center-with-windows-admin-center"></a>Azure Security Center と Windows Admin Center の統合

Windows Admin Center から Azure Security Center にオンプレミスの Windows サーバーを直接移行できるようになりました。 これで、オンプレミスのサーバー、仮想マシン、追加の PaaS ワークロードなど、すべての Windows Admin Center リソースのセキュリティ情報を Security Center で一元的に確認できるようになります。

Windows Admin Center から Azure Security Center にサーバーを移行すると、以下が可能になります。

- Windows Admin Center の Security Center 拡張機能で、セキュリティに関するアラートと推奨事項を確認する。
- Azure portal 内の Security Center で (または API を使用して)、セキュリティ体制を確認し、Windows Admin Center で管理されているサーバーの追加の詳細情報を取得する。

詳しくは、[Azure Security Center と Windows Admin Center を統合する方法](windows-admin-center-integration.md)に関するページを参照してください。


### <a name="protection-for-azure-kubernetes-service"></a>Azure Kubernetes Service の保護

Azure Security Center では、Azure Kubernetes Service (AKS) を保護するコンテナー セキュリティ機能を拡張しています。

Kubernetes は人気のオープンソース プラットフォームとして広く普及しており、現在コンテナー オーケストレーションの業界標準となっています。 その実装が広まっているにもかかわらず、Kubernetes 環境をセキュリティで保護する方法については、まだ十分に理解されていません。 コンテナー化されたアプリケーションの攻撃対象領域を保護するには、潜在的な脅威を確実かつ継続的に監視するようインフラストラクチャを構成するための専門知識が必要です。

Security Center には以下の防御機能があります。

- **検出と可視化** - Security Center に登録されているサブスクリプション内のマネージド AKS インスタンスの継続的な検出。
- **セキュリティに関する推奨事項** - AKS のセキュリティ ベストプラクティスに準拠するのに役立つ、実行可能な推奨事項。 組織のセキュリティ体制の一環として確認できるように、これらの推奨事項はお客様のセキュア スコアに含まれています。 AKS に関する推奨事項の例としては、"Kubernetes Service クラスターへのアクセスを制限するには、ロールベースのアクセス制御を使用する必要があります" などがあります。
- **脅威の防止** - Security Center では、AKS デプロイの継続的な分析によって、ホストおよび AKS クラスター レベルで検出された脅威や悪意のあるアクティビティについてアラートを通知します。

詳しくは、「[Azure Kubernetes Service と Security Center の統合](defender-for-kubernetes-introduction.md)」を参照してください。

詳しくは、[Security Center のコンテナー セキュリティ機能](container-security.md)に関するページを参照してください。


### <a name="improved-just-in-time-experience"></a>Just-In-Time エクスペリエンスの改善

お客様の管理ポートを保護する Azure Security Center の Just-In-Time ツールの機能、操作、UI が、次のように強化されました。 

- **理由のフィールド** - Azure portal の Just-In-Time ページを使用して仮想マシン (VM) へのアクセスを要求する際に、要求の理由を入力するためにオプションの新しいフィールドを使用できます。 このフィールドに入力された情報は、アクティビティ ログで追跡できます。 
- **冗長な Just-In-Time (JIT) 規則の自動クリーンアップ** - JIT ポリシーを更新するたびに、クリーンアップ ツールが自動的に実行され、規則セット全体の有効性がチェックされます。 このツールでは、ポリシー内の規則と NSG 内の規則の不一致が検索されます。 クリーンアップ ツールで不一致が検出されると、原因が特定され、削除した方が安全と見なされる場合には不要な組み込み規則が削除されます。 ユーザーが作成した規則がこのツールによって削除されることはありません。 

詳しくは、[JIT アクセス機能](security-center-just-in-time.md)に関するページを参照してください。


### <a name="two-security-recommendations-for-web-applications-deprecated"></a>Web アプリケーションに関する 2 つのセキュリティ推奨事項の非推奨化

Web アプリケーションに関する 2 つのセキュリティ推奨事項が非推奨になります。 

- IaaS NSG 上の Web アプリケーションに対する規則を強化する必要があります。
    (関連ポリシー:IaaS 上の Web アプリケーションに対する NSG 規則を強化する必要がある)

- App Services へのアクセスを制限する必要があります。
    (関連ポリシー:App Services へのアクセスを制限する必要があります (プレビュー))

これらの推奨事項は、Security Center の推奨事項リストに表示されなくなります。 関連ポリシーは、"Security Center の既定" という名前のイニシアチブに含まれなくなります。

詳しくは、[セキュリティに関する推奨事項](recommendations-reference.md)についてのページを参照してください。




## <a name="february-2020"></a>2020 年 2 月

### <a name="fileless-attack-detection-for-linux-preview"></a>Linux 向けのファイルレス攻撃検出 (プレビュー)

検出を回避するためにステルス性の高い方法を利用する攻撃者が増えているので、Azure Security Center では、Windows 向けに加えて Linux 向けのファイルレス攻撃検出を拡張しています。 ファイルレス攻撃では、ソフトウェアの脆弱性が悪用されて、悪意のあるペイロードが正常なシステム プロセスに挿入され、メモリ内に隠されます。 これらの手法には以下のようなものがあります。

- ディスク上のマルウェアの痕跡を最小化または排除する
- ディスクベースのマルウェア スキャン ソリューションによる検出の可能性を大幅に下げる

この脅威に対処するために、Azure Security Center では 2018 年 10 月にファイルレス攻撃検出をリリースしました。そしてこのたび、ファイルレス攻撃検出は Linux にも拡張されました。 



## <a name="january-2020"></a>2020 年 1 月

### <a name="enhanced-secure-score-preview"></a>強化されたセキュア スコア (プレビュー)

Azure Security Center のセキュア スコア機能の強化バージョンが、プレビュー版で利用できるようになりました。 このバージョンでは、複数の推奨事項が、脆弱な攻撃対象領域をより正確に反映するセキュリティ コントロールにグループ化されています (例: 管理ポートへのアクセスの制限)。

プレビュー段階でセキュア スコアの変更に慣れていただけるほか、ご自分の環境のセキュリティを高めるのに役立つその他の修復機能をご確認いただけます。

詳細については、[強化されたセキュア スコア (プレビュー)](secure-score-security-controls.md) に関するページを参照してください。



## <a name="november-2019"></a>2019 年 11 月

11 月の更新プログラムには次のものが含まれます。
 - [北米リージョンで Azure Key Vault 用 Threat Protection (プレビュー)](#threat-protection-for-azure-key-vault-in-north-america-regions-preview)
 - [Azure Storage 用 Threat Protection にマルウェア評価スクリーニングを追加](#threat-protection-for-azure-storage-includes-malware-reputation-screening)
 - [Logic Apps を使用したワークフローの自動化 (プレビュー)](#workflow-automation-with-logic-apps-preview)
 - [大量のリソースのクイック修正の一般提供を開始](#quick-fix-for-bulk-resources-generally-available)
 - [コンテナー イメージの脆弱性スキャン (プレビュー)](#scan-container-images-for-vulnerabilities-preview)
 - [規制コンプライアンス標準の追加 (プレビュー)](#additional-regulatory-compliance-standards-preview)
 - [Azure Kubernetes Service 用 Threat Protection (プレビュー)](#threat-protection-for-azure-kubernetes-service-preview)
 - [仮想マシンの脆弱性評価 (プレビュー)](#virtual-machine-vulnerability-assessment-preview)
 - [Azure Virtual Machines の SQL Server 向け Advanced Data Security (プレビュー)](#advanced-data-security-for-sql-servers-on-azure-virtual-machines-preview)
 - [カスタム ポリシーのサポート (プレビュー)](#support-for-custom-policies-preview)
 - [コミュニティおよびパートナー向けのプラットフォームで Azure Security Center のカバレッジを拡大](#extending-azure-security-center-coverage-with-platform-for-community-and-partners)
 - [推奨事項とアラートのエクスポートによる高度な統合 (プレビュー)](#advanced-integrations-with-export-of-recommendations-and-alerts-preview)
 - [Windows Admin Center から Security Center へのオンプレミス サーバーのオンボード (プレビュー)](#onboard-on-prem-servers-to-security-center-from-windows-admin-center-preview)

### <a name="threat-protection-for-azure-key-vault-in-north-america-regions-preview"></a>北米リージョンで Azure Key Vault 用 Threat Protection (プレビュー)

Azure Key Vault は、キー、シークレット、暗号化キー、ポリシーをクラウドで一元管理する機能を提供することによって、データを保護し、クラウド アプリケーションのパフォーマンスを向上させる非常に重要なサービスです。 Azure Key Vault には、機密性の高い、ビジネスに不可欠なデータが格納されているため、キー コンテナーとそこに格納されるデータの最大限のセキュリティが必要となります。

Azure Security Center による Azure Key Vault 用 Threat Protection のサポートにより、キー コンテナーへのアクセスやキー コンテナーの悪用を試みる、害を及ぼす可能性のある通常とは異なる試行を検出する、セキュリティ インテリジェンスのレイヤーが追加されます。 この新しい保護レイヤーにより、お客様は、セキュリティ専門家でなくてもキー コンテナーに対する脅威に対処することが可能となり、セキュリティ監視システムを管理できるようになります。 この機能は、北米リージョンでパブリック プレビュー段階にあります。


### <a name="threat-protection-for-azure-storage-includes-malware-reputation-screening"></a>Azure Storage 用 Threat Protection にマルウェア評価スクリーニングを追加

Azure Storage 用 Threat Protection は、Microsoft 脅威インテリジェンスを利用した新しい検出機能を提供します。これらの機能では、ハッシュ評価分析を使用して Azure Storage へのマルウェアのアップロードを検出したり、アクティブな Tor 出口ノード (匿名化プロキシ) からの不審なアクセスを検出したりすることができます。 Azure Security Center を使用して、ストレージ アカウント全体で検出されたマルウェアを表示できるようになりました。


### <a name="workflow-automation-with-logic-apps-preview"></a>Logic Apps を使用したワークフローの自動化 (プレビュー)

セキュリティと IT/運用が一元管理されている組織では、環境で不一致が見つかった場合に組織内で必要なアクションを実行する内部ワークフロー プロセスを実装しています。 多くの場合、これらのワークフローは反復可能なプロセスであるため、自動化によって組織内のプロセスを大幅に効率化できます。

このたび、Azure Logic Apps を利用して自動化構成を作成し、推奨事項やアラートなど、ASC の特定の結果に基づいてそれらを自動的にトリガーするポリシーを作成できる新しい機能が Security Center に導入されます。 Azure Logic App は、Logic App コネクタの広範なコミュニティによってサポートされているカスタム アクションを実行するように構成することも、メールの送信や ServiceNow™ チケットのオープンなど、Security Center が提供するテンプレートのいずれかを使用するように構成することもできます。

ワークフローを実行するための Security Center の自動および手動機能の詳細については、「[ワークフローの自動化](workflow-automation.md)」をご覧ください。

Logic Apps の作成方法については、[Azure Logic Apps](../logic-apps/logic-apps-overview.md) に関する記事をご覧ください。


### <a name="quick-fix-for-bulk-resources-generally-available"></a>大量のリソースのクイック修正の一般提供を開始

セキュリティ スコアの一部として、ユーザーには多くのタスクが示されるため、大規模なフリートで問題を効果的に修復するのが困難になる可能性があります。

セキュリティの構成の誤りを簡単に修復し、大量のリソースに関する推奨事項をすばやく修復できるようにして、セキュリティ スコアを向上させるには、クイック修正を使用します。

この操作により、ユーザーは修復を適用するリソースを選択し、ユーザーに代わって設定を構成する修復アクションを起動できます。

このたび、Security Center の推奨事項ページの一部として、クイック修正の一般提供が開始されました。

クイック修正が有効になっている推奨事項については、[セキュリティの推奨事項のリファレンス ガイド](recommendations-reference.md)をご覧ください。


### <a name="scan-container-images-for-vulnerabilities-preview"></a>コンテナー イメージの脆弱性スキャン (プレビュー)

Azure Security Center で、Azure Container Registry にあるコンテナー イメージの脆弱性をスキャンできるようになりました。

イメージのスキャンでは、コンテナー イメージ ファイルを解析し、(Qualys を利用して) 既知の脆弱性が存在するかどうかが確認されます。

スキャン自体は、新しいコンテナー イメージを Azure Container Registry にプッシュすると自動的にトリガーされます。 検出された脆弱性は Security Center の推奨事項として表示され、修正プログラムを適用してそれらの脆弱性による攻撃対象領域を削減する方法に関する情報と共に Azure セキュリティ スコアに含められます。


### <a name="additional-regulatory-compliance-standards-preview"></a>規制コンプライアンス標準の追加 (プレビュー)

規制コンプライアンス ダッシュボードは、Security Center の評価に基づいて、コンプライアンス体制に関する分析情報を提供します。 ダッシュボードでは、お使いの環境が特定の規制基準や業界ベンチマークで指定されたコントロールと要件にどのように準拠しているかが示され、これらの要件に対処する方法に関する規範的な推奨事項が提供されます。

これまで、規制コンプライアンス ダッシュボードでは、Azure CIS 1.1.0、PCI-DSS、ISO 27001、SOC-TSP の 4 つの組み込み標準がサポートされていました。 このたび、Microsoft は、サポート対象となる追加の標準として、NIST SP 800-53 R4、SWIFT CSP CSCF v2020、Canada Federal PBMM、UK Official および UK NHS のパブリック プレビュー リリースを発表しました。 また、Azure CIS 1.1.0 の更新バージョンもリリースし、この標準のより多くのコントロールに対応して拡張性を強化します。

規制コンプライアンス ダッシュボードでの標準セットのカスタイマイズの詳細については、[こちら](update-regulatory-compliance-packages.md)をご覧ください。


### <a name="threat-protection-for-azure-kubernetes-service-preview"></a>Azure Kubernetes Service 用 Threat Protection (プレビュー)

Kubernetes は、ソフトウェアをクラウドにデプロイして管理するための新しい標準になりつつあります。 Kubernetes の豊富な経験を持つ人はほとんどおらず、多くの人が一般的なエンジニアリングと管理にのみ注目し、セキュリティ面を見落としています。 コンテナーに焦点を合わせた攻撃対象領域が無防備な状態で攻撃者にさらされないように、Kubernetes 環境のセキュリティを慎重に構成する必要があります。 Security Center では、コンテナー領域でのサポートを、Azure で最も急成長しているサービスの 1 つである Azure Kubernetes Service (AKS) まで拡大しています。

このパブリック プレビュー リリースの新しい機能は次のとおりです。

- **検出と可視化** - Security Center の登録済みサブスクリプション内のマネージド AKS インスタンスの継続的な検出。
- **セキュリティ スコアの推奨事項** - お客様が AKS のセキュリティに関するベストプラクティスに準拠し、セキュリティ スコアを上げるのに役立つ実用的な項目。 「Kubernetes Service クラスターへのアクセスを制限するには、ロールベースの Access Control を使用する必要がある」などの項目を含む推奨事項。
- **脅威の検出** - ホストおよびクラスターベースの分析 (例: "特権コンテナーが検出されました")。


### <a name="virtual-machine-vulnerability-assessment-preview"></a>仮想マシンの脆弱性評価 (プレビュー)

多くの場合、仮想マシンにインストールされているアプリケーションには、仮想マシンの侵害につながるおそれのある脆弱性が存在する可能性があります。 Microsoft は、Security Center Standard レベルに、追加料金なしで仮想マシンの脆弱性評価を組み込むことを発表しました。 Qualys を利用した脆弱性評価 (パブリック プレビュー) を使用すると、仮想マシンにインストールされているすべてのアプリケーションを継続的にスキャンして脆弱なアプリケーションを検出し、Security Center ポータルのエクスペリエンスに結果を表示できます。 Security Center がすべてのデプロイ操作を処理するため、ユーザーが追加の作業を行う必要はありません。 今後、お客様固有のビジネス ニーズに対応する脆弱性評価オプションを提供する予定です。

Azure Virtual Machines の脆弱性評価の詳細については、[こちら](deploy-vulnerability-assessment-vm.md)をご覧ください。


### <a name="advanced-data-security-for-sql-servers-on-azure-virtual-machines-preview"></a>Azure Virtual Machines の SQL Server 向け Advanced Data Security (プレビュー)

IaaS VM 上で実行されている SQL DB の脅威からの保護と脆弱性評価の、Azure Security Center によるサポートのプレビューが開始されました。

[脆弱性評価](../azure-sql/database/sql-vulnerability-assessment.md)では、データベースの潜在的な脆弱性を検出、追跡、修復できるサービスを簡単に構成できます。 Azure セキュリティ スコアの一部としてセキュリティ体制が可視化され、セキュリティの問題を解決してデータベースの防御機能を強化するための手順が含まれます。

[Advanced Threat Protection](../azure-sql/database/threat-detection-overview.md) では、SQL Server へのアクセスや SQL Server の悪用を試みる、害を及ぼす可能性のある通常とは異なる試行を示す異常なアクティビティが検出されます。 データベースでの不審なアクティビティを継続的に監視し、異常なデータベース アクセス パターンに対してアクション指向のセキュリティ通知を提供します。 このようなアラートからは、不審なアクティビティの詳細と、脅威の調査や危険性の軽減のために推奨されるアクションが提示されます。


### <a name="support-for-custom-policies-preview"></a>カスタム ポリシーのサポート (プレビュー)

Azure Security Center でカスタム ポリシーがサポートされるようになりました (プレビュー)。

お客様は、Azure Policy で作成したポリシーに基づく独自のセキュリティ評価によって、Security Center の現在のセキュリティ評価の対象範囲を拡張することを求めてきました。 カスタム ポリシーのサポートにより、これが可能になりました。

これらの新しいポリシーは、Security Center の推奨事項エクスペリエンス、セキュリティ スコア、規制コンプライアンス標準ダッシュボードに含まれるようになります。 カスタム ポリシーのサポートにより、Azure Policy でカスタム イニシアチブを作成し、Security Center にポリシーとして追加して、推奨事項として表示できるようになりました。


### <a name="extending-azure-security-center-coverage-with-platform-for-community-and-partners"></a>コミュニティおよびパートナー向けのプラットフォームで Azure Security Center のカバレッジを拡大

Security Center を使用して、Microsoft だけでなく、Check Point、Tenable、CyberArk などのパートナーの既存のソリューションからも推奨事項を受け取ることができます。今後、さらに多くの統合が提供される予定です。  Security Center のシンプルなオンボード フローにより、既存のソリューションを Security Center に接続することで、セキュリティ体制に関する推奨事項を 1 か所で表示し、統合レポートを実行することが可能になります。組み込みの推奨事項とパートナーの推奨事項の両方に対して、Security Center のすべての機能を活用することもできます。 また、Security Center の推奨事項をパートナー製品にエクスポートすることもできます。

Microsoft インテリジェント セキュリティ アソシエーションの詳細については、[こちら](https://www.microsoft.com/security/partnerships/intelligent-security-association)をご覧ください。



### <a name="advanced-integrations-with-export-of-recommendations-and-alerts-preview"></a>推奨事項とアラートのエクスポートによる高度な統合 (プレビュー)

Security Center 上でエンタープライズ レベルのシナリオに対応するために、Azure portal または API 以外の場所でも Security Center のアラートと推奨事項を使用できるようになりました。 これらは、イベント ハブおよび Log Analytics ワークスペースに直接エクスポートできます。 これらの新しい機能を使用して作成できるワークフローは次のとおりです。

- Log Analytics ワークスペースにエクスポートすることにより、PowerBI でカスタム ダッシュボードを作成できます。
- イベント ハブにエクスポートすることにより、Security Center のアラートと推奨事項をサード パーティの SIEM、リアルタイムのサード パーティ ソリューション、または Azure Data Explorer にエクスポートできるようになります。


### <a name="onboard-on-prem-servers-to-security-center-from-windows-admin-center-preview"></a>Windows Admin Center から Security Center へのオンプレミス サーバーのオンボード (プレビュー)

Windows Admin Center は、Azure にデプロイされていない Windows サーバー用の管理ポータルであり、バックアップやシステム更新などの Azure 管理機能を提供します。 最近、これらの非 Azure サーバーをオンボードして、Windows Admin Center のエクスペリエンスから直接 ASC で保護する機能が追加されました。

この新しいエクスペリエンスにより、ユーザーは WAC サーバーを Azure Security Center にオンボードし、Windows Admin Center のエクスペリエンスでセキュリティのアラートや推奨事項を直接表示できるようになります。


## <a name="september-2019"></a>2019 年 9 月

9 月の更新プログラムには次のものが含まれます。

 - [適応型アプリケーション制御のルール管理の改善](#managing-rules-with-adaptive-application-controls-improvements)
 - [Azure Policy を使用してコンテナーのセキュリティの推奨事項を制御](#control-container-security-recommendation-using-azure-policy)

### <a name="managing-rules-with-adaptive-application-controls-improvements"></a>適応型アプリケーション制御のルール管理の改善

適応型アプリケーション制御を使用した仮想マシンのルール管理のエクスペリエンスが改善されました。 Azure Security Center の適応型アプリケーション制御を使用すると、仮想マシン上で実行できるアプリケーションを制御できます。 ルール管理の全般的な改善に加え、新たに追加される機能により、新しいルールを追加するときに保護するファイルの種類を制御できるようになります。

適応型アプリケーション制御の詳細については、[こちら](security-center-adaptive-application.md)をご覧ください。


### <a name="control-container-security-recommendation-using-azure-policy"></a>Azure Policy を使用してコンテナーのセキュリティの推奨事項を制御

コンテナーのセキュリティの脆弱性を修復するための Azure Security Center の推奨事項を、Azure Policy を使用して有効または無効にすることができるようになりました。

有効なセキュリティ ポリシーを表示するには、Security Center から [セキュリティ ポリシー] ページを開きます。


## <a name="august-2019"></a>2019 年 8 月

8 月の更新プログラムには次のものが含まれます。

 - [Azure Firewall 用 Just-In-Time (JIT) VM アクセス](#just-in-time-jit-vm-access-for-azure-firewall)
 - [セキュリティ体制を強化するシングル クリック修復 (プレビュー)](#single-click-remediation-to-boost-your-security-posture-preview)
 - [テナント間の管理](#cross-tenant-management)

### <a name="just-in-time-jit-vm-access-for-azure-firewall"></a>Azure Firewall 用 Just-In-Time (JIT) VM アクセス 

Azure Firewall 用 Just-In-Time (JIT) VM アクセスの一般提供が開始されました。 これを使用することで、NSG で保護された環境に加え、Azure Firewall で保護された環境もセキュリティで保護できます。

JIT VM アクセスでは、NSG と Azure Firewall の規則を使用して、必要な場合にのみ、VM への制御されたアクセスを提供することで、ネットワーク帯域幅消費型攻撃にさらされるリスクを低減します。

VM で JIT を有効にする場合は、保護するポート、ポートを開放しておく時間、これらのポートにアクセスできる承認済み IP アドレスを指定したポリシーを作成します。 このポリシーは、ユーザーがアクセスを要求したときに実行できる操作を制御するのに役立ちます。

要求は Azure アクティビティ ログに記録されるので、アクセスの監視と監査を簡単に行うことができます。 Just-In-Time ページは、JIT が有効になっている既存の VM や JIT が推奨される VM をすばやく特定する際にも役立ちます。

Azure Firewall の詳細については、[こちら](../firewall/overview.md)をご覧ください。


### <a name="single-click-remediation-to-boost-your-security-posture-preview"></a>セキュリティ体制を強化するシングル クリック修復 (プレビュー)

セキュリティ スコアは、ワークロードのセキュリティに対する姿勢を評価するのに役立つツールです。 セキュリティの推奨事項が見直され、優先順位が自動的に示されるので、どの推奨事項を最初に実行すべきかがわかります。 これは、最も重大なセキュリティの脆弱性を見つけて、調査に優先順位を付けるのに役立ちます。

セキュリティの構成の誤りを簡単に修復し、セキュリティ スコアを速やかに向上させるために、大量のリソースに関する推奨事項をシングル クリックで修復できる新しい機能が追加されました。

この操作により、ユーザーは修復を適用するリソースを選択し、ユーザーに代わって設定を構成する修復アクションを起動できます。

クイック修正が有効になっている推奨事項については、[セキュリティの推奨事項のリファレンス ガイド](recommendations-reference.md)をご覧ください。


### <a name="cross-tenant-management"></a>テナント間の管理

Security Center では、Azure Lighthouse の一部として、テナント間の管理のシナリオがサポートされるようになりました。 これにより、Security Center で複数のテナントのセキュリティ体制を可視化し、管理できるようになります。 

テナント間の管理エクスペリエンスの詳細については、[こちら](security-center-cross-tenant-management.md)をご覧ください。


## <a name="july-2019"></a>2019 年 7 月

### <a name="updates-to-network-recommendations"></a>ネットワークの推奨事項の更新

Azure Security Center (ASC) で新しいネットワークの推奨事項が導入され、既存の推奨事項が改善されました。 Security Center を使用することで、リソースのネットワーク保護をさらに強化できます。 

ネットワークの推奨事項の詳細については、[こちら](recommendations-reference.md#recs-network)をご覧ください。


## <a name="june-2019"></a>2019 年 6 月

### <a name="adaptive-network-hardening---generally-available"></a>アダプティブ ネットワークのセキュリティ強化機能 - 一般提供の開始

パブリック クラウドで実行されているワークロードの最大の攻撃対象領域の 1 つは、パブリック インターネットとの間の接続です。 Azure ワークロードを必要なソース範囲でのみ利用できるようにするために、どのネットワーク セキュリティ グループ (NSG) 規則を設定すればよいかをお客様が把握するのは困難です。 この機能を使用すると、Security Center が Azure ワークロードのネットワーク トラフィックと接続のパターンを学習し、インターネットに接続する仮想マシンに対して、NSG 規則の推奨事項を提供します。 これにより、お客様は、ネットワーク アクセス ポリシーをより適切に構成し、攻撃にさらされるリスクを抑えることができます。 

アダプティブ ネットワークのセキュリティ強化機能の詳細については、[こちら](security-center-adaptive-network-hardening.md)をご覧ください。