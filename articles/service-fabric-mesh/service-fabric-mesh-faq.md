---
title: Azure Service Fabric Mesh に関してよく寄せられる質問 | Microsoft Docs
description: Azure Service Fabric Mesh に関してよく寄せられる質問と答えについて説明します。
services: service-fabric-mesh
keywords: ''
author: chackdan
ms.author: chackdan
ms.date: 06/25/2018
ms.topic: troubleshooting
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: d0ae7fbb22f6d98662f83968158182d447a75394
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/03/2018
ms.locfileid: "39501969"
---
# <a name="commonly-asked-service-fabric-mesh-questions"></a>Service Fabric Mesh に関してよく寄せられる質問
Azure Service Fabric Mesh は、仮想マシン、ストレージ、ネットワークを管理することなく開発者がマイクロサービス アプリケーションをデプロイできるフル マネージド サービスです。 この記事ではよく寄せられる質問に回答します。

## <a name="how-do-i-report-an-issue-or-ask-a-question"></a>問題を報告したり、質問したりするにはどうすればよいですか。

[service-fabric-mesh-preview GitHub リポジトリ](https://aka.ms/sfmeshissues)で質問したり、Microsoft エンジニアから回答を得たり、問題を報告したりできます。

## <a name="quota-and-cost"></a>クォータとコスト

**プレビューに参加すると、どのくらいの費用がかかりますか。**

Mesh プレビューにアプリケーションやコンテナーをデプロイしても料金は発生しません。 しかしながら、頻繁にテストするのでなければ、デプロイしたリソースを削除し、実行状態のまま残さないようにお願いしています。

**コア数と RAM にはクォータ制限がありますか。**

はい。サブスクリプション別のクォータは次のようになっています。

- アプリケーションの数 - 5 
- アプリケーションあたりのコア数 - 12 
- アプリケーションあたりの合計 RAM - 48 GB 
- ネットワークとイングレス エンドポイントの数 – 5  
- アタッチできる Azure ボリュームの数 - 10 
- サービス レプリカの数 – 3 
- デプロイ可能なコンテナーは最大 4 コア、16-GB RAM に制限されています。
- コンテナーにコアを少しずつ割り当てることができます。増分は 0.5 コアであり、最大 6 コアまで割り当て可能です。

**アプリケーションを一晩中実行させておくことはできますか。**

はい。ただし、頻繁にテストするのでなければ、デプロイしたリソースを削除し、実行状態のまま残さないようにお願いしています。 この方針は今後変更されることがあります。誤用されていた場合、リソースは削除されることがあります。

## <a name="supported-container-os-images"></a>サポートされているコンテナー OS イメージ
サービスをデプロイすると、次のコンテナー OS イメージを使用できます。

- Windows - windowsservercore と nanoserver
    - Windows Server 2016
    - Windows Server バージョン 1709
- Linux
    - 既知の制限事項はありません

## <a name="features-gaps-and-known-issues"></a>機能の欠陥と既知の問題

**アプリケーションをデプロイしたところ、それに関連付けられているネットワーク リソースに IP アドレスがありません。**

現在、IP アドレスが遅れて現れるという既知の問題があります。 数分後にネットワーク リソースのステータスを見て、関連付けられている IP アドレスが表示されるか確認してください。

**アプリケーションが正しいネットワーク/ボリューム リソースにアクセスできません。**

アプリケーション モデル内では、関連付けられているリソースにネットワークやリソースがアクセスできるように、完全リソース ID を使用する必要があります。 簡単なサンプルを示すと次のようになります。

```json
"networkRefs": [
    {
    "name":  "[resourceId('Microsoft.ServiceFabric/networks', 'SbzVotingNetwork')]" 
    }
]
```

**現行のアプリケーション モデルではシークレットを暗号化する方法に対応していないようです。**

そのとおりです。現行のプライベート プレビューではシークレットを暗号化できません。 

**Service Fabric 開発クラスターの場合と Mesh の場合で、DNS が同じように動作しません。**

これは既知の問題であり、場合によっては、ローカル開発クラスターと Azure Mesh では異なる方法でサービスを参照する必要があります。 ローカル開発クラスターでは、{serviceName}.{applicationName} を使用してください。 Azure Service Fabric Mesh では、{servicename} を使用してください。 Azure Mesh では現在のところ、アプリケーション全体の DNS 解決に対応していません。

Windows 10 で Service Fabric 開発クラスターを実行するときに発生するその他の DNS 問題については、[Windows コンテナーのデバッグ](/azure/service-fabric/service-fabric-how-to-debug-windows-containers)方法に関するページをご覧ください。

**CLI モジュール _ImportError を使用すると、"cannot import name 'sdk_no_wait'" というエラーが表示されます。**

ご利用の CLI のバージョンが 2.0.30 より古い場合、このエラーが表示されます。

```
cannot import name 'sdk_no_wait'
Traceback (most recent call last):
File "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\knack\cli.py", line 193, in invoke cmd_result = self.invocation.execute(args)
File "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\azure\cli\core\commands_init_.py", line 241, in execute self.commands_loader.load_arguments(command)
File "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\azure\cli\core_init_.py", line 201, in load_arguments self.command_table[command].load_arguments() # this loads the arguments via reflection
File "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\azure\cli\core\commands_init_.py", line 142, in load_arguments super(AzCliCommand, self).load_arguments()
File "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\knack\commands.py", line 76, in load_arguments cmd_args = self.arguments_loader()
File "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\azure\cli\core_init_.py", line 332, in default_arguments_loader op = handler or self.get_op_handler(operation)
File "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\azure\cli\core_init_.py", line 375, in get_op_handler op = import_module(mod_to_import)
File "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\importlib_init_.py", line 126, in import_module return _bootstrap._gcd_import(name[level:], package, level)
File "", line 978, in _gcd_import
File "", line 961, in _find_and_load
File "", line 950, in _find_and_load_unlocked
File "", line 655, in _load_unlocked
File "", line 678, in exec_module
File "", line 205, in _call_with_frames_removed
File "C:\Users\annayak.azure\cliextensions\azure-cli-sbz\azext_sbz\custom.py", line 18, in 
from azure.cli.core.util import get_file_json, shell_safe_json_parse, sdk_no_wait
ImportError: cannot import name 'sdk_no_wait'.
```

**CLI 拡張パッケージをインストールすると、ディストリビューション名の不一致エラーが表示されます。**

このエラーは、拡張がインストールされなかったことを意味するものではありません。 問題なく CLI コマンドを使用できるはずです。

**スケールアウトすると、実行中のコンテナーを含め、すべてのコンテナーが影響を受けます。**

これはバグであり、次回のランタイム更新で修正されます。

## <a name="next-steps"></a>次の手順

Service Fabric Mesh の詳細については、[概要](service-fabric-mesh-overview.md)ページをお読みください。
