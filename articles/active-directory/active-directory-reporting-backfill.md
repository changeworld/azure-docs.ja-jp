<properties
   pageTitle="Azure Active Directory レポートのバックフィル時間 | Microsoft Azure"
   description="これまでのレポート イベントを Azure Active Directory に表示するのにかかる時間"
   services="active-directory"
   documentationCenter=""
   authors="kenhoff"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="12/07/2015"
   ms.author="kenhoff"/>

# Azure Active Directory レポートのバックフィル時間

*このドキュメントは、[Azure Active Directory レポート ガイド](active-directory-reporting-guide.md)の一部です*。

ディレクトリがレポートにオプトインされた後、レポートは、次に示す一定期間データをバックフィルします。

レポート | 説明
------------------------------------------------------- | -----------
不明なソースからのサインイン | 0 日
複数のエラー後のサインイン | 0 日
複数の地域からのサインイン | 0 日
不審なアクティビティのある IP アドレスからのサインイン | 0 日
感染している可能性があるデバイスからのサインイン | 0 日
不規則なサインイン アクティビティ | 0 日
異常なサインイン アクティビティがあるユーザー | 0 日
資格情報が漏洩したユーザー | 0 日
監査レポート | 30 日
パスワード リセット アクティビティ (Azure AD) | 0 日
パスワード リセット アクティビティ (Identity Manager) | 0 日
パスワード リセット登録アクティビティ (Azure AD) | 0 日
パスワード リセット登録アクティビティ (Identity Manager) | 0 日
セルフ サービス グループ アクティビティ (Azure AD) | 0 日
セルフ サービス グループ アクティビティ (Identity Manager) | 0 日
アプリケーションの使用状況 | 0 日
アカウント プロビジョニングのアクティビティ | 0 日
パスワード ロールオーバーの状態 | 0 日
アカウント プロビジョニング エラー | 0 日
RMS の利用状況 | 0 日
最もアクティブな RMS ユーザー | 0 日
RMS デバイスの利用状況 | 0 日
RMS 対応アプリケーションの利用状況 | 0 日

<!---HONumber=AcomDC_1210_2015-->