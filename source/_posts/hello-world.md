---
title: hello world
typora-root-url: ..
typora-copy-images-to: ../uploads/images
date: 2018-05-21 13:22:32
categories: helijia
tags: pay
---

#### [支付](https://git.hlj.team/pay)

- hlj-pay:https://git.hlj.team/pay/hlj-pay
- pay-api:目前还在svn的exodus.api下
- exodus.pay:目前还在svn的exodus下

##### 环境

10.0.200.3|13|5|15

##### DB-table

| 表名                  | 含义     |
| --------------------- | -------- |
| sms.pay               | 支付     |
| sms.pay_detail        | 支付详情 |
| sms.pay_refund        | 退款     |
| sms.pay_refund_detail | 退款详情 |

##### 支付、退款信息查询：

客服系统后台—>订单支付&退款

##### 微信支付沙箱环境：--用于微信支付测试环境

https://svn.int.helijia.com/svn/server/hlj-pay/branches/20171227_test_wechat_interface_change_chonglou/



#### 保险

- hlj-insurance:https://git.hlj.team/finance/hlj-insurance
- insurance.api:https://git.hlj.team/finance/insurance.api
- Insurance.core:https://git.hlj.team/finance/insurance.core

##### 环境

10.0.200.18  

##### DB-table

| 表名                  | 含义       |
| --------------------- | ---------- |
| ins_insurance_channel | 保险渠道表 |
| ins_insurance_type    | 保险类型表 |
| ins_policy            | 投保详情表 |

##### 投保类目配置

[kv-config](http://easyops.int.helijia.com/doLogin):

insurance—helijia.insurance----categorys.send

##### 每月查询保险sql

```
select count(1),sum_insured from ins_policy where create_time >= '2018-04-01' and create_time < '2018-05-01' and status = 1
group by sum_insured


单价说明
非半永久 0.44元/单
半永久 10元/单
```

#### [标签](https://git.hlj.team/marque)

- admin-marque:https://git.hlj.team/marque/admin-marque
- marque.api:https://git.hlj.team/marque/marque.api
- marque.core:https://git.hlj.team/marque/marque.core
- marque-dubbo-service:https://git.hlj.team/marque/marque-dubbo-service)  提供给搜索使用



##### 环境

正式环境 10.0.200.14

##### DB-table

| 表名                   | 含义               |
| ---------------------- | ------------------ |
| marque_tag             | 标签定义表         |
| marque_rule            | 标签规则表         |
| marque_user_tag        | 用户标签表         |
| marque_user_tag_detail | 用户标签明细表     |
| marque_user_tag_log    | 标签状态变更日志表 |

##### [eswich开关](http://eswitch-console.helijia.com/)

app-customer-core-order------order_remark_tag_with_search  用户备注标签展示开关

​					    |------order_remark_tag_with_generate 用户备注标签生成开关

![屏幕快照 2018-05-18 下午6.25.03](/images/屏幕快照 2018-05-18 下午6.25.03.png)

