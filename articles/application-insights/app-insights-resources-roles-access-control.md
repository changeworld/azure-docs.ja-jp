---
title: "Application Insights のリソース、役割、アクセス制御"
description: "組織の insights の所有者、共同作成者、閲覧者。"
services: application-insights
documentationcenter: 
author: alancameronwills
manager: douge
ms.assetid: 49f736a5-67fe-4cc6-b1ef-51b993fb39bd
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/07/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: 89e3cb3a7e9185d4a2944c1aa9aaf5aee4bd2b24
ms.lasthandoff: 03/15/2017


---
# <a name="resources-roles-and-access-control-in-application-insights"></a>Application Insights のリソース、ロール、アクセス制御
Azure [Application Insights][start] では、[Microsoft Azure のロールベースのアクセス制御](../active-directory/role-based-access-control-configure.md)を利用し、データを読み取りできる人と更新できる人を制御できます。

> [!IMPORTANT]
> リソース自体ではなく、アプリケーション リソースが属する **リソース グループまたはサブスクリプション** でユーザーにアクセスを割り当てます。 **Application Insights コンポーネントの共同作業者** ロールを割り当てます。 これにより、Web テストとアラート、アプリケーション リソースのアクセス制御が統一されます。 [詳細情報](#access)。
> 
> 

## <a name="resources-groups-and-subscriptions"></a>リソース、グループ、サブスクリプション
最初に、いくつかの定義を紹介します。

* **リソース** - Microsoft Azure サービスのインスタンス。 Application Insights リソースはアプリケーションから送られてくる利用統計情報データを収集し、分析し、表示します。  その他の種類の Azure リソースには Web アプリ、データベース、VM があります。
  
    すべてのリソースを表示するには、[Azure Portal][portal] に進み、サインインし、[参照] をクリックします。
  
    ![[参照] を選択し、[すべて] と [Application Insights でフィルター処理] のいずれかを選択します。](./media/app-insights-resources-roles-access-control/10-browse.png)

<a name="resource-group"></a>

* [**リソース グループ**][group] - すべてのリソースが&1; つのグループに属します。 関連リソースを管理するとき、特にアクセス制御の場合、グループは便利な方法です。 たとえば、1 つのリソース グループに Web アプリ、そのアプリを監視する Application Insights リソース、エクスポートされたデータを保持する Storage リソースを配置できます。

    ![[参照]、[リソース グループ] を選択し、グループを選択します。](./media/app-insights-resources-roles-access-control/11-group.png)

* [**サブスクリプション**](https://manage.windowsazure.com) - Application Insights またはその他の Azure リソースを使用するには、Azure サブスクリプションにサインインします。 すべてのリソース グループは&1; つの Azure サブスクリプションに属し、そこでは価格パッケージが選択されるほか、会社のサブスクリプションである場合にメンバーとそのアクセス許可が選択されます。
* [**Microsoft アカウント**][account] - Microsoft Azure サブスクリプション、XBox Live、Outlook.com、およびその他の Microsoft サービスへのサインインに使用するユーザー名とパスワード。

## <a name="access"></a> リソース グループのコントロール制御
アプリケーションに作成したリソースに加え、アラートと Web テストには非表示の個別リソースもあることを理解することが重要です。 これらはアプリケーションと同じ [リソース グループ](#resource-group) に関連付けられています。 Web サイトやストレージなど、他の Azure サービスをそこに配置することもあります。

![Application Insights のリソース](./media/app-insights-resources-roles-access-control/00-resources.png)

これらのリソースのアクセスを制御するには、次の処置が推奨されます。

* **リソース グループまたはサブスクリプション** レベルでアクセスを制御します。
* **Application Insights コンポーネントの共同作業者** ロールをユーザーに割り当てます。 この処置により、グループのその他のサービスのアクセス権を与えなくても、ユーザーは Web テスト、アラート、Application Insights リソースを編集できます。

## <a name="to-provide-access-to-another-user"></a>別のユーザーにアクセスを与えるには
サブスクリプションまたはリソース グループの所有者権限が必要です。

ユーザーは、[Microsoft アカウント][account]を持っているか、または[組織の Microsoft アカウント](../active-directory/sign-up-organization.md)にアクセスする必要があります。 個人と Azure Active Directory に定義されているユーザー グループにアクセスを供与できます。

#### <a name="navigate-to-the-resource-group"></a>リソース グループに移動する
そこでユーザーを追加します。

![アプリケーションのリソース ブレードで、Essentials を開き、リソース グループを開き、設定/ユーザーを選択します。 [追加] をクリックします。](./media/app-insights-resources-roles-access-control/01-add-user.png)

あるいは、もう&1; つ上のレベルに進み、ユーザーをサブスクリプションに追加できます。

#### <a name="select-a-role"></a>ロールを選択する
![新しいユーザーの役割を選択します。](./media/app-insights-resources-roles-access-control/03-role.png)

| 役割 | In the resource group |
| --- | --- |
| Owner |Can change anything, including user access |
| Contributor |Can edit anything, including all resources |
| Application Insights コンポーネントの共同作業者 |Can edit Application Insights resources, web tests and alerts |
| Reader |Can view but not change anything |

'Editing' includes creating, deleting and updating:

* Resources
* Web tests
* Alerts
* 連続エクスポート

#### <a name="select-the-user"></a>ユーザーを選択する
![新しいユーザーの電子メール アドレスを入力します。 ユーザーを選択する](./media/app-insights-resources-roles-access-control/04-user.png)

対象のユーザーがディレクトリにない場合、Microsoft アカウントを持つユーザーを招待できます。
(Outlook.com、OneDrive、Windows Phone、XBox Live などのサービスを利用している場合、Microsoft アカウントを持っています。)

## <a name="users-and-roles"></a>ユーザーと役割
* [Azure の役割基準のアクセス制御](../active-directory/role-based-access-control-configure.md)

<!--Link references-->

[account]: https://account.microsoft.com
[group]: ../azure-resource-manager/resource-group-overview.md
[portal]: https://portal.azure.com/
[start]: app-insights-overview.md

