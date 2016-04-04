<properties
	pageTitle="PowerApps Enterprise で Microsoft 管理の API と IT 管理の API を作成して構成する |Microsoft Azure"
	description="PowerApps で利用可能な API と、それらを Azure ポータルで登録する方法について説明します"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="MandiOhlinger"
	manager="dwrede"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="03/02/2016"
   ms.author="guayan"/>

# Microsoft 管理の API または IT 管理の API を登録する
API には **Microsoft で管理されるもの**と **IT で管理されるもの**があります。PowerApps Enterprise を有効にすると、Microsoft 管理の API が自動的に使用可能になります。メモリ、接続性、信頼なども自動的に管理されます。次の手順では、Twitter アカウントやパスワードなど、任意の特定のユーザー設定を入力します。

IT 管理の API を使用して、メモリ、接続性、信頼などを含めたすべてを制御し、監視します。IT 管理の API には SQL Server や SharePoint Server などのオンプレミスのシステムに接続できる API も含まれています。

> [AZURE.SELECTOR]
- [マネージ API](../articles/power-apps/powerapps-register-from-available-apis.md)
- [ASE の API](../articles/power-apps/powerapps-register-api-hosted-in-app-service.md)
- [Swagger API](../articles/power-apps/powerapps-register-existing-api-from-api-definition.md)

このトピックでは、**Microsoft 管理の API と IT 管理の API を登録する**、という 1 つ目の選択肢について説明します。

#### 開始のための前提条件

- [PowerApps Enterprise](powerapps-get-started-azure-portal.md) にサインアップします。
- [アプリ サービス環境](powerapps-get-started-azure-portal.md)を作成します。


## 使用可能な Microsoft 管理の API を表示する
**Microsoft 管理の** API は PowerApps Enterprise で提供され、Microsoft でもホストされています。多くのシナリオでは、Microsoft 管理の API はアプリに最適です。たとえば、アプリでツイートを送信したり、ファイルを OneDrive にアップロードしたり、Excel ファイルのデータを表示したりする場合、Microsoft 管理の API は適切な選択です。

次のような利点もあります。

- 独自のインスタンスを登録するために利用可能な Microsoft 管理のすべての API を使用できます。 
- ネットワーク、メモリ、またはセキュリティの構成を含むリソースが自動的に監視されます。たとえば、アプリで Excel のデータを表示するためにさらにメモリが必要な場合、メモリが自動的に追加されます。 
- Office や Twitter などのアプリと API の間の信頼が自動的に作成されます。 


#### Microsoft 管理の API

API | 説明 | 手順のリンク
--- | --- | ---
![][31] | **Dropbox**<br/><br/>項目の取得、更新、削除などができます。 | [**作業開始**](powerapps-create-api-dropbox.md)
![][32] | **DynamicsCRM Online**<br/><br/>項目の取得、更新、削除などができます。 | [**作業開始**](powerapps-create-api-crmonline.md)
![][33] | **Excel**<br/><br/>項目の取得、更新、削除などができます。 | [**作業開始**](powerapps-create-api-excel.md)
![][34] | **Google Drive**<br/><br/>項目の取得、更新、削除などができます。 | [**作業開始**](powerapps-create-api-googledrive.md)
![][35] | **Microsoft Translator**<br/><br/>テキストの翻訳、言語の検出などを行います。 | [**作業開始**](powerapps-create-api-microsofttranslator.md)
![][36] | **Office 365 Outlook**<br/><br/>電子メールを管理します。 | [**作業開始**](powerapps-create-api-office365-outlook.md)
![][37] | **Office 365 ユーザー**<br/><br/>ユーザーのプロファイル、その管理者や、直属の部下などにアクセスします。 | [**作業開始**](powerapps-create-api-office365-users.md)
![][38] | **OneDrive**<br/><br/>項目の取得、更新、削除などができます。 | [**作業開始**](powerapps-create-api-onedrive.md)
![][39] | **Salesforce**<br/><br/>項目の取得、更新、削除などができます。 | [**作業開始**](powerapps-create-api-salesforce.md)
![][40] | **SharePoint Online**<br/><br/>項目の取得、更新、削除などができます。 | [**作業開始**](powerapps-create-api-sharepointonline.md)
![][43] | **Twitter**<br/><br/>ツイートの送信、ツイートの検索、フォロワーの表示などができます。 | [**作業開始**](powerapps-create-api-twitter.md)


## 使用可能な IT 管理の API を表示する
**IT 管理の** API は自分で制御し、管理します。Microsoft 管理の環境では実行されません。一部のシナリオでは、独自の IT 管理の環境でこれらの API を使用する方がアプリのニーズに適している場合があります。たとえば、アプリが Twitter API を使用しており、(Microsoft の Twitter キー) ではなく、組織の Twitter キーを使用する必要があるとします。このような状況では、Twitter API を IT 管理の API として構成することをお勧めします。アプリがオンプレミスのデータベースに接続するために SQL Server API を使用するような例もあります。IT 管理の環境では、オンプレミスへの接続のために仮想ネットワークを設定したり、Express Route を使用したりできます。何を選択するのかは自由です。

次のような利点もあります。

- ネットワーク、メモリ、またはセキュリティの構成を含むリソースを自分で監視できます。たとえば、アプリに Excel のデータを表示するために追加のメモリが必要な場合、環境内にどれくらいのメモリを追加するかを制御できます。 
- 信頼を設定し、アプリと API の間のセキュリティを制御できます。たとえば、Office 365 API を Microsoft 管理 (自動信頼) にするか、独自の環境内で Office 365 API を使用する (独自の信頼を作成する) かを決定できます。 
- Microsoft 管理の API は、**すべて** IT 管理にすることもできます。たとえば、Office 365 の独自のインスタンスを作成し、このインスタンスを完全に制御することも可能です。Office 365 の IT 管理 API と、Office 365 の Microsoft 管理 API を同じ環境で使用することができます。これはアプリのニーズによります。
- オンプレミスのシステムに接続する場合や、Bing 検索を使用する場合、セキュリティ、認証、ライセンスなどを制御できます。


