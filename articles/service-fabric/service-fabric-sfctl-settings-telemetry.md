---
title: Azure Service Fabric CLI - sfctl settings telemetry
description: Azure Service Fabric のコマンド ライン インターフェイスである sfctl について説明します。 sfctl テレメトリを構成するためのコマンドの一覧が含まれています。
author: jeffj6123
ms.topic: reference
ms.date: 9/17/2019
ms.author: jejarry
ms.openlocfilehash: cdb4a44c8f19b31c164e2ba3ea5e16b7a09e743e
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/03/2020
ms.locfileid: "75645278"
---
# <a name="sfctl-settings-telemetry"></a>sfctl settings telemetry
sfctl のこのインスタンスに対してローカルなテレメトリ設定を構成します。

Sfctl テレメトリでは、パラメーターやパラメーター値が指定されていないコマンドの名前、sfctl バージョン、OS の種類、python のバージョン、コマンドの成否、返されたエラー メッセージが収集されます。

## <a name="commands"></a>コマンド

|command|[説明]|
| --- | --- |
| set-telemetry | テレメトリを有効または無効にします。 |

## <a name="sfctl-settings-telemetry-set-telemetry"></a>sfctl settings telemetry set-telemetry
テレメトリを有効または無効にします。

### <a name="arguments"></a>引数

|引数|[説明]|
| --- | --- |
| --off | テレメトリを無効にします。 |
| --on | テレメトリを有効にします。 これが既定値です。 |

### <a name="global-arguments"></a>グローバル引数

|引数|[説明]|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するようにログの詳細レベルを上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログの詳細レベルを上げます。 詳細なデバッグ ログを表示するには --debug を使います。 |

### <a name="examples"></a>例

テレメトリを無効にします。

```
sfctl settings telemetry set_telemetry --off
```

テレメトリを有効にします。

```
sfctl settings telemetry set_telemetry --on
```


## <a name="next-steps"></a>次のステップ
- Service Fabric CLI を[セットアップ](service-fabric-cli.md)します。
- [サンプル スクリプト](/azure/service-fabric/scripts/sfctl-upgrade-application)を使用して、Service Fabric CLI の使用方法を学習します。