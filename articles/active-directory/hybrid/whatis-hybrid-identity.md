---
title: Active Directory を Azure Active Directory と接続する | Microsoft Docs
description: Azure AD Connect は、オンプレミスのディレクトリと Azure Active Directory を統合する機能です。 Office 365、Azure、SaaS など Azure AD と連動するアプリケーションの ID を共通化することができます。
keywords: Azure AD Connect の紹介, Azure AD Connect の概要, Azure AD Connect とは, Active Directory のインストール
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 59bd209e-30d7-4a89-ae7a-e415969825ea
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/18/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 40ac3ca92c65607df056b883608dde60d816143e
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/26/2018
ms.locfileid: "47181781"
---
# <a name="hybrid-identity-and-microsofts-identity-solutions"></a>ハイブリッド ID と Microsoft の ID ソリューション
今日、ビジネスや企業ではますます、オンプレミスのアプリケーションとクラウド アプリケーションが混在して使用されるようになっています。  これらのアプリケーション (オンプレミスとクラウド内) へのアクセスを必要とするアプリケーションおよびユーザーの存在が、困難なシナリオとなっています。

Microsoft の ID ソリューションでは、オンプレミスとクラウドを基盤とする機能を利用する際に、場所に関係なく、1 つのユーザー ID ですべてのリソースの認証と権限付与を行います。 これをハイブリッド ID と呼んでいます。

## <a name="what-is-azure-ad-connect"></a>Azure AD Connect とは

Azure AD Connect は、ハイブリッド ID の目標に適合し、それを達成するように設計された Microsoft のツールです。  Office 365、Azure、SaaS など Azure AD と連動するアプリケーションに関して、ユーザーの ID を共通化することができます。  また、以下のような特徴があります。
    
- [同期](how-to-connect-sync-whatis.md) - このコンポーネントは、ユーザー、グループ、およびその他のオブジェクトを作成する役割を果たします。 さらに、オンプレミス ユーザーやオンプレミス グループの ID 情報をクラウド側と一致させる働きをします。  これは、パスワード ハッシュを Azure AD と同期させる役割を果たします。
-   [AD FS とフェデレーション統合](how-to-connect-fed-whatis.md) - フェデレーションは Azure AD Connect のオプション部分であり、オンプレミスの AD FS インフラストラクチャを使用してハイブリッド環境を構成するために使用できます。 これはまた、証明書の更新や追加の AD FS サーバー デプロイなどの AD FS 管理機能も提供します。
-   [パススルー認証](how-to-connect-pta.md) - ユーザーがオンプレミスとクラウド内で同じパスワードを使用できるようにする別のオプション コンポーネントですが、フェデレーション環境の追加のインフラストラクチャは必要ありません。
-   [正常性の監視](whatis-hybrid-identity-health.md) - Azure AD Connect Health は、堅牢な監視を提供したり、このアクティビティを表示するための Azure Portal 内の中央の場所を提供したりできます。 


![What is Azure AD Connect](./media/whatis-hybrid-identity/arch.png)



## <a name="what-is-azure-ad-connect-health"></a>Azure AD Connect Health とは

Azure Active Directory (Azure AD) Connect Health では、オンプレミスの ID インフラストラクチャと同期サービスを監視、分析できます。 これを使用すると、Active Directory フェデレーション サービス (AD FS) サーバー、Azure AD Connect サーバー (同期エンジン)、Active Directory ドメイン コントローラーなど、お使いの主要な ID コンポーネントの監視機能が用意されているため、Office 365 と Microsoft Online Services への信頼性の高い接続を維持できます。また、これらのコンポーネントの主要なデータ ポイントにアクセスしやすくなるため、使用状況やその他の重要な分析を取得して、しかるべき情報に基づく判断ができるようになります。

