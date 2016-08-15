<properties
	pageTitle="設定とデータのローミングに関する FAQ | Microsoft Azure"
	description="設定とアプリ データの同期に関する質問とその答えを IT 管理者向けに紹介しています。"
	services="active-directory"
    keywords="Enterprise State Roaming の設定, Windows クラウド, Enterprise State Roaming に関してよく寄せられる質問"
	documentationCenter=""
	authors="femila"
	manager="swadhwa"
	editor="curtand"/>

<tags
	ms.service="active-directory"  
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/21/2016"
	ms.author="femila"/>

# 設定とデータのローミングに関する FAQ

このトピックでは、設定とアプリ データの同期に関する質問とその答えを IT 管理者向けに紹介しています。

## ローミングの対象データは
**Windows 設定**: Windows オペレーティング システムに組み込まれている PC 設定。主に、ユーザーの PC をカスタマイズする設定が該当し、大きく次のカテゴリに分けられます。

- **テーマ**: デスクトップ テーマ、タスク バーの設定など。
- **Internet Explorer の設定**: 最近開いたタブ、お気に入りなど。
- **Edge ブラウザーの設定**: お気に入り、リーディング リスト
- **パスワード**: インターネット パスワード、Wi-Fi プロファイルなど。
- **言語設定**: キーボード レイアウト、システム言語、日付と時刻など。
- **簡単操作**: ハイ コントラスト テーマ、ナレーター、拡大鏡など。
- **その他の Windows の設定**: コマンド プロンプトの設定、アプリケーション一覧など。

