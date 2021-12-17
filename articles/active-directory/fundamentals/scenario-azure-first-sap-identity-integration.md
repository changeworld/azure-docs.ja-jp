---
title: シナリオ - Azure Active Directory を使用して SAP プラットフォームおよびアプリケーションへのアクセスをセキュリティで保護する
description: SAP プラットフォームとアプリケーションへのアクセスをセキュリティで保護する方法に関するアーキテクトおよび IT 管理者向けのガイド
services: active-directory
author: xstof
manager: alberts
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 08/26/2021
ms.author: christoc
ms.reviewer: ''
ms.custom: ''
ms.collection: ''
ms.openlocfilehash: 2c987a951cbaf3795757ab0b57e8dcea0eb47aee
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2021
ms.locfileid: "132063872"
---
# <a name="scenario---using-azure-active-directory-to-secure-access-to-sap-platforms-and-applications"></a>シナリオ - Azure Active Directory を使用して SAP プラットフォームおよびアプリケーションへのアクセスをセキュリティで保護する

このドキュメントでは、ユーザー認証のプライマリ サービスとして Azure Active Directory を使用する場合の、SAP プラットフォームおよびアプリケーションの技術的な設計と構成に関するアドバイスを提供します。

## <a name="terminology-used-in-this-guide"></a>このガイドで使用する用語

