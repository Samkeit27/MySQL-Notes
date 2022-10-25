【第三章】在多张表格中检索数据
Retrieving Data From Multiple Tables (时长1小时2分)

常常需要在多张表中检索数据，这一章讲的就是这个，具体来说，主要讲如何横向连接表和纵向连接查询结果

1. 内连接
Inner Joins (8:26)

小结

各表分开存放是为了减少重复信息和方便修改，需要时可以根据相互之间的关系连接成相应的合并详情表以满足相应的查询。FROM JOIN ON 语句就是告诉sql： 将哪几张表以什么基础连接/合并起来。

这种有多表合并的查询语句可分两部分从后往前看：

1. 后面的 from 表A join 表B on AB的关系，就是以某些相关联的列为依据（关系型数据库就是这么来的）进行多表合并得到所需的详情表

2. 前面的 select 就是在合并详情表中找到所需的列

关于表别名

之前在SELECT中给选定的列加别名主要是为了得到更有意义的列名，这里在 FROM JOIN 中给表加别名主要是为了简化

USE sql_store;

select 
    order_id, 
    o.customer_id, 
    ……
o.是别名，是之后from语句里定义的，试过，不用确实会报错，因为两个表都有这个customer_id列，只写customer_id的话会报错：ambiguous，必须指定一个表的customer_id，这里指定任意一个表的都行，因为正是按相等的customer_id来链接两个表的。总之选择多张表里都有的同名列时，必须加上表名前缀来明确列的来源。Mosh 个人喜欢在多表查询时将SELECT里所有的列名都加上表名前缀，因为这样更清晰，也能保证不会出错（我觉得倒也没必要全都加）

    ……
    first_name, 
    last_name
from orders as o
……
用了别名后其他地方（包括前面select语句，这一点当时觉得挺奇怪的，后来知道了是SQL语句执行顺序的关系，FROM … JOIN … 语句最先执行）只能用别名，用全名会报错。另外就像在select里一样，这里as也是可省略的。

……
(inner) join customers c 
    on o.customer_id = c.customer_id
因为别名需要在 WHERE JOIN 语句中确定等原因，最好先SELECT * FROM 选择全部，等写好了 FROM JOIN ON 等后面的语句，即确定了选哪些表以及怎么链接它们并取好了表别名后，再回头去 SELECT 里细化明确需要的列。

练习

通过 product_id 链接 orders_items 和 products:

USE sql_store;

select *
/select oi.*, p.name
/select 
    order_id, 
    oi.product_id, 
    name, 
    quantity, 
    oi.unit_price  
    ……
两个表都有unit_price，故要指明要的是哪一个，有两个单价是因为单价会变，订单项目表里的是下订单时的实际单价，产品表里的单价是目前的价格，若计算总价该用前者，别搞错了

……
from order_items oi
join products p
    on oi.product_id = p.product_id  -- 链接的基础
2. 跨数据库连接（合并）
Joining Across Databases (2:47)

小结

有时需要选取不同库的表的列，其他都一样，就只是WHERE JOIN里对于非现在正在用的库的表要加上库名前缀而已。依然可用别名来简化

实例

use sql_store;

select * from order_items oi
join sql_inventory.products p
    on oi.product_id = p.product_id
或

use sql_inventory;

select * from sql_store.order_items oi
join products p
    on oi.product_id = p.product_id
可见只有非当前使用的库才要加库前缀

3. 自连接
Self Joins (4:13)

小结

一个表和它自己合并。如下面的例子，员工的上级也是员工，所以也在员工表里，所以想得到的有员工和他的上级信息的合并表，就要员工表自己和自己合并，用两个不同的表别名即可实现。这个例子中只有两级，但也可用类似的方法构建多层级的组织结构。

案例

USE sql_hr;

select 
    e.employee_id,
    e.first_name,
    m.first_name as manager
……
自合并必然每列都要加表前缀，因为每列都同时在两张表中出现。另外，两个 first_name 列有歧义，注意将最后一列改名为 manager 使得结果表更易于理解

……
from employees e
join employees m
    on e.reports_to = m.employee_id
4. 多表连接
Joining Multiple Tables (6:46)

小结

FROM 一个核心表A，用多个 JOIN …… ON …… 分别通过不同的链接关系链接不同的表B、C、D……，通常是让表B、C、D……为表A提供更详细的信息从而合并为一张详情合并版A表，即：

