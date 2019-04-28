# denso_robot_ros usage

## Environment

>* Ubuntu distro: 16.04 LTS
>* ROS distro: kinetic

## Set static ip

The default IP address of COBOTTA is 

> 192.168.0.1

You can set static IP address to 

> 192.168.0.2

Static IP configuration method:

> https://www.jianshu.com/p/d69a95aa1ed7

You can use the following command in bash to confrim connection

```bash
ping 192.168.0.1
```

## Download source code

```bash
git clone https://github.com/NERanger/denso_robot_ros.git
```

## Launch

### 1.Use teaching panel to perform CALSET (initialize joint position)

If you launch the ros driver without CALSET, the following errors are highly possible to occur, which makes COBOTTA unavailable

> [ERROR] [1553080035.957341120]: Failed to motor on. (84500073)

> [ERROR] [1553080051.802591987]: Action client not connected: cobotta/arm_controller/follow_joint_trajectory

After CALSET every joint will be at/exceed its software limit, which may cause the following error

> [ERROR]Failed to write (80000900)

When the error occurs COBOTTA could not move anymore

Known solution to this problem is that to use teaching panel to hold back every joint a little, then COBOTTA can be launched normally

### 2.Launch COBOTTA ros driver

```bash
roslaunch denso_robot_bringup cobotta_bringup.launch sim:=false ip_address:=192.168.0.1
```
### 3.Perform motion planning

> Sometimes a goal will cause "Failed to Write" error, I guess it is due to the exceed of joint software limit. When this error occurs, you need to relaunch the ros driver

### 4.Move gripper

Current version does not provide the function to move the gripper in moveit, but there is a offical solution for current use

```bash
roslaunch denso_robot_bringup <launch file of COBOTTA> sim:=false ip_address:=<COBOTTA's IP Address>

roslaunch bcap_service bcap_service.launch ip_address:=<COBOTTA's IP Address>

rosservice call /bcap_service '{func_id: 3, vntArgs: [{vt: 8, value: "b-CAP"}, {vt: 8, value: "CaoProv.DENSO.VRC"}, {vt: 8, value: "localhost"}, {vt: 8, value: ""}] }'
```
After these steps the following feedback from cobotta is expected

> HRESULT: 0 vntRet: vt: 19 value: " <controller handle> "

The following command moves the gripper to maximum width (30mm).

Target width depends on the last parameter 

> value:"30, 100"

```bash
rosservice call /bcap_service '{func_id: 17, vntArgs: [{vt: 19, value: "<controller handle>"}, {vt: 8, value: "HandMoveA"}, {vt: 8195, value: "30, 100"}] }'
```

## others

These 2 errors can be ignored 

> [ERROR] [1553080074.135850]: Execution failed: [Errno 2] No such file or directory

> ERROR: cannot launch node of type [warehouse_ros/mongo_wrapper_ros.py]: can't locate node [mongo_wrapper_ros.py] in package [warehouse_ros]


## Issues

https://github.com/DENSORobot/denso_robot_ros/issues
