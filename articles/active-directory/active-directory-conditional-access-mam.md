---
title: "Azure Active Directory でのモバイル アプリ管理を使用した条件付きアクセス | Microsoft Docs"
description: "Azure Active Directory でのモバイル アプリ管理を使用した条件付きアクセスのしくみについて説明します。"
services: active-directory
keywords: "アプリへの条件付きアクセス, Azure AD での条件付きアクセス, 企業リソースへの安全なアクセス, 条件付きアクセス ポリシー"
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/01/2017
ms.author: markvi
ms.reviewer: spunukol
ms.translationtype: HT
ms.sourcegitcommit: ce0189706a3493908422df948c4fe5329ea61a32
ms.openlocfilehash: c6bc39dc151c80ffe1306464da60a029e54cc6b1
ms.contentlocale: ja-jp
ms.lasthandoff: 09/05/2017

---
# <a name="conditional-access-with-mobile-app-management-in-azure-active-directory"></a>Azure Active Directory でのモバイル アプリ管理を使用した条件付きアクセス  

Azure ポータルの Azure Active Directory (Azure AD) のアプリに基づく条件付きアクセスと Intune アプリ保護ポリシーを組み合わせることで、Intune アプリ保護をサポートしているモバイル アプリへのクラウド アプリからのアクセスを制限できます。たとえば、Exchange Online から Outlook アプリへのアクセスを制限できます。 このサポートにより、Intune MDM による管理対象として登録されていないデバイスでも、会社のデータを保護できます。   

モバイル アプリ管理の条件付きアクセスをデバイス ベースの条件付きアクセス ポリシーなどの他のポリシーと組み合わせて、個人のデバイスと会社のデバイスの両方でデータを保護することができます。 

##<a name="before-you-begin"></a>開始する前に

このトピックは、次の事項を熟知していることを前提としています。

- [Azure Active Directory の条件付きアクセス](active-directory-conditional-access-azure-portal.md)の基本的な概念。

- [条件付きアクセス ポリシーの構成](active-directory-conditional-access-azure-portal-get-started.md)方法。


さらに、「[Azure Active Directory の条件付きアクセスのベスト プラクティス](active-directory-conditional-access-best-practices.md)」を確認できます。  



## <a name="prerequisites"></a>前提条件

1.  アプリに基づく条件付きアクセス ポリシーを作成する前に、Enterprise Mobility + Security または Azure Active Directory Premium サブスクリプションが必要であり、ユーザーが EMS または Azure AD のライセンスを取得している必要があります。 
2.  モバイル アプリ管理ポリシーを使用して新しい条件付きアクセスを作成する前に、シナリオと移行に関する考慮事項を確認する必要があります。

## <a name="supported-platforms"></a>サポートされるプラットフォーム

-   iOS

-   Android

## <a name="approved-client-applications"></a>承認されたクライアント アプリケーション 

- Microsoft Outlook

- Microsoft SharePoint

- Microsoft OneDrive

- Microsoft Teams

- Microsoft Word

- Microsoft Excel

- Microsoft PowerPoint


## <a name="exchange-online-policy"></a>Exchange Online ポリシー 

このシナリオは、承認されたアプリで Exchange Online にアクセスするためのモバイル アプリ管理ポリシーによる条件付きアクセスで構成されます。


### <a name="scenario-playbook"></a>シナリオのプレイブック

このシナリオでは、ユーザーを次のように想定しています。

- iOS または Android でネイティブ メール アプリケーションを使用して Exchange に接続する電子メールを作成する

- Outlook アプリを使用したアクセスのみを利用できることを示す電子メールを受信する

- リンクを使用してアプリケーションをダウンロードする

- Outlook アプリケーションを開き、Azure AD 資格情報を使用してサインインする

- 続行するには Authenticor (iOS) またはポータル サイト (Android) をインストールすることを求められる

- アプリケーションをインストールした後、Outlook アプリに戻って続行できる

- デバイスを登録することを求められる

- 電子メールにアクセスできる

すべての Intune アプリ保護ポリシーは、会社データへのアクセス時にアクティブになり、ユーザーにアプリケーションの再起動や追加 PIN の使用などを求めることができます (アプリケーションとプラットフォームがそのように構成されている場合)。

### <a name="configuration"></a>構成 

**手順 1 - Exchange Online 用の Azure AD 条件付きアクセス ポリシーを構成する**

この手順の条件付きアクセス ポリシーでは、次のコンポーネントを構成する必要があります。

![条件付きアクセス](./media/active-directory-conditional-access-mam/01.png)

1. 条件付きアクセス ポリシーの**名前**。

