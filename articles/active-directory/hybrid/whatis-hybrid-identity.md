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
ms.date: 10/25/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 9c946c9b7d041b1d08dadc9f7bd830d4a1d658ad
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2018
ms.locfileid: "50250871"
---
# <a name="hybrid-identity-and-microsofts-identity-solutions"></a>ハイブリッド ID と Microsoft の ID ソリューション
[Microsoft Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md) ハイブリッド ID ソリューションを使用すると、オンプレミスのディレクトリ オブジェクトと Azure AD を同期したまま、オンプレミスでユーザーを管理できます。 オンプレミスの Windows Server Active Directory と Azure AD との同期を計画する際に最初に行う決断は、同期済み ID を使用するかフェデレーション済み ID を使用するかです。 

- **同期済み ID** (およびオプションでパスワード ハッシュ) を使用すると、ユーザーは、オンプレミスとクラウドベースの両方の組織リソースへのアクセスに同じパスワードを使用できます。 
- **フェデレーション ID** では、ユーザー認証を Azure から切り離し、信頼のおけるオンプレミスの ID プロバイダーに認証を委ねることで、ユーザーをより細かく制御することができます。 

ハイブリッド ID の構成にはいくつかのオプションがあります。 組織のニーズに最も合う ID モデルを検討するとき、時間、既存のインフラストラクチャ、複雑さ、コストも考慮する必要があります。 これらの要因は組織ごとに異なり、時間の経過とともに変化する場合があります。 ただし、要件が変化する場合、異なる ID モデルに柔軟に切り替えることもできます。

## <a name="synchronized-identity"></a>同期済み ID 

同期済み ID は、オンプレミスのディレクトリ オブジェクト (ユーザーおよびグループ) と Azure AD を同期する最も簡単な方法です。 

![同期済みハイブリッド ID](./media/whatis-hybrid-identity/synchronized-identity.png)

同期済み ID は最も簡単で高速なメソッドですが、ユーザーは、クラウドベースのリソース用に別のパスワードを管理する必要があります。 これを回避するには、(オプションで) Azure AD ディレクトリに[ユーザー パスワードのハッシュを同期](how-to-connect-password-hash-synchronization.md)することも可能です。 パスワード ハッシュを同期すると、ユーザーは、オンプレミスで使用しているものと同じユーザー名とパスワードを使用してクラウドベースの組織リソースにログインできます。 Azure AD Connect は、オンプレミスのディレクトリの変更を定期的にチェックし、Azure AD ディレクトリを同期された状態に保ちます。 オンプレミスの Active Directory でユーザー属性またはパスワードが変更されると、Azure AD で自動的に更新されます。 

多くの組織においては Office 365、SaaS アプリケーション、およびその他の Azure AD ベースのリソースへのユーザー サインインを可能にする必要があるのみなので、その場合は既定のパスワードの同期オプションをお勧めします。 これが役に立たない場合、パススルー認証と AD FS のいずれかを選択する必要があります。

> [!TIP]
> ユーザーのパスワードは、実際のユーザーのパスワードを表すハッシュ値の形式でオンプレミスの Windows Server Active Directory に格納されます。 ハッシュ値は、一方向の数学関数 (ハッシュ アルゴリズム) の結果として求められます。 一方向の関数の結果をパスワードのプレーンテキスト バージョンに戻す方法はありません。 パスワードのハッシュを使用してオンプレミスのネットワークにサインインすることはできません。 パスワードの同期を選択すると、Azure AD Connect は、オンプレミスの Active Directory からパスワード ハッシュを抽出し、パスワード ハッシュに追加のセキュリティ処理を適用してから Azure AD に同期します。 パスワードの同期とパスワードの書き戻しを組み合わせて使用すると、Azure AD でセルフ サービス パスワードのリセットを有効にすることもできます。 また、企業ネットワークに接続されたドメイン参加済みコンピューターのユーザーはシングル サインオン (SSO) を有効化できます。 シングル サインオンにおいて、有効になっているユーザーはユーザー名のみを入力して、クラウド リソースに安全にアクセスできます。 
>

## <a name="pass-through-authentication"></a>パススルー認証

