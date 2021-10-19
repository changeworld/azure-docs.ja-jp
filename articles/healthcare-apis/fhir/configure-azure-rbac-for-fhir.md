---
title: Azure RBAC for FHIR サービスの構成 - Azure Healthcare API
description: この記事では、Azure RBAC for FHIR を構成する方法について説明します。
author: ginalee-dotcom
ms.service: healthcare-apis
ms.topic: tutorial
ms.date: 09/10/2021
ms.author: zxue
ms.openlocfilehash: ff2a488a7ed8a693f23b533242748eed400bd802
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124782497"
---
# <a name="configure-azure-rbac-for-the-fhir-service"></a>FHIR サービス用に Azure RBAC を構成する

> [!IMPORTANT]
> Azure Healthcare APIs は現在プレビュー段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」に記載されています。

この記事では、Azure ロールベースのアクセス制御 [(Azure RBAC)](../../role-based-access-control/index.yml) を使用して、Healthcare API データ プレーンへのアクセスを割り当てる方法について説明します。 データ プレーンのユーザーが Azure サブスクリプションに関連付けられている Azure Active Directory テナントで管理されている場合は、Azure RBAC を使用してデータ プレーンのアクセス権を割り当てることをお勧めします。 

## <a name="assign-roles"></a>ロールを割り当てる

ユーザー、サービス プリンシパル、またはグループに FHIR データ プレーンへのアクセスを許可するには、次のページから FHIR サービスAzure portal。 [ **アクセス制御 (IAM)] を選択** し、[ロールの割り当て **] タブを選択** します。[+ **追加] を選択** し、[ロールの割り当 **ての追加] を選択します**。
 
ロールの割り当てオプションがグレー表示されている場合は、Azure サブスクリプション管理者に、サブスクリプションまたはリソース グループ ("ユーザー アクセス管理者" など) へのアクセス許可を付与するように求める必要があります。 Azure 組み込みロールの詳細については、「Azure 組み込み [ロール」を参照してください](../../role-based-access-control/built-in-roles.md)。

[![アクセス制御ロールの割り当て。 ](media/rbac/role-assignment.png) ](media/rbac/role-assignment.png#lightbox)

[ロール] の選択で、FHIR データ プレーンの組み込みロール ("FHIR データ共同作成者" など) のいずれかを検索します。 以下の他のロールを選択できます。

* **FHIR データ リーダー**: FHIR データを読み取り (および検索) できます。
* **FHIR データ ライター: FHIR** データの読み取り、書き込み、およびソフト削除が可能です。
* **FHIR Data Exporter:** データの読み取りおよびエクスポート ($export) できます。
* **FHIR データ共同作成者**: すべてのデータ プレーン操作を実行できます。
* **FHIR データ コンバーター**: コンバーターを使用してデータ変換を実行できます

[選択 **] セクション** で、クライアント アプリケーションの登録名を入力します。 名前が見つかった場合は、アプリケーション名が一覧表示されます。 アプリケーション名を選択し、 [保存] を **選択します**。 

クライアント アプリケーションが見つからない場合は、アプリケーションの登録を確認して、名前が正しいか確認します。 クライアント アプリケーションが、Azure Healthcare API の FHIR サービス (ここでは FHIR サービスと呼ばれる) がデプロイされているのと同じテナントに作成されます。


[![[ロールの割り当て] を選択します。 ](media/rbac/select-role-assignment.png) ](media/rbac/select-role-assignment.png#lightbox)

ロールの割り当てを確認するには、[アクセス制御 **(IAM)]** メニュー オプションから [ロールの割り当て] タブを選択します。
 

> [!NOTE]
> ロールの割り当てがシステムに反映されるのに数分かかる場合があります。 アプリケーションまたは他のテスト ツールで FHIR サービスにアクセスできない場合は、数分待ちます。 また、アプリケーション登録で Azure Healthcare api にuser_impersonationアクセス許可が付与されていないことを確認します。

## <a name="next-steps"></a>次の手順

この記事では、FHIR データ プレーンに Azure ロールを割り当てる方法について説明しました。 Postman を使用して FHIR サービスにアクセスする方法については、以下を参照してください。

>[!div class="nextstepaction"]
>[Postman を使用して FHIR サービスにアクセスする](../use-postman.md)