| 省略形                                                                                                          | 説明                                                                                                                                                                                                   |
| --------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [BTP](https://www.sap.com/products/business-technology-platform.html)                                                 | SAP Business Technology Platform。 SAP のテクノロジ オファリング全体。 ここで説明する SAP テクノロジのほとんどが、BTP の一部です。 以前 SAP Cloud Platform と呼ばれていた製品は、SAP BTP の一部です。 |
| [IAS](https://help.sap.com/viewer/6d6d63354d1242d185ab4830fc04feb1/Cloud/en-US)                                       | SAP Cloud Identity サービス - Identity Authentication サービス。 SAP によって提供される、マルチテナント クラウド ID プロバイダー サービス。 IAS は、ユーザーが自分の SAP サービス インスタンスに対して認証するのに役立ちます。           |
| [IDS](https://help.sap.com/viewer/65de2977205c403bbc107264b8eccf4b/Cloud/en-US/d6a8db70bdde459f92f2837349f95090.html) | SAP ID サービス。 SAP が運用する PaaS および SaaS サービスに対して顧客やパートナーを認証するために、SAP によって使用される IAS のインスタンス。                                                                                 |
| [IPS](https://help.sap.com/viewer/f48e822d6d484fa5ade7dda78b64d9f5/Cloud/en-US/2d2685d469a54a56b886105a06ccdae6.html) | SAP Cloud Identity サービス - Identity Provisioning サービス。  IPS は、異なるストアやターゲット システム間で ID を同期するのに役立ちます。                                                           |
| [XSUAA](https://blogs.sap.com/2019/01/07/uaa-xsuaa-platform-uaa-cfuaa-what-is-it-all-about/)                          | Cloud Foundry User Account and Authentication 用の拡張サービス。  XSUAA は、SAP BTP 内のマルチテナント OAuth 承認サーバーです。                                                                   |
| [CF](https://www.cloudfoundry.org/)                                                                                   | Cloud Foundry。 Cloud Foundry は、SAP が BTP 用のマルチクラウド オファリング (AWS、Azure、GCP、Alibaba) を構築した基盤の環境です。                                                                                      |
| [Fiori](https://www.sap.com/products/fiori/develop.html)                                                              | SAP の Web ベースのユーザー エクスペリエンス (デスクトップ ベースのエクスペリエンスではありません)。                                                                                                                            |

## <a name="overview"></a>概要

SAP と Microsoft のテクノロジ スタックには、ユーザーの認証と認可のシナリオで役割を果たすサービスとコンポーネントが多数あります。 下の図に、主要なサービスを示します。

![SAP ランドスケープの概要](./media/scenario-azure-first-sap-identity-integration/sap-landscape-overview.png)

構成するシナリオの候補は多数の組み合わせが存在するため、Azure AD ID を最優先する戦略に沿った 1 つのシナリオに焦点を当てます。 以下のことを想定します。

- すべての ID を一元的に、Azure AD からのみ行いたいと考えています。
- メンテナンス作業を可能な限り減らし、Microsoft と SAP の全体にわたって認証とアプリへのアクセスを自動化したいと考えています。
- IAS で構成された BTP および SAP SaaS アプリ上にデプロイされたアプリには、IAS を使用する Azure AD の一般的なガイダンスを適用します。 BTP に当てはまる場合 (例: Azure AD グループによるロール マッピングの使用) と SAP SaaS アプリに当てはまる場合 (例: ロールベースの認可での ID プロビジョニング サービスの使用) にも、特定の推奨事項が提供されます。
- また、ユーザーは既に、Azure AD 内にプロビジョニングされ、機能するためにはユーザーのプロビジョニングが必要となるすべての SAP システムに向けてプロビジョニングされていると想定します。  それがどのように実現されたかは問いません。プロビジョニングは手動のことも、オンプレミスの Active Directory から Azure AD Connect を通して行うことも、SAP SuccessFactors のような人事システムを介して行うこともあります。 したがってこのドキュメントでは、SuccessFactors は、(既存の) ユーザーがサインオンする他のどのアプリケーションとも同等のアプリケーションであると見なされます。  SuccessFactors から Azure AD への、ユーザーの実際のプロビジョニングについては取り上げません。

これらの前提条件に基づいて、ほとんどの場合、下の図に示した製品とサービスに重点を置きます。 これらは、クラウドベースの環境での認証と認可に最も関連性が高いさまざまなコンポーネントです。

![スコープ内の SAP サービス](./media/scenario-azure-first-sap-identity-integration/sap-services-in-scope.png)

## <a name="recommendations"></a>Recommendations

### <a name="summary"></a>まとめ

- [1 - SAP Business Technology Platform と SAP SaaS アプリケーションでは SAP ID 認証サービスを通してフェデレーション認証を使用する](#1---use-federated-authentication-in-sap-business-technology-platform-and-sap-saas-applications-through-sap-identity-authentication-service)
- [2 - 認可には認証用の Azure AD と IAS/BTP を使用する](#2---use-azure-ad-for-authentication-and-iasbtp-for-authorization)
- [3 - IAS/BTP ではロール コレクションを通して認可用の Azure AD グループを使用する](#3---use-azure-ad-groups-for-authorization-through-role-collections-in-iasbtp)
- [4 - 類似した ID 要件を持つアプリケーションに対してのみ単一の BTP サブアカウントを使用する](#4---use-a-single-btp-subaccount-only-for-applications-that-have-similar-identity-requirements)
- [5 - すべてのエンド ユーザーの認証と認可に対して実稼働の IAS テナントを使用する](#5---use-the-production-ias-tenant-for-all-end-user-authentication-and-authorization)
- [6 - SAML 署名証明書のロールオーバーのためにプロセスを定義する](#6---define-a-process-for-rollover-of-saml-signing-certificates)

### <a name="1---use-federated-authentication-in-sap-business-technology-platform-and-sap-saas-applications-through-sap-identity-authentication-service"></a>1 - SAP Business Technology Platform と SAP SaaS アプリケーションでは SAP ID 認証サービスを通してフェデレーション認証を使用する

#### <a name="context"></a>Context

BTP 内のアプリケーションは、[信頼の構成](https://help.sap.com/viewer/65de2977205c403bbc107264b8eccf4b/Cloud/en-US/cb1bc8f1bd5c482e891063960d7acd78.html)を介して ID プロバイダーを使用し、BTP/XSUAA と ID プロバイダーの間で SAML 2.0 プロトコルを使用してユーザーを認証できます。  アプリケーション自体と BTP/XSUAA (このコンテキストでは重要ではありません) の間で OpenID Connect プロトコルが使用される場合であっても、SAML 2.0 だけがサポートされることに注意してください。

BTP では、SAP ID サービス (これが既定です) に向けて信頼の構成を設定できますが、権限のあるユーザー ディレクトリが Azure AD である場合、ユーザーが既存の Azure AD アカウントでサインインできるように **フェデレーション** を設定できます。

フェデレーションに加えて、BTP 内に Azure AD ユーザーが前もってプロビジョニングされるように、必要に応じて **ユーザー プロビジョニング** を設定できます。 ただし、これはネイティブでサポートされていません (Azure AD から SAP ID 認証サービスに進む場合のみ)。ネイティブ サポートが統合されているソリューションは BTP Identity Provisioning サービスです。 ユーザー アカウントをあらかじめプロビジョニングすると、認可目的に便利な場合があります (例: ロールにユーザーを追加する)。 ただし要件によっては、Azure AD グループ (下記を参照) を使用してこれを実現することもできます。この場合、ユーザー プロビジョニングをまったく必要としないこともあります。

フェデレーション関係の設定時には、複数のオプションがあります。

- BTP/XSUAA から直接 Azure AD に向けてフェデレーションを行うことを選択できます。
- IAS とのフェデレーションを行うことを選択できます。IAS は次に、企業の ID プロバイダー ("SAML プロキシ" とも呼ばれます) としての Azure AD とフェデレーションするように設定されます。

SAP SaaS アプリケーションの場合、IAS がプロビジョニングされて、エンド ユーザーが容易にオンボードできるよう事前に構成されます。  (この例には、SuccessFactors、Marketing Cloud、Cloud4Customer、Sales Cloud などが含まれています。) IAS はターゲット アプリに直接接続され、XSUAA にプロキシされないので、このシナリオの複雑さは低下しています。 いずれにしても、この設定には、一般に、IAS を使用する Azure AD の場合と同じ規則が適用されます。

#### <a name="what-are-we-recommending"></a>推奨されていること

権限のあるユーザー ディレクトリが Azure AD である場合、BTP で IAS に対する信頼の構成を設定することをお勧めします。  IAS は次に、企業 ID プロバイダーとしての Azure AD とフェデレーションするように設定されます。

![SAP での信頼の構成](./media/scenario-azure-first-sap-identity-integration/sap-trust-configuration.png)

BTP での信頼の構成については、[ログオン時にシャドウ ユーザーを作成する] を有効にすることをお勧めします。  このようにすると、まだ BTP 内に作成されていないユーザーは、IAS/Azure AD を介して初めてサインインするときにアカウントを自動的に取得します。 この設定が無効にされると、事前にプロビジョニングされたユーザーのみがサインインを許可されます。

#### <a name="why-this-recommendation"></a>これが推奨される理由

フェデレーションを使用する場合は、BTP のサブアカウント レベルで信頼の構成を定義することを選択できます。 その場合、使おうとしている他のサブアカウントごとに構成を繰り返す必要があります。 IAS を中間の信頼の構成として使用することで、複数のサブアカウント間にわたり、一元化された構成を利用できます。また、[リスクベースの認証](https://help.sap.com/viewer/6d6d63354d1242d185ab4830fc04feb1/Cloud/en-US/bc52fbf3d59447bbb6aa22f80d8b6056.html)や、一元的な[アサーション属性のエンリッチメント](https://help.sap.com/viewer/6d6d63354d1242d185ab4830fc04feb1/Cloud/en-US/7124201682434efb946e1046fde06afe.html)などの IAS 機能を使用できます。 ユーザー エクスペリエンスを保護するため、これらの高度なセキュリティ機能の適用は、1 つの場所でのみ行う必要があります。 これは、IAS であるか、権限のある単一のユーザー ストアとして Azure AD を保持する場合 (このホワイト ペーパーでの前提と同様) は Azure AD の[条件付きアクセス管理](../conditional-access/overview.md)によって一元的に処理されるかのいずれかです。

注: IAS からは、そのサブアカウント内で 1 つ以上のアプリケーションをデプロイできる場合でも、すべてのサブアカウントが "アプリケーション" だと見なされます。  IAS 内では、そのようなアプリケーションすべてを、企業の同じ ID プロバイダー (この場合は Azure AD) とのフェデレーションに向けて設定できます。

#### <a name="summary-of-implementation"></a>実装についてのまとめ

Azure AD 内:

- オプションで、[シームレスなシングル サインオンのために Azure AD を構成](../hybrid/how-to-connect-sso.md)します (シームレス SSO)。これによってユーザーは、企業ネットワークに接続される企業デバイスを使用するときに自動的にサインインされます。 この機能を有効にすると、ユーザーは Azure AD にサインインするためにパスワードを入力する必要がなくなります。また、通常はユーザー名の入力も不要です。

Azure AD および IAS 内:

- ドキュメントに従って、Azure AD をフェデレーション (プロキシ) モードで IAS に接続します ([SAP のドキュメント](https://developers.sap.com/tutorials/cp-ias-azure-ad.html)、[Microsoft のドキュメント](../saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial.md))。 UPN はメール アドレスであるとは限らないので、Azure AD での SSO 構成に関しては、`NameID` の設定に注意してください。
- [[条件付き認証]](https://help.sap.com/viewer/6d6d63354d1242d185ab4830fc04feb1/Cloud/en-US/0143dce88a604533ab5ab17e639fec09.html) ページに移動し、[既定の認証 ID プロバイダー] を、Azure AD ディレクトリを表している企業 ID プロバイダーに設定して、"バンドルされたアプリケーション" が Azure AD を使用するように構成します。

BTP 内:

- IAS に向けた信頼の構成を設定し ([SAP ドキュメント](https://help.sap.com/viewer/65de2977205c403bbc107264b8eccf4b/Cloud/en-US/7c6aa87459764b179aeccadccd4f91f3.html#loio7c6aa87459764b179aeccadccd4f91f3))、[[ユーザー ログオンで使用可能]](https://help.sap.com/viewer/65de2977205c403bbc107264b8eccf4b/LATEST/en-US/affb201b1a36497996c2144c28683aed.html) と [ログオン時にシャドウ ユーザーを作成する] の両方が有効になっているようにします。
- 必要に応じて、ユーザーが常に Azure AD を介して認証され、ID プロバイダーを選択する画面が表示されないように、既定の [SAP ID サービス] の信頼構成で [ユーザー ログオンに使用できる] を無効にします。

### <a name="2---use-azure-ad-for-authentication-and-iasbtp-for-authorization"></a>2 - 認可には認証用の Azure AD と IAS/BTP を使用する

#### <a name="context"></a>Context

Azure AD に向けたフェデレーションを介して BTP と IAS がユーザー **認証** のために構成されているときには、**認可** を構成するためのオプションが複数あります。

- Azure AD では、Azure AD 内の SAP IAS インスタンスを表しているエンタープライズ アプリケーションに、Azure AD のユーザーとグループを割り当てることができます。
- IAS では、リスクベースの認証を使用して BTP 内のアプリケーションへのアクセスを防止することによって、サインインを許可またはブロックできます。
- BTP では、ロール コレクションを使用して、アプリケーションにアクセスしてユーザーとグループを定義し、特定のロールを取得できます。

#### <a name="what-are-we-recommending"></a>推奨されていること

Azure AD 自体には認可を直接含めずに、Azure AD で、エンタープライズ アプリケーションに対して [[ユーザーの割り当てが必要]](../manage-apps/assign-user-or-group-access-portal.md) を明示的にオフにすることをお勧めします。 SAML アプリケーションの場合、この設定は既定で有効になっているため、明示的なアクションを実行して無効にする必要があることに注意してください。

#### <a name="why-this-recommendation"></a>これが推奨される理由

アプリケーションが IAS を通じてフェデレーションされている場合、Azure AD の観点から見ると、ユーザーは本質的に、サインインのフロー中に "IAS に対して認証している" ことになります。  これは、Azure AD には、ユーザーがサインインしようとしている最終的な BTP アプリケーションに関する情報がないことを意味します。 それは、Azure AD での認可は、非常に大まかな認可を行うためにのみ使用できることも意味します。たとえば、ユーザーがサインインを許可する BTP のアプリケーションを "*すべて*" にしたり、"*なし*" にしたりすることができます。 ここでは、BTP のサブアカウント レベルでアプリと認証メカニズムを分離する SAP の戦略も重要視されています。

これは、[ユーザーの割り当てが必要] を使用する有効な理由となる可能性がありますが、2 つの異なる場所である、Azure AD 内でエンタープライズ アプリケーションに対して (*すべて* の BTP アプリケーションに適用されます) と、それぞれの BTP サブアカウントの両方で、認可情報を管理する必要が生じる可能性もあることを意味します。 これは、認可の設定が 1 つの場所で更新されても他の場所では更新されないという、混乱や構成ミスにつながる可能性があります。 たとえば、ユーザーが BTP で許可されても Azure AD でアプリケーションに割り当てられていないと、認証に失敗する結果となります。

#### <a name="summary-of-implementation"></a>実装についてのまとめ

IAS とのフェデレーション関係を表している Azure AD エンタープライズ アプリケーションで、[[ユーザーの割り当てが必要]](../manage-apps/assign-user-or-group-access-portal.md) を無効にします。 これは、[Microsoft Docs で詳しく説明されているようなユーザーの割り当て](../saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial.md#assign-the-azure-ad-test-user)を安全にスキップできることも意味します。

### <a name="3---use-azure-ad-groups-for-authorization-through-role-collections-in-iasbtp"></a>3 - IAS/BTP ではロール コレクションを通して認可用の Azure AD グループを使用する

#### <a name="context"></a>Context

BTP アプリケーションの認可を構成するときには複数のオプションがあります。

- アプリケーション自体の内部で、サインインしたユーザーに基づいてきめ細かなアクセス制御を構成できます。
- ユーザーやグループの割り当てに基づいて、BTP でのロールとロール コレクションを通してアクセスを指定できます。

最終的な実装では、両方の戦略を組み合わせて使用できます。 ただし、ロール コレクションを通した割り当ての場合、それをユーザーごとに実行することも、構成済みの ID プロバイダーのグループを使用することもできます。

#### <a name="what-are-we-recommending"></a>推奨されていること

きめ細かな認可を行うための権限のあるソースとして Azure AD を使おうとしている場合は、Azure AD のグループを使用して、それらを BTP のロール コレクションに割り当てることをお勧めします。 ユーザーに特定のアプリケーションへのアクセスを付与することは、単に、IAS/BTP で必要とされる構成をさらに行うことなく、関連する Azure AD グループにユーザーを追加することを意味しています。

この構成を使用する場合は、Azure AD グループのグループ ID (オブジェクト ID) を、表示名 ("sAMAccountName") ではなく、グループの一意の識別子として使用することをお勧めします。 これは、グループ ID を、Azure AD によって発行される SAML トークン内で "Groups" アサーションとして使用する必要があることを意味します。  さらに、グループ ID は、BTP でのロール コレクションへの割り当てに使用されます。

![SAP でのロール コレクションの使用](./media/scenario-azure-first-sap-identity-integration/sap-use-role-collections.png)

#### <a name="why-this-recommendation"></a>これが推奨される理由

"*ユーザー*" を BTP のロール コレクションに直接割り当てる場合、Azure AD で認可の決定を一元化することにはなりません。 これは、ユーザーが、BTP のロール コレクションに割り当てられる前に、既に IAS 内に存在している必要があることも意味します。また、ユーザーのプロビジョニングではなくフェデレーションをお勧めしているため、ユーザー割り当てを行う時点では IAS 内にユーザーのシャドウ アカウントがまだ存在しない可能性があります。 Azure AD グループを使用して、それらをロール コレクションに割り当てると、これらの問題は解消されます。

ロール コレクションにグループを割り当てることは、"*認可*" のために Azure AD を使用しないという前出の推奨事項と矛盾するように思えるかも知れません。 ただしこの場合でも、認可の決定は引き続き BTP で行われています。決定が、Azure AD で管理されているグループ メンバーシップに基づいて行われるようになっただけです。

グループ ID は、グローバルに一意で変更できず、後で別のグループに再利用することはできないため、その名前ではなく Azure AD グループのグループ ID を使用することをお勧めします。グループ名を使用すると、名前が変更されたときに問題につながる可能性があります。グループが削除されたり、同じ名前で、アプリケーションにアクセスできてはいけないユーザーが含まれる別のグループが作成されたりするという、セキュリティ上のリスクがあります。

#### <a name="summary-of-implementation"></a>実装についてのまとめ

Azure AD 内:

- BTP 内のアプリケーションにアクセスする必要のあるユーザーを追加できるグループを作成します (例: BTP 内のロール コレクションごとに Azure AD グループを作成する)。
- IAS とのフェデレーション関係を表している Azure AD エンタープライズ アプリケーションで、SAML のユーザー属性と要求を構成して、[セキュリティ グループ用のグループ要求を追加](../hybrid/how-to-connect-fed-group-claims.md#add-group-claims-to-tokens-for-saml-applications-using-sso-configuration)します。
    - [ソース] の属性を [グループ ID] に、[名前] を `Groups` に設定します (このように大文字の "G" で正確に書きます)。
    - さらに、要求ペイロードを小さく維持し、SAML アサーションでのグループ要求数が Azure AD によって 150 に制限されないように、要求で返されるグループを、明示的に割り当てたグループのみに制限することを強くお勧めします。  
        - [クレームで返される必要があるのは、ユーザーに関連付けられているどのグループですか?] で、 [アプリケーションに割り当てられているグループ] を選択します。  次に、要求として含めるグループについては、[ユーザーとグループ] セクションを使用して [ユーザー/グループの追加] を選択し、エンタープライズ アプリケーションにグループを割り当てます。

        ![Azure AD グループ要求の構成](./media/scenario-azure-first-sap-identity-integration/sap-aad-group-claim-configuration.png)

IAS 内:

- [企業 ID プロバイダー] の構成で、[ID フェデレーション] オプションの [[Identity Authentication ユーザー ストアを使用する]](https://help.sap.com/viewer/6d6d63354d1242d185ab4830fc04feb1/LATEST/en-US/c029bbbaefbf4350af15115396ba14e2.html) が無効になっていることを確認します。そうなっていないと、Azure AD からのグループ情報が BTP に向けた SAML トークン内に保持されず、認可が失敗します。

BTP 内:

- そのサブアカウント内のアプリケーションによって使用されるロール コレクションで、IAS の ID プロバイダーの構成を追加し、[名前] を Azure AD グループのグループ ID (オブジェクト ID) に設定して、[ロール コレクションをユーザー グループにマップ](https://help.sap.com/viewer/65de2977205c403bbc107264b8eccf4b/Cloud/en-US/51acfc82c0c54db59de0a528f343902c.html)します。

### <a name="4---use-a-single-btp-subaccount-only-for-applications-that-have-similar-identity-requirements"></a>4 - 類似した ID 要件を持つアプリケーションに対してのみ単一の BTP サブアカウントを使用する

#### <a name="context"></a>Context

BTP 内では、各サブアカウントに複数のアプリケーションを含めることができます。 ただし、IAS の観点から見ると、"バンドルされたアプリケーション" は完全な BTP サブアカウントであり、その中のより小さな単位のアプリケーションではありません。 これは、信頼設定、認証、アクセス構成、IAS でのブランド化とレイアウトのオプションはすべて、そのサブアカウント内のすべてのアプリケーションに適用されることを意味します。 同様に、BTP での信頼の構成とロール コレクションはすべて、そのサブアカウント内のすべてのアプリケーションにも適用されます。

#### <a name="what-are-we-recommending"></a>推奨されていること

複数のアプリケーションを 1 つの BTP サブアカウント内で組み合わせることは、ID レベル (ユーザー、グループ、ID プロバイダー、ロール、信頼の構成、ブランド化など) で同様の要件がある場合のみとすることをお勧めします。

#### <a name="why-this-recommendation"></a>これが推奨される理由

BTP の 1 つのサブアカウント内に、ID 要件が大きく異なる複数のアプリケーションを組み合わせると、安全でない構成ができてしまったり、構成ミスが発生しやすくなる可能性があります。  たとえば、BTP 内の 1 つのアプリケーションのために、ID プロバイダーのような共有リソースに対して構成変更を加えると、この影響は、この共有リソースに依存しているすべてのアプリケーションに及びます。

#### <a name="summary-of-implementation"></a>実装についてのまとめ

BTP 内の複数のサブアカウントにわたって複数のアプリケーションをグループ化する方法については、慎重に検討してください。 詳細については、[SAP のアカウント モデルに関するドキュメント](https://help.sap.com/viewer/65de2977205c403bbc107264b8eccf4b/Cloud/en-US/8ed4a705efa0431b910056c0acdbf377.html)を参照してください。

### <a name="5---use-the-production-ias-tenant-for-all-end-user-authentication-and-authorization"></a>5 - すべてのエンド ユーザーの認証と認可に対して実稼働の IAS テナントを使用する

#### <a name="context"></a>Context

IAS を使用する場合は、一般に、実稼働と Dev/Test 用のテナントが存在しています。 BTP のさまざまなサブアカウントやアプリケーションのために、使用する ID プロバイダー (IAS テナント) を選択できます。

#### <a name="what-are-we-recommending"></a>推奨されていること

エンド ユーザーとのいかなる対話についても、常に実稼働の IAS テナントを使用することをお勧めします。サインインする必要がある "*アプリケーション*" の開発/テスト用のバージョンや環境のコンテキストにおいても同様です。

その他の IAS テナントは、ID 関連の構成のテストにのみ使用することをお勧めします。これは、実稼働のテナントから切り離して実施する必要があります。

#### <a name="why-this-recommendation"></a>これが推奨される理由

IAS は、Azure AD とフェデレーションするように設定された、一元化されたコンポーネントであるため、1 つの場所だけでフェデレーションと ID の構成を設定し、管理する必要があります。 これを他の IAS テナントで重複して行うと、エンド ユーザー アクセスに関して、環境間で構成の誤りや不整合につながる可能性があります。

### <a name="6---define-a-process-for-rollover-of-saml-signing-certificates"></a>6 - SAML 署名証明書のロールオーバーのためにプロセスを定義する

#### <a name="context"></a>Context

Azure AD と IAS の間や、IAS と BTP の間にフェデレーションを構成すると、両者間で送信される SAML トークンの暗号化と暗号署名に使用される X.509 証明書が含まれている SAML メタデータが交換されます。 これらの証明書には有効期限があり、(たとえば証明書が侵害された場合の緊急時であっても) 定期的に更新する必要があります。

注: SAML アサーションに署名するために使用される初期 Azure AD 証明書の既定の有効期間は 3 年です (Azure AD のグローバル証明書によって署名される OpenID Connect や OAuth 2.0 のトークンとは異なり、証明書はエンタープライズ アプリケーションに固有であることに注意してください)。 [有効期限が異なる新しい証明書を生成する](../manage-apps/manage-certificates-for-federated-single-sign-on.md#customize-the-expiration-date-for-your-federation-certificate-and-roll-it-over-to-a-new-certificate)か、独自の証明書を作成してインポートするかを選択できます。

証明書は、有効期限が切れると使用できなくなるので、新しい証明書を構成する必要があります。 したがって、SAML トークンに署名するために使用されようとしている実際の証明書によって、証明書利用者 (これが署名を検証する必要があります) 内の証明書の構成が最新の状態に保たれるように、プロセスを確立する必要があります。

場合によっては、証明書利用者はこれを、最新のメタデータ情報を動的に返すメタデータ エンドポイント、つまり、通常は一般にアクセスできる URL を提供することによって自動的に実行できます。証明書利用者は、そこから定期的にメタデータを取得し、内部の構成ストアを更新できます。

ただし、IAS 利用時には、メタデータ XML ファイルのインポートを通して設定する企業 ID プロバイダーのみが許可されます。Azure AD メタデータを動的に取得するためのメタデータ エンドポイント (`https://login.microsoftonline.com/my-azuread-tenant/federationmetadata/2007-06/federationmetadata.xml?appid=my-app-id` など) を提供することはサポートされていません。 同様に、BTP 利用時には、IAS メタデータ エンドポイント (`https://my-ias-tenant.accounts.ondemand.com/saml2/metadata` など) から新しい信頼の構成を設定することはできません。メタデータ XML ファイルの 1 回限りのアップロードも必要です。

#### <a name="what-are-we-recommending"></a>推奨されていること

任意の 2 つのシステム間 (Azure AD と IAS、IAS と BTP など) で ID フェデレーションを設定する場合、使用される証明書の有効期限を確実に取得するようにします。 これらの証明書の置き換えが可能なことを事前に確認し、これらの証明書に依存するすべての証明書利用者に、新しいメタデータを更新するための文書化されたプロセスが存在することを確認します。

前に説明したように、BTP で IAS に向けた信頼の構成を設定することをお勧めします。IAS は次に、企業 ID プロバイダーとしての Azure AD とフェデレーションするように設定されます。 この場合、以下の証明書が重要です (これらは SAML の署名と暗号化に使用されます)。

- BTP でのサブアカウント証明書: これが変更されたら、IAS でのアプリケーションの SAML 2.0 構成を更新する必要があります。
- IAS でのテナント証明書: これが変更されたら、Azure AD でのエンタープライズ アプリケーションの SAML 2.0 構成と、BTP での信頼の構成の両方を更新する必要があります。
- Azure AD でのエンタープライズ アプリケーション証明書: これが変更されたら、IAS での企業 ID プロバイダーの SAML 2.0 構成を更新する必要があります。

![SAML 署名証明書のロールオーバー](./media/scenario-azure-first-sap-identity-integration/sap-rollover-saml-signing-certs.png)

SAP Cloud Platform 統合を使用したクライアント証明書通知の実装例は、[ここ](https://blogs.sap.com/2017/12/06/sap-cloud-platform-integration-automated-notification-of-keystore-entries-reaching-expiry/)と[ここ](https://blogs.sap.com/2019/03/01/sap-cloud-platform-integration-automated-notification-for-client-certificates-reaching-expiry/)に SAP によって用意されています。 これを、Azure Integration Services または PowerAutomate を使用して適合させることができる場合があります。 ただしこれらを、サーバー証明書で機能するように適合させる必要があります。 そのようなアプローチでは、カスタム実装が必要になります。

#### <a name="why-this-recommendation"></a>これが推奨される理由

証明書の有効期限が切れるままになった場合や、それらが期間内に置き換えられても、それらに依存する証明書利用者が新しい証明書情報によって更新されていない場合、ユーザーはフェデレーション介してどのアプリケーションにもサインインできなくなります。 これは、メタデータの再構成によってサービスを復元する間、すべてのユーザーに膨大なダウンタイムが生じることを意味します。

#### <a name="summary-of-implementation"></a>実装についてのまとめ

Azure AD で、[証明書の有効期限に関するメール通知アドレスを追加](../manage-apps/manage-certificates-for-federated-single-sign-on.md#add-email-notification-addresses-for-certificate-expiration)し、1 人の個人 (証明書の有効期限が切れようとしている時までに、もはやアカウントを持っていない可能性さえあります) に送信されないように、そのアドレスをグループのメールボックスに設定します。 既定では、エンタープライズ アプリケーションを作成したユーザーのみが通知を受信します。

証明書のロールオーバー プロセス全体を実行するように自動化を構築することを検討してください。  たとえば、有効期限が切れる証明書を定期的にチェックし、すべての証明書利用者を新しいメタデータで更新している間に、それらを置き換えることができます。
