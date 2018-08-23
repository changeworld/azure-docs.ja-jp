---
title: Azure AD Reporting API と証明書を使ってデータを取得する | Microsoft Docs
description: Azure AD Reporting API と証明書の資格情報を使い、ユーザーの介入なしでディレクトリからデータを取得する方法について説明します。
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: report-monitor
ms.date: 05/07/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: c1f5b88dc8cd6d7f8ff1cd1269d4791d5654e133
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2018
ms.locfileid: "42140377"
---
# <a name="get-data-using-the-azure-active-directory-reporting-api-with-certificates"></a>Azure Active Directory Reporting API と証明書を使用したデータの取得

[Azure Active Directory (Azure AD) レポート API](concept-reporting-api.md) は、一連の REST ベースの API を使用してデータへのプログラムによるアクセスを提供します。 これらの API は、さまざまなプログラミング言語とツールから呼び出すことができます。 ユーザーの介入なしに Azure AD Reporting API にアクセスする場合は、証明書を使用するようにアクセスを構成できます。

これには、次の手順が含まれます。

1. [前提条件をインストールする](#install-prerequisites)
2. [アプリで証明書を登録する](#register-the-certificate-in-your-app)
3. [MS Graph API のアクセス トークンを取得する](#get-an-access-token-for-ms-graph-api)
4. [MS Graph API のエンドポイントにクエリを行う](#query-the-ms-graph-api-endpoints)


## <a name="install-prerequisites"></a>必須コンポーネントをインストールする

1. 最初に、[Azure Active Directory Reporting API にアクセスするための前提条件](howto-configure-prerequisites-for-reporting-api.md)を満たしていることを確認します。 

2. [Azure Active Directory PowerShell](https://github.com/Azure/azure-docs-powershell-azuread/blob/master/Azure AD Cmdlets/AzureAD/index.md) についての手順に従い、Azure AD Powershell V2 をダウンロードしてインストールします。

3. [PowerShellGallery - MSCloudIdUtils](https://www.powershellgallery.com/packages/MSCloudIdUtils/) から MSCloudIDUtils をインストールします。 このモジュールには、いくつかのユーティリティ コマンドレットが用意されています。その例を次に示します。
    - 認証に必要な ADAL ライブラリ
    - ADAL を使用してユーザー キー、アプリケーション キー、証明書からアクセス トークンを取得
    - Graph API でページ単位の結果を処理

4. 初めてモジュールを使う場合は、**Install-MSCloudIdUtilsModule** を実行してセットアップを完了します。初めてではない場合は、**Import-Module** Powershell コマンドを使ってすぐにインポートできます。

セッションは次のような画面になります。

  ![Windows PowerShell](./media/tutorial-access-api-with-certificates/module-install.png)

## <a name="register-the-certificate-in-your-app"></a>アプリで証明書を登録する

1. 最初に、アプリケーションの登録ページに移動します。 これは、[Azure portal](https://portal.azure.com) に移動し、**[Azure Active Directory]** をクリックしてから、**[アプリの登録]** をクリックして一覧からアプリケーションを選択することによって、行うことができます。 

2. その後、アプリケーションの[証明書を Azure AD に登録する](https://docs.microsoft.com/azure/active-directory/develop/active-directory-certificate-credentials#register-your-certificate-with-azure-ad)手順に従います。 

3. アプリケーション ID と、アプリケーションで登録した証明書のサムプリントを書き留めます。 サムプリントを調べるには、ポータルのアプリケーション ページから **[設定]** に移動し、**[キー]** をクリックします。 サムプリントは **[公開鍵]** 一覧の下にあります。

  
## <a name="get-an-access-token-for-ms-graph-api"></a>MS Graph API のアクセス トークンを取得する

MS Graph API のアクセス トークンを取得するには、MSCloudIdUtils PowerShell モジュールの **Get-MSCloudIdMSGraphAccessTokenFromCert** コマンドレットを使います。 

>[!NOTE]
>アプリケーション ID (クライアント ID とも呼ばれます) と、コンピューターの証明書ストア (CurrentUser または LocalMachine 証明書ストア) にインストールされている秘密キーの証明書の証明書サムプリントを、使う必要があります。
>

 ![Azure ポータル](./media/tutorial-access-api-with-certificates/getaccesstoken.png)

## <a name="use-the-access-token-to-call-the-graph-api"></a>アクセス トークンを使用して Graph API を呼び出す

ここで、Powershell スクリプトでアクセス トークンを使って、Graph API のクエリを行うことができます。 次に示すのは、MSCloudIDUtils の **Invoke-MSCloudIdMSGraphQuery** コマンドレットを使って SignIns または diectoryAudits エンドポイントを列挙する方法の例です。 複数のページにわたる結果を処理し、それらの結果を PowerShell パイプラインに送っています。

### <a name="query-the-directoryaudits-endpoint"></a>DirectoryAudits エンドポイントのクエリ
 ![Azure ポータル](./media/tutorial-access-api-with-certificates/query-directoryAudits.png)

 ### <a name="query-the-signins-endpoint"></a>SignIns エンドポイントのクエリ
 ![Azure ポータル](./media/tutorial-access-api-with-certificates/query-signins.png)

このデータを CSV にエクスポートし、SIEM システムに保存できるようになります。 また、スケジュールされたタスクにスクリプトをラップすれば、ソース コードにアプリケーション キーを保存せずに Azure AD データをテナントから定期的に取得することができます。 


## <a name="next-steps"></a>次の手順

* [Reporting API の概要を把握します](concept-reporting-api.md)
* [監査 API リファレンス](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
* [サインイン アクティビティ レポート API リファレンス](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)



