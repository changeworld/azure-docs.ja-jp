---
title: Azure AD 属性マッピングのカスタマイズ | Microsoft Docs
description: Azure Active Directory における SaaS アプリの属性マッピングとは何かと、この属性マッピングをビジネス ニーズに合わせて変更する方法について説明します。
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: barbkess
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: accc2e994e7ea361315d5dfb33b257e58410490c
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2018
ms.locfileid: "42143212"
---
# <a name="customizing-user-provisioning-attribute-mappings-for-saas-applications-in-azure-active-directory"></a>Azure Active Directory の SaaS アプリケーションに対するユーザー プロビジョニング属性マッピングのカスタマイズ
Microsoft Azure AD では、Salesforce、Google Apps、およびその他のサードパーティの SaaS アプリケーションへのユーザー プロビジョニングのサポートを提供します。 サード パーティの SaaS アプリケーションでユーザー プロビジョニングを有効にした場合、Azure Portal では属性マッピングの形でその属性値を管理します。

Azure AD ユーザー オブジェクトと各 SaaS アプリのユーザー オブジェクトの間には、構成済みの一連の属性と属性マッピングが存在します。 アプリによっては、ユーザーだけでなくグループなど他のタイプのオブジェクトを管理するものもあります。 <br> 
 既定の属性マッピングをビジネスのニーズに合わせてカスタマイズできます。 つまり、既存の属性マッピングを変更、削除したり、新規の属性マッピングを作成したりすることができます。
 
## <a name="editing-user-attribute-mappings"></a>ユーザー属性マッピングの編集

Azure AD ポータルでこの機能にアクセスするには、**[エンタープライズ アプリケーション]** の **[管理]** セクションの **[プロビジョニング]** にある **[マッピング]** 構成をクリックします。


![Salesforce][5] 

**[マッピング]** 構成をクリックして、関連する **[属性マッピング]** 画面を開きます。 SaaS アプリケーションが正常に機能するために必要となる属性マッピングがあります。 必須の属性の場合は、**[削除]** 機能を使用できません。


![Salesforce][6]  

上の例で、Salesforce の管理オブジェクトの **Username** 属性には、リンクされている Azure Active Directory オブジェクトの **userPrincipalName** 値が設定されています。

既存の **[属性マッピング]** をカスタマイズするには、マッピングをクリックします。 これで **[属性の編集]** 画面が開きます。

![Salesforce][7]  


### <a name="understanding-attribute-mapping-types"></a>属性マッピングの種類の概要
属性マッピングでは、サード パーティの SaaS アプリケーションに属性を設定する方法を管理します。 次の 4 つの異なるマッピングの種類がサポートされます。

* **直接** - ターゲットの属性に、Azure AD でリンクされているオブジェクトの属性値を設定します。
* **定数** – ターゲットの属性に、指定した特定の文字列を設定します。
* **式** - ターゲットの属性を、スクリプトのような式の結果に基づいて設定します。 
  詳細については、「[Azure Active Directory における属性マッピングの式の書き方](active-directory-saas-writing-expressions-for-attribute-mappings.md)」を参照してください。
* **なし** - ターゲットの属性を変更しません。 ただし、ターゲットの属性が空の場合は、指定した既定値が設定されます。

これら 4 つの基本的な種類に加えて、カスタム属性マッピングではオプションの**既定**値の割り当てという概念をサポートします。 既定値の割り当てでは、Azure AD にもターゲット オブジェクトにも値がない場合にも、ターゲットの属性にかならず値を設定します。 最も一般的な構成では、これを空白のままにします。


### <a name="understanding-attribute-mapping-properties"></a>属性マッピングのプロパティの概要

前のセクションでは、属性マッピングの種類プロパティについて紹介しました。
属性マッピングは、このプロパティ以外にも次の属性もサポートしています。