**アプリケーション データ**: ユニバーサル Windows アプリは、設定データを "Roaming" フォルダーに書き込むことができます。このフォルダーに書き込まれたデータはすべて自動的に同期されます。この機能を利用するようにアプリを設計するかどうかは、アプリ開発者ごとの判断となります。ローミングを使用するようにユニバーサル Windows アプリを開発する方法について詳しくは、[アプリ データ ストレージ API](https://msdn.microsoft.com/library/windows/apps/mt299098.aspx) と[アプリ データのローミングに関する Windows 8 アプリ開発者ブログ](http://blogs.msdn.com/b/windowsappdev/archive/2012/07/17/roaming-your-app-data.aspx)を参照してください。

## 設定の同期に使用されるアカウントは
Windows 8 と Windows 8.1 では常に、コンシューマーの Microsoft アカウントが設定の同期に使用されていました。企業ユーザーは、その Active Directory ドメイン アカウントに Microsoft アカウントを接続することによって設定の同期を利用することができました。Windows 10 では、この "Microsoft アカウントの接続" 機能が、プライマリ/セカンダリ アカウント フレームワークによって置き換えられています。

ここでいうプライマリ アカウントとは、Windows へのログインに使用するアカウントをいいます。Microsoft アカウントや Azure Active Directory (Azure AD) アカウント、オンプレミスの Active Directory アカウント、ローカル アカウントが該当します。Windows 10 のユーザーは、プライマリ アカウントに加え、1 つまたは複数のセカンダリ クラウド アカウントを各自のデバイスに追加することができます。一般に、セカンダリ アカウントには、Microsoft アカウントや Azure Active Directory アカウントのほか、Gmail や Facebook といったアカウントが使用されます。セカンダリ アカウントで、シングル サインオンや Windows ストアなど特殊なサービスにアクセスすることはできますが、設定の同期機能にアクセスすることはできません。

Windows 10 における設定の同期機能に使用できるのは、デバイスのプライマリ アカウントだけです (「[Windows 8 における Microsoft アカウントでの設定の同期を Windows 10 における Azure AD での設定の同期にアップグレードするとどうなりますか](active-directory-windows-enterprise-state-roaming-faqs.md#How-do-I-upgrade-from-Microsoft-account-settings-sync-in-Windows-8-to-Azure-AD-settings-sync-in Windows-10?)」を参照)。

デバイス上の異なるユーザー アカウントのデータがミックスされることはありません。設定の同期には次の 2 つの規則があります。
- Windows の設定は常にプライマリ アカウントでローミングされます。
- アプリのデータは、そのアプリを取得する際に使われたアカウントでタグ付けされます。同期されるのは、プライマリ アカウントでタグ付けされたアプリだけです。アプリの所有権タグは、Windows ストア経由でアプリをインストールするとき、またはモバイル デバイス管理 (MDM) 経由でアプリをサイド ローディングしたときに決定されます。

アプリの所有者を特定できない場合は、プライマリ アカウントでローミングされます。デバイスを Windows 8 または Windows 8.1 から Windows 10 にアップグレードした場合、すべてのアプリが、Microsoft アカウントで取得されたものとしてタグ付けされます。一般に、Windows 10 がリリースされる前は、ほとんどのアプリが Windows ストア経由で取得され、また Windows ストアが Azure AD アカウントに対応していなかったためです。オフライン ライセンスでインストールされたアプリは、そのデバイス上のプライマリ アカウントでタグ付けされます。

>[AZURE.NOTE]  
企業の所有の下で Azure AD に接続された Windows 10 デバイスの Microsoft アカウントは、今後ドメイン アカウントに接続できません。Microsoft アカウントをドメイン アカウントに接続してそのユーザーの全データを Microsoft アカウントに同期させる機能 ("Microsoft アカウントと Active Directory との接続" による Microsoft アカウントでのローミング機能) は、Active Directory と Azure AD とが接続された環境に参加している Windows 10 デバイスからは削除されます。

## Windows 8 における Microsoft アカウントでの設定の同期を Windows 10 における Azure AD での設定の同期にアップグレードするとどうなりますか
Active Directory に接続されている Microsoft アカウントで Active Directory ドメインに参加している Windows 8 または Windows 8.1 のユーザーが設定を同期する際には、その Microsoft アカウントが使用されます。Active Directory ドメインと Azure AD が接続されていない場合、Windows 10 へのアップグレード後も、ドメインに参加しているユーザーは引き続き Microsoft アカウントでユーザー設定を同期することになります。一方、オンプレミスの Active Directory ドメインが Azure AD と接続されている場合、ユーザーのデバイスは、接続されている Azure AD アカウントを使って設定を同期し始めます。Azure AD 管理者によって Enterprise State Roaming が有効にされていない場合、接続されている Azure AD アカウントのユーザーに関しては設定の同期が中止されます。Azure AD での設定の同期を有効にするとその直後から、Windows 10 のユーザーが Azure AD の ID でログインした場合に、Azure AD を使用して Windows の設定が同期され始めます。

個人データを会社のデバイスに保存していたユーザーは、Windows OS とアプリケーションのデータが今後は Azure AD と同期されることに注意してください。これには、次のような意味があります。

- 今後は Microsoft アカウントでの設定の同期と Azure AD での設定の同期とで別々のアカウントが使用されるため、個人用 Microsoft アカウントのユーザー設定が、職場や学校の Azure AD アカウントの設定から徐々に "乖離" していきます。
- 従来、Active Directory に接続された Microsoft アカウント経由で同期されていた個人データ (Wi-Fi のパスワード、Web の資格情報、Internet Explorer のお気に入りやタブなど) は今後、Azure AD 経由で同期されます。


## Microsoft アカウントと Azure AD の Enterprise State Roaming の相互運用性について教えてください
Nov 2015 以降のリリースの Windows 10 では、Enterprise State Roaming が一度に 1 つのアカウントでのみサポートされます。ユーザーが職場や学校の Azure AD アカウントで Windows にサインインした場合、すべてのデータが Azure AD 経由で同期されます。ユーザーが個人用 Microsoft アカウントで Windows にサインインした場合は、すべてのデータが Microsoft アカウント経由で同期されます。ユニバーサル アプリのライセンスがプライマリ アカウントによって所有されている場合は、アプリのデータが、そのデバイス上のプライマリ サインイン アカウントのみを使ってローミングされます。セカンダリ アカウントによって所有されているアプリのユニバーサル アプリ データは同期されません。

## 複数テナントの Azure AD アカウントが存在する場合、設定は同期されますか
異なる Azure AD テナントに属している複数の Azure AD アカウントが同じデバイスに存在する場合、その各 Azure AD テナントについて、Azure Rights Management Service (RMS) サービスと通信するようにデバイスのレジストリを更新する必要があります。

1. まず、各 Azure AD テナントの GUID が必要です。Azure クラシック ポータルを開いて Azure AD テナントを選択します。テナントの GUID は、ブラウザーのアドレス バーに表示される URL で確認できます。 `https://manage.windowsazure.com/YourAccount.onmicrosoft.com#Workspaces/ActiveDirectoryExtension/Directory/Tenant GUID/directoryQuickStart`
2. GUID を確認したら、次のレジストリ キーを追加する必要があります。**HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\Windows\\SettingSync\\WinMSIPC<テナント ID GUID>** <**テナント ID GUID**> キーから、**AllowedRMSServerUrl** という名前の新しい複数行文字列値 (REG-MULTI-SZ) を作成し、そのデータに、そのデバイスがアクセスする他の Azure テナントのライセンス配布ポイントの URL を指定します。
3. ライセンス配布ポイントの URL は、**Get-AadrmConfiguration** コマンドレットを実行して確認できます。**LicensingIntranetDistributionPointUrl** の値と **LicenseingExtranetDistributionPointUrl** の値とが異なる場合は、両方の値を指定します。同じ値である場合は、その値を一度だけ指定してください。


## 既存の Windows デスクトップ アプリケーションに使用される設定のローミングに関しては、どのような選択肢がありますか
ローミングが利用できるのは、ユニバーサル Windows アプリに限られます。既存の Windows デスクトップ アプリケーションでローミングを利用する方法としては、次の 2 つの選択肢があります。

- [Desktop Bridge](http://aka.ms/desktopbridge) を使用する。既存の Windows デスクトップ アプリを簡単にユニバーサル Windows プラットフォームに移行できます。そこから、ごくわずかな変更をコードに加えることで、Azure AD のアプリ データ ローミングを利用できるようになります。既存のデスクトップ アプリは、そのデータのローミングに必要なアプリ ID を Desktop Bridge から得ることができます。
- [User Experience Virtualization (UE-V)](https://technet.microsoft.com/library/dn458947.aspx) を使用する。Win32 アプリについては、UE-V を使用して既存の Windows デスクトップ アプリ用のカスタム設定テンプレートを作成し、ローミングを有効にできます。この方法を選んだ場合、アプリの開発者がそのコードを変更する必要はありません。UE-V は、オンプレミスの Active Directory ローミングに限られ、Microsoft Desktop Optimization Package を購入済みであることが必要となります。

UEV の設定を行う管理者は、[UE-V グループ ポリシー](https://technet.microsoft.com/itpro/mdop/uev-v2/configuring-ue-v-2x-with-group-policy-objects-both-uevv2)を使って Windows OS の設定やユニバーサル アプリ データのローミングを無効にすることで、Windows デスクトップ アプリのデータのみをローミングすることができます。

- "Roam Windows settings (Windows 設定のローミング)" グループ ポリシーを無効にする。
- "Do not synchronize Windows Apps (Windows アプリを同期しない)" グループ ポリシーを有効にする。
- アプリケーション セクションで "IE" ローミングを無効にする。

将来的にはマイクロソフトが、さらに深いレベルで UE-V を Windows に統合し、Azure AD クラウドを使って設定をローミングできるように UE-V を拡張する方法を模索する可能性はあります。


## 同期された設定とデータをオンプレミスに保存することはできますか
Enterprise State Roaming では、同期対象となるすべてのデータが Azure クラウドに保存されます。一方、オンプレミスのローミング ソリューションは UE-V となります。オンプレミスのローミング ソリューションをご希望の企業はこちらをご利用ください。

## ローミングされるデータはだれが所有するのですか
Enterprise State Roaming でローミングされたデータは各企業の所有となります。データは Azure データセンターに保存されます。すべてのユーザー データは、転送されているときも、クラウドに保存されている間も、Azure Rights Management (Azure RMS) を使って暗号化されます。これは Microsoft アカウントを使った設定の同期機能から改良された点です。Microsoft アカウント ベースの同期では、特定の機密データ (ユーザーの資格情報など) だけが、デバイスから送信される前に暗号化されていました。

マイクロソフトは、顧客データの保護に積極的に取り組んでいます。企業ユーザーの設定データは、Windows 10 デバイスから送信される前に、Azure RMS によって自動的に暗号化されるため、別のユーザーがそのデータを読み取ることはできません。勤務先が Azure RMS の有料サブスクリプションを取得済みである場合は、他の Azure RMS 機能 (ドキュメントの追跡と失効など) を利用したり、機密情報を含んだ電子メールを自動的に保護したり、独自にキーを管理したり ("Bring Your Own Key" ソリューション、BYOK とも呼ばれます) することができます。これらの機能や Azure RMS の機能について詳しくは、「[Azure Active Directory Rights Management の概要](https://technet.microsoft.com/jj585026.aspx)」を参照してください。

## 特定のアプリや設定の同期を管理することはできますか
Windows 10 の MDM やグループ ポリシーには、個々のアプリケーションを対象にローミングを無効にするための設定は存在しません。テナントの管理者がその管理対象デバイス上の全アプリを対象にアプリ データの同期を無効にすることはできますが、アプリ単位やアプリ内の階層単位で細かく制御することはできません。

## ローミングを有効/無効にするために個々のユーザーができることは
**設定**アプリから **[アカウント]**、**[設定の同期]** の順に移動します。このページから、設定のローミングに使用されているアカウントを確認したり、ローミング対象となる設定グループの有効と無効を個別に切り替えたりすることができます。

##Windows 10 におけるローミングの実現手段として現在マイクロソフトが推奨する方法は
設定のローミングに関して、マイクロソフトでは、移動ユーザー プロファイル、UE-V、Enterprise State Roaming などいくつかのソリューションを提供しています。マイクロソフトが将来の Windows バージョン向けとして積極的に開発に取り組んでいるのは Enterprise State Roaming です。データをクラウドに移すことに関して勤務先が積極的でない、または準備が整っていない、という場合は、主要なローミング テクノロジとして UE-V を使用するようお勧めします。既存の Windows デスクトップ アプリケーションのローミングに対応する必要がある一方で、クラウドへの移行を勤務先が積極的に推進している場合は、Enterprise State Roaming と UE-V を併用するようお勧めします。UE-V と Enterprise State Roaming はよく似たテクノロジではありますが、両者は共存することができます。企業はこの 2 つの機能を相互補完的に利用することで、社内のユーザーが必要とするローミング サービスを確実に実現できます。

Enterprise State Roaming と UE-V を併用するときは、次の規則が当てはまります。

- Enterprise State Roaming がデバイス上の主要なローミング エージェントになります。UE-V は、"Win32 とのギャップ" を補うために使用されます。
- Windows の設定と最近の UWP アプリのデータについては、Enterprise State Roaming で処理される範囲に含まれているため、UE-V グループ ポリシーを使用して UE-V ローミングを無効にする必要があります。

##仮想デスクトップ インフラストラクチャ (VDI) に対する Enterprise State Roaming の対応について教えてください
Enterprise State Roaming がサポートされるのは Windows 10 クライアント SKU のみで、サーバー SKU ではサポートされません。ハイパーバイザー コンピューターでクライアント VM がホストされ、その仮想マシンにエンド ユーザーがリモートからログインしている場合、そのユーザーのデータはローミングされます。複数のユーザーが同じ OS を共有しており、ユーザーがリモートからサーバーにログインしてデスクトップとまったく同じようにその環境を利用している場合、ローミングは正常に機能しない場合があります。後者のセッション ベースのシナリオは公式対応していません。


## ローミング機能を使用してしばらく経った後に勤務先が Azure RMS を購入したらどうなりますか
Windows 10 と Azure RMS の (機能が限定された) 無料サブスクリプションでのローミングを既にご利用の場合、有料の Azure RMS サブスクリプションを購入しても、ローミング機能の働きには一切影響せず、IT 管理者が構成に変更を加える必要もありません。

## 既知の問題

- スマート カードまたは仮想スマート カードで Windows にログインすると、設定の同期が機能しなくなります。スマート カードまたは仮想スマート カードを使ってデバイスにログインしようとすると、同期機能が停止します。この問題は今後、Windows 10 の更新プログラムで解決される可能性があります。
- IE のお気に入りの同期は、以前のバージョンの Windows 10 ビルドでは正しく機能しません。IE のお気に入りの同期機能を使用するためには、July Cumulative Update for Windows 10 (ビルド 10586.494 以降) が必要となります。
- Multi-Factor Authentication (MFA) が構成されている場合、特定の条件下で Enterprise State Roaming がデータの同期に失敗する可能性があります。
    - Azure AD ポータルで [Azure MFA](multi-factor-authentication.md) を必須とするようにユーザーが構成されている場合、そのユーザーがパスワードを使って Windows 10 デバイスにログインしているときは、設定を同期できません。このタイプの MFA 構成は、Azure 管理者アカウントの保護を意図したものです。管理者ユーザーは、[Microsoft Passport for Work](active-directory-azureadjoin-passport.md) の PIN を使用するか、または他の Azure サービス (Office 365 など) にアクセスしている状態で Multi-Factor Authentication を行い、Windows 10 デバイスにログインすることによって同期を実行できる場合があります。
    - AD FS MFA 条件付きアクセス ポリシーが管理者によって構成されているとき、デバイス上のアクセス トークンの有効期限が切れている場合、同期に失敗する場合があります。一度ログオフしてから [Microsoft Passport for Work](active-directory-azureadjoin-passport.md) の PIN を使用してログインし直すか、または他の Azure サービス (Office 365 など) にアクセスしている状態で Multi-Factor Authentication を行ってください。
   
- コンピューターが Azure AD デバイスへの自動登録によってドメインに参加している場合、コンピューターが長期にわたって接続されず、かつドメイン認証が完了できないと同期に失敗します。この問題を解決するには、コンピューターを企業ネットワークに接続します。これで同期を再開することができます。


## 関連トピック
- [Enterprise State Roaming の概要](active-directory-windows-enterprise-state-roaming-overview.md)
- [Azure Active Directory の Enterprise State Roaming を有効にする](active-directory-windows-enterprise-state-roaming-enable.md)
- [設定を同期させるためのグループ ポリシーと MDM の設定](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
- [Windows 10 ローミング設定リファレンス](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)

<!---HONumber=AcomDC_0803_2016-->