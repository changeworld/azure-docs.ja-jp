<properties
   pageTitle="Azure リソース マネージャーの概要"
   description="Azure リソース マネージャーを使用して、 Azure のリソースをデプロイ、管理、およびのアクセス制御する方法について説明します。"
   services="multiple"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="multiple"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/22/2015"
   ms.author="tomfitz"/>

# Azure リソース マネージャーの概要

アプリケーションは通常、Web アプリケーション、データベース、データベース サーバー、ストレージ、およびサード パーティのサービスなどの、複数のコンポーネントで構成されます。これらのコンポーネントは別々のエンティティではなく、1 つのエンティティの中で互いに関連付けられ相互依存しています。これらのコンポーネントを、1 つのグループとしてデプロイ、管理、および監視するのが好ましいです。Azure リソース マネージャーを使用すると、アプリケーション内の複数リソースを 1 つのグループと見なして作業できます。アプリケーションのこれらすべてのリソースを、1 回の連携した操作でデプロイ、更新、または削除できます。デプロイにはテンプレートを使用しますが、このテンプレートは、テスト、ステージング、運用環境などのさまざまな環境に使用できます。グループ全体のロールアップ コストを表示すると、組織の課金ついて明確に把握できます。

Azure リソース マネージャーでは、 アクセス制御が管理プラットフォームにネイティブに統合されているため、組織内のユーザーがリソース グループに対して実行できるアクションを指定できます。

> [AZURE.NOTE]このトピックでは、プレビュー ポータルを使用してリソース、グループ、テンプレートについて説明することで、これらの概念を理解していただきます。また、Azure リソースの作成、管理、および削除には、[Mac、Linux、および Windows 用 Azure CLI](virtual-machines/xplat-cli-azure-resource-manager.md) や [PowerShell](powershell-azure-resource-manager.md) を使用することもできます。

## リソース グループ

リソース グループは、1 つのアプリケーションの関連リソースを保持するコンテナーです。リソース グループには、アプリケーションのすべてのリソース、または論理的にグループ化されたリソースのみを含めることができます。組織のニーズに合わせてリソースをリソース グループに割り当てる方法を指定できます。

リソース グループを定義する際、次のような考慮すべき要素があります。

1. グループ内のすべてのリソースで、同じライフ サイクルが共有される必要がある。そのため、これらのリソースは一緒にデプロイ、更新、および削除されます。データベース サーバーなどの 1 つのリソースが、別のデプロイ サイクル上に存在する必要がある場合は、別のリソース グループに含めなければなりません。
2. 各リソースは、1 つのリソース グループにのみ存在できる。
3. リソースは、いつでもリソース グループに追加したり、削除できる。
4. リソース グループには、別のリージョンに存在するリソースを含めることができる。
5. リソース グループを使用すると、管理操作のアクセス制御のスコープを設定できる。

Azure プレビュー ポータルでは、すべての新しいリソースはリソース グループ内で作成されます。Web サイトなどのリソースを 1 つだけ作成した場合でも、そのリソースを既存のグループに追加するのか、またはそのリソース用の新しいグループを作成するのかを決定する必要があります。

次の図は、Web サイト、データベース、および Application Insights を含むリソース グループを示しています。

![リソース グループの概要](./media/resource-group-overview/resourcegroupsummary.png)

リソース グループは、別のリソース グループ内のリソースへもリンクできます。リソースがリンク済みと見なされるのは、異なるリソース グループ内にあるリソースとの間にデプロイの依存関係が存在する場合です。たとえば、 1 つのリソース グループ内の Web アプリが、別のリソース グループ内のデータベースに接続している場合、これらのリソースはリンク済みです。

![リンク済みリソース](./media/resource-group-overview/linkedresource.png)

プレビュー ポータルでは、コストの参照、イベントの監視、および警告の管理が簡単にできます。次の図は、1 つのグループに対する一括請求を示しています。

![課金](./media/resource-group-overview/billing.png)

## テンプレートのデプロイ

