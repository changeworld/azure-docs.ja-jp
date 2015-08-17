<properties 
	pageTitle="自分の組織内で使用される承認されていないクラウド アプリを検出する方法" 
	description="このトピックでは、Cloud App Discvery とは何かと、これを使用する理由について説明します。" 
	services="active-directory" 
	documentationCenter="" 
	authors="markusvi" 
	manager="swadhwa" 
	editor="lisatoft"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/23/2015" 
	ms.author="markusvi"/>

# 自分の組織内で使用される承認されていないクラウド アプリを検出する方法

現代の企業では、IT 部門が、ユーザーが作業のために使用しているすべてのクラウド アプリケーションを認識できていないことがよくあります。この結果、多くの場合、管理者は、企業のデータに対する不正アクセス、データ漏えいの可能性や、アプリケーションに内在するその他のセキュリティ リスクに関連する問題を抱えています。使用されるアプリの数や種類が分からないために、これらのリスクに対処する計画立案を開始することさえ、困難を極めていると思われます。

これらの問題には、Cloud App Discovery を使用することで対応できます。Cloud App Discovery は、Azure Active Directory のプレミアム機能で、組織の従業員が使用するクラウド アプリケーションを検出することができます。


**Cloud App Discovery を使用すると次のことができます。**

* 使用されているアプリケーションを検出し、ユーザー数、トラフィック量、アプリケーションに対する Web 要求数別に使用状況を測定する 
* アプリケーションを使用しているユーザーを特定する 
* 追加のオフライン分析用にデータをエクスポートする 
* IT 管理下に置くアプリケーションに優先度を付け、アプリケーションを統合してシングル サインオンおよびユーザー管理を容易に有効化する 

Cloud App Discovery では、データの取得は、お使いの環境のコンピューター上で実行されるエージェントで行われます。エージェントによってキャプチャされるアプリの使用状況情報は、セキュリティで保護され、暗号化されたチャネル経由で Cloud App Discovery サービスに送信されます。Cloud App Discovery サービスでは、データが評価され、環境の分析に使用できるレポートが生成されます。


<center>![How Cloud App Discovery Works](./media/active-directory-cloudappdiscovery/cad01.png)</center>

##次のステップ


* Cloud App Discovery の動作の詳細については、[Cloud App Discovery の概要に関するページ](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx)をご覧ください。 
* セキュリティとプライバシーの考慮事項については、「[Cloud App Discovery のセキュリティとプライバシーの考慮事項](active-directory-cloudappdiscovery-security-and-privacy-considerations.md)」をご覧ください。 
* エンタープライズ環境に Cloud App Discovery エージェントをデプロイする方法の詳細について: 
 * Active Directory のグループ ポリシーの管理については、[Cloud App Discovery のグループ ポリシーのデプロイ ガイドに関するページ](http://social.technet.microsoft.com/wiki/contents/articles/30965.cloud-app-discovery-group-policy-deployment-guide.aspx)をご覧ください。 
 * Microsoft System Center Configuration Manager については、[Cloud App Discovery の System Center デプロイ ガイドに関するページ](http://social.technet.microsoft.com/wiki/contents/articles/30968.cloud-app-discovery-system-center-deployment-guide.aspx)をご覧ください。 
 * カスタム ポートを使用するプロキシ サーバーについては、[Cloud App Discovery のカスタム ポートを使用するプロキシ サービス用レジストリ設定に関するページ](active-directory-cloudappdiscovery-registry-settings-for-proxy-services.md)をご覧ください。 





**その他のリソース**


* [Cloud App Discovery: エージェントの変更ログ](http://social.technet.microsoft.com/wiki/contents/articles/24616.cloud-app-discovery-agent-changelog.aspx)
* [Cloud App Discovery: よく寄せられる質問](http://social.technet.microsoft.com/wiki/contents/articles/24037.cloud-app-discovery-frequently-asked-questions.aspx)

<!---HONumber=August15_HO6-->