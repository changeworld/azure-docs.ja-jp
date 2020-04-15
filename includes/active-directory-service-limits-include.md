---
title: インクルード ファイル
description: インクルード ファイル
services: active-directory
author: curtand
ms.service: active-directory
ms.topic: include
ms.date: 02/28/2020
ms.author: curtand
ms.custom: include file
ms.openlocfilehash: d906a3dd072770a05b818fd3ca8de359b8427728
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/09/2020
ms.locfileid: "80986601"
---
ここでは、Azure Active Directory (Azure AD) サービスの使用上の制約およびその他のサービスの制限を説明します。

| カテゴリ | 制限 |
| --- | --- |
| ディレクトリ | 1 人のユーザーは、最大で 500 の Azure AD ディレクトリにゲストまたはメンバーとして属することができます。<br/>1 人のユーザーは、最大で 200 個のディレクトリを作成できます。 |
| ドメイン | 追加できるマネージド ドメイン名は 900 個以下です。 オンプレミス Active Directory とのフェデレーションをすべてのドメインに設定する場合、各ディレクトリに追加できるドメイン名は 450 個以下です。 |
|リソース |<ul><li>Azure Active Directory の Free エディションのユーザーは、既定で 1 つのディレクトリに最大 50,000 個の Azure AD リソースを作成できます。 検証済みドメインが少なくとも 1 つある場合は、組織の既定の Azure AD サービス クォータは 300,000 個の Azure AD リソースに拡張されます。 このサービス制限は、Azure AD の価格ページに記載されている 500,000 リソースの価格レベル制限とは関係ありません。 既定のクォータを超えるためには、Microsoft サポートに連絡する必要があります。</li><li>管理者以外のユーザーは、最大 250 個の Azure AD リソースを作成できます。 アクティブ リソースと復元可能な削除済みリソースの両方が、このクォータに加算されます。 30 日未満に削除された削除済み Azure AD リソースのみが復元可能です。 復元できなくなった削除済み Azure AD リソースは、30 日間、 4 分の 1 の値でこのクォータに加算されます。 通常の作業で、このクォータを繰り返し超過する可能性のある開発者がいる場合は、無制限の数のアプリ登録を作成できる権限を持った[カスタムロールを作成して割り当てる](../articles/active-directory/users-groups-roles/roles-quickstart-app-registration-limits.md)こともできます。</li></ul> |
| スキーマの拡張機能 |<ul><li>文字列型の拡張の最大文字数は 256 文字です。 </li><li>バイナリ型の拡張は 256 バイトに制限されます。</li><li>1 つの Azure AD リソースに対して書き込める拡張値は、(*すべて*の型と*すべて*のアプリケーションで合計) 100 個のみです。</li><li>文字列型またはバイナリ型の単一値の属性を使用して拡張できるのは、User、Group、TenantDetail、Device、Application、および ServicePrincipal エンティティのみです。</li><li>スキーマ拡張は、Graph API バージョン 1.21 プレビューでのみ使用できます。 拡張を登録するには、アプリケーションに書き込みアクセス権を付与する必要があります。</li></ul> |
| アプリケーション |最大 100 人のユーザーが 1 つのアプリケーションの所有者になれます。 |
|アプリケーション マニフェスト |アプリケーション マニフェストには、最大で 1200 のエントリを追加できます。 |
| グループ |<ul><li>ユーザーは、Azure AD 組織に最大 250 個のグループを作成できます。</li><li>Azure AD 組織には、最大 5000 個の動的グループを含めることができます。<li>最大 100 人のユーザーが 1 つのグループの所有者になれます。</li><li>任意の数の Azure AD リソースが 1 つのグループのメンバーになることができます。</li><li>ユーザーは任意の数のグループのメンバーになることができます。</li><li>Azure AD Connect を使用してオンプレミスの Active Directory から Azure Active Directory に同期できるグループ内のメンバーの数は、50,000 ユーザーに制限されています。</li><li>Azure AD の入れ子になったグループは、すべてのシナリオにおいてサポートされていません</li></ul><br/> 現時点で、入れ子になったグループでサポートされるシナリオを次に示します。<ul><li> 1 つのグループを別のグループのメンバーとして追加することができるため、グループの入れ子を実現できます。</li><li> グループ メンバーシップ要求 (アプリがトークンでグループ メンバーシップ要求を受信するように構成されている場合は、サインインしているユーザーがメンバーになっている入れ子になったグループが含まれます)</li><li>条件付きアクセス (条件付きアクセス ポリシーをグループにスコープ設定する場合)</li><li>セルフサービスのパスワード リセットへのアクセスの制限</li><li>Azure AD Join とデバイス登録を実行できるユーザーの制限</li></ul><br/>次のシナリオでは、入れ子になったグループはサポートされません。<ul><li> アプリ ロールの割り当て (アプリへのグループの割り当てはサポートされていますが、直接割り当てられたグループ内で入れ子になっているグループにはアクセス権はありません)。アクセス権とプロビジョニングの両方</li><li>グループベースのライセンス (グループのすべてのメンバーにライセンスを自動的に割り当てます)</li><li>Office 365 グループ。</li></ul> |
| アプリケーション プロキシ | <ul><li>アプリケーション プロキシ アプリケーションごとに 1 秒あたり最大 500 件のトランザクション</li><li>Azure AD 組織に対する 1 秒あたり最大 750 件のトランザクション</li></ul><br/>トランザクションは、単一の http 要求と一意のリソースの応答として定義されます。 スロットルが行われた場合、クライアントでは 429 応答 (要求が多すぎます) を受信します。 |
| アクセス パネル |<ul><li>各ユーザーのアクセス パネルに表示できるアプリケーション数に制限はありません。 これは、Azure AD Premium または Enterprise Mobility Suite のライセンスが割り当てられているユーザーに適用されます。</li><li>最大で 10 個のアプリ タイルを各ユーザーのアクセス パネルに表示できます。 この制限は、Azure AD Free ライセンス プランのライセンスが割り当てられているユーザーに適用されます。 アプリ タイルの例として、Box、Salesforce、Dropbox が挙げられます。 この制限は、管理者アカウントには適用されません。</li></ul> |
| Reports | いずれのレポートでも、最大 1,000 行を表示またはダウンロードできます。 それを超えるデータは切り捨てられます。 |
| 管理単位 | Azure AD リソースは最大 30 個の管理単位のメンバーにすることができます。 |
| 管理者ロールとアクセス許可 | <ul><li>グループを[所有者](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context#object-ownership)として追加することはできません。</li><li>グループを[ロール](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)に割り当てることはできません。</li><li>管理者以外の全ユーザーがすべてのディレクトリ情報にアクセスできないよう、他のユーザーがディレクトリ情報を読み取ることができるユーザーのアクセス許可は、Azure AD 組織全体での切り替え以外の方法で制限することはできません (推奨されません)。 既定のアクセス許可の詳細については、[ここ](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context#to-restrict-the-default-permissions-for-member-users)を参照してください。</li><li>管理者ロールのメンバーシップの追加や失効が有効になるには、最大 15 分またはサインアウト/サインインが必要になることがあります。</li></ul> |