[Azure AD のパススルー認証](how-to-connect-pta.md)は、オンプレミスの Active Directory を使用して、Azure AD ベースのサービスの単純なパスワード検証ソリューションを提供します。 組織のセキュリティとコンプライアンスのポリシーが、ハッシュされたフォームであっても、ユーザーのパスワードの送信を許可しておらず、ドメインに参加したデバイスのデスクトップの SSO をサポートする必要があるだけの場合、パススルー認証を使用して評価することをお勧めします。 パススルー認証では、DMZ でのデプロイは必要ありません。よって、AD FS と比較するとデプロイ インフラストラクチャが単純です。 この認証方法により、ユーザーが Azure AD を使用してサインインするとき、ユーザーのパスワードがオンプレミスの Active Directory に対して直接検証されます。

![パススルー認証](./media/whatis-hybrid-identity/pass-through-authentication.png)

パススルー認証では、複雑なネットワーク インフラストラクチャの必要はなく、クラウドにオンプレミス パスワードを格納する必要もありません。 パススルー認証は、シングル サインオンと組み合わせて使用すると、Azure AD やその他のクラウド サービスにサインインするときに、本当の意味で認証が統合されます。

パススルー認証は Azure AD Connect を使用して構成されます。これは、パスワードの検証要求をリッスンするシンプルなオンプレミス エージェントを使用します。 エージェントは複数のコンピューターに簡単にデプロイでき、高可用性と負荷分散を実現できます。 すべての通信は送信のみであるため、DMZ にコネクタをインストールする必要がありません。 コネクタを使用する際のサーバー コンピューターの要件は次のとおりです。

- Windows Server 2012 R2 以降
- ユーザーの検証が行われるフォレスト内のドメインに参加済みであること

## <a name="federated-identity-ad-fs"></a>フェデレーション ID (AD FS)

Office 365 やその他のクラウド サービスにユーザーがアクセスする方法をさらに制御するために、[Active Directory フェデレーション サービス (AD FS)](how-to-connect-fed-whatis.md) を使用して、シングル サイン オン (SSO) でディレクトリの同期をセットアップできます。 AD FS でユーザーのサインインのフェデレーション処理を行うと、ユーザーの資格情報を検証するオンプレミスのサーバーに認証が委任されます。 このモデルでは、オンプレミスの Active Directory の資格情報が Azure AD に渡されることはありません。

![フェデレーション ID](./media/whatis-hybrid-identity/federated-identity.png)

このサインイン方法は、ID フェデレーションとも呼ばれ、すべてのユーザーの認証がオンプレミスで制御され、管理者はより厳格なアクセス制御を実装できます。 AD FS を使用した ID フェデレーションは、最も複雑なオプションであり、オンプレミスの環境に追加のサーバーをデプロイする必要があります。 ID フェデレーションは、Active Directory と AD FS インフラストラクチャの年中無休 (1 日 24 時間、週 7 日間) のサポートを提供することも約束します。 オンプレミスのインターネット アクセス、ドメイン コントローラー、または AD FS サーバーを使用できなくなった場合にユーザーがクラウド サービスにサインインできなくなるため、この高度なサポートが必要です。

> [!TIP]
> Active Directory フェデレーション サービス (AD FS) でフェデレーションを使用する場合、AD FS インフラストラクチャで障害が発生した際のバックアップとして、オプションでパスワード同期を設定することもできます。
>

## <a name="common-scenarios-and-recommendations"></a>一般的なシナリオと推奨事項

以降では、ハイブリッド ID とアクセス管理に関する一般的なシナリオをいくつか取り上げると共に、それぞれのシナリオで最適と思われるハイブリッド ID オプションを紹介します。

