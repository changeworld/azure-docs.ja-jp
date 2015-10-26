<properties
	pageTitle="Mobile Services の使用時に App Service はどのように役立つか"
	description="既存の Mobile Services プロジェクトに App Service がもたらす利点について説明します。"
	services="app-service\mobile"
	documentationCenter="ios"
	authors="kirillg"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/05/2015"
	ms.author="kirillg"/>

# <a name="getting-started"> </a>Mobile Services の使用時に App Service はどのように役立つか

##概要
既存の Mobile Service は安全で、引き続きサポートされます。ただし、*Azure App Service* プラットフォームでモバイル アプリ向けに提供される機能の一部は、現在の Mobile Services では利用できません。

- Web クライアントとモバイル クライアントの両方を含むアプリを、よりシンプルで簡単なコスト効率の良いものに
- Web ジョブ、カスタム CName、進化した監視を含む新しいホスト機能
- Office 365、Dynamics CRM、Salesforce やその他の重要な SaaS API との設定不要の統合
- Node.js と .NET だけでなく、Java や PHP のバックエンド コードもサポート
- Traffic Manager との設定不要の統合
- ハイブリッド接続に加えて、VNet を使用したオンプレミス リソースと VPN への接続
- 警告だけでなく、NewRelic や AppInsights も使用したアプリの監視とトラブルシューティング
- VM サイズなど、基盤となるコンピューティング リソースがより広範囲に
- 組み込みの自動スケール、負荷分散、パフォーマンス監視
- 組み込みのステージング、バックアップ、ロールバック、運用環境でのテスト機能

## 新しいホスト機能
*Azure App Service* では、*モバイル アプリ* バックエンド コードは Web アプリや API アプリと同じコンテナーで実行されます。したがって、このコンテナーの機能は、現在 Mobile Services にはないものも含めて、すべて利用することができます。

- Web ジョブを使用して、実行中のバックエンド ロジックを継続的に追加する
- バックエンド コードが常に実行されていることを確認する
- カスタムの CNames を使用して、使いやすくて安定した名前をモバイル バックエンド エンドポイントに提供する
- Traffic Manager を使用してアプリを地球スケールにする
- 任意のライブラリとパッケージが含まれます。.NET では、デプロイするアセンブリはランタイムで使用されるアセンブリです。ホスト環境のバージョンとの競合はありません。
- (.NET の場合) MVC などの ASP.NET の任意の機能を活用します。


##SaaS API への*モバイル アプリ*の接続
*Azure App Service* によって、モバイル アプリを Office 365、Dynamics、Salesforce、SAP などの SaaS API に簡単に接続できるようになります。*Azure App Service* は、ユーザーの代わりに設定不要の認証を提供し、プライマリ ID を使用して個々の SaaS API のトークンを関連付けることによって、使用するすべての SaaS API で真のシングル サインオンを実行できるようにします。

##VNet を使用したオンプレミス データへのアクセス
Mobile Services により、現在既にハイブリッド接続を使用してオンプレミス リソースにアクセスすることができます。ただし、VPN ソリューションの方が望ましい場合もあります。*Azure App Service* により、モバイル アプリのバックエンド コードに Azure VNet を使用できます。

##好みのバックエンド言語の使用
*Azure App Service* では、Mobile Services でサポートされている .NET と Node.js に加えて、Java、PHP、Python を含む開発者プラットフォームに広範で豊富なサポートを提供します。

##自動スケールの設定
Mobile services では、バックエンド コードのすべてのインスタンスが S VM で実行されていました。*Azure App Service* では、より豊富なオプション セットから VM のサイズを選択することができます。また、さまざまなパフォーマンス メトリックに基づいて、すばやくスケールアップまたはスケールアウトして顧客の受信負荷を処理することもできます。

##状況の把握
監視と警告を使用して問題にリアルタイムで対応し、自動的に担当者やチームに通知します。高度なアプリ分析と New Relic や AppInsights の監視機能を統合して、モバイル アプリの実行状況に対する洞察を深めます。*Azure App Service* を使用することで、さまざまなパフォーマンス メトリックに基づき、プログラムやポータルを使用して警告を設定できるようになりました。

##資産の安全性の保持
バックエンドとデータベースを自動的にバックアップします。コードとデータは災害からセキュリティ保護されていて簡単に復元できるため、安心して業務を遂行することができます。

##準備、ステージング、運用開始
*Azure App Service* を使用することにより、モバイル アプリ用に複数のプライベートなテスト環境とステージング環境を作成できるようになりました。これらを使用して、デプロイ前にテストを実行します。ダウンタイムなしで運用環境と交換します。Web アプリは事前に読み込まれ、優れたカスタマー エクスペリエンスを実現します。



*Azure App Service モバイル アプリ*機能が一般公開されるまで、既存の Mobile Services に、移行対象として選択する App Service へのシームレスな移行エクスペリエンスが提供されます。その間に *App Service* の調査を開始し、こちらの[チュートリアル](app-service-mobile-dotnet-backend-migrating-from-mobile-services-preview.md)に従って既存の Mobile Service に *App Service* を活用することができます。
 

<!---HONumber=Oct15_HO3-->