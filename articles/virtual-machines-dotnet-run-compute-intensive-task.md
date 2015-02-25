<properties pageTitle="仮想マシン上で多くのコンピューティング処理を要する .NET タスク - Azure" description="Azure の仮想マシンに多くのコンピューティング処理を要する .NET アプリケーションをデプロイして実行する方法と、Service Bus キューを使用して進捗をリモート監視する方法について説明します。" services="virtual-machines" documentationCenter=".net" authors="" manager="wpickett" editor="mollybos"/>

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="11/24/2014" ms.author="wpickett"/>

# Azure の仮想マシンで多くのコンピューティング処理を要する .NET タスクを実行する方法

Azure では、仮想マシンを使用して多くのコンピューティング処理を要するタスクを処理できます。たとえば、仮想マシンでタスクを処理して、結果をクライアント マシンやモバイル アプリケーションに配信することができます。このガイドを完了すると、多くのコンピューティング処理を要する .NET アプリケーションを実行し、それを別の .NET アプリケーションから監視できる仮想マシンの作成方法を理解できます。

このチュートリアルは、.NET コンソール アプリケーションの作成方法を知っていることを前提としています。Azure の知識は不要です。 

学習内容:

* Windows 仮想マシンの作成方法
* 仮想マシンにリモート ログインする方法
* Service Bus 名前空間の作成方法
* 多くのコンピューティング処理を要するタスクを実行する .NET アプリケーションの作成方法
* 多くのコンピューティング処理を要するタスクの進捗状況を監視する .NET アプリケーションの作成方法
* .NET アプリケーションの実行方法
* .NET アプリケーションの停止方法

このチュートリアルでは、多くのコンピューティング処理を要するタスクとして、巡回セールスマン問題を使用します。以下は、多くのコンピューティング処理を要するタスクを実行する .NET アプリケーションの例です。

![Traveling Salesman Problem solver][solver_output]

以下は、多くのコンピューティング処理を要するタスクを監視する .NET アプリケーションの例です。

![Traveling Salesman Problem client][client_output]

[AZURE.INCLUDE [create-account-and-vms-note](../includes/create-account-and-vms-note.md)]

<h2>仮想マシンを作成するには</h2>

