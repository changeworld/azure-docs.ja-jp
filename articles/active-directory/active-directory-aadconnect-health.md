<properties 
	pageTitle="クラウド内のオンプレミスの ID インフラストラクチャの監視" 
	description="これは、Azure AD Connct Health の内容と使用する理由を説明するページです。" 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/28/2015" 
	ms.author="billmath"/>

# クラウド内のオンプレミスの ID インフラストラクチャの監視

Azure AD Connect Health では、オンプレミスの ID インフラストラクチャを監視し、分析することができます。アラート、パフォーマンス、使用パターン、構成設定を表示する機能があり、Office 365 に対して信頼性の高い接続を維持するなどさまざまなことができます。これらは、対象のサーバーにインストールされているエージェントを使用して実現されます。詳細については [MSDN の Azure AD Connect Health](https://msdn.microsoft.com/library/azure/dn906722.aspx) のページを参照してください。

![Azure AD Connect Health とは](./media/active-directory-aadconnect-health/aadconnecthealth2.png)


この情報は、すべて Azure AD Connect Health ポータルで参照できます。Azure AD Connect Health ポータルを使用すると、アラート、パフォーマンスの監視、および使用状況の分析を表示できます。この情報はすべて 1 つの使いやすい場所に表示されるので、必要な情報を探して時間を無駄にすることはありません。

![Azure AD Connect Health とは](./media/active-directory-aadconnect-health/usage.png)

Azure AD Connect Health の今後の更新には、監視の追加と、他の ID コンポーネントやサービス (Azure AD Connect の同期サービスなど) の分析が含まれる予定です。このように ID というレンズを通した単一のダッシュボードが提供されることで、より堅牢で健全な統合環境を設定できるようになります。ユーザーはこの環境を利用して作業能力を向上させることができます。

詳細については [MSDN の Azure AD Connect Health](https://msdn.microsoft.com/library/azure/dn906722.aspx) のページを参照してください。


![Azure AD Connect Health とは](./media/active-directory-aadconnect-health/logo1.png)




## Azure AD Connect Health を使用する理由

オンプレミスのディレクトリと Azure AD を統合すると、クラウドとオンプレミス両方のリソースに共通の ID が提供されるため、ユーザーの生産性が向上します。ただし、この統合には課題があります。ユーザーが任意のデバイスからオンプレミスとクラウド両方のリソースに安全にアクセスできるように、環境を健全に保つ必要があります。Azure AD Connect Health では、Office 365 や他の Azure AD アプリケーションにアクセスするときに使用するオンプレミスの ID インフラストラクチャを、簡単なクラウドベースのアプローチで監視し分析することができます。オンプレミスの各 ID サーバーにエージェントをインストールするぐらいにシンプルです。

Azure AD Connect Health for AD FS は、Windows Server 2008/2008 R2 の AD FS 2.0 と Windows Server 2012/2012R2 の AD FS をサポートします。これらには、エクストラネット アクセスの認証をサポートする AD FS プロキシまたは Web アプリケーション プロキシ サーバーも含まれます。Azure AD Connect Health for AD FS の主な機能セットは次のとおりです。

- Azure AD などの AD FS が保護するアプリケーションへの信頼性の高いアクセスを保証するためにアラートを表示し対処する
- 重大なアラートの電子メール通知を送信する
- 容量計画を決定するパフォーマンス データを表示する
- 異常を判定するため、または容量計画のベースラインを確立するために AD FS ログイン パターンを詳細に表示する



----------------------------------------------------------------------------------------------------------
## Azure AD Connect Health エージェントのダウンロード

Azure AD Connect Health の使用を開始するには、こちら ([Azure AD Connect Health エージェントのダウンロード](http://go.microsoft.com/fwlink/?LinkID=518973)) から最新のバージョンのエージェントをダウンロードします。 エージェントをインストールする前に、Marketplace からサービスを追加したことを確認します。

----------------------------------------------------------------------------------------------------------





**その他のリソース**

* [MSDN の Azure AD Connect Health](https://msdn.microsoft.com/library/azure/dn906722.aspx)


 

<!---HONumber=62-->