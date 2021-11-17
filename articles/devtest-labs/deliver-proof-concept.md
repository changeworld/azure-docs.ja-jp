---
title: 概念実証を提供する
description: Azure DevTest Labs がエンタープライズ環境に適切に組み込まれるようにするために、概念実証を提供する方法について説明します。
ms.topic: how-to
ms.date: 06/2/2020
ms.openlocfilehash: fcd3ba364887fe26b13c74e16f3f50990d708549
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132325184"
---
# <a name="deliver-a-proof-of-concept"></a>概念実証を提供する 

Azure DevTest Labs の重要なシナリオの 1 つは、クラウドで開発環境とテスト環境を実現することです。 たとえば、次のようになります。

* クラウドで開発者デスクトップを作成する。
* テスト用の環境を構成する。
* 仮想マシンやその他の Azure リソースにアクセスできるようにする。
* 開発者が学習と実験を行うためのサンドボックス領域をセットアップする。

DevTest Labs のポリシー ガードレールとコスト制御を使用することで、企業では、セキュリティ、規制、およびコンプライアンスに関する企業のポリシーを忠実に守る "セルフサービス Azure" を開発者に提供できます。 

Azure DevTest Labs を企業の環境にうまく組み込むための要件は、企業によってさまざまです。 この記事では、企業が概念実証を成功させるために完了する必要がある最も一般的な手順について説明します。 概念実証は、エンドツーエンドのデプロイを成功させるための最初のステップです。 

## <a name="getting-started"></a>作業の開始 

概念実証を提供するには、まず、 Azure と DevTest Labs について学習することが重要です。  出発点となるリソースには以下のものがあります。 

