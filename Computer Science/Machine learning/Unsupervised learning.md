d1.clustering 聚类(K-means聚类算法)
 ![Exported image](Exported%20image%2020251007221952-0.png)

算法原理如上图：  
1.初始随机创建K个clusters种类；每个cluster由其中心坐标miu表示，其中miu的维度与sample xi的维度一致。计算samples中所有xi距离最近的cluster并统计，得到ci, c代表cluster种类，i代表sample索引，如此一来相当于将samples进行了一次clustering操作划分clusters;  
2.针对每个当前每个cluster中的xi,统计它们的坐标均值，作为新的本cluster的新miu(即pos),并基于1.内容重复操作；  
3.直到最后一次clustering操作的结果新miu与上一次操作一致，说明K-means聚类算法已“收敛”，聚类结束，过程如下图：

![Exported image](Exported%20image%2020251007221956-1.png)

伪代码如下：

![Exported image](Exported%20image%2020251007222004-2.png)   
实践：  
K-means可用于图像色彩丰富度压缩：

![Exported image](Exported%20image%2020251007222008-3.png)  

原始图像为：128X128Xpixels,其中每个pixel为RGB三通道，(a,b,c);其中每个维度23为256即8bits,原本的色彩种类有超过千种；original pic的storage size: 128 x 128 x (3x8bits) =393,216 bits;  
将原本img图像reshape为(128x128 , 3)的二维shape，并基于该数据进行K-means clustering，将colors保留为16种，结果图像storage : 16×24 + 128×128×4 = 65,920 bits ; 其中前一项记录色彩mapping记录(仍需要16中色彩，每种仍是RGB三通道24bits存储)；后一项是新的图片需要存储使用space（4是因为只有16种情况，4bits空间即可实现编码，最后根据编码内容映射到实际16中RGB colors）