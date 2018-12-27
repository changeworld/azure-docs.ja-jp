---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: 65477f62af80511a73307204c2a6f4b5e0f409d6
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/26/2018
ms.locfileid: "50166383"
---
1. 保護するサーバー上のローカル フォルダー (C:\Temp など) にインストーラーをコピーします。 コマンド プロンプトで管理者として次のコマンドを実行します。

  ```
  cd C:\Temp
  ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
  MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
  cd C:\Temp\Extracted.
  ```
2. モビリティ サービスをインストールするには、次のコマンドを実行します。

  ```
  UnifiedAgent.exe /Role "MS" /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
  ```
3. ここで、エージェントを構成サーバーに登録する必要があります。

  ```
  cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
  UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
  ```

#### <a name="mobility-service-installer-command-line-arguments"></a>モビリティ サービスのインストーラーのコマンド ライン引数

```
Usage :
UnifiedAgent.exe /Role <MS|MT> /InstallLocation <Install Location> /Platform “VmWare” /Silent
```

| パラメーター|type|説明|指定できる値|
|-|-|-|-|
|/Role|必須|モビリティ サービス (MS) とマスター ターゲット (MS) のどちらをインストールするかを指定します。|MS </br> MT|
|/InstallLocation|省略可能|モビリティ サービスをインストールする場所。|コンピューター上の任意のフォルダー|
|/Platform|必須|モビリティ サービスをインストールするプラットフォームを指定します。 </br> </br>- **VMware**: "*VMware vSphere ESXi ホスト*"、"*Hyper-V ホスト*"、または "*物理サーバー*" で実行されている VM にモビリティ サービスをインストールする場合は、この値を使用します。 </br> - **Azure**: Azure IaaS VM にエージェントをインストールする場合は、この値を使用します。 | VMware </br> Azure|
|/Silent|省略可能|インストーラーをサイレント モードで実行するよう指定します。| 該当なし|

>[!TIP]
> セットアップ ログは、%ProgramData%\ASRSetupLogs\ASRUnifiedAgentInstaller.log にあります。

#### <a name="mobility-service-registration-command-line-arguments"></a>モビリティ サービスの登録のコマンド ライン引数

```
Usage :
UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
```

  | パラメーター|type|説明|指定できる値|
  |-|-|-|-|
  |/CSEndPoint |必須|構成サーバーの IP アドレス| 任意の有効な IP アドレス|
  |/PassphraseFilePath|必須|パス フレーズの場所 |任意の有効な UNC またはローカル ファイル パス|


>[!TIP]
> エージェント構成のログは、%ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log にあります。
