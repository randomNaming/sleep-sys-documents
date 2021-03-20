## 演示
sleep项目会将各个版本的演示在此展示，目前演示的版本有H5，安卓，微信小程序，其他版本的演示将会陆续添加。


## 使用方法
H5版本可以用微信或者手机浏览器扫描二维码即可
微信小程序只能通过微信扫码查看
安卓版本只能在安卓使用，可以用安卓浏览器或者QQ扫码进行安装(微信中不能扫码安装)，安装过程中您可能需要勾选相应的提示，允许安装来自非应用市场的APP，或者您需要在`设置`中打开 [允许安装来自未知来源的应用](https://www.pc841.com/shoujijiqiao/45848.html)

## 说明

文档中也有相应的演示示例，但它是通过`iframe`嵌入到网页的，所以可能会造成某些uni-appAPI在网页上(浏览器F12手机调试模式无问题)无法使用，造成组件有BUG的错觉。

**注意**： 建议您只在浏览文档时使用文档右侧的演示功能，电脑示例由于分辨率和uni-app用`rpx`单位的问题，显示可能会不够细致，请通过微信扫码小程序体验最佳的效果。




## 扫码

<div style="display: flex">
	<div style="width: 280px" >
		<div class="item" ><img src="#" >
		<div class="platform-name" >
			微信小程序
			<p class="platform-tips" >只能微信扫码</p>
		</div>
		</div>
	</div> 
	<div style="width: 280px" >
		<div class="item" ><img src="http://81.69.99.37:9000/docs/app/H5.png" > 
			<div class="platform-name" >
				H5
				<p class="platform-tips" >微信或浏览器扫码</p>
			</div>
		</div>
	</div> 
	<div style="width: 280px" >
		<div class="item" ><img src="#" > 
			<div class="platform-name" >
				安卓
				<p class="platform-tips" >浏览器扫码安装(微信扫码无效)</p>
			</div>
		</div>
	</div>
</div>

<style>
	.item {
		    text-align: center;
		    border: 1px solid #eaeefb;
		    border-radius: 5px;
		    transition: bottom .4s;
		    position: relative;
		    bottom: 0;
		    margin: 1.5rem;
		    padding: 2rem 0;
	}
	
	.item img{
		width: 160px;
		margin: auto;
	}
	
	.item .platform-name {
		color: #606266;
		font-size: 18px;
		position: relative;
		margin: 20px 0;
	}
	
	.item .platform-tips {
		text-align: center;
		position: absolute;
		font-size: 14px;
		bottom: -50px;
		left: auto;
		width: 100%;
		right: auto;
		color: #909399;
	}
</style>