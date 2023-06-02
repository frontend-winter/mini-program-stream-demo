<template>
	<view class="content">
		<image class="logo" src="/static/logo.png"></image>
		<view class="text-area">
			<text class="title">{{title}}</text>
		</view>
		<button @click="test">
			你好
		</button>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				title: 'Hello World'
			}
		},
		onLoad() {

		},
		onShow() {
			this.test()
		},
		methods: {
			test() {
				let _this = this;
				// let token = uni.getStorageSync('token');

				const url = "https://hzdjs.cn/chat/chat-process";

				// const url = "https://chat.xyhelper.com.cn/api/chat-process";
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
						// authorization: 'Bearer 878298',
						// "accept-language": "zh-CN,zh;q=0.9",
						Authorization: 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc1JlZnJlc2giOmZhbHNlLCJyb2xlSWRzIjpbIjE1Il0sInVzZXJuYW1lIjoiMTU5Nzk4MDI2MzgiLCJ1c2VySWQiOjMyLCJwYXNzd29yZFZlcnNpb24iOjEyLCJjcmVhdGVUaW1lIjoiMjAyMy0wNC0xMiAxNzowNDo0NyIsImhlYWRJbWciOiIiLCJleHBpcmF0aW9uVGltZSI6IjIwMjMtMDYtMTUgMTI6MDA6MDAiLCJ1c2VyQ2hhdFR5cGUiOiIyIiwidXNlckNoYXRUb2tlbiI6IllrRXhldWFnUUYiLCJpYXQiOjE2ODU2ODY5NjAsImV4cCI6MTY4NTc3MzM2MH0.04UiZdiEmAe9njZt7yC1eqVh5xrK-3IZabjfmpwiju8'
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
			}
		}
	}
</script>

<style>
	.content {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
	}

	.logo {
		height: 200rpx;
		width: 200rpx;
		margin-top: 200rpx;
		margin-left: auto;
		margin-right: auto;
		margin-bottom: 50rpx;
	}

	.text-area {
		display: flex;
		justify-content: center;
	}

	.title {
		font-size: 36rpx;
		color: #8f8f94;
	}
</style>