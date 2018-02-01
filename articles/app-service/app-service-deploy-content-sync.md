---
title: "クラウド フォルダーから Azure App Service へのコンテンツの同期"
description: "コンテンツ同期を使用してクラウド フォルダーから Azure App Service にアプリをデプロイする方法を説明します。"
services: app-service
documentationcenter: 
author: dariagrigoriu
manager: erikre
editor: mollybos
ms.assetid: 88d3a670-303a-4fa2-9de9-715cc904acec
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2016
ms.author: dariagrigoriu
ms.openlocfilehash: 8e132e4d4a65588d57e3cfb969e785f5a164206c
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/18/2018
---
# <a name="sync-content-from-a-cloud-folder-to-azure-app-service"></a>クラウド フォルダーから Azure App Service へのコンテンツの同期
このチュートリアルでは、Dropbox や OneDrive などの一般的なクラウド ストレージ サービスから [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) にコンテンツを同期する方法について説明します。 

## <a name="overview"></a>コンテンツ同期デプロイの概要
オンデマンド コンテンツ同期デプロイでは、App Service と統合された [Kudu デプロイ エンジン](https://github.com/projectkudu/kudu/wiki) を使用しています。 [Azure Portal](https://portal.azure.com) では、クラウド ストレージのフォルダーを指定して、そのフォルダー内でアプリのコードとコンテンツの作業を行い、ボタンをクリックするだけで App Service に同期できます。 コンテンツ同期では、ビルドとデプロイに Kudu プロセスを使用します。 

## <a name="contentsync"></a>コンテンツ同期デプロイを有効化する方法
[Azure Portal](https://portal.azure.com) からのコンテンツ同期を有効化するには、次の手順を実行します。

1. Azure Portal の対象アプリのページで、**[設定]** > **[デプロイ ソース]** をクリックします。 **[ソースの選択]** をクリックし、デプロイのソースとして **[OneDrive]** または **[Dropbox]** を選択します。 
   
    ![コンテンツ同期](./media/app-service-deploy-content-sync/deployment_source.png)
   
   > [!NOTE]
   > API に違いがあるため、**OneDrive for Business** は現時点ではサポートされていません。 
   > 
   > 
2. OneDrive または Dropbox の専用の事前定義された指定パスに App Service がアクセスできるようにする、認証ワークフローを完了します。このパスが、App Service のコンテンツの保存先となります。 認証が完了すると、App Service プラットフォームで、指定のコンテンツ パスの下にコンテンツ フォルダーを作成するか、または指定のコンテンツ パスにある既存のコンテンツ フォルダーを選択できるようになります。 App Service 同期に使用するクラウド ストレージ アカウントの指定コンテンツ パスは次のとおりです。  
   
   * **OneDrive**: `Apps\Azure Web Apps` 
   * **Dropbox**: `Dropbox\Apps\Azure`
3. コンテンツ同期は、初回以降は Azure ポータルからオンデマンドで開始できます。 デプロイ履歴は **[デプロイ]** ページで確認できます。
   
    ![デプロイ履歴](./media/app-service-deploy-content-sync/onedrive_sync.png)

Dropbox デプロイの詳細については、[Dropbox からのデプロイ] (https://azure.microsoft.com/en-in/blog/new-deploy-to-windows-azure-web-sites-from-dropbox/)に関するページをご覧ください。

