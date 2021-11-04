---
title: Speech Studio でのロールベースのアクセス制御 - Speech サービス
titleSuffix: Azure Cognitive Services
description: Speech Studio を使用して Speech サービスにアクセス ロールを割り当てる方法について説明します。
services: cognitive-services
author: eric-urban
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/07/2021
ms.author: eur
ms.openlocfilehash: 4b84d73b242aba0b46674005211dcb6de77d69e4
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131509393"
---
# <a name="azure-role-based-access-control-in-speech-studio"></a>Speech Studio での Azure ロールベースのアクセス制御 

Speech Studio では、Azure リソースへの個々のアクセスを管理するための認可システムである、Azure のロールベースのアクセス制御 (Azure RBAC) がサポートされています。 Azure RBAC を使用して、お使いの Speech Studio 操作に対するさまざまなレベルのアクセス許可を、さまざまなチーム メンバーに割り当てることができます。 Azure RBAC の詳細については、「[Azure RBAC のドキュメント](../../role-based-access-control/overview.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

* Azure アカウントと Speech リソースを使用して、Speech Studio にサインインする必要があります。 「[Speech Studio の概要](speech-studio-overview.md)」を参照してください。

## <a name="manage-role-assignments-for-speech-resources"></a>Speech リソースのロールの割り当てを管理する

Azure Speech リソースへのアクセス権を付与するには、Azure portal の Azure RBAC ツールを使用してロールの割り当てを追加します。 

数分以内に、選択したスコープで選択したロールがターゲットに割り当てられます。 これらの手順については、「[Azure portal を使用して Azure ロールを割り当てる](../../role-based-access-control/role-assignments-portal.md?tabs=current)」を参照してください。

## <a name="supported-built-in-roles-in-speech-studio"></a>Speech Studio でサポートされている組み込みロール

ロールの定義はアクセス許可のコレクションです。 アクセス許可に固有のカスタム要件がない場合は、次の推奨される組み込みのロールを使用します。

| **組み込みのロール** | **リソースキーを一覧表示するアクセス許可** | **Custom Speech 操作のアクセス許可** | **Custom Voice 操作のアクセス許可**| **その他の機能のアクセス許可** |
| ---| ---| ---| ---| --|
|**所有者** |はい |プロジェクト、データ、モデル、エンドポイントを作成、編集、または削除するためのアクセス許可を含む、プロジェクトへのフルアクセス |プロジェクト、データ、モデル、エンドポイントを作成、編集、または削除するためのアクセス許可を含む、プロジェクトへのフルアクセス |フル アクセス |
|**Contributor** |はい |プロジェクト、データ、モデル、エンドポイントを作成、編集、または削除するためのアクセス許可を含む、プロジェクトへのフルアクセス |プロジェクト、データ、モデル、エンドポイントを作成、編集、または削除するためのアクセス許可を含む、プロジェクトへのフルアクセス |フル アクセス |
|**Cognitive Service 共同作成者** |はい |プロジェクト、データ、モデル、エンドポイントを作成、編集、または削除するためのアクセス許可を含む、プロジェクトへのフルアクセス |プロジェクト、データ、モデル、エンドポイントを作成、編集、または削除するためのアクセス許可を含む、プロジェクトへのフルアクセス |フル アクセス |
|**Cognitive Service ユーザー** |はい |プロジェクト、データ、モデル、エンドポイントを作成、編集、または削除するためのアクセス許可を含む、プロジェクトへのフルアクセス |プロジェクト、データ、モデル、エンドポイントを作成、編集、または削除するためのアクセス許可を含む、プロジェクトへのフルアクセス |フル アクセス |
|**Cognitive Service Speech 共同作成者** |いいえ |プロジェクト、データ、モデル、エンドポイントを作成、編集、または削除するためのアクセス許可を含む、プロジェクトへのフルアクセス |プロジェクト、データ、モデル、エンドポイントを作成、編集、または削除するためのアクセス許可を含む、プロジェクトへのフルアクセス |フル アクセス |
|**Cognitive Service Speech ユーザー** |いいえ |プロジェクト/データセット/モデル/エンドポイントを表示できます。作成、編集、削除を行うことはできません |プロジェクト/データセット/モデル/エンドポイントを表示できます。作成、編集、削除を行うことはできません |フル アクセス |
|**Cognitive Services データ閲覧者 (プレビュー)** |いいえ |プロジェクト/データセット/モデル/エンドポイントを表示できます。作成、編集、削除を行うことはできません |プロジェクト/データセット/モデル/エンドポイントを表示できます。作成、編集、削除を行うことはできません |フル アクセス |

または、[独自のカスタム ロールを作成](../../role-based-access-control/custom-roles.md)することもできます。 たとえば、カスタム音声データセットをアップロードすることはできるが、エンドポイントにカスタム音声モデルを配置することはできないアクセス許可を持つカスタム ロールを作成できます。

> [!NOTE]
> Speech Studio は、キーベースの認証をサポートしています。 リソースキー (`Microsoft.CognitiveServices/accounts/listKeys/action`) を一覧表示するアクセス許可を持つロールは、最初にリソースキーで認証され、Azure portal でキー認証が有効になっている限り、Speech Studio 操作へのフルアクセスを持ちます。 キー認証がサービス管理者によって無効にされている場合、これらのロールは、Studio へのすべてのアクセスを失います。

> [!NOTE]
> 1つのリソースを複数のロールに割り当てたり、継承したりすることができます。また、このリソースへの最後のアクセスレベルは、操作レベルからのすべてのロールのアクセス許可を組み合わせたものになります。

## <a name="next-steps"></a>次のステップ

[Speech サービスによる保存データの暗号化](./speech-encryption-of-data-at-rest.md)について学習します。
