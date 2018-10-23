<script type="text/javascript" src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=default"></script>

# 直方图均衡化 #

## 1.直方图均衡化介绍 ##
原始图像灰度直方图分布从集中在某个灰度区间变成在全部灰度范围内的均匀分布，增加灰度的动态范围，从而提高图片对比度，达到图片增强的目的。直方图均衡化通过对图像进行非线性拉伸，重新分配图像像素值，使一定灰度范围内的像素数量大致相同。当图像直方图完全均匀分布的时候，此时图像的熵是最大的（随机变量每个值的概率都相同时，熵最大），图像对比度是最大的。像素变换函数如下：
$$y=f(x)=(L-1)\sum_{i=1}^{x_i}\frac{h(x_i)}{w{\times}h}$$
其中\\(h(x_i)\\)表示每个灰度级像素的个数，\\(L\\)为灰度等级，\\(w\\)和\\(h\\)分别表示图像的宽和高。

## 2.Python实现源码 ##
**彩色图像均衡化**：

	def zhifang_mean(image_1,cha=0):
		'''单通道直方图均衡化实现，参数cha为通道选择，image_1为图片'''
		rsum = 0		#用于像素值累加
		lis = np.zeros([256])		#直方图数组
		result = np.zeros([256])	#像素变换数组
		channel_1 = image_1[:,:,cha]		#获取对应通道图像
		channel1_c = np.zeros(channel_1.shape)		#均衡化图像初始化
		c1 = channel_1.shape[0]			#图像矩阵行数
		c2 = channel_1.shape[1]			#图像矩阵列数
	
		'''计算直方图数组'''
		for i in range(c1):		
			for j in range(c2):
				lis[channel_1[i,j]] += 1
	
		'''计算各像素值对应变换像素'''		
		for i in range(256):
			rsum += lis[i]
			temp = int(255*(rsum/(512*512)))
			result[i] = temp
	
		'''根据像素变换数组进行对应位置像素赋值'''	
		for i in range(c1):
			for j in range(c2):
				channel1_c[i,j] = result[channel_1[i,j]]
	
		'''返回均衡化结果'''
		return channel1_c
	
	def my_pic_mean(filename):
		'''实现多通道图片均衡化'''
		img1 = plt.imread(filename)
		img2 = np.zeros(img1.shape)
		for k in range(img1.shape[2]):
			img2[:,:,k] = zhifang_mean(img1, k)/255
		return img2

**灰度图像均衡化**：

	def zhifang_mean_one(image_1):
		'''单通道直方图均衡化实现，image_1为图片'''
		rsum = 0		#用于像素值累加
		lis = np.zeros([256])		#直方图数组
		result = np.zeros([256])	#像素变换数组
		channel_1 = image_1[:,:]		#获取对应通道图像
		channel1_c = np.zeros(channel_1.shape)		#均衡化图像初始化
		c1 = channel_1.shape[0]			#图像矩阵行数
		c2 = channel_1.shape[1]			#图像矩阵列数
	
		'''计算直方图数组'''
		for i in range(c1):		
			for j in range(c2):
				lis[channel_1[i,j]] += 1
	
		'''计算各像素值对应变换像素'''		
		for i in range(256):
			rsum += lis[i]
			temp = int(255*(rsum/(512*512)))
			result[i] = temp
	
		'''根据像素变换数组进行对应位置像素赋值'''	
		for i in range(c1):
			for j in range(c2):
				channel1_c[i,j] = result[channel_1[i,j]]
	
		'''返回均衡化结果'''
		return channel1_c

## 3.代码实现效果 ##
**彩色图像均衡化效果对比**：从均衡化前后的直方图分布可以看出，像素分布更加均匀了，可见效果达到。不过可以看到结果图像的色调发生了变换（变为冷色调），考虑到是因为直接通过矩阵堆叠组成彩色图像导致各通道像素颜色发生变化（矩阵显示彩色图像问题）。

![](https://i.imgur.com/7TwQSfO.png)

**灰度图像均衡化效果对比**：先将彩色图像转换为单通道灰度图像，处理后。可以看到，图片的部分区域亮度有所增加，对比度更高。观察直方图，可以看到直方图分布明显更加均匀，并且灰度范围扩充到了0和255附近的区域。

![](https://i.imgur.com/omrcrFY.png)

