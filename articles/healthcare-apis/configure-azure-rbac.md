---
title: Azure RBAC for FHIR サービスの構成 - Azure Healthcare API
description: この記事では、Azure RBAC for FHIR を構成する方法について説明します。
author: SteveWohl
ms.service: healthcare-apis
ms.topic: tutorial
ms.date: 11/17/2021
ms.author: zxue
ms.openlocfilehash: b43127a483e9935fe2212f85ab730d344815db07
ms.sourcegitcommit: 81a1d2f927cf78e82557a85c7efdf17bf07aa642
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/19/2021
ms.locfileid: "132814741"
---
# <a name="configure-azure-rbac-for-healthcare-apis"></a>Azure RBAC for Healthcare API の構成

> [!IMPORTANT]
> Azure Healthcare APIs は現在プレビュー段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」に記載されています。

この記事では、Azure ロールベースのアクセス制御 [(Azure RBAC)](../role-based-access-control/index.yml) を使用して、Healthcare API データ プレーンへのアクセスを割り当てる方法について説明します。 データ プレーンのユーザーが Azure サブスクリプションに関連付けられている Azure Active Directory テナントで管理されている場合は、Azure RBAC を使用してデータ プレーンのアクセス権を割り当てることをお勧めします。

ロールの割り当ては、次の手順でAzure portal。 FHIR サービスと DICOM サービスでは、異なるアプリケーション ロールが定義されています。 ユーザー アクセス制御を管理するための 1 つ以上のロールを追加または削除します。

## <a name="assign-roles-for-the-fhir-service"></a>FHIR サービスのロールを割り当てる

ユーザー、サービス プリンシパル、またはグループに FHIR データ プレーンへのアクセスを許可するには、次のページから FHIR サービスAzure portal。 [ **アクセス制御 (IAM)] を選択** し、[ロールの割り当て **] タブを選択** します。[+ **追加] を選択** し、[ロールの割り当 **ての追加] を選択します**。
 
ロールの割り当てオプションがグレー表示されている場合は、Azure サブスクリプション管理者に、サブスクリプションまたはリソース グループ ("ユーザー アクセス管理者" など) へのアクセス許可を付与するように求める。 Azure 組み込みロールの詳細については、「Azure 組み込み [ロール」を参照してください](../role-based-access-control/built-in-roles.md)。

[![アクセス制御ロールの割り当て。 ](fhir/media/rbac/role-assignment.png) ](fhir/media/rbac/role-assignment.png#lightbox)

[ロール] の選択で、FHIR データ プレーンの組み込みロール ("FHIR データ共同作成者" など) のいずれかを検索します。 以下の他のロールを選択できます。

* **FHIR データ リーダー**: FHIR データを読み取り (および検索) できます。
* **FHIR データ ライター: FHIR** データの読み取り、書き込み、およびソフト削除が可能です。
* **FHIR Data Exporter:** データの読み取りおよびエクスポート ($export操作) できます。
* **FHIR データ共同作成者**: すべてのデータ プレーン操作を実行できます。
* **FHIR データ コンバーター**: コンバーターを使用してデータ変換を実行できます

[選択 **] セクション** で、クライアント アプリケーションの登録名を入力します。 名前が見つかった場合は、アプリケーション名が一覧表示されます。 アプリケーション名を選択し、 [保存] を **選択します**。 

クライアント アプリケーションが見つからない場合は、アプリケーションの登録を確認して、名前が正しいか確認します。 クライアント アプリケーションが、Azure Healthcare API の FHIR サービス (ここでは FHIR サービスと呼ばれる) がデプロイされているのと同じテナントに作成されます。


[![[ロールの割り当て] を選択します。 ](fhir/media/rbac/select-role-assignment.png) ](fhir/media/rbac/select-role-assignment.png#lightbox)

ロールの割り当てを確認するには、[アクセス制御 **(IAM)]** メニュー オプションから [ロールの割り当て] タブを選択します。
 
## <a name="assign-roles-for-the-dicom-service"></a>DICOM サービスのロールを割り当てる

DICOM データ プレーンへのアクセス権をユーザー、サービス プリンシパル、またはグループに付与するには、 **[アクセス制御 (IAM)]** ブレードを選択します。 **[ロールの割り当て]** タブを選択し、 **[+ 追加]** を選択します。

[ ![DICOM アクセス制御](dicom/media/dicom-access-control.png) ](dicom/media/dicom-access-control.png#lightbox)

**[ロール]** の選択で、DICOM データ プレーンの組み込みのロールのいずれかを検索します。

[ ![RBAC ロールの割り当てを追加します。](dicom/media/rbac-add-role-assignment.png) ](dicom/media/rbac-add-role-assignment.png#lightbox)

次のいずれかを選択できます。

* DICOM データ所有者: DICOM データへのフル アクセス。
* DICOM データ閲覧者: DICOM データの読み取りおよび検索。

これらのロールがニーズに十分ではない場合は、PowerShell を使用してカスタムのロールを作成することができます。  カスタム ロールの作成の詳細については、[Azure PowerShell を使用したカスタムロールの作成](../role-based-access-control/custom-roles-powershell.md)に関するページをご覧ください。

**[選択]** ボックスで、ロールを割り当てるユーザー、サービス プリンシパル、またはグループを検索します。

> [!NOTE]
> アプリケーションまたは他のツールで FHIR または DICOM サービスにアクセスできない場合は、ロールの割り当てがシステムでの伝達を完了するまで、さらに数分待つ必要があります。

## <a name="next-steps"></a>次の手順

この記事では、FHIR サービスと DICOM サービスに Azure ロールを割り当てる方法について説明しました。 Postman を使用して医療 API にアクセスする方法については、以下を参照してください。

- [Postman を使用したアクセス](use-postman.md)
- [REST クライアントを使用したアクセス](using-rest-client.md)
- [cURL を使用したアクセス](using-curl.md)
