---
title: バックアップ前およびバックアップ後のスクリプトの使用
description: この記事には、バックアップ前およびバックアップ後のスクリプトを指定する手順が含まれています。 Azure Backup Server (MABS)。
ms.topic: conceptual
ms.date: 07/06/2021
ms.openlocfilehash: 48d65679f4ff1b6486513067058c1f385e4f1434
ms.sourcegitcommit: e0ef8440877c65e7f92adf7729d25c459f1b7549
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2021
ms.locfileid: "113568608"
---
# <a name="using-pre-backup-and-post-backup-scripts"></a>バックアップ前およびバックアップ後スクリプトの使用

適用対象: Microsoft Azure Backup Server (MABS)

"_バックアップ前スクリプト_" は保護されたコンピューターにあるスクリプトで、各 MABS バックアップ ジョブの前に実行され、保護されたデータ ソースをバックアップのために準備します。

"_バックアップ後スクリプト_" は、MABS のバックアップ ジョブの後に実行されるスクリプトで、仮想マシンをオンラインに戻すなどバックアップ後の処理を行います。

コンピューターに保護エージェントをインストールすると、保護されたコンピューターの "_インストール パス_" \Microsoft Data Protection Manager\DPM\Scripting フォルダーに ScriptingConfig.xml ファイルが追加されます。 コンピューターの保護されたデータ ソースごとに、バックアップ前スクリプトとバックアップ後スクリプトを ScriptingConfig.xml で指定できます。

>[!Note]
>バックアップ前およびバックアップ後のスクリプトを VBScript にすることはできません。 代わりに、スクリプトの周りに **cscript myscript.vbs** を含むラッパー コマンドを使用する必要があります。

保護ジョブが MABS で実行されるときに、保護されたコンピューターの ScriptingConfig.xml が確認されます。 バックアップ前スクリプトを指定すると、MABS はスクリプトを実行してからジョブを完了します。 バックアップ後スクリプトを指定すると、MABS はジョブを完了してからスクリプトを実行します。

>[!Note]
>保護ジョブには、レプリカ作成、高速完全バックアップ、同期、および整合性チェックが含まれます。

MABS では、ローカル システム アカウントを使用して、バックアップ前およびバックアップ後スクリプトが実行されます。 ベスト プラクティスとして、スクリプトの読み取りと実行アクセスの許可が管理者およびローカル システム アカウントのみに与えられていることを確認する必要があります。 このレベルのアクセス許可を使用することで、権限のないユーザーがスクリプトを変更するのを防ぎます。

**ScriptingConfig.xml**

```
<?xml version="1.0" encoding="utf-8"?>
<ScriptConfiguration xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
xmlns:xsd="http://www.w3.org/2001/XMLSchema" 
xmlns="http://schemas.microsoft.com/2003/dls/ScriptingConfig.xsd">
   <DatasourceScriptConfig DataSourceName="Data source">
     <PreBackupScript>”Path\Script Parameters” </PreBackupScript>
     <PostBackupScript>"Path\Script Parameters” </PostBackupScript>
     <TimeOut>30</TimeOut>
   </DatasourceScriptConfig>
</ScriptConfiguration>
```

バックアップ前およびバックアップ後スクリプトを指定するには

1. 保護されたコンピューターで、ScriptingConfig.xml ファイルを XML エディターまたはテキスト エディターで開きます。

   >[!Note]
   >DataSourceName 属性を **Drive:** として指定する必要があります (たとえば、データ ソースが D ドライブにある場合は D:)。

1. データ ソースごとに、DatasourceScriptConfig の要素を次のように入力します。


   1. DataSourceName 属性には、ファイル データ ソースではデータ ソース ボリューム、またはその他すべてのデータ ソースでは名前を入力します。 アプリケーション データのデータ ソース名の形式は、SQL では _インスタンス\データベース_、Exchange では _ストレージ グループ名_、Virtual Server では _論理パス\コンポーネント名_、Windows SharePoint Services では _SharePoint ファーム\SQL Server 名\SQL インスタンス名\SharePoint Config DB_ にする必要があります。
   1. _PreBackupScript_ タグにパスとスクリプト名を入力します。
   1. _PreBackupCommandLine_ タグに、スクリプトに渡すコマンド ライン パラメーターをスペースで区切って入力します。
   1. _PostBackupScript_ タグにパスとスクリプト名を入力します。
   1. _PostBackupCommandLine_ タグに、スクリプトに渡すコマンド ライン パラメーターをスペースで区切って入力します。
   1. _TimeOut_ タグに、スクリプトを呼び出してからタイムアウトになってスクリプトに失敗とマークするまで MABS が待つ時間を分単位で入力します。

1. ScriptingConfig.xml ファイルを保存します。

>[!Note]
>MABS によって追加のブール値 (true/false) パラメーターがバックアップ後スクリプト コマンドにサフィックスとして付けられ、MABS バックアップ ジョブの状態が示されます。
