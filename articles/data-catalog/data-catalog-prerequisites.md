<properties
   pageTitle="Azure Data Catalog の前提条件"
   description="Azure Data Catalog の使用を開始するために必要なこと"
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="09/11/2015"
   ms.author="maroche"/>

# Azure Data Catalog の使用を開始するために必要なこと

## Azure Data Catalog の前提条件

**Azure Data Catalog** をセットアップする前に、いくつかのことに注意する必要があります。それほど長くはかかりません。

## Azure Active Directory

Azure Active Directory (Azure AD) は、企業がクラウドとオンプレミス環境の両方で ID とアクセスを簡単に管理できるサービスです。ユーザーは、任意のクラウドおよびオンプレミス Web アプリケーションへのシングル サインオンのために、1 つの職場または学校アカウントを使用できます。Azure Data Catalog は、Azure AD を使用して、サインオンを認証します。詳細については、「[Azure AD の使用開始方法](active-directory-get-started.md)」を参照してください。

## Active Directory ポリシーの構成

場合によっては、ユーザーは、Azure Data Catalog ポータルにログオンできる状況が発生することがありますが、データ ソース登録ツールにログオンしようとすると、ログオンを妨げるエラー メッセージが表示されます。ユーザーが企業ネットワーク上にいる場合のみ、またはユーザーが企業ネットワークの外部から接続している場合のみ、このエラー動作が発生することがあります。

データ ソース登録ツールは、フォーム認証を使用して、Active Directory に対するユーザー ログオンを検証します。ログオンを成功させるには、Active Directory 管理者によって、グローバル認証ポリシーでフォーム認証が有効にされている必要があります。

下図に示すように、グローバル認証ポリシーでは、イントラネット接続とエクストラネット接続で個別に認証方法を有効にすることができます。ログオン エラーは、ユーザーの接続元のネットワークで、フォーム認証が有効にされていない場合に発生する可能性があります。

 ![Active Directory グローバル認証ポリシー](./media/data-catalog-prerequisites/global-auth-policy.png)

詳細については、「[認証ポリシーの構成](https://technet.microsoft.com/ja-jp/library/dn486781.aspx)」を参照してください。


## Azure サブスクリプション
Azure サブスクリプションは、Azure Data Catalog のようなクラウド サービス リソースへのアクセスを整理するために役立ちます。さらに、リソースの使用状況の報告、課金、および支払い方法の制御にも役立ちます。サブスクリプションごとに異なる課金および支払いを設定することができるため、部門別、プロジェクト別、支社別などで、異なるサブスクリプションや異なるプランを利用することができます。すべてのクラウド サービスがサブスクリプションに属しているため、Azure Data Catalog をセットアップする前に、サブスクリプションが必要です。詳細については、「[アカウント、サブスクリプション、管理ロールの管理](https://msdn.microsoft.com/library/azure/hh531793.aspx)」を参照してください。

<!---HONumber=Oct15_HO3-->