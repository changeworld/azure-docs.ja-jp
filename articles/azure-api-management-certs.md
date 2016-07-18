<properties 
	pageTitle="Azure Management API 証明書のアップロード | Azure Microsoft" 
	description="Azure クラシック ポータルの Management API 証明書をアップロードする方法について説明します。" 
	services="cloud-services" 
	documentationCenter=".net" 
	authors="Thraka" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="na" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/18/2016"
	ms.author="adegeo"/>


# Azure Management API Management 証明書のアップロード

Azure のサービス管理 API に対する認証は、管理証明書で行うことができます。これらの証明書は、各種 Azure サービスの構成とデプロイメントを自動化するために、今後多くのプログラムおよびツール (Visual Studio、Azure SDK など) で使用されることになります。**これは Azure クラシック ポータルにのみ適用されます**。

>[AZURE.WARNING] ご注意ください。 これらの種類の証明書を使用して認証する場合、関連付けられているサブスクリプションを管理できます。

Azure の証明書の詳細 (自己署名証明書の作成を含む) については、[こちら](cloud-services/cloud-services-certs-create.md#what-are-management-certificates)をご覧ください。

クライアント コードの認証に [Azure Active Directory](/services/active-directory/) を使用することで自動化を図ることもできます。

## 管理証明書のアップロード

管理証明書 (公開キーのみを含んだ .cer ファイル) を作成したら、それをポータルにアップロードすることができます。ポータルで証明書が使用可能な状態になると、対になる証明書 (秘密キー) を持つすべての人が、Management API を経由して接続し、そのサブスクリプションに関連付けられているリソースにアクセスすることができます。

1. [Azure クラシック ポータル](http://manage.windowsazure.com)にログインします。

2. 証明書を関連付ける正しいサブスクリプションが選択されていることを確認します。ポータルの右上にある **[サブスクリプション]** テキストを押します。

	![設定](./media/azure-api-management-certs/subscription.png)

3. 正しいサブスクリプションが選択されたら、ポータルの左側にある **[設定]** を押します (下にスクロールすることが必要になる場合があります)。
    
    ![Settings](./media/azure-api-management-certs/settings.png)

4. **[管理証明書]** タブを押します。

    ![Settings](./media/azure-api-management-certs/certificates-tab.png)
    
5. **[アップロード]** ボタンを押します。

    ![Settings](./media/azure-api-management-certs/upload.png)
    
6. ダイアログの情報を入力し、確認のための**チェックマーク**を押します。

    ![設定](./media/azure-api-management-certs/upload-dialog.png)

## 次のステップ

サブスクリプションへの管理証明書の関連付けはこれで完了です。対になる証明書がローカルにインストールされていれば、プログラムから [Service Management REST API](https://msdn.microsoft.com/library/azure/mt420159.aspx) に接続し、同じサブスクリプションに関連付けられているさまざまな Azure リソースを自動化できます。

<!---HONumber=AcomDC_0706_2016-->