#### IT 管理の API
> [AZURE.NOTE] Microsoft 管理の API は**すべて** IT 管理にもできることを忘れないでください。次の API は IT のみで管理され、Microsoft 管理にすることはできません。

API | 説明 | 手順のリンク
--- | --- | ---
![][30] | **Bing 検索**<br/><br/>検索結果の埋め込み、検索機能の追加などができます。 | [**作業開始**](powerapps-create-api-bingsearch.md)
![][42] | **SQL Server**<br/><br/>項目の取得、更新、削除などができます。 | [**作業開始**](powerapps-create-api-sqlserver.md)
![][41] | **SharePoint Server**<br/><br/>項目の取得、更新、削除などができます。 | [**作業開始**](powerapps-create-api-sharepointserver.md)


#### 独自のインスタンスを登録する理由

既定の Microsoft 管理の API を使用する方が便利です。しかし、独自のインスタンスを IT 管理の API として登録すると、多くの利点があります。大まかに言えば、次のような場合に IT 管理の API を作成することをお勧めします。

- ユーザー アクセス、他のシステムへの接続時のセキュリティ、API の呼び出し制限、監視、ポリシーなどの拡張機能を含め、API に対する完全な管理が必要な場合。
- App Service Environment で仮想ネットワークがサポートされているため、オンプレミスのデータにアクセスする場合。
- ビジネス ユーザーが自分では API を使用できない可能性がある場合に、代わりに API を設定する場合。

次の表は、Microsoft 管理の API と IT 管理の API の機能を比較しています。

| 機能 | Microsoft 管理 | IT 管理 |
| ---------- | ----------------- | ------------ |
| API の呼び出し制限 | Microsoft によって定義済み | 自分で定義済み (ポリシーを使用) |
| SaaS に接続するときに、独自のキーを使用する | サポートされていません | サポートされています |
| API ユーザー アクセス | すべてのユーザーに対して有効 | AAD ユーザーおよびグループ レベルで完全に管理可能 |
| API 監視 | サポートされていません | サポートされています |
| API ポリシー | サポートされていません | サポートされています |
| 接続ユーザー アクセス | 表示のみ | AAD ユーザーおよびグループ レベルで完全に管理可能 |
| 接続管理 | 表示のみ | 完全に管理可能 |


## Microsoft 管理の API または IT 管理の API を登録する

1. [Azure ポータル](https://portal.azure.com/)で、職場アカウント (*ユーザー名*@*ユーザーの会社*.com) を使用してサインインします。会社のサブスクリプションに自動的にサインインされます。
2. **[参照]** を選択し、**[PowerApps]** を選択して、**[API の管理]** を選択します。  
![][17]
3. [API の管理] で、**[追加]** を選択します。  
![][18]  
4. **[API の追加]** で、API のプロパティを入力します。  

	- **[名前]** に、API の名前を入力します。入力した名前が、API のランタイム URL に含まれていることを確認してください。名前は意味があり、組織内で一意のものにしてください。
	- **[ソース]** で、**[利用可能な API から]**:  
	![][19] を選択します。
5. **[API]** を選択して、登録する API を選択します。  
![][20]
6. 特定の API を選択し、構成可能なプロパティをすべて追加します。
7. **[追加]** を選択して、手順を完了します。

> [AZURE.TIP] API を登録すると、その API が App Service Environment に登録されます。API が App Service Environment に登録されると、同じ App Service Environment 内の他のアプリでも使用することができます。


## まとめと次のステップ

このトピックでは、PowerApps が既定で提供する利用可能な API の独自のインスタンスを登録する方法について説明しました。PowerApps についての関連トピックとリソースがあります。


- [API プロパティの構成](powerapps-configure-apis.md)
- [ユーザーへの API アクセス許可](powerapps-manage-api-connection-user-access.md)
- [PowerApps でのアプリ作成の開始に関するページ](https://powerapps.microsoft.com/tutorials/)


<!--References-->

[17]: ./media/powerapps-register-from-available-apis/registered-apis-part.png
[18]: ./media/powerapps-register-from-available-apis/add-api-button.png
[19]: ./media/powerapps-register-from-available-apis/add-api-blade.png
[20]: ./media/powerapps-register-from-available-apis/add-api-select-from-marketplace-blade.png
[30]: ./media/powerapps-register-from-available-apis/bingsearch.png
[31]: ./media/powerapps-register-from-available-apis/dropbox.png
[32]: ./media/powerapps-register-from-available-apis/dynamicscrmonline.png
[33]: ./media/powerapps-register-from-available-apis/excel.png
[34]: ./media/powerapps-register-from-available-apis/googledrive.png
[35]: ./media/powerapps-register-from-available-apis/microsofttranslator.png
[36]: ./media/powerapps-register-from-available-apis/office365outlook.png
[37]: ./media/powerapps-register-from-available-apis/office365users.png
[38]: ./media/powerapps-register-from-available-apis/onedrive.png
[39]: ./media/powerapps-register-from-available-apis/salesforce.png
[40]: ./media/powerapps-register-from-available-apis/sharepointonline.png
[41]: ./media/powerapps-register-from-available-apis/sharepointserver.png
[42]: ./media/powerapps-register-from-available-apis/sqlserver.png
[43]: ./media/powerapps-register-from-available-apis/twitter.png

<!-----HONumber=AcomDC_0309_2016-->