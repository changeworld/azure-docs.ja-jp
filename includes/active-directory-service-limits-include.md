---
title: インクルード ファイル
description: インクルード ファイル
services: active-directory
author: curtand
ms.service: active-directory
ms.topic: include
ms.date: 02/19/2019
ms.author: curtand
ms.custom: include file
ms.openlocfilehash: 619dd7f3b01e2e7ce71e945fce77aa73cb87f264
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/20/2019
ms.locfileid: "56443339"
---
ここでは、Azure Active Directory (Azure AD) サービスの使用上の制約およびその他のサービスの制限を説明します。

| Category | 制限 |
| --- | --- |
| ディレクトリ | 1 人のユーザーは、最大で 500 の Azure AD ディレクトリにゲストまたはメンバーとして属することができます。<br/>1 人のユーザーは、最大で 20 個のディレクトリを作成できます。 |
| ドメイン | 追加できるマネージド ドメイン名は 900 個以下です。 オンプレミス Active Directory とのフェデレーションをすべてのドメインに設定する場合、各ディレクトリに追加できるドメイン名は 450 個以下です。 |
| オブジェクト |<ul><li>Azure Active Directory の Free エディションのユーザーは、1 つのディレクトリに最大 500,000 個のオブジェクトを作成できます。</li><li>管理者以外のユーザーは、最大 250 個のオブジェクトを作成できます。</li></ul> |
| スキーマの拡張機能 |<ul><li>文字列型の拡張の最大文字数は 256 文字です。 </li><li>バイナリ型の拡張の最大バイト数は 256 バイトです。</li><li>1 つのオブジェクトに対して、(すべての型とすべてのアプリケーションで合計) 100 個の拡張値を書き込むことができます。</li><li>"文字列" 型または "バイナリ" 型の単一値の属性を使用して拡張できるのは、"User"、"Group"、"TenantDetail"、"Device"、"Application"、および "ServicePrincipal" エンティティのみです。</li><li>スキーマ拡張は、Graph API-version 1.21-preview でのみ使用できます。 拡張を登録するには、アプリケーションに書き込みアクセス権を付与する必要があります。</li></ul> |
| [アプリケーション] |最大 100 人のユーザーが 1 つのアプリケーションの所有者になれます。 |
| グループ |<ul><li>最大 100 人のユーザーが 1 つのグループの所有者になれます。</li><li>任意の数のオブジェクトが 1 つのグループのメンバーになることができます。</li><li>ユーザーは任意の数のグループのメンバーになることができます。</li><li>Azure AD Connect を使用してオンプレミスの Active Directory から Azure Active Directory に同期できるグループ内のメンバーの数は、50,000 ユーザーに制限されています。</li></ul> |
| アクセス パネル |<ul><li>Azure AD Premium または Enterprise Mobility Suite のライセンスが割り当てられているユーザーの場合、各エンド ユーザーのアクセス パネルに表示できるアプリケーション数に制限はありません。</li><li>最大 10 個のアプリ タイル (例: Box、Salesforce、または Dropbox) を、Azure Active Directory の Free または Azure AD Basic エディションのライセンスが割り当てられているユーザーに対する各エンド ユーザーのアクセス パネルに表示できます。 この制限は、管理者アカウントには適用されません。</li></ul> |
| Reports | いずれのレポートでも、最大 1,000 行を表示またはダウンロードできます。 それを超えるデータは切り捨てられます。 |
| 管理単位 | オブジェクトは最大 30 個の管理単位のメンバーにすることができます。 |
| 管理者ロールとアクセス許可 | <li>グループを所有者として追加することはできません<li>グループをロールに割り当てることはできません<li>テナントのスイッチ (Azure AD でのユーザー設定) を超えて、既定のユーザー アクセス許可を変更することはできません |
