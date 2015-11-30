<properties 
	pageTitle="概要: Azure AD でのパスワード管理 | Microsoft Azure" 
	description="自分のパスワードのリセットを可能にし、パスワード リセットの前提条件を学習するほか、パスワード ライトバックを有効化してオンプレミスの Active Directory パスワードを管理します。" 
	services="active-directory" 
	documentationCenter="" 
	authors="asteen" 
	manager="kbrint" 
	editor="billmath"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/16/2015" 
	ms.author="asteen"/>

# パスワード管理の概要
ユーザーは、簡単な手順を実行するだけで、自分のクラウドの Azure Active Directory パスワードまたはオンプレミス Active Directory パスワードを管理できるようになります。いくつかの簡単な前提条件を満たせば、すぐに組織全体のパスワードの変更とリセットが実行できるようになります。この記事では、次の概念を説明します。

* [**クラウドの Azure Active Directory パスワードのユーザーによるリセットを有効にする方法**](#enable-users-to-reset-their-azure-ad-passwords)
 - [セルフサービスのパスワード リセットの前提条件](#prerequisites)
 - [手順 1: パスワードのリセット ポリシーを構成する](#step-1-configure-password-reset-policy)
 - [手順 2: テスト ユーザー用の連絡先データを追加する](#step-2-add-contact-data-for-your-test-user)
 - [手順 3: ユーザーとしてパスワードをリセットする](#step-3-reset-your-azure-ad-password-as-a-user)
* [**オンプレミス Active Directory パスワードのユーザーによるリセットまたは変更を有効にする方法**](#enable-users-to-reset-or-change-their-ad-passwords)
 - [パスワード ライトバックの前提条件](#writeback-prerequisites)
 - [手順 1: Azure AD Connect の最新バージョンをダウンロードする](#step-1-download-the-latest-version-of-azure-ad-connect)
 - [手順 2: UI または Powershell を使用して Azure AD Connect でパスワード ライトバックを有効にしてから確認する](#step-2-enable-password-writeback-in-azure-ad-connect)
 - [手順 3: ファイアウォールを構成する](#step-3-configure-your-firewall)
 - [手順 4: 適切な権限を設定する](#step-4-set-up-the-appropriate-active-directory-permissions)
 - [手順 5: ユーザーとして AD パスワードをリセットしてから確認する](#step-5-reset-your-ad-password-as-a-user)

## ユーザーによる Azure AD パスワードのリセットを有効にする
このセクションでは、AAD クラウド ディレクトリに対してセルフサービスのパスワード リセットを有効化し、セルフサービスのパスワード リセットが実行できるようにユーザーを登録し、最後にユーザーとしてセルフサービスのパスワード リセットのテストを実行する方法について説明します。

- [セルフサービスのパスワード リセットの前提条件](#prerequisites)
- [手順 1: パスワードのリセット ポリシーを構成する](#step-1-configure-password-reset-policy)
- [手順 2: テスト ユーザー用の連絡先データを追加する](#step-2-add-contact-data-for-your-test-user)
- [手順 3: ユーザーとしてパスワードをリセットする](#step-3-reset-your-azure-ad-password-as-a-user)


###  前提条件
セルフサービスのパスワード リセットを有効にして使用するには、次の前提条件を満たす必要があります。

- AAD テナントを作成する。詳細については、[Azure AD の使用](https://azure.microsoft.com/trial/get-started-active-directory/)を参照してください。
- Azure サブスクリプションを取得する。詳細については、[What is an Azure AD tenant? (Azure AD テナントとは)](active-directory-administer.md#what-is-an-azure-ad-tenant) を参照してください。
- AAD テナントを Azure サブスクリプションに関連付ける。詳細については、[Azure サブスクリプションを Azure AD に関連付ける方法](https://msdn.microsoft.com/library/azure/dn629581.aspx)を参照してください。
- Azure AD Premium または Basic にアップグレードする。詳細については、[Azure Active Directory のエディション](http://azure.microsoft.com/pricing/details/active-directory/)を参照してください。

  >[AZURE.NOTE]セルフサービスのパスワード リセットを有効にするには、Azure AD Premium または Azure AD Basic にアップグレードする必要があります。詳細については、「Azure Active Directory のエディション」を参照してください。この情報には、Azure AD Premium または Basic にサインアップする方法、ライセンス プランをアクティブ化して Azure AD アクセスをアクティブ化する方法、および管理者とユーザーのアカウントにアクセス権を割り当てる方法の詳細が含まれます。
  
- AAD ディレクトリで、管理者アカウントとユーザー アカウントを少なくとも 1 つずつ作成する。
- AAD Premium または Basic のライセンスを、作成済みの管理者およびユーザーのアカウントに割り当てる。

### 手順 1: パスワードのリセット ポリシーを構成する
ユーザー パスワードのリセット ポリシーを構成するには、以下の手順を実行します。
 
1.	任意のブラウザーを開いて、[Microsoft Azure 管理ポータル](https://manage.windowsazure.com)に移動します。
2.	[Microsoft Azure 管理ポータル](https://manage.windowsazure.com)の左側のナビゲーション バーで **[Active Directory] 拡張機能**を選択します。

    ![][001]

3. [**ディレクトリ**] タブで、Wingtip Toys など、ユーザー パスワードのリセット ポリシーを構成するディレクトリをクリックします。

    ![][002]

4.	[**構成**] タブをクリックします。

    ![][003]

5.	[**構成**] タブで、下にスクロールして [**ユーザー パスワードのリセット ポリシー**] セクションを表示します。ここでは、特定のディレクトリについて、ユーザー パスワードのリセット ポリシーのあらゆる側面を構成できます。

    >[AZURE.NOTE]この**ポリシーが適用されるのは組織内のエンド ユーザーのみで、管理者には適用されません**。セキュリティ上の理由から、管理者のパスワード リセット ポリシーの管理はマイクロソフトが行います。このセクションが表示されない場合は、Azure Active Directory Premium または Basic にサインアップ済みであることと、この機能の構成を行う管理者アカウントに**ライセンスを割り当て済み**であることを確認してください。

    ![][004]

6.	ユーザー パスワードのリセット ポリシーを構成するには、[**パスワードのリセットが有効になっているユーザー**] トグルを [**はい**] に設定します。この操作を行うと、組織のディレクトリでのこの機能の動作を構成するためのさまざまなコントロールが追加表示されます。組織のニーズに合わせて自由にパスワード リセットをカスタマイズしてください。パスワード リセット ポリシーの各コントロールの機能の詳細については、[Customize: Azure AD Password Management (カスタマイズ: Azure AD でのパスワード管理)](active-directory-passwords-customize) を参照してください。

    ![][005]

7.	テナントのユーザー パスワード リセット ポリシーの構成が完了したら、画面の一番下にある [**保存**] ボタンをクリックします。

  >[AZURE.NOTE]最も複雑な状況でこの機能がどのように動作するかを確認できるよう、ユーザー パスワードのリセット ポリシーのチャレンジは 2 つにすることをお勧めします。

  ![][006]

### 手順 2: テスト ユーザー用の連絡先データを追加する
組織内のユーザーがパスワードのリセットに使用するデータを指定する方法はいくつかあります。

-	[Microsoft Azure 管理ポータル](https://manage.windowsazure.com)または[ Office 365 管理ポータル](https://portal.microsoftonline.com)でユーザーを編集する
-	AAD Connect を使用して、ユーザー プロパティをオンプレミスの Active Directory ドメインから Azure AD に同期する
-	Windows PowerShell を使用してユーザー プロパティを編集する
-	ユーザーに登録ポータル ([http://aka.ms/ssprsetup](http://aka.ms/ssprsetup)) に移動するよう促して、自分のデータを登録してもらう
-	SSPR の構成オプション [**ユーザーが初めてアクセス パネルにサインインするときに登録を要求**] を [**はい**] に設定して、ユーザーがアクセス パネル ([http://myapps.microsoft.com](http://myapps.microsoft.com)) にサインインしたときにパスワード リセットの登録を求める

パスワード リセットで使用されるデータ、およびこのデータの形式の要件についての詳細は、[What data is used by password reset? (パスワードのリセットで使用されるデータとは)](active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset) を参照してください。

#### ユーザー登録ポータルからユーザーの連絡先データを追加するには
1.	パスワード リセットの登録ポータルを使用するには、組織内のユーザーにこのページへのリンク ([http://aka.ms/ssprsetup](http://aka.ms/ssprsetup)) を連絡するか、ユーザーに対して自動登録を求めるオプションをオンにする必要があります。ユーザーがこのリンクをクリックすると、組織のアカウントを使ってサインインするよう求められます。サインインが終わると、次のページが表示されます。

    ![][007]

2.	ここでは、携帯電話、代替電子メール アドレス、またはセキュリティの質問の指定や確認ができます。携帯電話の番号の確認画面は、以下のようになります。

    ![][008]

3.	ユーザーがこの情報を指定すると、ページが更新され、情報が有効である旨が表示されます (以下の画面では、情報を非表示にしています)。ユーザーが [**完了**] ボタンまたは [**キャンセル**] ボタンをクリックすると、アクセス パネルが表示されます。

    ![][009]

4.	ユーザーがここに挙げた情報を 2 つとも確認すると、入力されたデータでプロファイルが更新されます。この例では、**会社電話番号**が手動で指定されたため、この番号をパスワードをリセットする際の連絡方法として利用できます。

    ![][010]

### 手順 3: ユーザーとして Azure AD パスワードをリセットする
これでユーザーのリセット ポリシーが構成され、ユーザーの連絡先の詳細が指定できたので、このユーザーは、セルフサービスのパスワード リセットを実行できます。

#### セルフサービスのパスワード リセットを実行するには
1.	[**portal.microsoftonline.com**](http://portal.microsoftonline.com) などのサイトにアクセスすると、以下のようなログイン画面が表示されます。[**アカウントにアクセスできません**] リンクをクリックして、パスワード リセットの UI をテストします。

    ![][011]

2.	[**アカウントにアクセスできません**] をクリックすると新しいページが表示され、パスワードをリセットする**ユーザー ID** の入力を求められます。テスト **ユーザー ID** と captcha を入力し、[**次へ**] をクリックします。

    ![][012]

3.	この場合、ユーザーは**会社電話**、**携帯電話**、および**代替電子メール**を指定していたため、最初のチャレンジの選択肢にこれら 3 つがすべて表示されています。

    ![][013]

4.	今回は、最初に**会社電話**に**電話する**ことを選択します。電話ベースの方法を選択した場合、パスワードをリセットするには**電話番号の確認**が求められます。これには、悪意のある個人によって組織内のユーザーの電話番号が漏洩されることを防ぐ目的があります。

    ![][014]

5.	ユーザーが電話番号を確認した後は、[通話] ウォールをクリックするとスピナーが表示され、電話が鳴ります。ユーザーが電話を取ると、アカウントの確認のため **「#」 を押すよう**指示するメッセージが再生されます。# キーを押すと、ユーザーが最初のチャレンジをクリアしたことが自動的に確認され、2 番目の確認手順の UI に進みます。

    ![][015]

6.	最初のチャレンジをクリアすると、UI が自動的に更新され、クリアしたチャレンジが選択肢の一覧から除外されます。今回は、**会社電話**を最初に使用しているため、2 番目の確認手順に利用できるチャレンジの選択肢には、**携帯電話**と**代替電子メール**のみが残っています。[**連絡用電子メール アドレスにメールを送信**] オプションをクリックします。次に [電子メール] をクリックすると、登録済みの連絡用電子メール アドレスにメールが送信されます。

    ![][016]

7.	ユーザーに送信される電子メールのサンプルを次に示します。テナントのブランド化に注目してください。

    ![][017]

8.	電子メールが届くとページが更新され、電子メールに記載されている確認コードを、以下に示す入力ボックスに入力できるようになります。コードが正しく入力されると、[次へ] ボタンが点灯し、2 番目の確認手順が完了となります。

    ![][018]

9.	組織のポリシーの要件をクリアすると、新しいパスワードを作成できるようになります。ここで作成するパスワードは、AAD の 「強力な」 パスワードに関する要件 ([Azure AD でのパスワード ポリシー](https://msdn.microsoft.com/library/azure/jj943764.aspx)を参照) を満たすかどうかで検証されます。入力されたパスワードがこのポリシーに合致しているかどうかを示す、パスワードの強度検証コントロールが表示されます。

    ![][019]

10.	組織のポリシーに合致するパスワードを指定すると、パスワードがリセットされ、直ちに新しいパスワードでログインできるようになります。

    ![][020]


## ユーザーによる Azure AD パスワードのリセットまたは変更を有効にする

このセクションでは、オンプレミスの Active Directory パスワードにライトバックするためのパスワード リセットの構成方法を説明します。

- [パスワード ライトバックの前提条件](#writeback-prerequisites)
- [手順 1: Azure AD Connect の最新バージョンをダウンロードする](#step-1-download-the-latest-version-of-azure-ad-connect)
- [手順 2: UI または Powershell を使用して Azure AD Connect でパスワード ライトバックを有効にしてから確認する](#step-2-enable-password-writeback-in-azure-ad-connect)
- [手順 3: ファイアウォールを構成する](#step-3-configure-your-firewall)
- [手順 4: 適切な権限を設定する](#step-4-set-up-the-appropriate-active-directory-permissions)
- [手順 5: ユーザーとして AD パスワードをリセットしてから確認する](#step-5-reset-your-ad-password-as-a-user)


### ライトバックの前提条件
ライトバックを有効にして使用する前に、次の前提条件を満たす必要があります。

- Azure AD テナントで Azure AD Premium が有効になっている。詳細については、[Azure Active Directory のエディション](active-directory-editions.md)を参照してください。
- テナントでパスワード リセットが構成され、有効になっている。詳細については、[ユーザーによる Azure AD パスワードのリセットを有効にする](#enable-users-to-reset-their-azure-ad-passwords)を参照してください。
- 管理者アカウントと、この機能をテストするために使用できる Azure AD Premium ライセンスが付与されたテスト ユーザー アカウントが少なくとも 1 つずつある。詳細については、[Azure Active Directory のエディション](active-directory-editions.md)を参照してください。

  >[AZURE.NOTE]パスワード ライトバックの有効化に使用する管理者アカウントは、クラウドの管理者アカウント (Azure AD で作成された) であり、フェデレーション アカウント (オンプレミスの AD で作成され、Azure AD に同期された) ではありません。
  
- Windows Server 2008、Windows Server 2008 R2、Windows Server 2012、または Windows Server 2012 R2 を最新のサービス パックをインストールして実行している、1 つまたは複数のマルチフォレスト AD オンプレミスのデプロイを使用している。

  >[AZURE.NOTE]古いバージョンの Windows Server 2008 または 2008 R2 を実行している場合でもこの機能を使用できますが、クラウドでローカル AD パスワード ポリシーを適用するには、[KB 2386717 をダウンロードおよびインストール](https://support.microsoft.com/kb/2386717)する必要があります。
  
- Azure AD Connect ツールをインストールし、クラウドに同期するための AD 環境が準備されている。詳細については、[クラウド内のオンプレミスの ID インフラストラクチャの使用](active-directory-aadconnect.md)を参照してください。

  >[AZURE.NOTE]パスワード ライトバックをテストする前に、Azure AD Connect で AD と Azure AD の両方から、フル インポートと完全同期を完了していることを確認してください。

- Azure AD Sync または Azure AD Connect を使用している場合は、**TCP 送信ポート 443** (**場合によっては TCP 9350-9354**) を開く必要があります。詳細については、「[手順 3: ファイアウォールを構成する](#step-3-configure-your-firewall)」を参照してください。このシナリオでの DirSync の使用はサポートされなくなりました。DirSync をまだ使用している場合は、パスワード ライトバックをデプロイする前に Azure AD Connect の最新バージョンにアップグレードしてください。

  >[AZURE.NOTE]Azure AD Sync ツールまたは DirSync ツールを使用している場合、優れたエクスペリエンスと、リリースされる新しい機能を利用できるように、Azure AD Connect の最新バージョンにアップグレードすることを強くお勧めします。
  

### 手順 1: Azure AD Connect の最新バージョンをダウンロードする
パスワード ライトバックは、Azure AD Connect のリリース、またはバージョン番号が **1.0.0419.0911** 以降の Azure AD Sync ツールで使用できます。アカウントが自動的にロック解除されるパスワード ライトバックが使用できるのは、Azure AD Connect のリリース、またはバージョン番号が **1.0.0485.0222** 以降の Azure AD Sync ツールです。以前のバージョンを実行している場合は、続行する前に、少なくともこのバージョンにアップグレードしてください。[ここをクリックすると、Azure AD Connect の最新バージョンをダウンロードできます。](active-directory-aadconnect.md#download-azure-ad-connect)

#### Azure AD Sync のバージョンを確認するには
1.	**%ProgramFiles%\\Azure Active Directory Sync** に移動します。
2.	**ConfigWizard.exe** 実行可能ファイルを参照します。
3.	この実行可能ファイルを右クリックして、 コンテキスト メニューから [**プロパティ**] オプションを選択します。
4.	[**詳細**] タブをクリックします。
5.	[**ファイル バージョン**] フィールドを参照します。

    ![][021]

このバージョン番号が **1.0.0419.0911** 以上の場合、または Azure AD Connect をインストールしている場合は、[手順 2: UI または Powershell を使用して Azure AD Connect でパスワード ライトバックを有効にしてから確認する](#step-2-enable-password-writeback-in-azure-ad-connect)にスキップできます。

 >[AZURE.NOTE]Azure AD Connect ツールを初めてインストールする場合は、いくつかのベスト プラクティスに従って、ディレクトリ同期の環境を準備することをお勧めします。Azure AD Connect ツールをインストールする前に、[Office 365 管理ポータル](https://portal.microsoftonline.com) または [Microsoft Azure 管理ポータル](https://manage.windowsazure.com) のいずれかでディレクトリ同期を有効にする必要があります。詳細については、[Managing Azure AD Connect (Azure AD Connect の管理)](active-directory-aadconnect-whats-next.md) を参照してください。


### 手順 2: Azure AD Connect でパスワード ライトバックを有効にする
Azure AD Connect ツールをダウンロードしたので、パスワード ライトバックを有効にする準備ができました。次の 2 つの方法のいずれかで有効にします。パスワード ライトバックは、Azure AD Connect のセットアップ ウィザードの [オプション機能] 画面で有効にできますし、Windows PowerShell を使用して有効にすることもできます。

#### 構成ウィザードでパスワード ライトバックを有効にするには
1.	**ディレクトリ同期コンピューター**上で、**Azure AD Connect** の構成ウィザードを開きます。
2.	[**オプション機能**] 構成画面が表示されるまで、クリックして進みます。
3.	[**パスワード ライトバック**] オプションをオンにします。

    ![][022]

4.	ウィザードを完了します。最後のページには、パスワード ライトバックの構成の変更を含む、変更の概要が表示されます。

> [AZURE.NOTE]パスワード ライトバックはいつでも無効にできます。無効にするには、このウィザードを再実行してこの機能を選択解除するか、[Microsoft Azure 管理ポータル](https://manage.windowsazure.com)で、ディレクトリの [**構成**] タブの [**ユーザー パスワードのリセット ポリシー**] セクションにある [**オンプレミスのディレクトリにパスワードをライトバック**] を [**いいえ**] に設定します。パスワード リセットのエクスペリエンスのカスタマイズに関する詳細については、[Customize: Azure AD Password Management (カスタマイズ: Azure AD でのパスワード管理)](active-directory-passwords-customize.md) を参照してください。

#### Windows PowerShell を使用して、パスワード ライトバックを有効にするには
1.	**ディレクトリ同期コンピューター**上で、新しい [**管理者特権の Windows PowerShell**] ウィンドウを開きます。
2.	モジュールがまだ読み込まれていない場合は、`Import-Module ADSync` コマンドを入力して、Azure AD Connect コマンドレットを現在のセッションに読み込みます。
3.	`Get-ADSyncConnector` コマンドレットを実行してシステム内の AAD コネクタの一覧を取得し、その結果を`$aadConnectorName` に保存します。
4.	次のコマンドレットを実行して、現在のコネクタについてライトバックの現在の状態を取得します。`Get-ADSyncAADPasswordResetConfiguration –Connector $aadConnectorName`
5.	次のコマンドレットを実行して、パスワード ライトバックを有効にします。 `Set-ADSyncAADPasswordResetConfiguration –Connector $aadConnectorName –Enable $true`

> [AZURE.NOTE]資格情報を求められた場合、AzureADCredential に指定した管理者アカウントが、**クラウドの管理者アカウント (Azure AD で作成された)** であり、フェデレーション アカウント (オンプレミスの AD で作成され、Azure AD に同期された) ではないことを確認します。[AZURE.NOTE]パスワード ライトバックを無効にするには、PowerShell を使用して前述の手順を繰り返すか (ただし `$false` を渡す）、[Microsoft Azure 管理ポータル](https://manage.windowsazure.com)で、ディレクトリの [**構成**] タブの [**ユーザー パスワードのリセット ポリシー**] セクションにある [**オンプレミスのディレクトリにパスワードをライトバック**] を [**いいえ**] に設定します。

#### 構成が正常に完了したことを確認する
構成が正常に完了すると、[Windows PowerShell] ウィンドウに「パスワード リセット ライトバックが有効になりました。」というメッセージが表示されます。

サービスが正しくインストールされたことを確認するには、イベント ビューアーを開き、アプリケーション イベント ログに移動して、ソース ”**PasswordResetService**” でイベント ”**31005 - OnboardingEventSuccess**” が表示されているかを確認します。

  ![][023]

### 手順 3: ファイアウォールを構成する
Azure AD Connect ツールでパスワード ライトバックを有効にした後、サービスからクラウドに接続できることを確認する必要があります。

1.	インストールの完了後、ご使用の環境で不明な送信接続をブロックしている場合は、次の規則をファイアウォールに追加する必要があります。これらの変更を行った後に、AAD Connect のコンピューターを必ず再起動してください。
   - TCP ポート 443 経由の送信接続を許可する
   - https://ssprsbprodncu-sb.accesscontrol.windows.net/への送信接続を許可する 
   - プロキシを使用している場合や、一般的な接続問題が発生した場合は、TCP ポート 9350-9354 の送信接続を許可する

### 手順 4: Active Directory の適切な権限を設定する
パスワードをリセットするユーザーを含むすべてのフォレストについて、構成ウィザードでそのフォレストに対して (初期構成中に) 指定されたアカウントが X である場合、X には、そのフォレスト内の各ドメインで、`lockoutTime` の [**パスワードのリセット**] 、[**パスワードの変更**] 、 [**書き込みアクセス許可**] や、`pwdLastSet` の [**書き込みアクセス許可**] など、ルート オブジェクトの拡張権限を付与する必要があります。この権限は、すべてのユーザー オブジェクトにより継承されるものとしてマークされます。

上記のどのアカウントが参照されるか明らかでない場合は、Azure Active Directory Connect の構成 UI を開き、**[ソリューションの確認]** オプションをクリックします。以下のスクリーンショットで、アクセス許可を追加する必要があるアカウントには赤色の下線が表示されています。

**<font color="red">システムの各フォレストでドメインごとに、このアクセス許可を必ず設定します。そうしないと、パスワード ライトバックは正しく機能しません。</font>**

  ![][032]

  これらの権限を設定すると、パスワード管理が、フォレストに含まれるユーザー アカウントからではなく、各フォレストのMA サービス アカウントから可能になります。これらの権限を割り当てないと、ライトバックが正常に構成されているように思われる場合でも、クラウドからオンプレミスのパスワードを管理しようとするとエラーが発生します。ここでは、[**Active Directory ユーザーとコンピューター**] 管理スナップインを使用した権限の設定方法の手順を詳しく説明します。

>[AZURE.NOTE]ディレクトリ内のすべてのオブジェクトにこれらの権限をレプリケートするには、最大 1 時間かかることがあります。

#### ライトバックを行うために適切な権限を設定するには

1.	適切なドメインの管理権限を持つアカウントで [**Active Directory ユーザーとコンピューター**] を開きます。
2.	[**表示メニュー**] オプションで、[**高度な機能**] がオンになっていることを確認します。
3.	左側のパネルで、ドメインのルートを表すオブジェクトを右クリックします。
4.	[**セキュリティ**] タブをクリックします。
5.	[**詳細設定**] をクリックします。

    ![][024]

6.	[**アクセス権限**] タブで [**追加**] をクリックします。

    ![][025]

7.	アクセス許可するアカウント (フォレストの同期をセットアップ中に指定したものと同じアカウント) を選択します。
8.	上部のドロップダウンで、[**下位ユーザー オブジェクト**] を選択します。
9.	表示される [**アクセス許可エントリ**] ダイアログ ボックスで、`lockoutTime` の [**パスワードのリセット**] 、 [**パスワードの変更**] 、および [**書き込みのアクセス許可**] のチェック ボックスをオンにし、`pwdLastSet` の [**書き込みのアクセス許可**] のチェック ボックスをオンにします。

    ![][026] ![][027] ![][028]

10.	開いているすべてのダイアログ ボックスで** [適用] または [OK] **をクリックします。

### 手順 5: ユーザーとして AD パスワードをリセットする
これでパスワード ライトバックが有効になったので、その動作をテストできます。テストするには、アカウントがクラウド テナントと同期されているユーザーのパスワードをリセットします。
 
#### パスワード ライトバックの正常な動作を確認するには
1.	[http://passwordreset.microsoftonline.com](https://passwordreset.microsoftonline.com) に移動するか、組織の ID ログイン画面に移動し、[**アカウントにアクセスできない場合**] リンクをクリックします。

    ![][029]

2.	新しいページが表示され、パスワードをリセットするユーザー ID を求められます。テスト ユーザー ID を入力し、パスワード リセット フローを続行します。
3.	パスワードをリセットすると、次のような画面が表示されます。これは、オンプレミスのディレクトリまたはクラウド ディレクトリ、あるいは両方のディレクトリでパスワードが正常にリセットされたことを意味します。

    ![][030]

4.	操作が正常に完了したか、またはエラーが診断されたかを確認するには、**ディレクトリ同期コンピューター**に進んで [**イベント ビューアー**] を開き、[**アプリケーション イベント ログ**] に移動して、ソース ”**PasswordResetService**” でテスト ユーザーのイベント ”**31002 - PasswordResetSuccess**” が表示されているかを確認します。

    ![][031]


<br/> <br/> <br/>

## パスワードのリセットに関するドキュメントへのリンク
Azure AD のパスワードのリセットに関するすべてのドキュメント ページへのリンクを以下に示します。

* [**自分のパスワードのリセット**](active-directory-passwords-update-your-own-password) - システムのユーザーとして自分のパスワードをリセットまたは変更する方法について説明します。
* [**しくみ**](active-directory-passwords-how-it-works.md) - サービスの 6 つの異なるコンポーネントとそれぞれの機能について説明します。
* [**カスタマイズ**](active-directory-passwords-customize.md) - 組織のニーズに合わせてサービスの外観と動作をカスタマイズする方法について説明します。
* [**ベスト プラクティス**](active-directory-passwords-best-practices.md) - 組織内でのパスワードの迅速なデプロイと効果的な管理方法について説明します。
* [**洞察を得る**](active-directory-passwords-get-insights.md) - 統合レポート機能について説明します。
* [**FAQ**](active-directory-passwords-faq.md) -よく寄せられる質問の回答を得ます。
* [**トラブルシューティング**](active-directory-passwords-troubleshoot.md) - サービスに関する問題を迅速にトラブルシューティングする方法について説明します。
* [**詳細情報**](active-directory-passwords-learn-more.md) - サービスの機能の技術的な詳細を掘り下げます。



[001]: ./media/active-directory-passwords-getting-started/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-getting-started/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-getting-started/003.jpg "Image_003.jpg"
[004]: ./media/active-directory-passwords-getting-started/004.jpg "Image_004.jpg"
[005]: ./media/active-directory-passwords-getting-started/005.jpg "Image_005.jpg"
[006]: ./media/active-directory-passwords-getting-started/006.jpg "Image_006.jpg"
[007]: ./media/active-directory-passwords-getting-started/007.jpg "Image_007.jpg"
[008]: ./media/active-directory-passwords-getting-started/008.jpg "Image_008.jpg"
[009]: ./media/active-directory-passwords-getting-started/009.jpg "Image_009.jpg"
[010]: ./media/active-directory-passwords-getting-started/010.jpg "Image_010.jpg"
[011]: ./media/active-directory-passwords-getting-started/011.jpg "Image_011.jpg"
[012]: ./media/active-directory-passwords-getting-started/012.jpg "Image_012.jpg"
[013]: ./media/active-directory-passwords-getting-started/013.jpg "Image_013.jpg"
[014]: ./media/active-directory-passwords-getting-started/014.jpg "Image_014.jpg"
[015]: ./media/active-directory-passwords-getting-started/015.jpg "Image_015.jpg"
[016]: ./media/active-directory-passwords-getting-started/016.jpg "Image_016.jpg"
[017]: ./media/active-directory-passwords-getting-started/017.jpg "Image_017.jpg"
[018]: ./media/active-directory-passwords-getting-started/018.jpg "Image_018.jpg"
[019]: ./media/active-directory-passwords-getting-started/019.jpg "Image_019.jpg"
[020]: ./media/active-directory-passwords-getting-started/020.jpg "Image_020.jpg"
[021]: ./media/active-directory-passwords-getting-started/021.jpg "Image_021.jpg"
[022]: ./media/active-directory-passwords-getting-started/022.jpg "Image_022.jpg"
[023]: ./media/active-directory-passwords-getting-started/023.jpg "Image_023.jpg"
[024]: ./media/active-directory-passwords-getting-started/024.jpg "Image_024.jpg"
[025]: ./media/active-directory-passwords-getting-started/025.jpg "Image_025.jpg"
[026]: ./media/active-directory-passwords-getting-started/026.jpg "Image_026.jpg"
[027]: ./media/active-directory-passwords-getting-started/027.jpg "Image_027.jpg"
[028]: ./media/active-directory-passwords-getting-started/028.jpg "Image_028.jpg"
[029]: ./media/active-directory-passwords-getting-started/029.jpg "Image_029.jpg"
[030]: ./media/active-directory-passwords-getting-started/030.jpg "Image_030.jpg"
[031]: ./media/active-directory-passwords-getting-started/031.jpg "Image_031.jpg"
[032]: ./media/active-directory-passwords-getting-started/032.jpg "Image_032.jpg"

<!---HONumber=Nov15_HO4-->