|必要事項|PWS と SSO<sup>1</sup>| PTA と SSO<sup>2</sup> | AD FS<sup>3</sup>|
|-----|-----|-----|-----|
|新しいユーザー、連絡先、およびオンプレミスの Active Directory で作成したグループ アカウントを自動的にクラウドに同期|![推奨](./media/whatis-hybrid-identity/ic195031.png)| ![推奨](./media/whatis-hybrid-identity/ic195031.png) |![推奨](./media/whatis-hybrid-identity/ic195031.png)|
|テナントを Office 365 ハイブリッド シナリオにセット アップ|![推奨](./media/whatis-hybrid-identity/ic195031.png)| ![推奨](./media/whatis-hybrid-identity/ic195031.png) |![推奨](./media/whatis-hybrid-identity/ic195031.png)|
|ユーザーがオンプレミスのパスワードを使用してサインインしたりクラウド サービスにアクセスしたりすることを有効化|![推奨](./media/whatis-hybrid-identity/ic195031.png)| ![推奨](./media/whatis-hybrid-identity/ic195031.png) |![推奨](./media/whatis-hybrid-identity/ic195031.png)|
|企業の資格情報を使用したシングル サインオンの実装|![推奨](./media/whatis-hybrid-identity/ic195031.png)| ![推奨](./media/whatis-hybrid-identity/ic195031.png) |![推奨](./media/whatis-hybrid-identity/ic195031.png)|
|クラウドにパスワード ハッシュを保存しないことを確認| |![推奨](./media/whatis-hybrid-identity/ic195031.png)|![推奨](./media/whatis-hybrid-identity/ic195031.png)|
|クラウドの多要素認証ソリューションを有効化| |![推奨](./media/whatis-hybrid-identity/ic195031.png)|![推奨](./media/whatis-hybrid-identity/ic195031.png)|
|オンプレミスの Multi-factor Authentication ソリューションを有効化| | |![推奨](./media/whatis-hybrid-identity/ic195031.png)|
|ユーザー<sup>4</sup>のスマートカード認証をサポート| | |![推奨](./media/whatis-hybrid-identity/ic195031.png)|
|Office ポータル および Windows 10 デスクトップにパスワードの有効期限通知を表示| | |![推奨](./media/whatis-hybrid-identity/ic195031.png)|

> <sup>1</sup> シングル サインオンによるパスワード同期。
>
> <sup>2</sup> パススルー認証およびシングル サインオン。 
>
> <sup>3</sup> AD FS を使用したフェデレーション シングル サインオン。
>
> <sup>4</sup> AD FS は、エンタープライズ PKI と統合すると、証明書を使用したサインインが可能になります。 これらの証明書は、MDM や GPO などの信頼できるプロビジョニング チャネル、スマートカードの証明書 (PIV/CAC カードなど) または Hello for Business (証明書信頼) を経由してデプロイされるソフト証明書を使用できます。 スマートカード認証のサポートの詳細については、[このブログ](https://blogs.msdn.microsoft.com/samueld/2016/07/19/adfs-certauth-aad-o365/)を参照してください。
>

## <a name="what-is-azure-ad-connect"></a>Azure AD Connect とは

Azure AD Connect は、ハイブリッド ID の目標に適合し、それを達成するように設計された Microsoft のツールです。  Office 365、Azure、SaaS など Azure AD と連動するアプリケーションに関して、ユーザーの ID を共通化することができます。  また、以下のような特徴があります。
    
- [同期](how-to-connect-sync-whatis.md) - このコンポーネントは、ユーザー、グループ、およびその他のオブジェクトを作成する役割を果たします。 さらに、オンプレミス ユーザーやオンプレミス グループの ID 情報をクラウド側と一致させる働きをします。  これは、パスワード ハッシュを Azure AD と同期させる役割を果たします。
- [パスワード ハッシュ同期](how-to-connect-password-hash-synchronization.md) - ユーザー パスワードのハッシュを Azure AD との間で同期させることで、オンプレミスとクラウドでユーザーが同じパスワードを使用できるようにするオプション コンポーネントです。
-   [AD FS とフェデレーション統合](how-to-connect-fed-whatis.md) - フェデレーションは Azure AD Connect のオプション部分であり、オンプレミスの AD FS インフラストラクチャを使用してハイブリッド環境を構成するために使用できます。 これはまた、証明書の更新や追加の AD FS サーバー デプロイなどの AD FS 管理機能も提供します。
-   [パススルー認証](how-to-connect-pta.md) - ユーザーがオンプレミスとクラウド内で同じパスワードを使用できるようにする別のオプション コンポーネントですが、フェデレーション環境の追加のインフラストラクチャは必要ありません。
-   [PingFederate とフェデレーション統合](how-to-connect-install-custom.md#configuring-federation-with-pingfederate) - PingFederate を ID プロバイダーとして使用できるもう 1 つのフェデレーション オプション。
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









