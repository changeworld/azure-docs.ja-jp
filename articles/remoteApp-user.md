<properties 
    pageTitle="RemoteApp にユーザーを追加する" 
    description="RemoteApp でユーザーを追加する方法を説明します。" 
    services="remoteapp" 
    solutions="" 
	documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="04/06/2015" 
    ms.author="elizapo" />

# RemoteApp にユーザーを追加する方法

ユーザーが RemoteApp のアプリを表示したり使用するには、アクセス権を付与する必要があります。これは簡単です。**[ユーザー アクセス]** タブで、このサービスへのアクセス権を付与するユーザーのアカウント情報を入力します。

どのようなアカウント情報が必要ですか。 作成したコレクション (クラウドまたはハイブリッド) のタイプとそのコレクションで Office 365 ProPlus を使用しているかどうかによって異なります。

## サポートされているユーザー ID

Azure RemoteApp には、クラウドとハイブリッドという 2 つのデプロイメント方法があります。それぞれは、アプリケーションへのユーザー アクセスに異なる ID の使用をサポートします。

RemoteApp のハイブリッド コレクションでは、内部設置型 Active Directory ドメイン インフラストラクチャと Azure Active Directory テナントをディレクトリ統合でセットアップする必要があります (および必要に応じてシングル サインオン)。さらに、内部設置型ディレクトリに Active Directory オブジェクトを作成する必要があります。

RemoteApp のクラウド コレクションの場合、Azure Active Directory サポート ID を持つすべてのユーザーに、Microsoft アカウントを組み込むための RemoteApp へのユーザー アクセス権を付与できます。次の表を参照してください。

Office 365 ユーザーは、Azure Active Directory のユーザーです。そのユーザーに Azure Active Directory のハイブリッドのディレクトリ同期アカウントがあれば、RemoteApp ハイブリッド デプロイメントでのユーザー アクセス権を付与できます。

次の表は、コレクションでサポートされる ID と Active Directory の要件を確認するためのクイック リファレンスとして使用できます。

|ユーザー アカウント |クラウド |ハイブリッド|
|--------------|--------|------|
|Microsoft アカウント| 	あり|	いいえ|
|Azure Active Directory (Azure AD)| | |	
|Azure AD クラウドのみ |あり |いいえ |
|パスワード同期がある ADsync |あり |あり |
|パスワード同期がない ADsync|	あり |いいえ |
|AD FS がある ADsync |あり |あり |
|サード パーティの Azure サポート ID プロバイダー (Ping など) |あり |いいえ|	
|Multi-Factor Authentication |あり |あり |

RemoteApp を使用するために Active Directory を構成する方法の詳細については、[ここ](remoteapp-ad.md)を参照してください。


**注:** Azure Active Directory ユーザーは、サブスクリプションに関連付けられているテナントに属している必要があります(サブスクリプションは、ポータルの **[設定]** タブで表示および変更できます。詳細については、「[Change the Azure Active Directory tenant used by RemoteApp](remoteapp-changetenant.md)」を参照してください)。

## Office 365 ProPlus ユーザー アカウントの情報
コレクションに、Office 365 ProPlus のテンプレート イメージを使用しているか*または* Office 365 を使用するカスタム イメージを作成した場合は、サブスクリプションの既定のドメイン用の Office 365 サブスクリプションを持つ Azure Active Directory のユーザーのみを追加できます。詳細については、「[Azure RemoteApp で Office 365 を使用する](remoteapp-o365.md)」を参照してください。

<!--HONumber=54-->