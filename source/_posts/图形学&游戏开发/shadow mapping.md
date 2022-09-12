shadowMap

第一个阶段，把光源当作一个摄像机去渲染一遍场景，然后将光源能照到的点的深度值存储在一张纹理上， 称为shadow map

![image-20220513101640352](https://raw.githubusercontent.com/Vio1ette/blog-img/main/image-20220513101640352.png)

第二个阶段，从相机视角渲染场景，并利用光源视角下的投影矩阵的逆矩阵，将坐标从世界空间转换到光源空间【如果要逆转换，不应该用view矩阵的逆吗？怎么这里说是投影矩阵的逆？】得到光源空间下的坐标和深度值，前者可以用来对shadow map进行查询，后者可以用来和shadow map上的值进行比较，如果该点投影回光源视角的实际深度值大于在shadow map上对应的深度值，说明该点无法被光源照亮，处在阴影

渲染方程的改变：

![image-20220418103352469](https://raw.githubusercontent.com/Vio1ette/blog-img/main/image-20220418103352469.png)

GAMES202中主要实现的函数：

- DirectionalLight.js中的CalcLightMVP(translate, scale)函数
- phongFragment.glsl中的useShadowMap
- PCF
- PCSS
- main

shadowMap是一个uniform变量，不知道是怎么得到的

代码的实现是在已知shadowMap的基础之上进行的实现

## 0x01 CalcLightMVP(translate, scale)

利用给出的translate，scale，光线有关参数（lightPos、focalPoint、lightUp）得出MVP矩阵

## 0x02 useShadowMap(sampler2D shadowMap, vec4 shadowCoord)

shadowCoord是已经转换到light空间的点坐标，所以可以用其z坐标和shadowMap上的对应值做比较，如果是阴影，则visibility=0.0，可见则visibility=1.0

## 0x03 PCF

阴影抗锯齿，用shadowMap上的对应像素周围的多个像素，和shadowCoord.z做比较，比较结果从简单的0，1，变成了0~1之间的值，例如9次比较，6次为1，则visibility为6/9=0.667，可以在阴影边缘处实现抗锯齿的效果，进一步可以模拟软阴影

## 0x04 PCSS

在shadowMap取多大的范围来一起作比较，添加两个因素来调节这个范围，遮挡物到光源的距离和光源的大小

可以实现，近处的阴影偏硬，远处的阴影偏软

