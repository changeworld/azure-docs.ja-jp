<properties
	pageTitle="Azure ポータル内の移動に関するリファレンス"
	description="App Service Web のユーザー エクスペリエンスについて、管理ポータルと Azure ポータルの違いを説明します"
	services="app-service\web"
	documentationCenter=""
	authors="jaime-espinosa"
	manager="wpickett"
	editor="jimbe"/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/24/2015"
	ms.author="jaime-espinosa"/>

# Azure ポータル内の移動に関するリファレンス

Azure Websites は、現在は [App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714) と呼ばれています。この名前の変更を反映するため、また、新しい Azure ポータルの使用方法をお伝えするため、すべてのドキュメントを更新中です。この更新プロセスが完了するまでは、新しい Azure ポータルの Web Apps の操作ガイドとして、このドキュメントを使用することができます。
 
## 以前のポータルの今後について

以前のポータルでブランド変更が行われる一方、そのポータルを新しい Azure ポータルに置き換えるプロセスが進められています。以前のポータルが廃止されるため、新規開発の焦点は新しいポータルへと移行しつつあります。今後の Web Apps の新機能は、すべてが新しいポータルで提供されます。新しいポータルを使用し、Web Apps が提供する優れた最新機能を活用してください。

## 以前のポータルと Azure ポータルのレイアウトの違い

以前のポータルでは、すべての Azure サービスが左側に一覧表示されています。以前のポータルでの移動はツリー構造に従う方法で、サービスから各要素へと移動します。この構造は、独立したコンポーネントを管理する場合には適しています。しかし、Azure で構築されるアプリケーションは相互に接続されたサービスの集合体であり、このツリー構造は、サービスの集合体を操作するうえで最適な構造ではありません。 

新しいポータルでは、複数のサービスのコンポーネントによるエンド ツー エンドでのアプリケーション構築が容易になります。新しい Azure ポータルは、 *journeys* として構造化されています。 *journey* とは、さまざまなコンポーネントのコンテナーである *blades* が連なったものです。たとえば、Web アプリに対する自動スケーリングの設定は *journey* であり、これによって次の例に示すような、**[Web サイト]** ブレード (このブレード タイトルについては、まだ新しい用語には更新されていません)、**[設定]** ブレード、**[スケール]** ブレードなど、複数のブレードが表示されます。この例では、CPU の使用状況に応じた自動スケーリングが設定されているので、**[CPU の割合]** ブレードも同様に表示されています。 *blades* 内のコンポーネントは *parts* と呼ばれており、タイルに似ています。 

![](./media/app-service-web-app-preview-portal/AutoScaling.png)

## 移動の例: Web アプリを作成する

新しい Web アプリの作成方法は、以前と同じように簡単です。次の画像では、Web アプリを動作させるのに必要な手順の数がそれほど変化していないことを示すため、以前のポータルと新しいポータルを横に並べて表示しています。 

![](./media/app-service-web-app-preview-portal/CreateWebApp.png)

新しいポータルでは、WordPress のような人気のギャラリー アプリケーションなど、最も一般的な種類の Web アプリから選択することができます。利用できるすべてのアプリケーションの一覧については、「[Azure Marketplace]」を参照してください。

Web アプリを作成する場合は、以前のポータルで作成する場合と同様に、URL、App Service プラン、場所を新しいポータルで指定します。 

![](./media/app-service-web-app-preview-portal/CreateWebAppSettings.png)

さらに、新しいポータルでは、その他の共通設定を定義できます。たとえば、[リソース グループ](azure-preview-portal-using-resource-groups.md)は、関連する Azure リソースの表示と管理を単純化します。 

## 移動の例: 設定と機能

すべての設定と機能は、今では 1 つのブレードへと合理的にグループ化されており、そこから移動を開始することができます。

![](./media/app-service-web-app-preview-portal/WebAppSettings.png)

たとえば、**[設定]** ブレードの **[カスタム ドメインと SSL]** をクリックすると、カスタム ドメインを作成できます。

![](./media/app-service-web-app-preview-portal/ConfigureWebApp.png)

監視のアラートを設定するには、**[要求とエラー]**、**[アラートの追加]** の順にクリックします。

![](./media/app-service-web-app-preview-portal/Monitoring.png)

診断を有効にするには、**[設定]** ブレードの **[診断ログ]** をクリックします。

![](./media/app-service-web-app-preview-portal/Diagnostics.png)
 
アプリケーション設定を構成するには、**[設定]** ブレードの **[アプリケーション設定]** をクリックします。 

![](./media/app-service-web-app-preview-portal/AppSettingsPreview.png)

ブランド名のほかにも、ポータル内のいくつかの要素が、見つけやすくなるように名前を変更されたり、異なるグループに分けられたりしています。たとえば、以下のスクリーンショットは、以前のポータルのアプリケーション設定 (**構成**) に対応するページです。

![](./media/app-service-web-app-preview-portal/AppSettings.png)

## その他のリソース

[Azure クラウド アプリケーション プラットフォーム](app-service-cloud-app-platform.md)

[新しいポータル]: https://portal.azure.com
[Azure Marketplace]: /marketplace/

>[AZURE.NOTE] Azure アカウントにサイン アップする前に Azure App Service を使用してみる場合は、[App Service の試用](http://go.microsoft.com/fwlink/?LinkId=523751)に関するページにアクセスしてください。App Service で有効期限付きのスターター Web アプリをすぐに作成できます。クレジット カードやコミットメントは不要です。

## 変更箇所
*Websites から App Service への変更に関するガイドについては、「[Azure App Service と、それが既存の Azure サービスに与える影響](http://go.microsoft.com/fwlink/?LinkId=529714)」を参照してください。
*以前のポータルから新しいポータルへの変更に関するガイドについては、[新しいポータル内の移動に関するリファレンス](http://go.microsoft.com/fwlink/?LinkId=529715)のページを参照してください。

<!--HONumber=49-->