この情報は、[Azure AD Connect Health ポータル](https://aka.ms/aadconnecthealth)に表示されます。 Azure AD Connect Health ポータルでは、アラート、パフォーマンスの監視、使用状況の分析、その他の情報を表示できます。 Azure AD Connect Health では、1 つのレンズで主要な ID コンポーネントの正常性をまとめて確認できます。

![What is Azure AD Connect Health](./media/whatis-hybrid-identity-health/aadconnecthealth2.png)


Azure AD Connect Health の機能の増加に伴い、ID というレンズを使用した単一のダッシュボードがポータルに用意されました。 より強固で正常な統合環境が提供され、お客様のユーザーの生産性が向上します。


## <a name="why-use-azure-ad-connect"></a>Azure AD Connect を使用する理由
オンプレミスのディレクトリと Azure AD を統合すると、クラウドとオンプレミス両方のリソースにアクセスするための共通の ID が提供されるため、ユーザーの生産性が向上します。 ユーザーや組織にとっては次の利点があります。

* ユーザーは、単一の ID を使ってオンプレミスのアプリケーションとクラウド サービス (Office 365 など) にアクセスできます。
* 単一のツールにより、同期とサインインのための容易なデプロイメントを実現できます。
* それぞれのシナリオに適した最新の機能が手に入ります。 Azure AD Connect は、DirSync や Azure AD Sync など、旧バージョンの ID 統合ツールの後継ツールです。詳細については、「 [ハイブリッド ID ディレクトリ統合ツールの比較](plan-hybrid-identity-design-considerations-tools-comparison.md)」を参照してください。

## <a name="why-use-azure-ad-connect-health"></a>Azure AD Connect Health を使用する理由
オンプレミスのディレクトリと Azure AD を統合すると、クラウドとオンプレミスの両方のリソースに共通の ID でアクセスできるため、ユーザーの生産性が向上します。 ただし、この統合には課題があります。ユーザーが任意のデバイスからオンプレミスとクラウド両方のリソースに安全にアクセスできるように、この環境の正常性を確保する必要があります。 Azure AD Connect Health では、Office 365 または他の Azure AD アプリケーションにアクセスするときに使用するオンプレミスの ID インフラストラクチャを監視して分析することができます。 オンプレミスの各 ID サーバーにエージェントをインストールするぐらいにシンプルです。

### <a name="azure-ad-connect-health-for-ad-fshow-to-connect-health-adfsmd"></a>[Azure AD Connect Health for AD FS](how-to-connect-health-adfs.md)
Azure AD Connect Health for AD FS では、Windows Server 2008 R2、Windows Server 2012、Windows Server 2012 R2、および Windows Server 2016 上の AD FS 2.0 がサポートされています。 また、エクストラネット アクセスの認証をサポートする AD FS プロキシまたは Web アプリケーション プロキシ サーバーの監視もサポートの対象に含まれます。 Health エージェントの簡単で迅速なインストールにより、Azure AD Connect Health for AD FS は一連の重要な機能を提供します。

#### <a name="key-benefits-and-best-practices"></a>主な利点とベスト プラクティス

- *強化されたセキュリティ*
  - [エクストラネットのロックアウトの傾向](how-to-connect-health-adfs.md#usage-analytics-for-ad-fs)
  - [失敗したサインインのレポート](how-to-connect-health-adfs.md#risky-ip-report-public-preview) 
  - [プライバシー準拠](reference-connect-health-user-privacy.md)    
- *すべての[重大な ADFS システムの問題](how-to-connect-health-alert-catalog.md#alerts-for-active-directory-federation-services)に関する警告を受信する*
    - サーバー構成および可用性 
    - [パフォーマンスおよび接続](how-to-connect-health-adfs.md#performance-monitoring-for-ad-fs) 
  - 定期的なメンテナンス    
- *デプロイおよび管理が容易*
  - 迅速な[エージェント インストール](how-to-connect-health-agent-install.md#installing-the-azure-ad-connect-health-agent-for-ad-fs) 
  - 最新版へのエージェントの自動アップグレード 
  - ポータル内で数分以内に使用可能なデータ    
- *豊富な[使用状況メトリック](how-to-connect-health-adfs.md#usage-analytics-for-ad-fs)* 
  - 上位のアプリケーション使用状況
  - ネットワークの場所と TCP 接続
  - サーバーごとのトークン要求    
- *優れたユーザー エクスペリエンス* 
  - Azure Portal からのダッシュボード方式
  - [電子メール経由のアラート](how-to-connect-health-adfs.md#alerts-for-ad-fs)    

#### <a name="feature-highlight"></a>機能概要

*   AD FS と AD FS プロキシ サーバーが正常に動作していない場合に通知されるアラートを使用して監視する
*   重大なアラートの電子メール通知を送信する
*   AD FS の容量計画に役立つ、パフォーマンス データの傾向を表示する
*   ピボットによる AD FS サインインの使用状況分析 (アプリ、ユーザー、ネットワークの場所など)
*   無効なユーザー名とパスワードによる試行を行った上位 50 人のユーザーと直近の IP アドレスなど、AD FS に関するレポートを作成する
*   失敗した AD FS サインインの危険な IP レポート

[AD FS での Azure AD Connect Health の使用](how-to-connect-health-adfs.md)の詳細を確認してください

### <a name="azure-ad-connect-health-for-synchow-to-connect-health-syncmd"></a>[Azure AD Connect Health for Sync](how-to-connect-health-sync.md)
Azure AD Connect Health for Sync は、オンプレミスの Active Directory と Azure AD の間で実行された同期を監視し、情報を提供します。 Azure AD Connect Health for Sync の主な機能は次のとおりです。

* Azure AD Connect サーバー (同期エンジン) が正常に動作していない場合に通知されるアラートを使用して監視する
* 重大なアラートの電子メール通知を送信する
* 同期操作の待ち時間を示すグラフと各種操作 (追加、更新、削除など) の傾向を含む、同期操作の分析を行う
* 同期プロパティ、最後に正常に行われた Azure AD へのエクスポートについての概要情報を表示する
* オブジェクトレベルの同期エラーについてレポートするのに \(Azure AD Premium は不要\)

[Azure AD Connect Health for Sync の使用](how-to-connect-health-sync.md)の詳細を確認してください

### <a name="azure-ad-connect-health-for-ad-dshow-to-connect-health-addsmd"></a>[Azure AD Connect Health for AD DS](how-to-connect-health-adds.md)
Windows Server 2008 R2、Windows Server 2012、Windows Server 2012 R2、Windows Server 2016 にインストールされているドメイン コントローラーは、Azure AD Connect Health for Active Directory Domain Services (AD DS) で監視することができます。 Health エージェントをインストールすることによって、クラウドからオンプレミスの AD DS 環境を監視できるようになります。 Azure AD Connect Health for AD DS の主な機能は次のとおりです。

* ドメイン コントローラーの異常を検出する監視アラートと重大なアラートを知らせる電子メール通知
* [ドメイン コントローラー] ダッシュボード。ドメイン コントローラーの正常性と運用状態を把握できるクイック ビューアーが用意されています
* [レプリケーションの状態] ダッシュボード。レプリケーションに関する直近の情報のほか、エラーが検出された場合はトラブルシューティング ガイドへのリンクが表示されます
* 使用頻度の高いパフォーマンス カウンターのパフォーマンス データ グラフへの迅速なアクセス。トラブルシューティングと監視を行ううえで必要不可欠です

[AD DS での Azure AD Connect Health の使用](how-to-connect-health-adds.md)の詳細を確認してください

## <a name="next-steps"></a>次の手順


- [ハードウェアおよび前提条件](how-to-connect-install-prerequisites.md) 
- [簡単設定](how-to-connect-install-express.md)
- [カスタマイズした設定](how-to-connect-install-custom.md)
- [パスワード ハッシュの同期](how-to-connect-password-hash-synchronization.md)|
- [パススルー認証](how-to-connect-pta.md)
- [Azure AD Connect とフェデレーション](how-to-connect-fed-whatis.md)
- [Azure AD Connect Health エージェントをインストールする](how-to-connect-health-agent-install.md) 
- [Azure AD Connect Sync](how-to-connect-sync-whatis.md)
- [バージョン履歴](reference-connect-version-history.md)
- [ディレクトリ統合ツールの比較](plan-hybrid-identity-design-considerations-tools-comparison.md)
- [Azure AD Connect の FAQ](reference-connect-faq.md)