2. **ユーザーとグループ**: 各条件付きアクセス ポリシーには、少なくとも 1 名のユーザーまたは 1 つのグループが選択されている必要があります。

3. **クラウド アプリ:** クラウド アプリとして、**[Office 365 Exchange Online]** を選択する必要があります。

    ![条件付きアクセス](./media/active-directory-conditional-access-mam/07.png)

4. **条件:** **条件**として、**デバイス プラットフォーム**と**クライアント アプリ**を構成する必要があります。

    a. **デバイス プラットフォーム**として、**[Android]** と **[iOS]** を選択します。

    ![条件付きアクセス](./media/active-directory-conditional-access-mam/03.png)

    b. **クライアント アプリ**として、**[モバイル アプリとデスクトップ アプリ]** を選択します。

    ![条件付きアクセス](./media/active-directory-conditional-access-mam/04.png)

5. **アクセス コントロール**として、**[承認されたクライアント アプリが必要です (プレビュー)]** を選択する必要があります。

    ![条件付きアクセス](./media/active-directory-conditional-access-mam/05.png)
 

**手順 2 - Active Sync (EAS) を使用する Exchange Online 用の Azure AD 条件付きアクセス ポリシーを構成する**

この手順の条件付きアクセス ポリシーでは、次のコンポーネントを構成する必要があります。

![条件付きアクセス](./media/active-directory-conditional-access-mam/06.png)

1. 条件付きアクセス ポリシーの**名前**。

2. **ユーザーとグループ**: 各条件付きアクセス ポリシーには、少なくとも 1 名のユーザーまたは 1 つのグループが選択されている必要があります。


3. **クラウド アプリ:** クラウド アプリとして、**[Office 365 Exchange Online]** を選択する必要があります。

    ![条件付きアクセス](./media/active-directory-conditional-access-mam/07.png)

4. **条件:** **条件**として、**クライアント アプリ**を構成する必要があります。 

    a. **クライアント アプリ**として、**[Exchange Active Sync]** を選択します。

    ![条件付きアクセス](./media/active-directory-conditional-access-mam/08.png)

    b. **アクセス コントロール**として、**[承認されたクライアント アプリが必要です (プレビュー)]** を選択する必要があります。

    ![条件付きアクセス](./media/active-directory-conditional-access-mam/05.png)


**手順 3 - iOS および Android クライアント アプリケーション用の Intune アプリ保護ポリシーを構成する**


![条件付きアクセス](./media/active-directory-conditional-access-mam/09.png)

詳細については、「[Microsoft Intune でアプリとデータを保護する](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune)」を参照してください。


## <a name="exchange-online-and-sharepoint-online-policy"></a>Exchange Online と SharePoint Online のポリシー

このシナリオは、承認されたアプリで Exchange Online と SharePoint Online にアクセスするためのモバイル アプリ管理ポリシーによる条件付きアクセスで構成されます。

### <a name="scenario-playbook"></a>シナリオのプレイブック

このシナリオでは、ユーザーを次のように想定しています。

- SharePoint アプリを使用して、会社のサイトに接続し、表示もしようとしている

- Outlook アプリの資格情報と同じ資格情報でサインインしようとしている

- リソースの再登録なしでリソースにアクセスできる


### <a name="configuration"></a>構成

**手順 1 - Exchange Online と SharePoint Online 用の Azure AD 条件付きアクセス ポリシーを構成する**

この手順の条件付きアクセス ポリシーでは、次のコンポーネントを構成する必要があります。

![条件付きアクセス](./media/active-directory-conditional-access-mam/71.png)

1. 条件付きアクセス ポリシーの**名前**。

2. **ユーザーとグループ**: 各条件付きアクセス ポリシーには、少なくとも 1 名のユーザーまたは 1 つのグループが選択されている必要があります。


3. **クラウド アプリ:** クラウド アプリとして、**[Office 365 Exchange Online]** と **[Office 365 SharePoint Online]** を選択する必要があります。 

    ![条件付きアクセス](./media/active-directory-conditional-access-mam/02.png)

4. **条件:** **条件**として、**デバイス プラットフォーム**と**クライアント アプリ**を構成する必要があります。

    a. **デバイス プラットフォーム**として、**[Android]** と **[iOS]** を選択します。

    ![条件付きアクセス](./media/active-directory-conditional-access-mam/03.png)

    b. **クライアント アプリ**として、**[モバイル アプリとデスクトップ アプリ]** を選択します。

    ![条件付きアクセス](./media/active-directory-conditional-access-mam/04.png)

