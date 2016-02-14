<properties 
   pageTitle="動的 DNS を使用してホスト名を登録する"
   description="このページでは、独自の DNS サーバーにホスト名を登録するために動的 DNS をセットアップする方法の詳細を示しています。"
   services="virtual-network"
   documentationCenter="na"
   authors="GarethBradshawMSFT"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/21/2016"
   ms.author="joaoma" />

# 動的 DNS を使用してホスト名を登録する
Azure では [VM とロール インスタンスの名前解決](virtual-networks-name-resolution-for-vms-and-role-instances.md)を行います。Azure によって提供される名前解決を超えるニーズがある場合は、独自の DNS サーバーを設置できます。これにより、独自の特定のニーズに合わせて、DNS ソリューションをカスタマイズすることができます。たとえば、オンプレミス AD ドメイン コント ローラーにアクセスするなどです。

Azure には、DNS サーバーに直接レコードを登録する機能 (資格情報など) がないため、多くの場合に代替の準備が必要になります。次にいくつかの一般的なシナリオの概要を示します。

## Windows クライアント ##
ドメインに参加していない Windows クライアントは、起動時や IP アドレスの変更時に、セキュリティ保護されていない DDNS の更新を試みます。DNS 名は、ホスト名とプライマリ DNS サフィックスの組み合わせです。Azure はプライマリ DNS サフィックスを空白のままにしますが、これは VM で[UI](https://technet.microsoft.com/library/cc794784.aspx) または[自動化](https://social.technet.microsoft.com/forums/windowsserver/3720415a-6a9a-4bca-aa2a-6df58a1a47d7/change-primary-dns-suffix)によって、オーバーライドできます。

ドメインに参加している Windows クライアントは、セキュリティ保護された DDNS を使用して、その IP アドレスをドメイン コント ローラーに登録します。ドメイン参加プロセスでは、クライアントにプライマリ DNS サフィックスを設定し、信頼関係を管理します。

## Linux クライアント ##
Linux クライアントは一般に、起動時にそれ自体を DNS サーバーに登録することはなく、DHCP サーバーがそれを行うことを前提としています。Bind パッケージには *nsupdate* と呼ばれるツールが付属しており、これを DDNS の更新の送信に使用できます。DDNS プロトコルは標準化されているため、DNS サーバーで Bind を使用していない場合でも *nsupdate* を使用できます。

Linux DHCP クライアントは IP アドレスが割り当てられるか、変更されたときに、スクリプトを実行できるフックを用意しています。たとえば、*/etc/dhcp/dhclient-exit-hooks.d/* などです。これを使用して、DNS にホスト名を登録できます。次に例を示します。
    
    	#!/bin/sh
    	requireddomain=mydomain.local
    
    	# only execute on the primary nic
    	if [ "$interface" != "eth0" ]
    	then
    		return
    	fi
    
		# when we have a new IP, perform nsupdate
		if [ "$reason" = BOUND ] || [ "$reason" = RENEW ] || 
		   [ "$reason" = REBIND ] || [ "$reason" = REBOOT ]
		then
    		host=`hostname`
	    	nsupdatecmds=/var/tmp/nsupdatecmds
  			echo "update delete $host.$requireddomain a" > $nsupdatecmds
  			echo "update add $host.$requireddomain 3600 a $new_ip_address" >> $nsupdatecmds
  			echo "send" >> $nsupdatecmds

  			nsupdate $nsupdatecmds
		fi

		#done
		exit 0;

*nsupdate* コマンドは、セキュリティ保護された DDNS の更新も実行できます。たとえば、Bind DNS サーバーを使用している場合、公開/秘密キーのペアが[生成](http://linux.yyz.us/nsupdate/)され、キーの公開部分で DNS サーバーが[構成](http://linux.yyz.us/dns/ddns-server.html)されるため、要求の署名を検証できます。DDNS 更新要求に署名するには、*-k* オプションを使用して、*nsupdate* にキーのペアを渡す必要があります。

Windows DNS サーバーを使用している場合、nsupdate の *-g* スイッチ (Windows バージョンの *nsupdate* では使用できません) によって Kerberos 認証を使用できます。Kerberos 認証を使用するには、*kinit* を使用して、資格情報を読み込む ([キータブファイル](http://www.itadmintools.com/2011/07/creating-kerberos-keytab-files.html)などから) と、*nsupdate -g* によって、キャッシュから資格情報が取得されます。

必要に応じて、DNS 検索サフィックスを VM に追加できます。DNS サフィックスは、*/etc/resolv.conf* ファイルに指定します。ほとんどの Linux ディストリビューションはこのファイルの内容を自動的に管理するため、通常は編集できません。ただし、DHCP クライアントの *supersede* コマンドを使用して、サフィックスをオーバーライドできます。*/etc/dhcp/dhclient.conf* で、次を追加します。

		supersede domain-name <required-dns-suffix>;

<!---HONumber=AcomDC_0204_2016-->