---
title: "Azure Resource Manager ベースのクロスプラットフォーム コマンド ライン ツールを使用して Azure Web アプリを管理する | Microsoft Docs"
description: "Azure Resource Manager ベースの新しいクロスプラットフォーム コマンド ライン ツールを使用して Azure Web アプリを管理する方法について説明します。"
services: app-service\web
documentationcenter: 
author: ahmedelnably
manager: stefsch
editor: 
ms.assetid: d415b195-4262-416f-b59f-7e1aef200054
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2016
ms.author: aelnably
translationtype: Human Translation
ms.sourcegitcommit: 11a9e0a67b71e7ce4adf9713c825511ade4ff7d7
ms.openlocfilehash: 36a6863990e3c4cb87bc4f3a1207ab552c836c20


---
# <a name="using-azure-resource-manager-based-xplat-cli-for-azure-web-app"></a>Azure Resource Manager ベースの XPlat CLI を使用して Azure Web アプリを管理する
> [!div class="op_single_selector"]
> * [Azure CLI](app-service-web-app-azure-resource-manager-xplat-cli.md)
> * [Azure PowerShell](app-service-web-app-azure-resource-manager-powershell.md)
> 
> 

Microsoft Azure クロスプラットフォーム コマンドライン ツール バージョン 0.10.5 のリリースに伴い、新しいコマンドが追加されました。 それにより、Azure Resource Manager ベースの PowerShell コマンドを使って Web Apps を管理することができます。

リソース グループの管理の詳細については、「[Use the Azure CLI to manage Azure resources and resource groups (Azure CLI を使用して Azure のリソースとリソース グループを管理する)](../xplat-cli-azure-resource-manager.md)」を参照してください。 

## <a name="managing-app-service-plans"></a>App Service プランの管理
### <a name="create-an-app-service-plan"></a>App Service プランを作成する
App Service プランを作成するには、**azure appserviceplan create** コマンドを使用します。

以下、各パラメーターについて説明します。

* **--resource-group**: 新たに作成された App Service プランが属するリソース グループ。
* **--name**: App Service プランの名前。
* **--location**: App Service プランの場所。
* **--tier**: 目的の価格 SKU (選択肢: F1 (Free)、 D1 (Shared)、 B1 (Basic Small)、B2 (Basic Medium)、B3 (Basic Large)、 S1 (Standard Small)、S2 (Standard Medium)、S3 (Standard Large)、 P1 (Premium Small)、P2 (Premium Medium)、P3 (Premium Large))。
* **--instances**: App Service プランの worker の数 (既定値は 1 です)。

コマンドレットの使用例:

    azure appserviceplan create --name ContosoAppServicePlan --location "South Central US" --resource-group ContosoAzureResourceGroup --sku P1 --instances 10

#### <a name="create-a-linux-app-service-plan"></a>Linux App Service プランの作成

**--islinux true** パラメーターを追加して、同じ **azure appserviceplan create** コマンドを使用します。 「[Introduction to App Service on Linux (App Service on Linux の概要)](app-service-linux-intro.md)」に示されている制限事項とリージョンに注意してください。

### <a name="list-existing-app-service-plans"></a>既存の App Service プランの一覧表示
既存の App Service プランを一覧表示するには、**azure appserviceplan list** コマンドを使用します。

特定のリソース グループのすべての App Service プランを一覧表示するには、次のように入力します。

    azure appserviceplan list --resource-group ContosoAzureResourceGroup

特定の App Service プランを取得するには、**azure appserviceplan show** コマンドを使用します。

    azure appserviceplan show --name ContosoAppServicePlan --resource-group southeastasia

### <a name="configure-an-existing-app-service-plan"></a>既存の App Service プランの構成
既存の App Service プランの設定を変更するには、**azure appserviceplan config** コマンドを使用します。 SKU のほか、worker の数を変更することができます。 

    azure appserviceplan config --nameContosoAppServicePlan --resource-group ContosoAzureResourceGroup --sku S1 --instances 9

#### <a name="scaling-an-app-service-plan"></a>App Service プランのスケーリング
既存の App Service プランをスケーリングするには、次のように入力します。

    azure appserviceplan config --nameContosoAppServicePlan --resource-group ContosoAzureResourceGroup --instances 9

#### <a name="changing-the-sku-of-an-app-service-plan"></a>App Service プランの SKU の変更
既存の App Service プランの SKU を変更するには、次のように入力します。

    azure appserviceplan config --nameContosoAppServicePlan --resource-group ContosoAzureResourceGroup --sku S1


### <a name="delete-an-existing-app-service-plan"></a>既存の App Service プランの削除
既存の App Service プランを削除するにはまず、割り当てられているすべての Web アプリを移動するか削除する必要があります。 その後、**azure webapp delete** コマンドを使用して App Service プランを削除できます。

    azure appserviceplan delete --name ContosoAppServicePlan --resource-group southeastasia

## <a name="managing-app-service-web-apps"></a>App Service Web Apps の管理
### <a name="create-a-web-app"></a>Web アプリの作成
Web アプリを作成するには、**azure webapp create** コマンドを使用します。

