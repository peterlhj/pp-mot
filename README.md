### Jetson Nano部署FairMOT人流量统计模型


#### 介绍
疫情仍在流行，限制体育馆、食堂、图书馆等公共场合人流量是疫情防控的必要措施，通过对监控摄像头画面进行行人追踪，统计人流量，从而实现人流量预警。利用PaddleDetection套件选择FairMot算法进行模型训练。PP-Tracking是基于飞桨深度学习框架的业界首个开源实时跟踪系统,本项目主要介绍借助PP-Tracking部署在Jetson Nano上实现人流量监测。




#### 部署教程


**注意：以下操作是在jetson nano上进行**



**1. 安装PaddlePaddle环境**

下载[PaddlePaddle官方Linux预测库whl包](https://paddleinference.paddlepaddle.org.cn/user_guides/download_lib.html#python)
下载解压后将whl文件并传到Jetson nano上即可pip安装。

jetson nano配置参考：
[在Jetson Nano上基于python部署Paddle Inference——作者：Irving.Gao](https://aistudio.baidu.com/aistudio/projectdetail/2187459)
 安装whl
`pip3 install paddlepaddle_gpu-2.1.1-cp36-cp36m-linux_aarch64.whl`
 打开python3测试

```
import paddle
paddle.fluid.install_check.run_check()
```


**2.部署自己的人流量检测模型**

PP-Tracking提供了独立于PaddleDetection的预测脚本，因此无需安装PaddleDetection，只需要拉取PaddleDetection中的pptracking源码，

pptracking目录：PaddleDetection\deploy\pptracking\python

 拉取PaddleDetection源码

`!git clone https://gitee.com/PaddlePaddle/PaddleDetection`

**3.用导出的模型基于Python去预测行人跟踪**

（1）将第六步导出的FairMOT模型上传到jetson nano中


（2）pptracking提供的 FairMOT模型预测脚本为：deploy/pptracking/python/mot_jde_infer.py

其参数和含义：

--model_dir	模型路径

--image_file	需要预测的图片

--image_dir	要预测的图片文件夹路径

--video_file	需要预测的视频

--camera_id	用来预测的摄像头ID，默认为-1(表示不使用摄像头预测，可设置为：0 - (摄像头数目-1) )，预测过程中在可视化界面按q退出输出预测结果到：output/output.mp4

--device	运行时的设备，可选择CPU/GPU/XPU，默认为CPU

--do_entrance_counting	是否统计出入口流量，默认为False

--draw_center_traj	是否绘制跟踪轨迹，默认为False

job_name=fairmot_dla34_30e_1088x608
model_type=mot/mcfairmot
camera_id = 0 #摄像头编号


 Python预测摄像头：

```
python deploy/pptracking/python/mot_jde_infer.py --model_dir=output_inference/${job_name} --camera_id={camera_id} --device=GPU --threshold=0.5 

```
