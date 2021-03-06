# 课题背景
![image](https://user-images.githubusercontent.com/13820586/169219680-b69ad08d-4aac-426b-856f-a88bae284664.png)

虚拟人制作过程，通过**动作捕捉**技术，获取人物姿态，进而将任务姿态作为**关键帧**插入到动画中，是业界常见的制作方式。
通过动捕数据生成虚拟人姿态的技术，在一定程度上减轻了动画师的设计工作量。由于姿态是从真人演示中提取得到的，姿态均符合真实物理世界的运行规律，该技术能大幅提升虚拟人的**逼真程度**。

但是传统的动捕设备如**惯性传感器（IMU）**，需要真人在**每个关节**穿戴对应设备，在日常生活中难以推广。所以我们转向从**稀疏的惯性传感器**获取数据，期望通过少量的设备收集到的数据，预测人体全身的运动情况。

![image](https://user-images.githubusercontent.com/13820586/169219544-ad6bdfbb-8742-4abf-afa4-8383da699b76.png)


# 论文解读TransPose: Real-time 3D Human Translation and Pose Estimation with Six Inertial Sensors
## 论文目标
- 论文解决的问题：通过**稀疏惯性传感器**传入的信息，预测人体全部关节的**运动姿态**（关节旋转角和全局平移量）。
- 现有方法存在的问题：  
    a. 信号稀疏 —— 从**局部**信息预测**全局**信息存在难点；  
    b. 传感器输入信息**噪声**较大 —— 对预测结果易产生干扰。  
    
![Live Demo 1](1.gif)![Live Demo 2](2.gif)  

关键点：稀疏惯性传感器，实时性

## 论文方法  
- 方法：基于RNN构建神经网络，仅从6个惯性传感器(IMU)实现全身完整的运动捕捉，速度超过90帧/秒。
- 具体细节：对于身体姿态的估计，使用一个多阶段的网络，输入6个IMU数据，预测全身关节点的位置。为获得稳定的全局平移量，论文还提出一种基于支持脚的平滑方法，通过线性插值估计人体位移速度。  
![image](https://user-images.githubusercontent.com/13820586/169221973-d5daad97-109e-4806-ad46-8134e10aeaf6.png)

- 算法流程如下：
![image](https://user-images.githubusercontent.com/13820586/169199173-c6c382cf-d9d8-4723-95a0-ee1ef11b8ad0.png)

- **输入**：
![image](https://user-images.githubusercontent.com/13820586/169222182-1ba8dee3-69c2-4707-b57f-8d27eb427fcb.png)

- **Loss Function**：  
1. 预测旋转角theta  
![image](https://user-images.githubusercontent.com/13820586/169222314-897add58-e5b8-438f-aaff-e05b2e567a27.png)

![image](https://user-images.githubusercontent.com/13820586/169222385-1e25b072-66ff-4ec4-a941-92bd05b68830.png)

![image](https://user-images.githubusercontent.com/13820586/169222483-64a7046d-47fa-4fe4-988b-0e2a11d4f8e2.png)

2. 预测全局平移速度v：  
计算支撑脚速度：  
![image](https://user-images.githubusercontent.com/13820586/169222771-bdf246c1-4378-4dd7-8204-20ae99c65a2c.png)

网络预测根关节的速度：  
![image](https://user-images.githubusercontent.com/13820586/169223329-3ec87c72-9fd3-48ae-9dd4-19731e350832.png)

对两个速度进行线性插值：  
![image](https://user-images.githubusercontent.com/13820586/169223457-4c622c69-0cec-425a-b9d9-0b1c7ad57f2c.png)


![image](https://user-images.githubusercontent.com/13820586/169231559-480b0526-9087-4154-b9e3-2806f00137bd.png)
![企业微信截图_16529442521427](https://user-images.githubusercontent.com/13820586/169232066-855482e5-e65d-4ec5-ae65-b725a74d9f16.png)

## 论文结论
本文提出了一种基于6个惯性传感器的90帧/秒运动捕捉技术，该技术可重建完整的人体运动，包括身体姿态和全局平移等信息。

- **优点：实时性，稀疏性**。该算法不受环境设置(例如固定的摄像机)的限制，能够在较大范围的运动空间内采集动作数据（与光学预测相比），对于黑暗、户外以及遮挡的情况，也能准确还原人体运动信息。
- **不足**：  
    1. 硬件方面。
       1. 惯性传感器是通过磁力计来测量方向，易受到环境磁场的影响。因此，它不能在磁场变化强烈的环境中工作。
       2. 另外传感器是通过无线传输将信号传送到笔记本电脑，传输距离有限，传感器不能距离笔记本电脑太远(论文提到最多10米)。
    2. 软件方面。
       1. 网络存在泛化问题，不能处理与训练数据集大不相同的动作，例如劈叉和其他复杂动作。
       2. 基于支撑脚的优化方法是基于支撑脚在世界坐标空间中没有运动的假设，这对于像滑冰和滑动这样的运动是不成立的，又或者人体在转呼啦圈之类的场景也会失效。



# 论文解读Physical Inertial Poser (PIP): Physics-aware Real-time Human Motion Tracking from Sparse Inertial Sensors

## 论文目标
- 论文解决的问题：通过**稀疏**惯性传感器传入的信息，预测人体全部关节的**运动姿态（旋转角）和全局平移**。
- 现有方法存在的问题：  
    a. 信号稀疏——从**局部**信息预测**全局**信息存在难点；  
    b. 传感器输入信息**噪声**较大——对预测结果易产生干扰。   

## 论文方法  
- **方法**：输入6个惯性传感器(IMU)的动捕信息，通过基于RNN的神经网络和**物理优化处理**，预测全身完整的运动状态。
- **具体细节**：算法包含两个阶段:1)基于网络的运动参数估计和 2)基于物理的运动优化，它在运动捕捉中利用了人体运动学和动力学。
![image](https://user-images.githubusercontent.com/13820586/169223882-9129229f-0be7-442a-9044-dc5f8485f6fd.png)


- **关键算法**：
  1. 提出了一个双PD控制器，它包括1)一个控制关节旋转空间局部姿态的旋转控制器和 2)一个控制关节位置空间全局姿态的附加位置控制器。  

![image](https://user-images.githubusercontent.com/13820586/169224569-77067639-abe1-46e5-bc69-325aa5d3960a.png)

2. 提出了初始化RNN参数的方法，使用一个FCN从人体姿态输出RNN的初始化参数


项目地址：https://xinyu-yi.github.io/PIP/

# 总结
|     | TransPose  | PIP  |
|  ----  | ----  | ----  |
| 方法  | 纯神经网络 | 神经网络 + 物理优化 |
| 关键点  | 采用支撑脚的全局速度优化人体的全局速度 | 使用两个PD控制器约束全局位姿，而仍然保持问题的二次性 |
| 优点  | 不受遮挡和低质量光线情况的影响，实时 | 姿态自然稳定，实时 |
| 缺点  | 无法预测物体滑动状态下的姿态 | 无法预测上楼梯等姿态 |








