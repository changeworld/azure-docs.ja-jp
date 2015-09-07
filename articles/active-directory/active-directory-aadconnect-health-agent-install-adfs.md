<properties 
	pageTitle="Azure AD Connect Health AD FS エージェントのインストール | Microsoft Azure"
	description="これは、Active Directory フェデレーション サービス (AD FS) エージェントのインストールについて説明する Azure AD Connect Health のページです。"
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
	ms.date="08/14/2015"
	ms.author="billmath"/>






# AD FS の Azure AD Connect Health エージェントのインストール

このドキュメントでは、AD FS 向けの Azure AD Connect Health エージェントをサーバーにインストールして構成する手順を紹介します。

>[AZURE.NOTE]Azure AD Connect Health のインスタンスでデータを表示するためには、あらかじめ対象サーバーに Azure AD Connect Health エージェントをインストールしておく必要があります。[こちら](active-directory-aadconnect-health.md#requirements)に記した要件が満たされていることを必ず確認したうえでエージェントをインストールしてください。エージェントは[ここ](http://go.microsoft.com/fwlink/?LinkID=518973)からダウンロードできます。


## エージェントのインストール
エージェントのインストールを開始するには、ダウンロードした .exe ファイルをダブルクリックします。最初の画面で [インストール] をクリックします。

![Verify Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install1.png)

インストールが完了したら、[すぐに構成する] をクリックします。

![Verify Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install2.png)

コマンド プロンプトが起動され、続けて PowerShell の Register-AzureADConnectHealthADFSAgent が実行されます。Azure へのサインインを要求されます。要求に従ってサインインしてください。

![Verify Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install3.png)


サインイン後も、PowerShell は続行されます。完了したら PowerShell を閉じます。これで構成は完了です。

この時点でサービスが自動的に開始され、エージェントによる監視とデータの収集が実行されます。前のセクションで挙げた前提条件が満たされていない場合、PowerShell ウィンドウに警告が表示されます。[こちら](active-directory-aadconnect-health.md#requirements)に記した要件が満たされていることを必ず確認したうえでエージェントをインストールしてください。以下のスクリーンショットは、これらのエラーの例です。

![Verify Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install4.png)

エージェントがインストール済みであることを確認するには、[サービス] を開いて次のサービスを探します。エージェントの構成が完了していれば、これらのサービスが実行されています。構成が完了するまでは開始されません。

- Azure AD Connect Health AD FS Diagnostics Service
- Azure AD Connect Health AD FS Insights Service
- Azure AD Connect Health AD FS Monitoring Service
 
![Verify Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install5.png)


## Windows Server 2008 R2 サーバーへのエージェントのインストール

Windows Server 2008 R2 サーバーの場合、次の手順に従います。

1. Service Pack 1 以降がサーバーで実行されていることを確認します。
1. エージェントをインストールするために、[IE セキュリティ強化の構成] をオフにします。
1. AD Health エージェントをインストールする前に、それぞれのサーバーに Windows PowerShell 4.0 をインストールします。Windows PowerShell 4.0 をインストールするには:
 - 次のリンクを使用してオフライン インストーラーをダウンロードし、[Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=40779) をインストールします。
 - ([Windows の機能] から) PowerShell ISE をインストールします。
 - [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855) をインストールします。
 - Internet Explorer Version 10 以降をサーバーにインストールします。ヘルス サービスが、ユーザーの Azure Admin 資格情報を使用してユーザーを認証するために必須となります。
1. Windows Server 2008 R2 への Windows PowerShell 4.0 のインストールについて、さらに詳しい情報については、[こちら](http://social.technet.microsoft.com/wiki/contents/articles/20623.step-by-step-upgrading-the-powershell-version-4-on-2008-r2.aspx)の wiki 記事を参照してください。

## 関連リンク

* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Azure AD Connect Health の操作](active-directory-aadconnect-health-operations.md)
* [AD FS での Azure AD Connect Health の使用](active-directory-aadconnect-health-adfs.md)
* [Azure AD Connect Health の FAQ](active-directory-aadconnect-health-faq.md)

<!---HONumber=August15_HO9-->