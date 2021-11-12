<template>
	<view class="content">
<!-- 		<image class="logo" src="/static/logo.png"></image>
		<view class="text-area">
			<text class="title">{{title}}</text>
		</view> -->
		<uni-file-picker file-mediatype="video" return-type="object" @select="onSelectFile"></uni-file-picker>
		<text>{{mediainfo}}</text>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				title: 'Hello',
				mediainfo: {},
			}
		},
		onLoad() {

		},
		methods: {
			async onSelectFile(args) {
				let _this = this;
				
				// console.log(args)
				var file = args.tempFiles[0];
				var response = await fetch(file.path);
				var reader = response.body.getReader();
				
				var MP4Box = require('mp4box');
				var mp4boxfile = MP4Box.createFile();
				mp4boxfile.onReady = function(info) {
					console.log(info);
					
					_this.mediainfo = info
				};
				
				reader.read().then(function processData(res) {
					// console.log(res);
					
					if (res.value) {
						// console.log(res.value.buffer)
						res.value.buffer.fileStart = 0;
						mp4boxfile.appendBuffer(res.value.buffer);
					}
					if (!res.done) {
						return reader.read().then(processData);
					} else {
						mp4boxfile.flush();
					}
				});
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
