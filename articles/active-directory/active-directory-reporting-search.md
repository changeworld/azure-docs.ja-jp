---
title: Azure Active Directory のレポート検索
description: Azure Active Directory のセキュリティ、アクティビティ、および監査レポートを検索する方法
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
# Azure Active Directory のレポート検索
*このドキュメントは、[Azure Active Directory レポート ガイド](active-directory-reporting-guide.md)の一部です*。

Azure Active Directory (Azure AD) には、ディレクトリ管理者向けに、複数のレポートにわたってユーザーのセキュリティ、アクティビティ、および監査イベントを検索する機能が用意されています。

検索パネルを表示するには、**Azure クラシック ポータル、Azure Active Directory、[レポート]** の順に移動します。 パネルは、レポートの一覧の上部にあります。

特定のユーザーのアクティビティまたは監査イベントを検索するには、[開始] および [終了] フィールドで日付範囲を選択し、ユーザーの UPN または表示名を入力した後、チェックマーク (OK) ボタンをクリックします。

## 検索に含まれるレポート
検索結果にすべてのレポートが含まれるわけではありません。レポートが検索結果に含まれるかどうかを次の表に示します。

| レポート | あり |
| --- | --- |
| 不明なソースからのサインイン |なし |
| 複数のエラー後のサインイン |なし |
| 複数の地域からのサインイン |なし |
| 不審なアクティビティのある IP アドレスからのサインイン |なし |
| 感染している可能性があるデバイスからのサインイン |なし |
| 不規則なサインイン アクティビティ |なし |
| 異常なサインイン アクティビティがあるユーザー |なし |
| 資格情報が漏洩したユーザー |なし |
| 監査レポート |はい |
| パスワード リセット アクティビティ |はい |
| パスワード リセット登録アクティビティ |はい |
| セルフ サービス グループ アクティビティ |はい |
| アプリケーションの使用状況 |なし |
| アカウント プロビジョニングのアクティビティ |はい |
| パスワード ロールオーバーの状態 |なし |
| アカウント プロビジョニング エラー |なし |
| RMS の利用状況 |なし |
| 最もアクティブな RMS ユーザー |なし |
| RMS デバイスの利用状況 |なし |

## 詳細情報
* [Azure Active Directory レポート](active-directory-view-access-usage-reports.md)
* [Azure Active Directory レポートの監査イベント](active-directory-reporting-audit-events.md)

<!---HONumber=AcomDC_0928_2016-->