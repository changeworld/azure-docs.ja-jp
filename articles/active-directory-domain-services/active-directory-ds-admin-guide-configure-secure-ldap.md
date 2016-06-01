<properties
	pageTitle="Azure AD ドメイン サービスでのセキュリティで保護された LDAP (LDAPS) の構成 | Microsoft Azure"
	description="Azure AD ドメイン サービスの管理対象ドメインに対するセキュリティで保護された LDAP (LDAPS) の構成"
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/27/2016"
	ms.author="maheshu"/>

# Azure AD ドメイン サービスの管理対象ドメインに対するセキュリティで保護された LDAP (LDAPS) の構成
この記事では、Azure AD ドメイン サービスの管理対象ドメインに対して、セキュリティで保護されたライトウェイト ディレクトリ アクセス プロトコル (LDAPS) を有効にする方法について説明します。セキュリティで保護された LDAP は、「Secure Sockets Layer (SSL)/トランスポート層セキュリティ (TLS) 経由のライトウェイト ディレクトリ アクセス プロトコル (LDAP)」としても知られています。

## 開始する前に
この記事に記載されているタスクを実行するには、次が必要です。

1. 有効な **Azure サブスクリプション**。

2. オンプレミス ディレクトリまたはクラウド専用ディレクトリのいずれかと同期されている **Azure AD ディレクトリ**。

3. **Azure AD ドメイン サービス**が Azure AD ディレクトリに対して有効である必要があります。有効になっていない場合は、[作業の開始に関するガイド](./active-directory-ds-getting-started.md)に記載されているすべてのタスクを実行してください。