FROM  A 
    JOIN B ON AB的关系 
    JOIN C ON AC的关系 
    JOIN D ON AD的关系 
    ……
将得到一个合并了BCD……等表详细信息的详情合并版A表

真实工作场景中有时甚至要合并十多张表

案例1

订单表同时链接顾客表和订单状态表，合并为有顾客和状态信息的详细订单表

USE sql_store;

SELECT 
    o.order_id, 
    o.order_date,
    c.first_name,
    c.last_name,
    os.name AS status
FROM orders o
JOIN customers c
    ON o.customer_id = c.customer_id
JOIN order_statuses os
    ON o.status = os.order_status_id
案例2

同理，支付记录表链接顾客表和支付方式表形成支付记录详情表

USE sql_invoicing;

SELECT 
    p.invoice_id,
    p.date,
    p.amount,
    c.name,
    pm.name AS payment_method
FROM payments p
JOIN clients c
    ON p.client_id = c.client_id
JOIN payment_methods pm
    ON p.payment_method = pm.payment_method_id
5. 复合连接条件
Compound Join Conditions (3:41)

小结

像订单项目（order_items）这种表，订单id和产品id合在一起才能唯一表示一条记录，这叫复合主键，设计模式下也可以看到两个字段都有PK标识，订单项目备注表（order_item_notes）也是这两个复合主键，因此他们两合并时要用复合条件：FROM 表1 JOIN 表2 ON 条件1 【AND】 条件2

案例

将订单项目表和订单项目备注表合并

USE sql_store;

SELECT * 
FROM order_items oi
JOIN order_item_notes oin
    ON oi.order_Id = oin.order_Id
    AND oi.product_id = oin.product_id
6. 隐式连接语法
Implicit Join Syntax (2:20)

小结

就是用FROM WHERE取代FROM JOIN ON

注意

尽量别用，因为若忘记WHERE条件筛选语句，不会报错但会得到交叉合并（cross join）结果：即10条order会分别与10个customer结合，得到100条记录。最好使用显性合并语法，因为会强制要求你写合并条件ON语句，不至于漏掉。

案例

合并顾客表和订单表，显性合并：

USE sql_store;

SELECT * 
FROM orders o
JOIN customers c
    ON o.customer_id = c.customer_id
隐式合并语法：

SELECT * 
FROM orders o, customers c  
WHERE o.customer_id = c.customer_id
注意 FROM 子句里的逗号，就像 SELECT 多条列用逗号隔开一样，FROM 多个表也用逗号隔开，此时若忘记WHERE条件筛选语句则得到这几张表的交叉合并结果

这里也可以看得出来，ON/USING 和 WHERE 以及后面会学的 HAVING 的作用是类似的，本质上都是对行进行筛选的条件语句，只不过使用的位置不一样而已

7. 外连接
Outer Joins (6:27)

小结

(INNER) JOIN 结果只包含两表的交集，另外注意“广播（broadcast）”效应
LEFT/RIGHT (OUTER) JOIN 结果里除了交集，还包含只出现在左/右表中的记录
案例

合并顾客表和订单表，用 INNER JOIN：

USE sql_store;

SELECT 
    c.customer_id,
    c.first_name,
    o.order_id
FROM customers c
JOIN orders o
    ON o.customer_id = c.customer_id
ORDER BY customer_id
这样是INNER JOIN，只展示有订单的顾客（及其订单），也就是两张表的交集，但注意这里因为一个顾客可能有多个订单，所以INNER JOIN以后顾客信息其实是是广播了的，即一条顾客信息被多条订单记录共用，当然 这叫广播（broadcast）效应，是另一个问题，这里关注的重点是 INNER JOIN 的结果确实是两表的交集，是那些同时有顾客信息和订单信息的记录。

若要展示全部顾客（及其订单，如果有的话），要改用LEFT (OUTER) JOIN，结果相较于 INNER JOIN 多了没有订单的那些顾客，即只有顾客信息没有订单信息的记录

当然，也可以调换左右表的顺序（即调换FROM和JOIN的对象）再 RIGHT JOIN，即：

FROM orders o
    RIGHT [OUTER] JOIN customers c
    -- 中括号 [] 表示是可选项、可省略 
    ON o.customer_id = c.customer_id
