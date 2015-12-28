<properties
   pageTitle="Visual Studio を使用した Azure クラウド サービス プロジェクト内のロールの管理 | Microsoft Azure"
   description="Visual Studio を使用して、Azure クラウド サービス プロジェクトに新しいロールを追加したり、既存のロールを削除する方法について説明します。"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="09/08/2015"
   ms.author="tarcher" />

# Visual Studio を使用した Azure クラウド サービス プロジェクト内のロールの管理

Azure クラウド サービス プロジェクトを作成した後、そのプロジェクトに新しいロールを追加したり、プロジェクトから既存のロールを削除できます。既存のプロジェクトをインポートし、ロールに変換することもできます。たとえば、ASP.NET Web アプリケーションをインポートし、そのアプリケーションを Web ロールとして指定できます。

## ロールの追加または削除

**ロールを追加するには**

**ソリューション エクスプローラー**で、クラウド サービス プロジェクトの **[ロール]** ノードのショートカット メニューを開き、**[追加]** を選択します。現在のソリューションから既存の Web ロールや worker ロールを選択したり、新しい Web ロール プロジェクトや worker ロール プロジェクトを作成できます。または、ASP.NET Web アプリケーション プロジェクトなどの適切なプロジェクトを選択して、ロール プロジェクトに関連付けることができます。

**ロールの関連付けを削除するには**

ソリューション エクスプローラーのクラウド サービス プロジェクトの **[ロール]** ノードで、削除するロールのショートカット メニューを開き、**[削除]** を選択します。

## クラウド サービスでのロールの削除と追加

クラウド サービス プロジェクトからロールを削除し、後からそのロールをプロジェクトに戻す場合は、エンドポイントや診断情報など、ロールの宣言と基本属性のみが追加されます。ServiceDefinition.csdef ファイルや ServiceConfiguration.cscfg ファイルには、追加のリソースや参照は追加されません。この情報を追加するには、手動でこれらのファイルに情報を追加し直す必要があります。

たとえば、Web サービス ロールを削除した後、このロールを再度ソリューションに追加することになったとします。これを実行すると、エラーが発生します。このエラーを回避するには、次の XML に示す `<LocalResources>` 要素を ServiceDefinition.csdef ファイルに追加し直す必要があります。プロジェクトに追加し直した Web サービス ロールの名前を **<LocalStorage>** 要素の名前属性の一部として使用します。この例では、Web サービス ロールの名前は **WCFServiceWebRole1** です。

	<WebRole name="WCFServiceWebRole1">
	    <Sites>
	      <Site name="Web">
	        <Bindings>
	          <Binding name="Endpoint1" endpointName="Endpoint1" />
	        </Bindings>
	      </Site>
	    </Sites>
	    <Endpoints>
	      <InputEndpoint name="Endpoint1" protocol="http" port="80" />
	    </Endpoints>
	    <Imports>
	      <Import moduleName="Diagnostics" />
	    </Imports>
	   <LocalResources>
	      <LocalStorage name="WCFServiceWebRole1.svclog" sizeInMB="1000" cleanOnRoleRecycle="false" />
	   </LocalResources>
	</WebRole>

## 次のステップ

「[Visual Studio を使用した Azure クラウド サービスのロールの構成](vs-azure-tools-configure-roles-for-cloud-service.md)」を読み、Visual Studio でのロールの構成方法を理解する。

<!---HONumber=AcomDC_1217_2015-->