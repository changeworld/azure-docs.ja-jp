<properties 
	pageTitle="エンタープライズ マルチチャネル アプリ" 
	description="オンプレミスのリソースとクラウドベースのソフトウェア サービスにまたがるマルチチャネル アプリの概要。" 
	services="app-service" 
	documentationCenter="na" 
	authors="stefsch" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/23/2015" 
	ms.author="stefsch"/>

# 企業向けマルチチャネル アプリケーションの作成

## 概要

エンタープライズ マルチチャネル アプリは、複数のテクノロジを使用して顧客にデータを提示します。企業の Web、モバイル、API ユーザーはすべて、さまざまなソースの情報を取得および処理します。これらのソースは、オンプレミスで実行される内部システムだけでなく、クラウド ベースのサービスにまで広がっています。

また、エンタープライズ アプリケーションには、セキュリティで保護されたアクセスが必要です。企業の直接管理の下で、従業員とビジネス パートナーはセキュリティで保護された ID を使用してデータに接続します。

Azure App Service (AAS) で実行されるエンタープライズ アプリケーションは、これらの機能をすべて提供することができます。

次に示す従業員出張アプリケーションの例では、Azure Active Directory (AAD) によってセキュリティで保護されたエンタープライズ マルチチャネル アプリケーションを示します。このアプリケーションは、オンプレミスのリソースに加え、Office 365 や Salesforce などのサービスとしてのソフトウェア (SaaS) とも統合されています。

## <a name="acceptablefiles"></a>Azure Active Directory によるアクセス制御

出張アプリケーションのユーザーは、最初に、企業の Active Directory に対して認証する必要があります。次のいくつかの手順を実行して、Azure Active Directory (AAD) を使用するようにアプリケーションを構成しました。

* この企業の Azure Active Directory を作成しました。
* 企業のオンプレミスの Active Directory を Azure Active Directory と連携させました。
* 最後に、Azure App Service の簡単な AAD 統合機能を使用して、アプリケーションを AAD に登録しました。 

その結果、ユーザーは、AAD (さらに、企業の AD) に対してログインするように求められます。
	
![AAD ログイン][AADLogin]

AAS を AAD と統合するための設定の詳細については、[AAS の AAD との統合][AASIntegrationwithAAD]に関するブログ記事を参照してください。

## <a name="acceptablefiles"></a>オンプレミスのリソースへのアクセス

ユーザーが AAD に対してログインすると、計画されている出張の一覧が表示されます。Web アプリケーションは Microsoft Azure で実行されているため、この情報が含まれているオンプレミスの SQL Server にアクセスする方法が必要です。

![オンプレミスの SQL Server のデータ][DatafromOnpremisesSqlServer]

アプリケーションは、ポイント対サイト VNET 統合機能で構成されています。これにより、アプリケーションは、標準のデータ アクセス ロジック (この場合は Entity Framework) を使用して企業ネットワークの内部で実行されているリモートの SQL Server に透過的にアクセスできます。

ポイント対サイト VNET 統合の詳細については、[VNET 統合][VNETIntegration]に関するブログ記事を参照してください。

## <a name="acceptablefiles"></a>Office 365 との統合

この Web アプリケーションでは、従業員が出張レコードを作成するたびに、出張の要求が SharePoint Online の一覧に作成されます。アプリケーションには、従業員が簡単にこの Sharepoint の一覧にアクセスできるリンクがあります。

![SharePoint の一覧のリンク][SharepointListLink]

リンクをクリックすると、SharePoint の一覧に自動的に移動します。従業員は自社の AAD に対してログインしているため、AAD が発行したセキュリティ トークンを使用して Office 365 に対して透過的に認証されます。

![SharePoint の一覧][SharepointList]

また、Sharepoint Online のドキュメント ライブラリに出張書類も作成されます。

![SharePoint ドキュメント ライブラリ][SharepointDocumentLibrary]

Web アプリケーションでは、SharePoint Online に作成された資産を通じて Office 365 の機能を活用することができます。たとえば、Sharepoint の一覧に項目が作成されるたびに、意思決定と承認のワークフローを開始することができます。

Office 365 との統合の詳細については、「[Office 365 の統合][Office365Integration]」を参照してください。

## <a name="acceptablefiles"></a>SaaS サービスとの統合

今日の企業では、さまざまな SaaS サービスを使用しており、他のアプリケーションの SaaS データと統合する必要があります。出張アプリケーションは、このシナリオの一例を示しています。このアプリケーションでは、従業員が顧客アカウントへの出張を計画するたびに、Salesforce の顧客アカウントが更新されます。

![Salesforce の統合][SalesforceIntegration]

企業の Salesforce アカウントは、AAD を使用して、AAD の資格情報による Salesforce に対する透過的な認証を許可するよう設定されています。そのため、従業員が AAD を使用して出張アプリケーションにログインすると、アプリケーションから Salesforce に格納されているデータの読み取りと書き込みを実行できるようになります。

SaaS との統合の詳細については、「[SaaS の統合][SaaSIntegration]」を参照してください。

## <a name="NextSteps"></a>次のステップ

詳細については、「[Azure アプリケーション サービス][AzureApplicationServices]」を参照してください。
 
[AASIntegrationwithAAD]: http://azure.microsoft.com/blog/2014/11/13/azure-websites-authentication-authorization/
[VNETIntegration]: http://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/
[Office365Integration]: http://azure.microsoft.com/documentation/articles/app-service-logic-connector-office365/
[SaaSIntegration]: http://azure.microsoft.com/documentation/articles/app-service-logic-connector-salesforce/
[AzureApplicationServices]: ../app-service-cloud-app-platform.md

[AADLogin]: ./media/app-service-enterprise-multichannel-apps/01aAADLogin.png
[DatafromOnpremisesSqlServer]: ./media/app-service-enterprise-multichannel-apps/02aDatafromOnpremisesSqlServer.png
[SharepointListLink]: ./media/app-service-enterprise-multichannel-apps/03aSharepointListLink.png
[SharepointList]: ./media/app-service-enterprise-multichannel-apps/04aSharepointList.png
[SharepointDocumentLibrary]: ./media/app-service-enterprise-multichannel-apps/05aSharepointDocumentLibrary.png
[SalesforceIntegration]: ./media/app-service-enterprise-multichannel-apps/06aSalesforceIntegration.png

<!---HONumber=July15_HO4-->