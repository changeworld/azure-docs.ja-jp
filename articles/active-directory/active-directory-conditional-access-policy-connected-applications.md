<properties
	pageTitle="Azure Active Directory に接続されたアプリケーションのアクセスを制御する Azure Active Directory デバイス ベースの条件付きアクセス ポリシーを設定する方法"
	description="IT 管理者が Azure AD に接続されたアプリケーション用のデバイス ベースの条件付きアクセス ポリシーを設定する方法について説明します。"
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/09/2016"
	ms.author="markvi"/>


# Azure Active Directory に接続されたアプリケーションのアクセスを制御する Azure Active Directory デバイス ベースの条件付きアクセス ポリシーを設定する方法 


Azure Active Directory デバイス ベースの条件付きアクセスとは、組織のリソースを次から保護するための機能です。

- 不明または管理されていないデバイスからのアクセス
- 組織で定義されているセキュリティ ポリシーを満たしていないデバイス

条件付きアクセスの詳細については、「[Azure Active Directory の条件付きアクセス](active-directory-conditional-access.md)」を参照してください。

デバイス ベースの条件付きアクセス ポリシーは、次を保護するように設定できます。

- 組織のサイトとドキュメントを保護する Office 365 SharePoint Online。

- 組織の電子メールを保護する Office 365 Exchange Online。

- 認証用に Azure AD に接続されている SaaS アプリケーション。

- Azure AD アプリケーション プロキシを通じて発行されたオンプレミスのアプリケーション。


Microsoft Azure 管理ポータルでは、ディレクトリ内の特定のアプリケーションに移動してこのポリシーを設定できます。

 
  ![アプリケーション](./media/active-directory-conditional-access-policy-connected-applications/01.png "アプリケーション")


アプリケーションを選択し、**[構成]** タブをクリックして条件付きアクセス ポリシーを設定します。


  ![デバイス ベースのアクセス規則](./media/active-directory-conditional-access-policy-connected-applications/02.png "デバイス ベースのアクセス規則")


 

デバイス ベースの条件付きアクセス ポリシーを有効にするには、**[デバイス ベースのアクセス規則]** セクションの **[アクセス規則を有効にする]** を **[オン]** にします。

このポリシーは、次の 3 つのコンポーネントで構成されます。

1. **[適用対象]** - アプリケーションにアクセスする際にこのポリシーが適用されるユーザーの範囲。

2. **[デバイス ルール]** - デバイスがアプリケーションにアクセスするために満たす必要がある条件。

3. **[アプリケーション強制]** - ポリシーが評価される対象のクライアント アプリケーション (ネイティブ対ブラウザー)。

  ![デバイス ベースのアクセス規則](./media/active-directory-conditional-access-policy-connected-applications/03.png "デバイス ベースのアクセス規則")
 

## ポリシーが適用されるユーザーを選択する 

**[適用対象]** セクションで、このポリシーを適用するユーザーの範囲を選択できます。

範囲については、2 つのオプションがあります。

- **[すべてのユーザー]** - アプリケーションにアクセスしているすべてのユーザー

- **[グループ]** - 範囲をグループのメンバーに限定する場合

![適用対象](./media/active-directory-conditional-access-policy-connected-applications/11.png "適用対象")


**[除外]** を選択することで、アプリケーションにアクセスしている間ユーザーをこのポリシーの適用対象外にできます。これは、アプリケーションへのアクセスを特定のユーザーに対して一時的に有効にする必要がある場合に便利です。一部のユーザーが条件付きアクセスの準備ができていないデバイス (まだ登録されていない、コンプライアンス対象外など) を持っている場合などにこのオプションを選択します。
 

## デバイスが満たす必要がある条件を選択する 

**[デバイス ルール]** では、アプリケーションへのアクセスを有効にするためのデバイスの条件を設定します。

デバイス ベースの条件付きアクセスでは、次のデバイスがサポートされています。

- Windows 10 Anniversary Update、Windows 7 および Windows 8.1

- Windows Server 2016、Windows Server 2012 R2、Windows Server 2012 および Windows Server 2008 R2

- iOS デバイス (iPad、iPhone)

- Android デバイス

Mac のサポートは近日対応予定です。

  ![デバイス](./media/active-directory-conditional-access-policy-connected-applications/04.png "アプリケーション")



 >[AZURE.NOTE] Domain Join と Azure AD Join の違いについては、「[職場での Windows 10 デバイスの使用](active-directory-azureadjoin-windows10-devices.md)」を参照してください。


