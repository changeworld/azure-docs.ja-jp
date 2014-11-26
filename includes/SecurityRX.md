# Azure のセキュリティに関するガイダンス

## 要約

Azure のアプリケーションを開発する場合に注意する必要のある主要なセキュリティの項目は、ID とアクセスです。このトピックでは、クラウドでの ID とアクセスに関するセキュリティの主要項目と、クラウド アプリケーションを保護するための最良の方法について説明します。

## 概要

アプリケーションのセキュリティは、アプリケーションの表面で機能します。アプリケーションの公開先が増えるほど、セキュリティ上の懸念が拡大します。たとえば、自動のバッチ プロセスとして実行されるアプリケーションは、セキュリティの面からは、一般ユーザーが利用できる Web サイトよりも小規模で公開されます。

クラウドに移行すると、インフラストラクチャやネットワーキングに関する管理者の負担は軽減されます。世界規模のセキュリティ手法、ツール、テクノロジを備えたデータ センターで管理されるためです。その一方で、クラウドの性質上、アプリケーションの公開規模は大きくなるため、攻撃を受ける可能性が高まります。そのため、多くのクラウド テクノロジやサービスは、エンドポイント コンポーネントまたはメモリ内のコンポーネントとして公開されています。Azure ストレージ、Service Bus、SQL Database (旧 SQL Azure)、およびその他の多くのサービスは、ネットワーク経由でエンドポイントからアクセスできます。

クラウド アプリケーションでは、攻撃を食い止める高いセキュリティ標準を設計、開発、および保守するため、アプリケーション開発者の負担は従来よりも多くなります。次の図を参照してください (J.D. Meier の [Azure セキュリティノート PDF][Azure セキュリティノート PDF] より)。クラウド プロバイダー (マイクロソフトの場合はAzure) がインフラストラクチャ部分にどのように対応するかに注目してください。アプリケーション開発者に多くのセキュリティ関連作業が任されています。

![アプリケーションのセキュリティ保護][アプリケーションのセキュリティ保護]

開発者にとってよい点は、従来のセキュリティ開発手法、原則、技法すべてを、クラウド アプリケーションの開発にも適用できるということです。開発の際に考慮する必要のある主な領域は次のとおりです。

-   インジェクション攻撃を防ぐため、すべての入力を、種類、
    長さ、範囲、文字パターンについて検証します。また、アプリケーションが
    エコー バックするデータはすべて、適切に無害化します。
-   機密データを、ローカルおよびネットワーク上で保護し、
    情報漏えいやデータ改ざんの脅威を抑制します。
-   監査やログ機能を適切に実装し、非否認の脅威を
    抑制します。
-   認証と承認を、プラットフォームが提供する実証済みのメカニズムを
    使用して実装し、ID のなりすましや特権の昇格の脅威を
    抑制します。

脅威、攻撃、脆弱性、および対処法の包括的な一覧については、patterns & practices の「[一覧: Webアプリケーションのセキュリティ フレーム][一覧: Webアプリケーションのセキュリティ フレーム]」および「[アプリケーション セキュリティ ガイダンス インデックス][アプリケーション セキュリティ ガイダンス インデックス]」を参照してください。

クラウドにおける認証とアクセス制御のメカニズムは、内部設置型アプリケーションの場合と大きく異なります。クラウドアプリケーションでは、より多くの認証およびアクセス制御のオプションを利用できます。これが、混乱の原因となり、実装の欠陥につながります。クラウド アプリケーションの定義では、さらに混乱が生じます。たとえば、アプリケーションをクラウドにデプロイしても、認証メカニズムは Active Directory が提供することがあります。反対に、アプリケーションを内部設置でデプロイし、認証メカニズムはクラウドで提供することもあります。たとえば、Azure Active Directory Access Control (以前の Access Control Service (ACS)) を使用します。

## 脅威、脆弱性、および攻撃

脅威は、機密情報の漏えいやサービスの利用停止など、避けなければならない潜在的な問題です。脅威は通常、各脅威の頭文字をとった「STRIDE」という区分で分類されます。

-   **S**poofing or identity theft (なりすましまたは ID の盗難)
-   **T**ampering with data (データの改ざん)
-   **R**epudiation of actions (アクションの否認)
-   **I**nformation disclosure (情報漏えい)
-   **D**enial of service (サービス拒否)
-   **E**levation of privileges (特権の昇格)

脆弱性はコードの開発でもたらされるバグで、攻撃者によってアプリケーションの攻撃に利用される可能性があります。たとえば、機密データをクリア テキストで送信すると、トラフィック スニッフィング攻撃によって情報が漏えいする可能性があります。

攻撃者はこれらの脆弱性を利用して、アプリケーションを攻撃します。たとえば、クロス サイト スクリプト (XSS) は、無害化されていない出力を利用する攻撃です。もう 1 つの例は、クリア テキストで送信される機密情報を捕捉する、ネットワークの盗聴です。これらの攻撃は、ID のなりすまし攻撃の布石となります。論点を明確にするため、問題を脅威、脆弱性、および攻撃に限定します。Azure にデプロイした Web アプリケーションに関連する問題を概念化した次の図を参照してください (J.D.Meier の[Azure セキュリティ ノート PDF][Azure セキュリティノート PDF] より)。

![脅威、脆弱性、および攻撃][脅威、脆弱性、および攻撃]

開発者は、脆弱性を全体的に制御します。開発でもたらされる脆弱性が少ないほど、攻撃者が利用できる方法は少なくなります。

ID とアクセス関連の脆弱性は、対策しない限り、STRIDE モデルのすべての脅威に利用される可能性があります。たとえば、実装が不適切な認証メカニズムは ID のなりすましの原因となり、その結果、情報漏えい、データ改ざん、特権の昇格、またはサービス全体のシャットダウンにつながります。クラウド アプリケーションのID およびアクセス制御の実装における潜在的な脆弱性を発見するため、次の問いについて考えてみてください。

-   ネットワーク上ではクリア テキストで、Azure
    サービスに資格情報を送信しますか。
-   Azure サービスに資格情報をクリア テキストで格納しますか。
-   Azure サービスに強力なパスワード ポリシーを
    適用しますか。
-   資格情報の検証に Azure とカスタムの
    認証メカニズムのどちらを使用しますか。
-   Azure のサービス認証セッションまたはトークンの
    有効期間を適切に制限していますか。
-   配布したクラウド アプリケーションの Azure エントリ ポイントごと
    にアクセス許可を検証しますか。
-   利用する承認メカニズムは、機密情報を漏えいせずに、または
    無制限のアクセスを許可せずに、安全に遮断しますか。

## 対処法

攻撃に対する最良の対処法は、(独自に実装せずに) プラットフォームによって提供される ID やアクセスのメカニズムを使用することです。広く利用されている、次の ID およびアクセス テクノロジの導入を検討してください。

**Windows Identity Foundation (WIF)。** WIF は .NET Framework 4.5 に含まれている .NET ランタイム ライブラリです(.NET 3.5/4.0 用に単独でダウンロードすることもできます)。WIF は、WS-Federation やWS-Trust などのプロトコルの処理や、Security Assertion Markup Language(SAML) などのトークンの処理など、負荷の高い作業を実行します。そのため、アプリケーション用に複雑なセキュリティ関連コードを手動で作成する必要はありませんWIF の
詳しい情報については、次のリソースを参照してください。

