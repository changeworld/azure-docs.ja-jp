---
title: "Azure App Service Web Apps on Linux での Ruby の使用 | Microsoft Docs"
description: "Azure App Service Web Apps on Linux で Ruby を使用します。"
keywords: "Azure App Service, Web アプリ, FAQ, Linux, OSS, Ruby"
services: app-service
documentationCenter: 
authors: aelnably
manager: erikre
editor: 
ms.assetid: 
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2017
ms.author: aelnably;wesmc
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 5aeef6f31dacb1b27c605d39a35a81bd0211e06a
ms.contentlocale: ja-jp
ms.lasthandoff: 05/10/2017


---

# <a name="using-ruby-in-web-app-on-linux"></a>Web Apps on Linux で Ruby を使用する #

バックエンドの最新の更新で、Ruby v.2.3 のサポートが導入されました。 Linux の Web アプリの構成を設定することで、アプリケーション スタックを変更できます。

## <a name="using-the-azure-portal"></a>Azure ポータルの使用 ##

[Azure Portal](https://portal.azure.com) の新しいメニューでは、次の画像に示す [Web + モバイル] オプションから Linux での Web アプリの作成を選択できます。

![Azure Portal を使用した Web アプリの作成の開始][1]

次に、次の図のように **[作成] ブレード**が開きます。

![[作成] ブレード][2]

1. Web アプリに名前を付けます。
2. 既存のリソース グループを選択するか、新しいリソース グループを作成します  (「[limitations (制限)](app-service-linux-intro.md)」セクションで、使用可能なリージョンを確認してください)。
3. 既存の Azure App Service プランを選択するか、新しいプランを作成します  (「[limitations (制限)](app-service-linux-intro.md)」セクションで、App Service プランの注意事項を確認してください)。
4. 組み込みのランタイム スタックから Ruby を選択します。

Ruby Web アプリが作成されたら、Git または FTP を使用してデプロイできます。

## <a name="next-steps"></a>次のステップ
* [Web App on Linux とは何か](app-service-linux-intro.md)
* [Web App on Linux で Web Apps を作成する](app-service-linux-how-to-create-web-app.md)
* [Azure App Service へのローカル Git デプロイ](app-service-deploy-local-git.md)
* [Azure App Service Web App on Linux の FAQ](app-service-linux-faq.md)

<!--Image references-->
[1]: ./media/app-service-linux-using-ruby/New-Linux.png
[2]: ./media/app-service-linux-using-ruby/Ruby-UX.png
