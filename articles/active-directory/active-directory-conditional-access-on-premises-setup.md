<properties
	pageTitle="Azure Active Directory Device Registration を使用したオンプレミスの条件付きアクセスの設定 | Microsoft Azure"
	description="Windows Server 2012 R2 で Active Directory フェデレーション サービス (AD FS) を使用して、オンプレミス アプリケーションへの条件付きアクセスを有効にするための手順。"
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/19/2015"
	ms.author="femila"/>

# Azure Active Directory Device Registration を使用したオンプレミスの条件付きアクセスの設定

ユーザーに各自のデバイスを Azure Active Directory Device Registration サービスに社内参加させるよう求めることにより、ユーザーが個人的に所有しているデバイスを組織で認識済みとしてマークすることができます。Windows Server 2012 R2 で Active Directory フェデレーション サービス (AD FS) を使用してオンプレミス アプリケーションへの条件付きアクセスを有効にするための手順を以下に示します。

> [AZURE.NOTE]Azure Active Directory Device Registration Service の条件付きアクセス ポリシーに登録されているデバイスを使用する場合は、Office 365 のライセンスまたは Azure AD Premium ライセンスが必要になります。これには、Active Directory フェデレーション サービス (AD FS) によってオンプレミス リソースに適用されるポリシーが含まれます。

