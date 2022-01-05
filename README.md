# calibration_camera_lidar
这是从autoware分离出来的相机雷达联合标定ros包

- 下到自己的工作空间中
```
catkin_make
source devel/setup.bash
```
- 由于安装的ros版本是melodic，需要在文件路径为/home/ws/catkin_ws/src/calibration_camera_lidar/ls_calibration/calibration_camera_lidar目录下的CMakeLists.txt中第72行、94行、114行中分别增加三个melodic
- 如果还出现报错情况（一般是缺少包），按照报错提示安装相应的包即可。

- 新开一个终端执行
```
roscore
```

- 当前终端执行
```
rosrun calibration_camera_lidar calibration_toolkit
```
会弹出如图所示的对话框，依次选择axis/image_raw、Camera→Velodyne即可进入标定页面。

- 在播放bag的时候，需要把雷达点云的topic改成points_raw播放，这样autoware才能读取点云，例如：(这里的--pause是默认暂停，空格控制播放和暂停)
```
rosbag play --pause test.bag /neuvition_cloud:=/points_raw  
```
- 如果相机的的topic不是image_raw而是compressed，我们需要在播放数据包的时候重新打开一个终端执行格式转换命令，例如：
```
rosrun image_transport republish compressed in:=/axis/image_raw raw out:=/axis/image_raw
```
- 加载好图像和点云之后，右边可能显示不出点云，需要切换模式，1和2切换模式，qweasd键控制点云旋转。
- 点击load加载相机内参文件。注意：内参文件里的各种矩阵命名需要和UI中的一致，并且要把后缀yaml改为yml。
- 点击grab抓取数据流中的某一帧进行标定，如果点击「grab」没有反应，是正常的，可能是棋盘格离得太远或者模糊了，多试几个位置应该就能捕获到。
- 把鼠标放到右下角捕获的点云窗口，选择一个棋盘格的中心位置区域，尽量保证向外侧的平面法向量垂直于标定板平面，左键选择，右键取消选择，一般在点云模式1时才能够选择。
- 重复以上步骤，不断回放暂停，grab 捕获单帧图像和点云（多选一些），选择点云区域，直到回放结束，接着就可以点击右上角的「Calibrate」按钮计算外参矩阵。
- 然后再点击「Project」查看标定效果，工具会根据计算结果和激光雷达数据生成的图像对应位置，以红色散点表示，如果红色散点分布到标定板上，则说明正确，如果没有，就重新选点。
