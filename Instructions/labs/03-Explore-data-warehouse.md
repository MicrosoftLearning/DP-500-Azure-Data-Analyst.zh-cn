---
lab:
  title: 实验室 - 探索关系数据仓库
  module: 'Model, query, and explore data in Azure Synapse'
---

# <a name="explore-a-relational-data-warehouse"></a>实验室 - 探索关系数据仓库

Azure Synapse Analytics is built on a scalable set capabilities to support enterprise data warehousing; including file-based data analytics in a data lake as well as large-scale relational data warehouses and the data transfer and transformation pipelines used to load them. In this lab, you'll explore how to use a dedicated SQL pool in Azure Synapse Analytics to store and query data in a relational data warehouse.

完成本实验室大约需要 45 分钟。

## <a name="before-you-start"></a>准备工作

需要一个你在其中具有管理级权限的 [Azure 订阅](https://azure.microsoft.com/free)。

## <a name="provision-an-azure-synapse-analytics-workspace"></a>预配 Azure Synapse Analytics 工作区

An Azure Synapse Analytics <bpt id="p1">*</bpt>workspace<ept id="p1">*</ept> provides a central point for managing data and data processing runtimes. You can provision a workspace using the interactive interface in the Azure portal, or you can deploy a workspace and resources within it by using a script or template. In most production scenarios, it's best to automate provisioning with scripts and templates so that you can incorporate resource deployment into a repeatable development and operations (<bpt id="p1">*</bpt>DevOps<ept id="p1">*</ept>) process.

在本练习中，你将组合使用 PowerShell 脚本和 ARM 模板来预配 Azure Synapse Analytics 工作区。

1. 登录到 Azure 门户，地址为 [](https://portal.azure.com)。
2. Use the <bpt id="p1">**</bpt>[<ph id="ph1">\&gt;</ph>_]<ept id="p1">**</ept> button to the right of the search bar at the top of the page to create a new Cloud Shell in the Azure portal, selecting a <bpt id="p2">***</bpt>PowerShell<ept id="p2">***</ept> environment and creating storage if prompted. The cloud shell provides a command line interface in a pane at the bottom of the Azure portal, as shown here:

    ![具有 Cloud Shell 窗格的 Azure 门户](../images/cloud-shell.png)

    > 注意：如果以前创建了使用 Bash 环境的 Cloud shell，请使用 Cloud Shell 窗格左上角的下拉菜单将其更改为“PowerShell”。

3. Azure Synapse Analytics 基于可缩放集功能构建，以支持企业数据仓库;包括数据湖中的基于文件的数据分析以及用于加载数据的大型关系数据仓库和数据传输和转换管道。

4. 在 PowerShell 窗格中，输入以下命令以克隆此存储库：

    ```
    rm -r dp500 -f
    git clone https://github.com/MicrosoftLearning/DP-500-Azure-Data-Analyst dp500
    ```

5. 克隆存储库后，输入以下命令以更改为此实验室的文件夹，然后运行其中包含的 setup.ps1 脚本：

    ```
    cd dp500/Allfiles/03
    ./setup.ps1
    ```

6. 如果出现提示，请选择要使用的订阅（仅当有权访问多个 Azure 订阅时才会发生这种情况）。
7. 出现提示时，输入要为 Azure Synapse SQL 池设置的合适密码。

    > 注意：请务必记住此密码！

8. 在本实验室中，你将了解如何使用 Azure Synapse Analytics 中的专用 SQL 池在关系数据仓库中存储和查询数据。

## <a name="explore-the-data-warehouse-schema"></a>浏览数据仓库架构

在此实验室中，数据仓库托管在 Azure Synapse Analytics 中的专用 SQL 池中。

### <a name="start-the-dedicated-sql-pool"></a>启动专用 SQL 池

1. 脚本完成后，在 Azure 门户中转到创建的 dp500-*xxxxxxx* 资源组，然后选择 Synapse 工作区。
2. 在 Synapse 工作区“概述”页的“打开 Synapse Studio”卡中，选择“打开”，以在新浏览器标签页中打开 Synapse Studio；如果出现提示，请进行登录  。
3. 在 Synapse Studio 左侧，使用 &rsaquo;&rsaquo; 图标展开菜单，这将显示 Synapse Studio 中用于管理资源和执行数据分析任务的不同页面。
4. 在 **“管理** ”页上，确保选择了 **“SQL 池** ”选项卡，然后选择 **sql*xxxxxxx*** 专用 SQL 池，并使用其 **&#9655;** 图标启动它;确认在出现提示时要恢复它。
5. Wait for the SQL pool to resume. This can take a few minutes. Use the <bpt id="p1">**</bpt>&amp;#8635; Refresh<ept id="p1">**</ept> button to check its status periodically. The status will show as <bpt id="p1">**</bpt>Online<ept id="p1">**</ept> when it is ready.

### <a name="view-the-tables-in-the-database"></a>查看数据库中的表

1. 在Synapse Studio中，选择 **“数据**”页并确保选择了 **“工作区**”选项卡并包含 **SQL 数据库**类别。
2. 展开 **SQL 数据库**、 **sql*xxxxxxx*** 池及其 **表** 文件夹以查看数据库中的表。

    A relational data warehouse is typically based on a schema that consists of <bpt id="p1">*</bpt>fact<ept id="p1">*</ept> and <bpt id="p2">*</bpt>dimension<ept id="p2">*</ept> tables. The tables are optimized for analytical queries in which numeric metrics in the fact tables are aggregated by attributes of the entities represented by the dimension tables - for example, enabling you to aggregate Internet sales revenue by product, customer, date, and so on.
    
3. Expand the <bpt id="p1">**</bpt>dbo.FactInternetSales<ept id="p1">**</ept> table and its <bpt id="p2">**</bpt>Columns<ept id="p2">**</ept> folder to see the columns in this table. Note that many of the columns are <bpt id="p1">*</bpt>keys<ept id="p1">*</ept> that reference rows in the dimension tables. Others are numeric values (<bpt id="p1">*</bpt>measures<ept id="p1">*</ept>) for analysis.
    
    键用于将事实数据表与一个或多个维度表相关联，通常为 *星* 型架构;其中事实数据表与每个维度表直接相关， (在中心) 与事实数据表形成多尖的“星形”。

4. View the columns for the <bpt id="p1">**</bpt>dbo.DimPromotion<ept id="p1">**</ept> table, and note that it has a unique <bpt id="p2">**</bpt>PromotionKey<ept id="p2">**</ept> that uniquely identifies each row in the table. It also has an <bpt id="p1">**</bpt>AlternateKey<ept id="p1">**</ept>.

    Usually, data in a data warehouse has been imported from one or more transactional sources. The <bpt id="p1">*</bpt>alternate<ept id="p1">*</ept> key reflects the business identifier for the instance of this entity in the source, but a unique numeric <bpt id="p2">*</bpt>surrogate<ept id="p2">*</ept> key is usually generated to uniquely identify each row in the data warehouse dimension table. One of the benefits of this approach is that it enables the data warehouse to contain multiple instances of the same entity at different points in time (for example, records for the same customer reflecting their address at the time an order was placed).

5. 查看 dbo 的列 **。DimProduct**，请注意它包含一个 **ProductSubcategoryKey** 列，该列引用 **dbo。DimProductSubcategory** 表，后者又包含引用 dbo 的 **ProductCategoryKey** 列  **。DimProductCategory** 表。

    Azure Synapse Analytics *工作区*提供用于管理数据和数据处理运行时的中心点。

6. 查看 dbo 的列 **。DimDate** 表，并请注意，它包含反映日期的不同时态属性的多个列，包括星期几、月日、月、年、日名称、月名、月名等。

    可以使用Azure 门户中的交互式界面预配工作区，也可以使用脚本或模板部署工作区和资源。

## <a name="query-the-data-warehouse-tables"></a>查询数据仓库表

现在，你已经了解了数据仓库架构的一些更重要方面，现在可以查询表并检索某些数据。

### <a name="query-fact-and-dimension-tables"></a>比较事实数据表和维度表

在大多数生产方案中，最好使用脚本和模板自动预配，以便可以将资源部署合并到可重复的开发和操作中， (*DevOps*) 过程。

1. 在 **“数据**”页上，选择 **sql*xxxxxxx*** SQL 池，并在其 **...** 菜单中，选择 **“新建 SQL 脚本****空脚本** > ”。
2. When a new <bpt id="p1">**</bpt>SQL Script 1<ept id="p1">**</ept> tab opens, in its <bpt id="p2">**</bpt>Properties<ept id="p2">**</ept> pane, change the name of the script to <bpt id="p3">**</bpt>Analyze Internet Sales<ept id="p3">**</ept> and change the <bpt id="p4">**</bpt>Result settings per query<ept id="p4">**</ept> to return all rows. Then use the <bpt id="p1">**</bpt>Publish<ept id="p1">**</ept> button on the toolbar to save the script, and use the <bpt id="p2">**</bpt>Properties<ept id="p2">**</ept> button (which looks similar to <bpt id="p3">**</bpt>&amp;#128463;.<ept id="p3">**</ept>) on the right end of the toolbar to close the <bpt id="p4">**</bpt>Properties<ept id="p4">**</ept> pane so you can see the script pane.
3. 在新的空代码单元格中，添加以下代码：

    ```sql
    SELECT  d.CalendarYear AS Year,
            SUM(i.SalesAmount) AS InternetSalesAmount
    FROM FactInternetSales AS i
    JOIN DimDate AS d ON i.OrderDateKey = d.DateKey
    GROUP BY d.CalendarYear
    ORDER BY Year;
    ```

4. Use the <bpt id="p1">**</bpt>&amp;#9655; Run<ept id="p1">**</ept> button to run the script, and review the results, which should show the Internet sales totals for each year. This query joins the fact table for Internet sales to a time dimension table based on the order date, and aggregates the sales amount measure in the fact table by the calendar month attribute of the dimension table.

5. 按如下所示修改查询，从时间维度添加月份属性，然后运行修改后的查询。

    ```sql
    SELECT  d.CalendarYear AS Year,
            d.MonthNumberOfYear AS Month,
            SUM(i.SalesAmount) AS InternetSalesAmount
    FROM FactInternetSales AS i
    JOIN DimDate AS d ON i.OrderDateKey = d.DateKey
    GROUP BY d.CalendarYear, d.MonthNumberOfYear
    ORDER BY Year, Month;
    ```

    Note that the attributes in the time dimension enable you to aggregate the measures in the fact table at multiple hierarchical levels - in this case, year and month. This is a common pattern in data warehouses.

6. 按如下所示修改查询以删除月份，并将第二个维度添加到聚合中，然后运行该查询以查看结果 (，其中显示了每个区域的每年 Internet 销售总额) ：

    ```sql
    SELECT  d.CalendarYear AS Year,
            g.EnglishCountryRegionName AS Region,
            SUM(i.SalesAmount) AS InternetSalesAmount
    FROM FactInternetSales AS i
    JOIN DimDate AS d ON i.OrderDateKey = d.DateKey
    JOIN DimCustomer AS c ON i.CustomerKey = c.CustomerKey
    JOIN DimGeography AS g ON c.GeographyKey = g.GeographyKey
    GROUP BY d.CalendarYear, g.EnglishCountryRegionName
    ORDER BY Year, Region;
    ```

    使用页面顶部搜索栏右侧的 [\>_] 按钮在 Azure 门户中创建新的 Cloud Shell，在出现提示时选择“PowerShell”环境并创建存储。

7. 修改并重新运行查询以添加另一个雪花维度，并按产品类别聚合年度区域销售额：

    ```sql
    SELECT  d.CalendarYear AS Year,
            pc.EnglishProductCategoryName AS ProductCategory,
            g.EnglishCountryRegionName AS Region,
            SUM(i.SalesAmount) AS InternetSalesAmount
    FROM FactInternetSales AS i
    JOIN DimDate AS d ON i.OrderDateKey = d.DateKey
    JOIN DimCustomer AS c ON i.CustomerKey = c.CustomerKey
    JOIN DimGeography AS g ON c.GeographyKey = g.GeographyKey
    JOIN DimProduct AS p ON i.ProductKey = p.ProductKey
    JOIN DimProductSubcategory AS ps ON p.ProductSubcategoryKey = ps.ProductSubcategoryKey
    JOIN DimProductCategory AS pc ON ps.ProductCategoryKey = pc.ProductCategoryKey
    GROUP BY d.CalendarYear, pc.EnglishProductCategoryName, g.EnglishCountryRegionName
    ORDER BY Year, ProductCategory, Region;
    ```

    这一次，产品类别的雪花维度需要三个联接来反映产品、子类别和类别之间的分层关系。

8. 发布脚本以保存它。

### <a name="use-ranking-functions"></a>使用排名和行集函数

分析大量数据时的另一个常见要求是按分区对数据进行分组，并根据特定指标确定分区中每个实体的 *排名* 。

1. 在现有查询下，添加以下 SQL 以根据国家/地区名称检索 2022 分区的销售值：

    ```sql
    SELECT  g.EnglishCountryRegionName AS Region,
            ROW_NUMBER() OVER(PARTITION BY g.EnglishCountryRegionName
                              ORDER BY i.SalesAmount ASC) AS RowNumber,
            i.SalesOrderNumber AS OrderNo,
            i.SalesOrderLineNumber AS LineItem,
            i.SalesAmount AS SalesAmount,
            SUM(i.SalesAmount) OVER(PARTITION BY g.EnglishCountryRegionName) AS RegionTotal,
            AVG(i.SalesAmount) OVER(PARTITION BY g.EnglishCountryRegionName) AS RegionAverage
    FROM FactInternetSales AS i
    JOIN DimDate AS d ON i.OrderDateKey = d.DateKey
    JOIN DimCustomer AS c ON i.CustomerKey = c.CustomerKey
    JOIN DimGeography AS g ON c.GeographyKey = g.GeographyKey
    WHERE d.CalendarYear = 2022
    ORDER BY Region;
    ```

2. Cloud Shell 在 Azure 门户底部的窗格中提供命令行界面，如下所示：

    | 区域 | RowNumber | OrderNo | LineItem | SalesAmount | RegionTotal | RegionAverage |
    |--|--|--|--|--|--|--|
    |澳大利亚|1|SO73943|2|2.2900|2172278.7900|375.8918|
    |澳大利亚|2|SO74100|4|2.2900|2172278.7900|375.8918|
    |...|...|...|...|...|...|...|
    |澳大利亚|577.9|SO64284|1|2443.3500|2172278.7900|375.8918|
    |加拿大|1|SO66332|2|2.2900|563177.1000|157.8411|
    |加拿大|2|SO68234|2|2.2900|563177.1000|157.8411|
    |...|...|...|...|...|...|...|
    |加拿大|3568|SO70911|1|2443.3500|563177.1000|157.8411|
    |法国|1|SO68226|3|2.2900|816259.4300|315.4016|
    |法国|2|SO63460|2|2.2900|816259.4300|315.4016|
    |...|...|...|...|...|...|...|
    |法国|2588|SO69100|1|2443.3500|816259.4300|315.4016|
    |德国|1|SO70829|3|2.2900|922368.2100|352.4525|
    |德国|2|SO71651|2|2.2900|922368.2100|352.4525|
    |...|...|...|...|...|...|...|
    |德国|2617|SO67908|1|2443.3500|922368.2100|352.4525|
    |英国|1|SO66124|3|2.2900|1051560.1000|341.7484|
    |英国|2|SO67823|3|2.2900|1051560.1000|341.7484|
    |...|...|...|...|...|...|...|
    |英国|3077|SO71568|1|2443.3500|1051560.1000|341.7484|
    |美国|1|SO74796|2|2.2900|2905011.1600|289.0270|
    |美国|2|SO65114|2|2.2900|2905011.1600|289.0270|
    |...|...|...|...|...|...|...|
    |美国|10051|SO66863|1|2443.3500|2905011.1600|289.0270|

    观察以下有关这些结果的事实：

    - 每个销售订单行项都有一行。
    - 这些行根据销售的地理位置在分区中组织。
    - 每个地理分区中的行按销售额顺序进行编号， (从最小到最高) 。
    - 对于每一行，包括行项销售金额以及区域总计和平均销售金额。

3. 在现有查询下，添加以下代码以在 GROUP BY 查询中应用窗口化函数，并根据其总销售额对每个区域中的城市进行排名：

    ```sql
    SELECT  g.EnglishCountryRegionName AS Region,
            g.City,
            SUM(i.SalesAmount) AS CityTotal,
            SUM(SUM(i.SalesAmount)) OVER(PARTITION BY g.EnglishCountryRegionName) AS RegionTotal,
            RANK() OVER(PARTITION BY g.EnglishCountryRegionName
                        ORDER BY SUM(i.SalesAmount) DESC) AS RegionalRank
    FROM FactInternetSales AS i
    JOIN DimDate AS d ON i.OrderDateKey = d.DateKey
    JOIN DimCustomer AS c ON i.CustomerKey = c.CustomerKey
    JOIN DimGeography AS g ON c.GeographyKey = g.GeographyKey
    GROUP BY g.EnglishCountryRegionName, g.City
    ORDER BY Region;
    ```

4. Select only the new query code, and use the <bpt id="p1">**</bpt>&amp;#9655; Run<ept id="p1">**</ept> button to run it. Then review the results, and observe the following:
    - 结果包括每个城市（按区域分组）的行。
    - 为每个城市计算单个销售金额的总销售额 (总和) 
    - 区域销售总额 (区域) 中每个城市的销售额总和是根据区域分区计算的。
    - 每个城市在其区域分区中的排名是通过按降序对每个城市的总销售额进行排序来计算的。

5. 发布更新的脚本以保存更改。

> <bpt id="p1">**</bpt>Tip<ept id="p1">**</ept>: ROW_NUMBER and RANK are examples of ranking functions available in Transact-SQL. For more details, see the <bpt id="p1">[</bpt>Ranking Functions<ept id="p1">](https://docs.microsoft.com/sql/t-sql/functions/ranking-functions-transact-sql)</ept> reference in the Transact-SQL language documentation.

### <a name="retrieve-an-approximate-count"></a>检索近似计数

When exploring very large volumes of data, queries can take significant time and resources to run. Often, data analysis doesn't require absolutely precise values - a comparison of approximate values may be sufficient.

1. 在现有查询下，添加以下代码以检索每个日历年度的销售订单数：

    ```sql
    SELECT d.CalendarYear AS CalendarYear,
        COUNT(DISTINCT i.SalesOrderNumber) AS Orders
    FROM FactInternetSales AS i
    JOIN DimDate AS d ON i.OrderDateKey = d.DateKey
    GROUP BY d.CalendarYear
    ORDER BY CalendarYear;
    ```

2. 请注意，可以通过拖动窗格顶部的分隔条或使用窗格右上角的 &#8212;、&#9723; 或 X 图标来调整 Cloud Shell 的大小，以最小化、最大化和关闭窗格  。
    - 在查询下的 **“结果** ”选项卡上，查看每年的订单计数。
    - 在 **“消息** ”选项卡上，查看查询的总执行时间。
3. 有关如何使用 Azure Cloud Shell 的详细信息，请参阅 [Azure Cloud Shell 文档](https://docs.microsoft.com/azure/cloud-shell/overview)。

    ```sql
    SELECT d.CalendarYear AS CalendarYear,
        APPROX_COUNT_DISTINCT(i.SalesOrderNumber) AS Orders
    FROM FactInternetSales AS i
    JOIN DimDate AS d ON i.OrderDateKey = d.DateKey
    GROUP BY d.CalendarYear
    ORDER BY CalendarYear;
    ```

4. 查看返回的输出：
    - On the <bpt id="p1">**</bpt>Results<ept id="p1">**</ept> tab under the query, view the order counts for each year. These should be within 2% of the actual counts retrieved by the previous query.
    - On the <bpt id="p1">**</bpt>Messages<ept id="p1">**</ept> tab, view the total execution time for the query. This should be shorter than for the previous query.

5. 发布脚本以保存更改。

> 有关更多详细信息，请参阅 [APPROX_COUNT_DISTINCT](https://docs.microsoft.com/sql/t-sql/functions/approx-count-distinct-transact-sql) 函数文档。

## <a name="challenge---analyze-reseller-sales"></a>挑战 - 分析经销商销售

1. 为 **sql*xxxxxxx*** SQL 池创建新的空脚本，并使用“ **分析经销商销售**”名称保存它。
2. 在脚本中创建 SQL 查询，根据 **FactResellerSales** 事实数据表及其相关的维度表查找以下信息：
    - 每个会计年度和季度销售的项目总数。
    - 与销售人员关联的每个会计年度、季度和销售区域销售的项目总数。
    - 按产品类别按会计年度、季度和销售区域销售的项目总数。
    - 每个财政年度每个销售区域基于年度的总销售额的排名。
    - 每个销售区域中每年的大致销售订单数。

    > **提示**：将查询与Synapse Studio“**开发**”页**的解决方案脚本中的**查询进行比较。

3. 试验查询，以探索数据仓库架构中的其余表作为休闲。
4. 完成后，在 **“管理** ”页上暂停 **sql*xxxxxxx*** 专用 SQL 池。

## <a name="delete-azure-resources"></a>删除 Azure 资源

你已完成对 Azure Synapse Analytics 的探索，现在应删除已创建的资源，以避免产生不必要的 Azure 成本。

1. 关闭 Synapse Studio 浏览器选项卡并返回到 Azure 门户。
2. 在 Azure 门户的“主页”上，选择“资源组”。
3. 选择 Synapse Analytics 工作区的 dp500-*xxxxxxx* 资源组（不是受管理资源组），并确认它包含 Synapse 工作区、存储帐户和工作区的 Spark 池。
4. 在资源组的“概述”页的顶部，选择“删除资源组”。
5. 输入 dp500-*xxxxxxx* 资源组名称以确认要删除该资源组，然后选择“删除” 。

    几分钟后，将删除 Azure Synapse 工作区资源组及其关联的托管工作区资源组。
