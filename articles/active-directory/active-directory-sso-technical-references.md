<properties
   pageTitle="アプリケーションの管理に関するドキュメント ライブラリ | Microsoft Azure"
   description="操作方法、トラブルシューティング、およびよく寄せられる質問に対するテクニカル リファレンス リンクを提供する Azure Active Directory のアプリケーション管理トピック"
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="12/03/2015"
   ms.author="kgremban"/>

# アプリケーションの管理に関するドキュメント ライブラリ

このページには、Azure Active Directory でのシングル サインオンの主な機能に関する有用なリンクが含まれています。各リンクは、次のカテゴリに分類されています。

- **概要:** ユース ケースとトピックの紹介を含む、機能の概要。
- **操作方法:** 特定のシナリオで使用を開始するための詳細な手順。
- **トラブルシューティング:** 一般的な問題を診断するためのヒント。
- **よく寄せられる質問:** 一般的な質問や懸念事項に対する回答。  

## Cloud App Discovery

| | |
| ------ | ------ |
| 概要 | [自分の組織内で使用される承認されていないクラウド アプリを検出する方法](active-directory-cloudappdiscovery-whatis.md) |
| 操作方法 | [Getting started with Cloud App Discovery (Getting started with Cloud App Discovery の概要)](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx) <br><br> [Cloud App Discovery のセキュリティとプライバシーの考慮事項](active-directory-cloudappdiscovery-security-and-privacy-considerations.md) <br><br> [Cloud App Discovery group policy deployment guide (Cloud App Discovery のグループ ポリシーのデプロイメント ガイド)](http://social.technet.microsoft.com/wiki/contents/articles/30965.cloud-app-discovery-group-policy-deployment-guide.aspx) <br><br> [Cloud App Discovery System Center deployment guide (Cloud App Discovery System Center のデプロイメント ガイド)](http://social.technet.microsoft.com/wiki/contents/articles/30968.cloud-app-discovery-system-center-deployment-guide.aspx) <br><br> [Cloud App Discovery のプロキシ サービス用レジストリ設定](active-directory-cloudappdiscovery-registry-settings-for-proxy-services.md) |
| FAQ | [Cloud App Discovery に関してよく寄せられる質問](http://social.technet.microsoft.com/wiki/contents/articles/24037.cloud-app-discovery-frequently-asked-questions.aspx) |

## アプリの割り当て

| | |
| ------ | ------ |
| 概要 | [Azure Active Directory グループによるリソースのアクセス管理](active-directory-manage-groups.md) |
| 操作方法 | [単純なルールを作成してグループの動的メンバーシップ管理を行う](active-directory-accessmanagement-simplerulegroup.md) <br><br> [SaaS アプリケーションへのアクセスをグループで管理する](active-directory-accessmanagement-group-saasapps.md) <br><br> [アプリケーション アクセス管理のセルフ サービス化に必要な Azure Active Directory の設定](active-directory-accessmanagement-self-service-group-management.md) <br><br> [グループの所有者の管理](active-directory-accessmanagement-managing-group-owners.md) <br><br> [Azure Active Directory のセキュリティ グループの管理](active-directory-accessmanagement-manage-groups.md) <br><br> [Azure Active Directory の専用グループ](active-directory-accessmanagement-dedicated-groups.md) <br><br> [属性を使用した高度なルールの作成](active-directory-accessmanagement-groups-with-advanced-rules.md) <br><br> [Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](active-directory-saas-app-provisioning.md) <br><br> [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md) |

## フェデレーション シングル サインオン

| | |
| ------ | ------ |
| 概要 |[AD FS とのフェデレーションの構成](active-directory-aadconnect-get-started-custom.md)
| 操作方法 | [DirSync とシングル サインオン](https://msdn.microsoft.com/library/azure/dn441213.aspx) <br><br> [Microsoft Azure now supports federation with Windows Server Active Directory (Microsoft Azure での Windows Server Active Directory を使用したフェデレーションのサポート開始)](https://azure.microsoft.com/blog/windows-azure-now-supports-federation-with-windows-server-active-directory/) <br><br> [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md) <br><br> [Azure Active Directory アプリケーション](https://azure.microsoft.com/marketplace/active-directory/) |
| トラブルシューティング | [Troubleshooting Azure AD DirSync tool Configuration Wizard (Azure AD DirSync ツールの構成ウィザードのトラブルシューティング)](http://social.technet.microsoft.com/wiki/contents/articles/19100.troubleshooting-azure-ad-dirsync-tool-configuration-wizard-failed-to-get-address-for-method-createidentityhandle2.aspx) <br><br> [Azure Active Directory 同期ツールのインストールと構成ウィザードのエラー メッセージをトラブルシューティングする方法](https://support.microsoft.com/kb/2684395) <br><br> [ディレクトリ同期のトラブルシューティング](https://msdn.microsoft.com/library/azure/jj151787.aspx) <br><br> [シングル サインオンのトラブルシューティング](https://msdn.microsoft.com/library/azure/jj151834.aspx) |
| FAQ | [Azure Active Directory でのフェデレーション シングル サインオンの証明書の管理](active-directory-sso-certs.md) <br><br> [Azure Active Directory Connect の FAQ](active-directory-aadconnect-faq.md) |

## パスワード シングル サインオン

| | |
| ------ | ------ |
| 操作方法 | [オンプレミス ID と Azure Active Directory の統合](active-directory-aadconnect.md) <br><br> [DirSync とパスワード同期](https://msdn.microsoft.com/library/azure/dn441214.aspx) |
| トラブルシューティング | [Troubleshooting Azure AD DirSync tool Configuration Wizard (Azure AD DirSync ツールの構成ウィザードのトラブルシューティング)](http://social.technet.microsoft.com/wiki/contents/articles/19100.troubleshooting-azure-ad-dirsync-tool-configuration-wizard-failed-to-get-address-for-method-createidentityhandle2.aspx) <br><br> [Azure Active Directory 同期ツールのインストールと構成ウィザードのエラー メッセージをトラブルシューティングする方法](https://support.microsoft.com/kb/2684395) <br><br> [ディレクトリ同期のトラブルシューティング](https://msdn.microsoft.com/library/azure/jj151787.aspx) |
| FAQ | [Azure Active Directory Connect の FAQ](active-directory-aadconnect-faq.md) |

## リッチ アプリのプロビジョニング

| | |
| ------ | ------ |
| 概要 | [Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](active-directory-saas-app-provisioning.md) |
| 操作方法 | [チュートリアル: Azure Active Directory と Concur の統合](active-directory-saas-concur-tutorial.md) <br><br> [チュートリアル: Azure Active Directory と DocuSign の統合](active-directory-saas-docussign-tutorial.md) <br><br> [チュートリアル: Azure Active Directory と Dropbox for Business の統合](active-directory-saas-dropboxforbusiness-tutorial.md) <br><br> [チュートリアル: Azure Active Directory と Google Apps を統合する方法](active-directory-saas-google-apps-tutorial.md) <br><br> [チュートリアル: Azure Active Directory と Salesforce を統合する方法](active-directory-saas-salesforce-tutorial.md) <br><br> [チュートリアル: Azure Active Directory と ServiceNow の統合](active-directory-saas-servicenow-tutorial.md) <br><br> [チュートリアル: 受信同期のための Workday の構成](active-directory-saas-workday-inbound-tutorial.md) |

## Azure AD アプリ ギャラリー

| | |
| ------ | ------ |
| 操作方法 | [Azure Active Directory アプリケーション ギャラリーでのアプリケーションの表示](active-directory-app-gallery-listing.md) <br><br> ["Bring your own app" with Azure AD self-service SAML configuration (Azure AD セルフサービス SAML 構成で "独自のアプリを使用する")](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx) |

## カスタム アプリ

| | |
| ------ | ------ |
| 概要 | ["Bring your own app" with Azure AD self-service SAML configuration (Azure AD セルフサービス SAML 構成で "独自のアプリを使用する")](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx) |
| 操作方法 | [チュートリアル: Azure Active Directory と Salesforce を統合する方法](active-directory-saas-salesforce-tutorial.md) |

## アプリ プロキシ

| | |
| ------ | ------ |
| 概要 | [オンプレミス アプリケーションへの安全なリモート アクセスを実現する方法](active-directory-application-proxy-get-started.md) |
| 操作方法 | [Azure AD アプリケーション プロキシの有効化](active-directory-application-proxy-enable.md) <br><br> [Azure AD アプリケーション プロキシを使用してアプリケーションを発行する](active-directory-application-proxy-publish.md) <br><br> [条件付きアクセスの使用](active-directory-application-proxy-conditional-access.md) <br><br> [Azure AD アプリケーション プロキシでのカスタム ドメインの使用](active-directory-application-proxy-custom-domains.md) <br><br> [アプリケーション プロキシで要求に対応するアプリケーションを利用する](active-directory-application-proxy-claims-aware-apps.md) <br><br> [アプリケーション プロキシを使用したシングル サインオン](active-directory-application-proxy-sso-using-kcd.md) |
| トラブルシューティング | [アプリケーション プロキシのトラブルシューティング](active-directory-application-proxy-troubleshoot.md) |

## アプリの起動エクスペリエンス

| | |
| ------ | ------ |
| 概要 | [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md) |
| 操作方法 | [Azure Active Directory Access Panel - EMS (Azure Active Directory アクセス パネル - EMS)](http://blogs.msdn.com/b/haddy_el-haggan_blog/archive/2015/04/02/azure-active-directory-access-panel-ems.aspx) <br><br> [Azure Active Directory との統合](active-directory-how-to-integrate.md) |

<!---HONumber=AcomDC_1210_2015-->