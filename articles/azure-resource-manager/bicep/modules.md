---
title: Bicep モジュール
description: Bicep ファイルでモジュールを定義する方法と、モジュールのスコープを使用する方法について説明します。
ms.topic: conceptual
ms.date: 11/12/2021
ms.openlocfilehash: a8aedd784875fccbad81957550380cc4fa236616
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132551276"
---
# <a name="bicep-modules"></a>Bicep モジュール

Bicep を使用すると、デプロイをモジュールにまとめることができます。 モジュールとは、別の Bicep ファイルからデプロイされる単なる Bicep ファイルです。 モジュールを使用して、デプロイの複雑な詳細をカプセル化することで、Bicep ファイルの読みやすさが向上します。 また、モジュールを異なるデプロイに簡単に再利用することもできます。

組織内の他のユーザーとモジュールを共有するには、[プライベート レジストリを作成します](private-module-registry.md)。 レジストリ内のモジュールは、正しいアクセス許可を持つユーザーのみが使用できます。

Bicep モジュールは、[入れ子になったテンプレート](../templates/linked-templates.md#nested-template)を持つ単一の Azure Resource Manager テンプレートに変換されます。

### <a name="microsoft-learn"></a>Microsoft Learn

モジュールの詳細とハンズオン ガイダンスについては、**Microsoft Learn** の「[モジュールを使用して構成可能な Bicepファイルを作成する](/learn/modules/create-composable-bicep-files-using-modules/)」を参照する。

## <a name="definition-syntax"></a>定義の構文

モジュールを定義するための基本的な構文は次のとおりです。

```bicep
module <symbolic-name> '<path-to-file>' = {
  name: '<linked-deployment-name>'
  params: {
    <parameter-names-and-values>
  }
}
```

したがって、実際の簡単な例は次のようになります。

::: code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/modules/local-file-definition.bicep" :::

Bicep ファイルの別の部分にあるモジュールを参照するには、シンボリック名を使用します。 たとえば、シンボリック名を使用して、モジュールからの出力を取得できます。 シンボリック名には、a - z、A - Z、0 - 9、アンダースコア (`_`) を含めることができます。 名前の先頭を数字にすることはできません。 モジュールの名前を、パラメーター、変数、またはリソースと同じにすることはできません。

パスは、ローカル ファイルでもレジストリ内のファイルでもかまいません。 詳細については、「[モジュールへのパス](#path-to-module)」を参照してください。

**name** プロパティは必須です。 生成されるテンプレートでは、それは入れ子になったデプロイ リソースの名前になります。

メイン ファイルのスコープとは異なる **スコープを指定する** 必要がある場合は、scope プロパティを追加します。 詳細については、「[モジュールのスコープを設定する](#set-module-scope)」を参照してください。

::: code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/modules/scope-definition.bicep" highlight="4" :::

**モジュールを条件付きでデプロイする** には、`if` 式を追加します。 使用方法は、[リソースを条件付きでデプロイする](conditional-resource-deployment.md)場合と似ています。

::: code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/modules/conditional-definition.bicep" highlight="2" :::

モジュールの **複数のインスタンス** をデプロイするには、`for` 式を追加します。 `batchSize` デコレーターを使用して、インスタンスを順番にデプロイするか、または並列でデプロイするかを指定できます。 詳細については [、「Bicep の反復ループ」を参照してください](loops.md)。

::: code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/modules/iterative-definition.bicep" highlight="3" :::

リソースと同様に、モジュールは、他のモジュールやリソースに依存しない限り、並列でデプロイされます。 通常は、暗黙的に決定される依存関係を設定する必要はありません。 明示的な依存関係を設定する必要がある場合は、モジュールの定義に `dependsOn` を追加できます。 依存関係の詳細については、「 [リソースの依存関係](resource-declaration.md#dependencies)」を参照してください。

::: code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/modules/dependsOn-definition.bicep" highlight="6-8" :::

## <a name="path-to-module"></a>モジュールへのパス

モジュールのファイルには、ローカル ファイルまたは Bicep モジュール レジストリ内の外部ファイルのいずれかを指定できます。 次に両方の方法について説明します。

### <a name="local-file"></a>ローカル ファイル

モジュールが **ローカル ファイル** の場合は、そのファイルへの相対パスを指定します。 プラットフォーム間で一貫したコンパイルを保証するため、Bicep のすべてのパスは、スラッシュ (/) ディレクトリ区切り文字を使用して指定する必要があります。 Windows の円記号 (\\) 文字はサポートされていません。 パスにはスペースを含めることができます。

たとえば、ディレクトリ内でメイン ファイルより 1 つ上のレベルにあるファイルをデプロイするには、以下を使用します。

::: code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/modules/local-file-definition.bicep" highlight="1" :::

### <a name="file-in-registry"></a>レジストリ内のファイル

[モジュールをレジストリに発行](bicep-cli.md#publish)した場合は、そのモジュールにリンクできます。 Azure コンテナー レジストリの名前とモジュールへのパスを指定します。 モジュールのパスは次の構文で指定します。

```bicep
module <symbolic-name> 'br:<registry-name>.azurecr.io/<file-path>:<tag>' = {
```

- **br** は、Bicep レジストリのスキーマ名です。
- **ファイル パス** は、Azure Container Registry では `repository` と呼ばれます。 `/` 文字で区切ることで、複数のセグメントを **ファイル パス** に含めることができます。
- **タグ** は、モジュールのバージョンを指定するために使用します。

次に例を示します。

::: code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/modules/registry-definition.bicep" highlight="1" :::

レジストリ内のモジュールを参照すると、Visual Studio Code の Bicep 拡張機能によって [bicep restore](bicep-cli.md#restore) が自動的に呼び出されて、外部モジュールがローカル キャッシュにコピーされます。 外部モジュールが復元されるまで、しばらく時間がかかります。 モジュールの IntelliSense がすぐに機能しない場合は、復元が完了するまで待ちます。

レジストリ内のモジュールの完全なパスは長くなる場合があります。 モジュールを使用するたびに完全なパスを指定する代わりに、[bicepconfig.json ファイルでエイリアスを構成する](bicep-config-modules.md#aliases-for-modules)ことができます。 エイリアスを使用すると、モジュールの参照が簡単になります。 たとえば、エイリアスを使用すると、パスを次のように短縮できます。

::: code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/modules/alias-definition.bicep" highlight="1" :::

## <a name="parameters"></a>パラメーター

モジュールの定義で指定するパラメーターは、Bicep ファイル内のパラメーターと一致します。

次の Bicep の例には、storagePrefix、storageSKU、location という 3 つのパラメーターが含まれます。 storageSKU パラメーターには既定値があるので、デプロイ時にそのパラメーターの値を指定する必要はありません。

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/create-storage-account/main.bicep" highlight="3,15,17" :::

前の例をモジュールとして使用するには、それらのパラメーターの値を指定します。

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/modules/parent-output.bicep" highlight="14-17" :::

## <a name="set-module-scope"></a>モジュールのスコープを設定する

モジュールを宣言するとき、そのモジュールのスコープとして、中に含まれている Bicep ファイルとは異なるスコープを設定できます。 モジュールのスコープを設定するには、`scope` プロパティを使用します。 scope プロパティが指定されていない場合、モジュールは親のターゲット スコープにデプロイされます。

次の Bicep ファイルでは、リソース グループと、そのリソース グループ内のストレージ アカウントが作成されます。 ファイルはサブスクリプションにデプロイされますが、モジュールのスコープは新しいリソース グループになります。

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/modules/rg-and-storage.bicep" highlight="2,12,19" :::

次の例では、2 つの異なるリソース グループにストレージ アカウントがデプロイされます。 どちらのリソース グループも既に存在している必要があります。

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/modules/scope-two-resource-groups.bicep" highlight="1,13,22" :::

scope プロパティには、有効なスコープ オブジェクトを設定します。 Bicep ファイルを使用してリソース グループ、サブスクリプション、または管理グループをデプロイする場合、モジュールのスコープをそのリソースのシンボリック名に設定することができます。 また、スコープ関数を使用して有効なスコープを取得することもできます。

以下がその関数です。

- [resourceGroup](bicep-functions-scope.md#resourcegroup)
- [subscription](bicep-functions-scope.md#subscription)
- [managementGroup](bicep-functions-scope.md#managementgroup)
- [tenant](bicep-functions-scope.md#tenant)

次の例では、`managementGroup` 関数を使用してスコープを設定します。

::: code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/modules/function-scope.bicep" highlight="5" :::

## <a name="output"></a>出力

モジュールから値を取得し、メインの Bicep ファイルでそれを使用できます。 モジュールから出力値を取得するには、モジュール オブジェクトで `outputs` プロパティを使用します。

最初の例では、ストレージ アカウントが作成されて、プライマリ エンドポイントが返されます。

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/create-storage-account/main.bicep" highlight="33" :::

モジュールとして使用すると、その出力値を取得できます。

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/modules/parent-output.bicep" highlight="20" :::

## <a name="next-steps"></a>次のステップ

- チュートリアルについては、[Bicep テンプレートを使用した Azure リソースのデプロイ](/learn/modules/deploy-azure-resources-by-using-bicep-templates/)の記事を参照してください。
- 機密値をモジュールに渡すには、[getSecret](bicep-functions-resource.md#getsecret) 関数を使用します。