以下、各パラメーターについて説明します。

* **--name**: Web アプリの名前。
* **--plan**: Web アプリをホストするために使用するサービス プランの名前。
* **--resource-group**: App Service プランのホストとなるリソース グループ。
* **--location**: Web アプリの場所。

コマンドレットの使用例:

    azure webapp create --name ContosoWebApp --resource-group ContosoAzureResourceGroup --plan ContosoAppServicePlan --location "South Central US"

### <a name="delete-an-existing-web-app"></a>既存の Web アプリの削除
既存の Web アプリを削除するには、**azure webapp delete** コマンドを使用します。Web アプリの名前とリソース グループの名前を指定する必要があります。

    azure webapp delete --name ContosoWebApp --resource-group ContosoAzureResourceGroup

### <a name="list-existing-web-apps"></a>既存の Web アプリの一覧表示
既存の Web アプリを一覧表示するには、**azure webapp list** コマンドを使用します。

特定のリソース グループのすべての Web アプリを一覧表示するには、次のように入力します。

    azure webapp list --resource-group ContosoAzureResourceGroup

特定の Web アプリを取得するには、**azure webapp show** コマンドを使用します。

    azure webapp show --name ContosoWebApp --resource-group ContosoAzureResourceGroup

### <a name="configure-an-existing-web-app"></a>既存の Web アプリの構成
既存の Web アプリの設定と構成を変更するには、**azure webapp config set** コマンドを使用します。

例 (1): Web アプリの php のバージョンを変更する 

    azure webapp config set --name ContosoWebApp --resource-group ContosoAzureResourceGroup --phpversion 5.6

例 (2): アプリ設定を追加または変更する

    webapp config appsettings set --name ContosoWebApp --resource-group ContosoAzureResourceGroup appsetting1=appsetting1value,appsetting2=appsetting2value

変更可能な他の設定を調べるには、**azure webapp config -h** コマンドを使用します。

### <a name="change-the-state-of-an-existing-web-app"></a>既存の Web アプリの状態を変更する
#### <a name="restart-a-web-app"></a>Web アプリの再起動
Web アプリを再起動するには、Web アプリの名前とリソース グループを指定する必要があります。

    azure webapp restart --name ContosoWebApp --resource-group ContosoAzureResourceGroup

#### <a name="stop-a-web-app"></a>Web アプリの停止
Web アプリを停止するには、Web アプリの名前とリソース グループを指定する必要があります。

    azure webapp stop --name ContosoWebApp --resource-group ContosoAzureResourceGroup

#### <a name="start-a-web-app"></a>Web アプリの起動
Web アプリを起動するには、Web アプリの名前とリソース グループを指定する必要があります。

    azure webapp start --name ContosoWebApp --resource-group ContosoAzureResourceGroup

### <a name="manage-web-app-publishing-profiles"></a>Web アプリの発行プロファイルの管理
それぞれの Web アプリには、アプリを発行するために使用できる発行プロファイルがあります。

#### <a name="get-publishing-profile"></a>発行プロファイルの取得
Web アプリの発行プロファイルを取得するには、次のように入力します。

    azure webapp publishingprofile --name ContosoWebApp --resource-group ContosoAzureResourceGroup

このコマンドは、発行プロファイルのユーザー名とパスワードをコマンド ラインにエコーします。

### <a name="manage-web-app-hostnames"></a>Web アプリのホスト名の管理
Web アプリのホスト名のバインドを管理するには、**azure webapp config hostnames** コマンドを使用します。  

#### <a name="list-hostname-bindings"></a>ホスト名のバインドの一覧表示
Web アプリの現在のホスト名のバインドを取得するには、次のように入力します。

    azure webapp config hostnames list --name ContosoWebApp --resource-group ContosoAzureResourceGroup

#### <a name="add-hostname-bindings"></a>ホスト名のバインドの追加
Web アプリにホスト名のバインドを追加するには、次のように入力します。

    azure webapp config hostnames add --name ContosoWebApp --resource-group ContosoAzureResourceGroup --hostname www.contoso.com

#### <a name="delete-hostname-bindings"></a>ホスト名のバインドの削除
ホスト名のバインドを削除するには、次のように入力します。

    azure webapp config hostnames delete --name ContosoWebApp --resource-group ContosoAzureResourceGroup --hostname www.contoso.com

### <a name="next-steps"></a>次のステップ
* Azure Resource Manager の CLI のサポートについては、「[Use the Azure CLI to manage Azure resources and resource groups (Azure CLI を使用して Azure のリソースとリソース グループを管理する)](../xplat-cli-azure-resource-manager.md)」を参照してください。
* PowerShell を使用した App Service の管理については、「[Azure Resource Manager ベースの PowerShell を使用して Azure Web アプリを管理する](app-service-web-app-azure-resource-manager-powershell.md)」を参照してください。
* Linux での Azure App Service の詳細については、「[Introduction to App Service on Linux (App Service on Linux の概要)](app-service-linux-intro.md)」を参照してください。



<!--HONumber=Nov16_HO3-->


