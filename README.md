# 小程序左滑删除思路
## html and css
- 首先每个list-item通过**z-index**分为上下两层，上面一层放置正常内容，下面一层放置左滑显示出的内容
- item的上层使用**绝对定位**，通过left的属性值来实现左右滑动

Here is the css code:

```
.inner{
    position: absolute;
    top:0;
}

.txt{
    background-color: #fff;
    width: 100%;
    z-index: 5;
    padding:0 10rpx;
    transition: left 0.2s ease-in-out;
    white-space: nowrap;
    overflow: hidden;
    text-overflow:ellipsis;
}

.del{
    background-color: #e64340;
    width: 180rpx;
	 text-align: center;
    z-index: 4;
    right: 0;
    color: #fff
}

```
## js
- 通过[微信小程序api](https://developers.weixin.qq.com/miniprogram/dev/framework/view/wxml/event.html)提供的touch对象和3个有关手指触摸的函数（touchstart，touchmove，touchend）来实现item随手指移动。

Here is the js code

```
touchS: function (e) {
	if (e.touches.length == 1) {
		this.setData({
			//设置触摸起始点水平方向位置
			startX: e.touches[0].clientX
		});
	}
},

touchM: function (e) {
	if (e.touches.length == 1) {
		//手指移动时水平方向位置
		var moveX = e.touches[0].clientX;
		//手指起始点位置与移动期间的差值
		var disX = this.data.startX - moveX;
		var delBtnWidth = this.data.delBtnWidth;
		var txtStyle = "";
		//如果移动距离小于等于0，文本层位置不变
		if (disX == 0 || disX < 0) { 
			txtStyle = "left:0px";
		//移动距离大于0，文本层left值等于手指移动距离
		} else if(disX > 0){
			txtStyle = "left:-" + disX + "px";
			//控制手指移动距离最大值为删除按钮的宽度
			if (disX >= delBtnWidth) {
				txtStyle = "left:-" + delBtnWidth + "px";
			}
		}
		//获取手指触摸的是哪一项
		var index = e.target.dataset.index;
		var list = this.data.list;
		list[index].txtStyle = txtStyle;
		//更新列表的状态
		this.setData({
			list: list
		});
	}
},

touchE: function (e) {
	if (e.changedTouches.length == 1) {
		//手指移动结束后水平位置
		var endX = e.changedTouches[0].clientX;
		//触摸开始与结束，手指移动的距离
		var disX = this.data.startX - endX;
		var delBtnWidth = this.data.delBtnWidth;
		//如果距离小于删除按钮的1/2，不显示删除按钮
		var txtStyle = disX > delBtnWidth / 2 ? "left:-" + delBtnWidth + "px" : "left:0px";
		//获取手指触摸的是哪一项
		var index = e.target.dataset.index;
		var list = this.data.list;
		list[index].txtStyle = txtStyle;
		//更新列表的状态
		this.setData({
			list: list
		});
	}
},

```

