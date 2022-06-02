# 骨架坐标系
![image](https://user-images.githubusercontent.com/13820586/169970707-b2d19473-0820-4180-8e9d-a9a2f85bad80.png)
与您在 3D 建模软件中创建的任何 3DCG 对象一样，每个骨骼都有一个由 X、Y 和 Z 轴定义的局部坐标系。

例如，我可以将骨骼定义为一个对象，其中从骨骼原点到其尖端的方向为 Z 轴正方向，骨骼顶部为 Y 轴正方向。

本质上，我创建了一个对象，如下所示。

我将尖端涂成蓝色以匹配 3D 建模程序的正 Z 轴，将顶部涂成绿色以匹配 3D 建模程序的正 Y 轴，并将一侧涂成红色以匹配 3D 建模程序的正 X 轴。

骨头的另一侧是洋红色，骨头的下面是黄色。

骨骼的局部坐标系 X、Y 和 Z 轴与 3D 建模程序的 X、Y 和 Z 轴对齐，并且局部坐标系被认为是附加到对象上的。

局部坐标系原点 (X=0,Y=0,Z=0) 附加到对象的原点，即对象在 (X=0, Y=0, Z=0) 处的点3D建模程序。

我们创建的骨骼的局部坐标系如下所示。

该骨骼已从其创建位置平移，以显示局部坐标系如何被视为附加到骨骼。 通过确定从参考方向转动的量来测量旋转。 例如，如果我们指定 12 点钟位置作为时钟分针的参考方向，那么对于下图所示的时钟，我们可以说分针顺时针方向旋转了 306 度或逆时针方向旋转了 54 度方向。 在骨骼动画中，参考方向是通过将假想骨骼的局部坐标系与另一个坐标系对齐来获得的，这样两个系统的 +X、+Y 和 +Z 轴都指向相同的方向并且原点重合。

![image](https://user-images.githubusercontent.com/13820586/169971035-2735db60-b78b-4033-a6ff-fa80012bf2e0.png)



在骨骼动画中，参考方向是通过将一个假想的骨骼局部坐标系与另一个坐标系对齐来获得的，这样两个坐标系的+X、+Y和+Z轴就会指向同一个方向，并且原点重合。

![image](https://user-images.githubusercontent.com/13820586/169971353-177ccfde-2ead-460f-9842-65212a18f62a.png)

如果我们选择从与世界坐标系对齐的参考方向测量骨骼的旋转，我们将获得骨骼的绝对旋转。

如果我们选择从与其父骨骼的局部坐标系对齐的参考方向测量骨骼的旋转，我们将得到骨骼的相对旋转。

动画软件必须始终计算绝对旋转，因为它们是将姿势渲染到屏幕所需的。

相对旋转也会被计算出来，它们通常存储在磁盘上的动画文件中。

考虑如下图所示的两根骨骼骨架。 计算绝对值

![image](https://user-images.githubusercontent.com/13820586/169971548-36dc0062-cfe0-4c3f-8520-68aa4ee77fdb.png)

每个骨骼的旋转想象一个骨骼，其局部坐标系轴与世界坐标系轴对齐。

我们将使用这个骨骼的方向作为参考来测量两个骨骼的旋转。 

为了计算两个骨骼中的每一个的旋转，想象一下参考骨骼需要如何围绕世界轴旋转以匹配两个骨骼中的每一个的方向。 

请记住，对于右手坐标系，当从轴的正端观察到轴的原点时，绕轴的顺时针旋转是负的。 

匹配 bone1 方向的一种方法是先将参考骨骼绕 Z 轴旋转 –90 度，然后将其绕 X 轴旋转 –90 度。

![image](https://user-images.githubusercontent.com/13820586/169972042-655a0837-8de4-4ec1-bb01-55372c65a2f9.png)

![image](https://user-images.githubusercontent.com/13820586/169972555-3b8dde1a-8770-4566-a472-b54f13b9657c.png)
![image](https://user-images.githubusercontent.com/13820586/169972575-7f97669c-7bff-4780-93f5-1d85f26e8794.png)
将这两次旋转写成四元数

Rotation-x = (-0.7071 0 0 0.7071)

Rotation-z = (0 0 -0.7071 0.7071)

(-0.7071 0 0 0.7071) x (0 0 -0.7071 0.7071) = (-0.5 -0.5 -0.5 0.5)。

Bone1 的绝对旋转 = (-0.5 –0.5 -0.5 0.5)

Bone2 的绝对旋转 = (-0.5 –0.5 -0.5 0.5)

对于每个骨骼相对于其父骨骼的局部坐标系的旋转，想象一个骨骼的局部坐标系与父骨骼的局部坐标系。

为了计算每两个骨骼的旋转，想象参考骨骼需要如何围绕父骨骼的局部轴旋转以匹配两个骨骼中每一个的方向。

Bone1 没有父对象，因此使用了世界坐标系。要计算 bone2 的相对旋转，参考方向是其父节点 bone1 的方向。

![image](https://user-images.githubusercontent.com/13820586/169973572-e74ca892-bb36-4e13-a4b7-bd3987c9df4b.png)

为了计算bone2的相对旋转，bone2的reference orientation是父节点bone1朝向。

为了测量旋转，你想象如何旋转骨头1，使它的方向与骨头2的方向相匹配，但记住，你将围绕骨头1的局部X, Y和Z轴旋转，它们可能不再与世界轴对齐.

![image](https://user-images.githubusercontent.com/13820586/169974766-f4351c19-523b-4389-a089-f2f1acb9935d.png)

在这个例子中，bone1的方向已经匹配bone2的方向，所以不需要旋转。不旋转的四元数是(0 0 0 1)，也称为单位四元数。

骨头1的相对旋转=骨头1的绝对旋转=(-0.5 -0.5 -0.5 0.5)

骨头2的相对旋转=不旋转=(0 0 0 1)让我们计算另一个姿势的绝对旋转和相对旋转。

![image](https://user-images.githubusercontent.com/13820586/169975010-739e24d4-1976-4838-836c-9ade16f138c4.png)



*******************
来看另一种情况

![image](https://user-images.githubusercontent.com/13820586/169975251-3d4703ce-f4b6-45cf-9e1b-a098edf0a857.png)

首先把bone1朝y轴旋转-90°，然后x轴旋转180°

![image](https://user-images.githubusercontent.com/13820586/169975381-6c0337b9-80fd-45c3-add6-4528e74c0890.png)
![image](https://user-images.githubusercontent.com/13820586/169975465-b4f8eec3-71e5-4c7f-a74f-3367adfa80e6.png)

Rotation1 = (0 0.7071 0 0.7071)
Rotation2 = (1 0 0 0)

Rotation = Rotation2 x Rotation1 = (1 0 0 0) x (0 0.7071 0 0.7071) = (0.7071 0 0.7071 0)

为了匹配bone2的方向，参考方向必须围绕世界Y轴旋转180度。

![image](https://user-images.githubusercontent.com/13820586/169975664-9f5643a3-9ef2-495f-9a45-c1b0f9a0ec86.png)

Rotation = (0 1 0 0)

Bone1’s absolute rotation = (0.7071 0 0.7071 0)
Bone2’s absolute rotation = (0 1 0 0)

bone1的旋转全局坐标系的绝对旋转相同，因为它没有父节点。为了计算bone2的相对旋转，我们假设一个与bone1旋转后相同方向的参考方向，并围绕bone1的局部轴旋转它，直到它与bone2的方向匹配。

![image](https://user-images.githubusercontent.com/13820586/169975953-ed0eee6e-83bd-4a89-80ed-2f3ccd592902.png)

为了匹配骨2的方向，参考方向必须首先围绕骨1的Z轴旋转180度。然后在y轴旋转-90°

![image](https://user-images.githubusercontent.com/13820586/169976075-36d5bc80-13c7-4697-9cc2-887086953c3b.png)
![image](https://user-images.githubusercontent.com/13820586/169976176-df4a7b1f-c632-436b-a6da-e5890a4bb233.png)

Rotation1 = (0 0 1 0)
Rotation2 = (0 -0.7071 0 0.7071)

Rotation = Rotation2 x Rotation1 = (0 -0.7071 0 0.7071) x (0 0 1 0)

Bone1’s relative rotation = (0.7071 0 0.7071 0)
Bone2’s relative rotation = (-0.7071 0 0.7071 0)

## 旋转公式
绝对旋转 = 父骨骼的绝对旋转 x 相对旋转

因为在计算当前骨骼的绝对旋转之前，您需要知道父骨骼的绝对旋转，这意味着您需要先计算根骨骼的绝对旋转，然后计算子骨骼的绝对旋转，然后再计算孙骨骼的绝对旋转依此类推。

对于pose1，我们有：
Bone1 的相对旋转 = Bone1 的绝对旋转 = (-0.5 -0.5 -0.5 0.5)
Bone2 的相对旋转 = 不旋转 = (0 0 0 1)
因此 Bone2 的绝对旋转 = (-0.5 -0.5 -0.5 0.5) x (0 0 0 1) = (-0.5 -0.5 -0.5 0.5)

这个结果与我们之前计算的 Bone2 在pose1的绝对旋转一致。

对于pose2，我们有：
Bone1 的相对旋转 = (0.7071 0 0.7071 0) = Bone1 的绝对旋转
Bone2 的相对旋转 = (-0.7071 0 0.7071 0)
Bone2 的绝对旋转 = (0.7071 0 0.7071 0) x (-0.7071 0 0.7071 0) = (0 -1 0 0)

这个结果似乎和我们之前计算的绝对旋转不匹配但是记住有两个不同的四元数代表同样的旋转。

如果您有一个四元数表示旋转，则表示相同旋转的另一个四元数是通过将第一个四元数乘以 -1 获得的。

一些动画程序总是使用具有正实数部分 (w) 的对中的四元数。

所以 (0 -1 0 0) 与 –1 x (0 -1 0 0) = (0 1 0 0) 的旋转相同，这与我们之前计算的绝对旋转相匹配。

当您第一次为模型创建骨骼骨架时，实际上您也在创建姿势，因为每个骨骼都从参考方向旋转。

创建骨架时骨架的姿势称为绑定姿势或静止姿势。

我们在本文中一直使用的不是存储相对于参考方向测量的旋转，动画软件更常见的是存储相对于绑定姿势中骨骼方向的骨骼旋转。

假设姿势 1 是我们骨架的绑定姿势。 我们将使用绑定姿势方向作为参考方向来计算姿势 2 中每个骨骼的旋转。

## 围绕世界坐标系进行旋转

当您创建骨骼模型时，骨骼的初始姿势被称为bind pose或rest pose。比如T-pose，A-pose

如果您围绕世界轴旋转参考方向以匹配骨骼的方向，您将获得相对于绑定姿势的绝对旋转。

bone1 的参考方向如下所示。 为了匹配bone1 的方向，参考方向必须围绕世界Z 轴旋转–90 度。 

Rotation = (0 0 -0.7071 0.7071) bone2 的参考方向如下所示。 

为了匹配 bone1 的方向，必须首先将参考方向围绕世界 Z 轴旋转 90 度。 接下来，参考方向围绕世界 Y 轴旋转 –90 度。
![image](https://user-images.githubusercontent.com/13820586/169976985-f73bd295-dc10-4d93-9ae9-fce0a1a520eb.png)
![image](https://user-images.githubusercontent.com/13820586/169977045-de880307-2b7a-444f-860c-ada22938f470.png)

- 旋转1 = (0 0 0.7071 0.7071) 
- 旋转2 = (0 -0.7071 0 0.7071)

为了匹配bone2的方向，需进行如下旋转
![image](https://user-images.githubusercontent.com/13820586/169977225-a0d4c06d-1c7c-4a20-893d-30ffdb5bd9a7.png)
![image](https://user-images.githubusercontent.com/13820586/169977316-6d1a4088-a27a-4a13-9ba3-35706a0f3d58.png)
![image](https://user-images.githubusercontent.com/13820586/169977340-0505bcdb-1d60-4b00-850e-849be2e7327b.png)

Bone2 从绑定姿势的绝对旋转：Rotation2 x Rotation1 = (0 -0.7071 0 0.7071) x (0 0 0.7071 0.7071) = (0.5 -0.5 0.5 0.5)

最终我们可以得到：
Bone1 从绑定姿势的绝对旋转 = (0 0 -0.7071 0.7071)
Bone2 从绑定姿势的绝对旋转 = (0.5 - 0.5 0.5 0.5)

旋转公式：
绝对旋转 = 从bind pose旋转到指定pose的绝对旋转矩阵 * 旋转到bind pose的绝对旋转矩阵
absolute rotation = absolute rotation from bind pose x absolute rotation of bind pose

绝对旋转 * 旋转到bind pose的绝对旋转矩阵的逆 = 从bind pose旋转到指定pose的绝对旋转矩阵
absolute rotation x inverse of absolute rotation of bind pose = absolute rotation from bind pose


四元数的逆是，ijk三个虚部的值取反，例如 q = (i -j k w) then the inverse of q which is designated q-1 = (-i j -k w).







# 输入数据
## 获取原始的IMU数据
- 加速度a：[frames, 6, 3]，6个关节点，xyz方向，左手坐标系，相对于传感器的局部坐标系
- 旋转矩阵R：[frames, 6, 3, 3]，6个关节点，旋转矩阵是从四元数转化过来的，相对于全局坐标系


## 校准数据
定义：将数据从局部传感器坐标系转换到SMPL坐标系

1. 我们首先把传感器坐标系的坐标轴与SMPL坐标系相应的轴对齐,即把IMUx轴朝左，y轴朝上，z轴朝前。然后,把此时传感器的朝向设作PIM，也就是坐标系FI到坐标系FM的转移矩阵。  
![image](https://user-images.githubusercontent.com/13820586/169980643-aa3e1c78-1cd4-4eab-b534-b3c65453e984.png)

2. 
![image](https://user-images.githubusercontent.com/13820586/169980916-a344709c-7e7b-44e1-bcea-0d43e3d3ed1d.png)
传感器在全局坐标系下的旋转矩阵RI-sensor  
在全局坐标系下的绝对朝向是RI-bone  
骨骼和传感器的旋转距离是RI-offset



## 正则化数据

![image](https://user-images.githubusercontent.com/13820586/169965692-f2792043-c29d-4e77-8b32-aa7d9b7af67f.png)

### 代码实现

```python
acc = torch.cat((acc_cal[:, :5] - acc_cal[:, 5:], acc_cal[:, 5:]), dim=1).bmm(ori_cal[:, -1]) / config.acc_scale
ori = torch.cat((ori_cal[:, 5:].transpose(2, 3).matmul(ori_cal[:, :5]), ori_cal[:, 5:]), dim=1)
data_nn = torch.cat((acc.view(-1, 18), ori.view(-1, 54)), dim=1).to(device)
pose, tran = inertial_poser.forward_online(data_nn)
pose = rotation_matrix_to_axis_angle(pose.view(1, 216)).view(72)
```
