---
title: "Emulator Express を使用したローカル コンピューターでのクラウド サービス実行とデバッグ | Microsoft Docs"
description: "Emulator Express を使用したローカル コンピューターでのクラウド サービス実行とデバッグ"
services: visual-studio-online
documentationcenter: n/a
author: TomArcher
manager: douge
editor: 
ms.assetid: 73108f98-a552-4817-b7a1-551367b71906
ms.service: visual-studio-online
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/11/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: b09fb0be256fc4cc832822f676b6d1f9de1813cb


---
# <a name="using-emulator-express-to-run-and-debug-a-cloud-service-on-a-local-machine"></a>Emulator Express を使用したローカル コンピューターでのクラウド サービス実行とデバッグ
Emulator Express を使用することにより、管理者として Visual Studio を実行せずに、クラウド サービスをテストおよびデバッグできます。 クラウド サービスの要件に応じて、Emulator Express または完全なエミュレーターのどちらを使用するかをプロジェクト設定で指定できます。 完全なエミュレーターの詳細については、「[コンピューティング エミュレーターでの Azure アプリケーションの実行](storage/storage-use-emulator.md)」を参照してください。 Emulator Express は Azure SDK 2.1 で最初に付属し、Azure SDK 2.3 以降は既定のエミュレーターになりました。

## <a name="using-emulator-express-in-the-visual-studio-ide"></a>Visual Studio IDE での Emulator Express の使用
Azure SDK 2.3 以降で新しいプロジェクト作成すると、Emulator Express が既に選択されています。 以前のバージョンの SDK で作成した既存のプロジェクトは、次の手順に従って Emulator Express を選択します。

### <a name="to-configure-a-project-to-use-emulator-express"></a>Emulator Express を使用するようにプロジェクトを構成するには
1. Azure プロジェクトのショートカット メニューで、**[プロパティ]** を選択し、**[Web]** タブを選択します。
2. **[ローカル開発サーバー]** で、**[IIS Express を使用する]** オプションを選択します。 Emulator Express は IIS Web サーバーと互換性がありません。
3. **[エミュレーター]** で、**[Emulator Express を使用する]** オプションを選択します。
   
    ![Emulator Express](./media/vs-azure-tools-emulator-express-debug-run/IC673363.gif)

## <a name="launching-emulator-express-at-a-command-prompt"></a>コマンド プロンプトでの Emulator Express の起動
コマンド プロンプトで、Express バージョンの Azure コンピューティング エミュレーター csrun.exe を起動するには、/useemulatorexpress オプションを使用します。

## <a name="limitations"></a>制限事項
Emulator Express を使用する前に、いくつかの制限事項に注意する必要があります。

* Emulator Express は IIS Web サーバーと互換性がありません。
* クラウド サービスには複数のロールを含めることができますが、各ロールのインスタンスは 1 つに制限されます。
* 1,000 未満のポート番号にはアクセスできません。 たとえば、通常 1,000 未満のポートが使用される認証プロバイダーを使用する場合は、この値を 1,000 を超えるポート番号に変更する必要がある可能性があります。
* Azure コンピューティング エミュレーターに適用されるすべての制限は、Emulator Express にも適用されます。 たとえば、デプロイあたり 50 を超えるロール インスタンスを保持できません。  [コンピューティング エミュレーターでの Azure アプリケーションの実行](http://go.microsoft.com/fwlink/p/?LinkId=623050)

## <a name="next-steps"></a>次のステップ
[Cloud Services のデバッグ](https://msdn.microsoft.com/library/azure/ee405479.aspx)




<!--HONumber=Nov16_HO3-->


