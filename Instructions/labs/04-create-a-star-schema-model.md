---
lab:
  title: 创建星型架构模型
  module: Prepare data for tabular models in Power BI
---

# <a name="create-a-star-schema-model"></a>创建星型架构模型

## <a name="overview"></a>概述

完成本实验室预计需要 30 分钟

In this lab, you will use Power BI Desktop to develop a data model over the Azure Synapse Adventure Works data warehouse. The data model will allow you to publish a semantic layer over the data warehouse.

在此实验室中，你将了解如何完成以下操作：

- 创建与 Azure Synapse Analytics SQL 池的 Power BI 连接。

- 开发模型查询。

- 组织模型图。

## <a name="get-started"></a>入门

在本练习中，你将准备环境。

### <a name="load-data-into-azure-synapse-analytics"></a>将数据加载到 Azure Synapse Analytics

   > 注意：如果已使用 git 克隆将数据加载到 Azure Synapse Analytics 中，则可以跳过此任务并继续设置 Power BI 。

1. 使用 VM 右侧“资源”选项卡上的登录信息登录到 [Azure 门户](https://portal.azure.com)。
2. Use the <bpt id="p1">**</bpt>[<ph id="ph1">\&gt;</ph>_]<ept id="p1">**</ept> button to the right of the search bar at the top of the page to create a new Cloud Shell in the Azure portal, selecting a <bpt id="p2">***</bpt>PowerShell<ept id="p2">***</ept> environment and creating storage if prompted. The cloud shell provides a command line interface in a pane at the bottom of the Azure portal, as shown here:

    ![具有 Cloud Shell 窗格的 Azure 门户](../images/cloud-shell.png)

    > 注意：如果以前创建了使用 Bash 环境的 Cloud Shell，请使用 Cloud Shell 窗格左上角的下拉菜单将其更改为 PowerShell。

3. Note that you can resize the cloud shell by dragging the separator bar at the top of the pane, or by using the <bpt id="p1">**</bpt>&amp;#8212;<ept id="p1">**</ept>, <bpt id="p2">**</bpt>&amp;#9723;<ept id="p2">**</ept>, and <bpt id="p3">**</bpt>X<ept id="p3">**</ept> icons at the top right of the pane to minimize, maximize, and close the pane. For more information about using the Azure Cloud Shell, see the <bpt id="p1">[</bpt>Azure Cloud Shell documentation<ept id="p1">](https://docs.microsoft.com/azure/cloud-shell/overview)</ept>.

4. 在 PowerShell 窗格中，输入以下命令以克隆此存储库：

    ```
    rm -r dp500 -f
    git clone https://github.com/MicrosoftLearning/DP-500-Azure-Data-Analyst dp500
    ```

5. 克隆存储库后，输入以下命令以更改为安装程序文件夹，然后运行其中包含的 setup.ps1 脚本 ：

    ```
    cd dp500/Allfiles/04
    ./setup.ps1
    ```

6. 出现提示时，输入要为 Azure Synapse SQL 池设置的合适密码。

    > 注意：请务必记住此密码！

7. 等待脚本完成 - 这通常需要大约 20 分钟；但在某些情况下可能需要更长的时间。
8. After creating the Synapse workspace and SQL Pool and loading the data, the script pauses the pool to prevent unnecessary Azure charges. When you're ready to work with your data in Azure Synapse Analytics, you'll need to resume the SQL Pool.

### <a name="clone-the-repository-for-this-course"></a>克隆本课程的存储库

1. 在“开始”菜单上，打开命令提示符

    ![](../images/command-prompt.png)
1. 在命令提示符窗口中，键入以下内容导航到 D 驱动器：

    `d:` 

   按 Enter。

    ![](../images/command-prompt-2.png)


1. 在命令提示符窗口中，输入以下命令以下载课程文件并将其保存到名为 DP500 的文件夹中。
    
    `git clone https://github.com/MicrosoftLearning/DP-500-Azure-Data-Analyst DP500`
   

1. 克隆存储库后，在文件资源管理器中打开 D 驱动器，以确保文件已下载。

### <a name="set-up-power-bi"></a>设置 Power BI

在此任务中，你将设置 Power BI。

1. 若要打开 Power BI Desktop，请在任务栏上选择 Power BI Desktop 快捷方式。

    ![](../images/dp500-create-a-star-schema-model-image1.png)

2. 选择位于开始窗口右上角的“X”。

    ![](../images/dp500-create-a-star-schema-model-image2.png)

3. At the top-right corner of Power BI Desktop, if you're not already signed in, select <bpt id="p1">**</bpt>Sign In<ept id="p1">**</ept>. Use the lab credentials to complete the sign in process.

    ![](../images/dp500-create-a-star-schema-model-image3.png)
4. 在此实验室中，你将使用 Power BI Desktop 基于 Azure Synapse Adventure Works 数据仓库开发数据模型。

    ![](../images/dp500-create-a-star-schema-model-image3b.png)

5. 该数据模型允许在数据仓库上发布语义层。

1. 在右上角选择“个人资料”图标，然后选择“开始试用”。

    ![](../images/dp500-create-a-dataflow-image3.png)

1. 出现提示时，选择“开始试用”。

    ![](../images/dp500-create-a-dataflow-image4.png)

1. 执行所有剩余任务以完成试用设置。

    提示：Power BI Web 浏览器体验称为 Power BI 服务**。
    
1. 选择“工作区”和“创建工作区”。
    
    ![](../images/dp500-create-a-star-schema-model-image2a.png)

1. 创建名为 DP500 labs 的工作区，然后选择“保存”。

    注意：工作区名称必须是唯一的。如果收到错误，请更新工作区名称。

    ![](../images/dp500-create-a-star-schema-model-image2b.png)

1. Navigate back to Power BI Desktop. If you see <bpt id="p1">**</bpt>Sign in<ept id="p1">**</ept> in the top right corner of the screen, sign-in again using the credentials provided on the Resources tab of the lab environment. If you are already signed in, proceed to the next step.

    <img width="80" alt="image" src="https://user-images.githubusercontent.com/77289548/166337862-538a1900-ec67-44d1-905f-d404c5b0a58a.png">

1. Go to Power BI Desktop and select <bpt id="p1">**</bpt>File<ept id="p1">**</ept> then <bpt id="p2">**</bpt>Options and settings<ept id="p2">**</ept> then <bpt id="p3">**</bpt>Options<ept id="p3">**</ept> then <bpt id="p4">**</bpt>Security<ept id="p4">**</ept> and under Authentication Browser check <bpt id="p5">**</bpt>Use my default web browser<ept id="p5">**</ept> and select <bpt id="p6">**</bpt>OK<ept id="p6">**</ept>. Close Power BI Desktop. Do not save your file.

    在下一个练习中，你将再次打开 Power BI Desktop。

### <a name="start-the-sql-pool"></a>启动 SQL 池

在此任务中，你将启动 SQL 池。

1. 在 Microsoft Edge 中，转到 [https://portal.azure.com](https://portal.azure.com/)。

1. 使用实验室凭据完成登录过程。

1. Select <bpt id="p1">**</bpt>Azure Synapse Analytics<ept id="p1">**</ept> from Azure services. Select your Synapse workspace.

   ![](../images/dp500-create-a-star-schema-model-image3c.png)

1. 找到并选择专用 SQL 池。

   ![](../images/dp500-create-a-star-schema-model-image3d.png)

1. 恢复 SQL 池。

    ![](../images/dp500-create-a-star-schema-model-image3e.png)

    重要说明：SQL 池是一种成本高昂的资源。请在处理此实验室时限制此资源的使用。此实验室中的最终任务会指示你暂停使用资源。

### <a name="link-your-power-bi-workspace-to-azure-synapse-analytics"></a>将 Power BI 工作区链接到 Azure Synapse Analytics

在此任务中，你将把现有 Power BI 工作区链接到 Azure Synapse Analytics 工作区。
1. 在 Azure 门户的专用 SQL 池中，从功能区中选择“在 Synapse Studio 中打开”。

1. 在 Azure Synapse Studio 主页上，选择“可视化”以链接 Power BI 工作区。

    ![](../images/dp500-create-a-star-schema-model-image3f.png)

1. 从“工作区名称”下拉列表中，选择在上一个任务中创建的工作区，然后选择“创建” 。

    ![](../images/dp500-create-a-star-schema-model-image3g.png)

    ![](../images/dp500-create-a-star-schema-model-image3h.png)

1. 出现提示时，选择“发布”。

## <a name="develop-a-data-model"></a>开发数据模型

在本练习中，你将开发一个 DirectQuery 模型，以支持数据仓库分销商销售主题的 Power BI 分析和报告。

### <a name="download-a-dataset-file"></a>下载数据集文件

在此任务中，你将从 Synapse Studio 中下载 Power BI 数据源文件。

1. 在 Microsoft Edge 中，导航到 Synapse Studio。

    ![](../images/dp500-create-a-star-schema-model-image4a.png)

2. 在左侧，选择“开发”中心。

    ![](../images/dp500-create-a-star-schema-model-image4.png)

3. In the <bpt id="p1">**</bpt>Develop<ept id="p1">**</ept> pane, expand <bpt id="p2">**</bpt>Power BI<ept id="p2">**</ept>, then expand the workspace, and then select <bpt id="p3">**</bpt>Power BI datasets<ept id="p3">**</ept>. If not present, Click <bpt id="p1">**</bpt>Publish all<ept id="p1">**</ept> to publish Workspace and refresh the browser.

    ![](../images/dp500-create-a-star-schema-model-image5.png)

4. 在“Power BI 数据集”窗格中，选择“新建 Power BI 数据集” 。

    ![](../images/dp500-create-a-star-schema-model-image6.png)

5. 在左窗格底部，选择“开始”。

    ![](../images/dp500-create-a-star-schema-model-image7.png)

6. 选择 SQL 池 sqldw，然后选择“继续” 。

    ![](../images/dp500-create-a-star-schema-model-image8.png)

7. 选择“下载”以下载 .pbids 文件。

    ![](../images/dp500-create-a-star-schema-model-image9.png)

    .pbids 文件包含与 SQL 池的连接。这是启动项目的一种便捷方法。打开后，它将创建一个新的 Power BI Desktop 解决方法，该解决方法已将连接详细信息存储到 SQL 池。

8. .pbids 文件下载完成后，请将其打开。

    打开文件时，系统会提示你使用连接来创建查询。你将在下一个任务中定义这些查询。

### <a name="create-model-queries"></a>创建模型查询

在此任务中，你将创建五个 Power Query 查询，每个查询将作为表加载到模型。

1. 在 Power BI Desktop 的“SQL Server数据库”窗口中，在左侧选择“Microsoft 帐户” 。

    ![](../images/dp500-create-a-star-schema-model-image10.png)

2. 选择“登录”。

3. 使用提供的实验室 Azure 凭据登录。

4. 选择“连接”。

    ![](../images/dp500-create-a-star-schema-model-image11.png)

5. 在“导航器”窗口中，选择（不选中）DimDate 表 。

6. 请注意右窗格中的预览结果，其中显示了表行的子集。

    ![](../images/dp500-create-a-star-schema-model-image12.png)

7. 要创建（将成为模型表的）查询，请选中以下五个表：

    - DimDate

    - DimProduct

    - DimReseller

    - DimSalesTerritory

    - FactResellerSales

8. 若要将转换应用于查询，请在右下角选择“转换数据”。

    ![](../images/dp500-create-a-star-schema-model-image13.png)

    通过转换数据，可以定义模型中可用的数据。


9. 在“连接设置”窗口中，选择 DirectQuery 选项 。

    ![](../images/dp500-create-a-star-schema-model-image14.png)

    这个决定很重要。DirectQuery 是一种存储模式。使用 DirectQuery 存储模式的模型表不存储数据。因此，当 Power BI 报表视觉对象查询 DirectQuery 表时，Power BI 会向数据源发送原生查询。此存储模式可用于大型数据存储，如 Azure Synapse Analytics（因为导入大量数据可能不切实际或不经济），或用于需要近实时结果的情况。

10. 选择“确定”。

    ![](../images/dp500-create-a-star-schema-model-image15.png)


11. 在“Power Query 编辑器”窗口的“查询”窗格（位于左侧）中，请注意，选中的每个表都有一个查询 。

    ![](../images/dp500-create-a-star-schema-model-image16.png)

    现在，你将修改每个查询的定义。每个查询在应用于模型时都将成为模型表。现在你将重命名查询，以便以更友好、更简洁的方式描述它们，并应用转换来传递已知报告要求所需的列。

12. 选择 DimDate 查询。

    ![](../images/dp500-create-a-star-schema-model-image17.png)

13. 在“查询设置”窗格（位于右侧）的“名称”框中，将文本替换为“Date”，然后按 Enter 键，以重命名查询   。

    ![](../images/dp500-create-a-star-schema-model-image18.png)


14. 若要删除不必要的列，请在“主页”功能区选项卡上的“管理列”组中，选择“选择列”图标  。

    ![](../images/dp500-create-a-star-schema-model-image19.png)

15. 在“选择列”窗口中，取消选中第一个复选框，以取消选中所有复选框。

    ![](../images/dp500-create-a-star-schema-model-image20.png)


16. 选中以下五列。

    - DateKey

    - FullDateAlternateKey

    - EnglishMonthName

    - FiscalQuarter

    - FiscalYear

    ![](../images/dp500-create-a-star-schema-model-image21.png)

    此列选择决定了模型中可用的列。

17. 选择“确定”。

    ![](../images/dp500-create-a-star-schema-model-image22.png)

18. 在“查询设置”窗格的“已应用步骤”列表中，注意添加了一个步骤来删除其他列 。

    ![](../images/dp500-create-a-star-schema-model-image23.png)

    Power Query 定义实现所需结构和数据的步骤。每个转换都是查询逻辑中的一个步骤。

19. 若要重命名 FullDateAlternateKey 列，请双击 FullDateAlternateKey 列标题 。

20. 将文本替换为 Date，然后按 Enter 键 。

    ![](../images/dp500-create-a-star-schema-model-image24.png)

21. 请注意，新应用的步骤将添加到查询中。

    ![](../images/dp500-create-a-star-schema-model-image25.png)

22. 为以下列重命名：

    - 将 EnglishMonthName 重命名为 Month 

    - 将 FiscalQuarter 重命名为 Quarter 

    - 将 FiscalYear 重命名为 Year 


23. 若要验证查询设计，请在状态栏（位于窗口底部）中，验证查询是否包含五列。

    ![](../images/dp500-create-a-star-schema-model-image26.png)

    重要说明：如果查询设计不匹配，请查看练习步骤以进行更正。

    现在，Date 查询设计就完成了**。

24. 在“已应用步骤”窗格中，右键单击最后一步，然后选择“查看原生查询” 。

    ![](../images/dp500-create-a-star-schema-model-image27.png)

25. 在“原生查询”窗口中，查看反映查询设计的 SELECT 语句。

    这个概念很重要。原生查询是 Power BI 用于查询数据源的内容。为确保最佳性能，数据库开发人员应通过创建适当的索引等方式来确保此查询得到优化。

26. 若要关闭“原生查询”窗口，请选择“确定” 。

    ![](../images/dp500-create-a-star-schema-model-image28.png)


27. 选择 DimProduct 查询。

    ![](../images/dp500-create-a-star-schema-model-image29.png)

28. 将查询重命名为 Product。

    ![](../images/dp500-create-a-star-schema-model-image30.png)

29. 若要筛选查询，请在 FinishedGoodsFlag 列标题中打开下拉菜单，取消选中 FALSE 。

    ![](../images/dp500-create-a-star-schema-model-image31.png)

30. 选择“确定”。


31. 删除所有列，以下列除外：

    - ProductKey

    - EnglishProductName

    - Color

    - DimProductSubcategory

32. 若要配置查询以联接表，请在 DimProductSubcategory 列标题中选择“展开”按钮，然后取消选中“(选择所有列)”  。

    此功能允许基于源数据中的外键约束联接表。本实验室采用的设计方法是将雪花维度表联接在一起，以生成数据的非规范化表示形式。

33. 取消选中“使用原始列名作为前缀”。

    ![](../images/dp500-create-a-star-schema-model-image32.png)

34. 选中以下两列：

    - EnglishProductSubcategoryName

    - DimProductCategory

35. 选择“确定”。

36. 重复上述步骤以展开 DimProductCategory 并引入 EnglishProductCategoryName 列 。


37. 为以下列重命名：

    - 将 EnglishProductName 重命名为 Product 

    - 将 EnglishProductSubcategoryName 重命名为 Subcategory 

    - 将 EnglishProductCategoryName 重命名为 Category 

38. 在“已应用步骤”窗格中，右键单击最后一步，然后选择“查看原生查询” 。

    ![](../images/dp500-create-a-star-schema-model-image33.png)

39. 在“原生查询”窗口中，查看反映查询设计的 SELECT 语句。

    该语句包含嵌套子查询，用于生成非规范化查询结果。

40. 若要关闭“原生查询”窗口，请选择“确定” 。

41. 验证查询是否包含五列。

    现在，Product 查询设计就完成了**。

42. 选择 DimReseller 查询。

    ![](../images/dp500-create-a-star-schema-model-image34.png)

43. 将查询重命名为 Reseller。

44. 删除所有列，以下列除外：

    - ResellerKey

    - BusinessType

    - ResellerName

45. 为以下列重命名：

    - 将 BusinessType 重命名为 Business Type（用空格分开） 

    - 将 ResellerName 重命名为 Reseller 

46. 验证查询是否包含三列。

    现在，Reseller 查询设计就完成了**。

47. 选择 DimSalesTerritory 查询。

    ![](../images/dp500-create-a-star-schema-model-image35.png)

48. 将查询重命名为 Territory。

49. 删除所有列，以下列除外：

    - SalesTerritoryKey

    - SalesTerritoryRegion

    - SalesTerritoryCountry

    - SalesTerritoryGroup

50. 为以下列重命名：

    - 将 SalesTerritoryRegion 重命名为 Region 

    - 将 SalesTerritoryCountry 重命名为 Country 

    - 将 SalesTerritoryGroup 重命名为 Group 

51. 验证查询是否包含四列。

    现在，Territory 查询设计就完成了**。

52. 选择 FactResellerSales 查询。

    ![](../images/dp500-create-a-star-schema-model-image36.png)

53. 将查询重命名为 Sales。

54. 删除所有列，以下列除外：

    - ResellerKey

    - ProductKey

    - OrderDateKey

    - SalesTerritoryKey

    - OrderQuantity

    - UnitPrice

    ![](../images/dp500-create-a-star-schema-model-image37.png)

55. 为以下列重命名：

    - 将 OrderQuantity 重命名为 Quantity 

    - 将 UnitPrice 重命名为 Price 

56. 若要添加计算列，请在“添加列”功能区选项卡上的“常规”组中，选择“自定义列”  。

    ![](../images/dp500-create-a-star-schema-model-image38.png)


57. 在“自定义列”窗口的“新列名”框中，将文本替换为 Revenue  。

    ![](../images/dp500-create-a-star-schema-model-image39.png)

58. 在“自定义列公式”框中，输入以下公式：

    ```
    [Quantity] * [Price]
    ```


59. 选择“确定”。

60. 若要修改列数据类型，请在“收入”列标题中选择 ABC123，然后选择“十进制数”  。

    ![](../images/dp500-create-a-star-schema-model-image40.png)

61. 查看原生查询，注意 Revenue 列计算逻辑。

62. 验证查询是否包含七列。

    现在，Sales 查询设计就完成了**。


63. 若要应用查询，请在“主页”功能区选项卡上，从“关闭”组中选择“关闭并应用”图标  。

    ![](../images/dp500-create-a-star-schema-model-image41.png)

    每个查询都用于创建模型表。由于数据连接使用的是 DirectQuery 存储模式，因此仅创建模型结构，不导入任何数据。该模型现在由每个查询的一个表组成。

64. 在 Power BI Desktop 中，应用查询后，请注意状态栏左下角的模型存储模式为 DirectQuery。

    ![](../images/dp500-create-a-star-schema-model-image42.png)

### <a name="organize-the-model-diagram"></a>组织模型关系图

在此任务中，你将组织模型图，以便轻松了解星型架构设计。

1. 在 Power BI Desktop 左侧，选择“模型”视图。

    ![](../images/dp500-create-a-star-schema-model-image43.png)

2. 要调整模型图的大小以适应屏幕，请在右下角选择“适应屏幕”图标。

    ![](../images/dp500-create-a-star-schema-model-image44.png)

3. 将表拖动到适当的位置，以便 Sales 事实数据表位于关系图的中间，而其余表（即维度表）位于事实数据表周围。

4. 如果任何维度表都与事实数据表无关，请使用以下说明创建关系：

    - 拖动维度键列（例如 ProductKey），并将其放在 Sales 表的相应列上 。

    - 在“创建关系”窗口中，选择“确定” 。


5. 查看模型关系图的最终布局。

    ![](../images/dp500-create-a-star-schema-model-image45.png)

    星型架构模型的创建现已完成。现在可以应用许多建模配置，例如添加层次结构、计算和设置列可见性等属性。

6. （可选）若要保存解决方法，请在左上角选择磁盘图标。

7. 在“另存为”窗口中，转到“D：\DP500\Allfiles\04\MySolution”文件夹 。

8. 在“文件名”框中，输入“Sales Analysis” 。

    ![](../images/dp500-create-a-star-schema-model-image46.png)

9. 选择“保存”。

10. 关闭 Power BI Desktop。

### <a name="pause-the-sql-pool"></a>暂停 SQL 池

在此任务中，你将停止 SQL 池。

1. 在 Web 浏览器中，转到 https://portal.azure.com。

2. 定位 SQL 池。

3. 暂停 SQL 池。