4. **セキュリティで保護された LDAP を有効にするために使用する証明書**。
    - **推奨** - エンタープライズ CA または公開証明機関から証明書を取得します。これははるかに安全性の高い構成オプションです。
	- または、この記事の後半で説明するように、[自己署名証明書を作成](./active-directory-ds-admin-guide-configure-secure-ldap.md/#task-1---obtain-a-certificate-for-secure-ldap)することもできます。

<br>

### セキュリティで保護された LDAP 証明書の要件
セキュリティで保護された LDAP を有効にする前に、以下のガイドラインに従って有効な証明書を取得します。管理対象ドメインに対して、セキュリティで保護された LDAP を無効な証明書または正しくない証明書で有効にしようとすると、エラーが発生します。

1. **信頼された発行者** - 証明書は、セキュリティで保護された LDAP を使用してドメインに接続する必要があるコンピューターによって信頼された機関から発行される必要があります。これは、組織のエンタープライズ証明機関またはこれらのコンピューターによって信頼された公開証明機関のいずれかです。

2. **有効期間** - 証明書は少なくとも、今後 3 ～ 6 か月間有効である必要があります。これにより、証明書の有効期限が切れたときに、セキュリティで保護された LDAP の管理対象ドメインへのアクセスが切断されません。

3. **サブジェクト名** - 証明書のサブジェクト名は、管理対象ドメインに対してワイルドカードにする必要があります。たとえば、ドメインが contoso100.com という名前の場合、証明書のサブジェクト名は *.contoso100.com にする必要があります。DNS 名 (サブジェクト代替名) もこのワイルドカード名に設定する必要があります。

3. **キー使用法** - 証明書は、デジタル署名およびキーの暗号化に対して構成される必要があります。

4. **証明書の目的** - 証明書は、SSL サーバー認証に対して有効である必要があります。

<br>

## タスク 1 - セキュリティで保護された LDAP 用の証明書の取得
最初のタスクには、セキュリティで保護された LDAP の管理対象ドメインへのアクセスに使用する証明書の取得が含まれます。2 つのオプションがあります。

- 組織のエンタープライズ CA などの証明機関または公開証明機関から証明書を取得します。

- 自己署名証明書を作成します。


### オプション A (推奨) - セキュリティで保護された LDAP 証明書を証明機関から取得する
組織がエンタープライズ公開キー基盤 (PKI) をデプロイする場合は、組織のエンタープライズ証明機関 (CA) から証明書を取得する必要があります。組織が公開証明機関から証明書を取得する場合は、セキュリティで保護された LDAP 証明書をその公開証明機関から取得する必要があります。

証明書を請求する場合は、「[セキュリティで保護された LDAP 証明書の要件](./active-directory-ds-admin-guide-configure-secure-ldap.md/#requirements-for-the-secure-ldap-certificate)」で説明されている要件に従うようにします。

セキュリティで保護された LDAP を使用して管理対象ドメインに接続する必要があるクライアント コンピューターが、LDAPS 証明書の発行者を信頼する必要があることに注意してください。


### オプション B - セキュリティで保護された LDAP 用の自己署名証明書を作成する
次の場合、セキュリティで保護された LDAP 用の自己署名証明書を作成することを選択できます。

- 組織の証明書がエンタープライズ証明機関から発行されていない
- または公開証明機関からの証明書の使用を見込めない

**PowerShell を使用した自己署名証明書の作成**

Windows コンピューターで**管理者**として新しい PowerShell ウィンドウを開き、新しい自己署名証明書を作成するために、次のコマンドを入力します。

    $lifetime=Get-Date

    New-SelfSignedCertificate -Subject *.contoso100.com -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment -Type SSLServerAuthentication -DnsName *.contoso100.com

上記のサンプルで、contoso100.com を Azure AD ドメイン サービスの管理対象ドメインの DNS ドメイン名に置き換えます。

![Azure AD ディレクトリの選択](./media/active-directory-domain-services-admin-guide/secure-ldap-powershell-create-self-signed-cert.png)

新しく作成された自己署名証明書は、ローカル コンピューターの証明書ストアに配置されます。


## タスク 2 - セキュリティで保護された LDAP 証明書を .PFX ファイルにエクスポートする
このタスクを開始する前に、エンタープライズ証明機関または公開証明機関からセキュリティで保護された LDAP 証明書を取得していること、または自己署名証明書を作成していることを確認します。

LDAPS 証明書を .PFX ファイルにエクスポートするために、次の手順を実行します。

1. **[スタート]** ボタンを押して「**R**」と入力し、**[ファイル名を指定して実行]** ダイアログを表示します。「**mmc**」と入力して **[OK]** をクリックします。

    ![MMC コンソールを起動する](./media/active-directory-domain-services-admin-guide/secure-ldap-start-run.png)

2. **[ユーザー アカウント制御]** ダイアログで **[はい]** をクリックし、管理者として MMC (Microsoft 管理コンソール) を起動します。

3. **[ファイル]** メニューで **[スナップインの追加と削除...]** をクリックします。

    ![MMC コンソールにスナップインを追加する](./media/active-directory-domain-services-admin-guide/secure-ldap-add-snapin.png)

4. **[スナップインの追加と削除]** ダイアログで **[証明書]** のスナップインをクリックして、**[追加 >]** ボタンをクリックします。

    ![MMC コンソールに証明書スナップインを追加する](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-snapin.png)

5. **証明書スナップイン** ウィザードで **[コンピューター アカウント]**を選択し、**[次へ]** をクリックします。

    ![コンピューター アカウントの証明書スナップインを追加する](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-computer-account.png)

6. **[コンピューターの選択]** ページで **[ローカル コンピューター (このコンソールを実行しているコンピューター)]** を選択し、**[完了]** をクリックします。

    ![証明書スナップインを追加する - コンピューターを選択する](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-local-computer.png)

7. **[スナップインの追加と削除]** ダイアログで **[OK]** をクリックし、証明書スナップインを MMC に追加します。

    ![MMC に証明書スナップインを追加する - 完了](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-snapin-done.png)

8. MMC のウィンドウで **[コンソール ルート]** をクリックして展開します。証明書スナップインが読み込まれます。**[証明書 (ローカル コンピューター)]** をクリックして展開します。**[個人]** ノードをクリックして展開すると、**[証明書]** ノードが表示されます。

    ![個人証明書ストアを開く](./media/active-directory-domain-services-admin-guide/secure-ldap-open-personal-store.png)

9. 上で説明しているように、作成した自己署名証明書が表示されます。証明書のプロパティを確認して、証明書を作成したときに PowerShell ウィンドウで報告されたものと拇印が一致することを確認します。

10. 自己署名証明書を選択して**右クリック**します。右クリック メニューから **[すべてのタスク]** を選択し、**[エクスポート...]** を選択します。

    ![証明書をエクスポートします。](./media/active-directory-domain-services-admin-guide/secure-ldap-export-cert.png)

11. **証明書のエクスポート ウィザード**で **[次へ]** をクリックします。

    ![証明書のエクスポート ウィザード](./media/active-directory-domain-services-admin-guide/secure-ldap-export-cert-wizard.png)

12. **[秘密キーのエクスポート]** ページで **[はい、秘密キーをエクスポートします]** を選択し、**[次へ]** をクリックします。

    ![証明書のエクスポート - 秘密キー](./media/active-directory-domain-services-admin-guide/secure-ldap-export-private-key.png)

    > [AZURE.WARNING] 証明書とともに秘密キーをエクスポートする必要があります。管理対象ドメインに対してセキュリティで保護された LDAP を有効にしようとしても、証明書の秘密キーが含まれない PFX を提供すると失敗します。

13. **[エクスポート ファイルの形式]** ページで、エクスポートされた証明書のファイル形式として **[Personal Information Exchange - PKCS #12 (.PFX)]** を選択します。

    ![証明書のエクスポート - ファイル形式](./media/active-directory-domain-services-admin-guide/secure-ldap-export-to-pfx.png)

	> [AZURE.NOTE] .PFX ファイル形式のみがサポートされています。証明書を .CER ファイル形式にエクスポートしないでください。

14. **[セキュリティ]** ページで **[パスワード]** オプションを選択し、.PFX ファイルを保護するためのパスワードを入力します。次のタスクで必要になるので、このパスワードを覚えておいてください。**[次へ]** をクリックして続行します。

    ![証明書のエクスポート - パスワードを指定する](./media/active-directory-domain-services-admin-guide/secure-ldap-export-select-password.png)

	> [AZURE.NOTE] このパスワードを書き留めておいてください。「[タスク 3 - 管理対象ドメインに対してセキュリティで保護された LDAP を有効にする](./active-directory-ds-admin-guide-configure-secure-ldap.md/#task-3---enable-secure-ldap-for-the-managed-domain)」で、この管理対象ドメインに対してセキュリティで保護された LDAP を有効にするときに必要になります。

15. **[エクスポートするファイル]** ページで、ファイル名と証明書をエクスポートする場所を指定します。

    ![証明書のエクスポート - パスを指定する](./media/active-directory-domain-services-admin-guide/secure-ldap-export-select-path.png)

16. 次のページで **[完了]** をクリックして、証明書を PFX ファイルにエクスポートします。証明書がエクスポートされると、確認のダイアログが表示されます。

    ![証明書のエクスポート完了](./media/active-directory-domain-services-admin-guide/secure-ldap-exported-as-pfx.png)


## タスク 3 - 管理対象ドメインに対してセキュリティで保護された LDAP を有効にする
セキュリティで保護された LDAP を有効にするには、次の構成手順を実行します。

1. **[Azure クラシック ポータル](https://manage.windowsazure.com)**に移動します。

2. 左ウィンドウで、**[Active Directory]** を選択します。

3. Azure AD ドメイン サービスを有効にした Azure AD ディレクトリ ("テナント" とも呼ばれる) を選択します。

    ![Azure AD ディレクトリの選択](./media/active-directory-domain-services-getting-started/select-aad-directory.png)

4. **[構成]** タブをクリックします。

    ![ディレクトリの [構成] タブ](./media/active-directory-domain-services-getting-started/configure-tab.png)

5. **[ドメイン サービス]** というセクションまで下にスクロールします。次のスクリーンショットで示すように、**[セキュリティで保護された LDAP (LDAPS)]** というオプションが表示されます。

    ![Domain Services 構成セクション](./media/active-directory-domain-services-admin-guide/secure-ldap-start.png)

6. **[証明書の構成...]** ボタンをクリックして **[セキュリティで保護された LDAP 用の証明書の構成]** ダイアログを表示します。

    ![セキュリティで保護された LDAP 用の証明書の構成](./media/active-directory-domain-services-admin-guide/secure-ldap-configure-cert-page.png)

7. **[証明書を含む PFX ファイル]** の下にあるフォルダー アイコンをクリックし、セキュリティで保護された LDAP の管理対象ドメインへのアクセスに使用する証明書を含む PFX ファイルを指定します。また、証明書を PFX ファイルにエクスポートするときに指定したパスワードを入力します。完了したら、下部にある完了ボタンをクリックします。

    ![セキュリティで保護された LDAP の PFX ファイルとパスワードを指定する](./media/active-directory-domain-services-admin-guide/secure-ldap-specify-pfx.png)

8. **[構成]** タブの**[ドメイン サービス]** セクションは淡色表示になり、数分間 **[保留中...]** の状態になります。この期間中に、LDAPS 証明書の精度が検証され、セキュリティで保護された LDAP が管理対象ドメインに対して構成されます。

    ![セキュリティで保護された LDAP - 保留中の状態](./media/active-directory-domain-services-admin-guide/secure-ldap-pending-state.png)

    > [AZURE.NOTE] 管理対象ドメインに対してセキュリティで保護された LDAP を有効にするには、約 10 ～ 15 分かかります。提供されたセキュリティで保護された LDAP 証明書が必要条件を満たしていない場合 (例: ドメイン名が正しくない、証明書の有効期限が切れている、または間もなく切れる、など)、セキュリティで保護された LDAP はディレクトリに対して有効にならず、失敗します。

9. セキュリティで保護された LDAP が管理対象ドメインに対して有効になると、**[保留中...]** のメッセージは表示されなくなり、証明書の拇印が表示されます。

    ![セキュリティで保護された LDAP 有効](./media/active-directory-domain-services-admin-guide/secure-ldap-enabled.png)

<br>


## タスク 4 - インターネット経由でのセキュリティで保護された LDAP アクセスを有効にする
**オプションのタスク** - インターネット経由で LDAPS を使用して管理対象ドメインにアクセスする予定がない場合は、このタスクをスキップしてください。

このタスクを開始する前に、[タスク 3](./active-directory-ds-admin-guide-configure-secure-ldap.md/#task-3---enable-secure-ldap-for-the-managed-domain) で説明した手順が完了していることを確認してください。

1. **[構成]** ページの **[ドメイン サービス]** セクションに、**[インターネット経由でのセキュリティで保護された LDAP アクセスを有効にする]** ためのオプションが表示されます。セキュリティで保護された LDAP 経由での管理対象ドメインへのインターネット アクセスは既定で無効になっているので、この設定は既定では **[いいえ]** に設定されています。

    ![セキュリティで保護された LDAP 有効](./media/active-directory-domain-services-admin-guide/secure-ldap-enabled2.png)

2. **[インターネット経由でのセキュリティで保護された LDAP アクセスを有効にする]** を **[はい]** に切り替えます。下部のパネルの **[保存]** ボタンをクリックします。![セキュリティで保護された LDAP 有効](./media/active-directory-domain-services-admin-guide/secure-ldap-enable-internet-access.png)

3. **[構成]** タブの**[ドメイン サービス]** セクションは淡色表示になり、数分間 **[保留中...]** の状態になります。この期間中に、セキュリティで保護された LDAP 経由での管理対象ドメインへのインターネット アクセスが有効になります。

    ![セキュリティで保護された LDAP - 保留中の状態](./media/active-directory-domain-services-admin-guide/secure-ldap-enable-internet-access-pending-state.png)

    > [AZURE.NOTE] 管理対象ドメインに対して、セキュリティで保護された LDAP 経由でのインターネット アクセスを有効にするには、約 10 分かかります。

4. インターネット経由での管理対象ドメインへのセキュリティで保護された LDAP のアクセスが有効になると、**[保留中...]** のメッセージは表示されなくなります。LDAPS 経由でディレクトリにアクセスするために使用できる外部 IP アドレスが、**[LDAPS アクセスのための外部 IP アドレス]** フィールドに表示されます。

    ![セキュリティで保護された LDAP 有効](./media/active-directory-domain-services-admin-guide/secure-ldap-internet-access-enabled.png)

<br>

## タスク 5 - インターネットから管理対象ドメインにアクセスできるように DNS を構成する
**オプションのタスク** - インターネット経由で LDAPS を使用して管理対象ドメインにアクセスする予定がない場合は、このタスクをスキップしてください。

このタスクを開始する前に、[タスク 4](./active-directory-ds-admin-guide-configure-secure-ldap.md/#task-4---enable-secure-ldap-access-over-the-internet) で説明した手順が完了していることを確認してください。

管理対象ドメインに対してインターネット経由でのセキュリティで保護された LDAP アクセスを有効にしたら、クライアント コンピューターがこの管理対象ドメインを見つけられるようにするために、DNS を更新する必要があります。タスク 4 の最後には、**[構成]** タブの **[LDAPS アクセスのための外部 IP アドレス]** に外部 IP アドレスが表示されます。

外部 DNS プロバイダーを構成して、管理対象ドメインの DNS 名 (例: contoso100.com) がこの外部 IP アドレスをポイントするようにします。この例では、次の DNS エントリを作成する必要があります。

    contoso100.com  -> 52.165.38.113

これで、インターネット経由でセキュリティで保護された LDAP を使用して管理対象ドメインに接続する準備ができました。

> [AZURE.WARNING] LDAPS を使用して管理対象ドメインに正常に接続できるようにするために、クライアント コンピューターが LDAPS 証明書の発行者を信頼する必要があることに注意してください。エンタープライズ証明機関または公的に信頼された証明機関を使用している場合は、クライアント コンピューターがこれらの証明書発行者を信頼するため、問題にはなりません。自己署名証明書を使用している場合は、自己署名証明書 (秘密キーなしでエクスポートされる) の公開部分をクライアント コンピューター上の信頼された証明書ストアにインストールする必要があります。

<br>

## 関連コンテンツ

- [Azure AD ドメイン サービスで管理されているドメインの管理](active-directory-ds-admin-guide-administer-domain.md)

<!---HONumber=AcomDC_0518_2016-->