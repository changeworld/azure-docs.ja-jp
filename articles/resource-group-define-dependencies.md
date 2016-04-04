<properties
   pageTitle="Azure リソース マネージャーのテンプレートでの依存関係の定義"
   description="デプロイ時にあるリソースを別のリソースに依存するように設定する方法について説明します。"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/07/2015"
   ms.author="tomfitz"/>

# Azure リソース マネージャーのテンプレートでの依存関係の定義

特定のリソースでは、トポロジの成功に不可欠なアップストリームと子の依存関係が複数存在する場合があります。他のリソースとの依存関係を定義するには、リソースの **dependsOn** プロパティと **resources** プロパティを使用します。依存関係は、**reference** 関数を使用して指定することもできます。

    {
        "name": "<name-of-the-resource>",
        "type": "<resource-provider-namespace/resource-type-name>",
        "apiVersion": "<supported-api-version-of-resource>",
        "location": "<location-of-resource>",
        "tags": { <name-value-pairs-for-resource-tagging> },
        "dependsOn": [ <array-of-related-resource-names> ],
        "properties": { <settings-for-the-resource> },
        "resources": { <dependent-resources> }
    }

 また、リソース間のリレーションシップを定義したり、リソース グループ間でこのようなリレーションシップの定義をサポートできるリソース リンクもあります。

## dependsOn

特定の仮想マシンでは、データベース リソースが正常にプロビジョニングされていることを前提としている場合があります。もう 1 つのケースとして、クラスター管理ツールを使用して仮想マシンをデプロイする前にクラスター内の複数のノードがインストールされることを前提としている場合もあります。

テンプレート内では、dependsOn プロパティを使用すると、リソースのこの依存関係を定義できます。その値には、リソース名のコンマ区切りリストを指定できます。リソース間の依存関係が評価され、リソースは依存する順にデプロイされます。  
相互依存していないリソースは、平行してデプロイされます。  


dependsOn を使用してリソース間の依存関係をマップする傾向があるものの、重要なのは、その操作を行う理由を理解することです。これは、デプロイのパフォーマンスに影響を与える可能性があるためです。たとえば、リソースが相互にどのように接続されているかをドキュメント化することが理由である場合、dependsOn は適切な方法ではありません。DependsOn のライフサイクルはデプロイのみを対象としており、デプロイ後は使用できません。デプロイ後にこれらの依存関係を照会する方法はありません。dependsOn を使用すると、パフォーマンスに影響を及ぼすリスクが生じます。この場合、意図せずデプロイ エンジンによる並列処理の使用を阻止する可能性があります。リソース間のリレーションシップに対する照会機能をドキュメント化して提供するには、代わりに[リソース リンク](resource-group-link-resources.md)を使用する必要があります。

この要素は、reference 関数を使用してリソースの表現を取得する場合には不要です。これは、参照オブジェクトがリソースに対する依存関係を意味するためです。実際に、参照と dependsOn を使用するオプションがある場合は、reference 関数を使用して暗黙的な参照を含めるようにしてください。繰り返しますが、その根拠はパフォーマンスです。参照では、テンプレート内で参照される場合に必要になることがわかっている暗黙的な依存関係を定義します。参照は、その存在によって関連性があるため、再度パフォーマンスが最適化されることは回避されます。また、デプロイ エンジンで必要以上に並列処理を回避できないようにするという潜在的なリスクも回避されます。

リソースとコピー ループで作成されたリソースの間に依存関係を定義する必要がある場合、dependsOn 要素をループの名前に設定できます。例が必要であれば、「[Azure リソース マネージャーでリソースの複数のインスタンスを作成する](resource-group-create-multiple.md)」を参照してください。

## リソース

resources プロパティを使用すると、定義されているリソースに関連する子リソースを指定できます。子リソースの定義の深さは 5 レベルまでです。重要なのは、子リソースと親リソースの間に暗黙的な依存関係を作成しないことです。親リソースの後に子リソースをデプロイする必要がある場合は、dependsOn プロパティを使用してその依存関係を明示的に指定する必要があります。

各親リソースは、子リソースとして特定のリソースの種類のみを受け取ります。許容されるリソースの種類は、親リソースの[テンプレート スキーマ](https://github.com/Azure/azure-resource-manager-schemas)で指定されます。子リソースの種類の名前には、親リソースの種類の名前 (**Microsoft.Web/sites/config**、**Microsoft.Web/sites/extensions** など。これは両方とも **Microsoft.Web/sites** の子リソース)。

## reference 関数

reference 関数を使用すると、式では、他の JSON の名前と値のペアまたはランタイム リソースからその値を導出することができます。参照式では、あるリソースが別のリソースに依存することを暗黙的に宣言します。下記の **propertyPath** で表されるプロパティは省略可能です。指定しない場合は、参照先はリソースになります。

    reference('resourceName').propertyPath

この要素または dependsOn 要素のいずれかを使用して依存関係を指定できますが、同じ依存リソースに両方の要素を使用する必要はありません。不要な dependsOn によってデプロイ エンジンが誤ってデプロイの側面を並列実行できなくなるというリスクを回避するために暗黙的な参照を使用することをお勧めします。

詳細については、「[reference 関数](../resource-group-template-functions/#reference)」を参照してください。

## 次のステップ

- Azure リソース マネージャーのテンプレートの作成の詳細については、[テンプレートの作成](resource-group-authoring-templates.md)に関するページを参照してください。 
- テンプレートで使用可能な関数の一覧については、[テンプレートの関数](resource-group-template-functions.md)に関するページを参照してください。

<!---HONumber=AcomDC_0316_2016-->