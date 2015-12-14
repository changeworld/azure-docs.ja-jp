<properties
	pageTitle="# Windows 7 ドメイン参加済みデバイスの自動デバイス登録の構成 | Microsoft Azure"
	description="Windows 7 ドメイン参加済みデバイスを自動的に Azure AD に登録するように構成する手順と、Windows 7 ドメイン参加済みデバイスに System Center Configuration Manager などのソフトウェア配布を使用してデバイスの登録ソフトウェア パッケージをデプロイする手順。"
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
	ms.date="11/24/2015"
	ms.author="femila"/>

# Windows 7 ドメイン参加済みデバイスの自動デバイス登録の構成

IT 管理者は、Windows 7 ドメイン参加済みデバイスを、自動的に Azure AD に登録されるように構成できます。そのためには、Windows 7 ドメイン参加済みデバイスに、System Center Configuration Manager などのソフトウェア配布を使用してデバイスの登録ソフトウェア パッケージをデプロイする必要があります。「Windows ドメイン参加デバイスの Azure Active Directory を使用した自動デバイス登録」の一覧にある前提条件が満たされていることを確認してください。

##Windows 7 ドメイン参加済みデバイスへのデバイスの登録ソフトウェア パッケージのインストール

Windows 7 のデバイス登録は、[ダウンロード可能な MSI パッケージ](https://connect.microsoft.com/site1164)として利用できます。このパッケージは、Active Directory ドメインに参加している Windows 7 コンピューターにインストールする必要があります。System Center Configuration Manager などのソフトウェア配布システムを使用して、パッケージをデプロイしてください。MSI パッケージは、/quiet パラメーターを使用する、標準のサイレント インストール オプションをサポートしています。このソフトウェア パッケージは、[Microsoft Connect Web サイト](https://connect.microsoft.com/site1164)からダウンロードできます。ここで、Windows 7 用の社内参加を選択し、ダウンロードします。

![](./media/active-directory-conditional-access/device-registration-process-windows7.gif)

## Azure Active Directory での社内参加
Windows 7 ドメイン参加済みデバイスのデバイス登録にはユーザー インターフェイスが含まれず、使用する必要もありません。コンピューターにインストールされると、コンピューターにログインするすべてのドメイン ユーザーが Azure AD のデバイス オブジェクトを使用して自動的かつ暗黙的に登録されます。物理デバイスの登録済みのユーザーごとに、Azure AD で 1 つのデバイス オブジェクトが存在します。

インストーラーは、スケジュールされたタスクをシステムに作成します。このタスクは、ユーザーのコンテキストで実行され、ユーザーのサインインによってトリガーされます。このタスクにより、ユーザー サインインの完了後に、ユーザーとデバイスがサイレント モードで Azure AD に登録されます。スケジュールされたタスクは、タスク スケジューラ ライブラリで、**[Microsoft]** の **[社内参加]** にあります。タスクが実行され、コンピューターにサインインするすべての Active Directory ユーザーが登録されます。次の図は、自動デバイス登録のステップ バイ ステップの手順を示しています。

![](./media/active-directory-conditional-access/automatic-device-registration-windows7.png)

1. ユーザー (インフォメーション ワーカー) が、Active Directory ドメインの資格情報を使用して、Windows 7 クライアント コンピューターにログオンします。
1. 社内参加のスケジュールされたタスクが実行されます。
1. ユーザーは、Windows 統合認証を使用して AD FS で暗黙的に認証されます。
1. Windows 7 PC が Azure AD のユーザーに登録されます。
1. Azure AD で、デバイス オブジェクトと証明書が作成されます。オブジェクトは user@device を表します。
1. 社内参加の証明書が、コンピューターに格納されます。

## Windows 7 ドメイン参加済みデバイスの登録解除

ドメインに参加した Windows 7 デバイスの登録を解除するには、次の手順に従います。System Center Configuration Manager などのソフトウェア配布システムを使用して、Windows 7 ドメイン参加済みデバイスから社内参加のソフトウェア パッケージをアンインストールします。

Windows 7 コンピューターでコマンド プロンプトを開き、次のコマンドを実行して、デバイスの登録を解除します。
    
    %ProgramFiles%\Microsoft Workplace Join\AutoWorkplace.exe /leave

>[AZURE.NOTE]このコマンドは、コンピューターにサインインしている各ドメイン ユーザーのコンテキストで実行する必要があります。Windows 7 ドメイン参加済みデバイス用のイベント ビューアーとエラー

Windows 7 コンピューターの Windows イベント ログには、社内参加に関連するメッセージが表示されます。社内参加の成功したイベントと失敗したイベントの両方のメッセージが表示されます。イベント ログは、イベント ビューアーの [アプリケーションとサービス ログ] の [Microsoft - 社内参加] にあります。

## 関連トピック

- [Azure Active Directory Device Registration の概要](active-directory-conditional-access-device-registration-overview.md)
- [Azure Active Directory への Windows ドメイン参加済みデバイスの自動デバイス登録](active-directory-conditional-access-automatic-device-registration.md)
- [Windows 8.1 ドメイン参加済みデバイスの自動デバイス登録の構成](active-directory-conditional-access-automatic-device-registration-windows8_1.md)

 

<!---HONumber=AcomDC_1203_2015-->