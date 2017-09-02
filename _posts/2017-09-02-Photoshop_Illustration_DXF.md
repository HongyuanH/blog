---
layout:            post
title:             "使用 Photosho 与 Illustration 描边并导出 DXF 文件"
date:              2017-09-02
tags:              Others AutoCAD Photoshop Illustration
category:          Others
author:            hongyuan

---

(1) 打开 Adobe Photoshop，将扫描好的图片(.jpg)用鼠标拖入 Photoshop 当中：

![1_copyInto.png]({{ site.github.url }}/res/2017-09-02-Photoshop_Illustration_DXF/1_copyInto.png#middle)
  
(2) 菜单栏中点击选择 `图像` -> `自动对比度`：

![2_ziDongDuiBiDu.png]({{ site.github.url }}/res/2017-09-02-Photoshop_Illustration_DXF/2_ziDongDuiBiDu.png#middle)

(3) 菜单栏中点击选择 `滤镜` -> `锐化` -> `锐化`：

![3_ruiHua.png]({{ site.github.url }}/res/2017-09-02-Photoshop_Illustration_DXF/3_ruiHua.png#middle)

(4) 如有需要，可在菜单栏中点击选择 `滤镜` -> `锐化` -> `进一步锐化`：

![4_jinYiBuRuiHua.png]({{ site.github.url }}/res/2017-09-02-Photoshop_Illustration_DXF/4_jinYiBuRuiHua.png#middle)

(5) 菜单栏中点击选择 `滤镜` -> `风格化` -> `查找边缘`：

![5_chaZhaoBianYuan.png]({{ site.github.url }}/res/2017-09-02-Photoshop_Illustration_DXF/5_chaZhaoBianYuan.png#middle)

(6) 在左手边的工具栏中选择 `快速选择工具` （鼠标左键按住工具栏上第四个按钮会出现选择菜单，在 `快速选择工具` 与 `魔棒工具` 选第一个）：

![6_xuanZeGongJu.png]({{ site.github.url }}/res/2017-09-02-Photoshop_Illustration_DXF/6_xuanZeGongJu.png#middle)

(7) 用鼠标左键在我们想要选取的范围内随意画一画，确定一个大致范围即可（被选中范围会出现闪烁虚线，可用键盘 `Ctrl` 与 `+` 或者 `-` 来放大、缩小图片以方便选取选区范围）：

![8_xuanQu.gif]({{ site.github.url }}/res/2017-09-02-Photoshop_Illustration_DXF/8_xuanQu.gif#middle)

(8) 在图片上点击鼠标右键，选择 `调整边缘`：

![9_tiaoZhengBianYuan.png]({{ site.github.url }}/res/2017-09-02-Photoshop_Illustration_DXF/9_tiaoZhengBianYuan.png#middle)

(9) 点击 `视图模式` 中的 `视图`，选择 `黑白`：

![10_shiTuHeiBai.png]({{ site.github.url }}/res/2017-09-02-Photoshop_Illustration_DXF/10_shiTuHeiBai.png#middle)

(10) 此时图片中白色代表被选取的范围（现在还有很多毛刺、锯齿，后面会被消除掉）:

![10_shiTuHeiBai2.png]({{ site.github.url }}/res/2017-09-02-Photoshop_Illustration_DXF/10_shiTuHeiBai2.png#middle)

(11) 接下来进行微调，按照下面几个数值，使其平滑并且贴合我们的需求：

![11_canShu.png]({{ site.github.url }}/res/2017-09-02-Photoshop_Illustration_DXF/11_canShu.png#middle)

 * `智能半径`：勾选
 * `半径`：大概 **4.0** 像素，请根据实际情况调节该参数
 * `平滑`：**100**
 * `羽化`：大概 **20** 像素，请根据实际情况调节该参数
 * `对比度`：**100%**
 * `移动边缘`：大概 **-2%**，请根据实际情况调节该参数
 
(12) 如果对形状不太满意，手动微调到满意（`平滑` 与 `对比度` 最好选择100以保证曲线的平滑度！）：

![12_piaoLiangBianYuan.png]({{ site.github.url }}/res/2017-09-02-Photoshop_Illustration_DXF/12_piaoLiangBianYuan.png#middle)

(13) 在图片上点击鼠标右键，选择 `建立工作路径`：

![15_jianLiGongZuoLuJing.png]({{ site.github.url }}/res/2017-09-02-Photoshop_Illustration_DXF/15_jianLiGongZuoLuJing.png#middle)

(14) 容差 **2.0** 像素，点击确定：

![16_rongCha.png]({{ site.github.url }}/res/2017-09-02-Photoshop_Illustration_DXF/16_rongCha.png#middle)

(15) 此时可能看不清我们画出来的路径，因为与背景图重叠在一起了：

![17_kanBuQingLuJing.png]({{ site.github.url }}/res/2017-09-02-Photoshop_Illustration_DXF/17_kanBuQingLuJing.png#middle)

(16) 右键点选图层面板中 `背景` 图层，选择 `复制图层`

![18_fuZhiTuCen.png]({{ site.github.url }}/res/2017-09-02-Photoshop_Illustration_DXF/18_fuZhiTuCen.png#middle)

(17) 点击确定：

![18_fuZhiTuCenQueDing.png]({{ site.github.url }}/res/2017-09-02-Photoshop_Illustration_DXF/18_fuZhiTuCenQueDing.png#middle)

(18) 此时图层面板中会出现一个新的图层 `背景 副本`：

![19_tuCenMianBan.png]({{ site.github.url }}/res/2017-09-02-Photoshop_Illustration_DXF/19_tuCenMianBan.png#middle)

(19) 把两个图层旁边的两只 `眼睛` 点掉：

![20_tuCenYanJing.png]({{ site.github.url }}/res/2017-09-02-Photoshop_Illustration_DXF/20_tuCenYanJing.png#middle)

![21_tuCenDianDiaoYanJing.png]({{ site.github.url }}/res/2017-09-02-Photoshop_Illustration_DXF/21_tuCenDianDiaoYanJing.png#middle)

(20) 此时路径应该显示出来了,如果对这个形状有什么不满意的，请大侠重新来过或手动描边：

![22_luJingChuLai.png]({{ site.github.url }}/res/2017-09-02-Photoshop_Illustration_DXF/22_luJingChuLai.png#middle)

(21) 菜单栏中点击选择 `文件` -> `导出` -> `路径到 Illustrator...`：

![23_export.png]({{ site.github.url }}/res/2017-09-02-Photoshop_Illustration_DXF/23_export.png#middle)

(22) 下拉框选择 `工作路径`，然后点击确定：

![23_exportQueDing.png]({{ site.github.url }}/res/2017-09-02-Photoshop_Illustration_DXF/23_exportQueDing.png#middle)

(23) 存成 .ai 文件：

![24_baoCun.png]({{ site.github.url }}/res/2017-09-02-Photoshop_Illustration_DXF/24_baoCun.png#middle)



(24) 打开 Adobe Illustrator，将刚刚生成的 .ai 文件拖入 Illustrator 中，出现以下对话框，直接点击确定即可：

![25_daoRuAI.png]({{ site.github.url }}/res/2017-09-02-Photoshop_Illustration_DXF/25_daoRuAI.png#middle)

(25) 现在看好像毛都没有：

![26_baiZhiYiZhang.png]({{ site.github.url }}/res/2017-09-02-Photoshop_Illustration_DXF/26_baiZhiYiZhang.png#middle)

(26) 在右手边的工具栏中点击 `图层` 按钮 （倒数第二个按钮），出现图层面板：

![27_daKaiTuCenMianBan.png]({{ site.github.url }}/res/2017-09-02-Photoshop_Illustration_DXF/27_daKaiTuCenMianBan.png#middle)

(27) 在图层1的右边那个小圆圈上面点一下：

![28_dianYuanQuan.png]({{ site.github.url }}/res/2017-09-02-Photoshop_Illustration_DXF/28_dianYuanQuan.png#middle)

(28) 现在应该能看到路径了:

![29_kanDaoL.png]({{ site.github.url }}/res/2017-09-02-Photoshop_Illustration_DXF/29_kanDaoL.png#middle)

(29) 菜单栏中点击选择 `文件` -> `导出...`:

![30_daoChu.png]({{ site.github.url }}/res/2017-09-02-Photoshop_Illustration_DXF/30_daoChu.png#middle)

(30) 下拉框中选择保存类型为 `AutoCAD 交换文件 （*.DXF）`:

![31_cunDXF.png]({{ site.github.url }}/res/2017-09-02-Photoshop_Illustration_DXF/31_cunDXF.png#middle)

(31) 点击导出：

![32_daoChuDXF.png]({{ site.github.url }}/res/2017-09-02-Photoshop_Illustration_DXF/32_daoChuDXF.png#middle)

(32) 选择AutoCAD版本为 `2007/2008/2009`:

![33_autoCADBanBen.png]({{ site.github.url }}/res/2017-09-02-Photoshop_Illustration_DXF/33_autoCADBanBen.png#middle)

(33) 点选 `pt`，将单位改为 `毫米`:

![34_haoMi.png]({{ site.github.url }}/res/2017-09-02-Photoshop_Illustration_DXF/34_haoMi.png#middle)

(34) 将缩放改成 `1 毫米 = 1 个单位`:

![35_1haoMi.png]({{ site.github.url }}/res/2017-09-02-Photoshop_Illustration_DXF/35_1haoMi.png#middle)

(35) 最后点击确定:

![36_zuiHouQueDing.png]({{ site.github.url }}/res/2017-09-02-Photoshop_Illustration_DXF/36_zuiHouQueDing.png#middle)




(36) 用 AutoCAD 打开刚刚生成的 .dxf 文件，检查一下:

![37_daKaiDxf.png]({{ site.github.url }}/res/2017-09-02-Photoshop_Illustration_DXF/37_daKaiDxf.png#middle)

(37) 菜单栏中点击选择 `格式` -> `单位`:

![38_danWei.png]({{ site.github.url }}/res/2017-09-02-Photoshop_Illustration_DXF/38_danWei.png#middle)

(38) 将插入比例由 `英寸` 改为 `毫米`

![39_chaRuBiLi.png]({{ site.github.url }}/res/2017-09-02-Photoshop_Illustration_DXF/39_chaRuBiLi.png#middle)

(39) 点击确定:

![40_queDingBiLi.png]({{ site.github.url }}/res/2017-09-02-Photoshop_Illustration_DXF/40_queDingBiLi.png#middle)

(40) 将原始的图片(.jpg)用鼠标拖入 AutoCAD 当中，键盘输入依次敲打 `0` `，` `0` 以指定插入点为左下角 `（0,0）`，然后连按三次回车:

![41_tuoRuCad.png]({{ site.github.url }}/res/2017-09-02-Photoshop_Illustration_DXF/41_tuoRuCad.png#middle)

(41) 最后检查是否满意:

![42_jianCha.png]({{ site.github.url }}/res/2017-09-02-Photoshop_Illustration_DXF/42_jianCha.png#middle)


(42) Thanks for reading!