-   MSDN コード ギャラリーの [Windows Identity Foundation 4.5 のサンプル][Windows Identity Foundation 4.5 のサンプル]。
-   MSDN コード ギャラリーの [Visual Studio 11 ベータ向け Windows Identity Foundation 4.5 ツール][Visual Studio 11 ベータ向け Windows Identity Foundation 4.5 ツール]。
-   MSDN の [Windows Identity Foundation ランタイム (.Net 3.5/4.0)][Windows Identity Foundation ランタイム (.Net 3.5/4.0)]。
-   MSDN の[Windows Identity Foundation 3.5/4.0 サンプルおよび Visual Studio 2008/2010 テンプレート][Windows Identity Foundation 3.5/4.0 サンプルおよび Visual Studio 2008/2010 テンプレート]。

**Azure AD Access Control (以前の ACS)**。
Azure AD Access Control は、Security Token Service (STS) を提供するクラウド サービスです。企業の Active Directory などのさまざまな ID プロバイダー (IdP) または Windows Live ID、Microsoft アカウント、Facebook、Google、Yahoo! などのインターネット IdP、および Open ID 2.0 ID プロバイダーとのフェデレーションを利用できます。Azure AD Access Control の詳しい情報については、次のリソースを参照してください。

-   [Access Control Service 2.0][Access Control Service 2.0]
-   [ACS を使用したシナリオとソリューション][ACS を使用したシナリオとソリューション]
-   [ACS の利用方法][ACS の利用方法]
-   [A Guide to Claims-Based Identity and Access Control (クレームベースの ID およびアクセス制御)][A Guide to Claims-Based Identity and Access Control (クレームベースの ID およびアクセス制御)]
-   [Identity Developer Training Kit][Identity Developer Training Kit]
-   [MSDN 提供の Identity Developer Training Course][MSDN 提供の Identity Developer Training Course]

**Active Directory フェデレーション サービス (AD FS)**。Active Directory フェデレーション サービス (AD FS) 2.0 は、Windows Server およびActive Directory テクノロジを利用する要求対応 ID ソリューションをサポートします。AD FS 2.0 は各種プロトコル (WS-Trust、WS-Federation、および SAML) をサポートします。AD FS の詳しい情報については、次のリソースを参照してください。

-   [AD FS 2.0 Content Map (AD FS 2.0 コンテンツ マップ)][AD FS 2.0 Content Map (AD FS 2.0 コンテンツ マップ)]
-   [Web SSO の設計][Web SSO の設計]
-   [フェデレーション Web SSO の設計][フェデレーション Web SSO の設計]

**Azure Shared Access Signature。** Shared Access Signature を使用すると、BLOB またはコンテナー リソースへのアクセスを調整できます。Shared Access Signatureの詳しい情報については、次のリソースを参照してください。

-   [コンテナーと BLOB へのアクセス管理][コンテナーと BLOB へのアクセス管理]
-   [新しいストレージ機能:共有アクセス署名を使用します][新しいストレージ機能:共有アクセス署名を使用します]
-   [Shared Access Signatures Are Easy These Days (最近使いやすくなった共有アクセス署名)][Shared Access Signatures Are Easy These Days (最近使いやすくなった共有アクセス署名)]

## シナリオ マップ

このセクションでは、このトピックで扱う主なシナリオの概要を説明します。個々のアプリケーションに最適な ID ソリューションを決定する際の案内図として使用してください。

-   **フェデレーション認証を使用する ASP.NET Web フォーム アプリケーション。**この
    シナリオでは、Windows Server Active Directory で管理される Live ID や
    Microsoft アカウントなどのインターネット ID、または企業 ID を
    使用して、ASP.NET Web フォームへのアクセスを制御します。
-   **フェデレーション認証を使用する WCF (SOAP) サービス。**この
    シナリオでは、Azure AD Access Control によって管理されるサービス ID
    を使用して、WCF (SOAP) サービスへのアクセスを制御します。
-   **Active Directory のフェデレーション認証や ID を使用する WC
    (SOAP) サービス。**このシナリオでは、企業の Windows Server Active Directory
    によって管理される ID を使用して、WCF (SOAP) Web サービスへの
    アクセスを制御します。
-   **フェデレーション認証を使用する WCF (REST) サービス。**この
    シナリオでは、Azure AD Access Control によって管理されるサービス ID
    を使用して、WCF (REST) サービスへのアクセスを制御します。
-   **Live ID/Microsoft アカウント、Facebook、Google、Yahoo!、
    Open ID を使用する WCF (REST) サービス。**このシナリオでは、Live ID や
    Microsoft アカウントなどのインターネット ID を使用して、WCF (REST) サービスへの
    アクセスを制御します。
-   **共有 SWT トークンを使用する REST WCF サービスへの ASP.NET Web アプリケーション。**この
    シナリオでは、フロントエンド ASP.NET Web アプリケーションとダウンストリーム REST サービスを含む
    分散型アプリケーションがあり、エンド ユーザーの
    コンテキストを物理層でフローさせます。
-   **要求対応アプリケーションとサービスにおけるロールベースのアクセス
    制御 (RBAC) 承認。**このシナリオでは、ロールに基づいて
    アプリケーションに承認ロジックを実装します。
-   **要求対応アプリケーションとサービスにおける要求ベースの
    承認。**このシナリオでは、複雑な承認ルールに基づいて
    アプリケーションに承認ロジックを実装します。
-   **Azure のストレージ サービスの ID とアクセスのシナリオ。**この
    シナリオでは、Azure のストレージ BLOB およびコンテナーへの共有
    アクセスを安全に保護する必要があります。
-   **Azure SQL Database の ID とアクセスのシナリオ。** SQL
    Database では、SQL Server 認証のみがサポートされています。Windows
    認証 (統合セキュリティ) はサポートされていません。ユーザーは
    SQL Database に接続するたびに、資格情報 (ログイン名とパスワード) を
    入力する必要があります。
-   **Azure Service Bus の ID とアクセスのシナリオ。**この
    シナリオでは、Azure Service Bus キューに安全にアクセスする必要があります。
-   **メモリ内キャッシュの ID とアクセスのシナリオ。**このシナリオでは、
    メモリ内キャッシュによって管理されるデータに安全にアクセスする必要があります。
-   **Azure Marketplace の ID とアクセスのシナリオ。**このシナリオでは、
    Azure Marketplace のデータセットに安全にアクセスする
    必要があります。

## Azure の ID とアクセスのシナリオ

このセクションでは、さまざまなアプリケーション アーキテクチャにおける一般的な ID とアクセスのシナリオの概要について説明します。簡単な説明については「シナリオ マップ」を参照してください。

### フェデレーション認証を使用する ASP.NET Web フォーム アプリケーション

このアプリケーション アーキテクチャ シナリオでは、Web アプリケーションをAzure または内部設置型システムにデプロイできます。このアプリケーションでは、ユーザー認証は、企業の Active Directory またはインターネットID プロバイダーが行う必要があります。

このシナリオを解決するには、Azure AD Access Control とWindows Identity Foundation を使用します。

![Azure Active Directory Access Control][Azure Active Directory Access Control]

このシナリオを実装するには、以下のリソースを参照してください。

