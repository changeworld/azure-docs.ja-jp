<properties
   pageTitle="Azure Backup に関する制限の表"
   description="Azure Backup に関するシステムの制限について説明します。"
   services="backup"
   documentationCenter="NA"
   authors="Jim-Parker"
   manager="jwhit"
   editor="" />
<tags  ms.service="backup" ms.devlang="NA" ms.topic="article" ms.tgt_pltfrm="NA" ms.workload="TBD" ms.date="10/05/2015" ms.author="trinadhk";"jimpark"; "aashishr" />


次の制限は、Azure Backup に適用されます。

| 制限の種類 | 既定の制限 |
|---|---|
|各資格情報コンテナーに登録できるサーバーやマシンの数|Windows Server/Client/SCDPM で 50 <br/> IaaS VM で 200|
|Azure 資格情報コンテナー ストレージに格納されるデータのデータ ソースのサイズ|最大 54400 GB<sup>1</sup>|
|各 Azure サブスクリプションで作成できるバックアップ資格情報コンテナー数|25|
|1 日にバックアップをスケジュールできる回数|Windows Server/Client で 1 日 3 回<br/> SCDPM で 1 日 2 回<br/> IaaS VM で 1 日 1 回|
|バックアップ用に Azure の仮想マシンに接続できるデータ ディスクの数|16|

- <sup>1</sup> 54400 GB の制限は、IaaS VM のバックアップには適用されません。

<!---HONumber=Oct15_HO3-->