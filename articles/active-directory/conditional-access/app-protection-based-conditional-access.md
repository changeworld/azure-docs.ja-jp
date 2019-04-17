---
title: Azure Active Directory で条件付きアクセスを使用してクラウド アプリへのアクセスにアプリ保護ポリシーを要求する方法 | Microsoft Docs
description: Azure Active Directory で条件付きアクセスを使用してクラウド アプリへのアクセスにアプリ保護ポリシーを要求する方法について説明します。
services: active-directory
keywords: アプリへの条件付きアクセス, Azure AD での条件付きアクセス, 企業リソースへの安全なアクセス, 条件付きアクセス ポリシー
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.subservice: conditional-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 4/4/2019
ms.author: joflore
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: f695d50e251d0104cf9f0d38fe4489a0e66dfe15
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2019
ms.locfileid: "59287562"
---
# <a name="how-to-require-app-protection-policy-for-cloud-app-access-with-conditional-access-preview"></a>方法:条件付きアクセスを使用してクラウド アプリへのアクセスにアプリ保護ポリシーを要求する

従業員は個人的な作業と業務上の作業のどちらにもモバイル デバイスを使用します。 そこで、従業員の生産性を確保しながら、データの損失を防止する必要があります。 Azure Active Directory (Azure AD) の条件付きアクセスを使うと、クラウド アプリへのアクセスを最初にアプリ保護ポリシーを持つクライアント アプリに制限することで、会社のデータを保護することができます。

このトピックでは、データにアクセスする前にアプリ保護ポリシーを要求できる条件付きアクセス ポリシーを構成する方法について説明します。

## <a name="overview"></a>概要

[Azure AD の条件付きアクセス](overview.md)を使うと、承認されたユーザーがリソースにアクセスする方法を微調整できます。 たとえば、クラウド アプリへのアクセスを信頼済みデバイスのみに制限できます。

