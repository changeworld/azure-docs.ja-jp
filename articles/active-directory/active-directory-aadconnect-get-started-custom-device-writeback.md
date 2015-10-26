<properties
	pageTitle="Azure AD Connect でのデバイスの書き戻しの有効化 | Microsoft Azure"
	description="Azure AD Connect を使用するデバイスの書き戻しを有効にする方法について詳しく説明します"
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="StevenPo"
	editor="curtand"/>

<tags
	ms.service="active-directory"  
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/15/2015"
	ms.author="billmath"/>

# Azure AD Connect でのデバイスの書き戻しの有効化

ここでは、Azure AD Connect においてデバイスの書き戻し機能を有効にする方法について説明します。デバイスの書き戻しは、次のシナリオで使用されます。

AD FS (2012 R2 以降) で保護されたアプリケーション (証明書利用者の信頼) へのデバイスに基づく条件付きアクセスを有効にします。

これにより、セキュリティが強化され、アプリケーションへのアクセスが信頼されたデバイスに対してのみ許可されることが保証されます。条件付きアクセスの詳細については、「[条件付きアクセス ポリシーを使用したリスクの管理](active-directory-conditional-access.md)」および「[Azure Active Directory Device Registration を使用したオンプレミスの条件付きアクセスの設定](https://msdn.microsoft.com/library/azure/dn788908.aspx)」をご覧ください。

>[AZURE.Note]Azure Active Directory Device Registration Service の条件付きアクセス ポリシーに登録されているデバイスを使用する場合は、Office 365 または Azure AD Premium のサブスクリプションが必要になります。これには、Active Directory フェデレーション サービス (AD FS) によってオンプレミス リソースに適用されるポリシーが含まれます。

## パート 1: Azure AD Connect を準備する
デバイスの書き戻しの使用を準備するには、次の手順を使用します。

1.	Azure AD Connect がインストールされているコンピューターから、管理者特権モードで PowerShell を起動します。

2.	Active Directory PowerShell モジュールがインストールされていない場合。次のコマンドを使用してインストールします。

	Install-WindowsFeature –Name AD-DOMAIN-Services –IncludeManagementTools

3.	エンタープライズ管理者の資格情報で次のコマンドを実行した後、PowerShell を終了します。

	Import-Module ‘C:\\Program Files\\Microsoft Azure Active Directory Connect\\AdPrep\\AdSyncAdPrep.psm1’

	Initialize-ADSyncDeviceWriteback –DomainName <name> -AdConnectorAccount <account>

説明:



- CN=Device Registration Configuration,CN=Services,CN=Configureation,<forest-dn> の下にコンテナーとオブジェクトが存在しない場合は、新しく作成されて構成されます。



- CN=RegisteredDevices,<domain-dn> の下にコンテナーとオブジェクトが存在しない場合は、新しく作成されて構成されます。このコンテナーにデバイス オブジェクトが作成されます。



- Active Directory でデバイスを管理するために必要なアクセス許可を Azure AD コネクタ アカウントに設定します。



- Azure AD Connect が複数のフォレストにインストールされている場合であっても、1 つのフォレストで実行すれば十分です。

パラメーター:


- DomainName: デバイス オブジェクトが作成される Active Directory ドメイン。注: 特定の Active Directory フォレストのデバイスはすべて、1 つのドメインに作成されます。


- AdConnectorAccount: ディレクトリ内のオブジェクトを管理するために Azure AD Connect によって使用される Active Directory アカウント。

## パート 2: デバイスの書き戻しを有効にする
Azure AD Connect でデバイスの書き戻しを有効にするには、次の手順を使用します。

1.	AAD Connect ウィザードを実行します。ウィザードを初めて使用する場合は、[簡単設定] 画面で [カスタマイズ] を選択してカスタム インストールを実行します。 ![カスタム インストール](./media/active-directory-aadconnect-get-started-custom-device-writeback/devicewriteback1.png)
2.	初めてではない場合は、[追加のタスク] ページで [同期オプションのカスタマイズ] を選択し、[次へ] をクリックします。 ![カスタム インストール](./media/active-directory-aadconnect-get-started-custom-device-writeback/devicewriteback2.png)
3.	[オプション機能] ページで、デバイスの書き戻しを変更できるようになります。Azure AD Connect の準備手順が完了していない場合は、[オプション機能] ページでデバイスの書き戻しの設定を変更できないことに注意してください。[デバイスの書き戻し] チェック ボックスをオンにして、[次へ] をクリックします。 ![デバイスの書き戻し](./media/active-directory-aadconnect-get-started-custom-device-writeback/devicewriteback3.png)
4.	[書き戻し] ページでは、指定したドメインが既定の [デバイスの書き戻しフォレスト] として表示されます。 ![カスタム インストール](./media/active-directory-aadconnect-get-started-custom-device-writeback/devicewriteback4.png)
5.	その他の構成は変更しないで、ウィザードのインストールを完了します。必要に応じて、「[Azure AD Connect のカスタム インストール](active-directory-aadconnect-get-started-custom.md)」をご覧ください。



## 条件付きアクセスを有効にする
このシナリオを有効にする詳細な手順については、「[Azure Active Directory Device Registration を使用したオンプレミスの条件付きアクセスの設定](https://msdn.microsoft.com/library/azure/dn788908.aspx)」をご覧ください。

## デバイスが Active Directory に同期されていることを確認する
デバイスの書き戻しは正常に動作するようになっています。デバイス オブジェクトを AD に書き戻すには、最大 3 時間かかる可能性があります。デバイスが正しく同期されていることを確認するには、同期規則が完了した後で次のようにします。

1.	Active Directory 管理センターを起動します。
2.	フェデレーションされているドメイン内の RegisteredDevices を展開します。 ![カスタム インストール](./media/active-directory-aadconnect-get-started-custom-device-writeback/devicewriteback5.png)
3.	現在登録されているデバイスが一覧表示されます。

![カスタム インストール](./media/active-directory-aadconnect-get-started-custom-device-writeback/devicewriteback6.png)

## 追加情報
- [条件付きアクセス ポリシーを使用したリスクの管理](active-directory-conditional-access.md)
- [Azure Active Directory Device Registration を使用したオンプレミスの条件付きアクセスの設定](https://msdn.microsoft.com/library/azure/dn788908.aspx)

## 次のステップ
「[オンプレミス ID と Azure Active Directory の統合](active-directory-aadconnect.md)」をご覧ください。

<!---HONumber=Oct15_HO3-->