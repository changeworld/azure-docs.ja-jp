<properties
	pageTitle="PowerShell を使った SSL 証明書のバインド"
	description="PowerShell を使って SSL 証明書を Web アプリにバインドする方法について説明します。"
	services="app-service\web"
	documentationCenter=""
	authors="ahmedelnably"
	manager="stefsch"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/13/2016"
	ms.author="ahmedelnably"/>

# PowerShell を使った Azure App Service SSL 証明書のバインド #

Microsoft Azure PowerShell バージョン 1.1.0 のリリースに伴って新しいコマンドレットが追加され、既存の SSL 証明書または新しい SSL 証明書を既存の Web アプリにバインドできるようになりました。

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]


## 新しい SSL 証明書のアップロードとバインド ##

シナリオ: ユーザーは、いずれかの Web アプリに SSL 証明書をバインドしたいと考えています。

Web アプリを含むリソース グループの名前、Web アプリ名、ユーザーのコンピューターにおける証明書の .pfx ファイルのパス、証明書のパスワード、カスタム ホスト名がわかっていれば、次の PowerShell コマンドを使ってその SSL バインドを作成できます。

    New-AzureRmWebAppSSLBinding -ResourceGroupName myresourcegroup -WebAppName mytestapp -CertificateFilePath PathToPfxFile -CertificatePassword PlainTextPwd -Name www.contoso.com

## 既存の SSL 証明書のアップロードとバインド ##

シナリオ: ユーザーは、いずれかの Web アプリに、アップロード済みの SSL 証明書をバインドしたいと考えています。

次のコマンドを使って、特定のリソース グループにアップロードしてある証明書の一覧を取得できます。

	Get-AzureRmWebAppCertificate -ResourceGroupName myresourcegroup

証明書は、特定の場所とリソース グループに対してローカルであるため、必要な証明書とは別の場所およびリソース グループに構成済みの Web アプリがある場合は、証明書をもう一度アップロードする必要があります。

Web アプリを含むリソース グループの名前、Web アプリ名、証明書の拇印、カスタム ホスト名がわかっていれば、次の PowerShell コマンドを使ってその SSL バインドを作成できます。

    New-AzureRmWebAppSSLBinding -ResourceGroupName myresourcegroup -WebAppName mytestapp -Thumbprint <certificate thumbprint> -Name www.contoso.com

## 既存の SSL バインドの削除  ##

シナリオ: ユーザーは、既存の SSL バインドを削除したいと考えています。

Web アプリを含むリソース グループの名前、Web アプリ名、カスタム ホスト名がわかっていれば、次の PowerShell コマンドを使ってその SSL バインドを削除できます。

    Remove-AzureRmWebAppSSLBinding -ResourceGroupName myresourcegroup -WebAppName mytestapp -Name www.contoso.com

削除対象の SSL バインドがその場所でその証明書を使っている最後のバインドであった場合も、既定でその証明書は削除されますが、DeleteCertificate オプションを使って証明書を保持しておくこともできます。

	Remove-AzureRmWebAppSSLBinding -ResourceGroupName myresourcegroup -WebAppName mytestapp -Name www.contoso.com -DeleteCertificate $false

### 参照 ###
- [App Service 環境の概要](app-service-app-service-environment-intro.md)
- [Azure リソース マネージャーでの Azure PowerShell の使用](../powershell-azure-resource-manager.md)

<!---HONumber=AcomDC_0121_2016-->