5. **アクセス コントロール**として、**[承認されたクライアント アプリが必要です (プレビュー)]** を選択する必要があります。

    ![条件付きアクセス](./media/active-directory-conditional-access-mam/05.png)




**手順 2 - Active Sync (EAS) を使用する Exchange Online 用の Azure AD 条件付きアクセス ポリシーを構成する**

この手順の条件付きアクセス ポリシーでは、次のコンポーネントを構成する必要があります。

![条件付きアクセス](./media/active-directory-conditional-access-mam/06.png)

1. 条件付きアクセス ポリシーの**名前**。

2. **ユーザーとグループ**: 各条件付きアクセス ポリシーには、少なくとも 1 名のユーザーまたは 1 つのグループが選択されている必要があります。

3. **クラウド アプリ:** クラウド アプリとして、**[Office 365 Exchange Online]** を選択する必要があります。 オンライン 

    ![条件付きアクセス](./media/active-directory-conditional-access-mam/07.png)

4. **条件:** **条件**として、**クライアント アプリ**を構成する必要があります。

    a. **クライアント アプリ**として、**[Exchange Active Sync]** を選択します。

    ![条件付きアクセス](./media/active-directory-conditional-access-mam/08.png)

    b. **アクセス コントロール**として、**[承認されたクライアント アプリが必要です (プレビュー)]** を選択する必要があります。

    ![条件付きアクセス](./media/active-directory-conditional-access-mam/05.png)




**手順 3 - iOS および Android クライアント アプリケーション用の Intune アプリ保護ポリシーを構成する**


![条件付きアクセス](./media/active-directory-conditional-access-mam/09.png)

詳細については、「[Microsoft Intune でアプリとデータを保護する](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune)」を参照してください。


## <a name="mobile-application-management-or-compliant-device-policy-for-exchange-online-and-sharepoint-online"></a>Exchange Online と SharePoint Online 用のモバイル アプリケーション管理または準拠デバイス ポリシー

このシナリオは、承認されたアプリで Exchange Online にアクセスするためのモバイル アプリ管理ポリシーまたは準拠デバイス ポリシーによる条件付きアクセスで構成されます。


### <a name="scenario-playbook"></a>シナリオのプレイブック

このシナリオでは、ユーザーを次のように想定しています。
 
- 一部のユーザーは既に登録されている (会社のデバイスと共に、またはデバイスなしで)

- アプリで保護されたアプリケーションを使用して Azure AD に登録していないユーザーがリソースにアクセスするには、デバイスを登録する必要がある

- アプリで保護されたアプリケーションを使用して登録しているユーザーは、デバイスを再登録する必要はない


### <a name="configuration"></a>構成

**手順 1 - Exchange Online と SharePoint Online 用の Azure AD 条件付きアクセス ポリシーを構成する**

この手順の条件付きアクセス ポリシーでは、次のコンポーネントを構成する必要があります。

![条件付きアクセス](./media/active-directory-conditional-access-mam/62.png)

1. 条件付きアクセス ポリシーの**名前**。

2. **ユーザーとグループ**: 各条件付きアクセス ポリシーには、少なくとも 1 名のユーザーまたは 1 つのグループが選択されている必要があります。

3. **クラウド アプリ:** クラウド アプリとして、**[Office 365 Exchange Online]** と **[Office 365 SharePoint Online]** を選択する必要があります。 

     ![条件付きアクセス](./media/active-directory-conditional-access-mam/02.png)

4. **条件:** **条件**として、**デバイス プラットフォーム**と**クライアント アプリ**を構成する必要があります。 
 
    a. **デバイス プラットフォーム**として、**[Android]** と **[iOS]** を選択します。

    ![条件付きアクセス](./media/active-directory-conditional-access-mam/03.png)

    b. **クライアント アプリ**として、**[モバイル アプリとデスクトップ アプリ]** を選択します。

    ![条件付きアクセス](./media/active-directory-conditional-access-mam/04.png)

5. **アクセスコントロール**として、以下を選択する必要があります。

    - **デバイスは準拠としてマーク済みである必要があります**

    - **承認されたクライアント アプリが必要です (プレビュー)**

    - **選択したコントロールのいずれかが必要です**   
 
    ![条件付きアクセス](./media/active-directory-conditional-access-mam/11.png)



**手順 2 - Active Sync (EAS) を使用する Exchange Online 用の Azure AD 条件付きアクセス ポリシーを構成する**

この手順の条件付きアクセス ポリシーでは、次のコンポーネントを構成する必要があります。

![条件付きアクセス](./media/active-directory-conditional-access-mam/61.png)

1. 条件付きアクセス ポリシーの**名前**。

