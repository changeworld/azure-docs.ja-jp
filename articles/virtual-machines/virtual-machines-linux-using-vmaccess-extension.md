<properties
    pageTitle="VMAccess 拡張機能を使用した Azure Linux VM のアクセスのリセット | Microsoft Azure"
    description="VMAccess 拡張機能を使用した Azure Linux VM のアクセスのリセット"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="vlivech"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"
/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/30/2016"
    ms.author="v-livech"
/>

# VMAccess 拡張機能を使用して、Azure Linux VM 上のユーザー、SSH を管理し、ディスクをチェックまたは修復する

この記事では、Azure VMAcesss 拡張機能を使用して、Linux 上のディスクのチェックや修復、ユーザー アクセスのリセット、ユーザー アカウントの管理、またはSSHD 構成のリセットを行う方法を説明します。

前提条件は、[Azure アカウント](https://azure.microsoft.com/pricing/free-trial/)、[SSH の公開キーと秘密キー](virtual-machines-linux-mac-create-ssh-keys.md)があり、Azure CLI がインストールされていて、`azure config mode arm` を使用して Resource Manager モードに切り替えてあることです。

## クイック コマンド

Linux VM で VMAccess を使用する方法は 2 つあります。

- Azure CLI と必要なパラメーターを使用する。
- VMAccess が処理して、動作の基となる未加工の JSON ファイルを使用する。

クイック コマンド セクションで、Azure CLI の `azure vm reset-access` メソッドを使用します。以下のコマンド例の "example" を含む値は、実際の環境の値に置き換えてください。

## リソース グループと Linux VM の作成

```bash
azure group create resourcegroupexample westus
```

```bash
azure vm quick-create \
-M ~/.ssh/id_rsa.pub \
-u userexample \
-g resourcegroupexample \
-l westus \
-y Linux \
-n debianexamplevm \
-Q Debian
```

## root パスワードのリセット

root パスワードをリセットするには:

```bash
azure vm reset-access -g exampleResourceGroup -n exampleVMName -u root -p examplenewPassword
```

## SSH キーのリセット

root 以外のユーザーの SSH キーをリセットするには:

```bash
azure vm reset-access -g exampleResourceGroup -n exampleVMName -u userexample -M ~/.ssh/id_rsa.pub
```

## ユーザーの作成

ユーザーを作成するには:

```bash
azure vm reset-access -g exampleResourceGroup -n exampleVMName -u userexample -p examplePassword
```

## ユーザーの削除

```bash
azure vm reset-access -g exampleResourceGroup -n exampleVMName -R userexample
```

## SSHD のリセット

SSHD 構成をリセットするには:

```bash
azure vm reset-access -g exampleResourceGroup -n exampleVMName -r
```


## 詳細なチュートリアル

### VMAccess が定義済み:

Linux VM 上のディスクがエラーを示しています。何らかの理由で Linux VM の root パスワードをリセットしたか、誤って SSH 秘密キーを削除してしまいました。これがかつてのデータ センターの時代で起きていたら、車で駆けつけ、KVM を開けて、サーバー コンソールにたどり着くことになっていたでしょう。Azure VMAccess 拡張機能は、コンソールにアクセスして、Linux へのアクセスをリセットしたり、ディスク レベルの保守を実行したりできるその KVM スイッチとして考えてください。

詳しいチュートリアルでは、未加工の JSON ファイルを使用する長い形式の VMAccess を使用します。これらの VMAccess JSON ファイルは、Azure テンプレートから呼び出すこともできます。

### VMAccess を使用して Linux VM のディスクをチェックまたは修復する

VMAccess を使用して、Linux VM 下のディスクに対して、fsck を実行できます。また、ディスク チェックを実行し、VMAccess を使用して、ディスクの修復を実行できます。

ディスクをチェックして修復するには、この VMAccess スクリプトを使用します。

`disk_check_repair.json`

```json
{
  "check_disk": "true",
  "repair_disk": "true, user-disk-name"
}
```

次を使用して VMAccess スクリプトを実行します。

```bash
azure vm extension set exampleResourceGroup exampleVM \
VMAccessForLinux Microsoft.OSTCExtensions * \
--private-config-path disk_check_repair.json
```

### VMAccess を使用して Linux へのユーザーのアクセスをリセットする

Linux VM で root へのアクセスを失った場合、VMAccess スクリプトを起動して、root パスワードをリセットできます。

root パスワードをリセットするには、この VMAccess スクリプトを使用します。

`reset_root_password.json`

```json
{
  "username":"root",
  "password":"exampleNewPassword",   
}
```

次を使用して VMAccess スクリプトを実行します。

```bash
azure vm extension set exampleResourceGroup exampleVM \
VMAccessForLinux Microsoft.OSTCExtensions * \
--private-config-path reset_root_password.json
```

root 以外のユーザーの SSH キーをリセットするには、この VMAccess スクリプトを使用します。

`reset_ssh_key.json`

```json
{
  "username":"exampleUser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNG1vHY7P2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7iUo5IdwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5woYtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== exampleUser@exampleServer",   
}
```

次を使用して VMAccess スクリプトを実行します。

```bash
azure vm extension set exampleResourceGroup exampleVM \
VMAccessForLinux Microsoft.OSTCExtensions * \
--private-config-path reset_ssh_key.json
```

### VMAccess を使用して、Linux 上のユーザー アカウントを管理する

VMAccess は、ログインしたり、sudo や root アカウントを使用したりしなくても、Linux VM 上のユーザーを管理するために使用できる Python スクリプトです。

ユーザーを作成するには、この VMAccess スクリプトを使用します。

`create_new_user.json`

```json
{
"username":"exampleNewUserName",
"ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNG1vHY7P2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7iUo5IdwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5woYtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== exampleUser@exampleServer",
"password":"examplePassword",
}
```

次を使用して VMAccess スクリプトを実行します。

```bash
azure vm extension set exampleResourceGroup exampleVM \
VMAccessForLinux Microsoft.OSTCExtensions * \
--private-config-path create_new_user.json
```

ユーザーを作成するには:

`remove_user.json`

```json
{
"remove_user":"exampleUser",
}
```

次を使用して VMAccess スクリプトを実行します。

```bash
azure vm extension set exampleResourceGroup exampleVM \
VMAccessForLinux Microsoft.OSTCExtensions * \
--private-config-path remove_user.json
```

### VMAccess を使用して SSHD 構成をリセットする

Linux VM SSHD 構成を変更して、変更を確認する前に SSH 接続を閉じた場合、SSH 接続を再開できないことがあります。VMAccess を使用して、SSH 経由でログインせずに、SSHD 構成を前回正常起動時の構成にリセットすることができます。

SSHD 構成をリセットするには、この VMAccess スクリプトを使用します。

`reset_sshd.json`

```json
{
  "reset_ssh": true
}
```

次を使用して VMAccess スクリプトを実行します。

```bash
azure vm extension set exampleResourceGroup exampleVM \
VMAccessForLinux Microsoft.OSTCExtensions * \
--private-config-path reset_sshd.json
```

## 次のステップ

Azure VMAccess 拡張機能を使用して Linux を更新することは、実行中の Linux VM で変更を加える 1 つの方法です。また、cloud-init と Azure テンプレートのようなツールを使用して、起動時に Linux VM を変更することもできます。

[仮想マシンの拡張機能とその機能について](virtual-machines-linux-extensions-features.md)

[Linux VM 拡張機能を使用した Azure Resource Manager テンプレートの作成](virtual-machines-linux-extensions-authoring-templates.md)

[cloud-init を利用し、作成時に Linux VM をカスタマイズする](virtual-machines-linux-using-cloud-init.md)

<!---HONumber=AcomDC_0831_2016-->