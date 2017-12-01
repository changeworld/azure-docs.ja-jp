---
title: "Azure Access Control Service (ACS) からの移行"
description: "Azure Access Control Service からアプリとサービスを移動するためのオプション | Microsoft Azure"
services: active-directory
documentationcenter: dev-center-name
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/14/2017
ms.author: dastrock
ms.openlocfilehash: e32cac7feda929a63c4a80fc0078b221117eb2b5
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/18/2017
---
# <a name="migrating-from-azure-access-control-service-acs"></a>Azure Access Control Service (ACS) からの移行

Microsoft Azure Active Directory Access Control (Access Control Service または ACS とも呼ばれます) は 2018 年 11 月に廃止されます。  現在 ACS を使用するアプリケーションとサービスは、この日付より前に他の認証メカニズムに完全に移行する必要があります。 このドキュメントでは、現在の ACS 顧客が ACS の使用の廃止を計画する際の推奨事項を紹介します。 現在 ACS を使用していない場合は、特に対応は必要ありません。


## <a name="brief-acs-overview"></a>ACS の簡単な概要

クラウド認証サービスである ACS では、コードから認証および承認の多数の機能を除外しつつ、Web アプリケーションおよびサービスにアクセスするユーザーの認証および権限承認を簡単に行えるようになります。 主な対象ユーザーは、.NET クライアント、ASP.NET Web アプリケーションおよび WCF Web サービスの開発者と設計者です。

ACS のユース ケースは、主に次の 3 つのカテゴリに分けることができます。

- 特定の Microsoft クラウド サービス (Azure Service Bus、Dynamics CRM など) に対する認証 クライアント アプリケーションは、さまざまなアクションを実行するためにこれらのサービスに対する認証に使用できる ACS からトークンを取得できます。
- Web アプリケーション (カスタム ビルドまたは事前にパッケージされたアプリケーション (SharePoint など) のいずれも) への認証の追加 ACS の "パッシブ" 認証を使用して、Web アプリケーションは Google、Facebook、Yahoo、Microsoft アカウント (Live ID)、Azure Active Directory、および ADFS のアカウントでのログインに対応します。
- ACS によって発行されるトークンを使用したカスタム ビルド Web サービスのセキュリティ保護。 "アクティブな" 認証を使用することで、ACS を使用して認証された既知のクライアントからのみのアクセスが Web サービスで許可されるようになります。

これらの各ユース ケースと推奨される移行方法について、以下のセクションで説明します。 ほとんどの場合、既存のアプリやサービスを新しいテクノロジに移行するには大幅なコード変更が必要になります。 停止またはダウンタイムの可能性を防ぐために、移行の計画や実行を速やかに開始することをお勧めします。

> [!WARNING]
> ほとんどの場合、既存のアプリやサービスを新しいテクノロジに移行するには大幅なコード変更が必要になります。 停止またはダウンタイムの可能性を防ぐために、移行の計画や実行を速やかに開始することをお勧めします。

アーキテクチャ上、ACS 全体は、次のコンポーネントで構成されています。

- セキュリティ トークン サービス (STS)。認証要求を受け取って、セキュリティ トークンを発行します。
- 従来の Azure Portal。ACS 名前空間の作成、削除、および 有効化/無効化に使用されます。
- 個別の ACS 管理ポータル。ACS 名前空間の動作のカスタマイズと構成に使用されます。
- 管理サービス。ポータルの機能を自動化するために使用できます。
- トークン変換ルール エンジン。出力形式を操作するための複雑なロジックを定義するために使用できるトークン変換ルール エンジン。ACS によって発行されるトークンの出力形式を操作する複雑なロジックを定義するために使用できます。

これらのコンポーネントを使用するには、1 つ以上の ACS **名前空間**を作成する必要があります。 名前空間は、アプリケーションとサービスが通信に使用する ACS の専用インスタンスで、独自の名前空間を使用する他のすべての ACS 顧客から分離されます。 ACS の名前空間には、次のような専用の URL があります。

```HTTP
https://mynamespace.accesscontrol.windows.net
```

