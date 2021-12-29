---
layout: post
title:  "微信小程序云开发订阅消息"
lang: cn
category: develop
tags: 微信, 小程序, 订阅消息, 云开发
comments: true
---
# 前提
1. 了解微信小程序开发基本流程

- [小程序前端代码](#小程序前端代码)
- [云函数代码](#云函数代码)
- [自己后台服务器](#自己后台服务器)

# 目的
主要介绍微信小程序开发中的消息订阅功能如何设计。

# 开发设计步骤

在取得用户同意以后在未来的时间不定时的推送一条小程序的消息到微信的“服务通知”。

1. 在小程序公众后台选择模版，虽然有两种类型的模版，但是长期订阅只针对特定企业开发。

        1. 一次性订阅模版✅: 用户点击同意一次，然后发送一次。
        2. 长期订阅模版❌：

2. 引导用户点击事件触发弹窗同意接受。

3. 后台服务器或者云开发业务逻辑完成以后给该用户发送消息通知。


# 小程序前端代码
<style>
  img[alt=howitwork] {
    width: 200px;
  }
</style>
![howitwork]({{"/wechat_miniprog/ui.gif" | prepend: site.image_root}})

小程序代码：

    // RECORD_NOTIFY_TEMP_ID 公众后台的temp_id
    const handleSwitchSubscribe = (isSubscribe) => {
      if (isSubscribe) {
        // Displaying '获取授权订阅消息...',
        ...
        wx.requestSubscribeMessage({
          tmplIds: [RECORD_NOTIFY_TEMP_ID],
          success: function (res) {
            if (res[RECORD_NOTIFY_TEMP_ID] === 'accept') {
              // Displaying '授权成功',
              ...
              onChange && onChange(true, name);
            } else {
              // Displaying '授权失败',
              ...
              onChange && onChange(false, name);
            }
          },
        });
      } else {
        onChange && onChange(isSubscribe, name);
      }
    };

# 云函数代码

添加云函数, 定期运行, 同时上传云函数和云函数触发器

     ── sendMsg 函数名称
        ├── config.json 触发器
        ├── index.js 函数主体
        ├── package-lock.json 依赖wx-server-sdk
        └── package.json 依赖wx-server-sdk

微信云函数代码,

  - [index.js](#indexjs)

  - [config.js](#configjs)

## index.js
    const cloud = require('wx-server-sdk');

    exports.main = async (event, context) => {
      cloud.init({
        env: cloud.DYNAMIC_CURRENT_ENV
      });
      const db = cloud.database();
      const date = new Date();
      // Today in YYYY-MM-DD format in UTC+8
      const todayDateString = new Date(date.getTime() - (date.getTimezoneOffset() * 60000 ))
                        .toISOString()
                        .split("T")[0];

      try {
        // 从云开发数据库中查询等待发送的消息列表
        // Record.notify: 是否需要发送
        // Record.notifyDate: 需要发送的日期，从前端获取，用户指定
        const records = await db
          .collection('Record')
          .where({
            notify: true,
            notifyDate: todayDateString
          })
          .get();
        if (records.data.length < 1) {
          return;
        }

        // 模版需要的参数，具体查看添加的模版，一定要注意文档空值的检查，有些参数不能是空的
        // 账单金额
        // amount10.DATA

        // 记账时间
        // date12.DATA

        // 账单类型
        // thing18.DATA

        // 签单人
        // thing13.DATA

        // 备注
        // thing9.DATA
        const messages = records.data.map(record => {
          const { client, issuedAt, value, direction, description, _openid } = record;
          const touser = _openid;
          const data = {
            amount10: {
              value: `${value}元`
            },
            date12: {
              value: issuedAt || '（空）',
            },
            thing18: {
              value: direction === 'in' ? '收款' : '付款',
            },
            thing13: {
              value: client.name || '(匿名)',
            },
            thing9: {
              value: description || '（空）'
            }
          };
          const page = 'index';
          const templateId = 'XopC76MJ2a3Y90KqXIswpcINsd7k4q6Dl-3WzArXjgg';
          return {
            touser,
            data,
            page,
            templateId
          };
        });

        // 循环发送消息
        const sendPromises = messages.map(async message => {
          try {
            // 发送订阅消息
            await cloud.openapi.subscribeMessage.send({
              touser: message.touser,
              page: message.page,
              data: message.data,
              templateId: message.templateId,
            });
          } catch (e) {
            return e;
          }
        });

        return Promise.all(sendPromises);
      } catch (err) {
        console.log(err);
        return err;
      }
    };

## config.js

定时触发器的添加

    {
      "triggers": [
        {
          "name": "sendMsgTimer",
          "type": "timer",
          "config": "0 30 10 * * * *"
        }
      ]
    }


# 自己后台服务器

通过HTTP调用微信服务接口，附带access_token

[微信官方文档](doc-url)


[doc-url]: https://developers.weixin.qq.com/miniprogram/dev/api-backend/open-api/subscribe-message/subscribeMessage.send.html
