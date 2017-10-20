---
title: "Microsoft Azure Cloud Services の構成と管理の問題についてよくあるご質問 | Microsoft Docs"
description: "この記事では、Microsoft Azure Cloud Services の構成と管理についてよくあるご質問を紹介します。"
services: cloud-services
documentationcenter: 
author: genlin
manager: cshepard
editor: 
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 9/20/2017
ms.author: genli
ms.openlocfilehash: 2ce497146abf664b0084cd96963523812f166e3f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="configuration-and-management-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Azure Cloud Services の構成と管理の問題についてよくあるご質問 (FAQ)

この記事では、[Microsoft Azure Cloud Services](https://azure.microsoft.com/services/cloud-services) の構成と管理の問題についてよくあるご質問を紹介します。 サイズについては、 [Cloud Services VM サイズのページ](cloud-services-sizes-specs.md) を参照してください。

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="how-do-i-add-nosniff-to-my-website"></a>自分の Web サイトに "nosniff" を追加する方法を教えてください。
クライアントが MIME の種類をスニッフィングできないように、*web.config* ファイルに設定を追加します。

```xml
<configuration>
   <system.webServer>
      <httpProtocol>
         <customHeaders>
            <add name="X-Content-Type-Options" value="nosniff" />
         </customHeaders>
      </httpProtocol>
   </system.webServer>
</configuration>
```

これは、IIS に設定として追加することもできます。 [一般的なスタートアップ タスク](cloud-services-startup-tasks-common.md#configure-iis-startup-with-appcmdexe)に関する記事を参照して、次のコマンドを使用します。

```cmd
%windir%\system32\inetsrv\appcmd set config /section:httpProtocol /+customHeaders.[name='X-Content-Type-Options',value='nosniff']
```

## <a name="how-do-i-customize-iis-for-a-web-role"></a>IIS の Web ロールをカスタマイズする方法を教えてください。
[一般的なスタートアップ タスク](cloud-services-startup-tasks-common.md#configure-iis-startup-with-appcmdexe)に関する記事にある IIS スタートアップ スクリプトを使用します。

## <a name="i-cannot-scale-beyond-x-instances"></a>X 個のインスタンスを超えて拡張できない
Azure サブスクリプションには、使用できるコアの数に制限があります。 使用可能なすべてのコアを既に使用している場合、スケーリングは機能しません。 たとえば、コア数が 100 に制限されている場合、これは、クラウド サービスでは A1 サイズの仮想マシン インスタンスを 100 個、または A2 サイズの仮想マシン インスタンスを 50 個使用できることを意味します。

## <a name="how-can-i-implement-role-based-access-for-cloud-services"></a>Cloud Services にロールベースのアクセスを実装する方法を教えてください。
Cloud Services は、Azure Resource Manager ベースのサービスではないため、ロールベースのアクセス制御 (RBAC) モデルをサポートしていません。

「[Azure RBAC と従来のサブスクリプションの管理者の比較](../active-directory/role-based-access-control-what-is.md#azure-rbac-vs-classic-subscription-administrators)」をご覧ください。

## <a name="how-do-i-set-the-idle-timeout-for-azure-load-balancer"></a>Azure Load Balancer のアイドル タイムアウトを設定する方法を教えてください。
サービス定義 (csdef) ファイルで、次のようにしてタイムアウトを指定できます。

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceDefinition name="mgVS2015Worker" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRole1" vmsize="Small">
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
    </ConfigurationSettings>
    <Imports>
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
    <Endpoints>
      <InputEndpoint name="Endpoint1" protocol="tcp" port="10100"   idleTimeoutInMinutes="30" />
    </Endpoints>
  </WorkerRole>
```
詳細については、「[New: Configurable Idle Timeout for Azure Load Balancer](https://azure.microsoft.com/blog/new-configurable-idle-timeout-for-azure-load-balancer/) (Azure Load Balancer の構成可能なアイドル タイムアウト)」をご覧ください。

## <a name="can-microsoft-internal-engineers-rdp-to-cloud-service-instances-without-permission"></a>Microsoft 内部のエンジニアは、許可なくクラウド サービスのインスタンスに RDP 接続することはできますか。
Microsoft では、所有者、またはその指名を受けた担当者からの書面 (電子メールまたはその他の書面による通信) による許可なしで、内部のエンジニアがお客様のクラウド サービスに RDP 接続することがないように、厳格な手続きに従っております。

## <a name="why-is-the-certificate-chain-of-my-cloud-service-ssl-certificate-incomplete"></a>私のクラウド サービスの SSL 証明書の証明書チェーンが不完全なのはなぜですか。
Microsoft では、リーフ証明書だけではなく、完全な証明書チェーン (リーフ証明書、中間証明書、およびルート証明書) をインストールすることをお客様にお勧めしています。 お客様がリーフ証明書だけをインストールした場合、CTL を探索して証明書チェーンを構築することについては Windows の処理に頼ることになります。 Windows が証明書を検証しようとしたときに、Azure または Windows Update で断続的なネットワークの問題や DNS の問題が発生すると、証明書が無効と見なされる可能性があります。 完全な証明書チェーンをインストールすると、この問題を回避できます。 これを行う方法については、ブログ記事「[How to install a chained SSL certificate](https://blogs.msdn.microsoft.com/azuredevsupport/2010/02/24/how-to-install-a-chained-ssl-certificate/) (チェーンされた SSL 証明書をインストールする方法)」をご覧ください。

## <a name="how-do-i-associate-a-static-ip-address-to-my-cloud-service"></a>私のクラウド サービスに静的 IP アドレスを関連付ける方法を教えてください。
静的 IP アドレスを設定するには、予約済み IP を作成する必要があります。 この予約済み IP は、新しいクラウド サービスに、または既存のデプロイに関連付けることができます。 詳細については、次のドキュメントをご覧ください。
* [予約済み IP アドレスを作成する方法](../virtual-network/virtual-networks-reserved-public-ip.md#manage-reserved-vips)
* [既存のクラウド サービスの IP アドレスを予約する](../virtual-network/virtual-networks-reserved-public-ip.md#reserve-the-ip-address-of-an-existing-cloud-service)
* [予約済み IP を新しいクラウド サービスに関連付ける](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-new-cloud-service)
* [予約済み IP を実行中のデプロイに関連付ける](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-running-deployment)
* [サービス構成ファイルを使用してクラウド サービスに予約済み IP を関連付ける](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file)

## <a name="what-is-the-quota-limit-for-my-cloud-service"></a>クラウド サービスのクォータ制限とは何ですか。
[サービス固有の制限](../azure-subscription-service-limits.md#subscription-limits)のページをご覧ください。

## <a name="why-does-the-drive-on-my-cloud-service-vm-show-very-little-free-disk-space"></a>クラウド サービス VM 上のドライブで表示される空きディスク領域が非常に小さいのはなぜですか。
これは想定される動作であり、アプリケーションに問題が発生することはないはずです。 Azure PaaS VM の %approot% ドライブでジャーナルがオンになっているため、実質的に、ファイルが通常占有する領域の 2 倍の容量が消費されます。 ただし、この件で問題が起きないようにするには、いくつかの事柄に注意する必要があります。

%approot% ドライブのサイズは、<.cspkg のサイズ + ジャーナルの最大サイズ + 空き領域のマージン> の計算値と、1.5 GB のうち、どちらか大きいほうになります。 VM のサイズは、この計算に影響を与えません。 (VM のサイズは、一時的な C: ドライブのサイズにのみ影響を与えます。) 

%approot% ドライブへの書き込みはサポートされていません。 Azure VM に対して書き込む場合は、一時的な LocalStorage リソース (または、BLOB ストレージ、Azure Files など、その他のオプション) に対して書き込む必要があります。 したがって、%approot% フォルダー上の空き領域の量は重要ではありません。 アプリケーションが %approot% ドライブに対して書き込んでいるかどうか不明の場合は、サービスを数日間実行し、"実行前" と "実行後" のサイズを比較することができます。 

Azure は、%approot% ドライブに対して何も書き込みません。 .cspkg ファイルから VHD が作成され、Azure VM にマウントされた後、このドライブに書き込むことがある唯一の機能はアプリケーションです。 

ジャーナルの設定は構成不可であるため、オフにすることはできません。

## <a name="what-are-the-features-and-capabilities-that-azure-basic-ipsids-and-ddos-provides"></a>Azure の基本的な IPS/IDS と DDoS が提供する機能には何がありますか。
Azure では、脅威に対する防御のため、データセンターの物理サーバーに IPS/IDS を備えています。 それに加えて、お客様は、Web アプリケーション ファイアウォール、ネットワーク ファイアウォール、マルウェア対策、侵入検出/防御システム (IDS/IPS) などのサード パーティ製のセキュリティ ソリューションをデプロイすることができます。 詳細については、「[データと資産を保護し、国際的なセキュリティ基準に準拠](https://www.microsoft.com/en-us/trustcenter/Security/AzureSecurity)」をご覧ください。

Microsoft では、脅威を検出するためにサーバー、ネットワーク、アプリケーションを継続的に監視しています。 Azure の多面的な脅威管理アプローチでは、侵入の検出、分散型サービス拒否 (DDoS) 攻撃の防止、侵入テスト、行動分析、異常検出、機械学習を利用して、常に防御を強化し、リスク軽減に努めています。 Azure 向けの Microsoft マルウェア対策は、Azure Cloud Services および Virtual Machines を保護します。 お客様は、Web アプリケーション ファイアウォール、ネットワーク ファイアウォール、マルウェア対策、侵入検出/防御システム (IDS/IPS)、その他、サード パーティ製のセキュリティ ソリューションを必要に応じてデプロイすることができます。

## <a name="why-does-iis-stop-writing-to-the-log-directory"></a>IIS によるログ ディレクトリへの書き込みが停止するのはなぜですか。
ログ ディレクトリに書き込むためのローカル ストレージ クォータを使い果たしたからです。 これを修正するには、次の 3 つのいずれかの操作を行います。
* IIS の診断を有効にし、診断を定期的に BLOB ストレージに移動する。
* ログ ディレクトリからログ ファイルを手動で削除する。
* ローカル リソースのクォータ制限を大きくする。

詳細については、以下のドキュメントをご覧ください。
* [Azure Storage への診断データの保存と表示](cloud-services-dotnet-diagnostics-storage.md)
* [IIS Logs stops writing in cloud service](https://blogs.msdn.microsoft.com/cie/2013/12/21/iis-logs-stops-writing-in-cloud-service/) (IIS ログがクラウド サービスで書き込みを停止する)

## <a name="what-is-the-purpose-of-the-windows-azure-tools-encryption-certificate-for-extensions"></a>"Windows Azure Tools Encryption Certificate for Extensions" の目的は何ですか。
拡張機能がクラウド サービスに追加されると、必ず、これらの証明書が自動的に作成されます。 ほとんどの場合、これは WAD 拡張機能または RDP 拡張機能ですが、その他のマルウェア対策やログ コレクター拡張機能であることもあります。 これらの証明書は、拡張機能のプライベート構成を暗号化および復号化するためだけに使用されます。 有効期限の日付は確認されないため、証明書の有効期限が切れていてもかまいません。 

これらの証明書を無視することができます。 証明書をクリーンアップする場合、それらの証明書すべての削除を試行することができます。 使用中の証明書を削除しようとすると、Azure からエラーがスローされます。

## <a name="how-can-i-generate-a-certificate-signing-request-csr-without-rdp-ing-in-to-the-instance"></a>インスタンスに "RDP 接続" せずに証明書署名要求 (CSR) を生成する方法を教えてください。
次のガイダンス ドキュメントをご覧ください。

>[Obtaining a certificate for use with Windows Azure Web Sites (WAWS)](https://azure.microsoft.com/blog/obtaining-a-certificate-for-use-with-windows-azure-web-sites-waws/) (Windows Azure Web サイト (WAWS) で使用する証明書の取得)

CSR は、テキスト ファイルであることにご注意ください。 必ずしも、最終的に証明書が使用されるマシンから作成しないでもかまいません。 このドキュメントは App Service 用に書かれるものですが、CSR の作成は汎用的で、Cloud Services にも適用されます。

## <a name="how-can-i-add-an-antimalware-extension-for-my-cloud-services-in-an-automated-way"></a>Cloud Services にマルウェア対策拡張機能を自動的に追加する方法を教えてください。

スタートアップ タスクで PowerShell スクリプトを使用してマルウェア対策拡張機能を有効にすることができます。 次の記事の手順に従って実装してください。 
 
- [PowerShell のスタートアップ タスクを作成する](cloud-services-startup-tasks-common.md#create-a-powershell-startup-task)
- [Set-AzureServiceAntimalwareExtension](https://docs.microsoft.com/powershell/module/Azure/Set-AzureServiceAntimalwareExtension?view=azuresmps-4.0.0 )

マルウェア対策デプロイ シナリオの詳細とポータルから有効にする方法については、「[マルウェア対策のデプロイ シナリオ](../security/azure-security-antimalware.md#antimalware-deployment-scenarios)」を参照してください。

## <a name="how-to-enable-server-name-indication-sni-for-cloud-services"></a>Cloud Services で Server Name Indication (SNI) を有効にする方法を教えてください。

Cloud Services で SNI を有効にするには、次のいずれかの方法を使用します。

### <a name="method-1-use-powershell"></a>方法 1: PowerShell を使用する

SNI バインドは、以下のようにクラウド サービス ロール インスタンスのスタートアップ タスクで PowerShell コマンドレット **New-WebBinding** を使用して構成できます。
    
    New-WebBinding -Name $WebsiteName -Protocol "https" -Port 443 -IPAddress $IPAddress -HostHeader $HostHeader -SslFlags $sslFlags 
    
[こちら](https://technet.microsoft.com/library/ee790567.aspx)の説明のように、$sslFlags には次の値のいずれかを使用できます。

|値|意味|
------|------
|0|SNI なし|
|1|SNI が有効 |
|2 |中央証明書ストアを使用する SNI 以外のバインド|
|3|中央証明書ストアを使用する SNI バインド |
 
### <a name="method-2-use-code"></a>方法 2: コードを使用する

SNI バインドは、こちらの[ブログ投稿](https://blogs.msdn.microsoft.com/jianwu/2014/12/17/expose-ssl-service-to-multi-domains-from-the-same-cloud-service/)のように、ロール スタートアップでコードを使用して構成することもできます。

    
    //<code snip> 
                    var serverManager = new ServerManager(); 
                    var site = serverManager.Sites[0]; 
                    var binding = site.Bindings.Add(“:443:www.test1.com”, newCert.GetCertHash(), “My”); 
                    binding.SetAttributeValue(“sslFlags”, 1); //enables the SNI 
                    serverManager.CommitChanges(); 
    //</code snip> 
    
SNI バインドを有効にするには、上記のいずれかのアプローチを使用して、まずスタートアップ タスクまたはコードを使用して、各ホスト名に対応する証明書 (*.pfx) をロール インスタンスにインストールする必要があります。

## <a name="how-can-i-add-tags-to-my-azure-cloud-service"></a>Azure Cloud Service にタグを追加する方法を教えてください。 

クラウド サービスはクラシック リソースです。 Azure Resource Manager で作成したリソースだけがタグに対応しています。 クラウド サービスなど、クラシック リソースにタグを適用することはできません。 

## <a name="how-to-enable-http2-on-cloud-services-vm"></a>Cloud Services VM で HTTP/2 を有効にする方法を教えてください。

Windows 10 と Windows Server 2016 は、クライアントとサーバー側の両方で HTTP/2 に対応しています。 クライアント (ブラウザー) が、TLS 拡張機能で HTTP/2 とネゴシエートする TLS 上の IIS サーバーに接続している場合、サーバー側で変更を加える必要はありません。 これは、HTTP/2 の使用を指定した h2-14 ヘッダーが、既定で TLS 上で送信されるためです。 一方、クライアントが HTTP/2 にアップグレードするために Upgrade ヘッダーを送信する場合は、サーバー側で次の変更を加えることで、Upgrade が機能し、最終的に HTTP/2 接続できるようにする必要があります。 

1. regedit.exe を実行します。
2. レジストリ キー HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HTTP\Parameters を参照します。
3. **DuoEnabled** という新しいダブルワード値を作成します。
4. 値を 1 に設定します。
5. サーバーを再起動します。
6. **[既定の Web サイト]** に移動し、**[バインド]** で、作成した自己署名証明書を使用して新しい TLS バインドを作成します。 

詳細については、次を参照してください。

- [HTTP/2 on IIS](https://blogs.iis.net/davidso/http2) (IIS 上の HTTP/2)
- [ビデオ: HTTP/2 in Windows 10: Browser, Apps and Web Server](https://channel9.msdn.com/Events/Build/2015/3-88) (Windows 10 の HTTP/2: ブラウザー、アプリ、Web サーバー)
         

上記の手順は、スタートアップ タスクで、新しい PaaS インスタンスを作成するたびに、システム レジストリで上記の変更を実行するように自動化することができます。 詳細については、「[クラウド サービスのスタートアップ タスクを構成して実行する方法](cloud-services-startup-tasks.md)」を参照してください。

 
この処理を完了すると、次のいずれかの方法を使用して、HTTP/2 が有効かどうかを確認できるようになります。

- IIS ログでプロトコルのバージョンを有効にして、IIS ログを確認します。 ログには HTTP/2 が表示されます。 
- Internet Explorer/Edge で F12 Developer Tool を有効にして、[ネットワーク] タブに切り替えてプロトコルを確認します。 

詳細については、「[HTTP/2 on IIS](https://blogs.iis.net/davidso/http2)」(IIS 上の HTTP/2) を参照してください。