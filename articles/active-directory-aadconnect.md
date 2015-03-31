<properties pageTitle="Azure Active Directory Connect" description="Azure Active Directory Connect ウィザードは、オンプレミスの Windows Server Active Directory と Azure Active Directory を簡単な操作で連携できるツールです" services="active-directory" documentationCenter="" authors="Gayana" manager="terrylan" editor="" />

<tags ms.service="active-directory" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="02/27/2015" ms.author="gabag" />

<h1 id="vnettut1">Azure Active Directory Connect</h1>

Azure Active Directory Connect ウィザードは、オンプレミスのディレクトリと Azure Active Directory を簡単な操作で連携できるツールです。このウィザードでは、同期サービス、パスワードの同期や Active Directory フェデレーション サービス (AD FS) と Azure AD PowerShell モジュールなどの前提条件といった、ディレクトリの統合を開始するのに必要なすべてのコンポーネントをデプロイし、構成します。

>[AZURE.NOTE] **Azure Active Directory Connect には、DirSync と AAD Sync として以前リリースされていた機能を網羅しています。これらのツールは今後個別にリリースされることはありません。** 

> **Azure Active Directory Connect は、オンプレミスから Azure AD への統合について、同期、サインオン、その他のすべての側面のワン ストップ ショップです。**


現在 Azure Active Directory 同期ツール (DirSync)、Azure Active Directory 同期サービス (AAD Sync)、Forefront Identity Manager 2010 R2 を評価中か使用中の場合は、「[ディレクトリ統合ツール](http://msdn.microsoft.com/library/azure/dn757582.aspx "Directory Integration Tools")」で詳細をご覧ください。


## Azure Active Directory Connect パブリック プレビュー 

最新の Azure Active Directory Connect パブリック プレビューでは、1 つ以上の Windows Server Active Directory フォレストを Azure AD に簡単に統合できます。 

[Azure AD Connect パブリック プレビューのダウンロード](http://connect.microsoft.com/site1164/program8612 "Azure Active Directory Connect") 

Azure Active Directory Connect のプレビュー版では、次の操作を行うことができます。 

- 高速設定を選択して、4 回クリックするだけ 1 つのフォレストをすばやく簡単に構成する
- カスタム設定を選択して、複数のフォレストを構成したり、AD FS を選んでシングル サインオン (SSO) を使用したりする
- Exchange ハイブリッド モード、パスワードの書き戻し、代替の ID 属性といった追加の同期オプションを構成する

## その他のリソース
[Azure Active Directory のドキュメント](http://azure.microsoft.com/documentation/services/active-directory/)

<!--HONumber=47-->
