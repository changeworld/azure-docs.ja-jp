---
title: Azure AD での機密性の高い操作のレポート ブック | Microsoft Docs
description: 機密性の高い操作のレポート ブックを使用する方法について説明します。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: karenho
editor: ''
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/05/2021
ms.author: markvi
ms.reviewer: sarbar
ms.collection: M365-identity-device-management
ms.openlocfilehash: a44aa07539be94aa645c95a6cee544bf92468bd7
ms.sourcegitcommit: 5af89a2a7b38b266cc3adc389d3a9606420215a9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2021
ms.locfileid: "131990517"
---
# <a name="sensitive-operations-report-workbook"></a>機密性の高い操作のレポート ブック

IT 管理者は、環境内の侵害を識別して、確実に環境を正常な状態に維持できる必要があります。 

機密性の高い操作のレポート ブックは、環境内の侵害を示す可能性のある、疑わしいアプリケーションやサービス プリンシパルのアクティビティを識別する助けとなることを目的としています。


この記事では、このブックの概要を紹介します。


## <a name="description"></a>説明

![ブックのカテゴリ](./media/workbook-sensitive-operations-report/workbook-category.png)

このブックは、テナント内で実行されてきた、サービス プリンシパルの侵害である可能性のある、機密性の高い最近の操作を識別するものです。

組織で Azure Monitor ブックを使用したことがない場合は、ブックにアクセスする前に、Azure AD のサインインと監査のログを Azure Monitor と統合する必要があります。 これにより、最長で 2 年間、ブックを使用してログの格納、クエリの実行、視覚化を行うことができます。 Azure Monitor との統合後に作成されたサインイン イベントと監査イベントだけが格納されるため、ブックには、その日付より前の分析情報は含まれません。 Azure Active Directory のための、Azure Monitor ブックに対する前提条件についてさらに学習してください。 以前に Azure AD サインインと監査ログを Azure Monitor と統合している場合は、ブックを使用して過去の情報を評価できます。 
 
 

## <a name="sections"></a>セクション

このブックは、4 つのセクションに分割されています。

![ブックのセクション](./media/workbook-sensitive-operations-report/workbook-sections.png)


- **変更されたアプリケーションとサービス プリンシパルの資格情報および認証方法** - このレポートでは、最近多くのサービス プリンシパル資格情報を変更したアクターと、各種類のサービス プリンシパル資格情報がいくつ変更されたかにフラグが設定されます。

- **サービス プリンシパルに付与された新しいアクセス許可** - このブックでは、サービス プリンシパルに対して最近付与された OAuth 2.0 アクセス許可も強調表示されます。 

- **サービス プリンシパルのディレクトリ ロールとグループ メンバーシップの更新**



- **変更されたフェデレーション設定** - このレポートでは、ユーザーまたはアプリケーションにより、ドメインでのフェデレーション設定が変更される場合が強調表示されます。 たとえば、署名証明書など、Active Directory フェデレーション サービス (ADFS) の新しい TrustedRealm オブジェクトがドメインに追加された場合が報告されます。 ドメインのフェデレーション設定に対する変更は、めったにないはずです。 




### <a name="modified-application-and-service-principal-credentialsauthentication-methods"></a>変更されたアプリケーションとサービス プリンシパルの資格情報および認証方法

攻撃者が環境内で永続化を獲得する最も一般的な方法の 1 つは、既存のアプリケーションやサービス プリンシパルに新しい資格情報を追加する方法です。 資格情報があれば、攻撃者は標的のアプリケーションまたはサービス プリンシパルとして認証して、それらに、アクセス許可を持つすべてのリソースへのアクセスを付与できます。

このセクションには、検出に役立つ以下のデータが含まれています。

- 資格情報の種類を含め、アプリやサービス プリンシパルに追加された新しい資格情報すべて

- 上位のアクターと、それらが実行した資格情報の変更の量

- すべての資格情報変更のタイムライン



### <a name="new-permissions-granted-to-service-principals"></a>サービス プリンシパルに付与された新しいアクセス許可

攻撃者は、アクセスの取得に悪用する、高特権の一連のアクセス許可を持つサービス プリンシパルやアプリケーションを見つけられないと、多くの場合、別のサービス プリンシパルまたはアプリにアクセス許可を追加しようと試みます。

このセクションには、既存のサービス プリンシパルに AppOnly アクセス許可が付与された内訳が含まれています。 管理者は、Exchange Online、Microsoft Graph、Azure AD Graph を始めとして、過剰に高いアクセス許可が付与されているすべてのインスタンスを調査する必要があります。


### <a name="directory-role-and-group-membership-updates-for-service-principals"></a>サービス プリンシパルのディレクトリ ロールとグループ メンバーシップの更新 

攻撃者が既存のサービス プリンシパルやアプリケーションに新しいアクセス許可を追加する論理に従えば、もう 1 つのアプローチとなるのは、それらを既存のディレクトリ ロールまたはグループに追加することです。

このセクションには、サービス プリンシパルのメンバーシップに加えられたすべての変更の概要が含まれていて、高い特権を持つロールとグループへの追加はすべて確認する必要があります。



### <a name="modified-federation-settings"></a>変更されたフェデレーション設定

環境内に長期的な足がかりを得るための別の一般的なアプローチは、次のとおりです。

- テナントのフェデレーション ドメインでの信頼を変更する。
- 攻撃者が制御する SAML IDP を、信頼されている認証ソースとして追加する。 

このセクションには以下のデータが含まれています。

- ドメイン フェデレーションによる既存の信頼に対して実行された変更

- 新しいドメインや信頼の追加


  


## <a name="filters"></a>フィルタ

この段落では、各セクションでサポートされているフィルターの一覧を示します。


### <a name="modified-application-and-service-principal-credentialsauthentication-methods"></a>変更されたアプリケーションとサービス プリンシパルの資格情報および認証方法

- 時間の範囲
- 操作名
- 資格情報
- Actor
- アクターの除外


### <a name="new-permissions-granted-to-service-principals"></a>サービス プリンシパルに付与された新しいアクセス許可

- 時間の範囲
- クライアント アプリ
- リソース

### <a name="directory-role-and-group-membership-updates-to-service-principals"></a>サービス プリンシパルに対するディレクトリ ロールとグループ メンバーシップの更新

- 時間の範囲
- 操作
- 開始しているユーザーまたはアプリ

### <a name="modified-federation-settings"></a>変更されたフェデレーション設定

- 時間の範囲
- 操作
- 開始しているユーザーまたはアプリ




## <a name="best-practices"></a>ベスト プラクティス


**以下を使用してください。**
 
- **変更されたアプリケーションとサービス プリンシパルの資格情報の変更** によって、組織で使用頻度が低いサービス プリンシパルに追加されようとしている資格情報を見つけます。 このセクションに記載されたフィルターを利用して、変更された疑いのあるアクターやサービス プリンシパルがないかさらに調査します。


- **サービス プリンシパルに付与された新しいアクセス許可** によって、侵害される可能性があるサービス プリンシパルにアクターによって追加されようとしている、広範であったり過度であったりするアクセス許可を見つけます。  

- **変更されたフェデレーション設定** セクションによって、ターゲット ドメインまたは URL の追加や変更が、管理上の正当な操作であることを確認ます。 ドメイン フェデレーションの信頼を変更または追加するアクションはまれであり、確かに適正であることをできるだけ早く調査するアクションとして扱う必要があります。





## <a name="next-steps"></a>次の手順

- [Azure AD ブックの使用方法](howto-use-azure-monitor-workbooks.md)
