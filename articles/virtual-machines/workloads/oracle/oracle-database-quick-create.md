---
title: "Azure VM での Oracle 12c データベースの作成 | Microsoft Docs"
description: "Oracle 12c データベースをすばやく作成し、Azure 環境で実行します。"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: tonyguid
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/26/2017
ms.author: rclaus
ms.translationtype: Human Translation
ms.sourcegitcommit: db034a8151495fbb431f3f6969c08cb3677daa3e
ms.openlocfilehash: ba55e2e4449737c4b759211cf0c429d42b961a38
ms.contentlocale: ja-jp
ms.lasthandoff: 04/29/2017

---

# <a name="create-an-oracle-12c-database-on-azure-vm"></a>Azure VM での Oracle 12c データベースの作成

このスクリプトでは、Azure CLI を使用して Oracle 12c データベースを作成します。

Azure CLI は、コマンドラインやスクリプトで Azure リソースを作成および管理するために使用します。 このガイドでは、Azure CLI を使用して Marketplace ギャラリー イメージから Oracle 12c データベースをデプロイする方法について説明します。

開始する前に、Azure CLI がインストールされていることを確認してください。 詳細については、[Azure CLI インストール ガイド](https://docs.microsoft.com/cli/azure/install-azure-cli)を参照してください。 

## <a name="log-in-to-azure"></a>Azure へのログイン 

[az login](/cli/azure/#login) コマンドで Azure サブスクリプションにログインし、画面上の指示に従います。

```azurecli
az login
```

## <a name="create-a-resource-group"></a>リソース グループの作成

[az group create](/cli/azure/group#create) コマンドでリソース グループを作成します。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 

次の例では、`myResourceGroup` という名前のリソース グループを `westus` の場所に作成します。

```azurecli
az group create --name myResourceGroup --location westus
```

## <a name="create-virtual-machine"></a>仮想マシンの作成

[az vm create](/cli/azure/vm#create) コマンドで VM を作成します。 

次の例では、`myVM` という名前の VM を作成し、既定のキーの場所にまだ SSH キーが存在しない場合は SSH キーを作成します。 特定のキーのセットを使用するには、`--ssh-key-value` オプションを使用します。  

```azurecli
az vm create --resource-group myResourceGroup \
    --name myVM \
    --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
    --size Standard_DS2_v2 \
    --generate-ssh-keys
```

VM が作成されると、Azure CLI によって次の例のような情報が表示されます。`publicIpAddress` を書き留めておきます。 このアドレスは、VM へのアクセスに使用されます。

```azurecli
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westus",
  "macAddress": "00-0D-3A-36-2F-56",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.64.104.241",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="connect-to-virtual-machine"></a>仮想マシンへの接続

次のコマンドを使用して、仮想マシンとの SSH セッションを作成します。 IP アドレスを仮想マシンの `publicIpAddress` に置き換えます。

```bash 
ssh <publicIpAddress>
```

## <a name="create-database"></a>データベースの作成

Marketplace イメージには Oracle ソフトウェアが既にインストールされているので、次の手順ではデータベースをインストールします。 最初の手順では、'oracle' スーパー ユーザーとして実行し、ログ用のリスナーを初期化します。

```bash
sudo su - oracle
[oracle@myVM /]$ lsnrctl start
Copyright (c) 1991, 2014, Oracle.  All rights reserved.

Starting /u01/app/oracle/product/12.1.0/dbhome_1/bin/tnslsnr: please wait...

TNSLSNR for Linux: Version 12.1.0.2.0 - Production
Log messages written to /u01/app/oracle/diag/tnslsnr/myVM/listener/alert/log.xml
Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=myVM.twltkue3xvsujaz1bvlrhfuiwf.dx.internal.cloudapp.net)(PORT=1521)))

Connecting to (ADDRESS=(PROTOCOL=tcp)(HOST=)(PORT=1521))
STATUS of the LISTENER
------------------------
Alias                     LISTENER
Version                   TNSLSNR for Linux: Version 12.1.0.2.0 - Production
Start Date                23-MAR-2017 15:32:08
Uptime                    0 days 0 hr. 0 min. 0 sec
Trace Level               off
Security                  ON: Local OS Authentication
SNMP                      OFF
Listener Log File         /u01/app/oracle/diag/tnslsnr/myVM/listener/alert/log.xml
Listening Endpoints Summary...
  (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=myVM.twltkue3xvsujaz1bvlrhfuiwf.dx.internal.cloudapp.net)(PORT=1521)))
