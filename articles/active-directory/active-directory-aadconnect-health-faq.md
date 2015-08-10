<properties 
	pageTitle="Azure AD Connect Health の FAQ" 
	description="この FAQ は、Azure AD Connect Health について寄せられる質問とその回答です。サービスの課金モデル、機能、制限、サポートなど、その使用に関して多く寄せられる質問を取り上げています。" 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/12/2015"
	ms.author="billmath"/>


# Azure AD Connect Health についてよく寄せられる質問 (FAQ)

この FAQ は、Azure AD Connect Health について寄せられる質問とその回答です。サービスの課金モデル、機能、制限、サポートなど、その使用に関して多く寄せられる質問を取り上げています。

## 一般的な質問



**Q: Azure Active Directory にいくつかのテナントがあります。どのようにすればその 1 つを Azure Active Directory Premium に切り替えることができますか。**

左側のナビゲーション バーで [ホーム] を選択して、右上隅の現在ログオンしているユーザー名を選択して、正しいテナントのアカウントを選択すると、Azure AD テナントを切り替えることができます。そこにテナント アカウントが表示されない場合、[サインアウト] を選択し、Azure Active Directory Premium のグローバルのテナント管理資格情報を使用しログインします。


## インストールに関する質問



**Q: 各サーバーに Azure AD Connect Health エージェントをインストールすると、どのような影響があるでしょうか。**

CPU、メモリ消費、ネットワーク帯域幅、ストレージに関して、ADFS サーバーに Microsoft Identity Health エージェントをインストールする影響は最小限です。

おおよその数値を以下に示します。

- CPU 消費率: 約 1% 上昇
- メモリ消費量: システム メモリ合計の最大 10%
- ネットワーク帯域幅の使用量: 1000 件の ADFS 要求につき約 1 MB
>[AZURE.NOTE]エージェントが Azure と通信できなくなった場合、エージェントは、システム メモリの合計の 10% を上限に、データをローカルで格納します。物理メモリの合計の 10% に達し、エージェントがデータをサービスにアップロードできなくなると、新しい ADFS トランザクションにより、"キャッシュされている" トランザクションが最近最も使われていないものから順に上書きされます。


- AD Health エージェントのローカル バッファー ストレージ: 約 20 MB
- 監査チャネルに必要なデータ ストレージ


AD FS Audit Channel for AD Health エージェントがすべてのデータを処理できるように、1,024 MB (1 GB) のディスク領域をプロビジョニングすることをお勧めします。

**Q: Azure AD Connect Health エージェントのインストール時にサーバーを再起動する必要はありますか。**

いいえ。エージェントをインストールする場合、サーバーを再起動する必要はありません。ただし、前提条件の一部のインストール手順では、サーバーの再起動が必要です。

たとえば、Windows Server 2008 R2 では、.NET Framework 4.5 をインストールするにはサーバーの再起動が必要です。


**Q: Azure AD Connect Health サービスはパススルー http プロキシで動作しますか。**

はい。登録プロセスも通常の動作も、送信 http 要求を転送する明示的なプロキシ設定で機能します。"Netsh WinHttp set Proxy" は、エージェントが Microsoft Windows HTTP サービスではなく System.Net を使用して Web 要求を行うので、このケースでは動作しません。

Register-AdHealthAgent (インストールの最後の手順) を実行する前の任意の時点で実行してください。


- 手順 1 – machine.config ファイルにエントリを追加する


machine.config ファイルを探します。ファイルのパスは、%windir%\Microsoft.NET\Framework64\[version]\config\machine.config です。</li>

machine.config ファイルの <configuration></configuration> 要素の下に、次のエントリを追加します。
 
		
	<system.net>  
			<defaultProxy useDefaultCredentials="true">
       		<proxy 
        usesystemdefault="true" 
        proxyaddress="http://YOUR.PROXY.HERE.com"  
        bypassonlocal="true"/>
		</defaultProxy>
	</system.net> 

 

<defaultProxy> に関する情報は、[こちら](https://msdn.microsoft.com/library/kd3cf2ex(v=vs.110).aspx) も参照してください。

この設定により、システム全体の .NET アプリケーションが、http .NET 要求を行うときにユーザーが明示的に定義したプロキシを使用するように構成されます。個々の app.config を個別に変更することは、自動更新の間に元に戻るので、お勧めできません。machine.config のみを変更対象にすれば、変更するファイルが 1 つで済み、更新を経ても変更が維持されます。

- 手順 2 - [インターネット オプション] でプロキシを構成する

Internet Explorer から [設定]、[インターネット オプション]、[接続]、[LAN の設定] の順に開きます。

[LAN にプロキシ サーバーを使用する] をオンにします。

HTTP と HTTPS/Secure でプロキシ ポートが異なる場合は、[詳細設定] を選択します。




**Q: Azure AD Connect Health サービスは、Http プロキシに接続するときに基本認証をサポートしますか。**

いいえ。基本認証に対して任意のユーザー名/パスワードを指定するメカニズムは、現在サポートされていません。


## 操作に関する質問



**Q: 自分の AD FS アプリケーション プロキシ サーバーまたは Web アプリケーション プロキシ サーバーで監査を有効にする必要はありますか。**

いいえ。AD FS アプリケーション プロキシ サーバーまたは Web アプリケーション プロキシ サーバーで監査を有効にする必要はありません。AD FS フェデレーション サーバーでのみ有効にする必要があります。



**Q: Azure AD Connect Health アラートはどのように解決すればよいですか。**

Azure AD Connect Health アラートは、成功条件を満たすと解決されます。Azure AD Connect Health エージェントは、定期的に成功条件を検出して、成功条件をサービスにレポートします。一部のアラートは、時間に基づいて抑制されます。つまり、アラートの生成から 48 時間以内に同じエラー条件が観察されない場合、アラートは自動的に解決されます。




**Q: Azure AD Connect Health エージェントを使用するためには、どのファイアウォール ポートを開放すればよいですか。**

Azure AD Health サービス エンドポイントと通信できるようにするには、Azure AD Connect Health エージェント用に TCP/UDP ポート 80 と 443 を開く必要があります。

<!---HONumber=July15_HO5-->