2. **ユーザーとグループ**: 各条件付きアクセス ポリシーには、少なくとも 1 名のユーザーまたは 1 つのグループが選択されている必要があります。

3. **クラウド アプリ:** クラウド アプリとして、**[Office 365 Exchange Online]** を選択する必要があります。 

    ![条件付きアクセス](./media/active-directory-conditional-access-mam/07.png)

4. **条件:** **条件**として、**クライアント アプリ**を構成する必要があります。 

    **クライアント アプリ**として、**[Exchange Active Sync]** を選択します。

    ![条件付きアクセス](./media/active-directory-conditional-access-mam/08.png)

5. **アクセス コントロール**として、**[承認されたクライアント アプリが必要です (プレビュー)]** を選択する必要があります。
 
    ![条件付きアクセス](./media/active-directory-conditional-access-mam/11.png)




**手順 3 - iOS および Android クライアント アプリケーション用の Intune アプリ保護ポリシーを構成する**


![条件付きアクセス](./media/active-directory-conditional-access-mam/09.png)

詳細については、「[Microsoft Intune でアプリとデータを保護する](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune)」を参照してください。





## <a name="mobile-application-management-and-compliant-device-policy-for-exchange-online-and-sharepoint-online"></a>Exchange Online と SharePoint Online 用のモバイル アプリケーション管理および準拠デバイス ポリシー

このシナリオは、承認されたアプリで Exchange Online にアクセスするためのモバイル アプリ管理ポリシーおよび対応デバイス ポリシーによる条件付きアクセスで構成されます。

### <a name="scenario-playbook"></a>シナリオのプレイブック

このシナリオでは、ユーザーを次のように想定しています。
 
-   iOS または Android でネイティブ メール アプリケーションを使用して電子メールを作成して Exchange に接続する
-   アクセスするにはデバイスを登録する必要があることを示す電子メールを受信する
-   ポータル サイトをダウンロードしてサインインする
-   メールを確認すると、Outlook アプリを使用することを求められる
-   Outlook アプリをダウンロードする
-   Outlook アプリを開き、登録で使用される資格情報を入力する
-   電子メールにアクセスできる

すべての Intune アプリ保護ポリシーは、会社データへのアクセス時にアクティブになり、ユーザーにアプリケーションの再起動や追加 PIN の使用などを求めることができます (アプリケーションとプラットフォームがそのように構成されている場合)。


### <a name="configuration"></a>構成

**手順 1 - Exchange Online と SharePoint Online 用の Azure AD 条件付きアクセス ポリシーを構成する**

この手順の条件付きアクセス ポリシーでは、次のコンポーネントを構成する必要があります。

![条件付きアクセス](./media/active-directory-conditional-access-mam/62.png)

1. 条件付きアクセス ポリシーの**名前**。

2. **ユーザーとグループ**: 各条件付きアクセス ポリシーには、少なくとも 1 名のユーザーまたは 1 つのグループが選択されている必要があります。

3. **クラウド アプリ:** クラウド アプリとして、**[Office 365 Exchange Online]** と **[Office 365 SharePoint Online]** を選択する必要があります。 

     ![条件付きアクセス](./media/active-directory-conditional-access-mam/02.png)

4. **条件:** **条件**として、**デバイス プラットフォーム**と**クライアント アプリ**を構成する必要があります。 
 
    a. **デバイス プラットフォーム**として、**[Android]** と **[iOS]** を選択します。

    ![条件付きアクセス](./media/active-directory-conditional-access-mam/03.png)

    b. **クライアント アプリ**として、**[モバイル アプリとデスクトップ アプリ]** を選択します。

    ![条件付きアクセス](./media/active-directory-conditional-access-mam/04.png)

5. **アクセスコントロール**として、以下を選択する必要があります。

    - **デバイスは準拠としてマーク済みである必要があります**

    - **承認されたクライアント アプリが必要です (プレビュー)**

    - **選択したコントロールのいずれかが必要です**   
 
    ![条件付きアクセス](./media/active-directory-conditional-access-mam/11.png)



**手順 2 - Active Sync (EAS) を使用する Exchange Online 用の Azure AD 条件付きアクセス ポリシーを構成する**

この手順の条件付きアクセス ポリシーでは、次のコンポーネントを構成する必要があります。

![条件付きアクセス](./media/active-directory-conditional-access-mam/61.png)

1. 条件付きアクセス ポリシーの**名前**。

2. **ユーザーとグループ**: 各条件付きアクセス ポリシーには、少なくとも 1 名のユーザーまたは 1 つのグループが選択されている必要があります。