The listener supports no services
The command completed successfully
```

次の手順では、データベースを作成します。

```bash
[oracle@myVM /]$ dbca -silent \
   -createDatabase \
   -templateName General_Purpose.dbc \
   -gdbname cdb1 \
   -sid cdb1 \
   -responseFile NO_VALUE \
   -characterSet AL32UTF8 \
   -sysPassword OraPasswd1 \
   -systemPassword OraPasswd1 \
   -createAsContainerDatabase true \
   -numberOfPDBs 1 \
   -pdbName pdb1 \
   -pdbAdminPassword OraPasswd1 \
   -databaseType MULTIPURPOSE \
   -automaticMemoryManagement false \
   -storageType FS \
   -ignorePreReqs

Copying database files
1% complete
2% complete
8% complete
13% complete
19% complete
27% complete
Creating and starting Oracle instance
29% complete
32% complete
33% complete
34% complete
38% complete
42% complete
43% complete
45% complete
Completing Database Creation
48% complete
51% complete
53% complete
62% complete
70% complete
72% complete
Creating Pluggable Databases
78% complete
100% complete
Look at the log file "/u01/app/oracle/cfgtoollogs/dbca/cdb1/cdb1.log" for further details.
```

## <a name="preparing-for-connectivity"></a>接続の準備 
データベースが適切に初期化されていることを確認するために、ローカル接続をテストします。 これは、`sqlplus` に接続して行うのが最も簡単です。  接続する前に、環境変数をいくつか、具体的には *ORACLE_HOME* 環境変数と *ORACLE_SID* 環境変数を設定する必要があります。

```bash
ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1; export ORACLE_HOME

ORACLE_SID=cdb1; export ORACLE_SID
```

必要に応じて、ORACLE_HOME と ORACLE_SID を .bashrc ファイルに追加できます。これにより、今後のログインで使用できるように設定が保存されます。

```
# add oracle home
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1

# add oracle sid
export ORACLE_SID=cdb1

```

## <a name="setup-connectivity-to-oracle-em-express"></a>Oracle EM Express への接続のセットアップ

Oracle EM Express で GUI 管理ツールを使って、データベースを操作できるようにします。  Oracle EM Express に接続するには、まず Oracle でポートを構成する必要があります。

```bash
$ sudo su - oracle

sqlplus / as sysdba

SQL*Plus: Release 12.1.0.2.0 Production on Fri Apr 7 13:16:30 2017

Copyright (c) 1982, 2014, Oracle.  All rights reserved.


Connected to:
Oracle Database 12c Enterprise Edition Release 12.1.0.2.0 - 64bit Production
With the Partitioning, OLAP, Advanced Analytics and Real Application Testing options

SQL> select con_id, name, open_mode from v$pdbs;

    CON_ID NAME                           OPEN_MODE
---------- ------------------------------ ----------
         2 PDB$SEED                       READ ONLY
         3 PDB1                           MOUNT

SQL> alter session set container=pdb1;

Session altered.

SQL> alter database open;

database opened.

SQL> alter session set container=pdb1;

Session altered.

SQL> exec DBMS_XDB_CONFIG.SETHTTPSPORT(5502);

PL/SQL procedure successfully completed.
```
## <a name="automating-database-startup-and-shutdown"></a>データベースのスタートアップとシャットダウンの自動化

作成された Oracle インスタンスのセットアップは、コンピューターの起動時に自動的には開始されません。  このタスクを実現するには、ルートとしてログインし、システム ファイルをいくつか作成/更新する必要があります。

```bash
# sudo su -
```

/etc/oratab ファイルを既定の "N" から "Y" に更新します

```
cdb1:/u01/app/oracle/product/12.1.0/dbhome_1:Y
```

次に、/etc/init.d/dbora ファイルを作成します

```bash
#!/bin/sh
# chkconfig: 345 99 10
# description: Oracle auto start-stop script.
#
# Set ORA_HOME to be equivalent to the $ORACLE_HOME
ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
ORA_OWNER=oracle