1. [Azure 管理ポータル](https://manage.windowsazure.com)にログインします。
2. **[新規]** をクリックします。
3. **[仮想マシン]** をクリックします。
4. **[簡易作成]** をクリックします。
5. **[仮想マシンの作成]** 画面で、**[DNS 名]** に値を入力します。
6. **[イメージ]** ボックスの一覧から、**[Windows Server 2012]** などのイメージを選択します。
7. **[ユーザー名]** フィールドに、管理者の名前を入力します。この名前と次に入力するパスワードは忘れないでください。仮想マシンにリモート ログインするときに使用します。
8. **[新しいパスワード]** フィールドにパスワードを入力し、**[確認]** フィールドに再びパスワードを入力します。
9. **[場所]** ボックスの一覧から、仮想マシンを配置するデータ センターの場所を選択します。
10. **[仮想マシンの作成]** をクリックします。仮想マシンの作成が開始されます。その状態は、管理ポータルの **[仮想マシン]** セクションで監視できます。状態が **[有効]** と表示されたら、仮想マシンにログインできます。

<h2>仮想マシンにリモート ログインするには</h2>

1. [管理ポータル](https://manage.windowsazure.com)にログオンします。
2. **[仮想マシン]** をクリックします。
3. ログインする仮想マシンの名前をクリックします。
4. **[接続]** をクリックします。
5. 表示される画面で必要に応じて入力して、仮想マシンに接続します。管理者名とパスワードの入力画面が表示されたら、仮想マシンの作成時に指定した値を使用します。

<h2>サービス バス 名前空間の作成方法</h2>

Azure のサービス バス キューを使用するには、最初にサービス名前空間を作成する必要があります。サービス名前空間は、アプリケーション内でサービス バス リソースをアドレス指定するためのスコープ コンテナーを提供します。

サービス名前空間を作成するには:

1.  [Azure 管理ポータル](https://manage.windowsazure.com)にログインします。
2.  管理ポータルの左のナビゲーション ウィンドウで、**[サービス バス]** をクリックします。
3.  管理ポータルの下のウィンドウの **[作成]** をクリックします。

    ![Create new service bus][create_service_bus]
4.  **[名前空間を作成する]** ダイアログで、名前空間の名前を入力します。その名前が使用できるかどうかがすぐに自動で確認されます。重複する名前は使用できません。

    ![Create a namespace dialog][create_namespace_dialog]
5.  入力した名前が利用できることを確認できたら、名前空間をホストするリージョンを選択します (仮想マシンをホストするリージョンと同じリージョンを必ず使用してください)。

    > [AZURE.IMPORTANT]仮想マシンが使用するリージョンまたは使用する予定のリージョンと**同じリージョン**を選択してください。そうすることで、パフォーマンスが最高になります。

6. ログオンしたアカウントに複数の Azure サブスクリプションがある場合は、名前空間で使用するサブスクリプションを選択します(ログオンしたアカウントにサブスクリプションが 1 つしかない場合、サブスクリプションのドロップダウン リストは表示されません)。
7. チェック マークをクリックします。これで、システムによってサービス名前空間が作成され、有効になります。システムがアカウントのリソースを準備し終わるまでに、数分間かかる場合があります。

	![Click create screenshot][click_create]

作成した名前空間が管理ポータルに表示され、アクティブになります。これには少し時間がかかります。状態が **[有効]** になるのを待ってから、次の手順に進みます。

<h2>名前空間の既定の管理資格情報の取得</h2>

新規作成した名前空間に対してキューの作成などの管理操作を実行するには、名前空間の管理資格情報を取得する必要があります。

1.  左のナビゲーション ウィンドウで、**[Service Bus]** ノードをクリックして、
    使用できる名前空間の一覧を表示します。   
    ![Available namespaces screenshot][available_namespaces]
2.  表示された一覧から先ほど作成した名前空間を選択します。   
    ![Namespace list screenshot][namespace_list]
3. **[アクセス キー]** をクリックします。   
    ![Access key button][access_key_button]
4.  ダイアログで、**[既定の発行者]** と **[既定のキー]** のエントリを探します。その値を書き留めておきます。この情報は、この後に名前空間に対して操作を実行するときに使用します。 

<h2>多くのコンピューティング処理を要するタスクを実行する .NET アプリケーションの作成方法</h2>

1. 開発用コンピューターで (これは作成した仮想マシンと同じでなくてもかまいません)、[Azure SDK for .NET](http://www.windowsazure.com/ja-jp/develop/net/) をダウンロードします。
2. **TSPSolver** というプロジェクト名で .NET コンソール アプリケーションを作成します。ターゲット フレームワークは **[.NET Framework 4]** 以降に設定します (**[.NET Framework 4 Client Profile]** ではありません)。ターゲット フレームワークはプロジェクトを作成した後に変更することもできます。その場合は、Visual Studio で **[プロジェクト]** メニューの **[プロパティ]** をクリックし、**[アプリケーション]** タブをクリックして、**[ターゲット フレームワーク]** の値を設定します。
3. Microsoft ServiceBus ライブラリを追加します。Visual Studio のソリューション エクスプローラーで、**[TSPSolver]** を右クリックし、**[参照の追加]**、**[参照]** タブの順にクリックします。Azure .NET SDK (**C:\Program Files\Microsoft SDKs\Azure\.NET SDK\v2.5\ToolsRef** など) がある場所に移動し、**[Microsoft.ServiceBus.dll]** を参照として選択します。
4. System Runtime Serialization ライブラリを追加します。Visual Studio のソリューション エクスプローラーで、**[TSPSolver]** を右クリックし、**[参照の追加]** をクリックします。**[.NET]** タブをクリックし、**[System.Runtime.Serialization]** を参照として選択します。
5. このセクションの末尾にあるコード例を **Program.cs** の内容として使用します。
6. **your\_service\_bus\_namespace**、**your\_service\_bus\_owner**、**your\_service\_bus\_key** の各プレースホルダーを変更して、それぞれサービス バスの**名前空間**、**既定の発行者**、**既定のキー**の値を設定します。
7. アプリケーションをコンパイルします。これにより、プロジェクトの **bin** フォルダー (リリース ビルドかデバッグ ビルドかに応じて **bin\release** または **bin\debug**) に **TSPSolver.exe** が作成されます。この実行可能ファイルと Microsoft.ServiceBus.dll を後で仮想マシンにコピーします。

<p/>

	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Text;
	using System.IO;
	
	using Microsoft.ServiceBus;
	using Microsoft.ServiceBus.Messaging;
	
	namespace TSPSolver
	{
	    class Program
	    {
	        // Value specifying how often to provide an update to the console.
	        private static long loopCheck = 100000000;
	        private static long nTimes = 0, nLoops = 0;
	
	        private static double[,] distances;
	        private static String[] cityNames;
	        private static int[] bestOrder;
	        private static double minDistance;
	
	        private static NamespaceManager namespaceManager;
	        private static QueueClient queueClient;
	        private static String queueName = "TSPQueue";
	
	        private static void BuildDistances(String fileLocation, int numCities)
	        {
	
	            try
	            {
	                StreamReader sr = new StreamReader(fileLocation);
	                String[] sep1 = { ", " };
	
	                double[,] cityLocs = new double[numCities, 2];
	
	                for (int i = 0; i < numCities; i++)
	                {
	                    String[] line = sr.ReadLine().Split(sep1, StringSplitOptions.None);
	                    cityNames[i] = line[0];
	                    cityLocs[i, 0] = Convert.ToDouble(line[1]);
	                    cityLocs[i, 1] = Convert.ToDouble(line[2]);
	                }
	                sr.Close();
	
	                for (int i = 0; i < numCities; i++)
	                {
	                    for (int j = i; j < numCities; j++)
	                    {
	                        distances[i, j] = hypot(Math.Abs(cityLocs[i, 0] - cityLocs[j, 0]), Math.Abs(cityLocs[i, 1] - cityLocs[j, 1]));
	                        distances[j, i] = distances[i, j];
	                    }
	                }
	            }
	            catch (Exception e)
	            {
	                throw e;
	            }
	        }
	
	        private static double hypot(double x, double y)
	        {
	            return Math.Sqrt(x * x + y * y);
	        }
	
	        private static void permutation(List<int> startCities, double distSoFar, List<int> restCities)
	        {
	            try
	            {
	
	                nTimes++;
	                if (nTimes == loopCheck)
	                {
	                    nLoops++;
	                    nTimes = 0;
	                    DateTime dateTime = DateTime.Now;
	                    Console.Write("Current time is {0}.", dateTime);
	                    Console.WriteLine(" Completed {0} iterations of size of {1}.", nLoops, loopCheck);
	                }
	
	                if ((restCities.Count == 1) && ((minDistance == -1) || (distSoFar + distances[restCities[0], startCities[0]] + distances[restCities[0], startCities[startCities.Count - 1]] < minDistance)))
	                {
	                    startCities.Add(restCities[0]);
	                    newBestDistance(startCities, distSoFar + distances[restCities[0], startCities[0]] + distances[restCities[0], startCities[startCities.Count - 2]]);
	                    startCities.Remove(startCities[startCities.Count - 1]);
	                }
	                else
	                {
	                    for (int i = 0; i < restCities.Count; i++)
	                    {
	                        startCities.Add(restCities[0]);
	                        restCities.Remove(restCities[0]);
	                        permutation(startCities, distSoFar + distances[startCities[startCities.Count - 1], startCities[startCities.Count - 2]], restCities);
	                        restCities.Add(startCities[startCities.Count - 1]);
	                        startCities.Remove(startCities[startCities.Count - 1]);
	                    }
	                }
	            }
	            catch (Exception e)
	            {
	                throw e;
	            }
	        }
	
	        private static void newBestDistance(List<int> cities, double distance)
	        {
	            try
	            {
	                minDistance = distance;
	                String cityList = "Shortest distance is " + minDistance + ", with route: ";
	
	                for (int i = 0; i < bestOrder.Length; i++)
	                {
	                    bestOrder[i] = cities[i];
	                    cityList += cityNames[bestOrder[i]];
	                    if (i != bestOrder.Length - 1)
	                        cityList += ", ";
	                }
	                Console.WriteLine(cityList);
	                queueClient.Send(new BrokeredMessage(cityList));
	            }
	            catch (Exception e)
	            {
	                throw e;
	            }
	        }
	
	        static void Main(string[] args)
	        {
	            try
	            {
	
	                String serviceBusNamespace = "your_service_bus_namespace";
	                String issuer = "your_service_bus_owner";
	                String key = "your_service_bus_key";
	
	                String connectionString = @"Endpoint=sb://" +
	                       serviceBusNamespace +
	                       @".servicebus.windows.net/;SharedSecretIssuer=" +
	                       issuer + @";SharedSecretValue=" + key;
	
	                int numCities = 10; // Use as the default, if no value is specified
	                // at the command line.
	                if (args.Count() != 0)
	                {
	
	                    if (args[0].ToLower().CompareTo("createqueue") == 0)
	                    {
	                        // No processing to occur other than creating the queue.
	                        namespaceManager = NamespaceManager.CreateFromConnectionString(connectionString);
	                        namespaceManager.CreateQueue(queueName);
	                        Console.WriteLine("Queue named {0} was created.", queueName);
	                        Environment.Exit(0);
	                    }
	
	                    if (args[0].ToLower().CompareTo("deletequeue") == 0)
	                    {
	                        // No processing to occur other than deleting the queue.
	                        namespaceManager = NamespaceManager.CreateFromConnectionString(connectionString);
	                        namespaceManager.DeleteQueue("TSPQueue");
	                        Console.WriteLine("Queue named {0} was deleted.", queueName);
	                        Environment.Exit(0);
	                    }
	
	                    // Neither creating or deleting a queue.
	                    // Assume the value passed in is the number of cities to solve.
	                    numCities = Convert.ToInt32(args[0]);
	                }
	
	                Console.WriteLine("Running for {0} cities.", numCities);
	
	                queueClient = QueueClient.CreateFromConnectionString(connectionString, "TSPQueue");
	
	                List<int> startCities = new List<int>();
	                List<int> restCities = new List<int>();
	
	                startCities.Add(0);
	                for (int i = 1; i < numCities; i++)
	                {
	                    restCities.Add(i);
	                }
	                distances = new double[numCities, numCities];
	                cityNames = new String[numCities];
	                BuildDistances(@"c:\tsp\cities.txt", numCities);
	                minDistance = -1;
	                bestOrder = new int[numCities];
	                permutation(startCities, 0, restCities);
	                Console.WriteLine("Final solution found!");
	                queueClient.Send(new BrokeredMessage("Complete"));
	
	                queueClient.Close();
	                Environment.Exit(0);

	            }
	            catch (ServerBusyException serverBusyException)
	            {
	                Console.WriteLine("ServerBusyException encountered");
	                Console.WriteLine(serverBusyException.Message);
	                Console.WriteLine(serverBusyException.StackTrace);
	                Environment.Exit(-1);
	            }
	            catch (ServerErrorException serverErrorException)
	            {
	                Console.WriteLine("ServerErrorException encountered");
	                Console.WriteLine(serverErrorException.Message);
	                Console.WriteLine(serverErrorException.StackTrace);
	                Environment.Exit(-1);
	            }
	            catch (Exception exception)
	            {
	                Console.WriteLine("Exception encountered");
	                Console.WriteLine(exception.Message);
	                Console.WriteLine(exception.StackTrace);
	                Environment.Exit(-1);
	            }
	        }
	    }
	}



<h2>多くのコンピューティング処理を要するタスクの進捗状況を監視する .NET アプリケーションの作成方法</h2>

1. 開発用コンピューターで、**TSPClient** というプロジェクト名で .NET コンソール アプリケーションを作成します。ターゲット フレームワークは **[.NET Framework 4]** 以降に設定します (**[.NET Framework 4 Client Profile]** ではありません)。ターゲット フレームワークはプロジェクトを作成した後に変更することもできます。その場合は、Visual Studio で **[プロジェクト]** メニューの **[プロパティ]** をクリックし、**[アプリケーション]** タブをクリックして、**[ターゲット フレームワーク]** の値を設定します。
2. Microsoft ServiceBus ライブラリを追加します。Visual Studio のソリューション エクスプローラーで、**[TSPSolver]** を右クリックし、**[参照の追加]**、**[参照]** タブの順にクリックします。Azure .NET SDK (**C:\Program Files\Microsoft SDKs\Azure\.NET SDK\v2.5\ToolsRef** など) がある場所に移動し、**[Microsoft.ServiceBus.dll]** を参照として選択します。
3. System Runtime Serialization ライブラリを追加します。Visual Studio のソリューション エクスプローラーで、**[TSPClient]** を右クリックし、**[参照の追加]** をクリックします。**[.NET]** タブをクリックし、**[System.Runtime.Serialization]** を参照として選択します。
4. このセクションの末尾にあるコード例を **Program.cs** の内容として使用します。
5. **your\_service\_bus\_namespace**、**your\_service\_bus\_owner**、**your\_service\_bus\_key** の各プレースホルダーを変更して、それぞれサービス バスの**名前空間**、**既定の発行者**、**既定のキー**の値を設定します。
5. アプリケーションをコンパイルします。これにより、プロジェクトの **bin** フォルダー (リリース ビルドかデバッグ ビルドかに応じて **bin\release** または **bin\debug**) に **TSPClient.exe** が作成されます。このコードを開発用コンピューターから実行することも、別のコンピューターに実行可能ファイルと Microsoft.ServiceBus.dll をコピーして、そこでクライアント アプリケーションを実行することもできます (アプリケーションを仮想マシン上に置く必要はありません)。

<p/>

	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Text;
	using System.IO;
	
	using Microsoft.ServiceBus;
	using Microsoft.ServiceBus.Messaging;
	using System.Threading; // For Thread.Sleep
	
	namespace TSPClient
	{
	    class Program
	    {
	
	        static void Main(string[] args)
	        {
	
	            try
	            {
	
	                Console.WriteLine("Starting at {0}", DateTime.Now);
	
	                String serviceBusNamespace = "your_service_bus_namespace";
	                String issuer = "your_service_bus_owner";
	                String key = "your_service_bus_key";
	
	                String connectionString = @"Endpoint=sb://" +
	                       serviceBusNamespace +
	                       @".servicebus.windows.net/;SharedSecretIssuer=" +
	                       issuer + @";SharedSecretValue=" + key;
	
	                QueueClient queueClient = QueueClient.CreateFromConnectionString(connectionString, "TSPQueue");
	
	                BrokeredMessage message;
	
	                int waitMinutes = 3;  // Use as the default, if no value
	                // is specified at command line.
	
	                if (0 != args.Length)
	                {
	                    waitMinutes = Convert.ToInt16(args[0]);
	                }
	
	                String waitString;
	                waitString = (waitMinutes == 1) ? "minute" : waitMinutes.ToString() + " minutes";
	
	                while (true)
	                {
	                    message = queueClient.Receive();
	
	                    if (message != null)
	                    {
	                        try
	                        {
	                            string str = message.GetBody<string>();
	                            Console.WriteLine(str);
	
	                            // Remove message from queue
	                            message.Complete();
	
	                            if ("Complete" == str)
	                            {
	                                Console.WriteLine("Finished at {0}.", DateTime.Now);
	                                break;
	                            }
	                        }
	                        catch (Exception e)
	                        {
	                            // Indicates a problem. Unlock the message in the queue.
	                            message.Abandon();
	                            throw e;
	                        }
	                    }
	                    else
	                    {
	                        // The queue is empty.
	                        Console.WriteLine("Queue is empty. Sleeping for another {0}.", waitString);
	                        System.Threading.Thread.Sleep(60000 * waitMinutes);
	                    }
	                }
	                queueClient.Close();
	                Environment.Exit(0);
	            }
	            catch (ServerBusyException serverBusyException)
	            {
	                Console.WriteLine("ServerBusyException encountered");
	                Console.WriteLine(serverBusyException.Message);
	                Console.WriteLine(serverBusyException.StackTrace);
	                Environment.Exit(-1);
	            }
	            catch (ServerErrorException serverErrorException)
	            {
	                Console.WriteLine("ServerErrorException encountered");
	                Console.WriteLine(serverErrorException.Message);
	                Console.WriteLine(serverErrorException.StackTrace);
	                Environment.Exit(-1);
	            }
	            catch (Exception exception)
	            {
	                Console.WriteLine("Exception encountered");
	                Console.WriteLine(exception.Message);
	                Console.WriteLine(exception.StackTrace);
	                Environment.Exit(-1);
	            }
	        }
	    }
	}

<h2>.NET アプリケーションの実行方法</h2>
多くのコンピューティング処理を要するアプリケーションを、最初はキューを作成するために実行し、次は巡回セールスマン問題を解くために実行します。これにより、サービス バス キューに現在の最適な経路が追加されます。多くのコンピューティング処理を要するアプリケーションの実行中に (または実行後に)、クライアントを実行してサービス バス キューから結果を表示します。

<h3>多くのコンピューティング処理を要するアプリケーションの実行方法</h3>

1. 仮想マシンにログオンします。
2. **c:\TSP** という名前のフォルダーを作成します。これがアプリケーションを実行する場所です。
3. TSPSolver プロジェクトの **bin** フォルダーにある TSPSolver.exe と Microsoft.ServiceBus.dll を **c:\TSP** にコピーします。
4. **c:\TSP\cities.txt** という名前のファイルを作成し、内容を次のようにします。

		City_1, 1002.81, -1841.35
		City_2, -953.55, -229.6
		City_3, -1363.11, -1027.72
		City_4, -1884.47, -1616.16
		City_5, 1603.08, -1030.03
		City_6, -1555.58, 218.58
		City_7, 578.8, -12.87
		City_8, 1350.76, 77.79
		City_9, 293.36, -1820.01
		City_10, 1883.14, 1637.28
		City_11, -1271.41, -1670.5
		City_12, 1475.99, 225.35
		City_13, 1250.78, 379.98
		City_14, 1305.77, 569.75
		City_15, 230.77, 231.58
		City_16, -822.63, -544.68
		City_17, -817.54, -81.92
		City_18, 303.99, -1823.43
		City_19, 239.95, 1007.91
		City_20, -1302.92, 150.39
		City_21, -116.11, 1933.01
		City_22, 382.64, 835.09
		City_23, -580.28, 1040.04
		City_24, 205.55, -264.23
		City_25, -238.81, -576.48
		City_26, -1722.9, -909.65
		City_27, 445.22, 1427.28
		City_28, 513.17, 1828.72
		City_29, 1750.68, -1668.1
		City_30, 1705.09, -309.35
		City_31, -167.34, 1003.76
		City_32, -1162.85, -1674.33
		City_33, 1490.32, 821.04
		City_34, 1208.32, 1523.3
		City_35, 18.04, 1857.11
		City_36, 1852.46, 1647.75
		City_37, -167.44, -336.39
		City_38, 115.4, 0.2
		City_39, -66.96, 917.73
		City_40, 915.96, 474.1
		City_41, 140.03, 725.22
		City_42, -1582.68, 1608.88
		City_43, -567.51, 1253.83
		City_44, 1956.36, 830.92
		City_45, -233.38, 909.93
		City_46, -1750.45, 1940.76
		City_47, 405.81, 421.84
		City_48, 363.68, 768.21
		City_49, -120.3, -463.13
		City_50, 588.51, 679.33
	
5. コマンド プロンプトで、ディレクトリを c:\TSP に変更します。
6. 巡回セールスマン問題を解くプログラムを実行する前に、サービス バス キューを作成する必要があります。次のコマンドを実行して、サービス バス キューを作成します。

        TSPSolver createqueue

7. これでキューが作成されたので、巡回セールスマン問題を解くプログラムを実行できます。たとえば、次のコマンドを実行すると、8 都市を対象としてプログラムが実行されます。 

        TSPSolver 8

 数値を指定しなかった場合、プログラムは 10 都市を対象として実行されます。現時点で最短の経路が見つかると、それがキューに追加されます。

プログラムはすべての経路の調査が完了すると終了します。

> [AZURE.NOTE]
> 指定した数値が大きいほど、プログラムの実行時間は長くなります。たとえば、14 都市の場合は数分で実行できても、15 都市になると実行に数時間かかることがありえます。16 都市以上にすると実行時間が数日になる可能性があります (最終的には数週間、数か月、数年かかります)。これは都市数が増えるにつれてプログラムが評価する順列の数が急増するためです。
 
<h3>監視用のクライアント アプリケーションの実行方法</h3>
1. クライアント アプリケーションを実行するコンピューターにログオンします。これは、**TSPSolver** アプリケーションを実行するコンピューターと同じでなくてもかまいません。
2. アプリケーションを実行するフォルダーを作成します。たとえば、**c:\TSP** のように指定します。
3. TSPClient プロジェクトの **bin** フォルダーにある **TSPClient.exe** と Microsoft.ServiceBus.dll を c:\TSP フォルダーにコピーします。
4. コマンド プロンプトで、ディレクトリを c:\TSP に変更します。
5. 次のコマンドを実行します。

        TSPClient

    必要に応じて、コマンド ライン引数でキューのチェック間隔を分単位で指定します。キューのチェック間隔の既定値は 3 分です。**TSPClient** にコマンド ライン引数が指定されなかった場合、この値が使用されます。チェック間隔として別の値、たとえば 1 分を使用する場合は、次のように実行します。

	    TSPClient 1

    クライアントは、"Complete" というキュー メッセージが見つかるまで実行を続けます。注意点として、クライアントを実行しないで、問題を解くプログラムを複数実行した場合、キューを完全に空にするにはクライアントの複数回実行が必要になる場合があります。別の方法として、キューを削除して再び作成することもできます。キューを削除するには、次のように **TSPSolver** コマンドを実行します (**TSPClient** ではありません)。

        TSPSolver deletequeue

<h2>.NET アプリケーションの停止方法</h2>

問題を解くアプリケーションとクライアント アプリケーションのどちらでも、**Ctrl + C** キーを押すと、通常の処理が完了する前にアプリケーションが終了します。

<h2>TSPSolver を使用しないでキューを作成し削除する方法</h2>
TSPSolver を使用してキューを作成したり削除したりする代わりに、[Azure 管理ポータル](https://manage.windowsazure.com)を使用してキューの作成と削除を行うこともできます。管理ポータルの  サービス バス セクションには、キューを作成し削除する機能のほかに、接続文字列や発行者、アクセス キーを取得する機能も用意されています。また、サービス バス キューのダッシュボードを表示して、発着信メッセージのメトリックを表示することもできます。 

[solver_output]: ./media/virtual-machines-dotnet-run-compute-intensive-task/WA_dotNetTSPSolver.png
[client_output]: ./media/virtual-machines-dotnet-run-compute-intensive-task/WA_dotNetTSPClient.png
[create_service_bus]: ./media/virtual-machines-dotnet-run-compute-intensive-task/ServiceBusCreateNew.png
[create_namespace_dialog]: ./media/virtual-machines-dotnet-run-compute-intensive-task/CreateNameSpaceDialog.png
[available_namespaces]: ./media/virtual-machines-dotnet-run-compute-intensive-task/AvailableNamespaces.png
[click_create]: ./media/virtual-machines-dotnet-run-compute-intensive-task/ClickCreate.png
[namespace_list]: ./media/virtual-machines-dotnet-run-compute-intensive-task/NamespaceList.png
[access_key_button]: ./media/virtual-machines-dotnet-run-compute-intensive-task/AccessKey.png


<!--HONumber=42-->