* [Azure portal の概要](https://azure.microsoft.com/features/azure-portal/)
* [DevTest Labs の基礎](devtest-lab-overview.md)
* [DevTest Labs でサポートされるシナリオ](devtest-lab-guidance-get-started.md)
* [企業内での DevTest Labs に関するドキュメント](devtest-lab-guidance-prescriptive-adoption.md)
* [Azure ネットワークの概要](../virtual-network/virtual-networks-overview.md)

## <a name="prerequisites"></a>前提条件 

DevTest Labs でパイロットや概念実証を適切に遂行するには、いくつかの前提条件があります。 

* **Azure サブスクリプション**: 企業は多くの場合、Azure へのアクセスが可能になる [マイクロソフト エンタープライズ契約](https://azure.microsoft.com/pricing/purchase-options/enterprise-agreement/)を既に結んでおり、既存または新規のサブスクリプションを DevTest Labs に使用することができます。 または、企業はパイロットの期間中に [Visual Studio サブスクリプション](https://azure.microsoft.com/pricing/member-offers/visual-studio-subscriptions/)を使用することもできます (無料の Azure クレジットを利用)。 これらのオプションのいずれも使用できない場合、企業は[無料の Azure アカウント](https://azure.microsoft.com/free/search/?&OCID=AID719825_SEM_g4lyBqgB&lnkd=Bing_Azure_Brand&msclkid=ecc4275a31b61375749e7a5322c20de8&dclid=CMGW5-m78-ICFaLt4QodmUwGtQ)を作成して使用できます。 マイクロソフトエンタープライズ契約を結んでいる場合は、[Enterprise Dev/Test サブスクリプション](https://azure.microsoft.com/offers/ms-azr-0148p/)が最適なオプションです。 Windows 10 と Windows 8.1 クライアント オペレーティング システムにアクセスし、開発とテスト ワークロードに対して割引料金を利用できます。 
* **Azure Active Directory テナント**: ユーザーの追加やラボ所有者の追加などの管理を行う場合、ユーザーは、パイロットで使用される Azure サブスクリプションの [Azure Active Directory テナント](https://azure.microsoft.com/services/active-directory/)に属している必要があります。 通常、企業は、ユーザーがクラウドでオンプレミスの ID を使用できるように[ハイブリッド ID](../active-directory/hybrid/whatis-hybrid-identity.md) を設定します。 DevTest Labs のパイロットでは、ハイブリッド ID は必要ありません。 

## <a name="scoping-of-the-pilot"></a>パイロットの範囲の設定 

実装を開始する前にパイロットを計画することが重要です。 リソースが有限であることを事前に認識し、パイロットのユーザーに適切な期待値を設定します。 

> [!IMPORTANT]
> パイロットの範囲を明確に設定し、事前に期待値を設定しておくことはきわめて重要です。

パイロットを開始する前に、次の重要な質問に答えてください。 

* パイロットで学習したいことは何か、またパイロットの成果とは何か。 
* パイロットの対象となるワークロードまたはシナリオは何か。 パイロットを迅速に範囲設定して遂行できるように、定義する対象を少なくしておくことが重要です。 
* ラボで提供する必要があるリソースは何か。 たとえば、カスタム イメージ、マーケットプレース イメージ、ポリシー、ネットワーク トポロジなどがあります。 
* パイロットでエクスペリエンスを検証するのはどのユーザーおよびチームか。  
* パイロットの期間はどの程度か。 計画した範囲に十分に対応できる期間を選んでください (2 週間や 1 か月など)。 
* パイロットが完了した後、パイロットの期間中に使用された割り当て済みリソースはどうなるか。 パイロット リソースは破棄する予定か。 次のようなことが考えられます。
   
   "パイロットの終了時に仮想マシンとラボを破棄する予定ならば、パイロット用の 1 つのサブスクリプションを設定し、そこですべての作業を行うことができる。 スケール ロールアウトに関する問題は並行して解決できる。" 

パイロットを "完璧" にして、会社でサービスをロールアウトした後の最終的な状態と同等になるようにしようとする傾向があります。 この想定は誤りです。 "完璧" に近づけるほど、パイロットの開始 "*前に*" 実行しなければならない作業が増えます。 パイロットの目的は、スケールアップと最終的なサービスのロールアウトに関して適切な意思決定を行えることです。 

パイロットでは、Azure DevTest Labs が自社に適しているかどうかを決めるために、必要最小限のワークロードと依存関係を選択することに重点を置くべきです。 迅速でクリーンな成功を確実にするために、依存関係が最も少ない最も単純なワークロードを選ぶことをお勧めします。 それが不可能な場合は、潜在的な複雑さをあらわにする最も代表的なワークロードを選択して、スケールアウト フェーズでパイロットの成功を再現できるようにします。 

適切に範囲設定された概念実証の例を次に示します。 

## <a name="example-proof-of-concept-plan"></a>例: 概念実証の計画 

このセクションでは、DevTest Labs のパイロットの概念実証を範囲設定するために使用するサンプルを示します。 

> [!TIP]
> プロジェクトのセットアップが失敗する可能性を最小限に抑えるために、このセクションで説明する例を確認しておくことを強くお勧めします。 

### <a name="overview"></a>概要 

弊社では、DevTest Labs に基づいた新しい Azure 環境の開発を計画しています。 この環境は、企業ネットワークから分離されます。 ソリューションが要件を満たすかどうかを確認するために、エンドツーエンドのソリューションを検証する概念実証を開発します。 エクスペリエンスを試して検証するために、複数のベンダーを含めました。 

### <a name="outcomes"></a>結果 

概念実証を構築するときには、まず結果 (達成しようとしていること) に焦点を当てます。 概念実証の終了時の状態を次のように想定しています。 

* 実践的なエンドツーエンドのソリューションによって、ベンダーが Azure Active Directory (Azure AD) でゲスト アカウントを利用して、Azure 内の分離された環境にアクセスできる。 環境には、生産性向上に必要なリソースがあります。 
* 幅広い利用や導入を阻害する可能性がある問題が列挙され、確認されている。
* 概念実証の開発に関わった個人が、すべてのコードを十分に理解している。 また、関連資料を理解し、広範に導入できることを確信している。

### <a name="open-questions-and-prerequisites"></a>未解決の質問と前提条件 

* このプロジェクトに使用できるサブスクリプションが作成されているか。 
* Azure AD テナントがあるか、また Azure AD 関連の疑問点についてヘルプとガイダンスを提供できる Azure AD グローバル管理者が特定されているか。 
* プロジェクトに従事する個人が共同作業する場所があるか。 

   * ソース コードとスクリプト (Azure Repos など) 
   * ドキュメント (Microsoft Teams や Sharepoint など)  
   * 対話 (Microsoft Teams など) 
   * 作業項目 (Azure Boards など) 
* ベンダーに必要なリソースは何か。 リソースには、ネットワーク上で使用可能なアプリケーションが含まれます (仮想マシン上と他の必要なサーバー上の両方でローカル)。 
* 仮想マシンを Azure のドメインに参加させるか。 その場合は、Azure Active Directory Domain Services (Azure AD DS) にするか、それ以外にするか。 
* 概念実証の対象となるチームやベンダーが特定されているか。 その環境の利用者になるのはだれか。
* どの Azure リージョンを概念実証に使用するか。 
* ベンダーが IaaS (VM) 以外に DevTest Labs 経由で使用できる一連のサービスがあるか。 
* ベンダー/ユーザーにラボの使い方をどのようにトレーニングする予定か。 

### <a name="components-of-the-proof-of-concept-solution"></a>概念実証ソリューションのコンポーネント 

このソリューションのコンポーネントには次のものを想定しています。 

* さまざまなベンダー チームが Azure で一連のラボを使用します。
* これらのラボは、要件をサポートするネットワーク インフラストラクチャに接続されます。
* ベンダーは Azure AD とロールの割り当てによってラボにアクセスできます。
* ベンダーには、リソースに正常に接続する手段があります。 具体的には、パブリック IP アドレスを使わずに仮想マシンへの直接アクセスを可能にするサイト間 VPN です。
* 一連の成果物によって、仮想マシン上でベンダーが必要とするソフトウェアが対応されます。

## <a name="other-planning-and-design-decisions"></a>計画と設計に関するその他の意思決定 

完全な DevTest Labs ソリューションをリリースする前に、計画と設計に関するいくつかの重要な決定を行う必要があります。 この意思決定には、概念実証での経験が役立ちます。 追加で検討が必要な事項は次のとおりです。 

* **サブスクリプション トポロジ**: Azure のリソースに関するエンタープライズ レベルの要件が、[1 つのサブスクリプションで利用可能なクォータ](../azure-resource-manager/management/azure-subscription-service-limits.md)を超えることがあります。 複数の Azure サブスクリプション、または初期サブスクリプション制限を増やすサービス要求が必要な場合があります。 リソースを後で別のサブスクリプションに移動することは困難なので、サブスクリプション間でのリソースの分散方法を前もって決めておくことが重要です。 たとえば、ラボを作成後に別のサブスクリプションに移動することはできません。 有益なリソースの 1 つは、[サブスクリプション決定ガイド](/azure/architecture/cloud-adoption/decision-guides/subscriptions/)です。   
* **ネットワーク トポロジ**: DevTest Labs によって自動的に作成される [既定のネットワーク インフラストラクチャ](../app-service/networking-features.md)では、エンタープライズ ユーザーの要件と制約が十分に満たされない可能性があります。 [Azure ExpressRoute が仮想ネットワークに接続](/azure/architecture/reference-architectures/hybrid-networking/)されていたり、サブスクリプション間の接続に[ハブ アンド スポーク](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)が使用されていたり、オンプレミスの接続を確保するためだけに[強制的なルーティング](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md)が行われていたりすることがよくあります。 DevTest Labs では、既存の仮想ネットワークをラボに接続して、ラボで新しい仮想マシンを作成するときに使用することができます。 
* **仮想マシンのリモート アクセス**: DevTest Labs 内の仮想マシンにリモートでアクセスする方法は多数あります。 最も簡単な方法は、パブリック IP または共有パブリック IP を使用することです。 [これらの設定](devtest-lab-shared-ip.md)はラボで使用可能です。 これらのオプションでは十分でない場合は、リモート アクセス ゲートウェイを使用することもできます。 このオプションは、[DevTest Labs の企業向け参照アーキテクチャ](devtest-lab-reference-architecture.md)に示されています。 詳細については、[リモート デスクトップ ゲートウェイを使用するようにラボを構成](configure-lab-remote-desktop-gateway.md)に関するページを参照してください。 企業で ExpressRoute またはサイト間 VPN を使用して、ラボをオンプレミス ネットワークに接続することもできます。 このオプションを使用すると、プライベート IP アドレスに基づいて、仮想マシンにリモート デスクトップまたは SSH で直接接続できます。 インターネットへの露出はありません。 
* **アクセス許可の取り扱い**: DevTest Labs で一般に使用される 2 つの主要なアクセス許可は、[所有者とラボ ユーザー](devtest-lab-add-devtest-user.md)です。 DevTest Labs を広くロールアウトする前に、ラボの各アクセス レベルの管理を誰に託すかを決めることが重要です。 一般的なモデルでは、予算所有者 (チーム リーダーなど) がラボ所有者になり、チーム メンバーがラボ ユーザーとなります。 このモデルにより、予算の責任者である人物 (チーム リーダー) は、ポリシー設定を調整し、チームが予算を超過しないようにすることができます。  

## <a name="completing-the-proof-of-concept"></a>概念実証の完了 

期待される学習内容への対応が済んだら、パイロットを完了します。 ユーザーからフィードバックを集め、パイロットが成功したかどうかを判断し、企業で DevTest Labs のエンタープライズ スケール ロールアウトを進めるかどうかを決定します。 同時に、スケール ロールアウト全体の一貫性を確保するために、DevTest Labs と関連リソースのデプロイの自動化を検討することもお勧めします。 

## <a name="next-steps"></a>次のステップ 

* [企業内での DevTest Labs に関するドキュメント](devtest-lab-guidance-prescriptive-adoption.md)
* [エンタープライズ向け参照アーキテクチャ](devtest-lab-reference-architecture.md)
* [DevTest Labs デプロイのスケールアップ](devtest-lab-guidance-orchestrate-implementation.md)
* [Azure DevTest Labs の実装を調整する](devtest-lab-guidance-orchestrate-implementation.md)
