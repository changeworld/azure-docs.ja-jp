<properties 
	pageTitle="Azure AD Connect の既定の構成の変更" 
	description="Azure AD Connect の既定の構成を変更する方法について説明します。" 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/28/2015" 
	ms.author="billmath"/>

# Azure AD Connect の既定の構成の変更 


ほとんどの場合、Azure AD Connect の既定の構成で、簡単にオンプレミスのディレクトリをクラウドに拡張できます。ただし、既定値を変更し、組織のビジネス ロジックに合わせる必要がある場合もあります。このような場合には、既定の構成を変更できますが、実行する前に、いくつかの点に注意する必要があります。

既定の構成を変更する必要がある場合は、以下の手順を実行します。

- “標準" の同期規則の属性のフローを変更する必要がある場合は、変更しません。代わりに、必要な属性のフローを含む、優先順位の高い (小さい数値の) 新しい同期規則を作成します。
- 同期規則エディターを使用して、カスタムの同期規則をエクスポートします。これにより、障害復旧シナリオの場合に、簡単に再作成をするために使用できる PowerShell スクリプトが提供されます。
- "標準" の同期規則のスコープ設定または結合の設定を変更する必要がある場合は、このことを文書化し、Azure AD Connect の新しいバージョンにアップグレードした後、変更を適用します。 

<!---HONumber=62-->