若要展示全部订单（及其顾客），就应该是 orders RIGHT JOIN customers，结果相较于 INNER JOIN 多了没有顾客的那些订单，即只有订单信息没有顾客信息的记录。（注：因为这里所有订单都有顾客，所以这里 RIGHT JOIN 结果和 INNER JOIN 一样）

练习

展示各产品在订单项目中出现的记录和销量，也要包括没有订单的产品

SELECT 
    p.product_id,
    p.name, -- 或者直接name
    oi.quantity -- 或者直接quantity
FROM products p
LEFT JOIN order_items oi
    ON p.product_id = oi.product_id
8. 多表外连接
Outer Join Between Multiple Tables (6:18)

小结

与内连接类似，我们可以对多个表（3个及以上）进行外连接，最好只用 JOIN 和 LEFT JOIN

案例

查询顾客、订单和发货商记录，要包括所有顾客（包括无订单的顾客），也要包括所有订单（包括未发出的）

USE sql_store;

SELECT 
    c.customer_id,
    c.first_name,
    o.order_id,
    sh.name AS shipper
FROM customers c
LEFT JOIN orders o
    ON c.customer_id = o.customer_id
LEFT JOIN shippers sh
    ON o.shipper_id = sh.shipper_id
ORDER BY customer_id
最佳实践

虽然可以调换顺序并用 RIGHT JOIN，但作为最佳实践，最好调整顺序并统一只用 [INNER] JOIN 和 LEFT [OUTER] JOIN（总是左表全包含），这样，当要合并的表比较多时才方便书写和理解而不易混乱

练习

查询 订单 + 顾客 + 发货商 + 订单状态，包括所有的订单（包括未发货的），其实就只是前两个优先级变了一下，是要看全部订单而非全部顾客了

USE sql_store;

SELECT 
    o.order_id,
    o.order_date,
    c.first_name AS customer,
    sh.name AS shipper,
    os.name AS status
FROM orders o
JOIN customers c
    ON o.customer_id = c.customer_id
LEFT JOIN shippers sh
    ON o.shipper_id = sh.shipper_id
JOIN order_statuses os
    ON o.status = os.order_status_id
订单必有顾客和状态，所以这第1个和第3个 JOIN 加不加 LEFT 效果一样 但订单不一定发货了，即不一定有发货商，所以第2个 JOIN 必须是 LEFT JOIN，否者会筛掉没发货的订单

9. 自我外部连接
Self Outer Joins (2:12)

小结

就用前面那个员工表的例子来说，就是用LEFT JOIN让得到的 员工-上级 合并表也包括老板本人（老板没有上级，即 reports_to 字段为空，如果用 JOIN 会被筛掉，用 LEFT JOIN 才能保留）

USE sql_hr;

SELECT 
    e.employee_id,
    e.first_name,
    m.first_name AS manager
FROM employees e
LEFT JOIN employees m  -- 包含所有雇员（包括没有report_to的老板本人）
    ON e.reports_to = m.employee_id
10. USING子句
The USING Clause (5:22)

小结

当作为合并条件（join condition）的列在两个表中有相同的列名时，可用 USING (……, ……) 取代 ON …… AND …… 予以简化，内/外链接均可如此简化。

注意

一定注意 USING 后接的是括号，特容易搞忘

实例

SELECT
    o.order_id,
    c.first_name,
    sh.name AS shipper
FROM orders o
JOIN customers c
    USING (customer_id)
LEFT JOIN shippers sh
    USING (shipper_id)
ORDER BY order_id
复合主键表间复合连接条件的合并也可用 USING，中间逗号隔开就行：

SELECT *
FROM order_items oi
JOIN order_item_notes oin

ON oi.order_id = oin.order_Id AND
    oi.product_id = oin.product_id
/USING (order_id, product_id)
USING对复合主键的简化效果更加明显

练习

sql_invoicing库里，将payments、clients、payment_methods三张表合并起来，以知道什么日期哪个顾客用什么方式付了多少钱

USE sql_invoicing;

SELECT 
    p.date,
    c.name AS client,
    pm.name AS payment_method,
    p.amount
FROM payments p
JOIN clients c USING (client_id)
JOIN payment_methods pm
    ON p.payment_method = pm.payment_method_id
注意

列名不同就必须用 ON …… 了
实际中同一个字段在不同表列名不同的情况也很常见（如上面的 payment_method 和payment_method_id），不能想当然的用USING

11. 自然连接
Natural Joins (1:21)