-   [方法: ACS を使用して最初の要求対応 ASP.NET アプリケーションを作成する][方法: ACS を使用して最初の要求対応 ASP.NET アプリケーションを作成する]
-   [方法: ASP.NET Web アプリケーションでログイン ページをホストする][方法: ASP.NET Web アプリケーションでログイン ページをホストする]
-   [方法: WIF および ACS を使用して要求対応の ASP.NET アプリケーションで要求承認を実装する][方法: WIF および ACS を使用して要求対応の ASP.NET アプリケーションで要求承認を実装する]
-   [方法: WIF および ACS を使用して要求対応の ASP.NET アプリケーションでロール ベースのアクセス制御 (RBAC) を実装する][方法: WIF および ACS を使用して要求対応の ASP.NET アプリケーションでロール ベースのアクセス制御 (RBAC) を実装する]
-   [方法: X.509 証明書を使用して ACS と ASP.NET Web アプリケーションの間の信頼を構成する][方法: X.509 証明書を使用して ACS と ASP.NET Web アプリケーションの間の信頼を構成する]
-   [サンプル コード: ASP.NET の単純フォーム][サンプル コード: ASP.NET の単純フォーム]

### サービス ID を使用する WCF (SOAP) サービス

このアプリケーション アーキテクチャ シナリオでは、WCF (SOAP) サービスをAzure または内部設置型システムにデプロイできます。このサービスはダウンストリームサービスとして、Web アプリケーションまたはその他の Webサービスからアクセスされます。アプリケーション専用の ID を使用して、アクセスを制御します。IIS で使用されるアプリケーション プール アカウントの種類の点から考えてみましょう。テクノロジは異なりますが、エンド ユーザーアカウントではなくアプリケーション スコープ アカウントを使用してアクセスされるサービスという点で似ている方法です。

サービス ID 機能を Azure AD Access Control で使用します。これは、アプリケーションを Windows Server と IIS にデプロイするときに使用する、IIS アプリケーション プール アカウントと似ています。WCF (SOAP) サービスでWIF によって処理される SAML トークンを発行するように Azure AD Access Control を構成します。

![WCF (SOAP) サービス][WCF (SOAP) サービス]

このシナリオを実装するには、以下のリソースを参照してください。

-   [方法: X.509 証明書、パスワード、または対称キーを使用してサービス ID を追加する][方法: X.509 証明書、パスワード、または対称キーを使用してサービス ID を追加する]
-   [方法: ACS で保護された WCF サービスに対してクライアント証明書を使用して認証する][方法: ACS で保護された WCF サービスに対してクライアント証明書を使用して認証する]
-   [方法: ACS で保護された WCF サービスに対してユーザー名とパスワードを使用して認証する][方法: ACS で保護された WCF サービスに対してユーザー名とパスワードを使用して認証する]
-   [サンプル コード: WCF 証明書の認証][サンプル コード: WCF 証明書の認証]
-   [サンプル コード: WCF ユーザー名認証][サンプル コード: WCF ユーザー名認証]

### Active Directory のフェデレーション認証、ID を使用する WCF (SOAP) サービス

このアプリケーション アーキテクチャ シナリオでは、WCF (SOAP) サービスをAzure または内部設置型システムにデプロイできます。企業の Windows Server Active Directory (AD) によって管理される ID を使用して、WCF (SOAP)サービスへのアクセスを制御します。

Windows Server AD FS とのフェデレーションを構成済みの Azure AD Access Control を使用します。この場合、Azure AD Access Control でサービス ID を構成する必要はありません。WCF (SOAP) サービスにアクセスするエージェントが AD FS に資格情報を提供し、認証が成功すると、AD FS によってトークンが発行されます。このトークンは、次に Azure AD Access Control に送信され、再発行されてエージェントに戻ります。エージェントはこのトークンを使用して、要求を WCF (SOAP) サービスに送信します。

![AD を使用する WCF (SOAP) サービス][AD を使用する WCF (SOAP) サービス]

このシナリオを実装するには、以下のリソースを参照してください。

-   [方法: X.509 証明書、パスワード、または対称キーを使用してサービス ID を追加する][方法: X.509 証明書、パスワード、または対称キーを使用してサービス ID を追加する]
-   [方法: AD FS 2.0 を ID プロバイダーとして構成する][方法: AD FS 2.0 を ID プロバイダーとして構成する]
-   [方法: 管理サービスを使用して AD FS 2.0 をエンタープライズ IDプロバイダーとして構成する][方法: 管理サービスを使用して AD FS 2.0 をエンタープライズ IDプロバイダーとして構成する]
-   [サンプル コード: AD FS 2.0 による WCF フェデレーション][サンプル コード: AD FS 2.0 による WCF フェデレーション]

### サービス ID を使用する WCF (REST) サービス

このシナリオでは、WCF (REST) サービスを Azure または内部設置型システムにデプロイできます。このサービスはダウンストリーム サービスとして、Web アプリケーションまたはその他の Web サービスからアクセスされます。アプリケーション専用の ID を使用して、アクセスを制御します。IIS で使用されるアプリケーション プール アカウントの種類の点から考えてみましょう。テクノロジは異なりますが、エンド ユーザー アカウントではなくアプリケーション スコープ アカウントを使用してアクセスされるサービスという点で似ている方法です。

サービス ID 機能を Azure AD Access Control で使用します。Simple Web Token (SWT) トークンを発行するように Azure AD Access Control を構成します。REST サービス側で SWT トークンを処理するには、カスタムトークン ハンドラーを実装して WIF パイプラインに組み込むか、WIF インフラストラクチャを使用せずに "手動で"解析します。

次の図で考えてみましょう (WIF はオプションです)。

![REST サービス][REST サービス]

このシナリオを実装するには、以下のリソースを参照してください。

-   [方法: 対称キーを使用して ACS と WCF サービスの間の信頼を構成する][方法: 対称キーを使用して ACS と WCF サービスの間の信頼を構成する]
-   [方法: ACS を使用して Azure にデプロイした REST WCF サービスを認証する][方法: ACS を使用して Azure にデプロイした REST WCF サービスを認証する]
-   [サンプル コード: ASP.NET Web サービス][サンプル コード: ASP.NET Web サービス]
-   [サンプル コード: Windows Phone 7 アプリケーション][サンプル コード: ASP.NET Web サービス]
-   [Azure Access Control Service (ACS) によって発行される SWT トークンを使用する REST WCF][Azure Access Control Service (ACS) によって発行される SWT トークンを使用する REST WCF]

### Live ID/Microsoft アカウント、Facebook、Google、Yahoo!、Open ID を使用する WCF (REST) サービス。

このシナリオでは、WCF (REST) サービスを Azure または内部設置型システムにデプロイできます。Live ID、Microsoft アカウント、Facebook などのパブリックなインターネット ID を使用して、WCF (REST) サービスへのアクセスを制御します。

Azure AD Access Control を使用して SWT トークンを発行します。REST サービス側でSWT トークンを処理するには、カスタム トークン ハンドラーを実装してWIF パイプラインに組み込むか、WIF インフラストラクチャを使用せずに "手動で" 解析します。

このシナリオを実装するには、アプリケーションが Web ブラウザーを使用して、エンド ユーザーの資格情報収集を制御する必要があることに注意してください。そのため、REST サービスが ASP.NET Web アプリケーションからアクセスされるシナリオには適していません。REST サービスが、Windows Phone 7 アプリやリッチ デスクトップ クライアントなどのユーザー クライアント アプリケーションからアクセスされるシナリオに適しています。Web ブラウザーの制御を切り離す主な理由は、インターネット ID がアクティブなプロファイル シナリオ (Web サービス シナリオ) をネイティブではサポートしていないためです。インターネット ID は主に、ブラウザー リダイレクトに依存するパッシブなプロファイル シナリオ (Web アプリケーション) をサポートしています。そのため、Web ブラウザー制御が簡易になります。

