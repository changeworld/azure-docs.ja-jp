<properties title="Add a user in RemoteApp" pageTitle="RemoteApp にユーザーを追加する" description="RemoteApp 内のユーザーを追加する方法をについてください。" metaKeywords="" services="" solutions="" documentationCenter="" authors="elizapo"  />

<tags ms.service="remoteapp" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/12/2014" ms.author="elizapo" />

#RemoteApp にユーザーを追加する方法
 
ユーザーが RemoteApp のアプリを表示したり使用するには、アクセス権を付与する必要があります。これは簡単です:**[ユーザー アクセス]** タブで、このサービスへのアクセス権を付与するユーザーのアカウント情報を入力してください。

どのようなアカウント情報が必要ですか。作成したコレクション (クラウドまたはハイブリッド) のタイプとそのコレクションで Office 365 ProPlus を使用しているかどうかによって異なります。

##クラウドのユーザー アカウント情報とハイブリッドのユーザー アカウント情報の比較
クラウド コレクションは、Microsoft アカウントとディレクトリ同期された Azure Active Directory 作業アカウント (Office 365 アカウントも含まれます) をサポートしています。 

ハイブリッド コレクションは、Windows Server Active Directory の展開から同期されている (DirSync のようなツールを使用して) Azure Active Directory アカウントのみをサポートします。具体的には、パスワード同期オプションで同期されているか、または Active Directory フェデレーション サービス (AD FS) の構成されたフェデレーションのいずれかで同期されます。Azure AD の要件の詳細については、「[Configuring Active Directory for Azure RemoteApp (Azure RemoteApp Active Directory を構成する)](http://azure.microsoft.com/ja-jp/documentation/articles/remoteapp-ad/)」を参照してください。

**注:** Azure Active Directory ユーザーは、サブスクリプションに関連付けられているテナントからのものである必要があります (ポータルの **[設定]** タブでサブスクリプションを表示および変更できます。詳細については、「[Change the Azure Active Directory tenant used by RemoteApp (RemoteApp で使用される Azure Active Directory テナントの変更)](http://msdn.microsoft.com/ja-jp/3d6c4fd1-c981-4c57-9402-59fe31b11883)」を参照してください)。

##Office 365 ProPlus ユーザー アカウントの情報
コレクションに、Office 365 ProPlus のテンプレート イメージを使用しているか *または* Office 365 を使用するカスタム イメージを作成した場合は、サブスクリプションの既定のドメイン用の Office 365 サブスクリプションを持つ Azure Active Directory のユーザーのみを追加できます。詳細については、「[Using Office 365 with Azure RemoteApp (Azure Remoteapp で Office 365 を使用する)](http://azure.microsoft.com/ja-jp/documentation/articles/remoteapp-o365/)」を参照してください。

<!--HONumber=35.2-->
