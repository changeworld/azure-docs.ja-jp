---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/24/2020
ms.author: tomfitz
ms.openlocfilehash: 33a63280f6973d2c5e29db29f7a6f3fc68c57c77
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84424745"
---
| リソース | 制限 |
| --- | --- |
| [リソース グループ](../articles/azure-resource-manager/management/overview.md#resource-groups)あたりのリソース数 | リソースはリソース グループによって制限されることはありません。 代わりに、リソース グループのリソースの種類によって制限されます。 次の行を参照してください。 |
| リソースの種類ごとのリソース グループあたりのリソース数 |800 - リソースの種類によっては、800 の制限を超えることがあります。 「[リソース グループあたり 800 インスタンスに制限されないリソース](../articles/azure-resource-manager/management/resources-without-resource-group-limit.md)」を参照してください。 |
| デプロイ履歴でのリソース グループあたりのデプロイ数 |800<sup>1</sup> |
| デプロイあたりのリソース数 |800 |
| 一意のスコープあたりの管理ロック数 |20 |
| リソースまたはリソース グループあたりのタグ数 |50 |
| タグ キーの長さ |512 |
| タグ値の長さ |256 |

<sup>1</sup>2020 年 6 月から、この制限に近づくと、デプロイは履歴から自動的に削除されます。 デプロイ履歴からエントリを削除しても、デプロイ リソースには影響しません。 詳細については、「[デプロイ履歴からの自動削除](../articles/azure-resource-manager/templates/deployment-history-deletions.md)」を参照してください。

#### <a name="template-limits"></a>テンプレートの制限

| 値 | 制限 |
| --- | --- |
| パラメーター |256 |
| 変数 |256 |
| リソース (コピー数を含む) |800 |
| 出力 |64 |
| テンプレート式 |24,576 文字 |
| エクスポートされたテンプレート内のリソース |200 |
| テンプレート サイズ |4 MB |
| パラメーター ファイル サイズ |64 KB |

入れ子になったテンプレートを使用すると、一部のテンプレートの制限を超過することができます。 詳細については、[Azure リソース デプロイ時のリンクされたテンプレートの使用](../articles/azure-resource-manager/templates/linked-templates.md)に関するページを参照してください。 パラメーター、変数、出力の数を減らすために、いくつかの値を 1 つのオブジェクトに結合することができます。 詳しくは、[パラメーターとしてのオブジェクト](../articles/azure-resource-manager/resource-manager-objects-as-parameters.md)に関する記事をご覧ください。