Azure リソース マネージャーで、アプリケーションのデプロイと構成を定義する JSON 形式の単純なテンプレートを作成できます。このテンプレートは Azure テンプレートと呼ばれ、デプロイ定義を宣言できます。テンプレートを使用すると、アプリケーションをアプリのライフサイクルを通して繰り返しデプロイできるほか、常にリソースが一貫した状態でデプロイされます。

テンプレート内では、アプリのインフラストラクチャ、構成方法、およびアプリケーション コードをインフラストラクチャに発行する方法を定義します。Azure リソース マネージャーでは、依存関係を分析し、リソースが確実に正しい順序で作成されるため、デプロイの順序について心配する必要はありません。

インフラストラクチャの更新にも、テンプレートを使用することができます。たとえば、アプリに新しいリソースを追加したり、既にデプロイされたリソースに構成ルールを追加したりできます。テンプレートで新しいリソースの作成を指定した際、そのリソースが既に存在する場合は、Azure リソース マネージャーでは、新しいアセットを作成する代わりに更新が実行されます。Azure リソース マネージャーでは、既存のアセットが、新しいアセットと同じ状態になるよう更新されます。

テンプレートでパラメーターを指定すると、デプロイをカスタマイズでき、柔軟性が高まります。たとえば、テスト環境に合わせてデプロイを調整するためのパラメーター値を渡すことができます。パラメーターを指定すると、同じテンプレートを、すべてのアプリ環境のデプロイに使用できます。

テンプレートを使用すると、デプロイおよび構成に必要なすべての手順が実行されるため、手動操作は必要ありません。Azure リソース マネージャーでは、セットアップ時に含まれていなかった特定ソフトウェアのインストールなど、追加の操作が必要なシナリオのための拡張機能を使用できます。DSC、Chef、または Puppet などの構成管理サービスをすでにご利用の場合は、拡張機能を使用すれば、引き続きそのサービスで作業ができます。

Marketplace からソリューションを作成すると、ソリューションには自動的にデプロイ テンプレートが含まれます。最初からテンプレートを作成する必要はありません。はじめにソリューション向けのテンプレートを使用して、それを特定のニーズに合わせてカスタマイズできます。

最後に、テンプレートは、アプリのソース コードの一部になります。テンプレートはソース コード リポジトリにチェックインして、アプリの変更に合わせて更新できます。テンプレートは Visual Studio から編集できます。

テンプレートの定義の詳細については、[Azure リソース マネージャーのテンプレートの作成](./resource-group-authoring-templates.md)を参照してください。

デプロイにテンプレートを使用する方法については、[Deploy an application with Azure Resource Manager template (Azure リソース マネージャーのテンプレートを使用したアプリケーションのデプロイ)](azure-portal/resource-group-template-deploy.md) と [Deploy a complex application predictably in Azure (Azure での複雑なアプリケーションの予測可能な方法でのデプロイ)](app-service-web/app-service-deploy-complex-application-predictably.md) を参照してください。

## タグ

Azure リソース マネージャーでは、管理や課金の要件に合わせてリソースを分類できる、タグ付け機能を使用できます。リソース グループとリソースの複合型コレクションがある場合、これらのアセットを明確に視覚化するために、タグを使用することをお勧めします。たとえば、組織内で同じロールを果たしている複数リソース、または同じ部門に属している複数リソースにタグを付けることができます。

次のプレビュー ポータルで、タグのアイコンをクリックして操作を開始します。

![タグ](./media/resource-group-overview/tags.png)

タグを共有するために、リソースが同じリソース グループ内に格納されている必要はありません。組織内のユーザーが類似したタグを誤って適用しないよう (「department」の代わりに「dep」など) 、組織内のすべてのユーザーが共通のタグを使用できる独自のタグ分類法を作成することができます。

タグの詳細については、[タグを使用した Azure リソースの整理](./resource-group-using-tags.md)を参照してください。

## アクセス制御

