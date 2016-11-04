---
title: Azure Active Directory レポートの保持ポリシー | Microsoft Docs
description: Azure Active Directory でのレポート データの保持ポリシー
services: active-directory
documentationcenter: ''
author: dhanyahk
manager: femila
editor: ''

ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/07/2016
ms.author: dhanyahk

---
# Azure Active Directory レポートの保持ポリシー
*このドキュメントは、[Azure Active Directory レポート ガイド](active-directory-reporting-guide.md)の一部です*。

Azure Active Directory (Azure AD) レポートは、データを次に示す一定期間保存します。

| レポート | Description |
| --- | --- |
| 不明なソースからのサインイン |30 日 |
| 複数のエラー後のサインイン |30 日 |
| 複数の地域からのサインイン |30 日 |
| 不審なアクティビティのある IP アドレスからのサインイン |30 日 |
| 感染している可能性があるデバイスからのサインイン |30 日 |
| 不規則なサインイン アクティビティ |30 日 |
| 異常なサインイン アクティビティがあるユーザー |30 日 |
| 資格情報が漏洩したユーザー |30 日 |
| 監査レポート |180 日 |
| パスワード リセット アクティビティ (Azure AD) |30 日 |
| パスワード リセット アクティビティ (Identity Manager) |30 日 |
| パスワード リセット登録アクティビティ (Azure AD) |30 日 |
| パスワード リセット登録アクティビティ (Identity Manager) |30 日 |
| セルフ サービス グループ アクティビティ (Azure AD) |30 日 |
| セルフ サービス グループ アクティビティ (Identity Manager) |30 日 |
| アプリケーションの使用状況 |30 日 |
| アカウント プロビジョニングのアクティビティ |30 日 |
| パスワード ロールオーバーの状態 |30 日 |
| アカウント プロビジョニング エラー |30 日 |
| RMS の利用状況 |30 日 |
| 最もアクティブな RMS ユーザー |30 日 |
| RMS デバイスの利用状況 |30 日 |
| RMS 対応アプリケーションの利用状況 |30 日 |

<!---HONumber=AcomDC_0928_2016-->