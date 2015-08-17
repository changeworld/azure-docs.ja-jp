<properties
	pageTitle="属性マッピングのカスタマイズ"
	description="Azure Active Directory における SaaS アプリの属性マッピングとは何かと、この属性マッピングをビジネス ニーズに合わせて変更する方法について説明します。"
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="swadhwa"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/27/2015"
	ms.author="markusvi"/>


# 属性マッピングのカスタマイズ


Microsoft Azure AD では、Salesforce、Google Apps、およびその他のサードパーティの SaaS アプリケーションへのユーザー プロビジョニングのサポートを提供します。サード パーティの SaaS アプリケーションでユーザー プロビジョニングを有効にした場合、Azure 管理ポータルでは「属性マッピング」と呼ばれる構成の形でその属性値を管理します。

Azure AD ユーザー オブジェクトと各 SaaS アプリのユーザー オブジェクトの間には、構成済みの一連の属性マッピングが存在します。アプリによっては、グループや連絡先といった他のタイプのオブジェクトを管理するものもあります。<br>既定の属性マッピングをビジネスのニーズに合わせてカスタマイズできます。つまり、既存の属性マッピングを変更、削除したり、新規の属性マッピングを作成したりすることができます。

Azure AD ポータルでこの機能にアクセスするには、SaaS アプリケーションのツールバーで [属性] をクリックします。

> [AZURE.NOTE]**[属性]** リンクは SaaS アプリケーションでユーザー プロビジョニングを有効にしている場合にのみ使用できます。


![Salesforce][1]


ツールバーで [属性] をクリックすると、SaaS アプリケーションで現在構成されているマッピングのリストが表示されます。

次のスクリーンショットはその例です。



![Salesforce][2]


上の例で、Salesforce の管理オブジェクトの **firstName** 属性には、リンクさている Azure AD オブジェクトの **givenName** 値が設定されています。

属性マッピングをカスタマイズする場合、またはカスタマイズした設定を既定の構成に戻す場合は、アプリケーション下部のツールバーで関連するボタンをクリックします。


![Salesforce][3]


SaaS アプリケーションが正常に機能するために必要となる属性マッピングがあります。属性テーブルでは、関連する属性マッピングの **[必須]** 属性の値が **[はい]** になっています。属性マッピングが必須の場合は削除できません。この場合は、**削除**機能を使うことはできません。

既存の属性マッピングを変更するには、マッピングを選択して、**[編集]** をクリックするだけです。すると、選択した属性マッピングを変更できるダイアログ ページが表示されます。


![属性マッピングの編集][4]



## 属性マッピングの種類について


属性マッピングでは、サードパーティの SaaS アプリケーションに属性を設定する方法を管理します。次の 4 つの異なるマッピングの種類がサポートされます。

- **直接** - ターゲットの属性に、Azure AD でリンクされているオブジェクトの属性値を設定します。


- **定数** – ターゲットの属性に、指定した特定の文字列を設定します。


- **式** - ターゲットの属性を、スクリプトのような式の結果に基づいて設定します。詳細については、「[Azure Active Directory での属性マッピングのための式の作成](active-directory-saas-writing-expressions-for-attribute-mappings.md)」を参照してください。


- **なし** - ターゲットの属性を変更しません。ただし、ターゲットの属性が空の場合は、指定した既定値が設定されます。



これら 4 つの基本的な属性マッピングの種類に加えて、カスタム属性マッピングでは**既定**値の割り当てという概念をサポートします。既定値の割り当てでは、Azure AD にもターゲット オブジェクトにも値がない場合にも、ターゲットの属性にかならず値を設定します。

Microsoft Azure AD では、同期プロセスの非常に効率的な実装を提供します。初期化された環境では、更新が必要なオブジェクトのみが同期サイクル中に処理されます。属性マッピングの更新は、同期サイクルのパフォーマンスに影響を与えます。これは、属性マッピングの構成を更新するには、すべての管理オブジェクトを再評価する必要があるためです。このため、推奨されるベスト プラクティスとして、属性マッピングに対する連続的な変更の回数は最小限に抑えてください。



[AZURE.INCLUDE [saas-toc](../../includes/active-directory-saas-toc.md)]

<!--Image references-->
[1]: ./media/active-directory-saas-customizing-attribute-mappings/ic765497.png
[2]: ./media/active-directory-saas-customizing-attribute-mappings/ic775419.png
[3]: ./media/active-directory-saas-customizing-attribute-mappings/ic775420.png
[4]: ./media/active-directory-saas-customizing-attribute-mappings/ic775421.png

<!---HONumber=August15_HO6-->