次の図で考えてみましょう (WIF はオプションのため表示されていません)。

![WIF はオプション][WIF はオプション]

このシナリオを実装するには、以下のリソースを参照してください。

-   [方法: ACS を使用して Azure にデプロイした REST WCF サービスを認証する][方法: ACS を使用して Azure にデプロイした REST WCF サービスを認証する]
-   [方法: Google を ID プロバイダーとして構成する][方法: Google を ID プロバイダーとして構成する]
-   [方法: Facebook を ID プロバイダーとして構成する][方法: Facebook を ID プロバイダーとして構成する]
-   [方法: Yahoo! を ID プロバイダーとして構成する][方法: Yahoo! を ID プロバイダーとして構成する]
-   [サンプル コード: Windows Phone 7 アプリケーション][サンプル コード: ASP.NET Web サービス]
-   [Azure Access Control Service (ACS) によって発行される SWT トークンを使用する REST WCF][Azure Access Control Service (ACS) によって発行される SWT トークンを使用する REST WCF]

### 共有 SWT トークンを使用する REST WCF サービスへの ASP.NET Web アプリケーション

このシナリオでは、フロントエンド ASP.NET Web アプリケーションとダウンストリーム REST サービスを含む分散型アプリケーションがあり、エンド ユーザーのコンテキストを物理層間で維持します。これは、承認ロジックまたはログが、ダウンストリーム REST サービスのエンド ユーザー ID に基づく場合に必要になることがあります。

SWT トークンを発行するように Azure AD Access Control を構成します。SWT トークンはフロントエンド ASP.NET Web アプリケーションに発行され、ダウンストリーム RESTサービスと共有されます。この場合、Azure AD Access Control で証明書利用者を 1 つだけ構成します。ただし、注意点がいくつかあります。

-   WIF はそのまま使用できる SWT トークン ハンドラーを提供していないため、
    ASP.NET Web アプリケーションで使用されるカスタム トークン ハンドラーを
    実装する必要があります。ブラウザー リダイレクトに依存する WS-Federation プロトコルを
    サポートするための複雑な作業は、手動で実装せずに WIF を利用
    します。
-   SWT カスタム トークン ハンドラーを実装する場合、ブートストラップ トークンが
    保持されることを確認するため、ブートストラップ トークンにアクセスできることを
    確認します。アクセスできないと、共有してダウンストリーム REST サービスに
    送信することができません。
-   REST サービス側で WIF 使用する必要はありません。この場合はリダイレクトを
    処理する必要がないため、トークンを "手動で" 解析しても
    かまいません。

![ASP.NET Web アプリケーション][ASP.NET Web アプリケーション]

このシナリオを実装するには、以下のリソースを参照してください。

-   [方法: Google を ID プロバイダーとして構成する][方法: Google を ID プロバイダーとして構成する]
-   [方法: Facebook を ID プロバイダーとして構成する][方法: Facebook を ID プロバイダーとして構成する]
-   [方法: Yahoo! を ID プロバイダーとして構成する][方法: Yahoo! を ID プロバイダーとして構成する]
-   [共有 SWT トークンを使用する REST WCF サービス委任への ASP.NET Web アプリケーション][共有 SWT トークンを使用する REST WCF サービス委任への ASP.NET Web アプリケーション]

### 要求対応アプリケーションとサービスにおけるロールベースの Access Control (RBAC)

このシナリオでは、ロールに基づいて Web アプリケーションまたはサービスに承認ロジックを実装します。必要なロールを持っているユーザーはアクセスすることができ、必要なロールを持っていないユーザーは拒否されます。アプリケーションは、"ユーザーは X のロールか" という単純な質問に回答するだけです。

このシナリオを解決する方法は複数あります。Azure AD Access Control、WIF Claims Authentication Manager、samlSecurityTokenRequirementマッピング、またはカスタム ロール マネージャーを使用できます。

すべての場合に WIF が使用されます。WIF は IPrincipal.IsInRole("MyRole") メソッドをサポートしています。ほとんどの場合で重要となるのが、トークンにURI が<http://schemas.microsoft.com/ws/2008/06/identity/claims/role> のロールの種類の要求があり、IsInRole メソッドの呼び出しでWIF がロールのメンバーシップを検証できるようにすることです。

**Azure AD Access Control**。この実装では、Azure AD Access Control の要求変換ルール エンジンが使用されます。要求変換ルール エンジンのルールを使用すると、入力方向の要求すべてをロールの種類の要求に変換して、トークンがアプリケーションまたはサービスに到達したときに、WIF がこのロール要求を解析してIsInRole 呼び出しが成功したこと確認できます。

![][0]

**WIF ClaimsAuthenticationManager**。この実装では、ClaimsAuthenticationManager を WIF の機能拡張ポイントとして使用します。この方法を使用して、任意の入力方向の要求をアプリケーションでロールの要求の種類に変換します。複雑な変換作業が、コードの作成のみに軽減されます。

![][1]

**samlSecurityTokenRequriement マッピング**。この実装では、samlSecurityTokenRequirement 構成を web.config で使用して、ロール要求として動作する要求の種類を WIF に通知できます。たとえば、トークンがグループの種類の要求を運ぶ場合、それをロール要求の種類にマップします。この方法では、単純なマッピングのみを行うことができます。

![][2]

**カスタム RoleManager。**この実装では、カスタム RoleManger を実装します。GetAllRoles() などのカスタム RoleManger インターフェイスメソッドを実装すると、WIF を使用して入力方向の要求が検査されます。

![][3]

このシナリオを実装するには、以下のリソースを参照してください。

-   [方法: WIF および ACS を使用して要求対応の ASP.NET アプリケーションでロール ベースのアクセス制御 (RBAC) を実装する][方法: WIF および ACS を使用して要求対応の ASP.NET アプリケーションでロール ベースのアクセス制御 (RBAC) を実装する]
-   [方法: 規則を使用してトークン変換ロジックを実装する][方法: 規則を使用してトークン変換ロジックを実装する]
-   [要求対応 (WIF) ASP.NET Web アプリケーションに対する RoleManager による承認][要求対応 (WIF) ASP.NET Web アプリケーションに対する RoleManager による承認]
-   サンプル コード: [Windows Identity Foundation SDK][Windows Identity Foundation SDK] での IsInRole の要求の使用

### 要求対応アプリケーションとサービスにおける要求ベースの承認

このシナリオでは、複雑な承認ロジックを Web アプリケーションまたはサービスに実装します。IsInRole() メソッドは承認のニーズを満たしません。承認 IsInRole() がロールに依存する場合は、前のセクションで概説したロール ベースのアクセス制御を実装することを検討してください。

ClaimsAuthorizationManager を WIF の機能拡張ポイントとして使用します。ClaimsAuthorizationManager では外部のアクセス チェックを呼び出すので、アプリケーションのコードでアクセス チェックを実装するよりもアプリケーションコードが簡素化され、保守しやすくなります。

![][4]

このシナリオを実装するには、以下のリソースを参照してください。

