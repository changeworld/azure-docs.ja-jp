---
title: Azure Security Center のリリース ノート
description: Azure Security Center の新機能と変更点の説明。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/21/2020
ms.author: memildin
ms.openlocfilehash: 6909bcbc67680f9205af8a79782907d4671d668b
ms.sourcegitcommit: 95269d1eae0f95d42d9de410f86e8e7b4fbbb049
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/26/2020
ms.locfileid: "83860717"
---
# <a name="whats-new-in-azure-security-center"></a>Azure Security Center の最新情報

Azure のセキュリティは精力的な開発の下、継続的に改善されています。 常に最新の開発情報を把握していただけるよう、このページでは以下に関する情報を提供します。

- 新機能
- バグの修正
- 非推奨の機能

このページは頻繁に更新されるため、定期的にアクセスしてご確認ください。 6 か月以上前の項目を探す場合は、「[Azure Security Center の最新情報のアーカイブ](release-notes-archive.md)」をご覧ください。


## <a name="may-2020"></a>2020 年 5 月


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

詳しくは、[仮想マシンに対する Security Center の統合脆弱性評価](built-in-vulnerability-assessment.md)に関するページを参照してください。



### <a name="changes-to-just-in-time-jit-virtual-machine-vm-access"></a>Just-In-Time (JIT) 仮想マシン (VM) へのアクセスの変更

Security Center には、VM の管理ポートを保護するオプションの機能が備わっています。 これにより、最も一般的な形式のブルート フォース攻撃を防ぐことができます。

この更新では、この機能に次の変更が加えられました。

- VM での JIT の有効化を勧める推奨事項の名前が変更されました。 以前の "仮想マシンに Just-In-Time ネットワーク アクセス制御を適用する必要があります" から次のように変わりました: "仮想マシンの管理ポートは、Just-In-Time ネットワーク アクセス制御によって保護されている必要があります"。

- オープンな管理ポートがある場合にのみトリガーされるよう推奨事項が設定されました。

詳しくは、[JIT アクセス機能](security-center-just-in-time.md)に関するページを参照してください。


### <a name="custom-recommendations-have-been-moved-to-a-separate-security-control"></a>別のセキュリティ コントロールへのカスタム推奨事項の移動

強化されたセキュア スコアと共に導入されたセキュリティ コントロールの 1 つに、"セキュリティのベスト プラクティスの実装" がありました。 お客様のサブスクリプションで作成されたカスタム推奨事項は自動的に、このコントロールに配置されていました。 

カスタム推奨事項は見つけやすいように、専用のセキュリティ コントロールである "カスタム推奨事項" に移行しました。 このコントロールは、お客様のセキュア スコアに影響しません。

セキュリティ コントロールの詳細については、「[Azure Security Center の強化されたセキュア スコア (プレビュー)](secure-score-security-controls.md)」を参照してください。


### <a name="toggle-added-to-view-recommendations-in-controls-or-as-a-flat-list"></a>推奨事項をコントロールまたは単純なリストで表示するためのトグルの追加

セキュリティ コントロールは、関連するセキュリティ推奨事項の論理グループです。 これらはお客様の脆弱な攻撃対象領域を反映します。 コントロールは、セキュリティに関する一連の推奨事項と、これらの推奨事項を実装するための指示内容です。

組織が個々の攻撃対象領域をどの程度セキュリティで保護しているかをすぐに確認するには、各セキュリティ コントロールのスコアを確認します。

既定では、推奨事項がセキュリティ コントロールに表示されます。 この更新から、それらをリストとしても表示できるようになりました。 影響を受けるリソースの正常性状態で並べ替えられた単純なリストとして表示するには、新しいトグル "コントロールによるグループ化" を使用します。 このトグルはポータル内のリストの上にあります。

セキュリティ コントロール (およびこのトグル) は、新しいセキュア スコア エクスペリエンスの一部です。 ポータル内からフィードバックをお送りいただけますとさいわいです。

セキュリティ コントロールの詳細については、「[Azure Security Center の強化されたセキュア スコア (プレビュー)](secure-score-security-controls.md)」を参照してください。