3. **クラウド アプリ:** クラウド アプリとして、**[Office 365 Exchange Online]** を選択する必要があります。 

    ![条件付きアクセス](./media/active-directory-conditional-access-mam/07.png)

4. **条件:** **条件**として、**クライアント アプリ**を構成する必要があります。 

    **クライアント アプリ**として、**[Exchange Active Sync]** を選択します。

    ![条件付きアクセス](./media/active-directory-conditional-access-mam/08.png)

5. **アクセスコントロール**として、以下を選択する必要があります。

    - **デバイスは準拠としてマーク済みである必要があります**

    - **承認されたクライアント アプリが必要です (プレビュー)**

    - **選択したコントロールすべてが必要です**   
 
    ![条件付きアクセス](./media/active-directory-conditional-access-mam/64.png)




**手順 3 - iOS および Android クライアント アプリケーション用の Intune アプリ保護ポリシーを構成する**


![条件付きアクセス](./media/active-directory-conditional-access-mam/09.png)

詳細については、「[Microsoft Intune でアプリとデータを保護する](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune)」を参照してください。



## <a name="migration-considerations"></a>移行に関する考慮事項

Azure クラシック ポータルで構成したポリシーがある場合は、以下の理由により、Azure ポータルに移行する必要があります。


- Azure クラシック ポータルのポリシーと Azure ポータルのポリシーが適用されるユーザーは、両方のポリシーの要件を満たす必要がある 

- 既存のポリシーを移行しない場合、アクセスを許可するポリシーを実装できない


## <a name="migration-from-the-azure-classic-portal"></a>Azure クラシック ポータルからの移行

このシナリオでは: 

- [Azure クラシック ポータル](https://manage.windowsazure.com)で、以下を構成済みです。

    - SharePoint Online

    ![条件付きアクセス](./media/active-directory-conditional-access-mam/14.png)

    - デバイス ベースの条件付きアクセス ポリシー

    ![条件付きアクセス](./media/active-directory-conditional-access-mam/15.png)

- Azure ポータルで、モバイル アプリケーション管理の条件付きアクセス ポリシーを構成します。 
 

### <a name="configuration"></a>構成 

- デバイス ベースの条件付きアクセス ポリシーを見直す

- Azure ポータルに移行する 

- モバイル アプリケーション管理の条件付きアクセス ポリシーを追加する


## <a name="migrating-from-intune"></a>Intune からの移行 

このシナリオでは:

- [Intune](https://portal.azure.com/#blade/Microsoft_Intune/SummaryBlade ) で、Exchange Online または SharePoint Online 用のモバイル アプリケーション管理の条件付きアクセス ポリシーを構成済みです。

    ![条件付きアクセス](./media/active-directory-conditional-access-mam/15.png)

- Azure ポータルで、モバイル アプリケーション管理の条件付きアクセス ポリシーの使用に移行します。


### <a name="configuration"></a>構成 
 
- デバイス ベースの条件付きアクセス ポリシーを見直す

- Azure ポータルに移行する 

- Intune で、Exchange Online または SharePoint Online 用に構成したモバイル アプリケーション管理の条件付きアクセス ポリシーを見直す

- デバイス ベースの制御に加え、**承認されたアプリケーションを要求する**コントロールを追加する 
 

## <a name="migrating-from-the-azure-classic-portal-and-intune"></a>Azure クラシック ポータルと Intune からの移行

このシナリオでは:

- 以下を構成済みです。

    - **Azure クラシック ポータル:** デバイス ベースの条件付きアクセス ポリシー 

    - **Intune:** モバイル アプリケーション管理の条件付きアクセス ポリシー 
    
- Azure ポータルで、モバイル アプリケーション管理の条件付きアクセス ポリシーを使用するように両方のポリシーを移行します。


### <a name="configuration"></a>構成

- デバイス ベースの条件付きアクセス ポリシーを見直す

- Azure ポータルに移行する 

- Intune で、Exchange Online または SharePoint Online 用に構成したモバイル アプリケーション管理の条件付きアクセス ポリシーを見直す

- デバイス ベースの制御に加え、**承認されたアプリケーションを要求する**コントロールを追加する 




## <a name="next-steps"></a>次のステップ

条件付きアクセスポリシーの構成方法については、[Azure Active Directory での条件付きアクセスの使用](active-directory-conditional-access-azure-portal-get-started.md)に関する記事を参照してください。

環境に適用する条件付きアクセス ポリシーを構成する準備ができたら、「[Azure Active Directory の条件付きアクセスのベスト プラクティス](active-directory-conditional-access-best-practices.md)」を参照してください。 
