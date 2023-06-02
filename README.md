# 在微信小程序中如何支持使用流模式（stream），打造ChatGPT实时回复机器人，详细讲解。


## 重点方法

```javascript
const requestTask = uni.request({
    url: url,
    timeout: 15000,
    responseType: 'text',
    method: 'POST',
    enableChunked: true,
    data: {
        "options": {
            "accessToken": "YkExeuagQF",
            "userChatType": "2",
            "type": "3",
            "model": "text-davinci-002-render-sha"
        },
        "prompt": "你是GPT吗"

        // "prompt": "你是谁",
        // "options": {
        // 	// "conversationId": "73da18f6-865d-46b9-80db-053b3ebd50d8",
        // 	// "parentMessageId": "3c6b49da-034c-48b6-9d6d-7b5ef4fc5694"
        // },
        // "systemMessage": "You are ChatGPT, a large language model trained by OpenAI. Follow the user's instructions carefully. Respond using markdown.",
        // "baseURI": "https://freechat.xyhelper.cn",
        // "accessToken": "y9dVb0i4os",
        // "model": "text-davinci-002-render-sha"
    },
    header: {
        'Content-Type': 'application/json;charset=UTF-8',

        // token
        Authorization: 'u8'
    },
    success: response => {
        // console.log(response)
    },
    fail: error => {}
})
requestTask.onHeadersReceived(function(res) {
    // console.log(res.header);
});
requestTask.onChunkReceived(function(response) {
    const arrayBuffer = response.data;
    const uint8Array = new Uint8Array(uint8Array);
    let dataText = uni.arrayBufferToBase64(arrayBuffer)
    // dataText = new Buffer(dataText, 'base64')
    dataText = Buffer.from(dataText, 'base64')
    // console.log('dataText', dataText);	
    dataText = dataText.toString('utf-8');
    const lastIndex = dataText.lastIndexOf(
        "\n",
        dataText.length - 2
    );
    let chunk = dataText;
    if (lastIndex !== -1) chunk = dataText.substring(lastIndex);
    console.log('chunk', chunk);
    let lastMessage = {}
    // console.log(typeof chunk, 'chunk.text');
    try{
        lastMessage = JSON.parse(chunk);
    }catch(e){
        console.log(e, 'eee');
    }
    _this.title = lastMessage.text
    console.log(lastMessage, 'lastMessage ');
})
```

## 注意事项：编码

```javascript
const arrayBuffer = response.data;
const uint8Array = new Uint8Array(arrayBuffer);
let text = uni.arrayBufferToBase64(uint8Array)
text = new Buffer(text, 'base64')
text = text.toString('utf8');
```


- 我这个方式比较简单粗暴，我在网上看到有人使用了第三方库，但是我测试下来行不通，就使用了uni官方这个转成Base64，然后再进行转码。

- 以上就是整个小程序的流式响应回复所需要用到的技术，也是最直接有效的方法，如果你现在掌握这门技术，再加上ChatGPT目前的势头，我相信你也能做出一些事情。

- 好了，就这样，做一个小小的记录，后期如果有空，我会继续分享我在开发ChatGPT产品的其他思路。