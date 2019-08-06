---
title: 概念実証の提供 - Azure DevTest Labs |Microsoft Docs
description: Azure DevTest Labs がエンタープライズ環境に適切に組み込まれるようにするために、概念実証を提供する方法について説明します。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: tanmayeekamath
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2018
ms.author: takamath
ms.openlocfilehash: 6739679b1687393737dda1ded4265a95c4fce169
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/25/2019
ms.locfileid: "68501605"
---
# <a name="delivering-a-proof-of-concept"></a>概念実証の提供 

Azure DevTest Labs の重要なシナリオの 1 つは、クラウドで開発環境とテスト環境を実現することです。 たとえば、次のようになります。

* クラウドで開発者デスクトップを作成する。
* テスト用の環境を構成する。
* 仮想マシンやその他の Azure リソースにアクセスできるようにする。
* 開発者が学習と実験を行うためのサンドボックス領域をセットアップする。

また DevTest Labs では、ポリシー ガードレールとコスト制御を提供して、セキュリティ、規制、コンプライアンスに関する企業のポリシーを遵守している開発者に企業が "セルフサービス型の Azure" を提供できるようにしています。 

Azure DevTest Labs を企業の環境にうまく組み込むための要件は、企業によってさまざまです。 この記事では、エンド ツー エンドのデプロイを成功させるための第一歩である適切な概念実証の確保に向けて企業が通常遂行する必要がある一般的な手順について説明します。 

## <a name="getting-started"></a>Getting Started (概要) 

概念実証を提供するには、まず、 Azure と DevTest Labs について学習することが重要です。  出発点となるリソースには以下のものがあります。 

