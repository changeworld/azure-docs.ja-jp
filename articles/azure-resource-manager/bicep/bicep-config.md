---
title: Bicep 構成ファイル
description: Bicep デプロイの構成ファイルについて説明します。
ms.topic: conceptual
ms.date: 11/16/2021
ms.openlocfilehash: 8679daf65402c939f9eea7651b57b1198970fb8d
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132548142"
---
# <a name="configure-your-bicep-environment"></a>Bicep 環境を構成する

Bicep は、**bicepconfig.json** という名前の構成ファイルをサポートしています。 このファイル内では、Bicep の開発エクスペリエンスをカスタマイズする値を追加できます。 このファイルを追加しない場合、Bicep では既定値が使用されます。

値をカスタマイズするには、Bicep ファイルを保存するディレクトリにこのファイルを作成します。 複数のディレクトリに bicepconfig ファイルを追加できます。 ディレクトリ階層内で最も近い構成ファイルが使用されます。

## <a name="available-settings"></a>利用可能な設定

[モジュール](modules.md)を操作する際、モジュールのパスに別名を追加できます。 これらの別名は、複雑なパスを繰り返す必要がないため、Bicep ファイルを簡略化します。 レジストリに対する認証のための資格情報に優先順位を構成することもできます。 資格情報は、外部モジュールをローカル キャッシュに復元するために使用されます。 詳細については、「[Bicep 構成にモジュール設定を追加する](bicep-config-modules.md)」を参照してください。

[Bicep リンター](linter.md)を操作する場合は、Bicep ファイル検証の既定の設定をオーバーライドできます。 詳細については、「[Bicep 構成にリンター設定を追加する](bicep-config-linter.md)」を参照してください。

## <a name="intellisense"></a>Intellisense

Visual Studio Code の Bicep 拡張機能は、**bicepconfig.json** ファイルの intellisense をサポートしています。 Intellisense を使用して、使用可能なプロパティと値を検出します。

:::image type="content" source="./media/bicep-config/bicep-linter-configure-intellisense.png" alt-text="intellisense は bicepconfig.json の構成をサポートしています。":::

## <a name="next-steps"></a>次のステップ

* [Bicep 構成でモジュール設定を追加する](bicep-config-modules.md)
* [Bicep 構成にリンター設定を追加する](bicep-config-linter.md)
* [Bicep リンター](linter.md)について説明します