case "$1" in
'start')
    # Start the Oracle databases:
    # The following command assumes that the oracle login
    # will not prompt the user for any values
    # Remove "&" if you don't want startup as a background process.
    su - $ORA_OWNER -c "$ORA_HOME/bin/dbstart $ORA_HOME" &
    touch /var/lock/subsys/dbora
    ;;

'stop')
    # Stop the Oracle databases:
    # The following command assumes that the oracle login
    # will not prompt the user for any values
    su - $ORA_OWNER -c "$ORA_HOME/bin/dbshut $ORA_HOME" &
    rm -f /var/lock/subsys/dbora
    ;;
esac
```

アクセス許可を変更します

```bash
# chgrp dba /etc/init.d/dbora
# chmod 750 /etc/init.d/dbora
```
スタートアップとシャットダウンのシンボリック リンクを作成します

```bash
# ln -s /etc/init.d/dbora /etc/rc.d/rc0.d/K01dbora
# ln -s /etc/init.d/dbora /etc/rc.d/rc3.d/S99dbora
# ln -s /etc/init.d/dbora /etc/rc.d/rc5.d/S99dbora
```

VM を再起動してテストします
```bash
# reboot
```

## <a name="opening-the-ports-for-connectivity"></a>接続のポートを開く

最後に、外部エンドポイントを構成します。 VM を保護する Azure ネットワーク セキュリティ グループを構成するために、VM の SSH セッションを終了します。 Oracle DB にリモートでアクセスするためのエンドポイントを開くには、次のコマンドを実行します。 

```azurecli
az network nsg rule create --resource-group myResourceGroup\
    --nsg-name myVmNSG --name allow-oracle\
    --protocol tcp --direction inbound --priority 999 \
    --source-address-prefix '*' --source-port-range '*' \
    --destination-address-prefix '*' --destination-port-range 1521 --access allow
```

結果は、次の応答のようになります。

```
{
  "access": "Allow",
  "description": null,
  "destinationAddressPrefix": "*",
  "destinationPortRange": "1521",
  "direction": "Inbound",
  "etag": "W/\"bd77dcae-e5fd-4bd6-a632-26045b646414\"",
  "id": "/subscriptions/<subscription-id>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myVmNSG/securityRules/allow-oracle",
  "name": "allow-oracle",
  "priority": 999,
  "protocol": "Tcp",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sourceAddressPrefix": "*",
  "sourcePortRange": "*"
}
```

Oracle EM Express にリモートでアクセスするためのエンドポイントを開くには、次のコマンドを実行します。

```azurecli
az network nsg rule create --resource-group myResourceGroup\
    --nsg-name myVmNSG --name allow-oracle-EM\
    --protocol tcp --direction inbound --priority 1001 \
    --source-address-prefix '*' --source-port-range '*' \
    --destination-address-prefix '*' --destination-port-range 5502 --access allow
```

結果は、次の応答のようになります。

```azurecli
{
  "access": "Allow",
  "description": null,
  "destinationAddressPrefix": "*",
  "destinationPortRange": "5502",
  "direction": "Inbound",
  "etag": "W/\"06c68b5e-1b3f-4ae0-bcf6-59b3b981d685\"",
  "id": "/subscriptions/2dad32d6-b188-49e6-9437-ca1d51cec4dd/resourceGroups/kennyRG/providers/Microsoft.Network/networkSecurityGroups/kennyVM1NSG/securityRules/allow-oracle-EM",
  "name": "allow-oracle-EM",
  "priority": 1001,
  "protocol": "Tcp",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sourceAddressPrefix": "*",
  "sourcePortRange": "*"
}
```

ブラウザーから EM Express に接続します
```
https://<VM hostname>:5502/em
```
SYS アカウントを使用して、インストール中に指定したパスワードでログインすることができます


## <a name="delete-virtual-machine"></a>仮想マシンの削除

必要がなくなったら、次のコマンドを使用して、リソース グループ、VM、およびすべての関連リソースを削除できます。

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>次のステップ

[可用性が高い仮想マシンの作成のチュートリアル](../../linux/create-cli-complete.md)

[VM デプロイ CLI サンプルを探索する](../../linux/cli-samples.md)