-   [方法: 規則を使用してトークン変換ロジックを実装する][方法: 規則を使用してトークン変換ロジックを実装する]
-   [方法: WIF および ACS を使用して要求対応の ASP.NET アプリケーションで要求承認を実装する][方法: WIF および ACS を使用して要求対応の ASP.NET アプリケーションで要求承認を実装する]
-   サンプル コード: [Windows Identity Foundation SDK][Windows Identity Foundation SDK] での要求ベースの
    承認

## Azure ストレージ サービスの ID とアクセスのシナリオ

このシナリオでは、Azure のストレージ BLOB およびコンテナーへの共有アクセスを安全に保護する必要があります。

共有アクセス署名を使用します。独自のアプリケーションからストレージ サービスアカウントにアクセスするには、ストレージ サービス アカウントを構成および管理するときに Azure ポータルから利用できる共有ハッシュを使用します。自分のストレージ サービス アカウントで他のユーザーに BLOB およびコンテナーへのアクセスを許可するには、Shared Access Signature の URL を使用します。

情報漏えいを防ぐため、情報の管理には特に注意を払ってください。また、Shared Access Signature の有効期間についても注意します。

![][5]

このシナリオを解決するには、以下のリソースを参照してください。

-   [コンテナーと BLOB へのアクセス管理][コンテナーと BLOB へのアクセス管理]
-   [新しいストレージ機能:共有アクセス署名を使用します][新しいストレージ機能:共有アクセス署名を使用します]
-   [Shared Access Signatures Are Easy These Days (最近使いやすくなった共有アクセス署名)][Shared Access Signatures Are Easy These Days (最近使いやすくなった共有アクセス署名)]

## Azure SQL Database の ID とアクセスのシナリオ

SQL データベースでは SQL Server 認証のみがサポートされています。Windows認証 (統合セキュリティ) はサポートされていません。ユーザーはSQL Database に接続するたびに、資格情報 (ログイン名とパスワード) を入力する必要があります。情報漏えいを防ぐため、ユーザー名とパスワードの管理には特に注意を払ってください。

![][6]

このシナリオを解決するには、以下のリソースを参照してください。

-   [セキュリティのガイドラインと制限事項 (SQL データベース)][セキュリティのガイドラインと制限事項 (SQL データベース)]
-   [方法: sqlcmd を使用して SQL データベースに接続する][方法: sqlcmd を使用して SQL データベースに接続する]
-   [方法: ADO.NET を使用して SQL データベースに接続する][方法: ADO.NET を使用して SQL データベースに接続する]
-   [方法: ASP.NET で SQL データベースに接続する][方法: ASP.NET で SQL データベースに接続する]
-   [方法: WCF Data Services で SQL データベースに接続する][方法: WCF Data Services で SQL データベースに接続する]
-   [方法: PHP を使用して SQL データベースに接続する][方法: PHP を使用して SQL データベースに接続する]
-   [方法: JDBC を使用して SQL データベースに接続する][方法: JDBC を使用して SQL データベースに接続する]
-   [方法: ADO.NET Entity Framework を使用して SQL データベースに接続する][方法: ADO.NET Entity Framework を使用して SQL データベースに接続する]

## Azure Service Bus の ID とアクセスのシナリオ

Service Bus と Azure AD Access Control には特別な関連があり、Service Bus の各サービス名前空間が、同じ名前の Access Control サービス名前空間 (サフィックスは "–sb") とペアになっています。これは、Service Bus と Access Control が、相互の信頼関係および関連付けられた暗号化シークレットを管理するための特別なリレーションシップです。詳細については、以下のリソースを参照してください。

![][7]

このシナリオを解決するには、以下のリソースを参照してください。

-   [ACS による Service Bus のセキュリティ保護][ACS による Service Bus のセキュリティ保護] (ビデオ)
-   [ACS による Service Bus のセキュリティ保護][8] (スライド)
-   [Service Bus 認証と Access Control サービスによる認証][Service Bus 認証と Access Control サービスによる認証]

## メモリ内キャッシュの ID とアクセスのシナリオ

メモリ内キャッシュ (以前の Azure Cache) は、認証をAzure AD Access Control に依存します。管理ポータルから利用できる共有キーを使用します。キャッシュにアクセスするとき、コードまたは構成ファイルでキーを使用します。情報漏えいを防ぐために、キーを安全な状態で保管してください。

![][9]

このシナリオを解決するには、以下のリソースを参照してください。

-   [方法: プログラムによってキャッシュ クライアントを構成する(Azure Shared Caching)][方法: プログラムによってキャッシュ クライアントを構成する(Azure Shared Caching)]
-   [方法: アプリケーション構成ファイルを使用してキャッシュ クライアントを構成する (Azure Shared Caching)][方法: アプリケーション構成ファイルを使用してキャッシュ クライアントを構成する (Azure Shared Caching)]
-   [Windows Azure AppFabric のサンプル][Windows Azure AppFabric のサンプル] (「キャッシュのサンプル」セクション)

## Azure Marketplace の ID とアクセスのシナリオ

Azure Marketplace データセットへのすべてのアクセスは、無料でも有料でも、ユーザーのアクセスを許可する前に認証する必要があります。アプリケーションを作成するときは、認証プロセスをコードに含める必要があります。次の一般的なシナリオで考えてみましょう。

### 自分のデータセットにアクセスする

このシナリオでは、自分の Marketplace サブスクリプションでデータセットを使用するアプリケーションを作成しています。あなたはアプリケーションのユーザーです。アプリケーションは、Azure、内部設置型のシステム、または Marketplace にデプロイできます。

Marketplace サブスクリプションから利用できる共有キーを使用します。Marketplace ポータルを使用して、共有キーを取得します。

![][10]

このシナリオを解決するには、以下のリソースを参照してください。

-   [Marketplace アプリケーションでの HTTP 基本認証の実装][Marketplace アプリケーションでの HTTP 基本認証の実装]

### ユーザーが自分のデータセットにアクセスする

このシナリオでは、自分のデータセットにユーザーがアクセスするのを許可するアプリケーションを作成しています。アプリケーションは、Azure、内部設置型のシステム、または Marketplace にデプロイできます。

このシナリオを解決するには、OAuth 委任を使用します。ユーザーは、Live ID またはMicrosoft アカウントの資格情報を入力するように求められ、同意プロセスも適用されます。

![][11]

このシナリオを解決するには、以下のリソースを参照してください。

-   [OAuth Web クライアントの例][OAuth Web クライアントの例]
-   [OAuth リッチ クライアントの例][OAuth リッチ クライアントの例]

### Marketplace API にアクセスするアプリケーション

このシナリオでは、Marketplace API にアクセスするアプリケーションを作成しています。Marketplace API の呼び出しを完了するには、認証が必要です。アプリケーションは Azure Marketplace にデプロイします。

![][12]

認証の実装の詳細については、Marketplace の発行キットを参照してください。

このシナリオを解決するには、以下のリソースを参照してください。

-   [アプリケーション発行キットのダウンロード][アプリケーション発行キットのダウンロード]
-   [アプリケーション用の Azure Marketplace の紹介][アプリケーション用の Azure Marketplace の紹介]

## セキュリティのヒント

このセクションでは、Windows Identity Foundation および Azure AD Access Control のセキュリティのヒントについて説明します。アプリケーションを設計およびデプロイするときに、テクノロジの基本的なセキュリティ チェック リストとして使用してください。

### Windows Identity Foundation

