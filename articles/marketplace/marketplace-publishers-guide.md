---
title: Azure Marketplace と AppSource のパブリッシャー ガイド
description: Azure Marketplace の新しいパブリッシャーのためのステップ バイ ステップ ガイドと公開チェックリスト
services: Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: ellacroi
manager: msmbaldwin
editor: ''
ms.assetid: e8d228c8-f9e8-4a80-9319-7b94d41c43a6
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 01/18/2018
ms.author: ellacroi
ms.openlocfilehash: 0abf25726936a3d5e69c809c9714965936c7c64c
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2018
---
# <a name="azure-marketplace-and-appsource-publisher-guide"></a>Azure Marketplace と AppSource のパブリッシャー ガイド

これは [Azure Marketplace](https://azuremarketplace.microsoft.com) と [AppSource](https://appsource.microsoft.com) のパブリッシャー ガイドです。 このガイドは、新しいパブリッシャーおよび既存のパブリッシャーが、Azure Marketplace と AppSource のネットショップを利用する方法、アプリケーションとサービスを公開するための手順、Microsoft とのパートナーシップでビジネスを成長させる方法を理解できるように作られています。 このガイドを最後まで読むと、次の各トピックについての理解が深まり、さらに詳細な情報がどこで見つかるかわかります。

- Marketplace ネットショップに掲載する利点
- Azure Marketplace および AppSource の概要
- これらのネットショップを利用する方法
- パートナーのオファリングやサービスにはどちらのネットショップが適しているか 
- 公開できるアプリケーションおよびサービス オファリングの種類
- 各公開オプションの技術要件とビジネス要件
- 公開前資産チェックリスト
- パブリッシャーになる方法
- オファリングを作成して公開する場所
- リストを最適化し、Go-to-Market リソースを使って効果を高める方法
- Azure Marketplace、AppSource、またはこの公開ガイドに関する疑問点についてのヘルプとサポートを得るには、**cloudmarketplace@microsoft.com** で Marketplace チームにお問い合わせください。 

## <a name="benefits-of-participating-in-the-marketplace"></a>Marketplace に参加することの利点

Azure Marketplace と AppSource は、Microsoft と協力して市場進出活動を行うための出発点であり、ビジネスの成長に弾みを付けるものです。 発売プロモーション、需要創出、共同営業およびマーケティングなどにより、Marketplace カタログをクラウド ビジネス エンジンの中心的な場所にできます。 Marketplace には無料で参加できます。 目標は、Microsoft のお客様と、パートナーのエコシステムが提供する最高のソリューションとサービスを結び付けることです。

Marketplace の機能を活用して、ビジネスを成長させることができます。

- **リードと販売機会を生み出す。** Microsoft クラウド プラットフォームの広範なソリューション ポートフォリオを利用して、新しい市場に参入します。 Marketplace のオファリングをアップセルおよびクロスセルします。 
- **ビジネス価値を高め、既存および新規の顧客の取引規模を拡大する。** 取引規模を拡大し、ワークロードをクラウドに移動するときの顧客の問題点に対処します。 特定のワークロードおよび業界のシナリオを対象とした完成したソリューションを販売することで、販売サイクルを短縮し、取引の収益性を挙げます。
- **アクションにつながる洞察を得る。** パートナーの成功は、Microsoft の成功です。 Cloud パートナー ポータルでは、掲載のパフォーマンスについて洞察を得ることができます。 行われていること、開拓したリード、キャンペーン活動を最大化する方法について情報を得ることができます。

>[!NOTE]
>Office を拡張するアプリでは、Office アプリ公開プロセスで洞察にアクセスします。

## <a name="what-are-azure-marketplace-and-appsource"></a>Azure Marketplace および AppSource の概要

Microsoft が提供する 2 つの異なる Marketplace ネットショップ [Azure Marketplace](https://azuremarketplace.microsoft.com) と [AppSource](https://appsource.microsoft.com) では、パートナーは、オファリングを掲載し、試用版を利用できるようにし、Microsoft の顧客やエコシステムと直接取り引きすることができます。 これらのネットショップにより、顧客はデジタルへの転換を加速するアプリケーションやサービスを探し、試して、購入することができ、パブリッシャーは Microsoft の顧客とパートナー エコシステムへのアクセスを増やすことでビジネスを拡大できます。
 
Marketplace のネットショップは、対象ユーザーや Microsoft のクラウド製品に合わせて作られており、顧客が必要なものを見つけるのに役立ちます。 各ネットショップでは、次の表にまとめたような公開への投資を最大化するのに役立つ特別な公開オプションが提供されています。


|          |Azure Marketplace |AppSource  |
|---------|---------|---------|
|対象読者     |IT 専門化、開発者 (専門化の役割は、DBA、SecOp、DevOp などです)    | 基幹業務の意思決定者 (専門化の役割は、調達、製造、会計などです)      |
|構築環境または拡張対象     |Azure         | Azure、Dynamics 365、Office 365、Power BI、PowerApps       |
|ソリューションとサービスの種類     |  インフラストラクチャ ソリューションとプロフェッショナル サービス   | 完成した基幹業務クラウド アプリケーション、Office 365 アドイン、プロフェッショナル サービス        |
|公開オプション     |  連絡する、コンサルティング サービス オファリング、試用版、仮想マシンで、ソリューション テンプレート、管理対象アプリ       |  無料試用版、今すぐ取得、体験版、連絡する、コンサルティング サービス オファリング      |
|アプリ内エクスぺリエンス (アプリケーションのコンテキストに応じてアプリとサービスへのアクセスをユーザーに提供)  | Azure Portal、CLI         | Office 365、Dynamics 365、Power BI、Office クライアント アプリ       |

## <a name="leveraging-these-storefronts"></a>ネットショップの利用

各ネットショップは、顧客の固有ニーズに対応し、役割によって絞り込むことで、顧客に基づいて適切なソリューションやサービスを提供できます。

**Azure Marketplace** を通して IT 専門化やクラウド開発者が IaaS、SaaS、PaaS ソリューションを発見、試用、購入できるようにします。


|顧客ニーズ  |Azure Marketplace |
|---------|---------|
|**ビジネス上のニーズと技術的なニーズを満たすため、追加のクラウド プラットフォーム機能が必要である**     |  Azure での実行に最適化された補完的アプリケーションとサービスのポートフォリオを提供し、その数を増やし続ける       |
|**適切なアプリケーションまたはサービスを見つけ出すことが困難と判明した**    |  Azure 用のソリューションとサービスを発見、試用、購入するためのワンストップ ショップを提供する        |
|**サードパーティ製のアプリケーションとサービスをデプロイするためのスケーラブルなしくみが必要である**   | サードパーティ製のアプリケーションとサービス向けにスケーラブルなデプロイの作成と構成ができるようにする        |
|**新しいアプリケーションとサービスを既存のソリューションと統合して使用することが必要である**  |   Azure でサードパーティ製のアプリケーションとサービスを既存のソリューションと簡単に統合できる      |

**AppSource** を通してビジネス ユーザーが、業績を推進し、価値を得るまでの時間を短縮する基幹業務 SaaS アプリケーションと実装サービスを発見、試用、入手できるようにします。 


|顧客ニーズ  |AppSource  |
|---------|---------|
|**既に使用している Microsoft 製品と連携するビジネス ソリューションを探す** | 顧客がサードパーティ製アプリケーションとサービスを使って、Microsoft のクラウド アプリケーションとテクノロジを拡張できるようにする       |
|**適切なソリューションまたは実装サービスを簡単に見つけることができる**    |   アプリケーション、サービス、アドインなどを発見し、試用して入手するためのワンストップ ショップを提供する      |
|**具体的なビジネスの課題を解決する業界固有の基幹業務ソリューション**   | 多くの業界にまたがる具体的な要件の解決に役立つ、完成したエンドツーエンドの業界向けソリューションを提供する     |
|**生産性、効率性、ビジネスの洞察を高めるアプリ**    | カスタマー サービス、HR、運用など、基幹業務向けのアプリを提供する        |
| **固有の状況へのアプリの導入を支援する、経験を積んだ実装パートナー** | ビジネス ユーザーが予測可能な結果をもたらすコンサルティング サービス設計を見つけられるように、Dynamics 365、Power BI、PowerApps、サード パーティ アプリに基づいたソリューション向けのコンサルティング サービス オファリングのカタログを AppSource で提供する |

## <a name="understanding-the-differences-between-storefronts"></a>ネットショップ間の相違点の概要

ネットショップを選ぶときは最初に、オファリングの対象ユーザーを明らかにします。Azure Marketplace は IT 専門化と開発者のニーズに合わせて作られており、AppSource はビジネス ユーザーのニーズに合わせて作られています。 両方の対象ユーザーをターゲットとするソリューションの場合は、1 回公開するだけで、両方のネットショップに陳列できます。
 
各ネットショップの他の利点を考慮してください。

|ネットショップの利点  |Azure Marketplace  |AppSource   |
|---------|---------|---------|
|**課金の柔軟性**    | 仮想マシンの場合、"従量制課金" オプションは、Microsoft Enterprise Agreement または Web 直販モデルを使います。 価格オプションには、オファリングが永続的に無料の Free レベル サブスクリプションと、販売促進のために一定期間だけ無料で、その後は課金サブスクリプションに移行する "今すぐ試す" サブスクリプションがあります。 "ライセンス持ち込み" アクティブ化も、パブリッシャーをサポートするためのオプションです。どちらの課金オプションでも、Azure アプリ (ソリューション テンプレートや管理対象アプリなど) を使って仮想マシンが展開されるシナリオでは、プロビジョニングされるすべての Azure リソースが顧客に直接課金されます | AppSource は、試用エクスペリエンスのプロビジョニングを提供していますが、現在は商取引が有効な公開オプションは提供していません。これにより、追加の投資や変更なしで、現在の注文および課金インフラストラクチャを利用できます        |
|**他のパートナーとの接続の促進**     |現在、Azure Marketplace では、パブリッシャーはサービス プロバイダーまたは配信パートナーをオファリングにリンクすることはできませんが、この機能は 2018 年に使用できるようになります         |  独立系ソフトウェア ベンダー、システム インテグレーター、および管理対象サービス プロバイダーは、特定の実装シナリオにリンクでき、新規顧客への共同販売をサポートできます      |
|**Automation**     |    現在、Azure Marketplace では、パブリッシャーはサービス プロバイダーまたは配信パートナーをオファリングにリンクすることはできません     | アドインのプロビジョニングで自動 SaaS を利用し、ソリューション テンプレートを使って SaaS ベースのデータ コレクションと展開シナリオを自動化します        |独立系ソフトウェア ベンダー、システム インテグレーター、および管理対象サービス プロバイダーは、特定の実装シナリオにリンクでき、新規顧客への共同販売をサポートできます
|**複数のクラウド種類**     |   Azure Stack によりパブリック クラウドとオンプレミス ソリューションの両方に公開します。または、Azure Government および中国やドイツなどのリージョン クラウドに公開します      |    現在、AppSource では、Azure Stack、Azure Government、またはリージョン クラウドのサポートは提供されていません     |
|**顧客へのコンテキスト内プレゼンテーション**     |  コンテキスト検索のために、Azure Portal 内エクスペリエンスでソリューションを利用できるようにします (仮想マシンとソリューション テンプレート)       |  Dynamics 365、Power BI、Office 365 など Microsoft 製品のアプリ内エクスペリエンスをより多くのお客様に届けます    |

## <a name="select-a-publishing-option"></a>公開オプションの選択

各ネットショップは、複数の公開オプションとオファリング タイプ (リスト、試用、トランザクション) をサポートします。 アプリケーションとサービスの詳細情報を最もよく表すオファリング タイプを選びます。 すべての公開オプションで、パートナーはリード共有にアクセスできます。 

このセクションでは、両方のネットショップで使用できるオプションについて説明します。 


|**公開オプション**  | **オファリング タイプ** | **ネットショップ**  |
|---------|---------|---------|
|**リスト**    |    連絡する、コンサルティング サービス     |  Azure Marketplace、AppSource       |
|**試用版**   |     無料試用版、SaaS 試用版、対話型デモ、体験版    |  Azure Marketplace、AppSource       |
|**トランザクション**     |   仮想マシン、ソリューション テンプレート、管理対象アプリ      |    Azure Marketplace     |

### <a name="list"></a>リスト

試用レベルまたはトランザクション レベルの参加が適していない場合は、**連絡する**を使います。 この方法の利点は、市場にソリューションのあるパブリッシャーはすぐに、基本的な取引に発展させてビジネスを開始できるリードの受け取りを始められることです。 一方、欠点は、他のオファリング タイプと比べて顧客エンゲージメントが制限されることです。

>[!IMPORTANT]
>[連絡する] は、掲載の種類としてはお勧めしません。試用エクスペリエンスを用意する方法が他にない場合のみ使用してください。 顧客エンゲージメントが最も高いのは試用版とトランザクションです。 なんらかの種類の試用エクスペリエンスがある場合は、オンボード プロセスによって、シナリオに応じてオプションの 1 つが案内されます。

製品が主にプロフェッショナル サービス (評価、実装、ワーク ショップなど) で構成される場合は、**コンサルティング サービス**のオファリング タイプを使います。 オファリングの範囲、期間、価格は固定でなければならず、1 顧客を対象として、オンサイトで実施する必要があります。

### <a name="trial"></a>試用

試用エクスペリエンスを提供すると、顧客に対するエンゲージメント レベルが高くなり、ソリューションの公開がいっそう充実します。 試用を使うと、顧客は購入前にソリューションを調べることができます。 試用エクスペリエンスでは、ネットショップでのプロモーションの機会が多くなり、顧客エンゲージメントからより多くの有望なリードを期待できます。
 
すべての試用オプションは、顧客の環境または Azure サブスクリプションではなく、パートナーの試用版環境や Azure サブスクリプションに展開されます。 試用は顧客主導にする必要があり、追加購入はなくし、最小限の追加構成 (ある場合) で簡単なユース ケースが完成するようにします。 試用は、少なくとも試用期間中は無料サポートを含む必要があります。 最善の結果が得られるように、試用ユーザーは熟考された評価パスに沿って進展および監視する必要があります。 パブリッシャーには、Marketplace のリードとパブリッシャー独自のアプリ内インテリジェンスの両方を使って、試用ユーザーを監視および管理することをお勧めします。

試用の一般的なシナリオは次の 4 つです。


|**試用のオプション**  |**主な利点**  |**このオプションを選ぶ場合**  |
|---------|---------|---------|
|**無料試用版**    |     顧客は自動的な方法で購入して有料に変換する前にパートナーの製品を試すことができ、顧客および Microsoft 販売チームとの共同エンゲージメントに概念実証を可能にします |     ソリューションが仮想マシンまたはソリューション テンプレートであるか、またはソリューションが SaaS オファリングであり、マルチテナント SaaS 製品を提供する場合、顧客をすばやく稼働状態にできる最初の実行エクスペリエンスがある場合、単一テナントでも顧客を "ゲスト ユーザー" として追加する場合|
**体験版**     |     顧客が購入前に製品を試すことができるようにし、かつ、事前構成済みセットアップでのソリューションのガイド付きエクスペリエンスを提供します |   ソリューションが単一テナントの仮想マシン、ソリューション テンプレート、または SaaS アプリの場合、またはプロビジョニングが複雑な場合、または試用を有料オファリングに変換する方法がない場合 |
|**対話型デモ**    |  複雑なセットアップなしで、動作する製品を顧客が見られるようにします       |    試用期間に実現するのが困難な複雑なセットアップを必要とするソリューションの場合     |


#### <a name="free-trial"></a>無料試用版

ソリューションまたはアプリが無料で試用できる SaaS ベースの試用版を提供するときは、**無料試用**を使います。 このオプションは、関心のある顧客から高品質のリードを抽出し、ビジネスを開始するのに役立ちます。 無料試用は、限定使用または期間制限のある試用アカウントとして提供でき、ソフトウェアの有料使用への変換を加速する行動喚起を含む必要があります。

#### <a name="test-drive"></a>体験版

ソリューションが IaaS または SaaS アプリ経由で 1 つまたは複数の仮想マシンを使って展開されるときは、**体験版**を使います。 このアプローチの利点は、顧客が追加コストなしで評価できる、パートナーがホストするソリューションの "ガイド付きツアー" に格納された仮想アプライアンスまたはソリューション全体の環境の自動プロビジョニングです。 顧客は既存の Azure 顧客である必要はなく、高品質のリードを生成するのに役立ちます。

**体験版**には他に次の利点があります。

- Marketplace でのユーザー検索の 27% は、ユーザーが体験版でオファリングを見るだけで改善されます 
- 体験版ありのオファリングは、体験版なしのオファリングよりリードが 38% 増えます 
- Azure Marketplace での新規顧客獲得の 36% は体験版を利用した顧客からです 
- Microsoft の現場販売者は共同販売作業のためにパートナーの製品をよりよく理解できます

#### <a name="interactive-demo"></a>対話型デモ

**対話型デモ**で顧客に製品のガイド付きエクスペリエンスを提供します。 このオプションの利点は、複雑なソリューションの複雑なプロビジョニングなしで試用エクスペリエンスを提供できることです。 このオプションでは、顧客はソリューションを見て回ることができ、パブリッシャーは基本的な取引に発展させてビジネスを開始できるリードの受け取りを始めることができます。 

### <a name="transact"></a>トランザクション

Azure Marketplace では、顧客のサブスクリプションに仮想アプライアンスとしてソリューションを展開するときに**仮想マシン**を使います。 仮想マシンは、従量課金制モデルまたは BYOL 対応ライセンス モデルで完全に販売可能です。 Microsoft は商用トランザクションをホストし、パブリッシャーに代わって顧客に請求します。 パブリッシャーには、顧客が好む Microsoft との支払い関係 (Enterprise Agreement など) を利用できる利点があります。 

>[!NOTE]
>現在、Enterprise Agreement の年額コミットメントは仮想アプライアンスの Azure 利用には使えますが、パブリッシャーのソフトウェア ライセンス料金には使えません。

仮想アプライアンスに加えて追加の展開と構成の自動化がソリューションに必要な場合は、**Azure ソリューション テンプレート**を使います。 ソリューション テンプレートは、1 つまたは複数の仮想マシン リソースのプロビジョニングを自動化でき、さらに、ネットワーク リソースとストレージ リソースをプロビジョニングできます。 ソリューション テンプレートは、1 つの仮想マシンだけでなく、IaaS ベースのソリューション環境全体でも、オートメーションの利点を提供できます。 ソリューション テンプレートの作成について詳しくは、[こちら](https://github.com/MicrosoftDocs/azure-docs)をご覧ください。

パブリッシャーまたは顧客がサードパーティ (SI や MSP など) によるソリューションの管理を希望する場合に、顧客のサブスクリプションに仮想マシンまたは IaaS ベースのソリューション全体を展開するときは、**Azure 管理対象アプリ**を使います。 詳しくは、[こちらで管理対象アプリの構築](https://docs.microsoft.com/azure/managed-applications/overview)についてご覧ください。 よく寄せられる質問の一覧については、[Azure Marketplace の FAQ](https://azure.microsoft.com/marketplace/faq/) を参照してください。

>[!NOTE]
> 管理対象アプリは、Marketplace から展開できる必要があります。 お客様の通信に懸念がある場合、リード共有を有効にしておけば、興味を持つお客様に連絡することができます。

### <a name="azure-certified"></a>Azure 認定

Azure Marketplace で公開されるすべての仮想マシンは、**Azure 認定**プログラムに関してテストされます。 このプログラムは、仮想マシンが Azure プラットフォームおよび Marketplace 販売モデルと互換性があることを顧客に保証し、オンライン イメージがウイルスやマルウェアなどの安全性コンプライアンス対応であることをテストし、検証済みソリューションとして Microsoft の企業顧客へのプロモーションを強化するオファリング レベルでのバッジ取得を可能にします。

#### <a name="marketplace-commercial-considerations"></a>Marketplace での販売に関する考慮事項

Marketplace には無料で参加できます。 リスト、試用、および BYOL トランザクションのオプションを使って公開するときは、Marketplace への参加に対する収益の共有はありません。 詳しくは、「[Microsoft Azure Marketplace 参加ポリシー](https://azure.microsoft.com/support/legal/marketplace/participation-policies/)」をご覧ください。

#### <a name="pay-as-you-go-and-bring-your-own-license-billing-options"></a>従量課金制とライセンス持ち込みの課金オプション

従量課金トランザクション公開オプションを使うと、パブリッシャーの使用量ベースのソフトウェア ライセンスの収益は、パブリッシャーと Microsoft の間で 80%/20% で折半されます。 1 つのオファリングを従量制とライセンス持ち込み両方の課金モデルで価格設定でき、異なる SKU としてオファリング レベルで共存できます。 これは、Cloud パートナー ポータルの [Offer]\(オファー\) で構成できます。

次の例を考えてみましょう。

オプションとして従量課金を有効にする場合:


|ライセンス コスト   | 1.00 ドル/時間        |
|---------|---------|
|Azure 使用コスト (D1/1 コア)     | 0.14 ドル/時間  |
|**顧客は Microsoft から請求されます**    | **1.14 ドル/時間**       |

このシナリオでは、Microsoft は公開された仮想マシン イメージの使用に対して 1 時間あたり 1.14 ドルを請求します。


|**Microsoft が請求** |**1.14 ドル/時間**  |
|---------|---------|
|Microsoft は、ライセンス コストの 80% をパブリッシャーに支払います | 0.80 ドル/時間        |
|Microsoft は、ライセンス コストの 20% を受け取ります    | 0.20 ドル/時間        |
|Microsoft は、Azure の使用コストを受け取ります     |   0.14 ドル/時間      |

オプションとしてライセンス持ち込みを有効にした場合:

|ライセンス コスト     | ライセンス料はパブリッシャーによって交渉および請求されます        |
|---------|---------|
|Azure 使用コスト (D1/1 コア)    | 0.14 ドル/時間         |
|**顧客は Microsoft から請求されます**     | **0.14 ドル/時間**        |

このシナリオでは、Microsoft は公開された仮想マシン イメージの使用に対して 1 時間あたり 0.14 ドルを請求します。 

|**Microsoft が請求**    |   **0.14 ドル/時間**      |
|---------|---------|
|Microsoft は、Azure の使用コストを受け取ります     |    0.14 ドル/時間     |
|Microsoft は、ライセンス コストの 0% を受け取ります     |  0.00 ドル/時間       |

### <a name="single-billing-and-payment-methods"></a>一括請求および支払い方法

トランザクション公開オプションを使う重要な利点は、Microsoft が基になっている Azure の使用料と同時にライセンス コストを顧客に直接 "一括して請求" できることです。 このシナリオでは、Microsoft がパブリッシャーに代わって請求および徴収を行うので、パブリッシャーが独自に顧客と調達関係を築く必要はありません。 これはパブリッシャーの時間とリソースの節約になり、パブリッシャーは請求ではなく販売に集中できます。

### <a name="enterprise-agreement"></a>Enterprise Agreement

マイクロソフトの顧客は、Enterprise Agreement (EA) を使って、Azure の使用料など、Microsoft 製品の料金を支払う場合があります。 この支払いオプションは、ソフトウェアおよびクラウド サービスのライセンスを最低 3 年間供与したい組織のために作られています。 顧客は、1 回前払いする代わりに、分割して支払うことができます。 EA の顧客が従量課金制のトランザクション リストを使うときは、パブリッシャーのソフトウェア ライセンス コストの請求は、四半期ごとの EA 超過請求サイクルに従います。

### <a name="monetary-commitment"></a>年額コミットメント 

Enterprise Agreement の顧客は、Azure に対する前払いによる金銭的コミットメントにより、Azure を契約に追加できます。 このコミットメントは、Azure がグローバル データセンターから提供するさまざまな Cloud Services の組み合わせを使用して、年間を通じて使用されます。

## <a name="prerequisites-for-marketplace-publishing"></a>Marketplace での公開に関する前提条件

### <a name="for-all-marketplace-publishing-options"></a>すべての Marketplace 公開オプションについて


|**要件**  |**詳細**  |**公開オプション**  |
|---------|---------|---------|
|**参加ポリシー**    | Azure Marketplace 参加ポリシーは[こちら](https://azure.microsoft.com/support/legal/marketplace/participation-policies/)で確認してください。       | リスト、試用、トランザクション        |
|**Microsoft との統合**    | Azure Marketplace のオファリングでは、Compute、Networking、Storage などの Microsoft Azure サービスを利用または拡張し、データベース、セキュリティ、ネットワークなどの Azure Marketplace の既存カテゴリに合わせる必要があります。詳細な一覧については、[こちら](https://azuremarketplace.microsoft.com/marketplace/apps)をご覧ください。        | リスト、試用、トランザクション        |
|**対象ユーザー**    | Azure Marketplace のオファリングは、IT 専門化、クラウド開発者、または顧客のその他の技術的役割を対象としたものでなければなりません。       |  リスト、試用、トランザクション 
|**リード管理**    | Marketplace からリードを受け取るには、リード データを受け付けるように CRM (Marketo、Microsoft Dynamics、または Salesforce) を有効にする必要があります。        |   リスト、試用、トランザクション      |
|**プライバシー ポリシーと使用条件**     |   パブリッシャーのプライバシー ポリシーをパブリック URL 経由で利用できる必要があり、パブリッシャーの使用条件を公開の間にテキストとして入力する必要があります。      |   リスト、試用、トランザクション      |
|**サポート**     |  パブリッシャーのオファリングには、顧客がヘルプを発見できるパブリックに利用可能なサポート URL を含める必要があります。 試用では、試用期間中は追加コストなしでサポートを提供する必要があります。       |  試用、トランザクション       |

### <a name="prerequisites-specific-to-trial-publishing"></a>試用公開に固有の前提条件

|**要件**  | **詳細**  |**公開オプション**  |
|---------|---------|---------|
|**無料試用期間および試用エクスペリエンス**     |  顧客は、限られた期間、パブリッシャーのアプリを無料で使用できる必要があります。<br>つまり、顧客は、パブリッシャーの製品のライセンス料またはサブスクリプション料の対象とならず、基になる Microsoft のファーストパーティ製品またはサービスのコストもかかりません。 すべての試用オプションはパブリッシャーの Microsoft 製品サブスクリプションに展開されるため、試用コストの最適化と管理はパブリッシャーのみによって制御されます。<br>無料試用版、対話型デモ、または体験版を選ぶことができます。 何を選んでも、無料試用では顧客に、追加コストなしでアプリを試すための最低限の時間を提供する必要があります。<br> 体験版の作成を始めるには、cloudmarketplace@microsoft.com にお問い合わせください。 注: Azure Marketplace の SaaS 試用エクスペリエンスでは、ユーザーに Active Directory の仕事用資格情報でのサインインを許可する必要があります。 [詳細情報。](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-devhowto-appsource-certified#appsource-trial-experiences) |   試用版      | 
| **簡単に構成できるターンキー ソリューション**    |  アプリは、短時間で簡単に構成およびセットアップできる必要があります。       |  試用版       |
|**可用性/アップタイム**    |    SaaS アプリまたはプラットフォームのアップタイムは 99.9% 以上である必要があります。     |    試用版     |
|**Azure Active Directory**    |    オファリングでは、同意が有効な Azure Active Directory フェデレーション シングル サインオン (AAD フェデレーション SSO) を許可する必要があります。      |  試用版|

### <a name="prerequisites-specific-to-transact-publishing"></a>トランザクション公開に固有の前提条件


|**要件**  |**詳細** |**公開オプション**  |
|---------|---------|---------|
|**請求/メータリング**    |  仮想マシンは、ライセンス持ち込みまたは使用量に基づく毎月の請求をサポートする必要があります。       |    トランザクション    |
|**Azure と互換性がある仮想ハード ディスク (VHD)**     |   仮想マシンは、[Windows](https://docs.microsoft.com/en-us/azure/marketplace-publishing/marketplace-publishing-vm-image-creation) または [Linux](https://docs.microsoft.com/en-us/azure/marketplace-publishing/marketplace-publishing-vm-image-creation) 上に構築されている必要があります。    |   トランザクション      |

### <a name="prerequisites-specific-to-consulting-services-publishing"></a>コンサルティング サービス公開に固有の前提条件


|**要件** |**詳細**  |**公開オプション**  |
|---------|---------|---------|
|**サービス オファリングの特性**     | コンサルティング サービスは、固定範囲、固定期間、固定価格 (または無料) エンゲージメント、主として購入前指向、単一顧客限定、オンサイト実施で、提供される必要があります。        |    一覧表示     |
|**コンサルティング サービスのパートナー要件**    |   **AppSource のみ.**  Dynamics 365 for Customer Engagement の [Silver または Gold クラウド カスタマー リレーションシップ マネジメント コンピテンシー](https://partner.microsoft.com/en-us/membership/cloud-customer-relationship-management-competency). Dynamics 365 for Finance および Operations Enterprise エディション: Silver または Gold [エンタープライズ リソース プラニング](https://partner.microsoft.com/en-us/membership/enterprise-resource-planning-competency) コンピテンシー、および以降の 12 か月間にクラウド運用で 25,000 ドル以上の収益。 Dynamics 365 for Finance and Operations、Business エディション: 少なくとも 1 顧客に[クラウド サービス プロバイダー (CSP)](https://partner.microsoft.com/en-us/cloud-solution-provider) または [デジタル指名パートナー (DPOR)](https://partner.microsoft.com/en-us/membership/digital-partner-of-record) としてサービスを提供している。 Power BI: [ソリューション パートナー](file:///C:/Users/ellacroi/Downloads/BI%20Partner%20Program%20Overview%20&%20Incentives.pdf)の条件を満たしている。 PowerApps: [パートナー ショーケース](https://powerapps.microsoft.com/en-us/partner-showcase/) ソリューションを持っている |    一覧表示     |

## <a name="using-azure-active-directory-to-enable-trials"></a>Azure Active Directory を使用して試用を有効にする
Azure Active Directory (AAD) はクラウド ID サービスであり、業界標準のプロトコル OAuth および OpenID Connect を使って Microsoft 職場または学校アカウントでの認証を有効にします。 AAD について詳しくは、[こちら](https://www.microsoft.com/en-us/cloud-platform/azure-active-directory-features)をご覧ください。 

Microsoft はすべての Marketplace ユーザーを AAD で認証するので、認証されたユーザーが Marketplace で試用リストをクリックし、試用環境にリダイレクトされた場合、それ以上のサインイン ステップを必要とせずに、ユーザーを直接試用にプロビジョニングできます。 [認証の間にアプリが AAD から受け取るトークン](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-token-and-claims#sample-tokens)には、アプリでユーザー アカウントを作成するために使うことができる重要なユーザー情報が含まれているので、プロビジョニング エクスペリエンスを自動化し、変換の可能性を高くすることができます。 

AAD を使ってアプリまたは試用の 1 クリック認証を有効にすると、次のことが可能になります。

- Marketplace から試用版への顧客エクスペリエンスを簡素化する 
- ユーザーが 'Marketplace からパブリッシャーのドメインまたは試用環境にリダイレクトされる場合でも、"製品内エクスペリエンス" の感覚を維持する
- 余分なサインイン手順がないため、リダイレクトで中止される可能性を低くする
- 大規模な AAD ユーザーの展開障壁を削減する

### <a name="certifying-your-azure-active-directory-integration-for-marketplace"></a>Marketplace に対する Azure Active Directory 統合の認定

マルチテナント アプリケーションの場合:

現在 AAD をサポートしている場合

- Azure Portal でアプリケーションを登録します
- AAD でマルチテナント機能のサポート機能を有効にして、"ワンクリック" 試用環境を取得します
- [詳しくはこちらをご覧ください](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-integrating-applications)

AAD フェデレーション SSO が初めての場合

- Azure Portal でアプリケーションを登録します
- [OpenID Connect](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-protocols-openid-connect-code) または [OAuth 2.0](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-protocols-oauth-code) を使って、AAD で SSO を開発します
- AAD でマルチテナント機能のサポート機能を有効にして、"ワンクリック" 試用環境を取得します
- [詳しくはこちらをご覧ください](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-devhowto-appsource-certified)

シングルテナント アプリケーションの場合:

シングルテナント アプリケーションの場合は複数のオプションがあります。

- [Azure B2B](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b) を使って、ユーザーをパブリッシャーのディレクトリにゲスト ユーザーとして追加します
- [連絡する] を使って顧客用に試用版を手動でプロビジョニングします
- 顧客ごとの "体験版" を開発します
- SSO を使ってマルチテナント サンプル デモ アプリをビルドします

## <a name="publishing-processes-by-product-for-office-dynamics-and-power-bi"></a>製品別のプロセスの公開 (Office、Dynamics、Power BI)
このセクションでは、Office、Dynamics、Power BI を拡張する AppSource アプリについて固有の要件を製品のドキュメントで確認してください。 


|製品 |公開に関する情報  |
|---------|---------|
|Office 365     |    [公開プロセスとガイドライン]( https://docs.microsoft.com/en-us/office/dev/store/submit-to-the-office-store)を確認します。     |
|Dynamics 365 for Finance and Operations  |   Enterprise Edition 向けに開発する場合は、[公開プロセスとガイドライン](https://docs.microsoft.com/en-us/dynamics365/unified-operations/dev-itpro/lcs-solutions/lcs-solutions-app-source)を確認します。      |
|Dynamics 365 for Customer Engagement |[公開プロセスとガイドライン](https://docs.microsoft.com/en-us/dynamics365/customer-engagement/developer/publish-app-appsource)を確認します。 |
|Power BI   |     [公開プロセスとガイドライン]( https://docs.microsoft.com/en-us/power-bi/developer/office-store)を確認します。    |
|Cortana Intelligence     |    [AppSource での Cortana](https://docs.microsoft.com/en-us/azure/machine-learning/team-data-science-process/cortana-intelligence-appsource-publishing-guide) について確認します。     |
|AppSource コンサルティング サービス オファリング     |  [ガイドラインを確認し、オファリングの送信方法]( https://smp-cdn-prod.azureedge.net/documents/Microsoft%20AppSource%20Partner%20Listing%20Guidelines.pdf)を学びます。    |



## <a name="cloud-partner-portal-pre-publishing-checklist-for-azure-marketplace"></a>Azure Marketplace での Cloud パートナー ポータル公開前チェックリスト

公開プロセスを始める前に、オファリングを作成するために必要なコンポーネントを理解することをお勧めします。 Cloud パートナー ポータルでオファリングの作成公開ワークフローを実行するには、次のアーティファクトが必要です。 

### <a name="storefront-details"></a>ネットショップの詳細


|必要なアーティファクト  |オファリングの種類  |
|---------|---------|
|**オファリングの名前 (200 文字) と説明 (2000 文字)**    |  すべて        |
|**MPN ID とコンピテンシー**   |  コンサルティング サービス       |
|**国/リージョンの可用性**   | すべて        |
|**契約の期間**     |   コンサルティング サービス      |
|**該当する業界、カテゴリ、検索キーワード**     |  すべて       |
|**会社のロゴ (48 x 48、216 x 216)**     |  コンサルティング サービス       |
|**製品の概要ビデオ (省略可能)**  |  すべて       |
|**スクリーンショット (最大 5 個、1280 x 720)**   |    すべて     |
|**マーケティング ドキュメント (最大 3 個)**    |  すべて       |
|**リードの宛先**    |   すべて      |

### <a name="contacts"></a>連絡先


|必要なアーティファクト  |オファリングの種類  |
|---------|---------|
|**連絡先情報 (サポート、エンジニアリング、営業)**    |    すべて     |

### <a name="technical-info"></a>技術情報


|必要なアーティファクト  |オファリングの種類 |
|---------|---------|
|**試用版 URL**     |  すべての試用オファリングの種類       |
|**サポートされている言語**    |   すべての試用オファリングの種類      |
|**アプリのバージョン番号とリリース日**    |   すべての試用オファリングの種類      |
|**サポート URL**    |   すべての試用オファリングの種類、仮想マシン      |
|**使用条件とプライバシー ポリシーの URL**     |    すべて     |

### <a name="test-drive"></a>体験版


|必要なアーティファクト  |オファリングの種類  |
|---------|---------|
|**説明と期間**     |  体験版のみ       |
|**ユーザー マニュアル**     |   体験版のみ      |
|**体験版ビデオ (最大 1 個)**     |  体験版のみ       |
|**体験版の国/リージョンの可用性**    |   体験版のみ      |
|**Azure リソース グループ名**   |         |
|**Azure サブスクリプション ID**     |  体験版のみ       |
|**Azure AD テナント ID**   |    体験版のみ     |
|**Azure AD アプリ ID**  |  体験版のみ       |
|**Azure AD アプリ キー**     |   体験版のみ      |

### <a name="storefrontmarketplace"></a>ネットショップ/Marketplace


|必要なアーティファクト  |オファリングの種類  |
|---------|---------|
|**タイトル (最大 50 文字)**    |  トランザクション: 仮想マシン、Azure アプリ (ソリューション テンプレートと管理対象アプリ)       |
|**概要 (最大 200 文字)**    |  トランザクション: 仮想マシン、Azure アプリ (ソリューション テンプレートと管理対象アプリ)       |
|**長い概要 (最大 256 文字)**     |   トランザクション: 仮想マシン、Azure アプリ (ソリューション テンプレートと管理対象アプリ)      |
|**HTML 形式の説明 (最大 3000 文字)**    |  トランザクション: 仮想マシン、Azure アプリ (ソリューション テンプレートと管理対象アプリ)       |
|**会社のロゴ (40 x 40、90 x 90、115 x 115、255 x 115、815 x 290)**    |  トランザクション: 仮想マシン、Azure アプリ (ソリューション テンプレートと管理対象アプリ)       |

### <a name="sku"></a>SKU


|必要なアーティファクト  |オファリングの種類  |
|---------|---------|
|**バージョン番号**     |    トランザクション: Azure アプリ (ソリューション テンプレートと管理対象アプリ)     |
|**すべてのテンプレート ファイルと createUIDefinitionFile を含むパッケージ ファイル**   |トランザクション: Azure アプリ (ソリューション テンプレートと管理対象アプリ)         |
|**オペレーティング システムの詳細**    |   トランザクション: 仮想マシン      |
|**使用するポートとプロトコル**    |  トランザクション: 仮想マシン       |
|**使用する各 VHD のディスク バージョンと SAS URL**   |  トランザクション: 仮想マシン       |

## <a name="become-a-publisher"></a>パブリッシャーになる

このセクションでは、Azure Marketplace と AppSource でパブリッシャーになる手順、およびオファリングのビルド、公開、管理に使う Cloud パートナー ポータルにアクセスする手順を説明します。 

### <a name="process-overview"></a>プロセスの概要


|Marketplace の登録手順  |Time  |[説明]  |
|---------|---------|---------|
|Microsoft ID を作成する     |   15 分      |   パートナーは、パートナーの識別に使われる Microsoft ID を持つ必要があります。 この Microsoft ID を使って、Cloud パートナー ポータルにアクセスします。       |
|Marketplace 申請フォーム     |  1 ～ 3 日       |  パートナーは、申請フォームを送信して、Marketplace の承認プロセスを開始する必要があります。 フォームが送信されると、Marketplace オンボード チームによって申し込みが確認され、要求が検証されます。       |
|デベロッパー センターに登録する     |    5 ～ 10 日     | パートナーが登録されている国において有効な税 ID を持つ有効な法人であることを Microsoft が確認するため、Microsoft デベロッパー センターでの登録が必要です。 デベロッパー センターでは、パートナーは Microsoft 開発者として登録でき、Azure 開発者プログラムにアクセスできます。 <br><br>*注: Marketplace 申請フォームに入力しない場合、99 ドルの登録料金の支払いを求められます。この手数料を払わなくて済むようにするには、Marketplace 申請フォームを提出すると、メールでプロモーション コードを受け取ります。*  |
|Cloud パートナー ポータルにログインする     |  15 分       |   パートナーは、申請承認通知を Marketplace チームから受け取ると、[Cloud パートナー ポータル](https://cloudpartner.azure.com/)にアクセスできるようになります。 パートナーは、申請フォームで使った Microsoft ID を使って、Cloud パートナー ポータルのパブリッシャー プロファイルにログインする必要があります。 デベロッパー センターに登録した後、パートナーは、デベロッパー センター アカウントと、公開する Azure Marketplace パブリッシャー プロファイルを関連付ける必要があります。      |

#### <a name="create-a-microsoft-id"></a>Microsoft ID を作成する

Marketplace の公開プロセス全体を通して、Marketplace アカウントを示すメール アドレスを使います。 このメール アドレスは、Microsoft ID として登録されている必要があり、[Microsoft デベロッパー センター](https://developer.microsoft.com/)と [Cloud パートナー ポータル](https://cloudpartner.azure.com/)の両方で使われます。 Azure Marketplace と AppSource のオファリングにはただ 1 つの Microsoft ID アカウントを使う必要があり、他のサービスまたはオファリングとは共有しないことを強くお勧めします。

できる限り、会社のドメインに含まれ、社内の IT チームによって管理されているメール アドレスを選ぶ必要があります。 ID を作成する前に、Azure Marketplace アカウントを管理するための Microsoft ID の作成に関するガイドラインと、AAD フェデレーション ドメインでの Microsoft ID に関するガイダンスを含め、[ガイドラインと手順](#Guidelines-and-How-Tos)の各セクションをご覧ください。 

#### <a name="submit-the-marketplace-nomination-form"></a>Marketplace 申請フォームを送信する
Marketplace オンボード プロセスの一環として、申請フォームを送信し、アプリケーションまたはサービス オファリングに関する情報、会社の情報、および提供するサポートのレベルを、提供する必要があります。  
フォームが送信されると、Marketplace チームによって申し込みが確認され、要求が検証されます。 要求が確認され後、Cloud パートナー ポータルで承認済みパートナーになるために完了する必要のある次の手順についての通知をメールで受け取ります。 以下で申請を提出してください。

Azure Marketplace の申請: http://aka.ms/listonazuremarketplace   
AppSource の申請: http://aka.ms/listonappsource

#### <a name="register-in-the-developer-center"></a>デベロッパー センターに登録する

[Microsoft デベロッパー センター](https://developer.microsoft.com/)を使って、会社の情報を登録します。 登録者は、合法的な企業の代表者である必要があります。また、ID を検証するために、個人情報を提供する必要があります。 登録時には、会社用に共有されている Microsoft ID を使う必要があり、[Cloud パートナー ポータル](https://cloudpartner.azure.com/)でも同じアカウントを使う必要があります。 

>[!IMPORTANT]
>作成する前に、会社がまだ Microsoft デベロッパー センターのアカウントを所有していないことを確認してください。

作成中は、会社の住所情報、銀行口座情報、および税金に関する情報が収集されます。 通常、これらは財務またはビジネス用連絡先から取得できます。 さらに、オファリングの作成と展開のさまざまなフェーズを進めるために、次のようなパブリッシャー プロファイル コンポーネントを完成させる必要があります。


|**パブリッシャー プロファイル**  |**プロファイルの開始**  |**ステージング**  |**リストと試用**  |**トランザクション**
|---------|---------|---------|---------|---------|
|**会社の登録**     | **必須**        |  **必須**       | **必須**        |  **必須**       |
|**税プロファイル ID**   |    省略可能     |    省略可能     |  省略可能       | **必須**      |
|**銀行口座**     |   省略可能      |    省略可能     |  省略可能       |  **必須**      |

このプロセスの詳細な手順については、付録「デベロッパー センターで登録する方法の説明」をご覧ください。 

#### <a name="log-in-to-the-cloud-partner-portal"></a>Cloud パートナー ポータルにログインする

Marketplace チームから申請承認通知を受け取り、[Microsoft デベロッパー センター](https://dev.windows.com)で登録した後は、[Cloud パートナー ポータル](https://cloudpartner.azure.com)にアクセスするためのアカウントが自動的に作成されます。 初回サインイン用の資格情報は、申請承認のメールに含まれます。 

Marketplace アカウント (Microsoft ID) を使って、パブリッシャー プロファイルにアクセスします。 Cloud パートナー ポータルでの最後の手順は、デベロッパー センター アカウントと公開のための Marketplace パブリッシャー プロファイルを関連付けることです。 これは、Cloud パートナー ポータルでパブリッシャー プロファイル画面の下部にあるボタンを使って行うことができます。

Cloud パートナー ポータルの使い方について詳しくは、ポータル内の [[学習]](https://cloudpartner.azure.com/#Learn) メニューで、[ドキュメント] セクションをクリックしてください。 


## <a name="getting-support"></a>サポートの利用

Azure Marketplace のサポート オプションの一覧を次に示します。

**Azure Marketplace の一般的な問い合わせ:**
|サポート窓口 |[説明] |
|---------|---------|
|電子メール: cloudmarketplace@microsoft.com     |  オンボーディング サポート配布リスト。 オンボーディングの要求、探索セッションの設定、およびパートナーとのアーキテクチャ デザイン セッション (ADS) に使われます。        |

**Azure Marketplace の公開サポート:**

|サポート窓口  |[説明]  |
|---------|---------|
|電子メール: 
azurecertified@microsoft.com |   パートナーの Azure Marketplace でのアプリケーションの公開に関するサポートを提供します。 太平洋標準時ゾーンの営業時間。      |
|電子メール:
AzureMarketOnboard@microsoft.com |   Azure Marketplace ソリューション申請フォームとプロセスに関するサポートを提供します。 太平洋標準時ゾーンの営業時間。      |
|電子メール: 
Amp-testdrive@microsoft.com |   体験版へのオンボーディング アクセスを提供します。 太平洋標準時ゾーンの営業時間。 | 

**Azure Marketplace ポータルのサポート:**

|サポート窓口  |[説明]  |
|---------|---------|
|電子メール [サポート](https://go.microsoft.com/fwlink/?linkid=844975)    |   Marketplace 公開ポータルのサポート。 年中無休でサポートを提供します。        |

**技術的サポート**


|サポート窓口  |[説明]  |
|---------|---------|
|Slack: [Marketplace 参加 Slack](https://join.marketplace.azure.com)    |   技術的な問題に関してパートナーをサポートするために Slack 環境。 現在、この環境では 350 以上のパートナーが活動しています。        |
|MSDN フォーラム: [Marketplace](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=DataMarket)     | Microsoft Developer Network のフォーラム。         |
|StackOverflow: [Azure](https://stackoverflow.com/questions/tagged/azure)     |    StackOverflow Web サイトの複数のセクションで、ソリューションを入手し、Azure と AMP に関するあらゆることについて質問するための環境が提供されています。<ul><li>StackOverflow: [Azure Marketplace](https://stackoverflow.com/questions/tagged/azure-marketplace)</li><li>StackOverflow: [Azure Resource Manager](https://stackoverflow.com/questions/tagged/azure-resource-manager)</li><li>StackOverflow: [Azure Virtual Machines](https://stackoverflow.com/questions/tagged/azure-virtual-machine)</li></ul> |


**マーケティング リソース**

|サポート窓口  |[説明]  |
|---------|---------|
|電子メール: cosell@microsoft.com    |  オンボーディング プロセスと、共同販売プログラムに関する質問に対するサポートを提供します。 太平洋標準時ゾーンに基づきます。        |
|電子メール: gtm@microsoft.com    |  Go-to-Market の特典とプログラムに関する質問へのサポートを提供します。 太平洋標準時ゾーンの営業時間。        |
|電子メール: CEBrand@Microsoft.com     |  Azure ロゴに対するブランドの使用とブランド化に関する質問にお答えします。       |

## <a name="guidelines-and-how-tos"></a>ガイドラインと方法

### <a name="guidelines-for-creating-a-microsoft-id-to-manage-an-azure-marketplace-account"></a>Azure Marketplace アカウントを管理するための Microsoft ID の作成に関するガイドライン

会社のアカウントを作成する場合、そのアカウントを開いた Microsoft アカウントを使用して複数のユーザーがログインし、そのアカウントにアクセスする必要があるのであれば、次のガイドラインに従ってください。

>[!IMPORTANT]
>デベロッパー センター アカウントへのアクセスを複数のユーザーに許可する場合は、各ユーザー (各自の Azure AD 資格情報を使ってサインインし、アカウントにアクセスできるユーザー) に対し、Azure Active Directory を使ってロールを割り当てることをお勧めします。 詳しくは、[AAD フェデレーション ドメインについてのガイダンス](#guidance-with-aad-federated-domains)に関するページをご覧ください。個人ではなく、会社のドメインに属する電子メール アドレスを使って、Microsoft アカウントを作成してください (例: windowsapps@fabrikam.com)。

- この Microsoft アカウントへのアクセスは、最小限の数の開発者に制限してください。
- 開発者アカウントにアクセスする必要があるユーザーを登録した電子メール配布リストを会社で設定し、セキュリティ情報にこの電子メール アドレスを追加してください。 これにより、リストに登録されたすべての従業員が、必要なときにセキュリティ コードを受信し、Microsoft アカウントのセキュリティ情報を管理できるようになります。 配布リストを設定できない場合は、プロンプトが表示されたとき (アカウントに新しいセキュリティ情報が追加されたときや、新しいデバイスからアクセスする必要があるとき) に、各電子メール アカウントの所有者がセキュリティ コードにアクセスし、セキュリティ コードを共有できるようにする必要があります。
- 会社の電話番号を追加する際には、内線番号なしで主要なチーム メンバーに連絡できる番号を使用してください。
- 通常、開発者が会社の開発者アカウントにログインする際には、信頼済みデバイスを使用するようにします。 主要チーム メンバーの全員が、これらの信頼済みデバイスにアクセスできるようにする必要があります。 これにより、アカウントへのアクセス時にセキュリティ コードを送信する必要性を減らせます。
- 信頼されていない PC からアカウントにアクセスできるようにする必要がある場合は、アクセス権を付与する開発者を最大 5 人に制限してください。 これらの開発者はなるべく、地理的位置やネットワーク上の場所が同じであるマシンからアカウントにアクセスするようにしてください。
- [https://account.live.com/proofs/Manage](https://account.live.com/proofs/Manage) で、会社のセキュリティ情報がすべて最新であることを頻繁に確認するようにしてください。

開発者アカウントには、主に信頼済み PC からアクセスする必要があります。 アカウントごとに生成されるコードの数 (1 週間あたり) には制限があるため、このことは非常に重要です。 またこれは、サインインをシームレスするという意味でも重要でます。
開発者アカウントに関するその他のガイドラインやセキュリティについて詳しくは、[こちら](https://docs.microsoft.com/en-us/windows/uwp/publish/opening-a-developer-account)をクリックしてください。

### <a name="guidance-for-microsoft-ids-in-aad-federated-domain"></a>AAD フェデレーション ドメインでの Microsoft ID に関するガイダンス

[Azure Active Directory](https://docs.microsoft.com/en-us/azure/active-directory/) を使って会社のアカウントがフェデレーションされている場合があり、会社のメール アドレスで Microsoft ID を作成しようとするとエラーが返されます。 エラーが発生した場合は、最初に、IT チームと共にこれがその原因かどうかを確認します。 これは既知の問題であり、解決に取り組んでいます。 回避策は次のとおりです。

**@outlook.com** ドメインで新しいメール アドレスを作成することをお勧めします。 次の手順に従います。

1. [https://signup.live.com/signup](https://signup.live.com/signup) に移動して、**[新しいメール アドレスを取得]** を選択します。


2. 新しいメール アドレスを作成し、パスワードを入力します。 これにより、新しい Microsoft ID と、outlook.com サービスのメールボックスが作成されます。 アカウントが作成されるまで、登録プロセスを続行します。

>[!IMPORTANT]
>デベロッパー センターで登録に使用する電子メール ID または配布リスト (個人から依存関係を削除するときは配布リストを推奨) が、最初に Microsoft アカウントとして登録されていることを確認してください。 登録されていない場合は、こちらの [リンク](https://signup.live.com/signup?uaid=e479342fe2824efeb0c3d92c8f961fd3&lic=1)を使用して登録してください。

また、デベロッパー センターの登録に、**Microsoft 社のドメインのメール ID を使うことはできません**。

このアカウントで Microsoft ID が作成された後、アカウントの[メールボックス](https://outlook.live.com/owa/)にログインし、このメールボックスのすべてのメールを、Azure Marketplace アカウント管理用ドメインに作成したメール アドレスに移動するメール転送ルールを作成します。 outlook.com でメール転送ルールを作成する方法については、こちらの[リンク](https://support.office.com/en-us/article/Use-rules-in-Outlook-Web-App-to-automatically-forward-messages-to-another-account-1433e3a0-7fb0-4999-b536-50e05cb67fed)をご覧ください。

この最後のステップが完了すると、すべてのメール/通信が Microsoft ID からお使いのドメインのメール アカウントに送信されるようになります。 Microsoft デベロッパー センターと Cloud パートナー ポータルの両方で認証を行うには、@outlook.com メール アドレスを使う必要があります。

### <a name="instructions-on-how-to-register-in-the-development-center"></a>デベロッパー センターに登録する方法

1. 新しい Internet Explorer InPrivate または Chrome Incognito のブラウズ セッションを開き、個人用アカウントにサインインしていないことを確認します。
2. [http://dev.windows.com/registration?accountprogram=azure](http://dev.windows.com/registration?accountprogram=azure) に移動して、デベロッパー センターでご自身を販売者として登録します。 作業を進める前に、次の重要な注意事項をお読みください。

   ![デベロッパー センターの電子メール アドレス](./media/marketplace-publishers-guide/registerdevcenteremail.png)

3. [アカウントの保護にご協力ください] ウィザードを完了します。これにより、電話番号または電子メール アドレスから ID が検証されます。
4. [登録] の [アカウント情報] セクションで、ドロップダウン メニューから **[アカウントの国/地域]** を選択します。

   ![アカウント情報](./media/marketplace-publishers-guide/devcenterregistrationaccountinfo.png)
   
   >[!WARNING]
   >"販売元" の国: Azure Marketplace でサービスを販売するには、登録済みエンティティが、承認されたいずれかの "販売元" の国に所在している必要があります。 この制限は、支払いおよび課税上の理由から生じます。 詳細については、Marketplace 参加ポリシーに関するページ (https://azure.microsoft.com/support/legal/marketplace/participation-policies/) を参照してください。

5. [アカウントの種類] として、**[会社]** を選択し、**[次へ]** をクリックします。

   >[!IMPORTANT]
   >アカウントの種類と、自社に最適な選択について理解を深めるには、「[アカウントの種類、場所、料金](https://docs.microsoft.com/en-us/windows/uwp/publish/account-types-locations-and-fees)」をご覧ください。

6. **発行者表示名**を入力します。通常は、会社名を使用します。

   >[!TIP]
   >プランが表示されたら、デベロッパー センターに入力した発行者表示名は Azure Marketplace に表示されませんが、 登録プロセスを完了するために入力する必要があります。

7. アカウント確認用の **連絡先情報** を入力します。

   >[!IMPORTANT]
   >デベロッパー センターで会社を承認するための検証プロセスで使用されるため、連絡先情報は正確に入力してください。

8. **会社の承認者**の連絡先情報を入力します。 会社の承認者とは、デベロッパー センターでアカウントを作成する権限が、組織の代表者として付与されていることを証明できる社内の担当者です。 完了したら、**[次へ]** をクリックし、**[支払い] セクション**に移動します。

   ![デベロッパー センターの支払い](./media/marketplace-publishers-guide/devcenterregistrationpayment.png)

9. アカウントの料金を支払うための支払い情報を入力します。 登録コストが免除されるプロモーション コードがある場合は、ここで入力できます。 ない場合は、クレジット カード情報、またはサポートされている市場であれば PayPal 情報を入力します。 完了したら、**[次へ]** をクリックし、**確認画面**に移動します。

   ![デベロッパー センターの支払い](./media/marketplace-publishers-guide/devcenterregistrationpayment2.png)

10. アカウント情報を見直し、すべて正しいことを確認します。 次に、Microsoft Azure Marketplace 発行者契約の契約条件を読んで同意します。 チェック ボックスをオンにすると、これらの条件を読んで同意したと見なされます。
11. **[完了]** をクリックし、登録内容を確認します。 確認メッセージが電子メール アドレスに送信されます。
12. 無料プランのみを公開する予定の場合は、**[Go to the [Cloud Partner Portal](https://cloudpartner.azure.com)]\(Cloud パートナー ポータルに移動する\)** をクリックします。

商用 (トランザクション) オファリング (時間単位の課金モデルが指定された仮想マシン オファリングなど) の公開を計画している場合は、**[アカウント情報を更新してください]** をクリックします。そこで、デベロッパー センター アカウントに税金と銀行口座の情報を入力する必要があります。

後で税金と銀行の情報を更新する場合は、次のセクションに移動できます。 

>[!IMPORTANT]
>商用 (トランザクション) プランの場合、税金および銀行口座の情報の入力を完了することなく、プランを運用環境に移行することはできません。

#### <a name="add-tax-and-banking-information"></a>税金と銀行取引情報の追加

購入向けに商用プランを発行する場合は、支払い情報と税金情報を追加し、デベロッパー センターでの検証のために送信する必要があります。 無料オファリングまたは BYOL オファリングのみを公開する場合は、この情報を追加する必要はありません。 後ほど追加することはできますが、税金情報の検証に時間がかかります。 購入向けに商用プランを提供することが確定している場合は、これらの情報を可能な限り早く入力することをお勧めします。

**銀行口座情報**
1. Microsoft アカウントを使って、Microsoft デベロッパー センターにサインインします。
2.  左側の **[支払いアカウント]** をクリックし、**[支払方法の選択]** で **[銀行口座]** または **[PayPal]** をクリックします。

   >    [!IMPORTANT]
   >    Marketplace で顧客が購入した商用オファーがある場合、このアカウントで購入の支払いを受け取ることになります。

3. 支払い情報を入力し、問題がないことを確認して [保存] をクリックします。

   >    [!IMPORTANT]
   >    支払いアカウントを更新または変更する必要がある場合は、上記と同じ手順に従い、現在の情報の代わりに新しい情報を使用します。 支払いアカウントを変更すると、最大で支払い期間 1 回分の遅延が支払いに発生する可能性があります。 この遅延は、支払いアカウントが初めて設定された場合と同様に、アカウント変更の確認が必要になるために発生します。 アカウントが確認された後も、お支払いは引き続き一括払いになります。期日が現在の支払い期間内にある支払いは、次の支払い期間に追加されます。

4. **[次へ]** をクリックします。 

**税金情報**

1. 必要に応じて、Microsoft アカウントを使用して [Microsoft デベロッパー センター](https://dev.windows.com) にサインインします。
2. 左側のメニューの **[税プロファイル]** をクリックします。
3. **[税フォームの設定]** ページで、永続権を持つ国または地域を選択し、第一市民権を保持する国または地域を選択します。 **[次へ]** をクリックします。
4. 税金の詳細を入力し、**[次へ]** をクリックします。

   >    [!WARNING]
   >    Microsoft デベロッパー センター アカウントで税金と銀行口座の情報の入力を完了することなく、商用プランを運用環境に移行することはできません。

デベロッパー センター登録で問題が発生した場合は、次の手順でサポート チケットを記録してください。

1. サポート リンク https://developer.microsoft.com/windows/support に移動します。
2. **[お問い合わせ]** セクションで、**[インシデントを提出]** をクリックします (次のスクリーンショットを参照)。
3. **[問題の種類]** で [Dev Center のヘルプ] を、**[カテゴリ]** で [アプリの発行と管理] を選択します。 その後、[メールでのお問い合わせ] をクリックします。

   ![デベロッパー センターの問題](./media/marketplace-publishers-guide/devcentersubmitincident.png)

4. ログイン ページが表示されます。 Microsoft アカウントのサインインを使用します。 Microsoft アカウントがない場合は、こちらのリンクを使って作成します。 
5. 問題の詳細を入力し、**[送信]** をクリックしてチケットを送信します。