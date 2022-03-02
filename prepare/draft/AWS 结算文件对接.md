# AWS 结算文件对接

已经为您创建了一个专用的AWS S3存储库，用于访问售后付款结算文件。您可以通过awss3门户访问存储库，或者使用HTTPS GET以编程方式访问存储库。您的Afterpay商户帐户ID用作存储库文件夹名称，也用作登录S3门户的IAM用户名。如果您有多个品牌或地区，并且使用多个Afterpay商户帐户，则会为每个帐户创建一个单独的S3存储库。您可以读取和提取每个存储库中的每日结算文件。您可以在Amazon IAM用户凭据中创建新的安全密钥。首次登录到AWS S3门户后，如果要创建、修改和查看访问密钥，可以按照AWS说明进行操作

##  门户访问



## 编程访问

AWS S3 API文档，用于编程下载售后付款结算文件，请访问：http://docs.aws.amazon.com/AmazonS3/latest/API/Welcome.html

- 您可以使用 GET 操作检索结算文件。
- DELETE 和 PUT 操作未启用。





使用AWS SDK发送请求（请参阅示例代码和库）。使用此选项，您不需要编写代码来计算请求身份验证的签名，因为SDK客户机使用您提供的访问密钥对您的请求进行身份验证。除非您有充分的理由不这样做，否则您应该始终使用AWS SDK。

使用awscli进行amazons3api调用。有关设置AWS CLI和示例Amazon S3命令的信息，请参阅以下主题：

在《Amazon简单存储服务开发人员指南》中设置AWS CLI。

在AWS命令行界面用户指南中，将AmazonS3与AWS命令行界面一起使用。







####　研发流程规范

公司骨架工程

代码规范

接口规范

多实现少修改

单元测试覆盖率

codeup规范

测试前必须保证冒烟

bug日清

上线需要评审

线上问题解决效率

验收



##### 系统稳定要求

集群部署

系统间依赖关系要递高



子站点

```
[
            {
                "name":"cupshe",
                "siteId":1,
                "id":1
            },
            {
                "name":"de-cupshe",
                "siteId":1,
                "id":2
            },
            {
                "name":"fr-cupshe",
                "siteId":1,
                "id":3
            },
            {
                "name":"es-cupshe",
                "siteId":1,
                "id":4
            },
            {
                "name":"it-cupshe",
                "siteId":1,
                "id":5
            },
            {
                "name":"au-cupshe",
                "siteId":1,
                "id":6
            },
            {
                "name":"app",
                "siteId":2,
                "id":7
            },
            {
                "name":"Cupshe-US",
                "siteId":3,
                "id":8
            },
            {
                "name":"Cupshe-CA",
                "siteId":3,
                "id":9
            },
            {
                "name":"Cupshe-DE",
                "siteId":3,
                "id":10
            },
            {
                "name":"Seaselfie-US",
                "siteId":3,
                "id":11
            },
            {
                "name":"Seaselfie-CA",
                "siteId":3,
                "id":12
            }
        ]
```


paypal 全站点数据源文件拉取APP和fr站、es站、it站
afterpay 全站点数据源文件拉取APP和fr站、es站、au站


paypal 全站点数据源文件上传APP和fr站、es站、it站
afterpay 全站点数据源文件上传APP和fr站、es站、au站

app订单数据从大数据获取，反向订单渠道取正向订单渠道，日期按照先后顺序排序



APP正向应收单

```sql
select a.*,t1.payment_method,t1.amount,t1.date_add  from
(
SELECT distinct  t.id_order,t.reference,t.total_paid from ps_orders t
inner join ps_order_history t2
on t.id_order=t2.id_order
and t2.id_order_state=2
and t.date_add>='2021-03-09 00:00:00'
and t.date_add<'2021-03-10 00:00:00'
GROUP BY  t.id_order,t.reference,t.total_paid 
) a
inner join ps_order_payment t1
on a.reference=t1.order_reference
and  upper(t1.payment_method)='PAYPAL'
ORDER BY t1.date_add
```

APP反向应收单

```sql
select a.*,t1.payment_method from
(
select  t.id_order
,t1.reference
,t1.date_add  refund_time
,t.amount+t.other_cost_amount+ shipping_method+tax_cost_amount  as amount
from ps_order_slip t
inner join ps_orders t1
on t.id_order=t1.id_order
and t.date_add>='2021-03-09 00:00:00'
and t.date_add<'2021-03-10 00:00:00'
) a
inner join ps_order_payment t1
on a.reference=t1.order_reference
and  upper(t1.payment_method)='PAYPAL'
ORDER BY a.refund_time
```





# SQL脚本

站点配置表添加字段

ALTER TABLE `plutus`.`plutus_serial_site`  ADD COLUMN `regions` char(50) NOT NULL DEFAULT '' COMMENT 'S3存储的header必须字段'