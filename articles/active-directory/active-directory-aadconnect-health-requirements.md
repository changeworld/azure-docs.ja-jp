<properties 
	pageTitle="Azure AD Connect Health の要件。" 
	description="このページでは、Azure AD Connect Health の要件とエージェントのインストールについて説明しています。" 
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

# Azure Active Directory Connect Health の要件
このドキュメントでは、Azure AD Connect Health を使用するための要件を列挙しています。



## Azure AD Premium ライセンス

Azure AD Connect Health は Azure AD Premium の機能です。使用するためには Azure AD Premium のライセンスが必要となります。ライセンスを入手するには、「Azure Active Directory Premium の概要」を参照してください。
 

## Azure AD テナントの全体管理者であること

Azure AD Connect Health から得られる情報には、全体管理者が既定でアクセスできます。オンプレミスの Active Directory とのフェデレーション先となる Azure AD テナントの全体管理者以外は、Azure AD Connect Health のサービス インスタンスを作成できません。自分が全体管理者であることを確認してください。詳細については、[Azure AD ディレクトリの管理](https://msdn.microsoft.com/library/azure/hh967611.aspx)に関するページを参照してください。
 

## Azure AD Connect Health エージェントが個々のサーバーにインストールされていること

Azure AD Connect Health ポータルに表示されるデータを Azure AD Connect Health から得るためには、対象となるサーバーにエージェントがインストールされている必要があります。つまり、AD FS オンプレミス インフラストラクチャに関するデータを入手するためには、AD FS サーバーにエージェントがインストールされている必要があります。これには AD FS プロキシ サーバーや Web アプリケーション プロキシ サーバーが含まれます。Azure AD Connect Health エージェントのインストールについては、Azure AD Connect Health エージェントのインストール手順に関するページを参照してください。


## Azure AD Connect Health エージェントの要件

以降のセクションでは、Azure AD Connect Health エージェント固有の要件について説明します。
 

### Azure AD Connect Health エージェントのダウンロード

Azure AD Connect Health の使用を開始するには、こちら ([Azure AD Connect Health エージェントのダウンロード](http://go.microsoft.com/fwlink/?LinkID=518973)) から最新のバージョンのエージェントをダウンロードします。 エージェントをインストールする前に、Marketplace からサービスを追加したことを確認します。

 
### Azure サービスのエンドポイントに対する送信接続
エージェントをインストールしたり実行したりするためには、以下に示した、Azure AD Connect Health サービスのエンド ポイントへの接続が必要となります。送信接続をブロックしている場合は、以下の項目を許可リストに追加してください。

- *.servicebus.windows.net - ポート: 5671 - https://*.adhybridhealth.azure.com/
- https://*.table.core.windows.net/
- https://policykeyservice.dc.ad.msft.net/
- https://login.windows.net
- https://login.microsoftonline.com
- https://secure.aadcdn.microsoftonline-p.com 

## IE セキュリティ強化が有効になっている場合は以下の Web サイトが許可されていること
エージェントのインストール対象となるサーバーで IE セキュリティ強化が有効になっている場合、次の Web サイトを許可する必要があります。

- https://login.microsoftonline.com 
- https://secure.aadcdn.microsoftonline-p.com
- https://login.windows.net
- Azure Active Directory によって信頼されている組織のフェデレーション サーバー (例: https://sts.contoso.com) 


### AD FS で利用状況分析機能を使用する場合、AD FS 監査が有効になっていること

利用状況分析機能でデータを収集し、分析するには、AD FS 監査ログ内の情報に Azure AD Connect Health エージェントからアクセスできることが必要です。既定では、これらのログが有効になっていません。この要件が該当するのは AD FS フェデレーション サーバーのみです。AD FS プロキシ サーバーや Web アプリケーション プロキシ サーバーで監査を有効にする必要はありません。AD FS の監査を有効にしたり、AD FS の監査ログを特定したりするには、以下の手順に従ってください。

#### AD FS 2.0 の監査を有効にするには

1. **[スタート]** ボタンをクリックし、**[プログラム]**、**[管理ツール]** の順にポイントして、**[ローカル セキュリティ ポリシー]** をクリックします。
1. **"セキュリティの設定\ローカル ポリシー\ユーザー権利の管理"** フォルダーに移動し、[セキュリティ監査の生成] をダブルクリックします。
1. **[ローカル セキュリティの設定]** タブで、AD FS 2.0 サービス アカウントが表示されていることを確認します。表示されない場合は、**[ユーザーまたはグループの追加]** をクリックしてこのアカウントをリストに追加し、**[OK]** をクリックします。
1. 管理者特権でコマンド プロンプトを開き、次のコマンドを実行して監査を有効にします。`auditpol.exe /set /subcategory:"Application Generated" /failure:enable /success:enable`
1. ローカル セキュリティ ポリシーを閉じて、管理スナップインを開きます。管理スナップインを開くには、**[スタート]** ボタンをクリックし、**[プログラム]**、**[管理ツール]** の順にポイントして、[AD FS 2.0 管理] をクリックします。
1. 操作ウィンドウで、[フェデレーション サービス プロパティの編集] をクリックします。
1. **[フェデレーション サービス プロパティ]** ダイアログ ボックスの **[イベント]** タブをクリックします。
1. **[成功の監査]** チェック ボックスと **[失敗の監査]** チェック ボックスをオンにします。
1. **[OK]** をクリックします。

#### Windows Server 2012 R2 で AD FS の監査を有効にするには

1. スタート画面の **[サーバー マネージャー]** またはデスクトップのタスク バーにある [サーバー マネージャー] を開いて **[ローカル セキュリティ ポリシー]** を開き、**[ツール]、[ローカル セキュリティ ポリシー]** の順にクリックします。
1. **"セキュリティの設定\ローカル ポリシー\ユーザー権利の割り当て"** フォルダーに移動し、**[セキュリティ監査の生成]** をダブルクリックします。
1. **[ローカル セキュリティの設定]** タブで、AD FS サービス アカウントが表示されていることを確認します。表示されない場合は、**[ユーザーまたはグループの追加]** をクリックしてこのアカウントをリストに追加し、**[OK]** をクリックします。
1. 管理者特権でコマンド プロンプトを開き、次のコマンドを実行して監査を有効にします。`auditpol.exe /set /subcategory:"Application Generated" /failure:enable /success:enable.`
1. **[ローカル セキュリティ ポリシー]** を閉じて、**AD FS 管理**スナップインを開きます (サーバー マネージャーの [ツール] をクリックし、[AD FS 管理] を選択します)。
1. 操作ウィンドウで、**[フェデレーション サービス プロパティの編集]** をクリックします。
1. [フェデレーション サービス プロパティ] ダイアログ ボックスの **[イベント]** タブをクリックします。
1. **[成功の監査] チェックボックスと [失敗の監査] チェックボックス**をオンにし、**[OK]** をクリックします。






#### AD FS の監査ログを特定するには


1. **イベント ビューアー**を開きます。</li>
1. [Windows ログ] に移動し、**[セキュリティ]** を選択します。
1. 右側の **[現在のログをフィルター]** をクリックします。
1. [イベント ソース] の **[AD FS の監査]** を選択します。

![AD FS audit logs](./media/active-directory-aadconnect-health-requirements/adfsaudit.png)

> [AZURE.WARNING]グループ ポリシーで AD FS の監査が無効にされている場合、Azure AD Connect Health エージェントが情報を収集できません。監査を無効にするグループ ポリシーが設定されていないことを確認してください。


### Windows Server 2008 R2 サーバーへのエージェントのインストール

Windows Server 2008 R2 サーバーの場合、次の手順に従います。

1. Service Pack 1 以降がサーバーで実行されていることを確認します。
1. エージェントをインストールするために、[IE セキュリティ強化の構成] をオフにします。
1. AD Health エージェントをインストールする前に、それぞれのサーバーに Windows PowerShell 4.0 をインストールします。Windows PowerShell 4.0 をインストールするには:
 - 次のリンクを使用してオフライン インストーラーをダウンロードし、[Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=40779) をインストールします。
 - ([Windows の機能] から) PowerShell ISE をインストールします。
 - [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855) をインストールします。
 - Internet Explorer Version 10 以降をサーバーにインストールします。ヘルス サービスが、ユーザーの Azure Admin 資格情報を使用してユーザーを認証するために必須となります。
1. Windows Server 2008 R2 への Windows PowerShell 4.0 のインストールについて、さらに詳しい情報については、[こちら](http://social.technet.microsoft.com/wiki/contents/articles/20623.step-by-step-upgrading-the-powershell-version-4-on-2008-r2.aspx)の wiki 記事を参照してください。

## Azure AD Connect Health エージェントのデプロイ
このセクションでは、Azure AD Connect Health エージェントをサーバーにインストールして構成する手順を紹介します。Azure AD Connect Health のインスタンスでデータを表示するためには、あらかじめ対象サーバーに Azure AD Connect Health エージェントをインストールしておく必要があります。冒頭に記した要件が満たされていることを必ず確認したうえでエージェントをインストールしてください。エージェントは、前掲のリンクからダウンロードしてください。その後、以下の手順に従います。


ダウンロードした .exe ファイルをダブルクリックします。最初の画面で [インストール] をクリックします。

![Verify Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install1.png)

インストールが完了したら、[すぐに構成する] をクリックします。

![Verify Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install2.png)

コマンド プロンプトが起動され、続けて PowerShell の Register-AzureADConnectHealthADFSAgent が実行されます。Azure へのサインインを要求されます。要求に従ってサインインしてください。

![Verify Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install2.png)

サインイン後も、PowerShell は続行されます。完了したら PowerShell を閉じます。これで構成は完了です。

![Verify Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install3.png)

この時点でサービスが自動的に開始され、エージェントによる監視とデータの収集が実行されます。前のセクションで挙げた前提条件が満たされていない場合、PowerShell ウィンドウに警告が表示されます。以下のスクリーンショットはその例です。

![Verify Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install4.png)

エージェントがインストール済みであることを確認するには、[サービス] を開いて次のサービスを探します。エージェントの構成が完了していれば、これらのサービスが実行されています。構成が完了するまでは開始されません。

- Azure AD Connect Health AD FS Diagnostics Service
- Azure AD Connect Health AD FS Insights Service
- Azure AD Connect Health AD FS Monitoring Service
 
![Verify Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install5.png)

<!---HONumber=July15_HO4-->