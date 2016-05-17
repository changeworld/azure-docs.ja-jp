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
    ms.date="04/29/2016"
    ms.author="v-livech"
/>

# VMAccess 拡張機能を使用して、Azure Linux VM 上のユーザー、SSH を管理し、ディスクをチェックまたは修復する

この記事では、VMAcesss VM 拡張機能 [(Github)](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) を使用して、Linux 上のディスクのチェックや修復、ユーザー アクセスのリセット、ユーザー アカウントの管理、またはSSHD 構成のリセットを行う方法を説明します。この記事では、[Azure アカウント](https://azure.microsoft.com/pricing/free-trial/)、[SSH キー](virtual-machines-linux-mac-create-ssh-keys.md)、Azure Linux Virtual Machine、および Azure CLI がインストールされており、`azure config mode arm` を使用して、ARM モードに切り替えられている必要があります。

## クイック コマンド

Linux VM で VMAccess を使用する方法は 2 つあります。1 つは、`azure vm reset-access` と正しいフラグで Azure CLI を使用する方法です。VMAccess を使用する 2 つ目の方法は、VMAccess が処理して、動作の基となる未加工の json ファイルを使用することです。クイック コマンド セクションで、`azure vm reset-access` メソッドを使用します。

以下のコマンド例の &lt; と &gt; で囲まれた値は、実際の環境の値に置き換えてください。

## root パスワードのリセット

root パスワードをリセットするには:

```bash
azure vm reset-access -g <resource group> -n <vm name> -u root -p <examplePassword>
```

## SSH キーのリセット

root 以外のユーザーの SSH キーをリセットするには:

```bash
azure vm reset-access -g <resource group> -n <vm name> -u <exampleUser> -M <~/.ssh/azure_id_rsa.pub>
```

## ユーザーの作成

新しいユーザーを作成するには:

```bash
azure vm reset-access -g <resource group> -n <vm name> -u <exampleNewUserName> -p <examplePassword>
```

## ユーザーの削除

```bash
azure vm reset-access -g <resource group> -n <vm name> -R <exampleNewUserName>
```

## SSHD のリセット

SSHD 構成をリセットするには:

```bash
azure vm reset-access -g <resource group> -n <vm name> -r
```


## 詳細なチュートリアル

### VMAccess が定義済み:

Linux VM 上のディスクがエラーを示しています。何らかの理由で Linux VM の root パスワードをリセットしたか、誤って SSH 秘密キーを削除してしまいました。これがかつてのデータ センター暗黒時代で起きていたら、車で駆けつけ、手形でドアの鍵を開け、ケージの中に入り、KVM をこじ開けて、サーバー コンソールにたどり着くことになっていたでしょう。Azure VMAccess 拡張機能は、コンソールにアクセスして、Linux へのアクセスをリセットしたり、ディスク レベルの保守を実行したりできるその KVM スイッチとして考えてください。

詳しいチュートリアルでは、未加工の json ファイルを使用する長い形式の VMAccess を使用します。これらの VMAccess json ファイルは、Azure テンプレートから呼び出すこともできます。

### VMAccess を使用して Linux VM のディスクをチェックまたは修復する

VMAccess を使用して、Linux VM 下のディスクに対して、fsck を実行できます。ディスク チェックを実行し、何らかのエラーを検出した場合は、ディスクの修復を実行できます。

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
azure vm extension set exampleResourceGruop exampleVM \
VMAccessForLinux Microsoft.OSTCExtensions * \
--private-config-path disk_check_repair.json
```

### VMAccess を使用して Linux へのユーザーのアクセスをリセットする

Linux VM で root へのアクセスを失った場合、VMAccess スクリプトを起動して、root パスワードをリセットし、Linux のロックを解除できます。

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
azure vm extension set exampleResourceGruop exampleVM \
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
azure vm extension set exampleResourceGruop exampleVM \
VMAccessForLinux Microsoft.OSTCExtensions * \
--private-config-path reset_ssh_key.json
```

### VMAccess を使用して、Linux 上のユーザー アカウントを管理する

VMAccess は、ログインしたり、sudo や root アカウントを使用したりしなくても、Linux VM 上のユーザーを管理するために使用できる Python スクリプトです。

新しいユーザーを作成するには、この VMAccess スクリプトを使用します。

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
azure vm extension set exampleResourceGruop exampleVM \
VMAccessForLinux Microsoft.OSTCExtensions * \
--private-config-path create_new_user.json
```

新しいユーザーを作成するには、この VMAccess スクリプトを使用します。

`remove_user.json`

```json
{
"remove_user":"exampleUser",
}
```

次を使用して VMAccess スクリプトを実行します。

```bash
azure vm extension set exampleResourceGruop exampleVM \
VMAccessForLinux Microsoft.OSTCExtensions * \
--private-config-path remove_user.json
```

### VMAccess を使用して Linux 上の SSHD 構成をリセットする

Linux VM SSHD 構成を変更して、変更を確認する前に SSH 接続を閉じた場合、SSH 接続を再開できないことがあります。VMAccess を使用して、SSHD 構成を前回正常起動時の構成にリセットすることができます。

SSHD 構成をリセットするには、この VMAccess スクリプトを使用します。

`reset_sshd.json`

```json
{
  "reset_ssh": true
}
```

次を使用して VMAccess スクリプトを実行します。

```bash
azure vm extension set exampleResourceGruop exampleVM \
VMAccessForLinux Microsoft.OSTCExtensions * \
--private-config-path reset_sshd.json
```

<!---HONumber=AcomDC_0504_2016-->