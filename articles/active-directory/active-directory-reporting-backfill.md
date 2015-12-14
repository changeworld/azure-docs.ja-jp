<properties
   pageTitle="Azure Active Directory でのバックフィル時間のレポート | Microsoft Azure"
   description="Azure Active Directory で前のレポート イベントを表示するのにかかる時間"
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

## レポート ドキュメントの記事

 - [レポート API](active-directory-reporting-api-getting-started.md)
 - [イベントの監査](active-directory-reporting-audit-events.md)
 - [保持](active-directory-reporting-retention.md)
 - [プレビュー](active-directory-reporting-previews.md)
 - [Search](active-directory-reporting-search.md)
 - [バックフィル](active-directory-reporting-backfill.md)
 - [待機時間](active-directory-reporting-latencies.md)
 - ["Unknown Actor" イベント](active-directory-reporting-unknown-actor.md)

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

<!---HONumber=AcomDC_1203_2015-->