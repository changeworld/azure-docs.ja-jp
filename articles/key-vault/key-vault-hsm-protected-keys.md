<properties
	pageTitle="Azure Key Vault の HSM 保護キーを生成し、転送する方法 | Microsoft Azure"
	description="この記事は Azure Key Vault と共に使用する独自の HSM 保護キーを計画、生成、転送する際に役立ちます。"
	services="key-vault"
	documentationCenter=""
	authors="cabailey"
	manager="mbaldwin"
	tags="azure-resource-manager"/>

<tags
	ms.service="key-vault"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/01/2016"
	ms.author="cabailey"/>
#Azure Key Vault の HSM 保護キーを生成し、転送する方法

##はじめに

Azure Key Vault の使用時にさらに安心感を高める場合、ハードウェア セキュリティ モジュール (HSM) でキーをインポートしたり、生成したりできます。キーは HSM の境界内から出ることはありません。このシナリオは、多くの場合、*Bring Your Own Key* または BYOK と呼ばれています。HSM は、FIPS 140-2 レベル 2 で検証済みです。Azure Key Vault は HSM の Thales nShield ファミリを使用してキーを保護します。

このトピックの情報は Azure Key Vault と共に使用する独自の HSM 保護キーを計画、生成、転送する際に役立ちます。

この機能は Azure China では使用できません。

>[AZURE.NOTE] Azure Key Vault の詳細については、「[What is Azure Key Vault? (Azure Key Vault とは)](key-vault-whatis.md)」を参照してください。
>
>HSM 保護キーの Key Vault 作成を含む入門チュートリアルについては、「[Azure Key Vault の概要](key-vault-get-started.md)」を参照してください。

HSM 保護キーを生成し、インターネットで転送する方法:

- 攻撃を減らすオフライン ワークステーションからキーを生成します。

- キーは Key Exchange Key (KEK) で暗号化されます。Azure Key Vault HSM に転送されるまで暗号化が維持されます。暗号化されたキーだけが元のワークステーションを離れます。

- ツールセットにより、キーを Azure Key Vault セキュリティ ワールドにバインディングするテナント キーのプロパティが設定されます。そのため、Azure Key Vault HSM がキーを受け取り、復号化すると、これらの HSM のみでそれを使用できます。キーはエクスポートできません。このバインディングは Thales HSM により適用されます。

- キーの暗号化に使用される Key Exchange Key (KEK) は Azure Key Vault HSM 内で生成され、エクスポートできません。HSM により、HSM の外部では平文の KEK がありません。また、ツールセットには、KEK はエクスポート不可であり、Thales で作成された本物の HSM 内で生成されたことを示す Thales からの証明が含まれます。

- ツールセットには、Azure Key Vault セキュリティ ワールドも Thales で作成された本物の HSM 内で生成されたことを示す Thales からの証明が含まれます。これにより、Microsoft が本物のハードウェアを使用していることが証明されます。

- Microsoft は地域ごとに個別の KEK と個別のセキュリティ ワールドを使用します。それにより、キーを暗号化したリージョンのデータ センターでのみキーを使用できます。たとえば、欧州のお客様からのキーは北米やアジアのデータ センターでは使用できません。

##Thales HSM と Microsoft のサービスの詳細

Thales e-Security は、金融サービス、ハイテク、製造、政府、テクノロジ セクターにデータ暗号化とサイバー セキュリティのソリューションを提供することで世界をリードする企業です。企業と政府の情報を 40 年間保護してきた実績を持つ Thales ソリューションはエネルギー/航空宇宙産業の大企業 5 社のうち 4 社で採用され、22 の NATO 加盟国で利用されています。世界中の支払処理の 80% 以上を保護しています。

Microsoft は Thales と連携し、HSM の最新技術を強化しています。この改良により、キーの管理を放棄することなく、ホステッド サービスの一般的な長所を得ることができます。具体的には、この改良により、Microsoft があなたの代わりに HSM を管理できます。クラウド サービスとしては、Azure Key Vault は短期間でスケールアップされ、組織の利用率急増に対応します。同時に、キーは Microsoft の HSM 内で保護されます。あなたがキーを生成し、それを Microsoft の HSM に転送するため、キーが存続する間、あなたがそれを管理します。

##Azure Key Vault の Bring Your Own Key (BYOK) の実装

独自の HSM 保護キーを生成し、それを Azure Key Vault に転送する場合 (Bring Your Own Key (BYOK) シナリオ)、次の情報と手順を利用します。


##BYOK の前提条件

