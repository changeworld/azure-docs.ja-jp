---
title: "Azure Web アプリ プラグインを Jenkins 継続的インテグレーションで使用する方法 | Microsoft Docs"
description: "Azure Web アプリ プラグインを Jenkins 継続的インテグレーションで使用する方法について説明します。"
services: app-service\web
documentationcenter: 
author: rmcmurray
manager: erikre
editor: 
ms.assetid: be6c4e62-da76-44f6-bb00-464902734805
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 10/19/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: c6f18c9b802d6fa18c4f50d3a537003eaaf25d14


---
# <a name="how-to-use-the-azure-web-app-plugin-with-jenkins-continuous-integration"></a>Azure Web アプリ プラグインを Jenkins 継続的インテグレーションで使用する方法
Jenkins 用 Azure Web アプリ プラグインを使用すると、分散ビルドを実行しているときに Azure で Web アプリを簡単に作成し、WAR ファイルを Web アプリにデプロイすることができます。

### <a name="prerequisites"></a>前提条件
この記事の手順を実行する前に、クライアント アプリケーションを登録および認証し、認証中に Azure Active Directory に送信されるクライアント ID とクライアント シークレットを取得する必要があります。 これらの前提条件の詳細については、次の記事を参照してください。

* [Azure Active Directory とアプリケーションの統合][integrate-apps-with-AAD]
* [クライアント アプリを登録する][register-client-app]

さらに、次の URL から **azure-webapp-plugin.hpi** ファイルをダウンロードする必要があります。

* [https://github.com/Microsoft/azure-webapp-plugin/tree/master/install](https://github.com/Microsoft/azure-webapp-plugin/tree/master/install)

## <a name="how-to-install-the-azure-web-app-plugin-for-jenkins"></a>Jenkins 用の Azure Web アプリ プラグインをインストールする方法
1. [**azure-webapp-plugin.hpi** ファイルを GitHub からダウンロードします][azure-webapp-plugin-install]。
2. Jenkins ダッシュボードにログインします。
3. Jenkins ダッシュボードで、**[Manage Jenkins (Jenkins の管理)]** をクリックします。
   
    ![[Manage Jenkins (Jenkins の管理)]][jenkins-dashboard]
4. **[Manage Jenkins (Jenkins の管理)]** ページで **[Manage Plugins (プラグインの管理)]** をクリックします。
   
    ![[Manage Plugins (プラグインの管理)]][manage-jenkins]
5. **[Advanced (詳細)]** タブをクリックし、**[Upload Plugin (プラグインのアップロード)]** セクションの **[Browse (参照)]** をクリックします。 **[Prerequisites (前提条件)]** で、**azure-webapp-plugin.hpi** ファイルをダウンロードした場所を参照し、ファイルを選択して **[Upload (アップロード)]** をクリックします。
   
    ![[Upload Plugin (プラグインのアップロード)]][upload-plugin]
6. 必要な場合は、Jenkins を再起動します。

## <a name="configure-the-azure-web-app-plugin"></a>Azure Web アプリ プラグインの構成
1. Jenkins ダッシュボードで、いずれかのプロジェクトをクリックします。
   
    ![プロジェクトの選択][select-project]
2. プロジェクトのページが表示されたら、左側のメニューの **[Configure (構成)]** をクリックします。
   
    ![プロジェクトの構成][configure-project]
3. **[Post-build Actions (ビルド後のアクション)]** セクションで、**[Add post-build action (ビルド後のアクションの追加)]** ドロップダウン メニューをクリックし、**[Azure Webapp Configuration (Azure Web アプリの構成)]** を選択します。 
   
    ![詳細オプション][advanced-options]
4. **[Azure Webapp Configuration (Azure Web アプリの構成)]** セクションが表示されたら、**[Azure Profile Configuration (Azure プロファイルの構成)]** の **[Subscription ID (サブスクリプション ID)]**、**[Client ID (クライアント ID)]**、**[Client Secret (クライアント シークレット)]**、および **[OAuth 2.0 Token Endpoint (OAuth 2.0 トークン エンドポイント)]** に情報を入力します。
   
    ![[Azure Profile Configuration (Azure プロファイルの構成)]][azure-profile-configuration]
5. **[Webapp Configuration (Web アプリの構成)]** セクションで、**[Resource Group Name (リソース グループ名)]**、**[Location (場所)]**、**[Hosting Plan Name (ホスティング プラン名)]**、**[Web App Name (Web アプリ名)]**、**[Sku Name (SKU 名)]**、**[Sku Capacity (SKU 容量)]**、および **[War File Path (War ファイルのパス)]** に情報を入力します。
   
    ![[Webapp Configuration (Web App の構成)]][webapp-configuration]
6. **[Save (保存)]** をクリックしてプロジェクトの設定を保存します。
   
    ![プロジェクトの保存][save-project]
7. 左側のメニューの **[Build Now (今すぐビルド)]** をクリックします。
   
    ![プロジェクトのビルド][build-project]

> [!NOTE]
> Web アプリ コンテナーは、Web アプリ コンテナーがまだ存在していない場合にのみ作成されます。
> 
> 

<a name="see-also"></a>

## <a name="see-also"></a>関連項目
Java での Azure の使用の詳細については、 [Azure Java デベロッパー センター]を参照してください。

Jenkins 用 Azure Web アプリ プラグインの詳細については、GitHub の [Azure Web アプリ プラグイン] プロジェクトを参照してください。

<!-- URL List -->

[Azure Java デベロッパー センター]: https://azure.microsoft.com/develop/java/
[integrate-apps-with-AAD]: http://msdn.microsoft.com/library/azure/dn132599.aspx
[register-client-app]: http://msdn.microsoft.com/dn877542.aspx
[Azure Web アプリ プラグイン]: https://github.com/Microsoft/azure-webapp-plugin
[azure-webapp-plugin-install]: https://github.com/Microsoft/azure-webapp-plugin/tree/master/install

<!-- IMG List -->

[jenkins-dashboard]: ./media/app-service-web-azure-web-app-plugin-for-jenkins/jenkins-dashboard.png
[manage-jenkins]:    ./media/app-service-web-azure-web-app-plugin-for-jenkins/manage-jenkins.png
[upload-plugin]:     ./media/app-service-web-azure-web-app-plugin-for-jenkins/upload-plugin.png
[select-project]:    ./media/app-service-web-azure-web-app-plugin-for-jenkins/select-project.png
[configure-project]: ./media/app-service-web-azure-web-app-plugin-for-jenkins/configure-project.png
[advanced-options]:  ./media/app-service-web-azure-web-app-plugin-for-jenkins/advanced-options.png
[azure-profile-configuration]: ./media/app-service-web-azure-web-app-plugin-for-jenkins/azure-profile-configuration.png
[build-project]: ./media/app-service-web-azure-web-app-plugin-for-jenkins/build-project.png
[save-project]: ./media/app-service-web-azure-web-app-plugin-for-jenkins/save-project.png
[webapp-configuration]: ./media/app-service-web-azure-web-app-plugin-for-jenkins/webapp-configuration.png



<!--HONumber=Nov16_HO3-->