会社のデータの保護には、[Intune のアプリ保護ポリシー](https://docs.microsoft.com/intune/app-protection-policy)を利用できます。 Intune のアプリ保護ポリシーでは、モバイル デバイス管理 (MDM) ソリューションデバイスは必要ありません。このため、デバイスをデバイス管理ソリューションに登録しているかどうかに関係なく、会社のデータを保護することができます。

Azure Active Directory の条件付きアクセスを使うと、クラウド アプリへのアクセスを、アプリ保護ポリシーを受信するときに Intune が Azure AD に報告したクライアント アプリケーションに制限します。 たとえば、Exchange Online に対するアクセスを、Intune のアプリ保護ポリシーがある Outlook アプリのみに制限することができます。

条件付きアクセスに関する用語では、このようなクライアント アプリを**アプリ保護ポリシー**で保護されたポリシーと呼んでいます。  

![条件付きアクセス](./media/app-protection-based-conditional-access/05.png)

ポリシーで保護されたクライアント アプリの一覧については、[アプリの保護ポリシーの要件](technical-reference.md#approved-client-app-requirement)に関するセクションを参照してください。

アプリ保護ベースの条件付きアクセス ポリシーを[デバイス ベースの条件付きアクセス ポリシー](require-managed-devices.md)などの他のポリシーと組み合わせて、個人のデバイスと会社のデバイスの両方で柔軟にデータを保護することもできます。

## <a name="benefits-of-app-protection-based-conditional-access-requirement"></a>アプリ保護ベースの条件付きアクセスの要件の利点

マネージド デバイス向けの iOS および Android 用の Intune によって報告されているコンプライアンスと同様に、アプリ保護ポリシーが適用されているかが Intune から Azure AD に報告され、これが条件付きアクセス ポリシーでアクセス確認として使用されるようになりました。 この新しい条件付きアクセス ポリシーである**アプリ保護ポリシー**は、次のような管理エラーから保護することで、セキュリティを向上させます。

- Intune のライセンスを持たないユーザー
- Intune アプリ保護ポリシーを受信できないユーザー
- ポリシーを受信するように構成されていない Intune アプリ保護ポリシーのアプリ


## <a name="before-you-begin"></a>開始する前に

このトピックは、次の事項を熟知していることを前提としています。

- [アプリ保護ポリシーの要件](technical-reference.md#app-protection-policy-requirement)に関するテクニカル リファレンス。

- [承認されたクライアント アプリの要件](technical-reference.md#approved-client-app-requirement)に関するテクニカル リファレンス。

- [Azure Active Directory の条件付きアクセス](overview.md)の基本的な概念。

- [条件付きアクセス ポリシーの構成](app-based-mfa.md)方法。


## <a name="prerequisites"></a>前提条件

アプリ保護ベースの条件付きアクセスを作成するには、次のことが必要です
- Enterprise Mobility + Security または Azure Active Directory Premium サブスクリプション + Intune を持っていること
- ユーザーに EMS または Azure AD + Intune のライセンスが付与されていること
- クライアント アプリが Intune でアプリ保護ポリシーを受信するように構成されていること
- ユーザーが Intune で Intune アプリ保護ポリシーを受信するように構成されていること

## <a name="app-protection-based-policy-for-exchange-online"></a>Exchange Online 用のアプリ保護ベース

このシナリオは、Exchange Online にアクセスするためのアプリ保護ベースの条件付きアクセス ポリシーで構成されます。

### <a name="scenario-playbook"></a>シナリオのプレイブック

このシナリオでは、ユーザーを次のように想定しています。

- iOS または Android でネイティブ メール アプリケーションを使用して Exchange に接続する電子メールを作成する

- Outlook アプリを使用したアクセスのみを利用できることを示す電子メールを受信する

- リンクを使用してアプリケーションをダウンロードする

- Outlook アプリケーションを開き、Azure AD 資格情報を使用してサインインする

- 続行するには Authenticator (iOS) またはポータル サイト (Android) をインストールすることを求められる

- アプリケーションをインストールした後、Outlook アプリに戻って続行できる

- デバイスを登録することを求められる

- Intune アプリ保護ポリシーを受信できる

- 電子メールにアクセスできる

すべての Intune アプリ保護ポリシーは、会社データにアクセスするアプリケーション上に存在する必要があり、ユーザーにアプリケーションの再起動や追加 PIN の使用などを求めることができます (アプリケーションとプラットフォームがそのように構成されている場合)。

### <a name="configuration"></a>構成

**手順 1 - Exchange Online 用の Azure AD 条件付きアクセス ポリシーを構成する**

この手順の条件付きアクセス ポリシーでは、次のコンポーネントを構成する必要があります。

![条件付きアクセス](./media/app-protection-based-conditional-access/01.png)

1. 条件付きアクセス ポリシーの**名前**。

2. **ユーザーとグループ**:各条件付きアクセス ポリシーには、少なくとも 1 名のユーザーまたは 1 つのグループが選択されている必要があります。

3. **クラウド アプリ**:クラウド アプリとして、**[Office 365 Exchange Online]** を選択する必要があります。

    ![条件付きアクセス](./media/app-protection-based-conditional-access/07.png)

4. **条件**:**条件**として、**デバイス プラットフォーム**と**クライアント アプリ**を構成する必要があります。

    a. **デバイス プラットフォーム**として、**[Android]** と **[iOS]** を選択します。

    ![条件付きアクセス](./media/app-protection-based-conditional-access/03.png)

    b. **クライアント アプリ (プレビュー)** として、**[Mobile apps and desktop apps]** \(モバイル アプリとデスクトップ アプリ\) と **[先進認証クライアント]** を選択します。

    ![条件付きアクセス](./media/app-protection-based-conditional-access/91.png)

5. **アクセス コントロール**として、**[アプリの保護ポリシーが必要 (プレビュー)]** を選択する必要があります。

    ![条件付きアクセス](./media/app-protection-based-conditional-access/05.png)
 

**手順 2 - Active Sync (EAS) を使用する Exchange Online 用の Azure AD 条件付きアクセス ポリシーを構成する**

この手順の条件付きアクセス ポリシーでは、次のコンポーネントを構成する必要があります。

![条件付きアクセス](./media/app-protection-based-conditional-access/06.png)

1. 条件付きアクセス ポリシーの**名前**。

2. **ユーザーとグループ**:各条件付きアクセス ポリシーには、少なくとも 1 名のユーザーまたは 1 つのグループが選択されている必要があります。


3. **クラウド アプリ**:クラウド アプリとして、**[Office 365 Exchange Online]** を選択する必要があります。

    ![条件付きアクセス](./media/app-protection-based-conditional-access/07.png)

4. **条件**:**条件**として、**クライアント アプリ (プレビュー)** を構成する必要があります。 

    a. **クライアント アプリ (プレビュー)** として、**[Mobile apps and desktop apps]** \(モバイル アプリとデスクトップ アプリ\) と **[Exchange ActiveSync クライアント]** を選択します。

    ![条件付きアクセス](./media/app-protection-based-conditional-access/92.png)

    b. **アクセス コントロール**として、**[アプリの保護ポリシーが必要 (プレビュー)]** を選択する必要があります。

    ![条件付きアクセス](./media/app-protection-based-conditional-access/05.png)


**手順 3 - iOS および Android クライアント アプリケーション用の Intune アプリ保護ポリシーを構成する**


![条件付きアクセス](./media/app-protection-based-conditional-access/09.png)

詳細については、「[Microsoft Intune でアプリとデータを保護する](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune)」を参照してください。



## <a name="app-protection-based-or-compliant-device-policy-for-exchange-online"></a>Exchange Online 用のアプリ保護ベースまたは準拠しているデバイス ポリシー

このシナリオは、Exchange Online にアクセスするためのアプリ保護ベースまたは準拠しているデバイス ベースの条件付きアクセス ポリシーで構成されます。


### <a name="scenario-playbook"></a>シナリオのプレイブック

このシナリオでは、ユーザーを次のように想定しています。
 
- 一部のユーザーは既に登録されている (会社のデバイスと共に、またはデバイスなしで)

- アプリで保護されたアプリケーションを使用して Azure AD に登録していないユーザーがリソースにアクセスするには、デバイスを登録する必要がある

- アプリで保護されたアプリケーションを使用して登録しているユーザーは、デバイスを再登録する必要はない

- 未登録の場合、ユーザーは Intune アプリ保護ポリシーを受信できる

- 未登録の場合、ユーザーは Outlook と Intune アプリ保護ポリシーを使用してメールにアクセスできる

- デバイスが未登録の場合、ユーザーは Outlook を使用してメールにアクセスできる


### <a name="configuration"></a>構成

**手順 1 - Exchange Online 用の Azure AD 条件付きアクセス ポリシーを構成する**

この手順の条件付きアクセス ポリシーでは、次のコンポーネントを構成する必要があります。

![条件付きアクセス](./media/app-protection-based-conditional-access/62.png)

1. 条件付きアクセス ポリシーの**名前**。

2. **ユーザーとグループ**:各条件付きアクセス ポリシーには、少なくとも 1 名のユーザーまたは 1 つのグループが選択されている必要があります。

3. **クラウド アプリ**:クラウド アプリとして、**[Office 365 Exchange Online]** を選択する必要があります。 

     ![条件付きアクセス](./media/app-protection-based-conditional-access/07.png)

4. **条件**:**条件**として、**デバイス プラットフォーム**と**クライアント アプリ**を構成する必要があります。 
 
    a. **デバイス プラットフォーム**として、**[Android]** と **[iOS]** を選択します。

    ![条件付きアクセス](./media/app-protection-based-conditional-access/03.png)

    b. **クライアント アプリ (プレビュー)** として、**[モバイル アプリとデスクトップ クライアント]** と **[先進認証クライアント]** を選択します。

    ![条件付きアクセス](./media/app-protection-based-conditional-access/91.png)

5. **アクセスコントロール**として、以下を選択する必要があります。

   - **デバイスは準拠としてマーク済みである必要がある**

   - **アプリの保護ポリシーが必要 (プレビュー)**

   - **選択した制御のいずれかが必要**   
 
     ![条件付きアクセス](./media/app-protection-based-conditional-access/11.png)



**手順 2 - Active Sync (EAS) を使用する Exchange Online 用の Azure AD 条件付きアクセス ポリシーを構成する**

この手順の条件付きアクセス ポリシーでは、次のコンポーネントを構成する必要があります。

![条件付きアクセス](./media/app-protection-based-conditional-access/06.png)

1. 条件付きアクセス ポリシーの**名前**。

2. **ユーザーとグループ**:各条件付きアクセス ポリシーには、少なくとも 1 名のユーザーまたは 1 つのグループが選択されている必要があります。

3. **クラウド アプリ**:クラウド アプリとして、**[Office 365 Exchange Online]** を選択する必要があります。 

    ![条件付きアクセス](./media/app-protection-based-conditional-access/07.png)

4. **条件**:**条件**として、**クライアント アプリ**を構成する必要があります。 

    **クライアント アプリ (プレビュー)** として、**[Mobile apps and desktop apps]** \(モバイル アプリとデスクトップ アプリ\) と **[Exchange ActiveSync クライアント]** を選択します。

    ![条件付きアクセス](./media/app-protection-based-conditional-access/92.png)

5. **アクセスコントロール**として、以下を選択する必要があります。

   - **デバイスは準拠としてマーク済みである必要がある**

   - **アプリの保護ポリシーが必要 (プレビュー)**

   - **選択した制御のいずれかが必要**   
    ![条件付きアクセス](./media/app-protection-based-conditional-access/11.png)



**手順 3 - iOS および Android クライアント アプリケーション用の Intune アプリ保護ポリシーを構成する**


![条件付きアクセス](./media/app-protection-based-conditional-access/09.png)

詳細については、「[Microsoft Intune でアプリとデータを保護する](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune)」を参照してください。





## <a name="app-protection-based-and-compliant-device-policy-for-exchange-online"></a>Exchange Online 用のアプリ保護ベースおよび準拠しているデバイス ポリシー

このシナリオは、Exchange Online にアクセスするためのアプリ保護ベースおよび準拠しているデバイス ベースの条件付きアクセス ポリシーで構成されます。


### <a name="scenario-playbook"></a>シナリオのプレイブック

このシナリオでは、ユーザーを次のように想定しています。
 
-   iOS または Android でネイティブ メール アプリケーションを使用して電子メールを作成して Exchange に接続する
-   アクセスするにはデバイスを登録する必要があることを示す電子メールを受信する
-   ポータル サイトをダウンロードしてサインインする
-   メールを確認すると、Outlook アプリを使用することを求められる
-   Outlook アプリをダウンロードする
-   Outlook アプリを開き、登録で使用される資格情報を入力する
-   Intune アプリ保護ポリシーを受信できる
-   Outlook と Intune アプリ保護ポリシーを使用してメールにアクセスできる

すべての Intune アプリ保護ポリシーは、会社データへのアクセスする前にアクティブになり、ユーザーにアプリケーションの再起動や追加 PIN の使用などを求めることができます (アプリケーションとプラットフォームがそのように構成されている場合)


### <a name="configuration"></a>構成

**手順 1 - Exchange Online 用の Azure AD 条件付きアクセス ポリシーを構成する**

この手順の条件付きアクセス ポリシーでは、次のコンポーネントを構成する必要があります。

![条件付きアクセス](./media/app-protection-based-conditional-access/01.png)

1. 条件付きアクセス ポリシーの**名前**。

2. **ユーザーとグループ**:各条件付きアクセス ポリシーには、少なくとも 1 名のユーザーまたは 1 つのグループが選択されている必要があります。

3. **クラウド アプリ**:クラウド アプリとして、**[Office 365 Exchange Online]** を選択する必要があります。 

     ![条件付きアクセス](./media/app-protection-based-conditional-access/07.png)

4. **条件**:**条件**として、**デバイス プラットフォーム**と**クライアント アプリ**を構成する必要があります。 
 
    a. **デバイス プラットフォーム**として、**[Android]** と **[iOS]** を選択します。

    ![条件付きアクセス](./media/app-protection-based-conditional-access/03.png)

    b. **クライアント アプリ (プレビュー)** として、**[Mobile apps and desktop apps]** \(モバイル アプリとデスクトップ アプリ\) と **[先進認証クライアント]** を選択します。

    ![条件付きアクセス](./media/app-protection-based-conditional-access/91.png)

5. **アクセスコントロール**として、以下を選択する必要があります。

   - **デバイスは準拠としてマーク済みである必要がある**

   - **アプリの保護ポリシーが必要 (プレビュー)**

   - **選択したコントロールすべてが必要**   
 
     ![条件付きアクセス](./media/app-protection-based-conditional-access/13.png)



**手順 2 - Active Sync (EAS) を使用する Exchange Online 用の Azure AD 条件付きアクセス ポリシーを構成する**

この手順の条件付きアクセス ポリシーでは、次のコンポーネントを構成する必要があります。

![条件付きアクセス](./media/app-protection-based-conditional-access/06.png)

1. 条件付きアクセス ポリシーの**名前**。

2. **ユーザーとグループ**:各条件付きアクセス ポリシーには、少なくとも 1 名のユーザーまたは 1 つのグループが選択されている必要があります。

3. **クラウド アプリ**:クラウド アプリとして、**[Office 365 Exchange Online]** を選択する必要があります。 

    ![条件付きアクセス](./media/app-protection-based-conditional-access/07.png)

4. **条件**:**条件**として、**クライアント アプリ (プレビュー)** を構成する必要があります。 

    **クライアント アプリ (プレビュー)** として、**[Mobile apps and desktop apps]** \(モバイル アプリとデスクトップ アプリ\) と **[Exchange ActiveSync クライアント]** を選択します。

    ![条件付きアクセス](./media/app-protection-based-conditional-access/92.png)

5. **アクセスコントロール**として、以下を選択する必要があります。

   - **デバイスは準拠としてマーク済みである必要がある**

   - **承認済みクライアント アプリを必須にする (プレビュー)**

   - **選択したコントロールすべてが必要**   
 
     ![条件付きアクセス](./media/app-protection-based-conditional-access/13.png)




**手順 3 - iOS および Android クライアント アプリケーション用の Intune アプリ保護ポリシーを構成する**


![条件付きアクセス](./media/app-protection-based-conditional-access/09.png)

詳細については、「[Microsoft Intune でアプリとデータを保護する](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune)」を参照してください。


## <a name="app-protection-based-or-app-based-policy-for-exchange-online-and-sharepoint-online"></a>Exchange Online と SharePoint Online 用のアプリ保護ベースまたはアプリベースのポリシー

このシナリオは、Exchange Online および SharePoint Online にアクセスするためのアプリ保護ベースまたは承認されたアプリのポリシーで構成されます。


### <a name="scenario-playbook"></a>シナリオのプレイブック

このシナリオでは、ユーザーを次のように想定しています。

- アプリ保護ポリシーの要件または承認されたアプリの要件をサポートするアプリの一覧にある、いずれかのクライアント アプリケーションを構成します。  
- ユーザーは、アプリ保護ポリシーの要件を満たし、Intune アプリ保護ポリシーを受信できるクライアント アプリケーションを使用する
- ユーザーは、Intune アプリ保護ポリシーをサポートする、承認されたアプリのポリシーの要件を満たすクライアント アプリケーションを使用する
- 電子メールまたはドキュメントにアクセスするためのアプリケーションを開く
- Outlook アプリケーションを開き、Azure AD 資格情報を使用してサインインする
- 続行するには Authenticator (iOS) またはポータル サイト (Android) をインストールすることを求められる (インストールされていない場合)
- アプリケーションをインストールした後、Outlook アプリに戻って続行できる
- デバイスを登録することを求められる
- Intune アプリ保護ポリシーを受信できる
- Outlook と Intune アプリ保護ポリシーを使用してメールにアクセスできる
- アプリ保護ポリシーの要件ではなく、承認されたアプリの要件にリストされているアプリを使用して、サイト/ドキュメントにアクセスできる

会社データにアクセスするにはすべての Intune アプリ保護ポリシーが必要であり、ユーザーにアプリケーションの再起動や追加の PIN の使用などが求められる場合があります (アプリケーションとプラットフォームに対して構成されている場合)

**解説**

- アプリ保護ベースおよびアプリベースの両方の条件付きアクセス ポリシーをサポートする場合は、このシナリオを使用することができます。

- この *OR* ポリシーでは、**アプリ保護ポリシー**の要件があるアプリは、**承認されたクライアント アプリ**の要件より前に、アクセスについて最初に評価されます。

### <a name="configuration"></a>構成

**手順 1 - Exchange Online 用の Azure AD 条件付きアクセス ポリシーを構成する**

この手順の条件付きアクセス ポリシーでは、次のコンポーネントを構成する必要があります。

![条件付きアクセス](./media/app-protection-based-conditional-access/62.png)

1. 条件付きアクセス ポリシーの**名前**。

2. **ユーザーとグループ**:各条件付きアクセス ポリシーには、少なくとも 1 名のユーザーまたは 1 つのグループが選択されている必要があります。

3. **クラウド アプリ**:クラウド アプリとして、**[Office 365 Exchange Online]** を選択する必要があります。 

     ![条件付きアクセス](./media/app-protection-based-conditional-access/02.png)

4. **条件**:**条件**として、**デバイス プラットフォーム**と**クライアント アプリ**を構成する必要があります。 
 
    a. **デバイス プラットフォーム**として、**[Android]** と **[iOS]** を選択します。

    ![条件付きアクセス](./media/app-protection-based-conditional-access/03.png)

    b. **クライアント アプリ (プレビュー)** として、**[Mobile apps and desktop apps]** \(モバイル アプリとデスクトップ アプリ\) と **[先進認証クライアント]** を選択します。

    ![条件付きアクセス](./media/app-protection-based-conditional-access/91.png)

5. **アクセスコントロール**として、以下を選択する必要があります。

   - **承認済みクライアント アプリを必須にする**

   - **アプリの保護ポリシーが必要 (プレビュー)**

   - **選択した制御のいずれかが必要**   
 
     ![条件付きアクセス](./media/app-protection-based-conditional-access/12.png)


**手順 2 - iOS および Android クライアント アプリケーション用の Intune アプリ保護ポリシーを構成する**


![条件付きアクセス](./media/app-protection-based-conditional-access/09.png)

詳細については、「[Microsoft Intune でアプリとデータを保護する](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune)」を参照してください。




## <a name="next-steps"></a>次の手順

条件付きアクセス ポリシーの構成方法を把握するには、「[Azure Active Directory の条件付きアクセスを使用して特定のアプリケーションに対して MFA を必要にする](app-based-mfa.md)」を参照してください。

環境に適用する条件付きアクセス ポリシーを構成する準備ができたら、「[Azure Active Directory の条件付きアクセスのベスト プラクティス](best-practices.md)」を参照してください。 