WIF に関する主なセキュリティのヒントは次のとおりです。この情報は、「[設計に関する考慮事項][設計に関する考慮事項]」および「[ASP.NET Web アプリケーションの Windows Identity Foundation (WIF) のセキュリティ – 脅威と対策][ASP.NET Web アプリケーションの Windows Identity Foundation (WIF) のセキュリティ – 脅威と対策]」から抜粋しました。

-   **IssuerNameRegistry**: 信頼済み Security Token Services (STS) を
    指定します。信頼済み STS のみがリストされていることを確認します。
-   **cookieHandler requireSsl="true"**: セッション Cookie を SSL
    プロトコルで転送することを指定します。
-   **wsFederation's requireHttps="true"**: ID プロバイダーとの
    フェデレーション プロトコル通信を SSL プロトコル経由で行うことを
    指定します。
-   **tokenReplayDetection enabled="true"**: トークン再生検出機能を
    有効にすることを指定します。この機能は、使用されたトークンの
    ローカル コピーの管理時にサーバー アフィニティを作成することに注意してください。
-   **audienceUris**トークンの想定されている読者を指定します。アプリケーションが
    想定しないトークンを受け取ると、WIF によって拒否され
    ます。
-   **requestValidation** および **httpRuntime requestValidationType**:
    ASP.NET 検証機能を有効または無効にします。「[Windows Identity
    Foundation (WIF): 危険な可能性のある Request.Form 値が
    クライアントから検出されました][Windows Identity
    Foundation (WIF): 危険な可能性のある Request.Form 値が
    クライアントから検出されました]」で説明されているガイダンスを参照してください。

### Azure AD Access Control

Azure AD Access Control のデプロイにおいては、次のセキュリティに関するヒントを検討してください。この情報は、「[ACS セキュリティ ガイドライン][ACS セキュリティ ガイドライン]」および「[証明書およびキーの管理ガイドライン][証明書およびキーの管理ガイドライン]」から抜粋しました。

-   **STS トークンの有効期限**。Azure AD Access Control の管理ポータルを
    使用して、トークンの有効期限を短く設定します。
-   **Error URL 機能の使用時にデータを検証する**。Azure
    AD Access Control の Error URL 機能では、エラー メッセージを送信する
    アプリケーションのページへの匿名アクセスが必要です。このページに届くすべてのデータは、
    信頼できないソースからの危険なデータであると想定します。
-   **機密性の高いシナリオに暗号化トークンを使用する**。トークン上で
    利用できる情報が漏えいする脅威を軽減するため、トークンを暗号化
    することを考慮してください。
-   **Azure にデプロイするときに RSA を使用して Cookie を暗号化する**。
    WIF は既定で DPAPI を使用して Cookie を暗号化します。Web ファーム
    および Azure 環境にデプロイするときに、サーバー アフィニティが
    作成され、例外が発生します。Web ファームや Azure のシナリオでは、
    RSA で代用します。
-   **トークン署名証明書**。サービス拒否を防ぐため、トークン
    署名証明書は定期的に更新してください。Azure AD Access Control は、発行する
    すべてのセキュリティ トークンに署名します。X.509 証明書は、ACS が
    発行する SAML トークンを使用するアプリケーションをビルドするときに
    署名のために使用されます。署名証明書の有効期限が切れると、
    トークンの要求時にエラーが発生します。
-   **トークン署名キー**。サービス拒否を防ぐため、トークン署名キーは
    定期的に更新してください。Azure AD Access Control は、発行する
    すべてのセキュリティ トークンに署名します。256 ビット対称署名キーは、ACS が
    発行する SWT トークンを使用するアプリケーションをビルドするときに署名のために
    使用されます。署名キーの有効期限が切れると、トークンの要求時にエラーが
    発生します。
-   **トークン暗号化証明書**。サービス拒否を防ぐため、
    トークン暗号化証明書は定期的に更新してください。トークン暗号化は、
    証明書利用者のアプリケーションが WS-Trust プロトコル上の
    proof-of-possession トークンを使用する Web サービスである場合に
    必須になります。それ以外の場合、トークン暗号化は任意です。暗号化証明書の
    有効期限が切れると、トークンの要求時にエラーが発生
    します。
-   **トークン復号化証明書**。サービス拒否を防ぐため、
    トークン復号化証明書は定期的に更新してください。Azure
    AD Access Control は WS-Federation ID プロバイダー (たとえば AD FS 2.0)
    の暗号化されたトークンを受け付けることができます。Azure AD Access Control で
    ホストされる X.509 証明書は、復号化に使用されます。
    復号化証明書の有効期限が切れると、トークンの要求時に
    エラーが発生します。
-   **サービス ID 資格情報**。サービス拒否を防ぐため、サービス ID
    資格情報は定期的に更新してください。サービス ID とは、
    Azure AD Access Control 名前空間でグローバルに構成される
    資格情報で、これを使ってアプリケーションまたはクライアントは
    Azure AD Access Control で直接認証してトークンを受け取ることが
    できます。Azure AD Access Control サービス ID に
    関連付けることのできる資格情報は 3 種類あります。対称キー、
    パスワード、および X.509 証明書です。資格情報の有効期限が
    切れると、例外を受け取ります。
-   **Azure AD Access Control 管理サービス アカウント
    資格情報**。サービス拒否を防ぐため、管理サービス資格情報は定期的に
    更新してください。Azure AD Access Control
    管理サービスでは、Azure AD Access Control 名前空間用の
    設定を、プログラムによって管理および構成でき
    ます。管理サービス アカウントに
    関連付けることのできる資格情報は 3 種類あります。それは、
    対称キー、パスワード、および X.509 証明書です。資格情報の
    有効期限が切れると、例外を受け取ります。
-   **WS-Federation ID プロバイダー署名証明書および
    暗号化証明書**。サービス拒否を防ぐため、WS-Federation ID
    プロバイダーの証明書の有効性を照会します。WS-Federation
    ID プロバイダー証明書は、そのメタデータを介して利用できます。
    AD FS などの WS-Federation ID プロバイダーの構成時に、
    WS-Federation 署名証明書は、URL の一部またはファイルとして利用できる
    WS-Federation メタデータを介して構成されます。WS-Federation
    ID プロバイダーを構成した後、Azure
    AD Access Control 管理サービスを使用してその証明書の有効性を
    照会します。証明書の有効期限が切れると、例外を受け取り
    ます。

## Azure Websites を使用する共有ホスティング

このトピックで説明したすべてのシナリオおよびソリューションは、Azure Websites でアプリケーションをホストする場合に有効です。

## Azure Virtual Machines

このトピックで説明したすべてのシナリオおよびソリューションは、Azure Virtual Machines でアプリケーションをホストする場合に有効です。

## リソース

