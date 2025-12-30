
<video src="https://github.com/user-attachments/assets/a33a9cc6-e4f6-452d-960b-c722cfa4591f">

## 0.截帧分析
### 背景
**背景墙：**
一张Gradient配合一个Sphere罩住整个场景
![|721x470](./assets/终末地渲染分析/image.png)


**地板：**
一张纹理Texture，配合一张径向Mask作为Alpha
![|660x424](./assets/终末地渲染分析/image-1.png)

**角色剪影：**
一张烘好的2D图，配合A通道做Clip（通过测试发现是做的AlphaBlend）
![|844x631](./assets/终末地渲染分析/image-3.png)
**矩阵线框：**
依旧是简单朴素的做法，看图
![|820x515](./assets/终末地渲染分析/image-2.png)


### Face
![|817x272](./assets/终末地渲染分析/image-30.png)
贴图分析：
**Face_D** 
- RBG = BaseColor
- A = 鼻尖阴影
![](./assets/终末地渲染分析/image-9.png)
![|591x317](./assets/终末地渲染分析/image-6.png)

**SDF**
- RBG = sdf数据
- A = 鼻尖mask
![](./assets/终末地渲染分析/image-8.png)
![|1062x283](./assets/终末地渲染分析/image-7.png)

这个鼻尖的mask应该不是用在sdf上的，游戏里的sdf没看到鼻尖上边的形状

![](./assets/终末地渲染分析/image-12.png)

**Mask1**
- R = 面部外侧Mask
- G = 除去面部脖子耳朵部分的mask，应该是这部分不给sdf阴影，实测效果有瑕疵
- B = 这部分和G差不多，但是范围大一点，下巴也包括了，没猜到用途
- A = 鼻尖高光和RimMask

![](./assets/终末地渲染分析/image-10.png)

![](./assets/终末地渲染分析/image-19.png)

**Mask2**
单纯的嘴唇高光Mask
![|375x375](./assets/终末地渲染分析/image-18.png)

**RainFlow**
- RG = 双通道法线
- B = Mask或者Flow Path
- A = Alpha
![](./assets/终末地渲染分析/image-14.png)

![](./assets/终末地渲染分析/image-15.png)


### Eye

![|726x322](./assets/终末地渲染分析/image-20.png)
用的图比较少，就是一张MatCap+BaseColor

**BaseColor**
- RGB = BaseColor
- A = 虹膜Mask


**Matcap**
- RGB = MatCap 因为实际上眼球并没有做球体，MatCap是直接采样的，不需要构建uv
![|345x207](./assets/终末地渲染分析/image-25.png)
![|459x149](./assets/终末地渲染分析/image-26.png)

- A = 眼球上半部分遮罩(RenderDoc截出来时是反的，用的时候自己翻转一下)

![|906x400](./assets/终末地渲染分析/image-24.png)


游戏内效果：
![|596x512](./assets/终末地渲染分析/image-22.png)


### Hair
![|791x272](./assets/终末地渲染分析/image-29.png)
总共用了六张图，从左到右依次用于：Normal，BackHairMask，各种Mask，HairST，Ramp，HairBaseColor

![](./assets/终末地渲染分析/image-31.png)




## 1.UE还原渲染
### Skin
![](./assets/终末地渲染分析/image-32.png)

![](./assets/终末地渲染分析/image-33.png)

### Cloth

### Face
基础部分不做整理了，看了截帧部分的贴图应该都知道怎么做了

#### SDF Shadow
![|879x556](./assets/终末地渲染分析/image-13.png)

![](assets/终末地渲染分析/FaceSDF.mp4)
#### Specular
DivideUV作为一半的Mask，切换哪一面使用的就是SDF的FlipU的Side
![](assets/终末地渲染分析/FaceSpecular.mp4)
### Hair
#### Base , ST

#### Kajiya-Kay Specular

### Eyes

![](./assets/终末地渲染分析/image-27.png)


### 眉毛
眉毛半透方案有以下几种：
- WPO，朝着Camera的方向位移
- 模板测试 