小结

NATURAL JOIN 就是让MySQL自动检索同名列作为合并条件。

注意

最好别用，因为不确定合并条件是否找对了，有时会造成无法预料的问题，编程时保持对结果的控制是非常重要的

但也要知道有这个东西，混个脸熟，不要别人用了看不懂。

实例

USE sql_store;

SELECT 
    o.order_id,
    c.first_name
FROM orders o
NATURAL JOIN customers c
12. 交叉连接
Cross Joins (3:14)

小结

得到名字和产品的所有组合，因此不需要合并条件。 实际运用如：要得到尺寸和颜色的全部组合

实例

得到顾客和产品的全部组合（毫无意义，纯粹为了展示交叉连接）

USE sql_store;

SELECT 
    c.first_name AS customer,
    p.name AS product
FROM customers c
CROSS JOIN products p
ORDER BY c.first_name
上面是显性语法，还有隐式语法，之前讲过，其实就是隐式内合并忽略WHERE子句（即合并条件）的情况，也就是把 CROSS JOIN 改为逗号，即 FROM A CROSS JOIN B 等效于 FROM A, B，Mosh更推荐显式语法，因为更清晰

USE sql_store;

SELECT 
    c.first_name,
    p.name
FROM customers c, products p
ORDER BY c.first_name
练习

交叉合并shippers和products，分别用显式和隐式语法

USE sql_store;

SELECT 
    sh.name AS shippers,
    p.name AS product
FROM shippers sh
CROSS JOIN products p
ORDER BY sh.name
或

SELECT 
    sh.name AS shippers,
    p.name AS product
FROM shippers sh, products p
ORDER BY sh.name
13. 联合
Unions (8:29)

小结

FROM …… JOIN …… 可对多张表进行横向列合并，而 …… UNION …… 可用来按行纵向合并多个查询结果，这些查询结果可能来自相同或不同的表

同一张表可通过UNION添加新的分类字段，即先通过分类查询并添加新的分类字段再UNION合并为带分类字段的新表。
不同表通过UNION合并的情况如：将一张18年的订单表和19年的订单表纵向合并起来在一张表里展示
注意

合并的查询结果必须列数相等，否则会报错
合并表里的列名由排在 UNION 前面的决定
案例1

给订单表增加一个新字段——status，用以区分今年的订单和今年以前的订单

USE sql_store;

    SELECT 
        order_id,
        order_date,
        'Active' AS status
    FROM orders
    WHERE order_date >= '2019-01-01'

UNION

    SELECT 
        order_id,
        order_date,
        'Archived' AS status  -- Archived 归档
    FROM orders
    WHERE order_date < '2019-01-01';
案例2

合并不同表的例子——在同一列里显示所有顾客名以及所有商品名

USE sql_store;

    SELECT first_name AS name_of_all
    -- 新列名由排UNION前面的决定
    FROM customers

UNION

    SELECT name
    FROM products
练习

给顾客按积分大小分类，添加新字段type，并按顾客id排序，分类标准如下

points	type
<2000	Bronze
2000~3000	Silver
>3000	Gold
    SELECT 
        customer_id,
        first_name,
        points,
        'Bronze' AS type
    FROM customers 
    WHERE points < 2000

UNION

    SELECT 
        customer_id,
        first_name,
        points,
        'Silver' AS type
    FROM customers 
    WHERE points BETWEEN 2000 and 3000

UNION

    SELECT 
        customer_id,
        first_name,
        points,
        'Gold' AS type
    FROM customers 
    WHERE points > 3000

ORDER BY customer_id
可以看出ORDER BY的优先级在UNION之后，应该是排序和限制语句的执行优先级比较靠后，不知能否用括号调整执行顺序让这个ORDER BY只作用于最后一个子查询？（估计实际中很少有这种需求，一般都是最后统一排序）。另外，这里如果没有 ORDER BY 的话就会按3个 query 的先后来排序。

总结

感觉本质上可以将查询语句的任何一步和任何一个层次，包括：

横纵筛选 SELECT ……WHERE ……
选表 FROM ……
横纵连接 …… JOIN ………… UNION ……
排序、限制ORDER BY ……LIMIT ……
都看作暂时生成了一张新表（虚拟表），将后续步骤都看作是在对这些新表进行进一步的操作， 这样，层次步骤就能理清，就好理解了，也才真的能从本质上掌握并灵活运用
