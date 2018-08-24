---
title: Azure App Service への継続的デプロイ | Microsoft Docs
description: Azure App Service への継続的なデプロイを有効にする方法を説明します。
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
ms.assetid: 6adb5c84-6cf3-424e-a336-c554f23b4000
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/05/2018
ms.author: cephalin;dariagrigoriu
ms.openlocfilehash: 4d3f1c66c6403720bf02c80af1d6833dc3cee3f1
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/15/2018
ms.locfileid: "42142178"
---
# <a name="continuous-deployment-to-azure-app-service"></a>Azure App Service への継続的デプロイ
この記事では、[Azure App Service](app-service-web-overview.md) の継続的デプロイを構成する方法を示します。 App Service は、BitBucket、GitHub、および [Visual Studio Team Services (VSTS)](https://www.visualstudio.com/team-services/) サービスの既存のリポジトリから最新の更新プログラムをプルすることで、これらのサービスから継続的デプロイを実行できます。

Azure Portal に表示されないクラウド リポジトリ ([GitLab](https://gitlab.com/) など) の継続的デプロイを手動で構成する方法については、「[Setting up continuous deployment using manual steps](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps)」(手動による手順を使用した継続的デプロイの設定) を参照してください。

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

準備したリポジトリをサポートされているサービスのいずれかに発行します。 これらのサービスにプロジェクトを発行する方法の詳細については、「[Create a repo (GitHub) (リポジトリの作成 (GitHub))]」、「[Create a repo (BitBucket) (リポジトリの作成 (BitBucket))]」、「[Get started with VSTS (VSTS で作業を始める) (VSTS で作業を始める)]」を参照してください。

## <a name="deploy-continuously-from-github"></a>GitHub からの継続的デプロイ

GitHub を使用する継続的デプロイを有効にするには、[Azure Portal](https://portal.azure.com) の App Service アプリ のページに移動します。

左側のメニューで、**[デプロイ センター]** > **[GitHub]** > **[承認]** をクリックします。 承認のプロンプトに従います。 

![](media/app-service-continuous-deployment/github-choose-source.png)

GitHub の承認は、1 回のみ実行する必要があります。 既に承認されている場合は、**[続行]** をクリックします。 **[アカウントの変更]** をクリックすることで、承認された GitHub アカウントを変更できます。

![](media/app-service-continuous-deployment/github-continue.png)

**[ビルド プロバイダー]** ページで、ビルド プロバイダーを選択し、**[続行]** をクリックします。

### <a name="option-1-use-app-service-kudu-build-server"></a>オプション 1: App Service Kudu ビルド サーバーを使用する

**[構成]** ページで、継続的デプロイのソースになる組織、リポジトリ、およびブランチを選択します。 完了したら、**[続行]** をクリックします。

### <a name="option-2-use-vsts-continuous-delivery"></a>オプション 2: VSTS の継続的デリバリーを使用する

> [!NOTE]
> App Service で必要なビルドを作成して VSTS アカウントで定義をリリースするには、Azure アカウントが Azure サブスクリプションの**所有者**ロールを持っている必要があります。
>

**[構成]** ページの **[コード]** セクションで、継続的デプロイのソースになる組織、リポジトリ、およびブランチを選択します。 完了したら、**[続行]** をクリックします。

**[構成]** ページの **[ビルド]** セクションで、新しい VSTS アカウントを構成するか、既存のアカウントを指定します。 完了したら、**[続行]** をクリックします。

> [!NOTE]
> 表示されない既存の VSTS アカウントを使用する場合は、[VSTS アカウントを Azure サブスクリプションにリンク](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App)する必要があります。

**[テスト]** ページで、ロード テストを有効にするかどうかを選択し、**[続行]** をクリックします。

App Service プランの[価格レベル](https://azure.microsoft.com/pricing/details/app-service/plans/)によっては、**[ステージングへのデプロイ]** ページも表示される場合があります。 [デプロイ スロットを有効にする](web-sites-staged-publishing.md)かどうかを選択し、**[続行]** をクリックします。

### <a name="finish-configuration"></a>構成を終了する

**[概要]** ページで、選択内容を確認し、**[完了]** をクリックします。

構成が完了したら、選択したリポジトリ内の新しいコミットが App Service アプリに継続的にデプロイされます。

![](media/app-service-continuous-deployment/github-finished.png)

## <a name="deploy-continuously-from-bitbucket"></a>BitBucket からの継続的デプロイ

BitBucket を使用する継続的デプロイを有効にするには、[Azure Portal](https://portal.azure.com) の App Service アプリ のページに移動します。

左側のメニューで、**[デプロイ センター]** > **[BitBucket]** > **[承認]** をクリックします。 承認のプロンプトに従います。 

![](media/app-service-continuous-deployment/bitbucket-choose-source.png)

BitBucket の承認は、1 回のみ実行する必要があります。 既に承認されている場合は、**[続行]** をクリックします。 **[アカウントの変更]** をクリックすることで、承認された BitBucket アカウントを変更できます。

![](media/app-service-continuous-deployment/bitbucket-continue.png)

**[構成]** ページで、継続的デプロイのソースになるリポジトリとブランチを選択します。 完了したら、**[続行]** をクリックします。

**[概要]** ページで、選択内容を確認し、**[完了]** をクリックします。

構成が完了したら、選択したリポジトリ内の新しいコミットが App Service アプリに継続的にデプロイされます。

## <a name="deploy-continuously-from-vsts"></a>VSTS からの継続的デプロイ

VSTS を使用する継続的デプロイを有効にするには、[Azure Portal](https://portal.azure.com) の App Service アプリ のページに移動します。

左側のメニューで、**[デプロイ センター]** > **[VSTS]** > **[承認]** をクリックします。 

![](media/app-service-continuous-deployment/vsts-choose-source.png)

**[ビルド プロバイダー]** ページで、ビルド プロバイダーを選択し、**[続行]** をクリックします。

### <a name="option-1-use-app-service-kudu-build-server"></a>オプション 1: App Service Kudu ビルド サーバーを使用する

**[構成]** ページで、継続的デプロイのソースになる VSTS アカウント、プロジェクト、リポジトリ、およびブランチを選択します。 完了したら、**[続行]** をクリックします。

### <a name="option-2-use-vsts-continuous-delivery"></a>オプション 2: VSTS の継続的デリバリーを使用する

> [!NOTE]
> App Service で必要なビルドを作成して VSTS アカウントで定義をリリースするには、Azure アカウントが Azure サブスクリプションの**所有者**ロールを持っている必要があります。
>

**[構成]** ページの **[コード]** セクションで、継続的デプロイのソースになる VSTS アカウント、プロジェクト、リポジトリ、およびブランチを選択します。 完了したら、**[続行]** をクリックします。

> [!NOTE]
> 表示されない既存の VSTS アカウントを使用する場合は、[VSTS アカウントを Azure サブスクリプションにリンク](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App)する必要があります。

**[構成]** ページの **[ビルド]** セクションで、選択されたリポジトリに対して VSTS がビルド タスクを実行するために使用する必要がある言語フレームワークを指定します。 完了したら、**[続行]** をクリックします。

**[テスト]** ページで、ロード テストを有効にするかどうかを選択し、**[続行]** をクリックします。

App Service プランの[価格レベル](https://azure.microsoft.com/pricing/details/app-service/plans/)によっては、**[ステージングへのデプロイ]** ページも表示される場合があります。 [デプロイ スロットを有効にする](web-sites-staged-publishing.md)かどうかを選択し、**[続行]** をクリックします。 

### <a name="finish-configuration"></a>構成を終了する

**[概要]** ページで、選択内容を確認し、**[完了]** をクリックします。

構成が完了したら、選択したリポジトリ内の新しいコミットが App Service アプリに継続的にデプロイされます。

## <a name="disable-continuous-deployment"></a>継続的なデプロイの無効化

継続的デプロイを無効にするには、[Azure Portal](https://portal.azure.com) の App Service アプリ のページに移動します。

左側のメニューで、**[デプロイ センター]** > **[GitHub]** または **[VSTS]** または **[BitBucket]** > **[切断]** をクリックします。

![](media/app-service-continuous-deployment/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="additional-resources"></a>その他のリソース

* [継続的なデプロイに関する一般的な問題の調査方法](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [How to use PowerShell for Azure (Azure 用の PowerShell を使用する方法)]
* [Git に関するドキュメント]
* [プロジェクト Kudu](https://github.com/projectkudu/kudu/wiki)
* [Use Azure to automatically generate a CI/CD pipeline to deploy an ASP.NET 4 app](https://www.visualstudio.com/docs/build/get-started/aspnet-4-ci-cd-azure-automatic) (Azure を使用して、ASP.NET 4 アプリをデプロイする CI/CD パイプラインを自動的に生成する)

[Azure portal]: https://portal.azure.com
[VSTS Portal]: https://www.visualstudio.com/en-us/products/visual-studio-team-services-vs.aspx
[Installing Git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[How to use PowerShell for Azure (Azure 用の PowerShell を使用する方法)]: /powershell/azureps-cmdlets-docs
[Git に関するドキュメント]: http://git-scm.com/documentation

[Create a repo (GitHub) (リポジトリの作成 (GitHub))]: https://help.github.com/articles/create-a-repo
[Create a repo (BitBucket) (リポジトリの作成 (BitBucket))]: https://confluence.atlassian.com/display/BITBUCKET/Create+an+Account+and+a+Git+Repo
[Get started with VSTS (VSTS で作業を始める) (VSTS で作業を始める)]: https://www.visualstudio.com/docs/vsts-tfs-overview
