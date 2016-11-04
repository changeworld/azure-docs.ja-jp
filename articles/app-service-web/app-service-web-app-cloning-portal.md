---
title: Azure ポータルを使用した Web アプリの複製
description: Azure ポータルを使用して既存の Web アプリを新しい Web アプリに複製する方法を説明します。
services: app-service\web
documentationcenter: ''
author: ahmedelnably
manager: stefsch
editor: ''

ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2016
ms.author: ahmedelnably

---
# Azure ポータルを使用した Azure App Service アプリの複製
[Azure App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714) の複製機能を使用すると、既存の Web アプリを複製して、別のリージョンまたは同じリージョンにアプリを新規作成することができます。これにより、お客様は、リージョンをまたいでさまざまなアプリを迅速かつ簡単にデプロイできるようになります。

アプリの複製は、現在、Premium レベルの App Service プランでのみサポートされています。この新機能には Web アプリのバックアップ機能と同じ制限が適用されます。「[Azure App Service での Web アプリのバックアップ](web-sites-backup.md)」を参照してください。

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## 既存のアプリの複製
Web アプリの複製を作成するには、その Web アプリが **Premium** モードで実行されている必要があります。

1. [Azure ポータル](https://portal.azure.com/)の Web アプリのブレードを開きます。
2. **[ツール]** をクリックします。次に、**[ツール]** ブレードで **[アプリの複製]** をクリックします。
   
    ![][1]
   
   > [!NOTE]
   > Web アプリがまだ **Premium** モードでない場合は、アプリの複製がサポートされているモードを示すメッセージが表示されます。この時点で、**[アップグレード]** を選択することもできます。
   > 
   > 
3. **[アプリの複製]** ブレードで、新しい Web アプリの名前、リソース グループ、App Service プランを入力します。また、複数のソース Web アプリの設定を複製するかどうかも選択できます。
   
    ![][2]
4. **[作成]** をクリックすると、ソース Web アプリの複製の作成が開始されます。

## App Service 環境への既存のアプリの複製
**[アプリの複製]** ブレードでは、既存の App Service 環境のアプリ プールを選択することもできます。

## 現在の制限
この機能は現在プレビュー段階にあり、新機能を追加する作業が続けられています。次の一覧に、Azure ポータルでの現在のアプリの複製のサポートに関する既知の制限を示します。

* Azure Traffic Manager の設定は複製されない
* 自動スケールの設定は複製されない
* バックアップ スケジュールの設定は複製されない
* VNET の設定は複製されない
* App Insights は複製先の Web アプリで自動的にセットアップされない
* 簡単認証の設定は複製されない
* Kudu 拡張機能は複製されない
* TiP ルールは複製されない
* データベースの内容は複製されない

### 参照
* [PowerShell を使用した Web アプリの複製](app-service-web-app-cloning.md)
* [Azure App Service での Web アプリのバックアップ](web-sites-backup.md)
* [App Service 環境の作成方法](app-service-web-how-to-create-an-app-service-environment.md)
* [App Service 環境で Web アプリを作成する](app-service-web-how-to-create-a-web-app-in-an-ase.md)
* [App Service 環境の概要](app-service-app-service-environment-intro.md)

<!--Image references-->
[1]: ./media/app-service-web-app-cloning-portal/CloningBlade.png
[2]: ./media/app-service-web-app-cloning-portal/CloneSettings.png

<!---HONumber=AcomDC_0601_2016-->