### <a name="expanded-security-control-implement-security-best-practices"></a>拡張されたセキュリティ コントロール "セキュリティのベスト プラクティスの実装" 

強化されたセキュア スコアと共に導入されたセキュリティ コントロールの 1 つは、"セキュリティのベスト プラクティスの実装" です。 このコントロールに推奨事項があっても、セキュア スコアに影響はありません。 

この更新により、3 つの推奨事項が当初配置されていたコントロールからこのベスト プラクティス コントロールに移動されました。 この操作が行われたのは、これら 3 つの推奨事項のリスクが当初考えていたよりも低いことが判明したためです。

さらに、2 つの新しい推奨事項が導入され、このコントロールに追加されました。

移動された 3 つの推奨事項は次のとおりです。

- **ご利用のサブスクリプションに対して読み取りアクセス許可があるアカウントでは、MFA を有効にする必要があります** (当初は "MFA の有効化" コントロール内)
- **読み取りアクセス許可がある外部アカウントは、ご使用のサブスクリプションから削除する必要があります** (当初は "アクセスとアクセス許可の管理" コントロール内)
- **お使いのサブスクリプションに、最大 3 人の所有者を指定する必要があります** (当初は "アクセスとアクセス許可の管理" コントロール内)

コントロールに追加された 2 つの新しい推奨事項は次のとおりです。

