<properties 
pageTitle="Azure Cloud Services のロールでリモート デスクトップ接続を有効にする"
	description="Azure クラウド サービス アプリケーションを構成してリモート デスクトップ接続を許可する方法"
	services="cloud-services"
	documentationCenter=""
	authors="sbtron"
	manager="timlt"
	editor=""/>
<tags 
ms.service="cloud-services"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/31/2015"
	ms.author="saurabh"/>

# PowerShell を使用して Azure Cloud Services のロールでリモート デスクトップ接続を有効にする

>[AZURE.SELECTOR]
- [Azure Portal](cloud-services-role-enable-remote-desktop.md)
- [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
- [Visual Studio](https://msdn.microsoft.com/library/gg443832.aspx)


リモート デスクトップを使用して、Azure で実行されているロールのデスクトップにアクセスできます。リモート デスクトップ接続を使用すると、アプリケーションの実行中にそのアプリケーションの問題のトラブルシューティングや診断を行うことができます。

この記事では、PowerShell を使用して、クラウド サービスのロールでリモート デスクトップを有効にする方法について説明します。この記事で求められる前提条件については、[Azure PowerShell のインストールおよび構成方法](powershell-install-configure.md)に関するページを参照してください。PowerShell では、リモート デスクトップ拡張機能による方法を使用するため、アプリケーションのデプロイ後もリモート デスクトップを有効化できます。


## PowerShell からリモート デスクトップを構成する

[Set-AzureServiceRemoteDesktopExtension](https://msdn.microsoft.com/library/azure/dn495117.aspx) コマンドレットを使用すると、クラウド サービス デプロイの指定したロールまたはすべてのロールでリモート デスクトップを有効にすることができます。このコマンドレットでは、PSCredential オブジェクトを受け入れる *Credential* パラメーターを使用してリモート デスクトップ ユーザーのユーザー名とパスワードを指定できます。

PowerShell を対話形式で使用している場合は、[Get-Credentials](https://technet.microsoft.com/library/hh849815.aspx) コマンドレットを呼び出すことで、PSCredential オブジェクトを簡単に設定できます。

```
	$remoteusercredentials = Get-Credential
```

これにより、リモート ユーザーのユーザー名とパスワードを安全な方法で入力できるダイアログ ボックスが表示されます。

PowerShell は自動化のシナリオで使用されることが多いため、ユーザーとの対話を必要としない方法で PSCredential オブジェクトを設定することもできます。そのためには、最初に、セキュリティで保護されたパスワードを設定する必要があります。まずプレーンテキストのパスワードを指定し、[ConvertTo-SecureString](https://technet.microsoft.com/library/hh849818.aspx) を使用してそのパスワードをセキュリティで保護された文字列に変換します。次に、[ConvertFrom-SecureString](https://technet.microsoft.com/library/hh849814.aspx) を使用して、セキュリティで保護されたこの文字列を、暗号化された標準文字列に変換する必要があります。これで、この暗号化された標準文字列を、[Set-Content](https://technet.microsoft.com/library/ee176959.aspx) を使用してファイルに保存することができます。PSCredential オブジェクトを作成する際は、このファイルから読み取って安全な方法でパスワードを設定できるため、プロンプトでのパスワードを指定したり、プレーンテキストとしてパスワードを保存したりする必要ありません。

セキュリティで保護されたパスワード ファイルを作成するには、次の PowerShell を使用します。

```
	ConvertTo-SecureString -String "Password123" -AsPlainText -Force | ConvertFrom-SecureString | Set-Content "password.txt"
``` 

パスワード ファイル (password.txt) が作成されると、このファイルのみを使用するようになるので、プレーンテキストでパスワードを指定する必要がなくなります。パスワードの更新が必要な場合は、新しいパスワードを使用して上記の PowerShell を再度実行することで、新しい password.txt ファイルを生成できます。

>[AZURE.IMPORTANT]パスワードを設定するときは、[複雑さの要件](https://technet.microsoft.com/library/cc786468.aspx)を満たしていることを確認してください。

セキュリティで保護されたパスワード ファイルから資格情報オブジェクトを作成するには、ファイルの内容を読み取り、[ConvertTo-SecureString](https://technet.microsoft.com/library/hh849818.aspx) を使用して、セキュリティで保護された文字列にもう一度変換する必要があります。[Set-AzureServiceRemoteDesktopExtension](https://msdn.microsoft.com/library/azure/dn495117.aspx) コマンドレットは、資格情報だけでなく、ユーザー アカウントの期限が切れる日時を指定する *Expiration* パラメーターも受け入れます。静的な日時を指定することでこれを設定するか、または単に現在の日付から数日後にアカウントの期限が切れるように設定することもできます。

次の PowerShell の例では、クラウド サービスでリモート デスクトップ拡張機能を設定する方法を示しています。

```
	$servicename = "cloudservice"
	$username = "RemoteDesktopUser"
	$securepassword = Get-Content -Path "password.txt" | ConvertTo-SecureString
	$expiry = $(Get-Date).AddDays(1)
	$credential = New-Object System.Management.Automation.PSCredential $username,$securepassword
	Set-AzureServiceRemoteDesktopExtension -ServiceName $servicename -Credential $credential -Expiration $expiry 
```
また、必要に応じて、リモート デスクトップを有効にするデプロイ スロットおよびロールを指定することもできます。これらのパラメーターが指定されていない場合、コマンドレットは既定で運用環境のデプロイ スロットを使用し、運用環境のデプロイに含まれるすべてのロールでリモート デスクトップを有効にします。

リモート デスクトップ拡張機能は、デプロイに関連付けられています。サービスの新しいデプロイを作成した場合は、新しいデプロイでもう一度リモート デスクトップを有効にする必要があります。デプロイで常にリモート デスクトップを有効にしておく必要がある場合は、リモート デスクトップを有効にする PowerShell スクリプトをデプロイのワークフローに統合することを検討してください。


## ロール インスタンスへのリモート デスクトップ接続
[Get-AzureRemoteDesktopFile](https://msdn.microsoft.com/library/azure/dn495261.aspx) コマンドレットを使用すると、クラウド サービスの特定のロール インスタンスにリモート デスクトップ接続できます。このコマンドレットでは、*LocalPath* パラメーターを使用して RDP ファイルをローカルにダウンロードすることも、*Launch* パラメーターを使用して、クラウド サービスのロール インスタンスにアクセスするための [リモート デスクトップ接続] ダイアログを直接起動することもできます。

```
	Get-AzureRemoteDesktopFile -ServiceName $servicename -Name "WorkerRole1_IN_0" -Launch
```


## サービスでリモート デスクトップ拡張機能が有効になっているかどうかを確認する 
[Get-AzureServiceRemoteDesktopExtension](https://msdn.microsoft.com/library/azure/dn495261.aspx) コマンドレットを使用すると、サービスのデプロイでリモート デスクトップが有効になっているかどうかが表示されます。このコマンドレットによって、リモート デスクトップ ユーザーのユーザー名と、リモート デスクトップ拡張機能が有効になっているロールが返されます。デプロイ スロットは既定で運用環境になっているため、必要に応じて指定できます。

```
	Get-AzureServiceRemoteDesktopExtension -ServiceName $servicename
```

## リモート デスクトップ拡張機能をサービスから削除する 
デプロイで既にリモート デスクトップ拡張機能を有効にしている状態で、リモート デスクトップ設定を更新する必要がある場合は、まずリモート デスクトップ拡張機能を削除してから、新しい設定でもう一度有効にする必要があります。たとえば、リモート ユーザー アカウントに新しいパスワードを設定する必要がある場合やユーザー アカウントの有効期限が切れた場合は、拡張機能を削除した後、新しいパスワードまたは有効期限を指定してもう一度その機能を追加する必要があります。これは、リモート デスクトップ拡張機能が有効になっている既存のデプロイでのみ必要な操作です。新しいデプロイの場合は、単に拡張機能を直接適用できます。

リモート デスクトップ拡張機能をサービスのデプロイから削除するには、[Remove-AzureServiceRemoteDesktopExtension](https://msdn.microsoft.com/library/azure/dn495280.aspx) コマンドレットを使用します。また、必要に応じて、リモート デスクトップ拡張機能を削除するデプロイ スロットおよびロールを指定することもできます。

```
Remove-AzureServiceRemoteDesktopExtension -ServiceName $servicename -UninstallConfiguration

```  

>[AZURE.NOTE]*UninstallConfiguration* パラメーターを使用すると、サービスに適用されていた拡張機能の構成がアンインストールされます。拡張機能のすべての構成は、デプロイによって拡張機能をアクティブ化するためにサービス構成に関連付けられており、デプロイはその拡張機能の構成に関連付けられている必要があります。*UninstallConfiguration* を使用せずに Remove コマンドレットを呼び出すと、拡張機能の構成からデプロイとの関連付けが解除されるため、実質的にはデプロイから拡張機能が削除されることになります。ただし、拡張機能の構成は、サービスに関連付けられたままになります。拡張機能の構成を完全に削除するには、*UninstallConfiguration* パラメーターを使用して Remove コマンドレットを呼び出す必要があります。



## その他のリソース

[Cloud Services の構成方法](cloud-services-how-to-configure.md)

<!---HONumber=September15_HO1-->