-   [Identity Developer Training Kit][13]
-   [MSDN 提供の Identity Developer Training Course][14]
-   [要求ベースの ID およびアクセス制御のガイド][要求ベースの ID およびアクセス制御のガイド]
-   [Access Control Service][Access Control Service]
-   [ACS の利用方法][ACS の利用方法]
-   [Access Control Service v2.0 を使用して、Azure Web ロール ASP.NET Web アプリケーションをセキュリティ保護する][Access Control Service v2.0 を使用して、Azure Web ロール ASP.NET Web アプリケーションをセキュリティ保護する]
-   [Azure AD Access Control Service (ACS) Academy のビデオ][Azure AD Access Control Service (ACS) Academy のビデオ]
-   [Microsoft Security Development Lifecycle (Microsoft セキュリティ開発ライフサイクル)][Microsoft Security Development Lifecycle (Microsoft セキュリティ開発ライフサイクル)]
-   [SDL Threat Modeling Tool 3.1.8][SDL Threat Modeling Tool 3.1.8]
-   [セキュリティおよびプライバシーのブログ][セキュリティおよびプライバシーのブログ]
-   [Security Response Center (セキュリティ対応センター)][Security Response Center (セキュリティ対応センター)]
-   [Security Intelligence Report (セキュリティ インテリジェンス レポート)][Security Intelligence Report (セキュリティ インテリジェンス レポート)]
-   [Security Development Lifecycles (セキュリティ開発ライフサイクル)][Microsoft Security Development Lifecycle (Microsoft セキュリティ開発ライフサイクル)]
-   [セキュリティ デベロッパー センター (MSDN)][セキュリティ デベロッパー センター (MSDN)]

  [Azure セキュリティノート PDF]: http://blogs.msdn.com/b/jmeier/archive/2010/08/03/now-available-azure-security-notes-pdf.aspx
  [アプリケーションのセキュリティ保護]: ./media/SecurityRX/01_SecuringTheApplication.gif
  [一覧: Webアプリケーションのセキュリティ フレーム]: http://msdn.microsoft.com/ja-jp/library/ff649461.aspx
  [アプリケーション セキュリティ ガイダンス インデックス]: http://msdn.microsoft.com/ja-jp/library/ff650760.aspx
  [脅威、脆弱性、および攻撃]: ./media/SecurityRX/02_ThreatsVulnerabilitiesandAttacks.gif
  [Windows Identity Foundation 4.5 のサンプル]: http://code.msdn.microsoft.com/site/search?f%5B0%5D.Type=SearchText&f%5B0%5D.Value=wif&f%5B1%5D.Type=Topic&f%5B1%5D.Value=claims-based%20authentication
  [Visual Studio 11 ベータ向け Windows Identity Foundation 4.5 ツール]: http://visualstudiogallery.msdn.microsoft.com/e21bf653-dfe1-4d81-b3d3-795cb104066e
  [Windows Identity Foundation ランタイム (.Net 3.5/4.0)]: http://www.microsoft.com/ja-jp/download/details.aspx?id=17331
  [Windows Identity Foundation 3.5/4.0 サンプルおよび Visual Studio 2008/2010 テンプレート]: http://www.microsoft.com/ja-jp/download/details.aspx?displaylang=en&id=4451
  [Access Control Service 2.0]: http://msdn.microsoft.com/library/gg429786.aspx
  [ACS を使用したシナリオとソリューション]: http://msdn.microsoft.com/ja-jp/library/gg185920.aspx
  [ACS の利用方法]: http://msdn.microsoft.com/ja-jp/library/windowsazure/gg185939.aspx
  [A Guide to Claims-Based Identity and Access Control (クレームベースの ID およびアクセス制御)]: http://msdn.microsoft.com/ja-jp/library/ff423674.aspx
  [Identity Developer Training Kit]: http://www.microsoft.com/ja-jp/download/details.aspx?id=14347
  [MSDN 提供の Identity Developer Training Course]: http://msdn.microsoft.com/ja-jp/IdentityTrainingCourse
  [AD FS 2.0 Content Map (AD FS 2.0 コンテンツ マップ)]: http://social.technet.microsoft.com/wiki/contents/articles/2735.ad-fs-2-0-content-map.aspx
  [Web SSO の設計]: http://technet.microsoft.com/ja-jp/library/dd807033(WS.10).aspx
  [フェデレーション Web SSO の設計]: http://technet.microsoft.com/ja-jp/library/dd807050(WS.10).aspx
  [コンテナーと BLOB へのアクセス管理]: http://msdn.microsoft.com/ja-jp/library/ee393343.aspx
  [新しいストレージ機能:共有アクセス署名を使用します]: http://blog.smarx.com/posts/new-storage-feature-signed-access-signatures
  [Shared Access Signatures Are Easy These Days (最近使いやすくなった共有アクセス署名)]: http://blog.smarx.com/posts/shared-access-signatures-are-easy-these-days
  [Azure Active Directory Access Control]: ./media/SecurityRX/03_WindowsAzureADAccesscontrol.gif
  [方法: ACS を使用して最初の要求対応 ASP.NET アプリケーションを作成する]: http://msdn.microsoft.com/ja-jp/library/gg429779.aspx
  [方法: ASP.NET Web アプリケーションでログイン ページをホストする]: http://msdn.microsoft.com/ja-jp/library/gg185926.aspx
  [方法: WIF および ACS を使用して要求対応の ASP.NET アプリケーションで要求承認を実装する]: http://msdn.microsoft.com/ja-jp/library/gg185907.aspx
  [方法: WIF および ACS を使用して要求対応の ASP.NET アプリケーションでロール ベースのアクセス制御 (RBAC) を実装する]: http://msdn.microsoft.com/ja-jp/library/gg185914.aspx
  [方法: X.509 証明書を使用して ACS と ASP.NET Web アプリケーションの間の信頼を構成する]: http://msdn.microsoft.com/ja-jp/library/gg185947.aspx
  [サンプル コード: ASP.NET の単純フォーム]: http://msdn.microsoft.com/ja-jp/library/gg185938.aspx
  [WCF (SOAP) サービス]: ./media/SecurityRX/04_WCF(SOAP)Service.gif
  [方法: X.509 証明書、パスワード、または対称キーを使用してサービス ID を追加する]: http://msdn.microsoft.com/ja-jp/library/gg185924.aspx
  [方法: ACS で保護された WCF サービスに対してクライアント証明書を使用して認証する]: http://msdn.microsoft.com/ja-jp/library/hh289316.aspx
  [方法: ACS で保護された WCF サービスに対してユーザー名とパスワードを使用して認証する]: http://msdn.microsoft.com/ja-jp/library/gg185954.aspx
  [サンプル コード: WCF 証明書の認証]: http://msdn.microsoft.com/ja-jp/library/gg185952.aspx
  [サンプル コード: WCF ユーザー名認証]: http://msdn.microsoft.com/ja-jp/library/gg185927.aspx
  [AD を使用する WCF (SOAP) サービス]: ./media/SecurityRX/05_AzureADAccessControl.gif
  [方法: AD FS 2.0 を ID プロバイダーとして構成する]: http://msdn.microsoft.com/ja-jp/library/gg185961.aspx
  [方法: 管理サービスを使用して AD FS 2.0 をエンタープライズ IDプロバイダーとして構成する]: http://msdn.microsoft.com/ja-jp/library/gg185905.aspx
  [サンプル コード: AD FS 2.0 による WCF フェデレーション]: http://msdn.microsoft.com/ja-jp/library/hh127796.aspx
  [REST サービス]: ./media/SecurityRX/06_RESTService.gif
  [方法: 対称キーを使用して ACS と WCF サービスの間の信頼を構成する]: http://msdn.microsoft.com/ja-jp/library/gg185958.aspx
  [方法: ACS を使用して Azure にデプロイした REST WCF サービスを認証する]: http://msdn.microsoft.com/ja-jp/library/hh289317.aspx
  [サンプル コード: ASP.NET Web サービス]: http://msdn.microsoft.com/ja-jp/library/gg983271.aspx
  [Azure Access Control Service (ACS) によって発行される SWT トークンを使用する REST WCF]: http://code.msdn.microsoft.com/REST-WCF-With-SWT-Token-123d93c0
  [WIF はオプション]: ./media/SecurityRX/07_WIFisOptional.gif
  [方法: Google を ID プロバイダーとして構成する]: http://msdn.microsoft.com/ja-jp/library/gg185976.aspx
  [方法: Facebook を ID プロバイダーとして構成する]: http://msdn.microsoft.com/ja-jp/library/gg185919.aspx
  [方法: Yahoo! を ID プロバイダーとして構成する]: http://msdn.microsoft.com/ja-jp/library/gg185977.aspx
  [ASP.NET Web アプリケーション]: ./media/SecurityRX/08_ASPNETWebApptoREST.gif
  [共有 SWT トークンを使用する REST WCF サービス委任への ASP.NET Web アプリケーション]: http://code.msdn.microsoft.com/ASPNET-Web-App-To-REST-WCF-b2b95f82
  [0]: ./media/SecurityRX/09_RBAC.gif
  [1]: ./media/SecurityRX/10_WIFClaimsAuthenticationManager.gif
  [2]: ./media/SecurityRX/11_SecurityTokenRequriementmapping.gif
  [3]: ./media/SecurityRX/12_CustomRoleManager.gif
  [方法: 規則を使用してトークン変換ロジックを実装する]: http://msdn.microsoft.com/ja-jp/library/gg185955.aspx
  [要求対応 (WIF) ASP.NET Web アプリケーションに対する RoleManager による承認]: http://blogs.msdn.com/b/alikl/archive/2010/11/18/authorization-with-rolemanager-for-claims-aware-wif-asp-net-web-applications.aspx
  [Windows Identity Foundation SDK]: http://www.microsoft.com/downloads/details.aspx?FamilyID=c148b2df-c7af-46bb-9162-2c9422208504
  [4]: ./media/SecurityRX/13_ClaimsAuthorizationManager.gif
  [5]: ./media/SecurityRX/14_WindowsAzurestorage.gif
  [6]: ./media/SecurityRX/15_SQLAzureIdentityandAccessScenarios.gif
  [セキュリティのガイドラインと制限事項 (SQL データベース)]: http://msdn.microsoft.com/ja-jp/library/windowsazure/ff394108.aspx#authentication
  [方法: sqlcmd を使用して SQL データベースに接続する]: http://msdn.microsoft.com/ja-jp/library/windowsazure/ee336280.aspx
  [方法: ADO.NET を使用して SQL データベースに接続する]: http://msdn.microsoft.com/ja-jp/library/windowsazure/ee336243.aspx
  [方法: ASP.NET で SQL データベースに接続する]: http://msdn.microsoft.com/ja-jp/library/windowsazure/ee621781.aspx
  [方法: WCF Data Services で SQL データベースに接続する]: http://msdn.microsoft.com/ja-jp/library/windowsazure/ee621789.aspx
  [方法: PHP を使用して SQL データベースに接続する]: http://msdn.microsoft.com/ja-jp/library/windowsazure/ff394110.aspx
  [方法: JDBC を使用して SQL データベースに接続する]: http://msdn.microsoft.com/ja-jp/library/windowsazure/gg715284.aspx
  [方法: ADO.NET Entity Framework を使用して SQL データベースに接続する]: http://msdn.microsoft.com/ja-jp/library/windowsazure/ff951633.aspx
  [7]: ./media/SecurityRX/16_WindowsAzureServiceBusIdentity.gif
  [ACS による Service Bus のセキュリティ保護]: http://channel9.msdn.com/posts/Securing-Service-Bus-with-ACS
  [8]: https://skydrive.live.com/view.aspx?cid=123CCD2A7AB10107&resid=123CCD2A7AB10107%211849
  [Service Bus 認証と Access Control サービスによる認証]: http://msdn.microsoft.com/ja-jp/library/hh403962.aspx
  [9]: ./media/SecurityRX/17_WindowsAzureCacheIdentity.gif
  [方法: プログラムによってキャッシュ クライアントを構成する(Azure Shared Caching)]: http://msdn.microsoft.com/ja-jp/library/windowsazure/gg618003.aspx
  [方法: アプリケーション構成ファイルを使用してキャッシュ クライアントを構成する (Azure Shared Caching)]: http://msdn.microsoft.com/ja-jp/library/windowsazure/gg278346.aspx
  [Windows Azure AppFabric のサンプル]: http://msdn.microsoft.com/ja-jp/library/ee706741.aspx
  [10]: ./media/SecurityRX/18_IAccessMyDataset.gif
  [Marketplace アプリケーションでの HTTP 基本認証の実装]: http://msdn.microsoft.com/ja-jp/library/gg193417.aspx
  [11]: ./media/SecurityRX/19_UsersAccessMyDatasets.gif
  [OAuth Web クライアントの例]: http://go.microsoft.com/fwlink/?LinkId=219162
  [OAuth リッチ クライアントの例]: http://go.microsoft.com/fwlink/?LinkId=219163
  [12]: ./media/SecurityRX/20_ApplicationAccessMarketplaceAPI.gif
  [アプリケーション発行キットのダウンロード]: http://go.microsoft.com/fwlink/?LinkId=221323
  [アプリケーション用の Azure Marketplace の紹介]: https://datamarket.azure.com/
  [設計に関する考慮事項]: http://msdn.microsoft.com/ja-jp/library/ee517298.aspx
  [ASP.NET Web アプリケーションの Windows Identity Foundation (WIF) のセキュリティ – 脅威と対策]: http://blogs.msdn.com/b/alikl/archive/2010/12/02/windows-identity-foundation-wif-security-for-asp-net-web-applications-threats-amp-countermeasures.aspx
  [ACS セキュリティ ガイドライン]: http://msdn.microsoft.com/ja-jp/library/gg185962.aspx
  [証明書およびキーの管理ガイドライン]: http://msdn.microsoft.com/ja-jp/library/hh204521.aspx
  [13]: http://go.microsoft.com/fwlink/?LinkId=214555
  [14]: http://go.microsoft.com/fwlink/?LinkId=214561
  [要求ベースの ID およびアクセス制御のガイド]: http://go.microsoft.com/fwlink/?LinkId=214562
  [Access Control Service]: http://msdn.microsoft.com/ja-jp/library/windowsazure/gg429786.aspx
  [Access Control Service v2.0 を使用して、Azure Web ロール ASP.NET Web アプリケーションをセキュリティ保護する]: http://social.technet.microsoft.com/wiki/contents/articles/2590.aspx
  [Azure AD Access Control Service (ACS) Academy のビデオ]: http://social.technet.microsoft.com/wiki/contents/articles/2777.aspx
  [Microsoft Security Development Lifecycle (Microsoft セキュリティ開発ライフサイクル)]: http://www.microsoft.com/security/sdl/default.aspx
  [SDL Threat Modeling Tool 3.1.8]: http://www.microsoft.com/download/en/details.aspx?displaylang=en&id=2955
  [セキュリティおよびプライバシーのブログ]: http://www.microsoft.com/about/twc/en/us/blogs.aspx
  [Security Response Center (セキュリティ対応センター)]: http://www.microsoft.com/security/msrc/default.aspx
  [Security Intelligence Report (セキュリティ インテリジェンス レポート)]: http://www.microsoft.com/security/sir/
  [セキュリティ デベロッパー センター (MSDN)]: http://msdn.microsoft.com/security/