デバイス ルールには 2 つのオプションがあります。

- **[すべてのデバイスが準拠している必要があります]** - アプリケーションにアクセスしているすべてのデバイス プラットフォームが準拠している必要があります。デバイス ベースの条件付きアクセスをサポートしていないプラットフォームの場合、デバイスのアクセスが拒否されます。

- **[選択したデバイスだけが準拠している必要があります]** - 選択したデバイス プラットフォームのみが準拠している必要があります。アプリケーションにアクセスしている選択されていないプラットフォームやその他のプラットフォームはアクセスが許可されます。

  ![アプリケーション](./media/active-directory-conditional-access-policy-connected-applications/05.png "アプリケーション")



Azure AD 参加デバイスは、Azure AD に統合される Microsoft Intune またはサード パーティのモバイル デバイス管理 (MDM) システムで**準拠**としてマークされている場合に準拠します。

ドメイン参加デバイスは、次の 2 つの方法のいずれかで準拠します。

- Azure AD に登録されている場合。ドメイン参加であるという事実により数多くの組織がそれらを信頼されたデバイスとして扱います。

- System Center Configuration Manager 2016 により Azure AD で '準拠' としてマークされている場合。

 ![デバイス ルール](./media/active-directory-conditional-access-policy-connected-applications/06.png "デバイス ルール")
 

Windows の個人のデバイスは、Azure AD に統合される Microsoft Intune またはサード パーティのモバイル デバイス管理 (MDM) システムで**準拠**としてマークされている場合に準拠します。

Windows 以外のデバイスは、Microsoft Intune によりディレクトリで**準拠**とマークされている場合に準拠します。

 >[AZURE.NOTE] 管理システムによるデバイスの準拠について Azure AD を設定する方法の詳細については、「[Azure Active Directory の条件付きアクセス](active-directory-conditional-access.md)」を参照してください。


特定のデバイス プラットフォームを選択する際に、Android、iOS、Windows Mobile (Windows 8.1 の携帯電話とタブレット)、Windows (Windows 10 のすべてのデバイスを含むすべての Windows デバイス) から 1 つまたは複数のオプションを選択できます。このオプションにより、ポリシーの評価は選択したプラットフォームでのみ行われます。選択したデバイス以外からアクセスが試行されると、デバイス ポリシーの評価は行われず、ユーザーが許可されていればそのデバイスも許可されます。

![デバイス ルール](./media/active-directory-conditional-access-policy-connected-applications/07.png "デバイス ルール")
  

## ポリシーが評価されるクライアント アプリケーションの種類を選択する 

**[アプリケーション強制]** セクションで、ポリシーを評価する対象のアプリケーションの種類を設定します。


アプリケーションについては、2 つのオプションがあります。

- ブラウザーとネイティブ アプリケーション用
- ネイティブ アプリケーション専用


![アプリケーション](./media/active-directory-conditional-access-policy-connected-applications/08.png "アプリケーション")


**[ブラウザーとネイティブ アプリケーション用]** を選択すると、次からのアプリケーションにアクセスについてポリシーが適用されます。

- ブラウザー (Windows 10 の Edge、iOS の Safari など)
- Active Directory 認証ライブラリ (ADAL) (任意のプラットフォーム) または Web Account Manager (WAM) API (Windows 10) を使用するアプリケーション

>[AZURE.NOTE] ブラウザーのサポートおよびデバイス ベースの条件付きアクセスで保護されたアプリケーションにアクセスするエンド ユーザーのその他の考慮事項の詳細については、「[Azure Active Directory の条件付きアクセス](active-directory-conditional-access.md)」を参照してください。

 

## Exchange ActiveSync ベースのアプリケーションからの電子メールのアクセスを保護する 

Office 365 Exchange Online のアプリケーションには、**Exchange Activesync** と呼ばれる追加のセクションがあります。このセクションでは、Exchange ActiveSync ベースのメール アプリケーションへの電子メールのアクセスをブロックできます。

![アプリケーション](./media/active-directory-conditional-access-policy-connected-applications/09.png "アプリケーション")
 
![アプリケーション](./media/active-directory-conditional-access-policy-connected-applications/10.png "アプリケーション")

 
## 関連トピック

- [Azure Active Directory の条件付きアクセス](active-directory-conditional-access.md)

<!---HONumber=AcomDC_0810_2016-->