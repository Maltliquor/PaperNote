BY 叶工

# BVH文件相关的说明
## 文件结构
BVH文件分成两部分：
- 骨骼模型部分：描述骨骼模型的层次关系，结点位置信息，但无法记录骨骼的初始旋转姿态，或者说骨骼的初始姿态只能隐含在偏移量当中。
- 动作数据部分：一般负责记录上面的模型的关节姿态，根结点一般会另外记录位置信息，其它结点因为可以推算出来所以不记录位置信息。具体记录的数据顺序和含义需要根据骨骼模型的结点顺序和通道来定。

BVH文件中关键词、数字、名称、枚举值之间用空格分隔，缩进一般使用制表符，也可以不缩进。

## 骨骼模型部分
骨骼模型部分以关键词“HIERARCHY”为开头。

接着另起一行，开头为“ROOT”关键词，后面跟着骨骼根结点的名称，结点的内容包含在一对大括号内，为方便阅读，大括号内最好缩进一个制表符。除根结点外，其它结点使用“JOINT”作为关键词，除关键词不同外，所有结点的定义方式相同。

一个结点内部可能会有四种类型的信息：
- 偏移量：关键词“OFFSET”
- 通道：关键词“CHANNELS”
- 子结点：关键词“JOINT”
- 叶结点：关键词“End Site”

偏移量：在关键词“OFFSET”后紧跟着三个数字分别表示x、y、z方向上的偏移量，一个结点的偏移量表示一根骨骼的**起始点**到父结点坐标系原点的偏移量。或者说，这是一根骨骼起始点在父结点的局部坐标系下的坐标值。

通道：关键词“CHANNELS”后紧跟着一个整数表示通道数，然后是按顺序列出各通道类型。一共有6种通道类型分别为：Xposition、Yposition、Zposition、Xrotation、Yrotation、Zrotation。顾名思义，带position的通道类型表示坐标值，带rotation的表示旋转角度（欧拉角）。也就是说，结点的姿态用欧拉角表示，而且按这里的通道顺序进行旋转，单位为度。这个欧拉角是在局部坐标系下的，也就是相对于父结点坐标系的旋转。

子结点：子结点以关键词“JOINT”为开头，子结点也是一个结点，按结点的方式定义，所以说骨骼模型的定义是递归的或者是层次的。子结点可以有多个。

叶结点：以关键词“End Site”开头，后面是一对大括号，大括号内只能定义偏移量。叶结点的偏移量是其父结点的骨骼末端的坐标值。叶结点只能有一个。

## 动作数据部分
动作数据部分以关键词“MOTION”开头。第二行开头为“Frames: ”，然后是动作数据的帧数N（文件中的数据行数），但是Blender导入时并不会理会这个值。第三行开头为“Frame Time: ”，然后是帧的时间间隔，单位为秒，这个值也不会影响Blender的动画播放速度。然后是N行的数据，一行为一帧的数据，数据按前面模型数据的结点和通道排列。例如第一个结点有6个通道，那么前6个数字就分别表示这6个通道的值，第7个数字就是下一个结点第1个通道的值，依此类推。

## 坐标系
BVH文件以Y轴为向上的坐标轴，右手坐标系。Blender导入时会进行坐标轴变换，变换关系如下图。其实Blender导入BVH文件时可以进行坐标系变换的配置，前面的是一个默认的配置。
![](/pictures/coordinate_system.png)

## 测试文件
[skeleton-test.bvh](/glove-dll/skeleton-test.bvh)文件中构造了一个只有两个结点的模型和一些简单的旋转，用于帮助理解BVH文件数据的含义。[bvh_config-test.txt](/glove-dll/bvh_config-test.txt)是配套的BVH文件配置文件，将两个传感器分别绑定到两个结点上，用于测试传感器的姿态是否能正确地映射到结点上，以及子结点的局部坐标系的欧拉角计算是否有误。

## 参考资料
- [似乎是一个课程中的对BVH文件的介绍，包含一个文件示例](https://research.cs.wisc.edu/graphics/Courses/cs-838-1999/Jeff/BVH.html)
- [人体动作捕捉格式BVH及其与三维坐标的转换](https://blog.csdn.net/hysterisis/article/details/108485054)
- [BVH文件格式解析](https://blog.csdn.net/u012336923/article/details/50972968)
- [BVH后缀文件格式解析](http://www.youizone.com/2015/06/04/bvh-files.html)
- [The 3dsMax-friendly BVH release of CMU's motion capture database](https://sites.google.com/a/cgspeed.com/cgspeed/motion-capture/the-3dsmax-friendly-bvh-release-of-cmus-motion-capture-database)
