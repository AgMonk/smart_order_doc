#  智能订单系统文档
## 用户角色/权限

| 角色 | 终端用户 | 报单员 | 派单员 | 安装员 | 分组管理员 | 审核员 |
| ---- | -------- | ------ | ------ | ------ | ---------- | ------ |
|      |          |        |        |        |            |        |
|      |          |        |        |        |            |        |
|      |          |        |        |        |            |        |



## 业务流程

### 安装流程

1. 上报订单
   1. 分组订单：如果订单由某个分组（移动、联通等）内的报单员提交，则订单内商品将包括锁（锁内有锁体）
   2. 终端订单：如果订单由终端客户提交，则订单内将没有商品
2. 派单
   1. 派单员根据订单所处地区，安装员负责地区选派安装员
   2. 安装员接单
3. 联系确认
   1. 安装员联系客户，通过图片、视频方式确认是否满足安装要求、是否需要更换锁的自带锁体
   2. 如果需要更换锁体，安装员对订单进行修改，追加锁体
4. 在线付款（仅终端订单），将订单的"订单支付状态"设置为：已支付(微信)
5. 上门安装
   1. 安装完毕后安装员将：安装情况、安装使用的商品串号（仅分组订单）拍照上传，如有特殊情况在安装备注中说明
   2. 填写安装使用的商品串号（手动，可能有自动）
   3. 提交审核
6. 审核安装情况
   1. 查看安装情况照片，电话与业主联系确认是否安装完毕
   2. 确认填写的商品串号与图片上的串号是否一致（仅分组订单）
   3. 通过审核
7. 安装完毕
8. 结算流程

### 结算流程

- 本系统与分组结算：
  - 分组向本系统支付一定时间范围内的订单安装费用（不经过本系统）
  - 分组发起结算操作，选择需要结算的订单，填写结算费用，上传结算凭据，创建结算单据。
  - 本系统管理员查询结算单据，确认订单信息、金额、已收款后，确认结算。
  - 将这些订单的"订单支付状态"设置为已支付(其他)
- 本系统与安装员结算：
  - 本系统向安装员支付一定时间范围内的订单安装费用（不经过本系统）
  - 本系统管理员发起结算操作，选择需要结算的订单，填写结算费用，上传结算凭据，创建结算单据。
  - 安装员查询结算单据，确认订单信息、金额、已收款后，确认结算。
  - 将这些订单的"订单支付状态"设置为已支付(其他)

## 实体

### 用户组

由复数个报单员组成的组织，用户组向本系统上报安装服务订单，代收安装费用，并定期与本系统进行结算

| 字段       | 数据类型 | 说明           |
| ---------- | -------- | -------------- |
| 唯一编号   | 字符串   |                |
| 名称       | 字符串   |                |
| 状态       | 字符串   | 审核中 或 正常 |
| 管理员编号 | 字符串   |                |

#### 申请加入用户组

用户申请加入用户组的记录

| 字段       | 数据类型 | 说明                    |
| ---------- | -------- | ----------------------- |
| 唯一编号   | 字符串   |                         |
| 用户组编号 | 字符串   |                         |
| 用户编号   | 字符串   |                         |
| 状态       | 字符串   | 审核中 、已通过、已移除 |

### 商品档案

注意本实体为商品的档案信息，而非具体的一件商品

| 字段       | 数据类型   | 说明 |
| ---------- | ---------- | ---- |
| 唯一编号   | 字符串     |      |
| 名称       | 字符串     |      |
| 型号       | 字符串     |      |
| 拥有的尺寸 | 字符串数组 |      |
| 拥有的颜色 | 字符串数组 |      |

### 商品库存

代表某一商品的特定尺寸，特定颜色的库存；指向同一档案，每个尺寸和颜色的组合只允许有一条库存信息。

库存不允许用户直接操作，必须通过提交单据修改

| 字段         | 数据类型   | 说明 |
| ------------ | ---------- | ---- |
| 唯一编号     | 字符串     |      |
| 商品档案编号 | 字符串     |      |
| 型号         | 字符串     |      |
| 尺寸         | 字符串数组 |      |
| 颜色         | 字符串数组 |      |
| 数量         | 整数       |      |
| 价格         | 小数       |      |

### 商品库存变动单据

库存不允许用户直接操作，必须通过提交单据修改