- **ソース属性** - ソース システムのユーザー属性 (例: Azure Active Directory)。
- **対象の属性** - ターゲット システムのユーザー属性 (例: ServiceNow)。
- **この属性を使用してオブジェクトを照合する** - このマッピングを使用して、ソースとターゲットのシステム間でユーザーを一意に識別するかどうかを示します。 通常、これは Azure AD の userPrincipalName または mail 属性で設定され、一般的にターゲット アプリケーションの username フィールドにマップされます。
- **照合の優先順位** - 一致させる属性を複数設定できます。 複数の場合は、このフィールドで定義された順序で評価されます。 1 件でも一致が見つかると、一致する属性の評価はそれ以上行われません。
- **このマッピングを適用する**
    - **常に** - このマッピングをユーザーの作成と更新の両方のアクションに適用します
    - **作成中のみ** - このマッピングをユーザーの作成アクションのみに適用します


## <a name="editing-group-attribute-mappings"></a>グループ属性マッピングの編集

ServiceNow、Box、Google Apps などいくつかのアプリケーションでは、ユーザー オブジェクトに加えてグループ オブジェクトをプロビジョニングする機能もサポートされます。 グループ オブジェクトには、グループ メンバーの他に表示名や電子メール別名などのグループ プロパティを含めることができます。

![ServiceNow][8]  

グループのプロビジョニングは必要に応じて有効と無効を切り替えることができます。これには、**[属性マッピング]** 画面の **[マッピング]** の下でグループ マッピングを選択し、**[有効]** で必要なオプションを設定します。

グループ オブジェクトの一部としてプロビジョニングされる属性は、前に説明したユーザー オブジェクトの場合と同じ方法でカスタマイズできます。 

>[!TIP]
>グループ オブジェクト (プロパティとメンバー) のプロビジョニングは、アプリケーションへの[グループの割り当て](manage-apps/assign-user-or-group-access-portal.md)とは異なる概念です。 グループをアプリケーションに割り当てることができますが、プロビジョニングできるのはグループに含まれるユーザー オブジェクトのみです。 グループ オブジェクト全体のプロビジョニングは、割り当てでグループを使用するためには必要ありません。


## <a name="editing-the-list-of-supported-attributes"></a>サポートされている属性一覧の編集

特定のアプリケーションでサポートされるユーザー属性は事前に構成されています。 ほとんどのアプリケーションのユーザー管理 API ではスキーマ検出はサポートされないため、Azure AD プロビジョニング サービスは、アプリケーションを呼び出して、サポートされる属性の一覧を動的に生成することができません。 

ただし、一部のアプリケーションではカスタム属性がサポートされます。 Azure AD プロビジョニング サービスがカスタム属性の読み取りと書き込みをできるようにするため、**[属性マッピング]** 画面の一番下にある **[詳細オプションの表示]** チェック ボックスを使用し、カスタム属性の定義を Azure Portal に入力する必要があります。

属性一覧のカスタマイズをサポートするアプリケーションとシステムには次のものが含まれます。

