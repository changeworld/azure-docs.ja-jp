<properties
	pageTitle="組織での Microsoft Windows Hello for Business の有効化 | Microsoft Azure"
	description="組織で Microsoft Passport を有効にするためのデプロイメント手順"
	services="active-directory"
	documentationCenter=""
	keywords="Microsoft Passport、Microsoft Windows Hello for Business デプロイの構成"
	authors="markusvi"
	manager="femila"
	editor=""
	tags="azure-classic-portal"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/28/2016"
	ms.author="femila;markvi"/>


# 組織での Microsoft Windows Hello for Business の有効化

Windows 10 ドメイン参加済みデバイスを Azure Active Directory (Azure AD) に接続したら、以下の手順を実行して組織で Microsoft Windows Hello for Business を有効にします。

## System Center Configuration Manager Current Branch をデプロイする 
Windows Hello for Business のキーに基づいてユーザー証明書をデプロイするには、以下が必要です。

- **System Center Configuration Manager Current Branch**。バージョン 1606 以降をインストールする必要があります。詳細については、「[Documentation for System Center Configuration Manager (System Center Configuration Manager のドキュメント)](https://technet.microsoft.com/library/mt346023.aspx)」および [System Center Configuration Manager チームのブログ](http://blogs.technet.com/b/configmgrteam/archive/2015/09/23/now-available-update-for-system-center-config-manager-tp3.aspx)を参照してください。
- **公開キー基盤 (PKI)**: ユーザー証明書を使用して Microsoft Windows Hello for Business を有効にするには、PKI が準備されている必要があります。PKI がないか、PKI をユーザー証明書には使用しない場合は、次の方法があります。
 - **ドメイン コントローラーをデプロイする**: Windows Server 2016 ビルド 10551 (以降) がインストールされている新しいドメイン コントローラーをデプロイし、[レプリカ ドメイン コントローラーを既存のドメインにインストールする](https://technet.microsoft.com/library/jj574134.aspx)手順に従うか、[新しい環境を作成する場合は、新しい Active Directory フォレストをインストールする](https://technet.microsoft.com/library/jj574166)手順に従います([Signiant Media Exchange](https://datatransfer.microsoft.com/signiant_media_exchange/spring/main?sdkAccessible=true) で ISO をダウンロードできます)。

## Active Directory のグループ ポリシーを使用して Microsoft Windows Hello for Business を構成する

 Windows Server Active Directory のグループ ポリシーを使用すると、Windows へのユーザーのサインイン時に Windows Hello for Business のユーザー資格情報をプロビジョニングするように、Windows 10 ドメイン参加済みデバイスを構成できます。

1. 	サーバー マネージャーを開き、**[ツール]**、**[グループ ポリシーの管理]** の順に移動します。
2.	[グループ ポリシーの管理] から、Azure AD 参加を有効にするドメインに対応するドメイン ノードに移動します。
3.	**[グループ ポリシー オブジェクト]** を右クリックし、**[新規]** を選択します。グループ ポリシー オブジェクトに Enable Windows Hello for Business などの名前を付けます。**[OK]** をクリックします。
4.	新しいグループ ポリシー オブジェクトを右クリックし、**[編集]** を選択します。
5.	**[コンピューターの構成]** > **[ポリシー]** > **[管理用テンプレート]** > **[Windows コンポーネント]** > **[Windows Hello for Business]** の順に移動します。
6.	**[Enable Windows Hello for Business (Windows Hello for Business を有効にする)]** を右クリックし、**[編集]** を選択します。
7.	**[有効]** を選択し、**[適用]** をクリックします。**[OK]** をクリックします。
8.	これで、グループ ポリシー オブジェクトを選択した場所にリンクできるようになりました。組織内のすべての Windows 10 ドメイン参加済みデバイスに対してこのポリシーを有効にするには、グループ ポリシーをドメインにリンクします。次に例を示します。
 - Windows 10 ドメイン参加済みコンピューターを配置する Active Directory の特定の組織単位 (OU)
 - Azure AD に自動登録される Windows 10 ドメイン参加済みコンピューターを含む特定のセキュリティ グループ


## System Center Configuration Manager を使用し、Windows Hello for Business を構成する

System Center Configuration Manager のみ使用し、Windows 10 バージョン 1607 用 Windows Hello for Business ポリシーをデプロイできます。Windows 10 コンピューターのバージョンを参照する必要がある場合は、Windows 10 コンピューターで winver を起動してください。

1. **[System Center Configuration Manager]** を開き、**[資産とコンプライアンス] > [コンプライアンス設定] > [会社のリソースへのアクセス] > [ビジネス向け Windows Hello のプロファイル]** の順に移動します。

	![Windows Hello for Business の構成](./media/active-directory-azureadjoin-passport-deployment/01.png)


2. 上部のツールバーの **[ビジネス向け Windows Hello プロファイルの作成]** をクリックします。

	![Windows Hello for Business の構成](./media/active-directory-azureadjoin-passport-deployment/02.png)

2. **[全般]** ダイアログで、次の手順を実行します。

	![Windows Hello for Business の構成](./media/active-directory-azureadjoin-passport-deployment/03.png)

	a.**[名前]** ボックスに、プロファイルの名前を入力します。たとえば、「**My WHfB Profile**」と入力します。

	b.**[次へ]** をクリックします。


2. **[サポートされているプラットフォーム]** ダイアログで、このビジネス向け Windows Hello プロファイルでプロビジョニングするプラットフォームを選択し、**[次へ]** をクリックします。

	![Windows Hello for Business の構成](./media/active-directory-azureadjoin-passport-deployment/04.png)


2. **[設定]** ダイアログで、次の手順を実行します。

	![Windows Hello for Business の構成](./media/active-directory-azureadjoin-passport-deployment/05.png)

	a.**[Windows Hello for Business の構成]**として、 **[有効]** を選択します。

	b.**[トラステッド プラットフォーム モジュール (TPM) の使用]**として、 **[必須]** を選択します。

	c.**[認証方法]**" として、**[証明書ベース] を選択します。

	d.**[次へ]** をクリックします。



2. **[概要]** ダイアログで、**[次へ]** をクリックします。

2. **[完了]** ダイアログで、**[閉じる]** をクリックします。


2. 上部のツールバーの **[デプロイ]** をクリックします。

	![Windows Hello for Business の構成](./media/active-directory-azureadjoin-passport-deployment/06.png)



## Configuration Manager で Windows Hello for Business の登録証明書を使用するように証明書プロファイルを構成する


オンプレミスの認証に証明書ベースの認証を使用している場合は、証明書プロファイルを構成してデプロイする必要があります。このタスクでは、System Center Configuration Manager で NDES サーバーと証明書登録ポイント サイトの役割を設定する必要があります。詳細については、「[Prerequisites for Certificate Profiles in Configuration Manager (Configuration Manager の証明書プロファイルの前提条件)](https://technet.microsoft.com/library/dn261205.aspx)」を参照してください。

1. **[System Center Configuration Manager]** を開き、**[資産とコンプライアンス] > [コンプライアンス設定] > [会社のリソースへのアクセス] > [証明書プロファイル]** の順に移動します。


2. スマート カード サインイン拡張キー使用法 (EKU) を含むテンプレートを選択します。

証明書プロファイルの **[SCEP 登録]** ページで、**[キー格納プロバイダー]** として **[Passport for Work にインストールする (それ以外は失敗)]** を選択する必要があります。



## 次のステップ
* [エンタープライズ向け Windows 10: デバイスを仕事に使用する方法](active-directory-azureadjoin-windows10-devices-overview.md)
* [Azure Active Directory 参加を使用したクラウド機能の Windows 10 デバイスへの拡張](active-directory-azureadjoin-user-upgrade.md)
* [Microsoft Passport 経由でのパスワードを使用しない ID の認証](active-directory-azureadjoin-passport.md)
* [Azure AD 参加の使用シナリオについて](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Windows 10 エクスペリエンスのためのドメイン参加済みデバイスの Azure AD への接続](active-directory-azureadjoin-devices-group-policy.md)
* [Azure AD Join の設定](active-directory-azureadjoin-setup.md)

<!---HONumber=AcomDC_0928_2016-->