Azure リソース マネージャーは、組織に対する特定アクションにアクセスできるユーザーを制御できます。Azure リソース マネージャーでは、OAuth およびロールベースのアクセス制御 (RBAC) が管理プラットフォームにネイティブに統合されているため、そのアクセス制御がリソース グループ内のすべてのサービスに適用されます。ユーザーを事前定義されたプラットフォームおよびリソース固有のロールに追加し、これらのロールをサブスクリプション、リソース グループ、またはリソースに適用することで、アクセスを制限できます。たとえば、ユーザーにデータベース管理は許可するが、データベース サーバーやセキュリティ ポリシーの管理は許可しない SQL DB 共同作業者と呼ばれる事前定義済みのロールを利用するとします。このようなアクセスを必要とする組織内ユーザーを、SQL DB 共同作業者ロールに追加して、このロールをサブスクリプション、リソース グループ、またはリソースに適用します。

次のプレビュー ポータルでは、[アクセス] ボタンをクリックするとアクセス制御を定義できます。

![ユーザーアクセス制御](./media/resource-group-overview/access.png)

Azure リソース マネージャーでは、監査のために、自動的にユーザー操作が記録されます。

重要なリソースを、ユーザーによって削除または変更されないように、明示的にロックすることもできます。

ロールベースのアクセス制御の詳細については、[Azure プレビュー ポータルでのロール ベースのアクセス制御](./role-based-access-control-configure.md)を参照してください。

アクセス ポリシー設定の例については、[Managing and Auditing Access to Resources (リソースへのアクセスの管理および監視)](azure-portal/resource-group-rbac.md) を参照してください。

## 一貫性のある管理レイヤー

Azure リソース マネージャーでの操作は、Azure PowerShell、Mac、Linux、および Windows 用の Azure CLI、Azure ポータル、または REST API の操作と完全な互換性があります。ユーザーに最適なインターフェイスを使用できるほか、インターフェイス間を混乱せずにすばやく移動できます。ポータルでは、ポータルの外で実行された操作さえも表示されます。

PowerShell に関する詳細については、[リソース マネージャーでの Windows PowerShell の使用](./powershell-azure-resource-manager.md)および [Azure Resource Manager Cmdlets (Azure リソース マネージャのコマンドレッド)](https://msdn.microsoft.com/library/azure/dn757692.aspx) を参照してください。

Azure CLI の詳細については、[Azure リソース管理での、Mac、Linux、および Windows 用 Azure CLI の使用](./virtual-machines/xplat-cli-azure-resource-manager.md)を参照してください。

REST API の詳細については、[Azure リソース マネージャー REST API リファレンス](https://msdn.microsoft.com/library/azure/dn790568.aspx)を参照してください。

## 次のステップ
Getting Started (概要)

- [リソース マネージャーでの Windows PowerShell の使用](./powershell-azure-resource-manager.md)
- [リソース マネージャーでの Azure クロスプラットフォーム CLI の使用](./virtual-machines/xplat-cli-azure-resource-manager.md)
- [Using the Azure Preview Portal to manage your Azure resources (Azure プレビュー ポータルを使用した Azure リソースの管理)](azure-portal/resource-group-portal.md)

アプリケーションの作成とデプロイ

- [テンプレートの作成](./resource-group-authoring-templates.md)
- [テンプレートのデプロイ](azure-portal/resource-group-template-deploy.md)
- [デプロイのトラブルシューティング](virtual-machines/resource-group-deploy-debug.md)
- [Azure で複雑なアプリケーションを予測どおりにデプロイ](app-service-web/app-service-deploy-complex-application-predictably.md)
- [.NET ライブラリとテンプレートを使用した Azure リソースのデプロイ](virtual-machines/arm-template-deployment.md)
- [テンプレート関数](./resource-group-template-functions.md)
- [高度なテンプレートの操作](./resource-group-advanced-template.md)

リソースの整理

- [タグを使用した Azure リソースの整理](./resource-group-using-tags.md)

アクセスの管理と監査

- [Managing and Auditing Access to Resources (リソースへのアクセスの管理と監査)](azure-portal/resource-group-rbac.md)
- [Azure プレビュー ポータルでのロール ベースのアクセス制御](./role-based-access-control-configure.md)
- [Azure リソース マネージャーでのサービス プリンシパルの認証](./resource-group-authenticate-service-principal.md)
- [Azure ポータルを使用した Azure サービス プリンシパルの新規作成](./resource-group-create-service-principal-portal.md)

<!---HONumber=July15_HO1-->