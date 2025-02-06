# 使用Telegram订阅Pixiv新图的一站式解决方案

作为一名热爱艺术的创作者，时刻关注Pixiv上心仪画师的更新无疑是一种不可或缺的习惯。然而，每天手动检查新图不仅耗时，还容易遗漏佳作。那么，有没有一种更高效的方式来实时获取这些精美画作呢？

## RSS与Telegram的完美结合

首先，我想到了RSS订阅这一传统方式。通过RSSHub，我发现了一个可以直接订阅Pixiv画师新图的接口。虽然RSS订阅本身并不复杂，但单独使用RSS阅读器仍然不够便捷。于是，我决定将这些更新推送至更常用的通讯工具——Telegram。

### 从IFTTT到自建解决方案

最初，我尝试使用IFTTT将RSSHub生成的内容推送至Telegram频道。然而，随着IFTTT对非付费用户功能的限制，这个方案逐渐失效。作为一名开发者，我决定自己动手，打造一个功能类似的自定义工具。这不仅是一次技术挑战，更是一次提升开发能力的机会。

### 技术选型与开发思路

经过一番调研，我确定了基本思路：通过RSS采集数据，处理后通过Telegram Bot的API接口发送至指定频道。由于我习惯使用Node.JS进行开发，因此选择了Node.JS作为开发和部署平台。

## 具体实现步骤

### 1. RSS数据采集

为了避免手动解析XML文件的繁琐，我直接使用了`rss-parser`这个现成的组件。通过简单的安装和调用，我能够轻松获取RSS资讯并转化为JavaScript对象，方便后续处理。

bash
npm i rss-parser --save


### 2. 数据处理与图片提取

RSSHub生成的订阅内容包含了丰富的图片信息。通过正则表达式，我们可以轻松提取出图片的ArtworkID和PicID，从而构建出完整的图片地址。为了适应Telegram的图片大小限制，我选择了发送预览图，并提供完整图的下载链接。

javascript
const picIdReg = /https:\/\/pixiv\.cat\/(\d+)-?(\d+)?\.(jpg|png|gif)/gi;
const artworks = [...item.content.matchAll(picIdReg)];


### 3. 图片消息发送

通过Telegram的Bot API，我使用`sendPhoto`接口将处理后的图片及描述信息发送至指定频道。为了提升用户体验，我还添加了内联键盘按钮，用户可以一键跳转至原图页面或直接下载图片。

javascript
const apiBaseUrl = `https://api.telegram.org/bot${confData.bot.token}`;
got('sendPhoto', {
    method: 'POST',
    prefixUrl: apiBaseUrl,
    json: {
        chat_id: confData.bot.chat,
        photo: picItem.preview,
        caption: picItem.text,
        reply_markup: {
            inline_keyboard: [
                [{
                    text: '🌏',
                    url: picItem.url
                }, {
                    text: '⤵',
                    url: picItem.pic
                }]
            ]
        }
    }
});


### 4. 项目部署与运行

为了确保服务的稳定性，我使用`pm2`来管理项目的运行。只需简单的命令即可启动服务，并确保其在后台持续运行。

bash
pm2 start bot.js --name phandream


## 结语

通过这套方案，我们能够轻松实现Pixiv新图的自动推送，大大提升了日常浏览的便捷性。如果你也想体验这一功能，不妨试一试吧！

👉 [WildCard | 一分钟注册，轻松订阅海外线上服务](https://bbtdd.com/WildCard)