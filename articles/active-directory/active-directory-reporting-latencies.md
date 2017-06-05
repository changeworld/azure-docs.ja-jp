---
title: "Azure Active Directory レポートの待機時間 | Microsoft Docs"
description: "Azure Active Directory でレポート イベントを表示するのにかかる時間"
services: active-directory
documentationcenter: 
author: dhanyahk
manager: femila
editor: 
ms.assetid: 346b14f8-d16d-4b07-8211-e6c5eec07062
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/04/2017
ms.author: dhanyahk;markvi
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: 9443a00232420d58dea52ed01f31a4ef964a1620
ms.contentlocale: ja-jp
ms.lasthandoff: 05/09/2017


---
# <a name="azure-active-directory-report-latencies"></a>Azure Active Directory レポートの待機時間
*このドキュメントは、[Azure Active Directory レポート ガイド](active-directory-reporting-guide.md)の一部です。*

| レポート | 最小値 | 平均 | 最大値 |
| --- | --- | --- | --- |
| **セキュリティ レポート** | | | |
| 不規則なサインイン アクティビティ |2 時間 |4 時間 |8 時間 |
| 不明なソースからのサインイン |2 時間 |4 時間 |8 時間 |
| 複数のエラー後のサインイン |2 時間 |4 時間 |8 時間 |
| 複数の地域からのサインイン |2 時間 |4 時間 |8 時間 |
| 不審なアクティビティのある IP アドレスからのサインイン |2 時間 |4 時間 |8 時間 |
| 感染している可能性があるデバイスからのサインイン |2 時間 |4 時間 |8 時間 |
| 異常なサインイン アクティビティがあるユーザー |2 時間 |4 時間 |8 時間 |
| 資格情報が漏洩したユーザー |2 時間 |4 時間 |8 時間 |
| すべてのユーザーのサインイン アクティビティ |2 時間 |4 時間 |8 時間 |
| **アプリケーション レポート** | | | |
| アカウント プロビジョニングのアクティビティ |2 時間 |4 時間 |8 時間 |
| アカウント プロビジョニング エラー |2 時間 |4 時間 |8 時間 |
| アプリケーションの使用状況 |2 時間 |4 時間 |8 時間 |
| パスワード ロールオーバーの状態 |2 時間 |4 時間 |8 時間 |
| **監査およびアクティビティ レポート** | | | |
| 監査レポート |1 分 |約 15 分 |30 分 |
| パスワード リセット アクティビティ (Azure AD) |2 時間 |4 時間 |8 時間 |
| パスワード リセット アクティビティ (Identity Manager) |2 時間 |4 時間 |8 時間 |
| パスワード リセット登録アクティビティ (Azure AD) |2 時間 |4 時間 |8 時間 |
| パスワード リセット登録アクティビティ (Identity Manager) |2 時間 |4 時間 |8 時間 |
| セルフ サービス グループ アクティビティ (Azure AD) |2 時間 |4 時間 |8 時間 |
| セルフ サービス グループ アクティビティ (Identity Manager) |2 時間 |4 時間 |8 時間 |
| **RMS レポート** | | | |
| 最もアクティブな RMS ユーザー |2 時間 |4 時間 |8 時間 |
| RMS の利用状況 |2 時間 |4 時間 |8 時間 |
| RMS デバイスの利用状況 |2 時間 |4 時間 |8 時間 |
| RMS 対応アプリケーションの利用状況 |2 時間 |4 時間 |8 時間 |


