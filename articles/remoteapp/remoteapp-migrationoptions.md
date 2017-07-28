---
title: "Azure RemoteApp から移行する際の選択肢 | Microsoft Docs"
description: "Azure RemoteApp から移行する際の選択肢について説明します。"
services: remoteapp
documentationcenter: 
author: ericorman
manager: mbaldwin
ms.assetid: c4e0e5bc-5c13-4487-b1b6-ebf2a5edc1f0
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2017
ms.author: mbaldwin
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: 9ab63124e2521ee1922d15c1e388c54d50eb8301
ms.contentlocale: ja-jp
ms.lasthandoff: 06/17/2017


---
# <a name="options-for-migrating-out-of-azure-remoteapp"></a>Azure RemoteApp から移行する際の選択肢
> [!IMPORTANT]
> Azure RemoteApp の提供は、2017 年 8 月 31 日で終了します。 詳細については、 [お知らせ](https://go.microsoft.com/fwlink/?linkid=821148) をご覧ください。


[提供終了のお知らせ](https://go.microsoft.com/fwlink/?linkid=821148)があったため、または評価が完了したために Azure RemoteApp のご使用を停止された場合は、Azure RemoteApp から別のアプリ サービスに移行する必要があります。 移行には、自己管理型 (サービスとしてのインフラストラクチャ [IaaS] と呼ばれることが多い) デプロイと、完全管理型 (サービスとしてのプラットフォームまたはサービスとしてのソフトウェア [PaaS/SaaS] と呼ばれることが多い) サービスの、2 つの異なるアプローチがあります。 

セルフ サービスの IaaS は、ユーザーが管理、操作、および所有し、仮想マシン (VM) または物理システムに直接デプロイする手動によるデプロイです。 一方、完全管理型の PaaS/SaaS サービスは、Azure RemoteApp とよく似ており、パートナーが操作とサービスを処理するリモート処理ソリューションの上にサービス層を提供し、ユーザーが顧客としてイメージとアプリを管理することができます。

[Azure RemoteApp ウェビナー移行時オプションを表示](https://social.msdn.microsoft.com/Forums/azure/40557aaa-3e9f-403c-b221-ad3eac10dc56/migration-option-webinar-recordings?forum=AzureRemoteApp)するか、詳細 (さまざまなホスト オプションの例を含む) を確認します。

## <a name="self-managed-iaas-solutions"></a>自己管理型 (IaaS) ソリューション
### <a name="rds-on-iaas"></a>**IaaS 上の RDS**
RemoteApp またはデスクトップのオンプレミスを使用するか、ホストされた環境 (Azure VM など)で、ネイティブのセッション ベースのリモート デスクトップ サービス (Windows Server 内) をデプロイできます。 IaaS 上の RDS のデプロイは、RDS のデプロイに既に精通していて現行の技術的な専門知識をお持ちのお客様に最適です。 

> [!NOTE]
> このデプロイ方法を選択するには、RDS クライアント アクセス ライセンスのソフトウェア アシュアランス (SA) を含むボリューム ライセンスが必要になります。

Azure VM 上の RDS のデプロイは、デプロイ テンプレートと修正テンプレートを使用していたこれまでよりも簡単です ([概要](https://blogs.technet.microsoft.com/enterprisemobility/2015/07/13/azure-resource-manager-template-for-rds-deployment/)を参照してから[取得してください](https://aka.ms/rdautomation))。 [自動スケーリング スクリプト](https://gallery.technet.microsoft.com/scriptcenter/Automatic-Scaling-of-9b4f5e76)を使用して、Azure RemoteApp 内で (Azure のリソース モデルのリソースではなく) Azure クラシック デプロイメント モデル リソースを含む、同様のエラスティックなスケーリング機能を利用できます。ただし、カスタマイズと構成が追加で発生します。 Azure VM で RDS をデプロイする場合は、[Azure サポート](https://azure.microsoft.com/support/plans/)で Azure RemoteApp と同じサポート プロフェッショナルによるサポートが提供されています。 [Azure サポート](https://azure.microsoft.com/support/plans/)に連絡して現在の使用状況に基づいた概算コストを取得したり、間もなくリリースされるコスト計算ツールを使用して自分で計算したりできます。  また、N シリーズの VM (現在プライベート プレビュー中) を使用して、vGPU を追加できます。vGPU の追加に関する詳細とその方法については、Ignite セッションで「[Harness RDS improvements in Windows Server 2016 (Windows Server 2016 での RDS 機能強化の活用)](https://myignite.microsoft.com/videos/2794)」を参照してください。   

弊社が提供する [Windows Server 2012 R2](http://aka.ms/rdsonazure) と [Windows Server 2016](http://aka.ms/rdsonazure2016) のデプロイの詳細な手順ガイドがデプロイに役立ちます。 最新の情報については、[リモート デスクトップについてのブログ](https://blogs.technet.microsoft.com/enterprisemobility/?product=windows-server-remote-desktop-services)を参照してください。

### <a name="citrix-on-iaas"></a>**IaaS 上の Citrix**
セッション ベースの XenApp または XenDesktop のネイティブ Citrix デプロイは、オンプレミスまたはホストされた環境内 (Azure VM など) でデプロイできます。 

詳細については、デプロイの詳細な手順ガイドである [Azure 上の Citrix XA 7.6](http://www.citrixandmicrosoft.com/Documents/Citrix-Azure Deployment Guide-v.1.0.docx) を参照してください。 価格計算ツールなどの詳細については、[Azure 上の Citrix](http://www.citrixandmicrosoft.com/Solutions/AzureCloud.aspx) に関するページを参照してください。 [Citrix に問い合わせ](http://citrix.com/English/contact/index.asp)て、オプションについて相談することもできます。

## <a name="fully-managed-paassaas-offerings"></a>完全管理型 (PaaS/SaaS) サービス

### <a name="citrix-xenapp-essentials-released-april-2017"></a>Citrix XenApp Essentials (2017 年 4 月リリース)
[Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Citrix.XenAppEssentials) から現在利用できる Citrix XenApp Essentials は、新しいアプリケーション仮想化サービスであり、Microsoft Azure RemoteApp のシンプルかつ規範的で使いやすい表示機能と Citrix Cloud プラットフォームの処理能力および柔軟性を組み合わせたものです。 

既存の Azure RemoteApp のお客様は[登録して無料試用版を使用できます](https://www.citrix.com/products/citrix-cloud/form/xenapp-essentials-msft-trial/)。  注: Citrix ユーザー サービスの料金のみ無料で、Azure のコンピューティングおよびストレージ料金が適用されます

詳細情報
- [Azure RemoteApp から Citrix XenApp Essentials に移行する](remoteapp-migrate-citrix.md)
- [Citrix と Microsoft](https://www.citrix.com/global-partners/microsoft/remote-app.html)
- [Citrix XenApp Essentials のプレゼンテーション](https://www.youtube.com/watch?v=91Z7CCfQ-9k)  

### <a name="citrix-cloud-xenapp-service-and-xendesktop-service"></a>Citrix Cloud の XenApp サービスおよび XenDesktop サービス 

[Citrix Cloud の XenApp サービスおよび XenDesktop サービス](https://www.citrix.com/products/citrix-cloud/services.html)は、アプリとデスクトップの両方を配信し、高度な管理および監視機能を提供する最適なソリューションです。 

#### <a name="conexlink-platform-name-mycloudit"></a>Conexlink (プラットフォーム名: MyCloudIT)
[MyCloudIT](https://mycloudit.com) は、IT 企業が Microsoft Azure クラウドでのリモート デスクトップ、リモート アプリケーション、およびインフラストラクチャの移行と提供を簡略化、最適化、およびスケーリングするための自動化プラットフォームです。 

MyCloudIT プラットフォームを利用すると、わずかなキー入力のみで顧客の IT インフラストラクチャ全体をクラウドに移動でき、デプロイの時間が 95%、Azure のコストが 30% 削減されます。 パートナーは、1 つのグローバル ダッシュボードから顧客を管理し、サービスの対象をかつてないほど世界中のエンドユーザーに広げて、オーバーヘッドや広範な Azure のトレーニングを増やすことなく増収できるようになりました。  

> 主な拠点: ダラス (米国テキサス州)
> 
> 営業地域: 世界中
> 
> パートナー ステータス: [ゴールド](https://partnercenter.microsoft.com/pcv/solution-providers/conexlink_4298787366/843036_1?k=Conexlink)
> 
> Microsoft クラウド サービス プロバイダー: はい
> 
> セッション ベースの RemoteApp とデスクトップ ソリューションの提供: 両方を提供
> 
> Azure RemoteApp の移行ソリューション: はい ([詳細情報](https://mycloudit.com/remote-app-microsoft/))
> 
> Brian Garoutte、ビジネス開発担当副社長
> 
> 電話番号: 972-218-0741
>   
> 電子メール： [brian.garoutte@conexlink.com](mailto:brian.garoutte@conexlink.com)

### <a name="frame"></a>Frame

Frame は、クラウド上のセキュリティ保護された、ソフトウェア定義のワークスペースの作成および管理のために、エンタープライズおよび政府の IT 組織、管理されたサービス プロバイダー、主要ソフトウェア ベンダーによって選ばれています。 小規模組織から大規模組織まで、Frame を使用するとどのデバイスのどのブラウザーからでも、ユーザーはとても簡単に Windows アプリケーションにアクセスできるようになります。 Frame のプラットフォームには、Azure インフラストラクチャおよび RDS ライセンスなど、クラウドからアプリケーションをデプロイする際に管理者に必要なものがすべて含まれています (ユーザー自身の Azure アカウントとライセンスを持ち込むかどうかは任意です)。 

[Frame on Azure の詳細](https://www.fra.me/ara) 

> 主な拠点: サン マテオ (米国カリフォルニア州)
>
> 営業地域: 世界中
>
> Frame は Microsoft Partner です。
> 
> 電話番号: 1-480-269-4668

### <a name="awingu"></a>Awingu
Awingu では、レガシ アプリ、SaaS、およびドキュメントを html5 のブラウザーから実行する単純なオンライン ワークスペース ソリューションを提供します。 そのため、すべてのアプリケーションがあらゆる種類のデバイスで安全に使用できるようになります。 SaaS サービスの場合、広範なシングル サインオン オプションを使用できます。 また、多様な (クラウド) ファイル システムを自分のワークスペースに密接に統合できます。 完全なモビリティに近い機能を備えた Awingu の豊富なオンライン ワークスペースによって、きめ細かな制御 (例: ダウンロードとアップロード)、使用状況の完全な監査、Multi-Factor Authentication (例: Azure MFA)、セッションの記録などの最適なセキュリティを提供します。 Awingu は、最適化およびセキュリティ保護されたコラボレーションのためのドキュメントおよびアプリケーションのセッション共有が、最初からすぐに使用できます。
Awingu のソリューションは、マルチ テナントかつマルチ AD のオープン API です。 これは小規模企業から大規模企業、クラウド サービス プロバイダー、および [ISV](http://www.isv2saas.com) によって使用されます。 これらのお客様は、使いやすいく容易にインストールでき、TCO が低いことを特に高く評価しています。

Awingu オールインワンは [Azure Marketplace で利用可能で](https://azuremarketplace.microsoft.com/marketplace/apps/awingu.awingu-arm)、2 ユーザー分の同時ユーザーが組み込まれています。 追加のライセンスは、[さまざまなディストリビューターやリセラー](http://www.awingu.com/reseller)から入手できます。

[Azure RemoteApp に代わるサービスとしての Awingu の詳細については、こちらを参照してください](http://alternative-for-azure-remoteapp.awingu.com/)。


> 主な拠点: ベルギー
> 
> 営業地域: EMEA、北米およびブラジル
> 
> セッション ベースの RemoteApp とデスクトップ ソリューションの提供: 両方を提供 
> 
> **グローバル**:
> 
> Arnaud Marlière、CMO
> 
> 電子メール： [arnaud@awingu.com](mailto:arnaud@awingu.com)
> 
> 電話番号: +1 646 583 3025
> 
> **ベルギー HQ**:
> 
> Ottergemsesteenweg-Zuid 808 B44
> 
> 9000 Gent
> 
> 電子メール： [info@awingu.com](mailto:info@awingu.com) 
> 
> 電話番号: +32 9 296 40 11
> 
> **USA**:
> 
> 7th floor, 1177 Ave of the Americas,
> 
> New York, NY 10036
> 
> 電子メール： [info.us@awingu.com](mailto:info.us@awingu.com)

### <a name="microsoft-hosted-service-provider"></a>Microsoft ホステッド サービス プロバイダー
ホスティング パートナーは、通常、ホストされた完全管理型 Windows デスクトップおよびアプリケーション サービスを提供します。これには、Azure リソース、オペレーティング システム、アプリケーション、およびヘルプデスクの管理が含まれます。このために、パートナーは、マイクロソフトやその他のソフトウェア プロバイダーとのライセンス契約、およびサブスクライバー アクセス ライセンス (SAL) の再販を許可するサービス プロバイダー ライセンス契約を使用します。 次の情報で、Azure RemoteApp から移行する顧客のサポートに特化したホスティング パートナーに関する詳細と連絡先を入手できます。 [ホストされるサービスのプロバイダーの最新の一覧](http://aka.ms/rdsonazurecertified)で、IaaS 上の RDS のラーニング パスおよび評価に関する詳細情報を確認してください。  

### <a name="citrix-service-provider-program"></a>Citrix サービス プロバイダー プログラム
Citrix サービス プロバイダー プログラムを利用すると、サービス プロバイダーは簡便な仮想クラウド コンピューティングを SMB に提供し、必要なサービスを従量課金制モデルで簡単に提供することができます。 Citrix サービス プロバイダーは、Microsoft SPLA ビジネスを発展させ、任意のデバイス、任意の場所、広範なアプリケーション サポート、豊富な機能、追加のセキュリティおよび拡張性によって RDS プラットフォームへの投資を拡張しています。 さらに、Citrix サービス プロバイダーは顧客満足度を高め、運用コストを削減するので、多くのサブスクライバーを引き付けます。 [詳細を参照](http://www.citrix.com/products/service-providers.html)するか、[パートナーを見つけ](https://www.citrix.com/buy/partnerlocator.html)てください。

#### <a name="acuutech"></a>Acuutech
[Acuutech](http://www.acuutech.com) は、ホストされたデスクトップ ソリューションを提供し、マイクロソフトのテクノロジで構築された完全なデスクトップおよび ISV アプリケーション環境を、Azure と自社のデータ センターから世界中の顧客に提供することに特化しています。

> 主な拠点: ロンドン (英国)、シンガポール、ヒューストン (テキサス州)
> 
> 営業地域: 世界中
> 
> パートナー ステータス: ゴールド
> 
> Microsoft クラウド サービス プロバイダー: はい
> 
> セッション ベースの RemoteApp とデスクトップ ソリューションの提供: 両方を提供
> 
> Azure RemoteApp の移行ソリューション: はい ([詳細情報](http://www.acuutech.com/ara-migration/))
> 
> **英国**:
>   
> 5/6 York House, Langston Road,
>   
> Loughton, Essex IG10 3TQ
>   
> 電話番号: +44 (0) 20 8502 2155
> 
> **シンガポール**:
>   
> 100 Cecil Street, #09-02, 
>   
> The Globe, Singapore 069532
> 
> 電話番号: +65 6709 4933
>   
> **北米**:
>   
> 3601 S. Sandman St.
>   
> Suite 200, Houston, TX 77098
>   
> 電話番号: +1 713 691 0800

#### <a name="aspex"></a>ASPEX
[ASPEX](http://www.aspex.be/en) は、クラウドに移行する ISV および現在のクラウド設定を最適化を希望する ISV に特化しています。 ASPEX では、幅広い管理サービス、DevOps、およびコンサルティング サービスを提供しています。  

> 主な拠点: アントワープ (ベルギー)
> 
> 営業地域: 西ヨーロッパ
> 
> パートナー ステータス: [シルバー](https://partnercenter.microsoft.com/pcv/solution-providers/aspex_9397f5dd-ebdd-405b-b926-19a5bda61f7a/cfe00bac-ea36-4591-a60b-ec001c4c3dff)
> 
> Microsoft クラウド サービス プロバイダー: はい
> 
> セッション ベースの RemoteApp とデスクトップ ソリューションの提供: 両方を提供
> 
> Azure RemoteApp の移行ソリューション: はい ([詳細情報](https://www.aspex.be/en/azure-remote-apps))
> 
> 電話番号: +3232202198
> 
> 電子メール: [info@aspex.be](mailto:info@aspex.be)
> 
> Web: [http://cloud.aspex.be/contact-ara-0](http://cloud.aspex.be/contact-ara-0)

#### <a name="caasecom"></a>Caase.com
[Caase.com](http://www.caase.com/) は、Microsoft Cloud で働き方の改革を模索する企業や地方自治体、非政府団体、医療機関を支援します。 どこにいても、どんなデバイスからでも、IT コストを低く抑えて、高い生産性とセキュリティを実現します。 Caase.com は、Microsoft Office 365、Azure、Enterprise Mobility and Security、Windows で優れた実績を持つスペシャリスト集団です。 Caase.com は、そのコンサルタント サービスと移行サービス、導入プログラム、トレーニング、マネジメント、サポートを通じて、顧客企業の従業員やパートナー、サプライヤーのコラボレーションに最適化された安全なプラットフォームを構築します。
Caase.com は、Azure Remote Workspace (モバイル ワークプレース) と Digital Workplace (ソーシャル イントラネット) を提唱しています。 この 2 つのソリューションを足掛かりとして、ユーザーは、Microsoft Cloud への移行を快適に、効率よく、実り多い形で進めることができます。
オランダ語の翻訳と関連ムービーは、http://caase.com/over-ons/ でご覧いただけます。

> 営業地域: オランダを拠点として全世界に展開
> 
> パートナー ステータス: [ゴールド](https://partnercenter.microsoft.com/pcv/solution-providers/caasecom_4295593260/51159_3)
> 
> Microsoft クラウド サービス プロバイダー: はい
> 
> セッション ベースの RemoteApp とデスクトップ ソリューションの提供: 両方を提供
> 
> Azure RemoteApp の移行ソリューション: はい ([詳細情報](http://caase.com/diensten/microsoft-azure/))
> 
> 
> オランダ:
> 
> Rigtersbleek-Zandvoort 10 (De Spinnerij)
> 
> 7521 BE, Enschede
> 
> 電話: +31 (0) 88 4320 000


#### <a name="nerdio"></a>Nerdio
[Nerdio for Azure](http://getnerdio.com/nfa/) は、IT 環境の完全 Microsoft Cloud 化に向けて、驚くほどシンプルなプロビジョニング、管理、最適化を実現する IT オートメーション プラットフォームです。 仮想デスクトップ、リモート アプリ、サーバーを 2 ～ 3 時間で立ち上げることができます。 Nerdio Admin Portal から 3 回未満のクリックで環境を管理することができます。 インテリジェントな自動スケールによって、Azure IaaS リソースを 40 ～ 60% 節約することができます。

> 主な拠点: イリノイ州シカゴ、営業地域: 世界中、パートナー ステータス: [ゴールド](https://partnercenter.microsoft.com/en-us/pcv/solution-providers/adar-inc_341c9afa-f12c-46f5-8f7b-3f9ef59a66a5/3a7ae479-3ac2-42f6-84e2-d456dc7424e1)、Microsoft クラウド サービス プロバイダー: はい
> 
> セッション ベースの RemoteApp とデスクトップ ソリューションの提供: 両方を提供
> 
> Azure RemoteApp の移行ソリューション: はい
> 
> 
> 8001 Lincoln Ave
> 
> Suite 212
> 
> Skokie, IL 60077
> 
> USA
> 
> (844) 4NERDIO ext. 6
> 
> [sayhello@getnerdio.com](mailto:sayhello@getnerdio.com)

#### <a name="saasplaza"></a>**SaaSplaza**
[SaaSplaza](http://www.saasplaza.com/) は、完全な Microsoft Dynamics ポートフォリオ (NAV、AX、GP、SL、CRM) のプライベートおよびパブリック クラウド (Azure) を提供しています。

> 本拠地: オランダ
> 
> 営業地域: 世界中
> 
> パートナー ステータス: [ゴールド](https://partnercenter.microsoft.com/pcv/solution-providers/saasplaza_4295495801/791011_2?k=saasplaza)
> 
> Microsoft クラウド サービス プロバイダー: はい
> 
> セッション ベースの RemoteApp とデスクトップ ソリューションの提供: 両方を提供
> 
> **EMEA**: 
> 
> Prins Mauritslaan 29-35
> 
> 71 LP Badhoevedorp
> 
> The Netherlands
> 
> 電話番号: +31 20 547 8060 
> 
>  **アメリカ**: 
> 
> 171 Saxony Road, Suite 105
> 
> Encinitas, CA 92024
> 
> San Diego
> 
> 米国
> 
> 電話番号: +1 858 385 8900 
> 
> **APAC**: 
> 
> 105 Cecil Street
>    
> \#11-08, The Octagon
> 
> Singapore 069534
> 
> シンガポール
>   
> 電話番号 - シンガポール: +65 6222 6591
> 
> 電話番号 - オーストラリア: +61 2 8310 5568 
>    
> 電話番号 - ニュージーランド: +64 4 488 0321
> 
## <a name="need-more-help"></a>さらにサポートが必要な場合
選択についてサポートが必要な場合やご質問がある場合は、 次のいずれかの方法を利用してください。 

1. [arainfo@microsoft.com](mailto:arainfo@microsoft.com) に電子メールを送信する。
2. [Azure サポート](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)に問い合わせる。 まず [Azure サポート ケース](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)を開きます。
3. 電話で問い合わせる。 [地元の営業所の電話番号を調べてください](https://azure.microsoft.com/overview/sales-number/)。


