<properties
   pageTitle="Marketplace 向けの VM を作成するための PowerShell のセットアップ | Microsoft Azure"
   description="Azure PowerShell をセットアップしてオプションのプロセス フローとして使用し、VM イメージを作成して Azure Marketplace にデプロイし、販売する方法を説明します"
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/04/2016"
   ms.author="hascipio"/>

# Azure Marketplace のプランを作成するための Azure PowerShell のセットアップ
Azure で PowerShell をセットアップする方法の詳細については、「[Azure PowerShell のインストールおよび構成方法](../powershell-install-configure.md)」をご覧ください。証明書方式を使用すると、認証に必要な証明書がダウンロードおよびインポートされるため簡単に処理できます。必要な証明書を取得するには、**Get-AzurePublishSettingsFile** コマンドレットを使用します。求められたらファイルを保存します。証明書を PowerShell セッションにインポートするには、**Import-AzurePublishSettingsFile** コマンドレットを使用します。

PowerShell セッション用に一般的な Microsoft Azure サブスクリプション設定を構成して保存するには、次のように **Set-AzureSubscription** および **Select-AzureSubscription** コマンドレットを使用します。

        Set-AzureSubscription -SubscriptionName “mySubName” -CurrentStorageAccountName “mystorageaccount”
        Select-AzureSubscription -SubscriptionName "mySubName" –Current

最初のコマンドは、既定のストレージ アカウントをサブスクリプションに関連付けます (一部の VM プロビジョニング操作に必要)。2 番目のコマンドは、そのサブスクリプションを現在のサブスクリプションにします (他のコマンドレットによって認識される)。

## 関連項目
- [Microsoft Azure Marketplace へのプランの発行方法](marketplace-publishing-getting-started.md)
- [Marketplace 向けの仮想マシン イメージの作成](marketplace-publishing-vm-image-creation.md)

<!---HONumber=AcomDC_0211_2016-->