* Salesforce
* ServiceNow
* Workday
* Azure Active Directory ([Azure AD Graph API の既定の属性](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity)とカスタム ディレクトリ拡張機能がサポートされる)
* [SCIM 2.0](https://tools.ietf.org/html/rfc7643) をサポートするアプリ ([コア スキーマ](https://tools.ietf.org/html/rfc7643)に定義された属性を追加する必要がある)

>[!NOTE]
>サポートされている属性一覧の編集は、アプリケーションとシステムのスキーマをカスタマイズし、カスタム属性がどのように定義されたかを直接知っている管理者のみにお勧めします。 場合によっては、アプリケーションやシステムで提供される API および開発者ツールに慣れている必要があります。 

![エディター][9]  

サポートされている属性の一覧を編集するときは、次のプロパティが表示されます。

* **[名前]** - ターゲット オブジェクトのスキーマで定義されている属性のシステム名。 
* **[型]** - ターゲット オブジェクトのスキーマで定義されている、属性が格納するデータの型。 これは次のいずれかです。
   * *Binary* - 属性にはバイナリ データが含まれます。
   * *Boolean* - 属性には True または False 値が含まれます。
   * *DateTime* - 属性には日付文字列が含まれます。
   * *Integer* - 属性には整数が含まれます。
   * *Reference* - 属性には、ターゲット アプリケーションの別のテーブルに格納されている値を参照するための ID が含まれます。
   * *String*  - 属性にはテキスト文字列が含まれます。 
* **[Primary Key?]\(主キー\)** - ターゲット オブジェクトのスキーマで属性が主キー フィールドとして定義されているかどうか。
* **必須** - ターゲット アプリケーションまたはシステムに属性を移入する必要があるかどうか。
* **[Multi-value?]\(複数値\)** - 属性が複数値をサポートするかどうか。
* **[Exact case?]\(大文字小文字の区別\)** - 大文字と小文字を区別して属性値を評価するかどうか。
* **[API 式]** - 特定のプロビジョニング コネクタ (Workday など) のドキュメントで指示されていない限り使用しません。
* **[Referenced Object Attribute]\(参照オブジェクト属性\)** - これが Reference 型属性の場合は、このメニューを使用して、この属性に関連付けられている値を含むターゲット アプリケーションのテーブルと属性を選択できます。 たとえば、"Department" という名前の属性があるとき、そこに格納されている値が別の "Departments" テーブルのオブジェクトを参照する場合は、"Departments.Name" を選択します。 特定のアプリケーションでサポートされる参照テーブルとプライマリ ID フィールドは事前に構成されます。現在、Azure Portal を使用して編集することはできませんが、[Graph API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-configure-with-custom-target-attributes) を使用すると編集できます。

新しい属性を追加するには、サポートされる属性一覧の最後までスクロールしてから、提供された入力値を使用して上のフィールドを設定し、**[属性の追加]** を選択します。 属性の追加が終了したら、**[保存]** を選択します。 新しい属性を属性マッピング エディターで使用できるようにするには、**[プロビジョニング]** タブをリロードする必要があります。

## <a name="restoring-the-default-attributes-and-attribute-mappings"></a>既定の属性と属性マッピングの復元

最初からやり直すために、既存のマッピングを既定の状態にリセットする必要がある場合は、**[既定のマッピングを復元する]** チェック ボックスをオンにして構成を保存します。 こうすると、アプリケーションがアプリケーション ギャラリーから Azure AD テナントに追加された直後のようにすべてのマッピングが設定されます。 

このオプションを選択すると、プロビジョニング サービスを実行している際に、すべてのユーザーの再同期が実際に強制されます。 

>[!IMPORTANT]
>このオプションを使用する前に、**[サービス提供の状況]** を **[オフ]** に設定することを強くお薦めします。


## <a name="what-you-should-know"></a>知っておくべきこと

* Microsoft Azure AD では、同期プロセスの効率的な実装を提供します。 初期化された環境では、更新が必要なオブジェクトのみが同期サイクル中に処理されます。 

* 属性マッピングの更新は、同期サイクルのパフォーマンスに影響を与えます。 属性マッピングの構成を更新するには、すべての管理オブジェクトを再評価する必要があります。 

* 推奨されるベスト プラクティスとして、属性マッピングに対する連続的な変更の回数は最小限に抑えてください。


## <a name="next-steps"></a>次の手順

* [Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](active-directory-saas-app-provisioning.md)
* [属性マッピングの式の書き方](active-directory-saas-writing-expressions-for-attribute-mappings.md)
* [ユーザーのプロビジョニング用のフィルターのスコープ](active-directory-saas-scoping-filters.md)
* [SCIM を使用して、Azure Active Directory からアプリケーションへのユーザーとグループの自動プロビジョニングを有効にする](manage-apps/use-scim-to-provision-users-and-groups.md)
* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](saas-apps/tutorial-list.md)

<!--Image references-->
[5]: ./media/active-directory-saas-customizing-attribute-mappings/21.png
[6]: ./media/active-directory-saas-customizing-attribute-mappings/22.png
[7]: ./media/active-directory-saas-customizing-attribute-mappings/23.png
[8]: ./media/active-directory-saas-customizing-attribute-mappings/24.png
[9]: ./media/active-directory-saas-customizing-attribute-mappings/25.PNG

