---
title: インクルード ファイル
description: インクルード ファイル
services: active-directory
author: curtand
ms.service: active-directory
ms.topic: include
ms.date: 07/30/2018
ms.author: curtand
ms.custom: include file
ms.openlocfilehash: a2055c3f00306fbfdad3028a16bb49d919e1e251
ms.sourcegitcommit: 99a6a439886568c7ff65b9f73245d96a80a26d68
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2018
ms.locfileid: "39361457"
---
ここでは、Azure Active Directory (Azure AD) サービスの使用上の制約およびその他のサービスの制限を説明します。

| Category | 制限 |
| --- | --- |
| ディレクトリ | 1 人のユーザーは、最大で 500 の Azure AD ディレクトリにゲストまたはメンバーとして属することができます。<br/>1 人のユーザーは、最大で 20 個のディレクトリを作成できます。 |
| ドメイン | 追加できるマネージド ドメイン名は 900 個以下です。 オンプレミス Active Directory とのフェデレーションをすべてのドメインに設定する場合、各ディレクトリに追加できるドメイン名は 450 個以下です。 |
| オブジェクト |<ul><li>Azure Active Directory の Free エディションのユーザーは、1 つのディレクトリに最大 500,000 個のオブジェクトを作成できます。</li><li>管理者以外のユーザーは、最大 250 個のオブジェクトを作成できます。</li></ul> |
| スキーマの拡張機能 |<ul><li>文字列型の拡張の最大文字数は 256 文字です。 </li><li>バイナリ型の拡張の最大バイト数は 256 バイトです。</li><li>1 つのオブジェクトに対して、(すべての型とすべてのアプリケーションで合計) 100 個の拡張値を書き込むことができます。</li><li>"文字列" 型または "バイナリ" 型の単一値の属性を使用して拡張できるのは、"User"、"Group"、"TenantDetail"、"Device"、"Application"、および "ServicePrincipal" エンティティのみです。</li><li>スキーマ拡張は、Graph API-version 1.21-preview でのみ使用できます。 拡張を登録するには、アプリケーションに書き込みアクセス権を付与する必要があります。</li></ul> |
| [アプリケーション] |最大 100 人のユーザーが 1 つのアプリケーションの所有者になれます。 |
| グループ |<ul><li>最大 100 人のユーザーが 1 つのグループの所有者になれます。</li><li>1 つの Azure Active Directory グループのメンバーとして登録できるオブジェクト数に制限はありません。</li><li>Azure AD Connect を使用してオンプレミスの Active Directory から Azure Active Directory に同期できるグループ内のメンバーの数は、50,000 ユーザーに制限されています。</li></ul> |
| アクセス パネル |<ul><li>Azure AD Premium または Enterprise Mobility Suite のライセンスが割り当てられているユーザーの場合、各エンド ユーザーのアクセス パネルに表示できるアプリケーション数に制限はありません。</li><li>Azure Active Directory の Free または Azure AD Basic エディションのライセンスが割り当てられているユーザーは、最大 10 個のアプリ タイル (例: Box、Salesforce、または Dropbox) を各エンド ユーザーのアクセス パネルに表示できます。 この制限は、管理者アカウントには適用されません。</li></ul> |
| レポート | いずれのレポートでも、最大 1,000 行を表示またはダウンロードできます。 それを超えるデータは切り捨てられます。 |
| 管理単位 | オブジェクトは最大 30 個の管理単位のメンバーにすることができます。 |
