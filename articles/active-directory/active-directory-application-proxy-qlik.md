---
title: Azure AD アプリ プロキシと Qlik Sense| Microsoft Docs
description: Azure Portal でアプリケーション プロキシを有効にして、リバース プロキシ用のコネクタをインストールします。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 03/26/2018
ms.author: markvi
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 331f8ed2e77a076dd8969dc37add1cdeafc852dc
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2018
---
# <a name="application-proxy-and-qlik-sense"></a>アプリケーション プロキシと Qlik Sense 
Azure Active Directory アプリケーション プロキシと Qlik Sense は一緒に連携動作し、アプリケーション プロキシを使用して Qlik Sense 配置用のリモート アクセスを容易に提供できるようにします。  

## <a name="prerequisites"></a>前提条件 
このシナリオの残りは、以下を終えていることが前提となっています。
 
- [Qlik Sense](https://community.qlik.com/docs/DOC-19822) を構成した。 
- アプリケーション プロキシ コネクタをインストールした 

## <a name="install-an-application-proxy-connector"></a>アプリケーション プロキシ コネクタのインストール 
既にアプリケーション プロキシを有効にし、コネクタをインストールしている場合は、このセクションをスキップして「[アプリケーション プロキシを使って Azure AD にアプリを登録する](application-proxy-ping-access.md)」に進むことができます。 

アプリケーション プロキシ コネクタは、遠隔にいる従業員からのトラフィックを発行済みアプリに誘導する Windows Server サービスです。 詳しいインストール手順については、「[Azure Portal でアプリケーション プロキシを有効にする](active-directory-application-proxy-enable.md)」を参照してください。 


1. [Azure Portal](https://portal.azure.com/) にグローバル管理者としてサインインします。 
2. [Azure Active Directory]、[アプリケーション プロキシ] の順に選択します。 
3. [Download Connector (コネクタのダウンロード)] を選択してアプリケーション プロキシ コネクタのダウンロードを開始します。 画面の指示に従ってインストールしてください。 
 
>[!NOTE]
>通常、コネクタをダウンロードすれば、ディレクトリに対してアプリケーション プロキシが自動的に有効になります。有効にならなかった場合は、**[アプリケーション プロキシの有効化]** を選択してください。 
 
## <a name="publish-your-applications-in-azure"></a>アプリケーションを Azure に発行する 
QlikSense を発行するには、Azure で 2 つのアプリケーションを発行する必要があります。  

### <a name="application-1"></a>アプリケーション 1: 
アプリを公開するには、次の手順に従います。 手順 1 ～ 8 の詳細については、「[Azure AD アプリケーション プロキシを使用してアプリケーションを発行する](application-proxy-publish-azure-portal.md)」を参照してください。 


1. 直前のセクションの手順を省略した場合は、グローバル管理者として Azure Portal にサインインしてください。 
2. **[Azure Active Directory]** > **[エンタープライズ アプリケーション]** の順に選択します。 
3. ブレード上部の **[追加]** を選択します。 
4. **[オンプレミスのアプリケーション]** を選択します。 
5.       新しいアプリに関する情報を必須フィールドに入力します。 次のガイダンスに従って設定してください。 
    - **[内部 URL]**: このアプリケーションは、QlikSense URL そのものである内部 URL を持っている必要があります。 たとえば、**https&#58;//demo.qlikemm.com** です 
    - **[事前認証方法]**: Azure Active Directory (推奨ですが必須ではありません) 
1.       ブレード下部の **[追加]** を選択します。 アプリケーションが追加されて、クイック スタート メニューが表示されます。 
2.       クイック スタート メニューで **[テスト用のユーザーを割り当てる]** を選択し、少なくとも 1 ユーザーをアプリケーションに追加します。 このテスト アカウントでオンプレミスのアプリケーションにアクセスできることを確認します。 
3.       **[割り当て]** を選択して、テスト ユーザーの割り当てを保存します。 
4.       (オプション) アプリの管理ブレードで [シングル サインオン] を選択します。 ドロップダウン メニューから **[Kerberos の制約付き委任]** を選択し、Qlik 構成に基づいて必要なフィールドに記入します。 **[保存]** を選択します。 

### <a name="application-2"></a>アプリケーション 2: 
次の例外を除き、アプリケーション 1 の場合と同じ手順に従います。 

**手順 5.**: 内部 URL は、アプリケーションによって使用される認証ポートを持つ QlikSense URL になっているはずです。 既定値は、HTTPS の場合は **4244**、HTTP の場合は 4248 です。 例: **https&#58;//demo.qlik.com:4244** 
**手順 10:** SSO を設定しないで、**シングルサインオンを無効**のままにします。
 
 
## <a name="testing"></a>テスト 
これでアプリケーションをテストする準備ができました。 アプリケーション 1 で QlikSense を発行するために使用した外部 URL にアクセスし、割り当てられているユーザーとして、両方のアプリケーションにログインします。  

## <a name="next-steps"></a>次の手順

- [アプリケーション プロキシを使用してアプリケーションを発行する](application-proxy-publish-azure-portal.md)
- [アプリケーション プロキシ コネクタの使用方法](active-directory-application-proxy-connectors-azure-portal.md)。