Azure Key Vault の Bring Your Own Key (BYOK) の前提条件の一覧については、次の表を参照してください。

|要件|詳細情報|
|---|---|
|Azure のサブスクリプション|Azure Key Vault を作成するには、Azure サブスクリプションが必要です: [無料試用版に登録する](../../../../pricing/free-trial)|
|HSM をサポートする Azure Key Vault|Azure Key Vault のサービス層と機能に関する詳細については、[Azure Key Vault 価格](../../../../pricing/details/key-vault/) Web サイトを参照してください。|
|Thales HSM、スマート カード、サポート ソフトウェア|Thales ハードウェア セキュリティ モジュールにアクセスできることと Thales HSM の基本操作知識が必要です。互換性のあるモデルの一覧については、あるいは所有していない場合に HSM を購入する方法については、「[Thales ハードウェア セキュリティ モジュール](https://www.thales-esecurity.com/msrms/buy)」を参照してください。|
|次のハードウェアとソフトウェア: <ol><li>Windows 7 以降の Windows OS とバージョン 11.50 以降の Thales nShield ソフトウェアをインストールしているオフライン x64 ワークステーション。<br/><br/>このワークステーションで Windows 7 を実行する場合、[Microsoft .NET Framework 4.5](http://download.microsoft.com/download/b/a/4/ba4a7e71-2906-4b2d-a0e1-80cf16844f5f/dotnetfx45_full_x86_x64.exe) をインストールする必要があります。</li><li>Windows 7 以降の Windows OS をインストールし、インターネットに接続されているワークステーション。</li><li>空き容量が 16 MB 以上の USB ドライブまたはその他のポータブル ストレージ デバイス。</li></ol>|セキュリティ上の理由から、最初のワークステーションをネットワークに接続しないことをお勧めします。ただし、これはプログラミングでは適用されません<br/><br/>次の手順では、このワークステーションを「未接続ワークステーション」と呼んでいることに注意してください。</p></blockquote><br/>また、テナント キーは本稼動ネットワーク用の場合、2 つ目の別個のワークステーションを利用してツールセットをダウンロードし、テナント キーをアップロードすることをお勧めします。ただし、テスト目的で、1 つ目のワークステーションとして同じワークステーションを使用できます。<br/><br/>次の手順では、この 2 つ目のワークステーションを「ネットワーク接続ワークステーション」と呼んでいることに注意してください。</p></blockquote><br/>|

##キーを生成し、Azure Key Vault HSM に転送する

次の 5 つの手順でキーを生成し、Azure Key Vault HSM に転送します。

- [手順 1: インターネット接続ワークステーションを準備する](#step-1-prepare-your-internet-connected-workstation)
- [手順 2: 未接続ワークステーションを準備する](#step-2-prepare-your-disconnected-workstation)
- [手順 3: キーを生成する](#step-3-generate-your-key)
- [手順 4: キーの転送準備をする](#step-4-prepare-your-key-for-transfer)
- [手順 5: キーを Azure Key Vault に転送する](#step-5-transfer-your-key-to-azure-key-vault)

## 手順 1: インターネット接続ワークステーションを準備する
この最初の手順では、インターネットに接続されているワークステーションで次の手順を実行します。


###手順 1.1: Azure PowerShell をインストールする

インターネット接続ワークステーションから、Azure Key Vault を管理するためのコマンドレットを含む Azure PowerShell をダウンロードしてインストールします。この作業には 0.8.13 以降のバージョンが必要です。

インストール指示については、「[Azure PowerShell のインストールと構成の方法](../powershell-install-configure.md)」を参照してください。

###手順 1.2: Azure サブスクリプション ID を取得する

Azure PowerShell セッションを開始し、次のコマンドで Azure アカウントにサインインします。

		Add-AzureAccount
ポップアップ ブラウザー ウィンドウで、Azure アカウントのユーザー名とパスワードを入力します。次に [Get-azuresubscription](https://msdn.microsoft.com/library/azure/dn790366.aspx) コマンドを使用します。

		Get-AzureSubscription
出力から、Azure Key Vault で使用するサブスクリプションの ID を見つけます。このサブスクリプション ID は後で必要になります。

Azure PowerShell ウィンドウを閉じないでください。

###手順 1.3: Azure Key Vault の BYOK ツールセットをダウンロードする

Microsoft ダウンロード センターにアクセスし、自分の地域リージョンまたは Azure のインスタンスの [Azure Key Vault BYOK ツールセットをダウンロード](http://www.microsoft.com/download/details.aspx?id=45345)します。

|地域リージョンまたは Azure のインスタンス|パッケージ名|SHA-256 パッケージ ハッシュ|
|---|---|---|
|北米|KeyVault-BYOK-Tools-UnitedStates.zip|D9FDA9F5A34E1388CD6C9138E5B75B7051FB7D6B11F087AFE0553DC85CCF0E36|
|ヨーロッパ|KeyVault-BYOK-Tools-Europe.zip|881DCA798305B8408C06BAE7B3EFBC1E9EA6113A8D6EC443464F3744896F32C3|
|アジア|KeyVault-BYOK-Tools-AsiaPacific.zip|0C76967B3AC76687E4EA47EB96174EE6B25AB24E3114E28A90D9B93A2E6ABF6E|
|ラテン アメリカ|KeyVault-BYOK-Tools-LatinAmerica.zip|B38015990D4D1E522B8367FF78E78E0234BF9592663470426088C44C3CAAAF48|
|日本|KeyVault-BYOK-Tools-Japan.zip|DB512CD9472FDE2FD610522847DF05E4D7CD49A296EE4A2DD74D43626624A113|
|オーストラリア|KeyVault-BYOK-Tools-Australia.zip|8EBC69E58E809A67C036B50BB4F1130411AD87A7464E0D61A9E993C797915967|
|[Azure Government](../../../../features/gov/)|KeyVault-BYOK-Tools-USGovCloud.zip|4DE9B33990099E4197ED67D786316F628E5218FC1EB0C24DCAD8A1851FD345B8|

ダウンロードした BYOK ツールセットの整合性を検証するには、Azure PowerShell セッションから、[Get FileHash](https://technet.microsoft.com/library/dn520872.aspx) コマンドレットを使用します。

	Get-FileHash KeyVault-BYOK-Tools-*.zip

ツールセットには次が含まれます。

- Key Exchange Key (KEK) パッケージ。この名前は「**BYOK-KEK-pkg-**」から始まります。
- セキュリティ ワールド パッケージ。この名前は「**BYOK-SecurityWorld-pkg-**」から始まります。
- 「verifykeypackage.py」という名前の Python スクリプト。
- 「**KeyTransferRemote.exe**」という名前のコマンドライン実行可能ファイルと関連 DLL。
- 「**vcredist\_x64.exe**」という名前の Visual C++ 再配布可能パッケージ。

USB ドライブまたはその他のポータブル ストレージにパッケージをコピーします。

##手順 2: 未接続ワークステーションを準備する

この 2 つ目の手順では、ネットワーク (インターネットまたは内部ネットワーク) に接続されていないワークステーションで次の手順を実行します。


###手順 2.1: Thales HSM で未接続ワークステーションを準備する

Windows コンピューターに nCipher (Thales) サポート コンピューターをインストールし、そのコンピューターに Thales HSM をアタッチします。

Thales ツールがパスにあることを確認します (**%nfast\_home%\\bin** と **%nfast\_home%\\python\\bin**)。たとえば、次を入力します。

		set PATH=%PATH%;”%nfast_home%\bin”;”%nfast_home%\python\bin”

詳細については、Thales HSM に付属のユーザー ガイドを参照してください。

###手順 2.2: 未接続ワークステーションに BYOK ツールセットをインストールします。

USB ドライブまたはその他のポータブル ストレージから BYOK ツールセット パッケージをコピーし、次の操作します。

1. ダウンロードしたパッケージから任意のフォルダーにファイルを抽出します。
2. そのフォルダーから vcredist\_x64.exe を実行します。
3. 指示に従い、Visual Studio 2012 用の Visual C++ ランタイム コンポーネントをインストールします。

##手順 3: キーを生成する

この 3 つ目の手順では、未接続ワークステーションで次の手順を実行します。

###手順 3.1: セキュリティ ワールドを作成する

コマンド プロンプトを起動し、Thales new-world プログラムを実行します。

	new-world.exe --initialize --cipher-suite=DLf1024s160mRijndael --module=1 --acs-quorum=2/3

このプログラムにより **Security World** ファイルが %NFAST\_KMDATA%\\local\\world で作成されます。これは C:\\ProgramData\\nCipher\\Key Management Data\\local フォルダーに対応します。クォーラムにはさまざまな値を使用できますが、今回の例では、3 枚の空白カードと各カードのピンを入力するように求められます。いずれかの 2 枚のカードがセキュリティ ワールドに完全アクセスを与えます。その 2 枚のカードが新しいセキュリティ ワールドの**管理者カード セット**になります。

次に、次を実行します。

- ワールド ファイルをバックアップします。ワールド ファイル、管理者カード、そのピンを保護します。1 人の人間が複数のカードにアクセスできないようにします。

###手順 3.2: ダウンロードしたパッケージを検証します。

この手順は省略可能ですが、次を検証できるので推奨されます。

- ツールセットに含まれる Key Exchange Key が本物の Thales HSM から生成されていること。
- ツールセットに含まれているセキュリティ ワールドのハッシュが本物の Thales HSM から生成されていること。
- Key Exchange Key がエクスポート不可であること。

>[AZURE.NOTE]ダウンロードしたパッケージを検証するには、HSM を接続し、電源を入れる必要があります。また、(たとえば、あなたが先ほど作成した) セキュリティ ワールドが入っている必要があります。

ダウンロードしたパッケージを検証するには:

1.	地域リージョンまたは Azure のインスタンスによっては、次のいずれかを入力して、verifykeypackage.py スクリプトを実行します。
	- 北米:

			python verifykeypackage.py -k BYOK-KEK-pkg-NA-1 -w BYOK-SecurityWorld-pkg-NA-1
	- ヨーロッパ:

			python verifykeypackage.py -k BYOK-KEK-pkg-EU-1 -w BYOK-SecurityWorld-pkg-EU-1
	- アジア:

			python verifykeypackage.py -k BYOK-KEK-pkg-AP-1 -w BYOK-SecurityWorld-pkg-AP-1
	- ラテン アメリカ:

			python verifykeypackage.py -k BYOK-KEK-pkg-LATAM-1 -w BYOK-SecurityWorld-pkg-LATAM-1
	- 日本:

			python verifykeypackage.py -k BYOK-KEK-pkg-JPN-1 -w BYOK-SecurityWorld-pkg-JPN-1
	- オーストラリア:

			python verifykeypackage.py -k BYOK-KEK-pkg-AUS-1 -w BYOK-SecurityWorld-pkg-AUS-1
	- [Azure 政府機関向け](../../../../features/gov/)の場合、Azure の米国政府インスタンスを使用します。

			python verifykeypackage.py -k BYOK-KEK-pkg-USGOV-1 -w BYOK-SecurityWorld-pkg-USGOV-1

	>[AZURE.TIP]Thales ソフトウェアの %NFAST\_HOME%\\python\\bin に Python が含まれています。

2.	次の表示を確認します。これは検証の成功を示します: **Result: SUCCESS**

このスクリプトは Thales ルート キーまで署名者のチェーンを検証します。このルート キーのハッシュがスクリプトに埋め込まれており、その値は **59178a47 de508c3f 291277ee 184f46c4 f1d9c639** になります。[Thales Web サイト](http://www.thalesesec.com/)にアクセスすると、この値を個別に確認できます。

これで新しいキーを作成する準備が整いました。

###手順 3.3: 新しいキーを作成する

Thales **generatekey** プログラムを利用してキーを生成します。

次のコマンドを実行してキーを生成します。

	generatekey --generate simple type=RSA size=2048 protect=module ident=contosokey plainname=contosokey nvram=no pubexp=

このコマンドを実行するとき、次の指示に従います。

- パラメーター *protect* は、次のように **module** に設定する必要があります。module に設定すると、モジュールで保護されたキーが作成されます。BYOK ツールセットは、OCS で保護されたキーをサポートしていません。

- **ident** と **plainname** の *contosokey* の値を文字列値に置換します。管理費を最小限に抑え、エラーを犯す可能性を減らすために、両方に同じ値を使用することをお勧めします。**Ident** 値には数字、ダッシュ、小文字のみを使用できます。

- pubexp はこの例では空白のまま (既定) ですが、特定の値を指定できます。詳細については、Thales の文書を参照してください。

このコマンドにより %NFAST\_KMDATA%\\local フォルダーにトークン化されたキーのファイルが作成されます。この名前は "**key\_simple\_**" で始まり、コマンドで指定した ident が続きます (例: **key\_simple\_contosokey**)。このファイルには暗号化されたキーが含まれます。

安全な場所でこのトークン化されたキーのファイルをバックアップします。

>[AZURE.IMPORTANT] 後で Azure Key Vault にキーを転送すると、Microsoft はこのキーをあなたの元にエクスポートできません。そのため、キーとセキュリティ ワールドを安全にバックアップすることが極めて重要です。キーのバックアップ方法と最良事例については Thales にお問い合わせください。

これで Azure Key Vault にキーを転送する準備ができました。

##手順 4: キーの転送準備をする

この 4 つ目の手順では、未接続ワークステーションで次の手順を実行します。

###手順 4.1: アクセス権が制限されたキーのコピーを作成します。

キーのアクセス権を制限するには、地域リージョンまたは Azure のインスタンスによって、コマンド プロンプトから次のいずれかを実行します。

- 北米:

		KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-1
- ヨーロッパ:

		KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-EU-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-EU-1
- アジア:

		KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AP-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AP-1
- ラテン アメリカ:

		KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1
- 日本:

		KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1
- オーストラリア:

		KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1
- [Azure 政府機関向け](../../../../features/gov/)の場合、Azure の米国政府インスタンスを使用します。

		KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1

このコマンドを実行するとき、*contosokey* を[キーの生成](#step-3-generate-your-key)手順の「**手順 3.3: 新しいキーを作成する**」で指定した同じ値で置換します。

セキュリティ ワールドの管理者カードを差し込むように求められます。

コマンドが完了すると、**Result: SUCCESS** と表示され、アクセス権が制限されたキーのコピーが "key\_xferacId\_<contosokey>" という名前のファイルに表示されます。

###手順 4.2: キーの新しいコピーを検査する

必要に応じて、Thales ユーティリティを実行し、新しいキーの最小アクセス権を確認します。

- aclprint.py:

		"%nfast_home%\bin\preload.exe" -m 1 -A xferacld -K contosokey "%nfast_home%\python\bin\python" "%nfast_home%\python\examples\aclprint.py"
- kmfile-dump.exe:

		"%nfast_home%\bin\kmfile-dump.exe" "%NFAST_KMDATA%\local\key_xferacld_contosokey"
これらのコマンドを実行するとき、contosokey を[キーの生成](#step-3-generate-your-key)手順の「**手順 3.3: 新しいキーを作成する**」で指定した同じ値で置換します。

###手順 4.3: Microsoft の Key Exchange Key を使用してキーを暗号化する

地域リージョンまたは Azure のインスタンスによって、次のいずれかのコマンドを実行します。

- 北米:

		KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- ヨーロッパ:

		KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-EU-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-EU-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- アジア:

		KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AP-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AP-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- ラテン アメリカ:

		KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- 日本:

		KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- オーストラリア:

		KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- [Azure 政府機関向け](../../../../features/gov/)の場合、Azure の米国政府インスタンスを使用します。

		KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey

このコマンドを実行するとき、次の指示に従います。

- *contosokey* を[キーの生成](#step-3-generate-your-key)手順の「**手順 3.3: 新しいキーを作成する**」でキーの生成に使用した ID で置換します。

- *SubscriptionID* を Key Vault が含まれる Azure サブスクリプションの ID で置換します。この値は先に、[インターネット接続ワークステーションの準備](#step-1-prepare-your-internet-connected-workstation)手順の「**手順 1.2: Azure サブスクリプション ID を取得する**」で取得しました。

- *ContosoFirstHSMKey* を出力ファイル名に使用するラベルで置換します。

完了すると、**Result: SUCCESS** と表示され、"TransferPackage-*ContosoFirstHSMkey*.byok" という名前の新しいファイルが現在のフォルダーに表示されます。

###手順 4.4: キー転送パッケージをインターネット接続ワークステーションにコピーします。

USB ドライブまたはその他のポータブル ストレージを使用し、前の手順の出力ファイル (KeyTransferPackage-ContosoFirstHSMkey.byok) をインターネット接続ワークステーションにコピーします。

##手順 5: Azure Key Vault にキーを転送する

この最後の手順では、インターネット接続ワークステーションで、[Add-AzureKeyVaultKey](https://msdn.microsoft.com/library/azure/dn868048.aspx) コマンドレットを使用し、未接続ワークステーションからコピーしたキー転送パッケージを Azure Key Vault HSM にアップロードします。

	Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMkey' -KeyFilePath 'c:\TransferPackage-ContosoFirstHSMkey.byok' -Destination 'HSM'

アップロードされると、追加したキーのプロパティが表示されます。


##次のステップ

これでこの HSM 保護キーを Key Vault で使用できます。詳細については、[Azure Key Vault の概要](key-vault-get-started.md)のチュートリアルの「**ハードウェア セキュリティ モジュール (HSM) を使用する場合**」セクションを参照してください。

<!---HONumber=AcomDC_0204_2016-->