* [Azure portal の概要](https://azure.microsoft.com/features/azure-portal/)
* [DevTest Labs の基礎](devtest-lab-overview.md)
* [DevTest Labs でサポートされるシナリオ](devtest-lab-guidance-get-started.md)
* [エンタープライズ規模の DevTest Labs に関するドキュメント](devtest-lab-guidance-prescriptive-adoption.md)
* [Azure ネットワークの概要](../virtual-network/virtual-networks-overview.md)

## <a name="prerequisites"></a>前提条件 

DevTest Labs でパイロットや概念実証を適切に遂行するには、いくつかの前提条件が必要です。 

* **Azure サブスクリプション**:企業は多くの場合、Azure へのアクセスを可能にする[マイクロソフトエンタープライズ契約](https://azure.microsoft.com/pricing/purchase-options/enterprise-agreement/)を既に結んでおり、既存または新規のサブスクリプションを DevTest Labs に使用することができます。 あるいは、パイロットの期間中に [Visual Studio Subscription](https://azure.microsoft.com/pricing/member-offers/visual-studio-subscriptions/) を使用できます (無料の Azure クレジットを利用)。 どちらのオプションも利用できない場合は、[無料の Azure アカウント](https://azure.microsoft.com/free/search/?&OCID=AID719825_SEM_g4lyBqgB&lnkd=Bing_Azure_Brand&msclkid=ecc4275a31b61375749e7a5322c20de8&dclid=CMGW5-m78-ICFaLt4QodmUwGtQ)を作成して使用することができます。 マイクロソフトエンタープライズ契約を結んでいる場合は、[Enterprise Dev/Test サブスクリプション](https://azure.microsoft.com/offers/ms-azr-0148p/)を使用して、Windows 10/Windows 8.1 クライアント オペレーティング システムにアクセスし、開発とテストのワークロードを割引料金で利用できます。 
* **Azure Active Directory テナント**:ユーザーの管理 (ユーザーの追加やラボ所有者の追加など) を可能にするには、対象のユーザーがパイロット用の Azure サブスクリプションで使用される [Azure Active Directory テナント](https://azure.microsoft.com/services/active-directory/)に属している必要があります。 通常、企業はユーザーがクラウドでオンプレミスの ID を利用できるように[ハイブリッド ID](../active-directory/hybrid/whatis-hybrid-identity.md) を設定しますが、DevTest Labs パイロットではその必要はありません。 

## <a name="scoping-of-the-pilot"></a>パイロットの範囲の設定 

実装を開始する前に、適切なパイロットを計画することが非常に重要です。 リソースが有限であることを事前に認識し、パイロットのユーザーに適切な期待値を設定します。 

> [!IMPORTANT]
> パイロットの範囲を明確に設定し、事前に期待値を設定しておくことはきわめて重要です。

パイロットを開始する前に回答する必要がある重要な質問があります。 

* パイロットで学習したいことは何か、またパイロットの成果とは何か。 
* パイロットの対象となるワークロードまたはシナリオは何か。 パイロットを迅速に範囲設定して遂行できるように、定義する対象を少なくしておくことが重要です。 
* ラボで提供する必要があるリソースは何か。 たとえば、カスタム イメージ、マーケットプレース イメージ、ポリシー、ネットワーク トポロジなどがあります。 
* パイロットでエクスペリエンスを検証するのはどのエンド ユーザー/チームか。  
* パイロットの期間はどの程度か。 計画した範囲に十分に対応できる期間を選んでください (2 週間や 1 か月など)。 
* パイロットが完了したら、パイロットの期間中に使用された割り当て済みリソースはどうするか。 パイロット リソースは破棄する予定か。
   
   パイロット終了時に仮想マシンとラボを破棄する予定であれば、パイロット用のサブスクリプションを 1 つだけセットアップし、そこですべての作業を実行しながらスケール ロールアウトに関する疑問点を並行して解決できます。 

一般に、社内でサービスがロールアウトされたときに最終的な状態が完全に同じになるようにパイロットを "完璧" にしようとする傾向があります。 この想定は間違っています。 "完璧" に近づけるほど、パイロットの開始 "*前に*" 実行しなければならない作業が増えます。 パイロットの目的は、最終的なサービスのスケールアップとロールアウトに関して適切な意思決定を行うことです。 

パイロットでは、Azure DevTest Labs が自社に適したサービスであるかどうかを確認するために、必要最小限のワークロードと依存関係を選ぶことに重点を置く必要があります。 明確な成果を迅速に得るために、最も単純で最も依存関係が少ないワークロードを選ぶことをお勧めします。 それが不可能である場合は、次に最善な選択肢として、潜在的な複雑さを明らかにする代表的なワークロードを選んで、パイロット フェーズでの成果をスケールアウト フェーズでも再現できるようにします。 

適切に範囲設定された概念実証の例を次に示します。 

## <a name="example-proof-of-concept-plan"></a>例: 概念実証の計画 

このセクションでは、DevTest Labs のパイロットの概念実証を範囲設定するために使用するサンプルを示します。 

> [!TIP]
> プロジェクトのセットアップが失敗する可能性を最小限に抑えるために、このセクションで説明する例を確認しておくことを強くお勧めします。 

### <a name="overview"></a>概要 

ベンダーが企業ネットワークから分離された環境として使用できるように、Azure で DevTest Labs をベースにした新しい環境を開発することを計画しています。 ソリューションが要件を満たすかどうかを確認するために、エンド ツー エンドのソリューションを検証する概念実証を開発することになり、エクスペリエンスを試して検証するベンダーを複数追加しました。 

### <a name="outcomes"></a>結果 

概念実証を構築するときには、まず以下に挙げる結果 (達成しようとしていること) に焦点を当てます。  概念実証の終了時の状態を次のように想定しています。 

* 実践的なエンド ツー エンドのソリューションによって、ベンダーが Azure Active Directory (Azure AD) でゲスト アカウントを利用して、生産性向上に必要なリソースを備えた Azure 内の分離された環境にアクセスできる。 
* 幅広い利用や導入を阻害する可能性がある問題が列挙され、確認されている。
* 概念実証の開発に関わった個人が、すべてのコードを十分に理解している。 また、関連資料を理解し、広範に導入できることを確信している。

### <a name="open-questions--prerequisites"></a>未解決の質問と前提条件 

* このプロジェクトに使用できるサブスクリプションが作成されているか。 
* Azure AD テナントがあるか、また Azure AD 関連の疑問点についてヘルプとガイダンスを提供できる Azure AD グローバル管理者が特定されているか。 
* プロジェクトに従事する個人が共同作業する場所: 

   * ソース コードとスクリプト (Azure Repos など) 
   * ドキュメント (Teams や Sharepoint など)  
   * 対話 (Microsoft Teams など) 
   * 作業項目 (Azure Boards など) 
* ベンダーに必要なリソースは何か。 これには、仮想マシンおよびその他の必要なサーバーの両方でローカルに、ネットワークで利用可能なアプリケーションが含まれます。 
* 仮想マシンを Azure のドメインに参加させるか。 その場合は、Azure AD Domain Services にするか、それ以外にするか。 
* 概念実証の対象となるチーム/ベンダーが特定されているか。 その環境の利用者になるのはだれか。
* どの Azure リージョンを概念実証に使用するか。 
* ベンダーが IaaS (VM) 以外に DevTest Labs 経由で使用できる一連のサービスがあるか。 
* ベンダー/ユーザーにラボの使い方をどのようにトレーニングする予定か。 

### <a name="proof-of-concept-solution-components"></a>概念実証ソリューションのコンポーネント 

このソリューションのコンポーネントには次のものを想定しています。 

* さまざまなベンダー チームを対象とした Azure の一連の DevTest Labs。
* これらのラボは、要件をサポートするネットワーク インフラストラクチャに接続されます。
* ベンダーは Azure AD 経由でラボにアクセスでき、ラボのロールの割り当てが可能です。
* ベンダーが各自のリソースに正常に接続する手段。 具体的には、パブリック IP アドレスを使わずに仮想マシンへの直接アクセスを可能にするサイト間 VPN です。
* 仮想マシン上でベンダーが必要とするソフトウェアに対応する一連の成果物。

## <a name="additional-planning-and-design-decisions"></a>計画と設計に関するその他の意思決定 

DevTest Labs ソリューションを完全にリリースする前に、計画と設計に関して重要な意思決定を行う必要があります。 この意思決定には、概念実証での経験が役立ちます。 追加で検討が必要な事項は次のとおりです。 

* **サブスクリプション トポロジ**:Azure のリソースに関するエンタープライズ レベルの要件が、[1 つのサブスクリプションで利用可能なクォータ](https://docs.microsoft.com/azure/azure-subscription-service-limits)を超えることがあります。この場合、当初のサブスクリプションの制限を引き上げるために、複数の Azure サブスクリプションやサービス要求が必要となります。 サブスクリプション間でリソースを分配する方法を事前に決めておくことが重要です。このときに役立つのが[サブスクリプション決定ガイド](https://docs.microsoft.com/azure/architecture/cloud-adoption/decision-guides/subscriptions/)です。リソースを後から別のサブスクリプションに移動するのは難しいためです。 たとえば、DevTest Lab を作成後に別のサブスクリプションに移動することはできません。  
* **ネットワーク トポロジ**:DevTest Labs で自動的に作成される[既定のネットワーク インフラストラクチャ](../app-service/networking-features.md)は、エンタープライズ ユーザーの要件と制約を十分に満たさない可能性があります。 [ExpressRoute が VNet に接続されていたり](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/)、サブスクリプション間の接続に[ハブ アンド スポーク](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)が使用されていたり、オンプレミスの接続を確保するためだけに[強制的なルーティング](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md)が行われていたりすることがよくあります。 DevTest Labs では、既存の仮想ネットワークをラボに接続して、ラボで新しい仮想マシンを作成するときに使用することができます。 
* **仮想マシンのリモート アクセス**:DevTest Labs 内の仮想マシンにリモートでアクセスする方法は多数あります。 最も簡単なのは、パブリック IP または共有パブリック IP を使用する方法です。これらは[ラボで設定できます](devtest-lab-shared-ip.md)。 これらの方法では十分でない場合は、[DevTest Labs の企業向け参照アーキテクチャ](devtest-lab-reference-architecture.md)に示されているように、リモート アクセス ゲートウェイを使用することもできます。詳細については、[DevTest labs のリモート デスクトップ ゲートウェイに関するドキュメント](configure-lab-remote-desktop-gateway.md)を参照してください。 企業で ExpressRoute またはサイト間 VPN を使用して、DevTest Labs をオンプレミス ネットワークに接続することもできます。 これにより、インターネットに公開されていないプライベート IP アドレスに基づいて、仮想マシンにリモート デスクトップまたは SSH で直接接続することができます。 
* **アクセス許可の処理**:DevTest Labs で一般に使用される 2 つの主要なアクセス許可が、["所有者" と "ラボユーザー"](devtest-lab-add-devtest-user.md) です。 DevTest Labs を広くロールアウトする前に、ラボの各アクセス レベルの管理を誰に託すかを決めることが重要です。 一般的なモデルでは、予算所有者 (チーム リーダーなど) がラボの所有者となり、チーム メンバーがラボのユーザーとなります。 これにより、予算の責任者である人物 (チーム リーダー) は、チームが予算を超過しないようにポリシー設定を調整することができます。  

## <a name="completing-the-proof-of-concept"></a>概念実証の完了 

期待される学習内容への対応が完了したら、まとめの作業に入ってパイロットを完了します。 この時点で、ユーザーからフィードバックを集め、パイロットが成功したかどうかを判断し、企業内で DevTest Labs のスケール ロールアウトを進めてよいかどうかを決定します。 同時に、スケール ロールアウト全体の一貫性を確保するために、DevTest Labs と関連リソースのデプロイの自動化を検討することもお勧めします。 

## <a name="next-steps"></a>次の手順 

* [エンタープライズ規模の DevTest Labs に関するドキュメント](devtest-lab-guidance-prescriptive-adoption.md)
* [エンタープライズ向け参照アーキテクチャ](devtest-lab-reference-architecture.md)
* [DevTest Labs デプロイのスケールアップ](devtest-lab-guidance-orchestrate-implementation.md)
* [Azure DevTest Labs の実装を調整する](devtest-lab-guidance-orchestrate-implementation.md)