- **(プレビュー) ゲスト構成エージェントをインストールする必要があります** - [Azure Policy ゲスト構成](https://docs.microsoft.com/azure/governance/policy/concepts/guest-configuration)を使用することで、サーバーとアプリケーションの設定に対して仮想マシンが可視化されます (Windows のみ)。

- **(プレビュー) Windows Exploit Guard を有効にする必要があります** - Windows Defender Exploit Guard では、Azure Policy ゲスト構成エージェントが利用されます。 Exploit Guard には、さまざまな攻撃ベクトルに対してデバイスをロックダウンし、マルウェア攻撃でよく使用される動作をブロックするよう設計された 4 つのコンポーネントがありますが、企業がセキュリティ リスクと生産性の要件のバランスをとれるようになっています (Windows のみ)。

Windows Defender Exploit Guard の詳細については、「[Exploit Guard ポリシーの作成と展開](https://docs.microsoft.com/mem/configmgr/protect/deploy-use/create-deploy-exploit-guard-policy)」を参照してください。

セキュリティ コントロールの詳細については、「[Azure Security Center の強化されたセキュア スコア (プレビュー)](secure-score-security-controls.md)」を参照してください。


### <a name="custom-policies-with-custom-metadata-are-now-generally-available"></a>カスタム メタデータを使用したカスタム ポリシーの一般提供の開始

カスタム ポリシーは、Security Center の推奨事項エクスペリエンス、セキュア スコア、規制コンプライアンス標準ダッシュボードに含まれました。 この機能の一般提供が開始され、お客様は Security Center で組織のセキュリティ評価の範囲を拡大できるようになりました。 

Azure Policy でカスタム イニシアチブを作成し、それにポリシーを追加して Azure Security Center にオンボードし、推奨事項として視覚化します。

さらに、カスタム推奨事項のメタデータを編集するオプションも追加されました。 メタデータ オプションには、重要度、修復手順、脅威情報などが含まれます。  

詳しくは、「[詳細情報でのカスタム推奨事項の拡張](custom-security-policies.md#enhancing-your-custom-recommendations-with-detailed-information)」を参照してください。


### <a name="crash-dump-analysis-capabilities-migrating-to-fileless-attack-detection"></a>ファイルレス攻撃検出へのクラッシュ ダンプ分析機能の移行 

Microsoft は Windows クラッシュ ダンプ (CDA) 検出機能を[ファイルレス攻撃検出](https://docs.microsoft.com/azure/security-center/threat-protection#windows-fileless)に移行しています。 ファイルレス攻撃検出分析では、Windows マシンで次のセキュリティ アラートの高度なバージョンを利用できます。コード インジェクションの検出、Windows モジュールの偽装の検出、シェルコードの検出、疑わしいコード セグメントの検出。

この移行のメリットをいくつか紹介します。

- **プロアクティブかつタイムリーなマルウェア検出** - CDA のアプローチでは、クラッシュが発生して初めて分析を実行し、アクティブなマルウェアを検出する必要がありました。 ファイルレス攻撃検出を使用すると、メモリ内の脅威が活動しているのをプロアクティブに特定できます。 

- **強化されたアラート** - ファイルレス攻撃検出のセキュリティ アラートには、CDA では利用できない強化が加わっています (例: アクティブなネットワーク接続情報)。 

- **アラートの集計** - CDA では、単一のクラッシュ ダンプ内で複数の攻撃パターンを検出した場合に、複数のセキュリティ アラートをトリガーしていました。 ファイルレス攻撃検出では、同じプロセスから特定された攻撃パターンをすべて単一のアラートにまとめて、複数のアラートを関連付ける必要をなくしています。

- **Log Analytics ワークスペースでの要件の減少** - 機密データを含んでいる可能性があるクラッシュ ダンプは、Log Analytics ワークスペースにアップロードされなくなります。



## <a name="april-2020"></a>2020 年 4 月

### <a name="dynamic-compliance-packages-are-now-generally-available"></a>動的コンプライアンス パッケージの一般提供の開始

Azure Security Center の規制コンプライアンス ダッシュボードに、追加の業界および規制の標準を追跡するための**動的コンプライアンス パッケージ**が追加されました (一般提供開始)。

動的コンプライアンス パッケージは、Security Center のセキュリティ ポリシー ページからサブスクリプションまたは管理グループに追加できます。 標準またはベンチマークをオンボードすると、評価として、マップされた関連するすべてのコンプライアンス データと共に、標準が規制コンプライアンス ダッシュボードに表示されます。 オンボードされた標準の概要レポートがダウンロードできるようになります。

次のような標準を追加できるようになりました。

- **NIST SP 800-53 R4**
- **SWIFT CSP CSCF-v2020**
- **UK OFFICIAL および UK NHS**
- **カナダ連邦の PBMM**
- **Azure CIS 1.1.0 (新規)** (Azure CIS 1.1.0 のより完全な表現)

さらに、**Azure セキュリティ ベンチマーク**が最近追加されました。これは Microsoft が作成したもので、一般的なコンプライアンス フレームワークに基づくセキュリティとコンプライアンスのベスト プラクティスに関する Azure 固有のガイドラインです。 追加の標準が使用可能になると、ダッシュボードでサポートされます。  
 
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

### <a name="workflow-automation-is-now-generally-available"></a>ワークフロー自動化の一般提供の開始

Azure Security Center のワークフロー自動化機能の一般提供が開始されました。 これを使用すると、セキュリティ アラートと推奨事項が出されたときにロジック アプリを自動でトリガーできます。 さらに、クイック修復オプションが利用可能なすべての推奨事項とアラートでは、手動のトリガーが使用可能です。

すべてのセキュリティ プログラムには、インシデント対応のための複数のワークフローが含まれています。 これらのプロセスには、直接の利害関係者への通知、変更管理プロセスの開始、および特定の修復手順の適用が含まれます。 これらのプロシージャの手順をできるだけ多く自動化することがセキュリティの専門家によって推奨されています。 自動化を行うと、オーバーヘッドが減少します。また、迅速かつ一貫した方法で、定義済みの要件に従ってプロセスの手順が実行されるようにして、セキュリティを向上させることができます。

ワークフローを実行するための Security Center の自動および手動機能の詳細については、「[ワークフローの自動化](workflow-automation.md)」を参照してください。

詳しくは、[ロジック アプリの作成](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)に関するページを参照してください。


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

詳しくは、「[Azure Kubernetes Service と Security Center の統合](azure-kubernetes-service-integration.md)」を参照してください。

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

詳しくは、「[Azure Security Center の強化されたセキュア スコア (プレビュー)](secure-score-security-controls.md)」を参照してください。