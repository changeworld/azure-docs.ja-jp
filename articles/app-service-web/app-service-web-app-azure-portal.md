<properties
	pageTitle="Azure ポータル内の移動に関するリファレンス"
	description="App Service Web のユーザー エクスペリエンスについて、管理ポータルと Azure ポータルの違いを説明します"
	services="app-service"
	documentationCenter=""
	authors="jaime-espinosa"
	manager="wpickett"
	editor="jimbe"/>

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/15/2015"
	ms.author="jaime-espinosa"/>

# Azure ポータル内の移動に関するリファレンス

Azure Websites は、現在は [App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714) と呼ばれています。この名前の変更を反映するため、また、新しい Azure ポータルの使用方法をお伝えするため、すべてのドキュメントを更新中です。この更新プロセスが完了するまでは、新しい Azure ポータルの Web Apps の操作ガイドとして、このドキュメントを使用することができます。

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]
 
## 以前のポータルの今後について

以前のポータルでブランド変更が行われる一方、そのポータルを新しい Azure ポータルに置き換えるプロセスが進められています。以前のポータルが廃止されるため、新規開発の焦点は新しいポータルへと移行しつつあります。今後の Web Apps の新機能は、すべてが新しいポータルで提供されます。新しいポータルを使用し、Web Apps が提供する優れた最新機能を活用してください。

## 以前のポータルと Azure ポータルのレイアウトの違い

以前のポータルでは、すべての Azure サービスが左側に一覧表示されています。以前のポータルでの移動はツリー構造に従う方法で、サービスから各要素へと移動します。この構造は、独立したコンポーネントを管理する場合には適しています。しかし、Azure で構築されるアプリケーションは相互に接続されたサービスの集合体であり、このツリー構造は、サービスの集合体を操作するうえで最適な構造ではありません。

新しいポータルでは、複数のサービスのコンポーネントによるエンド ツー エンドでのアプリケーション構築が容易になります。新しい Azure ポータルは、*ジャーニー*として構造化されています。*ジャーニー*とは、さまざまなコンポーネントのコンテナーである*ブレード*が連なったものです。たとえば、Web アプリに対する自動スケールの設定は*ジャーニー*であり、これによって次の例に示すような、**[Web サイト]** ブレード (このブレード タイトルについては、まだ新しい用語には更新されていません)、**[設定]** ブレード、**[スケール]** ブレードなど、複数のブレードが表示されます。この例では、CPU の使用状況に応じた自動スケーリングが設定されているので、**[CPU の割合]** ブレードも同様に表示されています。*ブレード*内のコンポーネントは*パート*と呼ばれており、タイルに似ています。

![](./media/app-service-web-app-azure-portal/AutoScaling.png)

## 移動の例: Web アプリを作成する

新しい Web アプリの作成方法は、以前と同じように簡単です。次の画像では、Web アプリを動作させるのに必要な手順の数がそれほど変化していないことを示すため、以前のポータルと新しいポータルを横に並べて表示しています。

![](./media/app-service-web-app-azure-portal/CreateWebApp.png)

新しいポータルでは、WordPress のような人気のギャラリー アプリケーションなど、最も一般的な種類の Web アプリから選択することができます。利用できるすべてのアプリケーションの一覧については、[Azure Marketplace] を参照してください。

Web アプリを作成する場合は、以前のポータルで作成する場合と同様に、URL、App Service プラン、場所を新しいポータルで指定します。

![](./media/app-service-web-app-azure-portal/CreateWebAppSettings.png)

さらに、新しいポータルでは、その他の共通設定を定義できます。たとえば、[リソース グループ](../resource-group-overview.md)は、関連する Azure リソースの表示と管理を単純化します。

## 移動の例: 設定と機能

すべての設定と機能は、今では 1 つのブレードへと合理的にグループ化されており、そこから移動を開始することができます。

![](./media/app-service-web-app-azure-portal/WebAppSettings.png)

たとえば、**[設定]** ブレードの **[カスタム ドメインと SSL]** をクリックすると、カスタム ドメインを作成できます。

![](./media/app-service-web-app-azure-portal/ConfigureWebApp.png)

監視のアラートを設定するには、**[要求とエラー]**、**[アラートの追加]** の順にクリックします。

![](./media/app-service-web-app-azure-portal/Monitoring.png)

診断を有効にするには、**[設定]** ブレードの **[診断ログ]** をクリックします。

![](./media/app-service-web-app-azure-portal/Diagnostics.png)
 
アプリケーション設定を構成するには、**[設定]** ブレードの **[アプリケーション設定]** をクリックします。

![](./media/app-service-web-app-azure-portal/AppSettingsPreview.png)

ブランド名のほかにも、ポータル内のいくつかの要素が、見つけやすくなるように名前を変更されたり、異なるグループに分けられたりしています。たとえば、以下のスクリーンショットは、以前のポータルのアプリケーション設定 (**構成**) に対応するページです。

![](./media/app-service-web-app-azure-portal/AppSettings.png)

## その他のリソース

[Azure クラウド アプリケーション プラットフォーム](../app-service-cloud-app-platform.md)

[New portal]: https://portal.azure.com
[Azure Marketplace]: /marketplace/

>[AZURE.NOTE]Azure アカウントにサインアップする前に Azure App Service の使用を開始する場合は、[App Service の試用](http://go.microsoft.com/fwlink/?LinkId=523751)に関するページにアクセスしてください。App Service で有効期間の短いスターター Web アプリをすぐに作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

## 変更内容
* Websites から App Service への変更ガイドについては、「[Azure App Service と既存の Azure サービス](http://go.microsoft.com/fwlink/?LinkId=529714)」を参照してください。
* 以前のポータルから新しいポータルへの変更ガイドについては、「[Azure ポータル内の移動に関するリファレンス](http://go.microsoft.com/fwlink/?LinkId=529715)」を参照してください。
 

<!---HONumber=Oct15_HO3-->