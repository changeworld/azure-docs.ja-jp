<properties
	pageTitle="Azure AD Connect Health の FAQ"
	description="この FAQ は、Azure AD Connect Health について寄せられる質問とその回答です。サービスの課金モデル、機能、制限、サポートなど、その使用に関して多く寄せられる質問を取り上げています。"
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/15/2015"
	ms.author="billmath"/>


# Azure AD Connect Health についてよく寄せられる質問 (FAQ)

この FAQ は、Azure AD Connect Health について寄せられる質問とその回答です。サービスの課金モデル、機能、制限、サポートなど、その使用に関して多く寄せられる質問を取り上げています。

## 一般的な質問



**Q: 複数の Azure AD ディレクトリを管理しています。どのようにすればその 1 つを Azure Active Directory Premium に切り替えることができますか。**

右上隅にある、現在サインインしているユーザー名を選択し、適切なアカウントを選択することで、Azure AD ディレクトリを切り替えることができます。ここにアカウントが表示されていない場合は、[サインアウト] を選択し、Azure Active Directory Premium が有効になっているディレクトリのグローバル管理者資格情報を使用してサインインします。

## インストールに関する質問



**Q: 各サーバーに Azure AD Connect Health エージェントをインストールすると、どのような影響があるでしょうか。**

CPU、メモリ消費、ネットワーク帯域幅、ストレージに関して、ADFS サーバーに Microsoft Identity Health エージェントをインストールする影響は最小限です。

おおよその数値を以下に示します。

- CPU 消費率: 約 1% 上昇
- メモリ消費量: システム メモリ合計の最大 10%
- ネットワーク帯域幅の使用量: 1000 件の ADFS 要求につき約 1 MB

>[AZURE.NOTE]エージェントは、Azure と通信できない場合、定義済みの上限に達するまで、データをローカルに保存します。上限に達した後、エージェントがサービスにデータをアップロードできなくなった場合は、新しい ADFS トランザクションによって、“処理された時期が最も古い” トランザクションから順に、“キャッシュされた” トランザクションが上書きされます。

- AD Health エージェントのローカル バッファー ストレージ: 約 20 MB
- 監査チャネルに必要なデータ ストレージ


AD FS Audit Channel for AD Health エージェントがすべてのデータを処理できるように、1,024 MB (1 GB) のディスク領域をプロビジョニングすることをお勧めします。

**Q: Azure AD Connect Health エージェントのインストール時にサーバーを再起動する必要はありますか。**

いいえ。エージェントをインストールする場合、サーバーを再起動する必要はありません。ただし、前提条件の一部のインストール手順では、サーバーの再起動が必要です。

たとえば、Windows Server 2008 R2 では、.NET Framework 4.5 をインストールするにはサーバーの再起動が必要です。


**Q: Azure AD Connect Health サービスはパススルー http プロキシで動作しますか。**

はい。実行中の操作については、HTTP プロキシを使用して送信 http 要求を転送するように Health エージェントを構成できます。詳細については、「[HTTP プロキシを使用するための Azure AD Connect Health エージェントの構成](active-directory-aadconnect-health-agent-install-adfs.md#configure-azure-ad-connect-health-agent-to-use-http-proxy)」をご覧ください。

エージェントの登録時にプロキシを構成する必要がある場合は、Internet Explorer のプロキシ設定を変更する必要があります。<br>Internet Explorer で、[設定]、[インターネット オプション]、[接続]、[LAN の設定] の順に開きます。<br> [LAN にプロキシ サーバーを使用する] をオンにします。<br> HTTP と HTTPS/Secure でプロキシ ポートが異なる場合は、[詳細設定] を選択します。<br>


**Q: Azure AD Connect Health サービスは、Http プロキシに接続するときに基本認証をサポートしますか。**

いいえ。基本認証に対して任意のユーザー名/パスワードを指定するメカニズムは、現在サポートされていません。



## 操作に関する質問



**Q: 自分の AD FS アプリケーション プロキシ サーバーまたは Web アプリケーション プロキシ サーバーで監査を有効にする必要はありますか。**

いいえ。AD FS アプリケーション プロキシ サーバーまたは Web アプリケーション プロキシ サーバーで監査を有効にする必要はありません。AD FS フェデレーション サーバーでのみ有効にする必要があります。



**Q: Azure AD Connect Health アラートはどのように解決すればよいですか。**

Azure AD Connect Health アラートは、成功条件を満たすと解決されます。Azure AD Connect Health エージェントは、定期的に成功条件を検出して、成功条件をサービスにレポートします。一部のアラートは、時間に基づいて抑制されます。つまり、アラートの生成から 48 時間以内に同じエラー条件が観察されない場合、アラートは自動的に解決されます。




**Q: Azure AD Connect Health エージェントを使用するためには、どのファイアウォール ポートを開放すればよいですか。**

Azure AD Health サービス エンドポイントと通信できるようにするには、Azure AD Connect Health エージェント用に TCP/UDP ポート 80、443、および 5671 を開く必要があります。

## 関連リンク

* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [AD FS の Azure AD Connect Health エージェントのインストール](active-directory-aadconnect-health-agent-install-adfs.md)
* [AD FS での Azure AD Connect Health の使用](active-directory-aadconnect-health-adfs.md)
* [Azure AD Connect Health の操作](active-directory-aadconnect-health-operations.md)

<!---HONumber=Oct15_HO4-->