| 字段       | 数据类型 | 说明                                                   |
| ---------- | -------- | ------------------------------------------------------ |
| 唯一编号   | 字符串   |                                                        |
| 库存编号   | 字符串   |                                                        |
| 操作员编号 | 字符串   |                                                        |
| 订单编号   | 字符串   | 如果变动由订单产生，记录订单的编号                     |
| 单据类型   | 字符串   | 有效值为：入库、出库、盘点                             |
| 时间戳     | 整数     | 单据创建时间                                           |
| 数量       | 整数     |                                                        |
| 事由       | 字符串   | 如果变动由订单产生，系统填写该字段；否则由操作用户填写 |

### 安装服务订单

由终端用户或报单员创建

| 字段               | 数据类型 | 说明                                                         |
| ------------------ | -------- | ------------------------------------------------------------ |
| 唯一编号           | 字符串   |                                                              |
| 时间戳             | 整数     | 单据创建时间                                                 |
| 上次状态更新时间   | 整数     | 状态变更时记录时间戳                                         |
| 订单状态           | 字符串   |                                                              |
| 订单类型           | 字符串   | 终端订单 或 分组订单                                         |
| 订单支付状态       | 字符串   | 本系统是否已收到安装费用，未支付 、 已支付（微信） 、 已支付（其他） |
| 订单支付单据编号   | 字符串   |                                                              |
| 安装费支付状态     | 字符串   | 本系统是否已向安装员支付安装费用，未支付 、 已支付（微信） 、 已支付（其他） |
| 安装费支付单据编号 | 字符串   |                                                              |
| 报单员编号         | 字符串   | 终端用户或报单员的用户编号                                   |
| 安装员编号         | 字符串   | 安装员的用户编号                                             |
| 分组编号           | 字符串   | 如果订单由隶属于某一用户组的报单员提交，该字段即为该分组编号；否则为空 |
| 业主姓名           | 字符串   |                                                              |
| 业主地址           | 字符串   |                                                              |
| 业主电话           | 电话     |                                                              |
| 订单备注           | 字符串   | 由终端用户、报单员填写，派单员、安装员查看的备注             |
| 安装备注           | 字符串   | 由安装员记录的与安装过程相关的情况说明                       |
| 撤单理由           | 字符串   | 执行撤单时填写                                               |
| 拒单理由           | 字符串   | 执行拒单时填写                                               |

#### 订单状态

- 待提交：正在编辑的订单，可以被编辑、删除；此时派单员对其不可见。执行提交操作后，状态变更为已提交
- 已提交：完成编辑的订单，可以被编辑、删除；如果进行编辑则状态回退到待提交，此时派单员对其可见，可以进行派单，指定一位安装员并执行派单操作后，状态变更为已提交；
- 已派单：已经被派发给一位安装员的订单，不可以编辑、删除；此时该安装员可以选择接单（状态变更为已接单）或拒单（状态变更为已提交）
- 已接单：安装员已接受的订单，正在安装中；安装员可以为订单中的商品补全唯一编码（SN），添加安装备注；安装完成后安装员应当上传安装完成的照片，并提交审核，订单状态变更为待审核。
- 待审核：审核员审核完毕后，确认订单完成，订单状态变更为已完成。
- 已撤单：订单状态为：已提交、已派单、已接单时，可以执行撤单操作，填写撤单理由，订单状态变更为已撤单。如有需要应另行创建。

### 结算单据

分组向本系统结算费用，或本系统向安装员结算费用的单据

| 字段       | 数据类型 | 说明                                       |
| ---------- | -------- | ------------------------------------------ |
| 唯一编号   | 字符串   |                                            |
| 单据类型   | 字符串   | 订单支付 或 安装费支付                     |
| 金额       | 小数     |                                            |
| 安装员编号 | 字符串   | 如果类型为 安装费支付 记录支付目标的安装员 |

### 订单内商品库存

某一订单名下需要被安装的商品；订单创建、编辑、删除、追加商品时，需要联动操作

| 字段     | 数据类型 | 说明                                   |
| -------- | -------- | -------------------------------------- |
| 唯一编号 | 字符串   |                                        |
| 库存编号 | 字符串   |                                        |
| 订单编号 | 字符串   | 该库存所属的安装订单编号               |
| SN码     | 字符串   | 该库存对应的实物唯一编号（位于实物上） |

