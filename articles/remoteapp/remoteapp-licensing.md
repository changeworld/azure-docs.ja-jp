<properties 
    pageTitle="Azure RemoteApp ライセンス" 
    description="Azure RemoteApp のライセンスの仕組みについて説明します。" 
    services="remoteapp" 
	documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="05/28/2015" 
    ms.author="elizapo" />


# RemoteApp のライセンスはどのような仕組みになっていますか。


Azure RemoteApp サービスを設定し、テンプレートを作成したので、ユーザーにアプリケーションを発行する準備ができました。最後にもうひとつ解決することがあります: ライセンスです。RemoteApp のライセンスはどのような仕組みになっていますか。また、RemoteApp を通して共有するアプリケーションについてはどうですか。

RemoteApp は、Windows ライセンスもリモート デスクトップ CAL も必要としません。サブスクリプションにより、RemoteApp 側自体のライセンス処理が行われます (詳細については、「[Azure の料金体系について](../../../pricing/details/remoteapp/)」を参照してください)。

RemoteApp に同梱される Office 365 テンプレート イメージを使用する場合は、*既に* Office 365 ProPlus プランを所有している必要があります。カスタム テンプレートを使用して公開する Office 365 アプリも同様です。自分のサブスクリプションでのアプリを有効化する必要があります。これは評価版でも有料サブスクリプションでも同様です。試行時に Office 365 テンプレート イメージを使用する必要があり、*サブスクリプションをまだ所有していない*場合は、Office 365 ページにアクセスして、評価版サブスクリプションに[サインアップ](https://go.microsoft.com/fwlink/p/?LinkID=403802)する必要があります。詳細については、「[Using Office 365 with Azure RemoteApp](remoteapp-o365.md)」を参照してください。

試用期間中に、Office 365 評価版が必要ない場合は、RemoteApp に付属 Office 2013 Professional Plus テンプレート イメージを使用します。このテンプレート イメージは 30 日間のみ使用でき、有料のコレクションに変換することはできません。

その他のアプリケーションについては、そのアプリケーションを共有するためのライセンスを所有していることを確認します。

ご理解いただけたでしょうか。 法的に共有が可能なすべてのアプリを発行することができます。またプログラムを共有する資格を確認する必要があります。

クラウド コレクションでは CAL またはボリューム ライセンス契約を使用できないことに注意してください。ボリューム ライセンス契約を使用して、ハイブリッド コレクションのアプリケーションをライセンス認証することが*できます* (Office を除く)。ボリューム ライセンス メディアから、テンプレート イメージにインストールする必要があります。アプリケーションのベンダの情報に従ってリモート デスクトップ環境にライセンスをインストールしてください。
 

<!---HONumber=July15_HO4-->