STS および管理操作とのすべての通信はこの URL で行われます (目的別に異なるパスを使用)。 アプリケーションまたはサービスで ACS を使用するかどうかを決めるには、`https://{namespace}.accesscontrol.windows.net` に対するトラフィックを監視します。  この URL へのトラフィックは ACS によって処理されるトラフィックであるため、停止する必要があります。  1 つの例外として、`https://accounts.accesscontrol.windows.net` へのトラフィックがあります。この URL へのトラフィックは既に他のサービスによって対処されており、ACS の廃止の影響を受けません。  また、従来の Azure Portal にログインし、所有するサブスクリプションに ACS 名前空間がないか確認する必要があります。  ACS 名前空間は、**[Active Directory]** サービスに一覧表示されます (**[Access Control 名前空間]** タブ)。

ACS について詳しくは、[MSDN のアーカイブされたドキュメント](https://msdn.microsoft.com/library/hh147631.aspx)をご覧ください。

## <a name="retirement-schedule"></a>提供終了のスケジュール

2017 年 11 月の時点では、すべての ACS コンポーネントは完全にサポートされており、使用可能です。 唯一の制限は、[新しい ACS 名前空間を従来の Azure Portal を通じて作成できない](https://azure.microsoft.com/blog/acs-access-control-service-namespace-creation-restriction/)ことです。

これらのコンポーネントの廃止スケジュールのタイムラインは、次のとおりです。

- **2017 年 11 月**: 従来の Azure Portal での Azure AD 管理エクスペリエンスの[提供は終了します](https://blogs.technet.microsoft.com/enterprisemobility/2017/09/18/marching-into-the-future-of-the-azure-ad-admin-experience-retiring-the-azure-classic-portal/)。 この時点で、ACS の名前空間の管理は、次の新しい専用の URL で利用できるようになります: `http://manage.windowsazure.com?restoreClassic=true`。 ここでは、既存の名前空間を表示、有効化/無効化、必要に応じて全体を削除できます。
- **2018 年 4 月**: ACS 名前空間の管理をこの専用の URL で利用できなくなります。 この時点では、ACS 名前空間を有効化/無効化、削除、または列挙することはできません。 ただし、ACS 管理ポータルは完全に機能し、`https://{namespace}.accesscontrol.windows.net` にあります。 ACS の他のすべてのコンポーネントも引き続き正常に動作します。
- **2018 年 11 月**: すべての ACS コンポーネントが完全にシャットダウンされます。 これには、ACS 管理ポータル、管理サービス、STS、およびトークン変換ルール エンジンが含まれます。 この時点では、(`{namespace}.accesscontrol.windows.net` にある) ACS に送信されるすべての要求は失敗します。 この期間までには、すべての既存のアプリケーションとサービスを他のテクノロジに移行している必要があります。


## <a name="migration-strategies"></a>移行方法

次のセクションでは、ACS から他の Microsoft テクノロジに移行する際の推奨事項の概要を示します。

### <a name="clients-of-microsoft-cloud-services"></a>Microsoft クラウド サービスのクライアント

ACS によって発行されるトークンを受け入れる各 Microsoft クラウド サービスで、少なくとも 1 つの認証の代替形式がサポートされるようになりました。 適切な認証メカニズムはサービスによって異なるため、公式のガイダンスについては、各サービスのドキュメントを参照することをお勧めします。 ここでは簡単に、各ドキュメントのセットを示します。

| サービス | ガイダンス |
| ------- | -------- |
| Azure Service Bus | [Shared Access Signatures への移行](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-migrate-acs-sas) |
| Azure Relay | [Shared Access Signatures への移行](https://docs.microsoft.com/azure/service-bus-relay/relay-migrate-acs-sas) |
| Azure Cache | [Azure Redis Cache への移行](https://docs.microsoft.com/azure/redis-cache/cache-faq#which-azure-cache-offering-is-right-for-me) |
| Azure Data Market | [Cognitive Services APIs への移行](https://docs.microsoft.com/azure/machine-learning/studio/datamarket-deprecation) |
| BizTalk Services | [Azure Logic Apps への移行](https://docs.microsoft.com/azure/machine-learning/studio/datamarket-deprecation) |
| Azure Media Services | [Azure AD 認証への移行](https://azure.microsoft.com/blog/azure-media-service-aad-auth-and-acs-deprecation/) |
| Azure Backup | [Azure Backup エージェントのアップグレード](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq) |

<!-- Dynamics CRM: Migrate to new SDK, Dynamics team handling privately -->
<!-- Azure RemoteApp deprecated in favor of Citrix: http://www.zdnet.com/article/microsoft-to-drop-azure-remoteapp-in-favor-of-citrix-remoting-technologies/ -->
<!-- Exchange push notifications are moving, customers don't need to move -->
<!-- Retail federation services are moving, customers don't need to move -->
<!-- Azure StorSimple: TODO -->
<!-- Azure SiteRecovery: TODO -->


### <a name="web-applications-using-passive-authentication"></a>パッシブ認証を使用する Web アプリケーション

ユーザー認証に ACS を使用する Web アプリケーション用に、ACS では Web アプリケーションの開発者および設計者向けの次の機能を提供しています。

- Windows Identity Foundation (WIF) との緊密な統合
- Google、Facebook、Yahoo、Microsoft アカウント (Live ID)、Azure Active Directory、ADFS とのフェデレーション
- 次の認証プロトコルのサポート: OAuth 2.0 Draft 13、WS-Trust、WS-Federation。
- 次のトークン形式のサポート: JSON Web トークン (JWT)、SAML 1.1、SAML 2.0、Simple Web Token (SWT)
- ホーム領域検出のエクスペリエンス (WIF に統合)。サインインに使用するアカウントの種類を選択できました。 このエクスペリエンスは Web アプリケーションによってホストされ、完全にカスタマイズ可能でした。
- トークン変換。Web アプリケーションが ACS から受け取った要求の豊富なカスタマイズが可能でした。これには次のようなものがあります。
    - ID プロバイダーからの要求をパススルー
    - その他のカスタム要求の追加
    - 特定の条件下で要求を発行する単純な if-then ロジック

残念ながら、これらに匹敵するすべての機能を提供する単一のサービスはありません。  必要な ACS の機能を評価して、[**Azure Active Directory**](https://azure.microsoft.com/develop/identity/signin/) (Azure AD)、[**Azure AD B2C**](https://azure.microsoft.com/services/active-directory-b2c/)、または他のクラウド認証サービスのいずれを使用するかを検討する必要があります。

#### <a name="migrating-to-azure-active-directory"></a>Azure Active Directory への移行

候補となる経路として、アプリケーションとサービスの Azure Active Directory との直接統合があります。 Azure AD は、Microsoft の職場または 学校のアカウントのクラウド ベースの ID プロバイダーで、Office 365、Azure などのプロバイダーです。 これは、ACS と同様のフェデレーション認証機能を提供しますが、一部の ACS の機能をサポートしていません。 主な例は、Facebook、Google、Yahoo などのソーシャル ID プロバイダーとのフェデレーションです。 ユーザーがこの種類の資格情報でサインインする場合、Azure AD は適しません。 これも、ACS と必ずしも同じ認証プロトコルに対応していない一方で、ACS と Azure AD はいずれも OAuth をサポートしています。たとえば、移行の一環としてコードを変更する必要がある実装によっては微妙な違いがあります。

ただし、Azure AD には ACS の顧客にとっていくつかの潜在的な利点があります。 たとえば、クラウドでホストされる Microsoft の職場または学校のアカウント (ACS 顧客によって一般的に使用される) をネイティブにサポートします。  Azure AD テナントを、ADFS を介してオンプレミスの Active Directory の 1 つ以上のインスタンスにフェデレーションすることもできます。これにより、クラウド ベースのユーザーとオンプレミスでホストされるユーザーの両方をアプリで認証できます。  また、WS-Federation プロトコルにも対応しています。これにより、Windows Identity Foundation (WIF) を使用して、Web アプリケーションと比較的簡単に統合できます。

次の表は、Web アプリケーションに関連する ACS の機能を Azure AD で利用できる機能と比較したものです。 大まかには、**ユーザーに Microsoft の職場または学校アカウントを使用してサインインさせるだけなら、Azure Active Directory がおそらく移行に適した選択肢です**。

| 機能 | ACS のサポート | Azure AD のサポート |
| ---------- | ----------- | ---------------- |
| **アカウントの種類** | | |
| Microsoft の職場または学校のアカウント | サポートされています | サポートされています |
| Windows Server Active Directory および ADFS のアカウント | サポート対象 (Azure AD テナントとのフェデレーションを使用) <br /> サポート対象 (ADFS との直接フェデレーションを使用) | Azure AD テナントとのフェデレーションによってのみサポート対象 | 
| その他のエンタープライズ ID 管理システムのアカウント | Azure AD テナントとのフェデレーションにより可能 <br />サポート対象 (直接フェデレーションを使用) | Azure AD テナントとのフェデレーションにより可能 |
| 個人的な使用のための Microsoft アカウント (Windows Live ID) | サポートされています | サポート対象 (Azure AD の v2.0 OAuth プロトコルを使用。その他のプロトコルは使用できません) | 
| Facebook、Google、Yahoo アカウント | サポートされています | 未サポート |
| **プロトコルと SDK の互換性** | | |
| Windows Identity Foundation (WIF) | サポートされています | サポート対象。ただし、使用できる手順に制限あり。 |
| WS-Federation | サポートされています | サポートされています |
| [OAuth 2.0] | Draft 13 をサポート | RFC 6749 (ほとんどの最新の仕様) をサポート |
| WS-Trust | サポートされています | サポートされていません |
| **トークン形式** | | |
| Json Web トークン (JWT) | Beta でサポート | サポートされています |
| SAML 1.1 トークン | サポートされています | サポートされています |
| SAML 2.0 トークン | サポートされています | サポートされています |
| Simple Web Token (SWT) | サポートされています | サポートされていません |
| **カスタマイズ** | | |
| カスタマイズ可能なホーム領域検出/アカウント選択 UI | アプリに組み込めるダウンロード可能なコード | サポートされていません |
| カスタムのトークン署名証明書をアップロード | サポートされています | サポートされています |
| トークンの要求をカスタマイズ | ID プロバイダーからの入力要求をパススルー<br />ID プロバイダーからアクセス トークンを要求として取得<br />入力要求の値に基づいて出力要求を発行<br />定数値を持つ出力要求を発行 | フェデレーション ID プロバイダーからの要求をパススルーすることはできません<br />ID プロバイダーからアクセス トークンを要求として取得できません<br />入力要求の値に基づいて出力要求を発行できません<br />定数値を持つ出力要求を発行できます<br />Azure AD に同期されたユーザーのプロパティに基づいて出力要求を発行できます |
| **Automation** | | |
| 構成/管理タスクを自動化 | サポート対象 (ACS 管理サービスを使用) | サポート対象 (Microsoft Graph と Azure AD Graph API を使用)。 |

Azure AD がアプリケーションとサービスについて先に進むための適切な経路であると判断した場合は、アプリを Azure AD と統合する 2 つの方法を認識する必要があります。

Azure AD と統合するために WS-Federation/WIF を使用するには、[この記事で説明しているアプローチ](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-federated-sso-non-gallery)に従うことをお勧めします。 この記事では、SAML ベースのシングル サインオン用の Azure AD の構成を参照しますが、WS-Federation の構成にも使用できます。 このアプローチに従うには Azure AD Premium ライセンスが必要ですが、2 つのメリットがあります。

- Azure AD トークン カスタマイズの柔軟性をフルに活用できます。 これにより、特にユーザー ID や名前識別子の要求など、Azure AD によって発行される要求をカスタマイズして、ACS によって発行される要求と一致させることができます。 テクノロジが変更された後もユーザーのユーザー ID が引き続き一貫するように、Azure AD によって発行されるユーザー ID が ACS によって発行されるユーザー ID と一致することを確認する必要があります。
- アプリケーション固有のトークン署名証明書を構成し、その有効期間を制御できます。

<!--

Possible nameIdentifiers from ACS (via AAD or ADFS):
- ADFS - Whatever ADFS is configured to send (email, UPN, employeeID, what have you)
- Default from AAD using App Registrations, or Custom Apps before ClaimsIssuance policy: subject/persistent ID
- Default from AAD using Custom apps nowadays: UPN
- Kusto can't tell us distribution, it's redacted

-->

> [!NOTE]
> このアプローチで進めるには、Azure AD Premium ライセンスが必要です。 ACS のお客様がアプリケーションにシングル サインオンを設定するために Premium ライセンスを必要とする場合、ご連絡いただければ開発者用ライセンスを喜んで提供いたします。

別のアプローチは、WS-Federation の設定方法の手順が若干異なる[このコード サンプル](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation)に従うことです。 このコード サンプルでは、WIF を使用せず、代わりに ASP.NET 4.5 OWIN ミドルウェアを使用します。 ただし、アプリ登録の手順は WIF を使用するアプリに対して有効で、Azure AD Premium ライセンスは必要ありません。  このアプローチを選ぶ場合は、[Azure AD での署名キー ロールオーバー](https://docs.microsoft.com/azure/active-directory/develop/active-directory-signing-key-rollover)について理解する必要があります。 このアプローチでは、Azure AD のグローバル署名キーを使用してトークンを発行します。 既定では、WIF は署名キーを自動的に更新しません。 Azure AD がそのグローバル署名キーを回転する場合、変更を確定するために WIF 実装を準備する必要があります。

OpenID Connect または OAuth プロトコル経由で Azure AD と統合できる場合は、その方法で統合することをお勧めします。  Azure AD を Web アプリケーションに統合する方法について、さまざまなドキュメントとガイダンスが [Azure AD 開発者ガイド](http://aka.ms/aaddev)に記載されています。

<!-- TODO: If customers ask about authZ, let's put a blurb on role claims here -->

#### <a name="migrating-to-azure-ad-b2c"></a>Azure AD B2C への移行

検討すべきその他の移行パスは、Azure AD B2C です。  B2C はクラウド認証サービスであり、ACS と同様に、開発者はクラウド サービスに認証および ID 管理ロジックを外部委託できます。  これは、アクティブ ユーザーが最大何百万人もになることのあるコンシューマー向けアプリケーション用に設計された有料サービス (Free および Premium レベル) です。

ACS と同様に、B2C の最も魅力的な機能の 1 つは、さまざまな種類のアカウントがサポートされることです。 B2C では、ユーザーは Facebook、Google、Microsoft、LinkedIn、GitHub、Yahoo などのアカウントでサインインできます。 さらに B2C では、"ローカル アカウント"、つまりユーザーがアプリケーション専用に作成するユーザー名とパスワードもサポートされます。 Azure AD B2C には、ログイン フローのカスタマイズに使用できる豊富な機能拡張も用意されています。 ただし、ACS の顧客が必要とする可能性のある広範な認証プロトコルとトークン形式はサポートされません。 トークンを取得したり、ユーザーに関する追加情報を ID プロバイダーや Microsoft などにクエリしたりするために使用することもできません。

次の表は、Web アプリケーションに関連する ACS の機能を Azure AD B2C で利用できる機能と比較したものです。 大まかに言えば、**おそらく Azure AD B2C は、アプリケーションがコンシューマー向けの場合、またはさまざまな種類のアカウントをサポートする場合の移行に適した選択肢です。**

| 機能 | ACS のサポート | Azure AD B2C のサポート |
| ---------- | ----------- | ---------------- |
| **アカウントの種類** | | |
| Microsoft の職場または学校のアカウント | サポートされています | サポート対象 (カスタム ポリシーを使用)  |
| Windows Server Active Directory および ADFS のアカウント | サポート対象 (ADFS との直接フェデレーションを使用) | サポート対象 (カスタム ポリシーを使用した SAML フェデレーション経由) |
| その他のエンタープライズ ID 管理システムのアカウント | サポート対象 (WS-Federation 上の直接フェデレーションを使用) | サポート対象 (カスタム ポリシーを使用した SAML フェデレーション経由) |
| 個人的な使用のための Microsoft アカウント (Windows Live ID) | サポートされています | サポートされています | 
| Facebook、Google、Yahoo アカウント | サポートされています | Facebook と Google はネイティブにサポートされ、Yahoo はカスタム ポリシーを使用して OpenID Connect フェデレーションを介してサポートされます |
| **プロトコルと SDK の互換性** | | |
| Windows Identity Foundation (WIF) | サポートされています | サポートされていません。 |
| WS-Federation | サポートされています | サポートされていません。 |
| [OAuth 2.0] | Draft 13 をサポート | RFC 6749 (ほとんどの最新の仕様) をサポート |
| WS-Trust | サポートされています | サポートされていません。 |
| **トークン形式** | | |
| Json Web トークン (JWT) | Beta でサポート | サポートされています |
| SAML 1.1 トークン | サポートされています | サポートされていません |
| SAML 2.0 トークン | サポートされています | サポートされていません |
| Simple Web Token (SWT) | サポートされています | サポートされていません |
| **カスタマイズ** | | |
| カスタマイズ可能なホーム領域検出/アカウント選択 UI | アプリに組み込めるダウンロード可能なコード | カスタム CSS を使用して完全にカスタマイズできます。 |
| カスタムのトークン署名証明書をアップロード | サポートされています | 証明書ではなくカスタム署名キーがサポート対象です (カスタム ポリシーを使用)。 |
| トークンの要求をカスタマイズ | ID プロバイダーからの入力要求をパススルー<br />ID プロバイダーからアクセス トークンを要求として取得<br />入力要求の値に基づいて出力要求を発行<br />定数値を持つ出力要求を発行 | ID プロバイダーからの要求をパススルーできます。 一部の要求にはカスタム ポリシーが必要です。<br />ID プロバイダーからアクセス トークンを要求として取得できません<br />カスタム ポリシーを使用して入力要求の値に基づいて出力要求を発行できます<br />カスタム ポリシーを使用して定数値を持つ出力要求を発行できます |
| **Automation** | | |
| 構成/管理タスクを自動化 | サポート対象 (ACS 管理サービスを使用) | Azure AD Graph API を使用してユーザーを作成できます。 B2C テナント、アプリケーション、またはポリシーをプログラムで作成することはできません。 |

Azure AD B2C がアプリケーションとサービスについて先に進むための適切な経路であると判断した場合は、次のリソースで開始する必要があります。

- [Azure AD B2C ドキュメント](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)
- [Azure AD B2C カスタム ポリシー](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview-custom)
- [Azure AD B2C の価格](https://azure.microsoft.com/pricing/details/active-directory-b2c/)


#### <a name="other-migration-options"></a>その他の移行オプション

Azure AD と Azure AD B2C のどちらでも Web アプリケーションのニーズが満たされない場合は、先に進むのに最適な経路を特定できるようお手伝いいたしますのでご連絡ください。

<!--

#### Migrating to Ping Identity or Auth0

In some cases, you may find that neither Azure AD nor Azure AD B2C is sufficient to replace ACS in your web applications without making major code changes.  Some common examples might include:

- web applications using WIF and/or WS-Federation for sign-in with social identity providers such as Google or Facebook.
- web applications performing direct federation to an enterprise IDP over the Ws-Federation protocol.
- web applications that require the access token issued by a social identity provider (such as Google or Facebook) as a claim in the tokens issued by ACS.
- web applications with complex token transformation rules that Azure AD or Azure AD B2C cannot reproduce.

In these cases, you may want to consider migrating your web application to another cloud authentication service. We recommend exploring the following options, as each provides capabilities similar to ACS:

- [Auth0](https://auth0.com/) has created [high-level migration guidance for customers of ACS](https://auth0.com/blog/windows-azure-acs-alternative-replacement/), and provides a feature-by-feature comparison of ACS vs. Auth0.
- Enterprise customers should consider [Ping Identity](https://www.pingidentity.com) as well. Reach out to us and we can connect you with a representative from Ping who is prepared to help identify potential solutions.

Our aim in working with Ping Identity & Auth0 is to ensure that all ACS customers have a path forward for their apps & services that minimizes the amount of work required to move off of ACS.

-->

<!--

## Sharepoint 2010, 2013, 2016

TODO: AAD only, use AAD SAML 1.1 tokens, when we bring it back online.
Other IDPs: use Auth0? https://auth0.com/docs/integrations/sharepoint.

-->

### <a name="web-services-using-active-authentication"></a>アクティブな認証を使用する Web サービス

ACS によって発行されたトークンでセキュリティ保護された Web サービスに対して、ACS は次の機能を提供しています。

- トークンを要求するために使用できる 1 つまたは複数の**サービス ID** を ACS 名前空間に登録する機能。
- 次の種類の資格情報を使用してトークンを要求するための OAuth WRAP および OAuth 2.0 Draft 13 プロトコルのサポート。
    - サービス ID に対して作成されるシンプルなパスワード
    - 対称キーまたは X509 証明書を使用した署名付き SWT
    - 信頼できる ID プロバイダー (通常は ADFS インスタンス) によって発行された SAML トークン
- 次のトークン形式のサポート: JSON Web トークン (JWT)、SAML 1.1、SAML 2.0、Simple Web Token (SWT)
- シンプルなトークン変換ルール

ACS のサービス ID は、通常は認証と同様にサーバー対サーバー (S2S) の実装に使用されます。  

#### <a name="migrating-to-azure-active-directory"></a>Azure Active Directory への移行

この種類の認証フローに対する推奨事項は、[**Azure Active Directory**](https://azure.microsoft.com/develop/identity/signin/) (Azure AD) に移行することです。 Azure AD は、Microsoft の職場または 学校のアカウントのクラウド ベースの ID プロバイダーで、Office 365、Azure などのプロバイダーです。 ただし、Azure AD の OAuth クライアント資格情報の付与の実装を使用して、サーバーをサーバー認証に使用することもできます。  次の表では、サーバー対サーバー認証での ACS の機能を Azure AD で使用可能な機能と比較します。

| 機能 | ACS のサポート | Azure AD のサポート |
| ---------- | ----------- | ---------------- |
| Web サービスを登録する方法 | ACS 管理ポータルで証明書利用者を作成します。 | Azure Portal で Azure AD Web アプリケーションを作成します。 |
| クライアントを登録する方法 | ACS 管理ポータルでサービス ID を作成します。 | Azure Portal で別の Azure AD Web アプリケーションを作成します。 |
| 使用されるプロトコル | OAuth WRAP プロトコル<br />OAuth 2.0 Draft 13 クライアント資格情報の付与 | OAuth 2.0 クライアント資格情報の付与 |
| クライアント認証方法 | シンプルなパスワード<br />署名付き SWT<br />フェデレーション IDP からの SAML トークン | シンプルなパスワード<br />署名付き JWT |
| トークン形式 | JWT<br />SAML 1.1<br />SAML 2.0<br />SWT<br /> | JWT のみ |
| トークン変換 | カスタム要求の追加<br />シンプルな if-then 要求発行ロジック | カスタム要求の追加 | 
| 構成/管理タスクを自動化 | サポート対象 (ACS 管理サービスを使用) | サポート対象 (Microsoft Graph と Azure AD Graph API を使用)。 |

サーバー対サーバーのシナリオの実装のガイダンスについては、次のリソースをご覧ください。

- [Azure AD 開発者ガイドの「サービス間」セクション](https://aka.ms/aaddev)。
- [シンプルなパスワード クライアント資格情報を使用したデーモン コード サンプル](https://github.com/Azure-Samples/active-directory-dotnet-daemon)
- [証明書クライアント資格情報を使用したデーモン コード サンプル](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential)

#### <a name="other-migration-options"></a>その他の移行オプション

Azure AD では Web サービスのニーズが満たされない場合は、コメントを入力いただければ、具体的なケースに最適なプランの識別をお手伝いいたします。

<!--

#### Migrating to Ping Identity or Auth0

In some cases, you may find that neither Azure AD's client credentials OAuth grant implementation is not sufficient to replace ACS in your architecture without major code changes.  Some common examples might include:

- server-to-server authentication using token formats other than JWTs.
- server-to-server authentication using an input token provided by an external identity provider.
- server-to-server authentication with token transformation rules that Azure AD cannot reproduce.

In these cases, you may want to consider migrating your web application to another cloud authentication service. We recommend exploring the following options, as each provides capabilities similar to ACS:

- [Auth0](https://auth0.com/) has created [high-level migration guidance for customers of ACS](https://auth0.com/blog/windows-azure-acs-alternative-replacement/), and provides a feature-by-feature comparison of ACS vs. Auth0.
- Enterprise customers should consider [Ping Identity](https://www.pingidentity.com) as well. Reach out to us and we can connect you with a representative from Ping who is prepared to help identify potential solutions.

Our aim in working with Ping Identity & Auth0 is to ensure that all ACS customers have a path forward for their apps & services that minimizes the amount of work required to move off of ACS.

-->

## <a name="questions-concerns--feedback"></a>質問、懸念事項、フィードバック

Microsoftでは、多くの ACS のお客様がこの記事を読んでも明確な移行パスを見つけられず、適切なプランの決定において支援やガイダンスを必要とする可能性があることを認識しています。 移行シナリオと質問について話し合いをご希望の場合は、このページにコメントを残してください。
