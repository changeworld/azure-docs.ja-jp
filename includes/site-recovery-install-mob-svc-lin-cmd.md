---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: 2b92aba8b9a8d8f46ae2aeac3a7bfe60a4755f9b
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/26/2018
ms.locfileid: "50166008"
---
1. 保護するサーバー上のローカル フォルダー (/tmp など) にインストーラーをコピーします。 ターミナルから次のコマンドを実行します。
  ```
  cd /tmp ;

  tar -xvzf Microsoft-ASR_UA*release.tar.gz
  ```
2. モビリティ サービスをインストールするには、次のコマンドを実行します。

  ```
  sudo ./install -d <Install Location> -r MS -v VmWare -q
  ```
3. インストールが完了したら、モビリティ サービスを構成サーバーに登録する必要があります。 以下のコマンドを実行して、モビリティ サービスを構成サーバーに登録できます。

  ```
  /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <CSIP> -P /var/passphrase.txt
  ```

#### <a name="mobility-service-installer-command-line"></a>モビリティ サービスのインストーラーのコマンド ライン

```
Usage:
./install -d <Install Location> -r <MS|MT> -v VmWare -q
```

|パラメーター|type|説明|指定できる値|
|-|-|-|-|
|-r |必須|モビリティ サービス (MS) とマスター ターゲット (MS) のどちらをインストールするかを指定します。|MS </br> MT|
|-d |省略可能|モビリティ サービスをインストールする場所。|/usr/local/ASR|
|-v|必須|モビリティ サービスをインストールするプラットフォームを指定します。 </br> </br>- **VMware**: "*VMware vSphere ESXi ホスト*"、"*Hyper-V ホスト*"、または "*物理サーバー*" で実行されている VM にモビリティ サービスをインストールする場合は、この値を使用します。 </br> - **Azure**: Azure IaaS VM にエージェントをインストールする場合は、この値を使用します。| VMware </br> Azure|
|パラメーター|省略可能|インストーラーをサイレント モードで実行するよう指定します。| 該当なし|


#### <a name="mobility-service-configuration-command-line"></a>モビリティ サービスの構成のコマンド ライン

```
Usage:
cd /usr/local/ASR/Vx/bin
UnifiedAgentConfigurator.sh -i <CSIP> -P <PassphraseFilePath>
```

|パラメーター|type|説明|指定できる値|
|-|-|-|-|
|-i |必須|構成サーバーの IP|任意の有効な IP アドレス|
|-P |必須|接続パスフレーズが保存されているファイルの完全パス|任意の有効なフォルダー|
