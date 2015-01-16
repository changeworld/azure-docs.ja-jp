<properties title="Azure Websites Web Hosting Plans In-Depth Overview" pageTitle="詳解 Azure Websites の Web ホスティング プラン - Windows Azure 機能ガイド" description="Azure Websites の Web ホスティング プランのしくみと、それが管理エクスペリエンスにもたらすメリットについて説明します。" metaKeywords="Azure Web Sites, Azure Websites, WHP, Web Hosting Plan, Web Hosting Plans, Resource Groups" services="web-sites" solutions="web" documentationCenter="Infrastructure" authors="Byron Tardif and Yochay Kiryaty" videoId="" scriptId="" manager="wpickett" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="Byron Tardif and Yochay Kiryaty" />
</br>
#詳解 Azure Websites の Web ホスティング プラン#
</br>
Web ホスティング プラン (WHP) は、Web サイト間で共有できる一連の機能と容量を表します。  Web ホスティング プランでは、Azure Websites の価格レベルが 4 つ (無料、共有、基本、標準) サポートされ、価格レベルごとに利用できる機能と容量が異なります。  サブスクリプション、リソース グループ、地理的位置が同じサイト間では、Web ホスティング プランを共有できます。同じ Web ホスティング プランに属するすべてのサイトは、その Web ホスティング プラン階層で定義されているすべての機能を利用できます。特定の Web ホスティング プランに関連付けられているすべての Web サイトは、その Web ホスティング プランに定義されているリソース上で実行されます。たとえば、"S" サイズの 2 台の仮想マシンを使用するように Web ホスティング プランが構成されている場合、その Web ホスティング プランに関連付けられているすべてのサイトが、その 2 台の仮想マシンで実行されます。通常の Azure Websites と同様、サイトを実行する仮想マシンは完全に管理され、高い可用性が確保されます。
</br>
この記事では、Web ホスティング プランの階層とスケール、Web サイトを管理する中での働きなど、主な特徴を詳しく見ていきます。 
</br>
##Web サイト、Web ホスティング プラン、リソース グループ##
</br>
1 つの Web サイトは、常時 1 つの Web ホスティング プランにのみ関連付けることができます。Web ホスティング プランは、リソース グループに関連付けられます。リソース グループは Azure 内の新しい概念であり、そのグループに含まれるすべてのリソースに対してライフサイクルの境界という機能を果たします。リソース グループを使用することによって、1 つのアプリケーションのすべての構成要素をまとめて管理することができます。 
</br>
1 つのリソース グループに複数の Web ホスティング プランを追加して、それぞれの Web ホスティング プランに異なる機能を持たせたうえで、サイトを関連付けることにより、それらのサイトは、ホスティング プランがそれぞれ備えている機能一式を利用することができます。  その関係を示したのが次の画像です。
</br>
</br>
![Resource Groups and Web Hosting Plans](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview01.png)
</br>
</br>
Web ホスティング プランは 1 つのリソース グループに複数割り当てることができるため、複数のサイトをそれぞれ異なるリソース (主に Web サイトを実行する仮想マシン) に割り当てることができます。たとえば、開発テスト環境サイトと運用環境サイト間でリソースを分離することができます。運用環境サイト専用のリソースを持った Web ホスティング プランと、開発およびテスト環境サイト用の Web ホスティング プランを割り当てることが可能です。 
</br>
また、1 つのリソース グループに複数の Web ホスティング プランを割り当てることによって、複数のリージョンにまたがるアプリケーションを定義することもできます。たとえば、1 つの高可用性 Web サイトを 2 つのリージョンで実行する場合、2 つの Web ホスティング プラン (リージョンごとに 1 つ) を追加したうえで、各 Web ホスティング プランに Web サイトを 1 つ関連付けます。この場合、単一のリソース グループで 1 つのアプリケーションを定義し、そこにすべてのサイトを関連付けることができます。複数の Web ホスティング プランと複数のサイトを 1 つのリソース グループにまとめることで、Web サイトの管理と統制がしやすくなり、正常性の確認も容易になります。特定のアプリケーションに使用する Web サイト リソースと個々のサイトを管理することに加え、関連するあらゆる Azure リソース (SQL Azure データベースなど) やチーム プロジェクトを関連付けることができます。 
</br>
##リソース グループと Web ホスティング プランを作成するタイミング##
</br>
新しい Web サイトを作成するとき、作成しようとしている Web サイトが、新規性のある Web アプリケーションといえる場合は、新しいリソース グループの作成を検討してください。この場合、リソース グループとそれに関連する Web ホスティング プラン、Web サイトを新規作成することをお勧めします。新しい Azure ポータル プレビューから、ギャラリー (Web サイト + SQL の新規作成オプション) を使用してこのような新しい Web サイトを作成すると、新しいサイト用の新しいリソース グループと Web ホスティング プランが既定で作成されます。ただし、これらの既定値は上書きすることができます。
</br>
</br>
![Creating a new Web Hosting Plan](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview02.png)
</br>
</br>
既存のリソース グループには、新しい Web サイトやその他のリソースをいつでも追加することができます。既存のリソース グループのコンテキストから新しい Web サイトを作成すると、Web サイトの新規作成ウィザードにより、既存のリソースと Web ホスティング プランが既定で選択されます。この場合も、既定の動作を必要に応じて上書きすることが可能です。既存のリソース グループに新しい Web サイトを追加するときは、既にある Web ホスティング プランにサイトを追加するか (新しい Azure ポータル プレビューでは、こちらが既定で選択されます)、またはサイトの追加先となる新しい Web ホスティング プランを作成するかを選ぶことができます。
</br>
新しいホスティング プランを作成すると、Web サイトのリソース一式を新たに割り当てることができ、より柔軟にリソースの割り当てを制御することができます。Web ホスティング プランごとに独立した一連の仮想マシンが割り当てられるためです。同じリージョンに属している Web ホスティング プラン間であれば Web サイトの移動が可能であるため、Web ホスティング プランを新規に作成するかどうかの判断はさほど重要ではありません。特定の Web サイトのリソース消費が激しくなってきた場合や、いくつかの Web サイトを分離する必要が生じた場合は、新しい Web ホスティング プランを作成して、そこに Web サイトを移動することができます。
</br>
別のリージョンに新しい Web サイトを作成する必要が生じたものの、そのリージョンにまだ Web ホスティング プランが存在しない場合は、Web サイトを関連付けるための新しい Web ホスティング プランを当該リージョンに作成する必要があります。 
</br>
ただし、リソース グループ間で Web ホスティング プランや Web サイトを移動することはできないので注意してください。また、2 つの異なるリージョンに属している 2 つの Web ホスティング プラン間で Web サイトを移動することもできません。 
</br>
##Azure プレビュー ポータルの既存のリソース グループ##
</br>
既に Web サイトが Azure Websites に存在する場合、そのすべての Web サイトが Azure プレビュー ポータルに表示されます。左側のナビゲーション ウィンドウにある **[参照]** ボタンをクリックし、**[Web サイト]** をクリックすると、すべての Web サイトがフラットに一覧表示されます。
</br>
</br>
![See all your website as a flat list](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview03.png)
</br>
</br>
また、左側のナビゲーション ウィンドウの **[参照]** ボタンをクリックし、**[リソース グループ]** を選択すると、作成済みのすべてのリソース グループを表示することができます。
</br>
</br>
![See all the resource groups that have been created](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview04.png)
</br>
</br>
また、既に Web サイトがある各リージョンには、既定のリソース グループが自動的に生成されていることがわかります。Web サイトに関して自動的に生成されたリソース グループの名前は *Default-Web-<場所の名前>* になります。ここで、<場所の名前> は、Azure のリージョンを表します (例: *Default-Web-WestUS*)。各リソース グループには、そのグループのリージョンに既にあるサイトがすべて表示されます。完全版であれ、プレビュー版であれ、Azure ポータルで過去に作成したサイトと将来作成するサイトは、どちらのポータルでも利用できるようになります。 
</br>
Web サイトはすべて Web ホスティング プランに関連付ける必要があるため、Microsoft では、以下の規則に従い、各リージョンの既存のサイトに対する既定の Web ホスティング プランを作成しました。
</br>
* **無料**の Web サイトはすべて**既定**の Web ホスティング プランに関連付けられ、その価格レベルは**無料**に設定されます。 
</br>
* **共有**の Web サイトはすべて**既定**の Web ホスティング プランに関連付けられ、その価格レベルは**共有**に設定されます。
</br>
* **標準**の Web サイトはすべて既定の Web ホスティング プランに関連付けられ、その価格レベルは**標準**に設定されます。 
</br>
この Web ホスティング プランの名前は **DefaultServerFarm** になります。従来の API をサポートする関係上、この名前が選ばれました。実際には **Web ホスティング プラン**を表していることを考えると、**ServerFarm** という名前はやや正確性に欠けますが、あくまでこれは Web ホスティング プランの名前です。実体ではないのでご注意ください。 
</br>
##Web ホスティング プランの FAQ##
</br>
**Q**: Web ホスティング プランの作成方法を教えてください。
</br>
**A**: Web ホスティング プランはコンテナーであるため、空の Web ホスティング プランを作成することはできません。新しい Web ホスティング プランは、サイトの作成時に明示的に作成します。
</br>
新しい **Azure プレビュー ポータル**の UI を使って作成するには、**[新規]** をクリックし、**[Web サイト]** を選択して、Web サイトの作成ブレードを表示します。以下の 1 つ目の画像には、左下に **[新規]** アイコンが表示されています。また、2 つ目の画像には、**[Web サイト]** 作成ブレード、**[Web ホスティング プラン]** ブレード、**[価格レベル]** ブレードが確認できます。
</br>
</br>
![Create a new website](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview05.png)
</br>
</br>
![Website, Web Hosting Plan and pricing tier blades](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview06.png)
</br>
</br>
この例では、**contosomarketing** という新しい Web サイトを作成し、**contoso** という新しい Web ホスティング プランに配置しています。この Web ホスティング プランに選択した料金レベルは "**S、標準**" です。Web ホスティング プランの価格レベルと、それぞれに用意されている機能、料金、スケール オプションの詳細については、「[Websites (Websites)](http://go.microsoft.com/?linkid=9845586)」を参照してください。 
</br>
既にある Azure ポータルに Web ホスティング プランを作成することもできます。これは、**[Web ホスティング プラン]** ボックスの一覧の **[新しい Web ホスティング プランの作成]** を選択することによって、**簡易作成ウィザード**の中で行います。
</br>
</br>
![Create new web hosting plan in the existing portal](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview07.png)
</br>
</br>
この例では、**northwind** という新しいサイトを作成し、Web ホスティング プランを新規作成するように選択しています。この操作を実行すると、Web サイト **northwind** を含んだ **default0** という新しい Web ホスティング プランが作成されます。この方法で作成されたすべての Web ホスティング プランは、この名前付け規則に従います。Web ホスティング プランの作成後に名前を変更することはできません。また、この方法で作成された Web ホスティング プランは、**無料**の価格レベルに作成されます。
</br>
**Q**: **Web ホスティング プラン**にサイトを割り当てる方法を教えてください。
</br>
**A**: Web ホスティング プランへのサイトの割り当ては、サイトの作成プロセスの中で行います。この作業を新しい **Azure プレビュー ポータル**の UI で行うには、**[新規]** をクリックし、**[Web サイト]** を選択します。
</br>
</br>
![Create a new website](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview08.png)
</br>
</br>
次に、Web サイトの作成ブレードで、ホスティング プランを選択します。
</br>
</br>
![Select a hosting plan](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview09.png)
</br>
</br>
既存の Azure ポータルを使用して特定の Web ホスティング プランにサイトを作成することもできます。これは、**簡易作成**ウィザードの中で行います。Web サイトの URL を入力した後、**[Web ホスティング プラン]** ボックスの一覧から、サイトの追加先となるプランを選択します。
</br>
</br>
![Select a hosting plan in the existing portal](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview10.png)
</br>
</br>
**Q**: Web ホスティング プラン間でサイトを移動する方法を教えてください。
</br>
**A**: Web ホスティング プラン間でのサイトの移動は、Azure プレビュー ポータルで行うことができます。Web サイトは、同じリソース グループに属している同じ地理的リージョンの Web ホスティング プラン間で移動することができます。
</br>
サイトを別のプランに移動するには、移動するサイトの Web サイト ブレードに移動します。  続けて、**[Web ホスティング プラン]** をクリックします。
</br>
</br>
![Choose a new or existing web hosting plan](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview22.png)
</br>
</br>
すると、[Web ホスティング プラン] ブレードが表示されます。この時点で、既存の Web ホスティング プランを選択するか、新しい Web ホスティング プランを作成することができます。異なる地理的な場所またはリソース グループに属しているプランは淡色表示され、選択できません。
</br>
Web ホスティング プランにそれぞれ価格レベルが割り当てられている点に注目してください。**無料**レベルの Web ホスティング プランから**標準**の Web ホスティング プランにサイトを移動すると、標準レベルのすべての機能とリソースを Web サイトで利用できるようになります。 
</br>
</br>
**Q**: Web ホスティング プランを拡張する方法を教えてください。
</br>
**A**: Web ホスティング プランは 2 とおりの方法で拡張できます。1 つは、Web ホスティング プランとそこに関連付けられているすべてのサイトをスケール アップする方法です。Web ホスティング プランの価格レベルを変更すると、その Web ホスティング プラン内のすべてのサイトに、その価格レベルで定義されている機能とリソースが適用されます。 
</br>
以下の画像には、**[Web ホスティング プラン]** ブレードと **[価格レベル]** ブレードが表示されています。**[Web ホスティング プラン]** ブレードの **[価格レベル]** の部分をクリックすると、**[価格レベル]** ブレードが展開表示され、Web ホスティング プランの価格レベルを変更することができます。
</br>
</br>
![The Web Hosting Plan blade and the Pricing Tier](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview16.png)
</br>
</br>
Web ホスティング プラン内のインスタンス数を増やすことによってスケール アウトする方法もあります。これがプランを拡張する 2 つ目の方法です。以下の画像には、**[Web ホスティング プラン]** ブレードと **[スケール]** ブレードが表示されています。**[Web ホスティング プラン]** ブレードの [スケール] 領域をクリックして展開表示することによって、プランのインスタンス数を変更することができます。
</br>
</br>
![Changing the instance count of a hosting plan](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview17.png)
</br>
</br>
上の画像の Web ホスティング プランは、**標準**価格レベルを使用するように構成されているため、**[自動スケール]** オプションが有効になっています。 
</br>
完全版の Azure ポータルでは、以下の図に示すように、**[スケール]** タブでこの操作を実行できます。
</br>
</br>
![Changing the instance count of a hosting plan in the existing portal](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview18.png)
</br>
</br>
**Q**: Web ホスティング プランを削除する方法を教えてください。
</br>
**A**: Web ホスティング プランを削除するにはまず、そこに関連付けられている Web サイトをすべて削除する必要があります。Web ホスティング プランからすべての Web サイトを削除した後、[Web ホスティング プラン] ブレードから Web ホスティング プランを削除できます。
</br>
</br>
![Deleting a web hosting plan](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview19.png)
</br>
</br>
完全版の Azure ポータルでは、Web ホスティング プランに残った最後の Web サイトを削除すると、関連付けられている Web ホスティング プランが自動的に削除されます。
</br>
**Q**: Web ホスティング プランを監視する方法を教えてください。
</br>
**A**: Web ホスティング プランの監視には、[Web ホスティング プラン] ブレードの [監視] 領域を使用できます。
</br>
</br>
![Monitoring a web hosting plan](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview20.png)
</br>
</br>
モニタリング コントロールを右クリックし、**[クエリの編集]** を選択すると、コントロールをカスタマイズすることができます。
</br>
</br>
![Editing the monitoring controls](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview21.png)
</br>
</br>
公開されるメトリックは次のとおりです。
</br>
* CPU の割合
</br>
* メモリの割合
</br>
* ディスク キューの長さ 
</br>
* HTTP キューの長さ 
</br>
このメトリックは、特定の Web ホスティング プランに属している全インスタンスの平均使用量を表します。これらすべてのメトリックを使用して、アラートや自動スケール ルールをセットアップすることができます。
</br>
##まとめと結論##
</br>
Web ホスティング プランは、Web サイト間で共有できる一連の機能と容量を表します。  Web ホスティング プランを使用することで、一連のリソース (仮想マシン) に対するサイトの割り当てを柔軟に行えるようになり、Web サイトの目的に合わせて Azure リソースを割り当てて使用することができます。 
