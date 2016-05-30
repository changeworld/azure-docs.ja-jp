<properties 
	pageTitle="Azure API Management の FAQ | Microsoft Azure" 
	description="Azure API Management についてよく寄せられる質問の回答、パターン、ベスト プラクティスについて説明します。" 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/28/2016" 
	ms.author="sdanie"/>

# Azure API Management の FAQ

Azure API Management についてよく寄せられる質問の回答、パターン、ベスト プラクティスについて説明します。

## よく寄せられる質問

-	[API Management チームに質問するにはどうすればよいですか。](#how-can-i-ask-a-question-to-the-api-management-team)
-	[機能がプレビュー段階とはどういう意味ですか。](#what-does-it-mean-if-a-feature-is-in-preview)
-	[API Management ゲートウェイとバックエンド サービス間の接続をセキュリティで保護する場合、どのような方法がサポートされていますか。](#what-are-the-supported-options-to-secure-the-connection-between-the-api-management-gateway-and-my-backend-services)
-	[API Management インスタンスを新しいインスタンスにコピーするにはどうすればよいですか。](#how-can-i-copy-an-api-management-instance-to-a-new-instance)
-	[API Management インスタンスはプログラムで管理できますか。](#can-i-manage-my-api-management-instance-programmatically)
-	[ユーザーを Administrators グループに追加するにはどうすればよいですか。](#how-can-i-add-a-user-to-the-administrators-group)
-	[追加するポリシーがポリシー エディターで有効になっていないのはなぜですか。](#why-is-the-policy-that-i-want-to-add-not-enabled-in-the-policy-editor)
-	[API Management で API のバージョン管理を実現するにはどうすればよいですか。](#how-can-i-achieve-api-versioning-with-api-management)
-	[サンドボックスや実稼働など、API の複数の環境を構成するにはどうすればよいですか。](#how-can-i-configure-multiple-environments-of-apis-for-example-sandbox-and-production)
-	[SOAP は API Management でサポートされていますか。](#is-soap-supported-in-api-management)
-	[API Management ゲートウェイ IP アドレスは一定ですか。 それをファイアウォール ルールで使用できますか。](#is-the-api-management-gateway-ip-address-constant-can-i-use-it-in-firewall-rules)
-	[ADFS セキュリティを使用して OAuth 2.0 認証サーバーを構成できますか。](#can-i-configure-an-oauth-20-authorization-server-with-adfs-security)
-	[複数の地理的な場所にデプロイされた場合、API Management はどのようなルーティング方法を使用しますか。](#what-routing-method-does-api-management-use-when-deployed-to-multiple-geographic-locations)
-	[ARM テンプレートを使用して API Management サービス インスタンスを作成できますか。](#can-i-create-an-api-management-service-instance-using-an-arm-template)



### API Management チームに質問するにはどうすればよいですか。

-	質問を [API Management MSDN フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=azureapimgmt)に投稿できます。
-	質問を電子メール (`apimgmt@microsoft.com`) でお送りいただけます。
-	[機能についてのご要望](https://feedback.azure.com/forums/248703-api-management)をお送りいただけます。

### 機能がプレビュー段階とはどういう意味ですか。

プレビュー段階の機能は機能的には完成していますが、Microsoft ではこの機能に関するフィードバックを積極的に求めているため、プレビュー段階となっています。お客様からのフィードバックに応じて重大な変更を加える可能性があるため、機能によっては実稼動環境での使用はお勧めしません。プレビュー段階の機能に関するご意見やご感想があれば、「[API Management チームに質問するにはどうすればよいですか。](#how-can-i-ask-a-question-to-the-api-management-team)」に記載されているいずれかの方法でお知らせください。

### API Management ゲートウェイとバックエンド サービス間の接続をセキュリティで保護する場合、どのような方法がサポートされていますか。

複数の方法がサポートされています。

1. HTTP 基本認証を使用します。詳細については、「[API 設定の構成](api-management-howto-create-apis.md#configure-api-settings)」を参照してください。
2. 「[Azure API Management でクライアント証明書認証を使用してバックエンド サービスを保護する方法](api-management-howto-mutual-certificates.md)」の説明に従って、SSL 相互認証を使用します。
3. バックエンド サービスで IP ホワイトリストを使用します。Standard または Premium レベルの API Management インスタンスを使用している場合、ゲートウェイの IP アドレスが変わることはないので、この IP アドレスを許可するようにホワイトリストを構成できます。API Management インスタンスの IP アドレスは、Azure クラシック ポータルの**ダッシュボード**で取得できます。
4. API Management インスタンスを Azure Virtual Network (クラシック) に接続できます。詳細については、「[Azure API Management で VPN 接続を設定する方法](api-management-howto-setup-vpn.md)」を参照してください。

### API Management インスタンスを新しいインスタンスにコピーするにはどうすればよいですか。

API Management サービス インスタンスは、いくつかの方法で新しいインスタンスにコピーできます。

-	API Management のバックアップと復元機能を使用します。詳細については、「[Azure API Management でサービスのバックアップと復元を使用して障害復旧を実装する方法](api-management-howto-disaster-recovery-backup-restore.md)」を参照してください。
-	[API Management REST API](https://msdn.microsoft.com/library/azure/dn776326.aspx) を使用して独自のバックアップと復元機能を作成し、サービス インスタンスの目的のエンティティを保存して復元します。
-	Git を使用してサービス構成をダウンロードし、新しいインスタンスにアップロードしてバックアップします。詳細については、「[Git を使用して API Management サービス構成を保存および構成する方法](api-management-configuration-repository-git.md)」を参照してください。

### API Management インスタンスはプログラムで管理できますか。

はい。[API Management REST API](https://msdn.microsoft.com/library/azure/dn776326.aspx) と、[Microsoft Azure API Management Service Management Library SDK](http://aka.ms/apimsdk) と、[サービス デプロイ](https://msdn.microsoft.com/library/mt619282.aspx)および[サービス管理用の PowerShell コマンドレット](https://msdn.microsoft.com/library/mt613507.aspx)を使用して管理できます。

### ユーザーを Administrators グループに追加するにはどうすればよいですか。

以下の手順に従って実現できます。

1. 新しい [Azure ポータル](https://portal.azure.com)にログインします 
2. 目的の API Management インスタンスを含むリソース グループに移動します
3. 目的のユーザーを "API Management サービス共同作成者" ロールに追加します

それが終わると、新しく追加した共同作業者は Azure PowerShell [コマンドレット](https://msdn.microsoft.com/library/mt613507.aspx)を使用して管理者としてログインできます。

1. `Login-AzureRmAccount` コマンドレットを使用してログインします
2. `Set-AzureRmContext -SubscriptionID <subscriptionGUID>` を使用して、サービスを含むサブスクリプションへのコンテキストを設定します
3. `Get-AzureRmApiManagementSsoToken -ResourceGroupName <rgName> -Name <serviceName>` を使用して SSO トークンを取得します
4. URL をコピーしてブラウザーに貼り付けると、ユーザーは管理ポータルにアクセスできます


### 追加するポリシーがポリシー エディターで有効になっていないのはなぜですか。

追加するポリシーが有効になっていない場合、そのポリシーの正しいスコープが選択されていることを確認します。各ポリシー ステートメントは、特定のスコープおよびポリシー セクション内で使用するように設計されています。ポリシーのポリシー セクションとスコープを確認するには、[ポリシー リファレンス](https://msdn.microsoft.com/library/azure/dn894080.aspx) ページでそのポリシーの**使用例**を参照してください。


### API Management で API のバージョン管理を実現するにはどうすればよいですか。

-	API Management では、バージョンが異なる API を個別に構成できます。たとえば、2 つの異なる API として `MyAPI v1` と `MyAPI v2` を用意することができます。開発者は使用するバージョンを選択できます。
-	バージョン セグメントを含まないサービス URL で API を構成することもできます (たとえば、`https://my.api`)。この場合、各操作の[書き換え URL](https://msdn.microsoft.com/library/azure/dn894083.aspx#RewriteURL) テンプレートでバージョンのセグメントを構成することができます。たとえば、`/resource` の [URL テンプレート](api-management-howto-add-operations.md#url-template)と `/v1/Resource` の [書き換え URL](api-management-howto-add-operations.md#rewrite-url-template) テンプレートで操作を作成することができます。そうすれば、各操作で個別にバージョン セグメントの値を変更できます。
-	API のサービス URL 内に "既定" のバージョン セグメントを保持する場合は、選択した操作に対して、[バックエンド サービスの設定](https://msdn.microsoft.com/library/azure/dn894083.aspx#SetBackendService)ポリシーを使用してバックエンド要求パスを変更するポリシーを設定できます。

### サンドボックスや実稼働など、API の複数の環境を構成するにはどうすればよいですか。

この時点では、次のオプションがあります。

-	同じテナントで個別の API をホストすることができます。
-	それぞれ異なるテナントで同じ API をホストすることができます。

### SOAP は API Management でサポートされていますか。

現在、Azure API Management での SOAP のサポートは制限されています。これは現在調査中の機能です。私共では、貴社の顧客の WSDL のサンプルを取得すること、貴社の顧客が必要としている機能について情報を得ることに非常に関心があります。それは思考の形成に役立つからです。「[API Management チームに質問するにはどうすればよいですか。](#how-can-i-ask-a-question-to-the-api-management-team)」に記載されている連絡先にお問い合わせください。

この機能を動作させる必要がある場合は、弊社コミュニティで推奨されている回避策を参照してください (「[Azure API Management - APIM, consuming a SOAP WCF service over HTTP (Azure API Management - APIM、HTTP を介した SOAP WCF サービスの使用)](http://mostlydotnetdev.blogspot.com/2015/03/azure-api-management-apim-consuming.html)」)。

その方法でソリューションを実装する場合、ポリシーをいくらか手動で構成する必要があるほか、WSDL インポート/エクスポートはサポートされず、また、ユーザーは開発者ポータルでテスト コンソールを使用して発行される要求の本体を形成する必要があります。

### API Management ゲートウェイ IP アドレスは一定ですか。 それをファイアウォール ルールで使用できますか。

Standard レベルと Premium レベルで、API Management テナントのパブリック IP アドレス (VIP) は、テナントの有効期間中、静的です。ただし、以下に示したようないくつかの例外があります。複数リージョンのデプロイ用に構成される Premium レベルのテナントには、リージョンごとに 1 つのパブリック IP アドレスが割り当てられることに注意してください。

IP アドレスは次の状況で変化します。

-	サービスが削除され再作成された
-	サービスのサブスクリプションが中断され (たとえば、未払いのため)、復元された
-	VNET が追加または削除された (VNET は Premium レベルでのみサポート)
-	リージョン アドレスはリージョンが空になり、再入力された場合に変化する (複数リージョンのデプロイは Premium レベルでのみサポート)

IP アドレス (複数リージョンのデプロイの場合は複数個) は Azure クラシック ポータルのテナント ページにあります。

### ADFS セキュリティを使用して OAuth 2.0 認証サーバーを構成できますか。

このシナリオの構成方法の詳細については、「[Using ADFS in API Management (API Management での ADFS の使用)](https://phvbaars.wordpress.com/2016/02/06/using-adfs-in-api-management/)」を参照してください。

### 複数の地理的な場所にデプロイされた場合、API Management はどのようなルーティング方法を使用しますか。 

API Management では[パフォーマンスによるトラフィック ルーティング方法](../traffic-manager/traffic-manager-routing-methods.md#performance-traffic-routing-method)が使用されます。着信トラフィックは、最も近い API ゲートウェイにルーティングされます。1 つのリージョンがオフラインになった場合、着信トラフィックは自動的に次に最も近いゲートウェイにルーティングされます。ルーティング方法の詳細については、「[Traffic Manager のルーティング方法](../traffic-manager/traffic-manager-routing-methods.md)」を参照してください。

### ARM テンプレートを使用して API Management サービス インスタンスを作成できますか。

はい、「[Azure API Management Service](http://aka.ms/apimtemplate)」クイックスタート テンプレートを参照してください。

<!---HONumber=AcomDC_0518_2016-->