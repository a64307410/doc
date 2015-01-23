# 外卖项目 - 客户端 接口文档

######关于错误返回值与错误代码，参见 “错误代码说明”

## 注意事项


## 地址
<!-- MarkdownTOC -->
http://server请填写这部分

## 客户端 HTTP API 加密方式

### 通过HTTP 头
在 HTTP 头中发送以下内容，所有请求**均需要**带这几个头

HTTP 头名称 | 说明 | 必选
------------------------- | ------------------------- | -------------------------
deviceid| 设备唯一号，iOS 可以用 OpenUDID，Andriod 可以用 IMEI | 是
timestamp| 时间戳 | 是
version | 接口版本号，当前版本为 1 | 是
signature | 签名串 | 是

所有结果以json格式返回，其中最外层是以下三个字段的子集

字段 | 类型| 说明 
-------| -------------| -------------
code   |int           |必有项；为0表示正常，其他值表示出错
msg    |string        |当 code 不为0时才存在，表示出错信息
data   |dict          |所有返回的数据都在这个字段中

**以下返回示例，都为data字段中的数据**

##定单状态说明
状态码 | 说明 
-----------| -------------
0 |定单初始化状态
1 |定单已发布，等待车主抢单
2 |叫车超时，乘客不再等待
3 |乘客取消叫车
4 |已有车主接单，待支付
5 |支付完成，未到时间
6 |支付超时
7 |支付取消
8 |车主取消息定单
* |快到时间，准备出发（删除）
9 |到时间，待确认搭乘
10|已确认搭乘(搭乘完成）
11|已付款给车主

##认证状态说明
有三项认证状态  
1、车主认证状态  
2、证件认证状态  
3、车辆认证状态

状态值 | 说明 
-----------| -------------
0| 默认 
1| 审核中
2| 审核未通过
3| 审核通过
    
<!-- /MarkdownTOC -->

## 1 获取验证码
GET /auth/vcode

### 提交参数说明
参数字段 | 类型|说明 | 必选
--------|-------------|--------|---
mobile  |string       | 手机号  | 是

### 返回参数说明
参数字段 | 类型|说明
------------ | -------------|-------------
vcode        | string       | 6位数字


### 返回示例
```json
{
  "code": 0,
  "data": {},
  "msg": "\u64cd\u4f5c\u6210\u529f"
}
```

## 2. 手机登录
POST /auth/login

### 提交参数说明
参数字段 |类型|说明|    必须 
------------ | -------|---| -------------
mobile       |string  |手机号|是 
vcode        |string  |验证码|是 

### 返回数据说明
参数字段 | 类型|说明
-------| -------------| -------------
uid    |int           |用户id; 9位数字，非0开头
new_user    |boolean       |用户是否是新创建的
role   |enum           |0乘客，1车主   
token |string     | 登陆令牌   
...          | | 待补充       

### 返回示例
```json
{
  "code": 0,
  "data": {
    "new_user": false,
    "role": 1,
    "token": "BaBRH6o99gfiQlsUGsWwFofbqqiv9C9X",
    "uid": 1000000001
  },
  "msg": "\u64cd\u4f5c\u6210\u529f"
}
```

## 3. 用户基础信息
接口不区别车主和乘客

### 请求方法
	GET /user/profile/uid

### 请求参数
无


### 返回数据说明
参数字段 | 类型|说明
------------ | -------------| -------------
avatar| string|用户头像
company|string|公司
gender|enum|性别
industry|string|行业
license|string|驾驶证地址
mobile|string|手机号
new_user|boolean|是否是新用户
owner_ride_count|int|作为车主成功搭乘次数
owner_score_total|int|乘客对该用户评价总分
papers_status|int|车主证件认证状态
passenger_ride_count|int|作为乘客成功搭乘次数
passenger_score_total|int|车主对该用户评价总分
role|enum|0乘客 1车主|
status|int|用户认证状态,
vehicles[0]->brand|string|品牌
vehicles[0]->certificate|string|行驶证
vehicles[0]->color|string|颜色
vehicles[0]->id|string|车辆id
vehicles[0]->level|enum|车等级，经济型，舒适型，豪华型
vehicles[0]->license_plate|string|车牌
vehicles[0]->status|string|车辆认证状态
vehicles[0]->vehicle_45|string|车辆45度照
vehicles[0]->vehicle_front|string|车辆正面照
wallet->balance|int|余额
wallet->drawed|int|已提取
wallet->restrained|int|不能提取的钱
wallet->total_income|int|累计收入
wallet->un_income|int|未到账

### 返回示例

```json
{
  "code": 0,
  "data": {
    "avatar": "",
    "company": "",
    "gender": "\u7537",
    "industry": "",
    "last_name": "\u5982",
    "license": "",
    "mobile": "13683310657",
    "new_user": false,
    "owner_ride_count": 4,
    "owner_score_total": 20,
    "papers_status": 0,
    "passenger_ride_count": 4,
    "passenger_score_total": 20,
    "role": 1,
    "status": 0,
    "vehicles": [
      {
        "brand": "\u51f1\u8fea\u62c9\u514b",
        "certificate": "",
        "color": "blue",
        "id": 100000000900001,
        "level": "\u8c6a\u534e\u578b",
        "license_plate": "\u4eacV 74110",
        "status": 0,
        "vehicle_45": "",
        "vehicle_front": ""
      }
    ],
    "wallet": {
      "balance": 0,
      "drawed": 0,
      "restrained": 0,
      "total_income": 100,
      "un_income": 50
    }
  },
  "msg": "\u64cd\u4f5c\u6210\u529f"
}
```

## 4. 查看未读消息
返回用户所有的未读消息 

### 请求方法
	GET /notice/unread/uid

### 请求参数
无

### 返回数据说明

### 返回示例
```json
{
  "code": 0,
  "data": [
    {
      "body": "",
      "head": "\u60a8\u7684\u4fe1\u606f\u5df2\u901a\u8fc7\u5ba1\u6838",
      "notice_id": 1
    },
    {
      "body": "",
      "head": "\u60a8\u7684\u8bc1\u4ef6\u5df2\u901a\u8fc7\u5ba1\u6838",
      "notice_id": 2
    }
  ],
  "msg": "\u64cd\u4f5c\u6210\u529f"
}
```

## 5. 消息回执

用户读过某条消息后的回执
    
### 请求方法
    POST /notice/reply/uid
    
### 请求参数
参数 | 说明 |必选 
------------ | ------------- | ------------
notice_id| notice ID | 是

### 返回示例

```json
{
  "code": 0,
  "data": {
    "notice_id": "100000000100001"
  },
  "msg": "\u64cd\u4f5c\u6210\u529f"
}
```

## 6. 订单拉取
返回用户在某个时间范围内的定单

### 请求方法
	GET /order/list/uid

### 请求参数
参数  |类型 |说明 |必选
------|------- | ------------- | ------------
role|enum|0乘客，1车主|Y
start_time| int| 开始时间戳 | N  
end_time|int| 结束时间戳| N

### 返回数据说明
返回定单，按更新时间desc排列  
跟据role为0时返回数据里包含owner详细信息  
跟据role为1时返回数据里包含passenger详细信息  

字段 | 类型|说明
--------------- | -------------| -------------
next_page|boolean| 如果有下一页 next_page是新的end_time值，否则为0
order\_list[\*]\->appointed_at|string|约定时间
order\_list[\*]\->create_at|string|订单创建时间
order\_list[\*]\->order_no|long|订单号
order\_list[\*]\->price|double|价格
order\_list[\*]\->remark|string|乘客留言
order\_list[\*]\->update_at|string|定单信息更新时间
order\_list[\*]\->owner\->avatar|string|车主头像地址
order\_list[\*]\->owner\->company|string|车主公司
order\_list[\*]\->owner\->gender|enum'男','女'|车主性别
order\_list[\*]\->owner\->industry|string|车主行业
order\_list[\*]\->owner\->mobile|string|车主手机
order\_list[\*]\->owner\->name|string|车主名字
order\_list[\*]\->owner\->owner_ride_count|int|车主成功搭载次数
order\_list[\*]\->owner\->owner_score_total|int|乘客评分总分
order\_list[\*]\->owner\_route\->distance|double|车主路线距离
order\_list[\*]\->owner\_route\->from_poi|string|车主路线起点poi
order\_list[\*]\->owner\_route\->from_addr|string|车主路线起点名字
order\_list[\*]\->owner\_route\->from_lat|float| 纬度
order\_list[\*]\->owner\_route\->from_lng|float| 经度
order\_list[\*]\->owner\_route->id|long|车主路线ID
order\_list[\*]\->owner\_route->level|enum|车型
order\_list[\*]\->owner\_route->start_time|string|车主路线开始时间
order\_list[\*]\->passenger\_route\->to_poi|string|车主路线终点poi
order\_list[\*]\->passenger\_route\->to_addr|string|车主路线终点名字
order\_list[\*]\->passenger\_route\->to_lat|float| 纬度
order\_list[\*]\->passenger\_route\->to_lng|float| 经度
order\_list[\*]\->passenger\->avatar|string|乘客头像地址
order\_list[\*]\->passenger\->company|string|乘客公司
order\_list[\*]\->passenger\->gender|enum|乘客性别
order\_list[\*]\->passenger\->industry|string|乘客行业
order\_list[\*]\->passenger\->mobile|string|乘客手机
order\_list[\*]\->passenger\->name|string|乘客名字
order\_list[\*]\->passenger\->passenger_ride_count|int|乘客成功搭载次数
order\_list[\*]\->passenger\->passenger_score_total|int|乘客评分总分
order\_list[\*]\->passenger\_route\->distance|double|乘客路线距离
order\_list[\*]\->passenger\_route\->from_poi|string|乘客路线起点poi
order\_list[\*]\->passenger\_route\->from_addr|string|乘客路线起点名字
order\_list[\*]\->passenger\_route\->from_lat|float| 纬度
order\_list[\*]\->passenger\_route\->from_lng|float| 经度
order\_list[\*]\->passenger\_route\->id|string|乘客路线ID
order\_list[\*]\->passenger\_route\->start_time|double|乘客路线开始时间
order\_list[\*]\->passenger\_route\->to_poi|string|乘客路线终点poi
order\_list[\*]\->passenger\_route\->to_addr|string|乘客路线终点名字
order\_list[\*]\->passenger\_route\->to_lat|float| 纬度
order\_list[\*]\->passenger\_route\->to_lng|float| 经度

### 返回示例

```json
{
  "code": 0,
  "data": {
    "list": [
      {
        "appointed_at": "2014-11-20 08:00:00",
        "create_at": "2014-11-07 18:34:43",
        "order_no": 141530142493315,
        "owner_id": 1000000001,
        "owner_route": {
          "distance": 0,
          "from_addr": "",
          "from_lat": 0.0,
          "from_lng": 0.0,
          "from_poi": "P010A00CHR9",
          "route_id": 100000000100001,
          "start_time": "9:00:00",
          "to_addr": "",
          "to_lat": 0.0,
          "to_lng": 0.0,
          "to_poi": "P010A00CHR9",
          "vehicle_id": 100000000100001
        },
        "passenger": {
          "avatar": "",
          "company": "",
          "gender": "\u7537",
          "id": 1000000006,
          "industry": "",
          "last_name": "\u5b59",
          "mobile": "11652515135",
          "passenger_ride_count": 0,
          "passenger_score_total": 0
        },
        "passenger_route": {
          "distance": 10000,
          "from_addr": "",
          "from_lat": 0.0,
          "from_lng": 0.0,
          "from_poi": "P010A00CHR9",
          "route_id": 100000000600002,
          "to_addr": "",
          "to_lat": 0.0,
          "to_lng": 0.0,
          "to_poi": "P010A00CHR9"
        },
        "price": 7.0,
        "remark": "\u8f66\u8981\u5e72\u51c0",
        "state": 3,
        "update_at": "2014-12-26 11:35:25",
        "vehicle": {
          "brand": "\u5225\u514b",
          "certificate": "/uploads/certificate/100000000100001_c5424d63556c0032.JPG",
          "color": "\u7ea2\u8272",
          "level": "\u7ecf\u6d4e\u578b",
          "license_plate": "\u4eacA10001",
          "vehicle_45": "/uploads/vehicle_45/100000000100001_46f463ebb1e73a0c.JPG",
          "vehicle_front": "/uploads/vehicle_front/100000000100001_f57f2a4f0c7509a0.JPG",
          "vehicle_id": 100000000100001
        },
        "vehicle_option": 7
      },
      ... 
    ],
  "next_page": 0
},

```

## 7 乘客下定单

### 请求方法
POST /order/place/uid

### 请求参数
参数 |类型| 说明 | 必选 
-------|----- | ------------- | ------------
from_poi|string| 起点 | 是
to_poi|string| 终点 | 是
vehicle_options|int| 1经济型 2舒适型 4豪华型 相+组合;默认为7| 否
appointed_ts |int|约定的时间|是
remark|string|留言 |否

### 返回数据
字段  |类型| 说明
------------|------ | -------------
price[\*]\-> comfortable, economic, luxury |double|三种车型的报价

### 返回示例

```json
{
  "code": 0,
  "data": {
    "order": {
      "appointed_at": "2014-11-20 08:00:00",
      "create_at": "2014-11-17 19:59:36",
      "order_no": 141620106694195,
      "owner_id": 0,
      "owner_route_id": 0,
      "passenger": {
        "avatar": "",
        "company": "",
        "gender": "\u7537",
        "id": 1000000006,
        "industry": "",
        "last_name": "\u5b59",
        "mobile": "11652515135",
        "passenger_ride_count": 0,
        "passenger_score_total": 0
      },
      "passenger_route": {
        "distance": 10000,
        "from_addr": "",
        "from_lat": 0.0,
        "from_lng": 0.0,
        "from_poi": "P010A00CHR9",
        "route_id": 100000000600003,
        "start_time": "8:00:00",
        "to_addr": "",
        "to_lat": 0.0,
        "to_lng": 0.0,
        "to_poi": "P010A00CHR9"
      },
      "price": 0.0,
      "remark": "\u8f66",
      "state": 0,
      "update_at": "None",
      "vehicle_option": 7
    },
    "price": {
      "comfortable": 15.0,
      "economic": 10.0,
      "luxury": 20.0
    }
  },
  "msg": "\u64cd\u4f5c\u6210\u529f"
}
```

## 8 获取定单推送的状态
定单状态变化时，定单的详情数据也会发生变化  
    
### 请求方法
	GET /order/push_result/uid

### 请求参数
参数 |类型| 说明 | 必选 
-------|----- | ------------- | ------------
order_no |int | 定单号 | 是 

### 返回数据
字段  |类型| 说明
------------|------ | -------------
avg_waiting |int| 平均等待时间|
max_waiting |int| 最大等待时间|
order_no |int| 定单时间|
owner_num |int| 定单推送的车主数|

### 返回示例

```json
{
  "code": 0,
  "data": {
    "avg_waiting": 15,
    "max_waiting": 60,
    "order_no": 141530142493315,
    "owner_num": 10
  },
  "msg": "\u64cd\u4f5c\u6210\u529f"
}
```

## 9 乘客对超时定单的操作

### 请求方法
POST /order/timeout/uid

### 请求参数
参数 | 类型 | 说明 |必填
-----|------- | ------------- | ------------
order_no | int | 定单号 | 是 |
action | enum | 0:再等等 1:不再等待 | 是 |

### 返回数据
字段  |类型| 说明
------------|------ | -------------
order_no |int|定单号|

### 返回示例

```json
{
  "code": 0,
  "data": {
    "order_no": 141530142493315
  },
  "msg": "\u64cd\u4f5c\u6210\u529f"
}
```

## 10 乘客取消没人接的定单

### 请求方法
	POST /order/cancel/uid

### 请求参数
参数 | 类型 | 说明 |必填
-----|------- | ------------- | ------------
order_no | int | 定单号 | 是 |

### 返回数据
字段  |类型| 说明
------------|------ | -------------
order_no |int|定单号|

### 返回示例

```json
{
  "code": 0,
  "data": {
    "order_no": 141530142493315,
    "state": 3
  },
  "msg": "\u64cd\u4f5c\u6210\u529f"
}
```

## 11 车主接受定单
定单状态变化时，定单的详情数据也会发生变化  
    
### 请求方法
	POST /order/accept/uid

### 请求参数
参数 |类型| 说明 | 必选 
-------|----- | ------------- | ------------
order\_no |int | 定单号 | 是 
owner\_route\_id | int | 车主的路线|是|

### 返回数据
字段  |类型| 说明
------------|------ | -------------
order |dict|定单详情，具体字段含义见 /order/list|

### 返回示例
```json
{
  "code": 0,
  "data": {
    "order": {
      "appointed_at": "2014-11-20 08:00:00",
      "create_at": "2014-11-07 18:34:43",
      "order_no": 141530142493315,
      "owner": {
        "avatar": "/uploads/avatar/1000000001_82e8dd0072654772.JPG",
        "company": "\u535a\u6d3e\u901a\u8fbe\u901f\u9012",
        "gender": "\u7537",
        "id": 1000000001,
        "industry": "\u4e92\u8054\u7684\u7f51",
        "last_name": "\u5f20",
        "mobile": "15652515130",
        "owner_ride_count": 2,
        "owner_score_total": 9
      },
      "owner_route": {
        "distance": 10,
        "from_addr": "\u4e0a\u5730",
        "from_lat": 0.0,
        "from_lng": 0.0,
        "from_poi": "P010A00CHR9",
        "level": "\u7ecf\u6d4e\u578b",
        "route_id": 100000000100001,
        "start_time": "8:00:00",
        "to_addr": "\u4e2d\u5173\u6751",
        "to_lat": 0.0,
        "to_lng": 0.0,
        "to_poi": "P010A00CHR9"
      },
      "passenger": {
        "avatar": "",
        "company": "",
        "gender": "\u7537",
        "id": 1000000006,
        "industry": "",
        "last_name": "\u5b59",
        "mobile": "11652515135",
        "passenger_ride_count": 0,
        "passenger_score_total": 0
      },
      "passenger_route": {
        "distance": 2001,
        "from_addr": "\u4e2d\u5173\u6751",
        "from_lat": 0.0,
        "from_lng": 0.0,
        "from_poi": "",
        "route_id": 100000000600002,
        "start_time": "18:00:00",
        "to_addr": "\u4e0a\u5730\u4e09\u8857",
        "to_lat": 0.0,
        "to_lng": 0.0,
        "to_poi": ""
      },
      "price": 7.0,
      "remark": "\u8f66\u8981\u5e72\u51c0",
      "state": 4,
      "update_at": "2014-11-17 12:05:50",
      "vehicle_option": 7
    }
  },
  "msg": "\u64cd\u4f5c\u6210\u529f"
}
```

## 12 车主忽略定单
定单状态变化时，定单的详情数据也会发生变化  
    
### 请求方法
	POST /order/ignore/uid

### 请求参数
参数 |类型| 说明 | 必选 
-------|----- | ------------- | ------------
order\_no |int | 定单号 | 是 

### 返回示例
```jaon
{
  "code": 0,
  "data": {
    "order_no": 141530142493315,
    "state": 1
  },
  "msg": "\u64cd\u4f5c\u6210\u529f"
}
```


## 13 获取指定定单详情
定单状态变化时，定单的详情数据也会发生变化  
    
### 请求方法
	GET /order/detail/uid

### 请求参数
参数 |类型| 说明 | 必选 
-------|----- | ------------- | ------------
order_no |int | 定单号 | 是 

### 返回数据
字段  |类型| 说明
------------|------ | -------------
order |dict|定单详情，具体字段含义见 /order/list|

### 返回示例

```json
{
  "code": 0,
  "data": {
    "order": {
      "appointed_at": "2014-11-20 08:00:00",
      "create_at": "2014-11-07 18:34:43",
      "order_no": 141530142493315,
      "owner": {
        "avatar": "/uploads/avatar/1000000001_82e8dd0072654772.JPG",
        "company": "\u535a\u6d3e\u901a\u8fbe\u901f\u9012",
        "gender": "\u7537",
        "id": 1000000001,
        "industry": "\u4e92\u8054\u7684\u7f51",
        "last_name": "\u5f20",
        "mobile": "15652515130",
        "owner_ride_count": 2,
        "owner_score_total": 9
      },
      "owner_route": {
        "distance": 0,
        "from_addr": "",
        "from_lat": 0.0,
        "from_lng": 0.0,
        "from_poi": "P010A00CHR9",
        "route_id": 100000000100001,
        "start_time": "9:00:00",
        "to_addr": "",
        "to_lat": 0.0,
        "to_lng": 0.0,
        "to_poi": "P010A00CHR9"
      },
      "passenger": {
        "avatar": "",
        "company": "",
        "gender": "\u7537",
        "id": 1000000006,
        "industry": "",
        "last_name": "\u5b59",
        "mobile": "11652515135",
        "passenger_ride_count": 0,
        "passenger_score_total": 0
      },
      "passenger_route": {
        "distance": 10000,
        "from_addr": "",
        "from_lat": 0.0,
        "from_lng": 0.0,
        "from_poi": "P010A00CHR9",
        "route_id": 100000000600002,
        "to_addr": "",
        "to_lat": 0.0,
        "to_lng": 0.0,
        "to_poi": "P010A00CHR9"
      },
      "price": 7.0,
      "remark": "\u8f66\u8981\u5e72\u51c0",
      "state": 3,
      "update_at": "2014-12-26 11:35:25",
      "vehicle": {
        "brand": "\u5225\u514b",
        "certificate": "/uploads/certificate/100000000100001_c5424d63556c0032.JPG",
        "color": "\u7ea2\u8272",
        "level": "\u7ecf\u6d4e\u578b",
        "license_plate": "\u4eacA10001",
        "vehicle_45": "/uploads/vehicle_45/100000000100001_46f463ebb1e73a0c.JPG",
        "vehicle_front": "/uploads/vehicle_front/100000000100001_f57f2a4f0c7509a0.JPG",
        "vehicle_id": 100000000100001
      },
      "vehicle_option": 7
    }
  },
  "msg": "\u64cd\u4f5c\u6210\u529f"
}
```

## 14. 接单后乘客或车主取消定单

### 请求方法
POST /order/refuse/uid

### 请求参数
参数 | 类型 | 说明 |必填
-----|------- | ------------- | ------------
order_no | int | 定单号 | 是 |
msg|string|留言| 否

### 返回数据
字段  |类型| 说明
------------|------ | -------------
order_no |int|定单号|

### 返回示例

```json
{
  "code": 0,
  "data": {
    "order_no": 141530142493315,
    "state": 8
  },
  "msg": "\u64cd\u4f5c\u6210\u529f"
}
```

## 15 乘客支付

### 请求方法
	POST /order/pay/uid

### 请求参数
参数 | 类型 | 说明 |必填
-----|------- | ------------- | ------------
order_no | int | 定单号 | 是 |

### 返回数据
字段  |类型| 说明
------------|------ | -------------
order_no |int|定单号|

### 返回示例

```json
{
  "code": 0,
  "data": {
    "order_no": 141530142493315
  },
  "msg": "\u64cd\u4f5c\u6210\u529f"
}
```


## 16. 乘客确认搭乘
### 请求方法
	POST /order/confirm/uid

### 请求参数
参数 | 类型 | 说明 |必填
-----|------- | ------------- | ------------
order_no | int | 定单号 | 是 |

### 返回数据
字段  |类型| 说明
------------|------ | -------------
order_no |int|定单号|

### 返回示例

```json
{
  "code": 0,
  "data": {
    "order_no": 141530142493315,
    "state": 5
  },
  "msg": "\u64cd\u4f5c\u6210\u529f"
}
```

## 17. 评价列表
参考原型：3-2-3_乘客对车主的评论

### 请求方法
    GET /comment/list/uid
    
### 请求参数
参数 | 类型 | 说明 |必填
-----|------- | ------------- | ------------
target_uid | int | 要查看的uid | 是 |   
role|enum|角色|是
start_time|int|开始时间戳| 是
end_time|int|结束时间戳| 是

### 返回参数说明
参数字段 | 类型|说明
------------ | -------------| -------------
uid | int|用户id
list|array|评论列表
star|int|总体评价星级
comment_account|int|评论人数
item_1|obj|约定时间地点见面
item_2|obj|路线是否熟悉
item_3|obj|车内是否整洁
comment_id|int|评论id
comment_time|string|评论时间
order_no|string|订单号
comment|string|评论内容

### 返回示例

```json
{
  "code": 0,
  "data": {
    "list": [
      {
        "comment": "hehe",
        "create_at": "2014-11-13 15:55:20",
        "item0": 1,
        "item1": 0,
        "item2": 2,
        "order_no": 141530142493315,
        "owner_id": "1000000001",
        "passenger": {
          "gender": "\u7537",
          "id": 1000000006,
          "last_name": "\u5b59"
        },
        "star": 3
      },
      ...
    ],
    "next_page": 0
  },
  "msg": "\u64cd\u4f5c\u6210\u529f"
}
```

## 18. 评价对方

### 请求方法
    POST /comment/submit/uid
    
### 请求参数
参数 | 类型 | 说明 |必填
-----|------- | ------------- | ------------
order_no | int | 定单号 | 是 |   
star|int|分数|是
items|string|三项评价以'-'拼接例如1-0-1| 乘客评价车主时才有
comment|string|评论| 是

### 返回参数说明
参数字段 | 类型|说明
------------ | -------------| -------------

### 返回示例
```json
{
  "code": 0,
  "data": {},
  "msg": "\u64cd\u4f5c\u6210\u529f"
}
```

## 19. 评价统计

### 请求方法
    GET /comment/stat/uid
    
### 请求参数
参数 | 类型 | 说明 |必填
-----|------- | ------------- | ------------
role|enum| 角色 | 是

### 返回参数说明
参数字段 | 类型|说明
------------ | -------------| -------------
count| int | 总评价数
item\*| 3 | 某项评价的的数量
star |3.6667 | 分数

### 返回示例
```json
{
  "code": 0,
  "data": {
    "count": 3,
    "item0": 1,
    "item1": 1,
    "item2": 0,
    "star": 3.6667
  },
  "msg": "\u64cd\u4f5c\u6210\u529f"
}
```

## 20. 路线发布
参考原型：3-2-4_已发布路线

### 请求方法
   POST /route/publish/uid

### 请求参数
参数  |类型| 说明 | 必选
------------|------ | ------------- | ------------
home\_poi |string| 小区地址 |是
office\_poi |string | 工作地址 | 是
leave\_office\_time |string| 时:分 | 是
leave\_home\_time |string| 时:分 | 是
action |enum |0上下班都不接单 1上班接单 2下班接单 3上下班都接单| 是

### 返回数据
返回新发布的路线

### 返回示例
```json
{
  "code": 0,
  "data": {
    "list": [
      {
        "\u4e0a\u73ed": {
          "create_at": "2014-12-26 11:29:56",
          "distance": 0,
          "from_addr": "",
          "from_lat": 0.0,
          "from_lng": 0.0,
          "from_poi": "P010A00CHR9",
          "price": 0.0,
          "route_id": 100000000100037,
          "start_time": "09:00:00",
          "status": 1,
          "to_addr": "",
          "to_lat": 0.0,
          "to_lng": 0.0,
          "to_poi": "P010A00CHR9",
          "type": "\u4e0a\u73ed",
          "uid": 1000000001,
          "vehicle_id": 100000000100001
        },
        "\u4e0b\u73ed": {
          "create_at": "2014-12-26 11:29:56",
          "distance": 0,
          "from_addr": "",
          "from_lat": 0.0,
          "from_lng": 0.0,
          "from_poi": "P010A00CHR9",
          "price": 0.0,
          "route_id": 100000000100038,
          "start_time": "19:00:00",
          "status": 1,
          "to_addr": "",
          "to_lat": 0.0,
          "to_lng": 0.0,
          "to_poi": "P010A00CHR9",
          "type": "\u4e0b\u73ed",
          "uid": 1000000001,
          "vehicle_id": 100000000100001
        }
      }
    ]
  },
  "msg": "\u64cd\u4f5c\u6210\u529f"
}
```

## 21. 路线开关
参考原型：3-2-4_已发布路线

### 请求方法
   post /route/switch/uid

### 请求参数
参数  |类型| 说明 | 必选
------------|------ | ------------- | ------------
route_id|int| 路线ID|是
status|int|0关 1开|是

### 返回数据
JSON示例

```json
{
  "code": 0,
  "data": {
    "route_id": 100000000100022,
    "status": 0
  },
  "msg": "\u64cd\u4f5c\u6210\u529f"
}
```

## 22. 路线列表
参考原型：3-2-4_已发布路线

### 请求方法
	GET /route/list/uid

### 请求参数
参数  |类型 |说明 |必选
------|------- | ------------- | ------------
role|enum|0乘客，1车主|Y
start_time| int| 开始时间戳 | N  
end_time|int| 结束时间戳| N

### 返回数据

```json
{
  "code": 0,
  "data": {
    "list": [
      {
        "\u4e0a\u73ed": {
          "create_at": "2014-12-25 19:05:34",
          "deleted": 0,
          "distance": 0,
          "from_addr": "",
          "from_lat": 0.0,
          "from_lng": 0.0,
          "from_poi": "P010A00CHR9",
          "price": 0.0,
          "route_id": 100000000100021,
          "start_time": "09:00:00",
          "status": 1,
          "to_addr": "",
          "to_lat": 0.0,
          "to_lng": 0.0,
          "to_poi": "P010A00CHR9",
          "type": "\u4e0a\u73ed",
          "uid": 1000000001,
          "vehicle_id": 100000000100001
        },
        "\u4e0b\u73ed": {
          "create_at": "2014-12-25 19:05:34",
          "deleted": 0,
          "distance": 0,
          "from_addr": "",
          "from_lat": 0.0,
          "from_lng": 0.0,
          "from_poi": "P010A00CHR9",
          "price": 0.0,
          "route_id": 100000000100022,
          "start_time": "19:00:00",
          "status": 1,
          "to_addr": "",
          "to_lat": 0.0,
          "to_lng": 0.0,
          "to_poi": "P010A00CHR9",
          "type": "\u4e0b\u73ed",
          "uid": 1000000001,
          "vehicle_id": 100000000100001
        }
      },
      ...
    ],
    "next_page": 0
  },
  "msg": "\u64cd\u4f5c\u6210\u529f"
}
```

## 23. 获取我的优惠券列表
参考资料：4-3_个人资料页

### 请求方法
	GET /coupon/list/uid

### 请求参数
参数 | 必选 | 说明
------------ | ------------- | ------------
start_time|int|开始时间戳| 是
end_time|int|结束时间戳| 是

### 返回数据
JSON示例

```json
{
  "code": 0,
  "data": {
    "list": [
      {
        "change_at": "2014-11-14 16:55:53",
        "code": "123123",
        "create_at": "2014-11-05 08:10:00",
        "expire_at": "2014-12-14 16:29:46",
        "id": 1,
        "name": "5\u5143\u4f18\u60e0\u5377",
        "status": 1,
        "uid": 1000000001,
        "value": 5
      },
    ...
    ],
    "next_page": 0
  },
  "msg": "\u64cd\u4f5c\u6210\u529f"
}
```

### 返回参数说明
参数字段 | 类型|说明
------------ | -------------| -------------
uid | int|用户id
expire_time|int|过期时间
coupon_name|int|券名称
coupon_id|int|券id
status|int|是否已兑换  0 未兑换 1 已兑换 2已使用


## 24. 兑换优惠券
参考资料：4-3_个人资料页

### 请求方法
	POST coupon/change/uid

### 请求参数
参数 | 说明 | 必选 
------------ | ------------- | ------------
coupon_id| coupon ID| 是
code| 兑换码 | 是 

### 返回数据
JSON示例

```json
{
  "code": 0,
  "data": {},
  "msg": "\u64cd\u4f5c\u6210\u529f"
}
```

## 25. 上传头像/驾照/行驶本
参考原型：3-2-4_已发布路线

### 请求方法
    POST /user/upload/uid

### 请求参数
参数 | 类型 | 说明 | 必选
------------ | ------------- | -------|-----
|avatar |file|头像|否
|license|file|驾驶证 |否
|certificate|file|行驶证|否


### 注意事项
请求必须用POST方式提交，并且注意采用multipart/form-data编码方式；

### 返回参数说明
参数字段 | 类型|说明
------------ | -------------| -------------
uid | int|用户id
avatar |string|用户头像图片地址
license |string|驾驶证图片地址
certificate |string|行驶证图片地址

### 返回示例
```json
{
  "code": 0,
  "data": {
    "avatar": "/uploads/avatar/1000000001_82e8dd0072654772.JPG",
    "certificate": "/uploads/certificate/100000000100001_c5424d63556c0032.JPG",
    "license": "/uploads/license/1000000001_e8ee3de9d8175c0a.JPG"
  },
  "msg": "\u64cd\u4f5c\u6210\u529f"
}
```


## 26. 修改用户信息
参考原型：3-2-4_已发布路线

### 请求方法
    POST /user/update/uid

### 请求参数
参数 | 类型 | 说明 | 必选
------------ | ------------- | -------|-----
last_name|string| 姓 | 否
company|string|公司| 否
industry|string|行业| 否
gender|enum| 男/女|  否

### 注意事项


### 返回示例

```json
 {
  "code": 0,
  "data": {},
  "msg": "\u64cd\u4f5c\u6210\u529f"
}

```

## 27. 修改车辆信息
参考原型：3-2-4_已发布路线

### 请求方法
    POST /vehicle/update/uid

### 请求参数
参数 | 类型 | 说明 | 必选
------------ | ------------- | -------|-----
brand|string| 品牌 | 否
color|string|红色| 否
license_plate|string|车牌号| 否

### 注意事项


### 返回示例

```json
 {
  "code": 0,
  "data": {},
  "msg": "\u64cd\u4f5c\u6210\u529f"
}

```

## 28. 上传车辆照片
参考原型：3-2-4_已发布路线

### 请求方法
    POST /vehicle/upload/uid

### 请求参数
参数 | 类型 | 说明 | 必选
------------ | ------------- | -------|-----
|vehicle_45 |file|车辆45度|否
|vehicle_front |file|车辆正面照片 |否

### 注意事项
请求必须用POST方式提交，并且注意采用multipart/form-data编码方式；

### 返回参数说明
参数字段 | 类型|说明
------------ | -------------| -------------
vehicle_45 |string|车辆45度照片地址
vehicle_front |string|车辆正面照片地址

### 返回示例
```json
{
  "code": 0,
  "data": {
    "vehicle_45": "/uploads/vehicle_45/100000000200001_883a52ade0447ed0.JPG",
    "vehicle_front": "/uploads/vehicle_front/100000000200001_07bff11799d34c82.JPG"
  },
  "msg": "\u64cd\u4f5c\u6210\u529f"
}
```


## 29. 获取计费详情  以下接口未完成
参考原型：3-3-4计费详情

### 请求方法
    GET /sfche/cost_list

### 返回数据
JSON示例

```json
[
    {
        "mile": "0-5km",
        "jj": "15元",
        "ss": "20元",
        "hh": "25元"
    },
    {
        "mile": "5-10km",
        "jj": "20元",
        "ss": "25元",
        "hh": "30元"
    }
]
```


## 30. 提现
参考资料：4-3_个人资料页

### 请求方法
POST /sfche/withdraw

### 请求参数
参数 | 必选 | 说明
------------ | ------------- | ------------
uid| Y  | 车主uid
tx_type|Y|提现类型 1支付宝 2银行卡
zfb_account|N|支付宝账号 提现类型为支付宝时
zfb_name|N|支付宝姓名 提现类型为支付宝时
bank_name|N|银行开户行 提现类型为银行时
bank_card_no|N|银行卡 提现类型为银行时
bank_user_name|N|用户名 提现类型为银行时
sessid|Y|session id

### 返回数据

JSON示例

```json
{
    "error_code": "00",
    "error_msg": "ok"
}
```


## 31. 获取协议
### 请求方法
GET /sfche/protocol/{id}

* 1:注册协议 2搭乘条例 3 三方协议 4计费表 5保险协议




## 32. 后台cron程序
说明 | 规则 | 命名
------------ | ------------- | ------------
检测订单置为过期| * * * * *   | check_expire_order
检测支付订单置为过期| * * * * *   | check_expire_pay_order
检测队列，匹配路线| * * * * *   | queue_make_route

## 错误代码说明
返回格式 JSON：

```json
{
    "error_code": "01",
    "error_msg": "系统异常"
}

```

错误码 | 含义 | 话术
-------|-------|-------
1001|系统异常|对不起系统忙请稍后
1002|短信发送失败|短信发送失败
1003|验证码校验失败|验证码校验失败
1004|手机号码错误|手机号码错误
2001|定单已失效|