オンプレミスの条件付きアクセス シナリオの詳細については、「[任意のデバイスからの職場への参加による業務用アプリケーション間の SSO とシームレスな 2 要素認証](https://technet.microsoft.com/library/dn280945.aspx)」をご覧ください。これらの機能は、Azure Active Directory Premium ライセンスを購入したお客様に提供されます。

サポートされているデバイス
-------------------------------------------------------------------------
* Windows 7 ドメイン参加デバイス
* Windows 8.1 個人用およびドメイン参加デバイス
* iOS 6 以降
* Android 4.0 以降、Samsung GS3 以降の携帯電話、Samsung Note2 以降のタブレット

シナリオの前提条件
------------------------------------------------------------------------
* Windows Server Active Directory (Windows Server 2008 以降)
* Windows Server 2012 R2 の更新されたスキーマ
* Azure Active Directory Premium のサブスクリプション
* Azure AD に対する SSO 用に構成された Windows Server 2012 R2 フェデレーション サービス
* Windows Server 2012 R2 Web アプリケーション プロキシ
* デバイス オブジェクトの書き戻しがあるディレクトリ同期ツール (DirSync)。[ここから dirsync.exe の最新ビルドをダウンロードしてください。](http://go.microsoft.com/fwlink/?LinkID=278924)

このリリースの既知の問題
-------------------------------------------------------------------------------
* デバイス ベースの条件付きアクセス ポリシーには、Azure Active Directory から Active Directory へのデバイス オブジェクトの書き戻しが必要です。デバイス オブジェクトを Active Directory に書き戻すには、最大 3 時間かかる可能性があります。
* iOS7 デバイスの場合、クライアント証明書の認証時に、常に証明書の選択が求められます。 
* 今回のリリースでは、iOS8 ベータ デバイスを使用できません。


## Azure Active Directory Device Registration Service をデプロイする
組織用に Azure Active Directory Device Registration Service をデプロイおよび構成するには、以下のガイドに従います。

ここでは、Windows Server Active Directory の構成および Microsoft Azure Active Directory へのサブスクライブが完了していることを前提とします。前記の前提条件をご覧ください。

Azure Active Directory Device Registration サービスを Azure Active Directory テナントと一緒にデプロイするには、以下のチェックリストに示す作業を順番に実行します。参照リンクをクリックして概要に関するトピックに移動した場合は、そのトピックを確認した後、このチェックリストに戻って、チェックリストの残りの作業に進んでください。一部のタスクには、手順が正常に完了したことを確認できるシナリオの検証手順が含まれています。

**チェックリスト: Azure Active Directory Device Registration を有効にする**

以下のチェックリストに従って、Azure Active Directory Device Registration サービスを有効にして構成します。

| タスク | リファレンス |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------|
| Azure Active Directory テナント内の [デバイスの登録] を有効にして、デバイスがワークプレースに参加できるようにします。既定では、サービスに対して Multi-Factor Authentication は有効になっていません。ただし、デバイスを登録するときは Multi-Factor Authentication をお勧めします。ADRS で Multi-Factor Authentication を有効にする前に、Multi-Factor Authentication プロバイダー用に AD FS が構成されていることを確認します。 | [Azure Active Directory Device Registration を有効にする](active-directory-conditional-access-device-registration-overview.md) |
| デバイスは既知の DNS レコードを探すことにより、Azure Active Directory Device Registration サービスを検出します。デバイスが Azure Active Directory Device Registration サービスを検出できるように、会社の DNS を構成する必要があります。 | [Azure Active Directory Device Registration の検出を構成する](active-directory-conditional-access-device-registration-overview.md) |

**チェックリスト:Windows Server 2012 R2 Active Directory フェデレーション サービスをデプロイおよび構成し、Azure Active Directory とのフェデレーション関係をセットアップする**次の一連のタスクでは、Windows Server 2012 R2 フェデレーション サービスを Azure Active Directory に統合します。統合することで、Azure Active Directory Device Registration サービスに登録したデバイスを使用して、ADFS の条件付きアクセス ポリシーに対応できます。


| タスク | リファレンス |
|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------|
| Windows Server 2012 R2 スキーマ拡張で Active Directory ドメイン サービス ドメインをデプロイします。(ドメイン コント ローラーを Windows Server 2012 R2 にアップグレードする必要はありません。スキーマのアップグレードが唯一の要件です)。 | Active Directory ドメイン サービス スキーマをアップグレードする |
| Windows Server 2012 R2 フェデレーション サービスを Web アプリケーション プロキシと一緒にデプロイします。 | Windows Server 2012 R2 フェデレーション サービスを Web アプリケーション プロキシと一緒にデプロイする |
| デバイス オブジェクトのサポートに必要なオブジェクトおよびコンテナーで、Active Directory ドメイン サービス フォレストを構成する必要があります。また、AD FS でデバイス認証を有効にします。 | デバイスをサポートするために Active Directory フォレストの準備を行う |
| 組織と Azure Active Directory 間のフェデレーション関係を設定します。ここでは、Windows Server 2012 R2 フェデレーション サービスを使用して、順番に Azure Active Directory テナントを構成します。 | Azure Active Directory とのフェデレーション関係を構成し、ディレクトリ同期ツールを構成する |
| デバイス オブジェクトの書き戻しを許可するようにディレクトリ同期 (DirSync) を構成します。Azure Active Directory で作成されたデバイスが、Active Directory に書き込まれます。 | デバイス オブジェクトの書き戻しを許可するように DirSync を構成する |
| Windows Server 2012 R2 フェデレーション サービスで Multi-Factor Authentication プロバイダーを構成することを強くお勧めします。構成することで、ユーザーは Multi-Factor Authentication を使用してデバイスを安全に登録できるようになります。 | フェデレーション サービスを構成して Multi-Factor Authentication を実現する |


### Active Directory ドメイン サービス スキーマをアップグレードする
> [AZURE.NOTE]Active Directory スキーマのアップグレードを行うと、それを元に戻すことはできません。これはテスト環境で実行することをお勧めします。

1. エンタープライズ管理者権限とスキーマ管理者権限を両方とも有するアカウントでドメイン コントローラーにログインします。
2. [media]\\support\\adprep ディレクトリとサブディレクトリを、Active Directory ドメイン コントローラーの 1 つにコピーします。 
3. ここで、[media] は Windows Server 2012 R2 インストール メディアへのパスです。
4. コマンド プロンプトから、adprep ディレクトリに移動し、adprep.exe /forestprep を実行します。画面の手順に従ってスキーマのアップグレードを完了します。

### Windows Server 2012 R2 フェデレーション サービスを Web アプリケーション プロキシと一緒にデプロイする
以下の 2 つのガイドに従って Windows Server 2012 R2 フェデレーション サービスを Web アプリケーション プロキシと一緒にデプロイし、このガイドに戻ります。Windows Server 2012 R2 フェデレーション サーバー ファームをデプロイするための手順に従います。

> [AZURE.NOTE]AD FS をデプロイするときは、「オプション手順:フェデレーション サーバーに Device Registration Service (DRS) を構成する」という名前のオプション手順はスキップする必要があります。この手順は、Azure Active Directory Device Registration を使用するときは必要ありません。Windows Server 2012 R2 フェデレーション サーバー プロキシをデプロイするための手順に従います。

Windows Server 2012 R2 フェデレーション サーバーおよびプロキシ サーバーのすべてに、Windows Server 2012 R2 の最新版をインストールします。この操作はフェデレーション ファームの構成前または構成後に実行できます。更新プログラムは、「Windows Server 2012 R2 の更新」で入手できます。


### デバイスをサポートするために Active Directory フォレストの準備を行う

> [AZURE.NOTE]これは、デバイスをサポートするために Active Directory フォレストを準備する際に実行する必要のある 1 回限りの操作です。この手順を完了するには、エンタープライズ管理者権限を使用してログオンする必要があり、Active Directory フォレストに Windows Server 2012 R2 スキーマが組み込まれている必要があります。

1. フェデレーション サーバーで、Windows PowerShell コマンド ウィンドウを開き、次のように入力します。*Initialize-ADDeviceRegistration*

2. [ServiceAccountName] が要求されたら、AD FS のサービス アカウントとして選択したサービス アカウントの名前を入力します。gMSA アカウントである場合は、domain\\accountname$ 形式でアカウントを入力します。ドメイン アカウントである場合は、domain\\accountname の形式を使用します。


### AD FS でデバイス認証を有効にする

1. フェデレーション サーバーで、AD FS 管理コンソールを開き、**[AD FS]** > **[認証ポリシー]** の順に移動します。
2. **[アクション]** ペインで、[グローバル プライマリ認証の編集...] を選択します。
3. **[デバイス認証を有効にする]** をオンにし、[OK] をクリックします。
4. 既定では、AD FS は未使用のデバイスを Active Directory から定期的に削除します。Azure でデバイスを管理できるように Azure Active Directory Device Registration を使用する場合は、この動作を無効にする必要があります。


### 未使用のデバイスのクリーンアップを無効にする
1. フェデレーション サーバーで、Windows PowerShell コマンド ウィンドウを開き、次のように入力します。`*Set-AdfsDeviceRegistration -MaximumInactiveDays 0*`


###Azure Active Directory とのフェデレーション関係を構成し、ディレクトリ同期ツールを構成する
次のセクションでは、Azure Active Directory と AD FS の間にフェデレーションによる信頼関係をセットアップするための情報を提供します。一部の手順を実行する際、このページから離れる場合があります。以下の手順に従った後に、このガイドに戻ります。

Azure Active Directory をローカル Active Directory と統合し、シングル サインオンを構成する
------------------------------------------------------------------------------------------------------

1. Azure ポータルに**管理者**としてサインインします。
2. 左側のウィンドウで **[Active Directory]** を選択します。
3. **[ディレクトリ]** タブでディレクトリを選択します。
4. **[ディレクトリの統合]** タブを選択します。
5. [ローカルの Active Directory との統合] セクションで、[ディレクトリ同期] オプションを特定し、[アクティブ化] を選択します。
6. 手順 1 ～ 4 に従って、Azure Active Directory をローカル ディレクトリと統合します。
  1. **ドメインを追加します。**
  2. **シングル サインオンを構成し、ディレクトリ同期の準備します**。AD FS のデプロイが既に完了している場合は、以下のサブセクションに従って AD FS と Azure AD との信頼関係を構成および検証することができます。 
      *  [AD FS によるシングル サインオン用の Windows PowerShell をインストールする](https://msdn.microsoft.com/library/azure/jj151814.aspx)
      *  [AD FS と Azure AD の間の信頼を確立する](https://msdn.microsoft.com/library/azure/jj205461.aspx)
      *  [AD FS によるシングル サインオンを確認および管理する](https://msdn.microsoft.com/library/azure/jj151809.aspx)
  	  *  [その他の AD FS リファレンス](https://msdn.microsoft.com/library/azure/dn151321.aspx)

  3. ディレクトリ同期ツールをインストールして実行します。DirSync のインストールが既に完了している場合は、最新バージョンへのアップグレードを行う必要があります。6862.0000 以降のバージョンが必要です。
  4. ディレクトリの同期を確認および管理します。 
  5. Azure Active Directory の証明書利用者の信頼オブジェクトに関する追加の要求規則を構成します。通常、この証明書利用者の信頼オブジェクトの名前は「Microsoft Office 365 ID プラットフォーム」です。

###Azure Active Directory 証明書利用者の信頼の要求規則を構成する

1. AD FS 管理コンソールを開き、[AD FS] > [信頼関係] > **[証明書利用者の信頼]** の順に移動します。**[Microsoft Office 365 ID プラットフォーム] 証明書利用者の信頼オブジェクト**を右クリックし、[要求規則の編集…] を選択します。
2. **[発行変換規則]** タブで、[規則の追加] を選択します。
3. **[要求規則テンプレート]** ドロップダウン ボックスから、**[カスタムの規則を使用して要求をを送信する]** を選択します。**[次へ]** を選択します。
4. **[要求規則名]** テキスト ボックスに「Auth Method Claim Rule」と入力します。
5. [要求規則] テキスト ボックスに次の要求規則を入力します。

    ` c:[Type == "http://schemas.microsoft.com/claims/authnmethodsreferences"]` `=> issue(claim = c);`

6. **[OK]** を 2 回クリックして、ダイアログ ボックスを閉じます。
7. Azure Active Directory の証明書利用者の信頼オブジェクトに関する追加の認証クラス参照を構成します。通常、この証明書利用者の信頼オブジェクトの名前は「Microsoft Office 365 ID プラットフォーム」です。


###Azure Active Directory 証明書利用者の信頼の認証クラス参照を構成する

フェデレーション サーバーで、Windows PowerShell コマンド ウィンドウを開き、次のように入力します。

*Set-AdfsRelyingPartyTrust -TargetName <RPObjectName> -AllowedAuthenticationClassReferences wiaormultiauthn*


ここで、<RPObjectName> は、Azure Active Directory 証明書利用者の信頼オブジェクトの証明書利用者オブジェクト名です。通常、このオブジェクトは「Microsoft Office 365 ID プラットフォーム」という名前です。

### デバイス オブジェクトの書き戻しを許可するように DirSync を構成する
デバイス オブジェクトの書き戻しを許可するようにディレクトリ同期 (DirSync) を構成する必要があります。Azure Active Directory で作成されたデバイスが、Active Directory に書き込まれます。Azure Active Directory で作成したデバイスが、Active Directory に書き戻しされるまでに最大 3 時間かかる可能性があります。
>[Azure.Note]次の手順を完了するには、エンタープライズ管理者権限を使用してログオンする必要があります。ここから dirsync.exe の最新ビルドをダウンロードしてください。

> Active Directory へのデバイスの書き戻しを許可するには、Office 365 ライセンスまたは Azure AD Premium ライセンスが必要です。

> DirSync インストール ウィザードを完了したら、サインアウトしてから、サインインして続行してください。これで、更新されたアクセス トークンが使用されます。

>デバイス オブジェクトを Active Directory ドメイン サービスに書き戻すには、最大 3 時間かかる可能性があります。

1. ディレクトリ同期サーバーで、Windows PowerShell コマンド ウィンドウを開き、次のコマンドを発行します。

    `Import-Module DirSync`
    
    `$AADCreds = Get-Credential`
    
    `$ADCreds = Get-Credential`

2. 資格情報を求められたら、クラウド サービス管理者のアカウント資格情報と、Active Directory 管理者の資格情報を入力します。

*Enable-MSOnlineObjectManagement –ObjectTypes Device –TargetCredential $AADCreds -Credential $ADCreds*

### フェデレーション サービスを構成して Multi-Factor Authentication を実現する
組織に登録されたデバイスは、シームレスな 2 つ目の認証要素として使用することができます。したがって、デバイスの登録時には強力な認証を必要とすることを推奨します。以下のガイドに従って Azure Multi-Factor Authentication で AD FS を構成し、このガイドに戻ります。Azure Multi-Factor Authentication をデプロイするための手順に従います。

## Azure Active Directory Device Registration を使用してデバイスをワークプレースに参加させる

### Azure Active Directory Device Registration を使用して iOS デバイスを参加させる

Azure Active Directory Device Registration では、iOS デバイスに対して無線プロファイル登録プロセスを使用します。このプロセスは、ユーザーによる Safari Web ブラウザーからプロファイル登録用 URL への接続で始まります。URL の形式は次のとおりです。

    https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/"yourdomainname"

ここで <yourdomainname> は、Azure Active Directory で構成したドメイン名です。たとえば、ドメイン名が contoso.com の場合、URL は次のようになります。

    https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/contoso.com

この URL は、さまざまな方法ユーザーに伝えることができます。その 1 つとして、AD FS 内のカスタム アプリケーション アクセス拒否メッセージで、この URL を発行する方法が推奨されます。これについては、以降の「アプリケーションのアクセス ポリシーとカスタム アクセス拒否メッセージを作成する」セクションで説明します。

###Azure Active Directory Device Registration を使用して Windows 8.1 デバイスを参加させる

1. Windows 8.1 デバイスで、**[PC の設定]** > **[ネットワーク]** > **[ワークプレース]** の順に移動します。
2. ユーザー名を UPN 形式で入力します。たとえば、「dan@contoso.com.」のように入力します。
3. **[参加]** を選択します。
4. メッセージが表示されたら、資格情報でサインインします。これでデバイスが参加します。

### Azure Active Directory Device Registration を使用して Android デバイスを参加させる

「Android 用の Azure Authenticator」トピックに、Android デバイスに Azure 認証アプリをインストールして、仕事用アカウントを追加するための手順が説明されています。Android デバイスで仕事用アカウントが正常に作成されると、そのデバイスを組織に社内参加させることができます。

### 登録済みのデバイスが Active Directory に書き戻されていること確認する
LDP.exe または ADSI Edit を使用して、デバイス オブジェクトが Active Directory に書き戻されたことを表示し、確認することができます。どちらも、Active Directory 管理ツールで利用できます。

既定では、Azure Active Directory から書き戻されるデバイス オブジェクトは、AD FS ファームと同じドメインに配置されます。

`CN=RegisteredDevices,defaultNamingContext`

###アプリケーションのアクセス ポリシーとカスタム アクセス拒否メッセージを作成する
次のシナリオを考慮します。AD FS でアプリケーションの証明書利用者の信頼を作成し、登録されたデバイスのみを許可する発行承認規則を構成します。これで、登録されたデバイスのみにアプリケーションへのアクセスが許可されます。ユーザーがアプリケーションに容易にアクセスできるようにするには、ユーザーのデバイスを参加させる手順を含むカスタム アクセス拒否メッセージを構成します。これで、ユーザーはアプリケーションにアクセスするために、シームレスな方法でデバイスを登録できます。

次の手順に、このシナリオを実装する方法を示します。
>[AZURE.NOTE]ここでは、AD FS でアプリケーションに対して証明書利用者の信頼を構成済みであると想定します。

1. AD FS MMC ツールを開き、[AD FS] > [信頼関係] > [証明書利用者の信頼] の順に移動します。
2. この新しいアクセス ルールが適用されるアプリケーションを特定します。アプリケーションを右クリックし、[要求規則の編集...] を選択します。
3. **[発行承認規則]** タブを選択し、**[規則の追加...]** を選択します。
4. **[要求規則]** テンプレート ドロップダウン リストから、**[着信要求に基づいてユーザーを許可または拒否する]** を選択します。**[次へ]** を選択します。
5. [要求規則名] フィールドに「**登録されたデバイスからのアクセスを許可する**」と入力します。
6. [着信要求の種類] ドロップダウン リストで **[登録ユーザー]** を選択します。
7. [着信要求の値] フィールドに「**true**」と入力します。
9. **[完了]** を選択し、**[適用]** を選択します。
10. 作成した規則よりも寛容なルールはすべて削除します。たとえば、既定の [すべてのユーザーにアクセスを許可する] を削除します。 
8. **[この着信要求でユーザーにアクセスを許可する]** ラジオ ボタンを選択します。すべてのユーザー ルールにアクセスを許可します。

これで、ユーザーが自身で登録しワークプレースに参加させたデバイスからアクセスする場合に限り、アクセスを許可するようにアプリケーションが構成されます。より高度なアクセス ポリシーについては、「多要素アクセス制御を使用したリスク管理」をご覧ください。

次に、アプリケーションのカスタム エラー メッセージを構成します。エラー メッセージは、アプリケーションにアクセスするには事前にユーザーのデバイスをワークプレースに参加させなければならないことをユーザーに通知するものです。カスタム HTML および Windows PowerShell を使用してカスタム アプリケーション アクセス拒否メッセージを作成できます。

フェデレーション サーバーで、Windows PowerShell コマンド ウィンドウを開き、次のコマンドを入力します。コマンドの一部をシステムに固有の項目に置き換えます。

`Set-AdfsRelyingPartyWebContent -Name "relying party trust name" -ErrorPageAuthorizationErrorMessage ` このアプリケーションにアクセスするには、事前にデバイスを登録する必要があります。

**iOS デバイスを使用している場合は、このリンクを選択してデバイスを参加させてください**:

    a href='https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/yourdomain.com

この iOS デバイスを職場に参加させる。


**Windows 8.1 デバイスを使用している場合は**、[PC 設定] > **[ネットワーク]** > **[ワークプレース]** にアクセスすると、デバイスを参加させることができます。


ここで、"**relying party trust name**" は、AD FS にあるアプリケーション証明書利用者の信頼オブジェクトの名前です。ここで、yourdomain.com は、Azure Active Directory で構成したドメイン名です。たとえば、contoso.com です。Set-AdfsRelyingPartyWebContent コマンドレットに渡す html コンテンツからは、改行 (ある場合) を必ず削除します。

<!---HONumber=August15_HO9-->