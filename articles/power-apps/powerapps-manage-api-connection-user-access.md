<properties
	pageTitle="新しい API の追加または作成と PowerApps におけるユーザーへのアクセス許可の付与 | Microsoft Azure"
	description="Azure ポータルでの新しい API、接続、または接続プロファイルの追加、作成、構成とユーザーへのアクセス許可権限の付与"
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
   ms.author="litran"/>


# 新しい API の追加、接続の追加、ユーザーへのアクセス許可

API は[App Service 環境](powerapps-get-started-azure-portal.md)に存在します。API が作成されるのは PowerApps 用の利用可能な API から、App Service 環境でホストされる API アプリから、または Swagger 2.0 からです。PowerApps に容易に追加できる事前構築の API はたくさんあります。自分の API を JSON 形式または Swagger 2.0 にてアップロードすることもできます。

こちらのトピック:

- API を PowerApps に追加し、社内のユーザーに API 使用のためのアクセス許可を付与する手順 (API のプロパティ変更を含む) を説明します。
- API に接続を追加し、社内のユーザーに接続使用のためのアクセス許可を付与する手順を説明します。


#### 開始のための前提条件

- [Azure サブスクリプションの PowerApps](powerapps-get-started-azure-portal.md) を有効にする。
- [App Service 環境](powerapps-get-started-azure-portal.md)の作成
- 次の方法のいずれかを使用して API を作成する。  
	- [Microsoft 管理の API または IT 管理の API](powerapps-register-from-available-apis.md) の作成
	- [App Service 環境](powerapps-register-api-hosted-in-app-service.md)内でホストされる API の作成
	- [Swagger 2.0 API の定義](powerapps-register-existing-api-from-api-definition.md)を使用して作成


## ユーザーへの API アクセス許可
API を作成して、App Service 環境に追加したら、社内のユーザーに使用許可を与える手順に入ります。

1. PowerApps にて、**[管理 API]** を選択し、API を選択します。たとえば、*MS Power BI* API を作成した場合は、それを選択して、そのブレードを開きます。**API ユーザー アクセス**を選択します。: ![][1]  

2. **追加**を選択してユーザーを追加し、権限を選択します。完了したら、**追加**を選択して変更を保存します。ユーザーまたはグループの数が **API ユーザー アクセス**ウィンドウで増加します。


## API への新しい接続の追加
次の手順は API に対する "接続" の作成です。これは接続文字列のようなものです。これにより、API が正常に「バックエンド」システムに接続可能になります。PowerApps Enterprise パブリック プレビューについては、SQL Server の接続のみが追加および構成可能です。これでさらに追加されました。

- [SQL Server の接続の作成](powerapps-create-api-sqlserver.md)

## ユーザーへの接続に対するランタイム アクセスの付与
社内ユーザーに接続を使用するアクセス許可を行います。

1. API を開き、[**接続**]を選択して、特定の接続を選択します。これにより、接続名が上部に表示されている新しいブレードが開きます。 
2. この新しいブレードで、[**接続ユーザー アクセス**] を選択します。次の例では、**ハイブリッド トンネル**接続が選択されています。新しいブレードが開き、こちらで**接続ユーザー アクセス**を選択します。: ![][2]
  
3. **接続ユーザー アクセス**で、**追加**を選択し、付与するアクセス許可を選択します。: ![][3]
  
4. ユーザーまたはグループを追加します。**追加**を選択して、変更を保存します。

ユーザーが API とその接続に対してアクセス許可を得たので、ユーザーはこれらの API を PowerApps で作成したアプリに追加できるようになりました。具体的には次の処理が行われます。

- ユーザーは PowerApps で**利用可能な接続**の下に一覧表示されている API を参照できます。
- ユーザーは PowerApps で**接続**の下にある接続を参照できます。


## API の削除
以前に追加した API を削除することもできます。PowerApps にて、**管理 API**から API を選択し、**削除**を選択します。: ![][4]


## まとめと次のステップ
このトピックで説明した内容は次のとおりです。

- API を追加して、社内ユーザーに使用する権限を付与しました。これらの手順により、ランタイム アクセスをいつでも管理することが可能です。たとえば、ユーザー A が退職した場合、Azure ポータルを使用してユーザーのアクセス許可を容易に削除できます。ユーザー B が入社した場合にも同じシナリオです。
- 接続を追加しました (接続文字列に似ています)。この手順により、Azure でホストされている API がオンプレミスの SQL Server のようにシステムに接続できるようになります。また、社内のユーザーに接続を使用できるアクセス許可を付与しました。 
- タスクごとに異なるブレードにて作業しました。接続を追加するため、API を開いてそのブレードを使用します。ユーザーのアクセスを許可するには、アクセスを付与する対象に合わせて、API または接続を開きます。 
- App Service 環境で作成した API はいずれも削除が可能です。

次は、[PowerApps の管理および監視](powerapps-manage-monitor-usage.md)が可能です。

[1]: ./media/powerapps-manage-api-connection-user-access/apiuseraccess.png
[2]: ./media/powerapps-manage-api-connection-user-access/connectionuseraccess.png
[3]: ./media/powerapps-manage-api-connection-user-access/selectpermission.png
[4]: ./media/powerapps-manage-api-connection-user-access/deleteapi.png

<!---HONumber=AcomDC_0309_2016-->