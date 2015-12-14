<properties
	pageTitle="Azure Active Directory レポートの保持ポリシー | Microsoft Azure"
	description="Azure Active Directory でのレポート データの保持ポリシー"
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
	ms.date="11/30/2015"
	ms.author="kenhoff"/>

# Azure Active Directory レポートの保持ポリシー

## ドキュメント記事のレポート

 - [Reporting API](active-directory-reporting-api-getting-started.md)
 - [イベントの監査](active-directory-reporting-audit-events.md)
 - [保持](active-directory-reporting-retention.md)
 - [プレビュー](active-directory-reporting-previews.md)
 - [Search](active-directory-reporting-search.md)
 - [バックフィル](active-directory-reporting-backfill.md)
 - [待機時間](active-directory-reporting-latencies.md)
 - ["Unknown Actor"　イベント](active-directory-reporting-unknown-actor.md)

Azure AD レポートは、次に示す一定期間データを保存します。

レポート | 説明
------------------------------------------------------- | -----------
不明なソースからのサインイン | 30 日
複数のエラー後のサインイン | 30 日
複数の地域からのサインイン | 30 日
不審なアクティビティのある IP アドレスからのサインイン | 30 日
感染している可能性があるデバイスからのサインイン | 30 日
不規則なサインイン アクティビティ | 30 日
異常なサインイン アクティビティがあるユーザー | 30 日
資格情報が漏洩したユーザー | 30 日
監査レポート | 180 日
パスワード リセット アクティビティ (Azure AD) | 30 日
パスワード リセット アクティビティ (Identity Manager) | 30 日
パスワード リセット登録アクティビティ (Azure AD) | 30 日
パスワード リセット登録アクティビティ (Identity Manager) | 30 日
セルフ サービス グループ アクティビティ (Azure AD) | 30 日
セルフ サービス グループ アクティビティ (Identity Manager) | 30 日
アプリケーションの使用状況 | 30 日
アカウント プロビジョニングのアクティビティ | 30 日
パスワード ロールオーバーの状態 | 30 日
アカウント プロビジョニング エラー | 30 日
RMS の利用状況 | 30 日
最もアクティブな RMS ユーザー | 30 日
RMS デバイスの利用状況 | 30 日
RMS 対応アプリケーションの利用状況 | 30 日

<!---HONumber=AcomDC_1203_2015-->