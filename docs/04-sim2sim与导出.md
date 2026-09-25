# 策略导出与 sim2sim 验证

> 上游：unitree_rl_gym（deploy/deploy_mujoco、deploy/deploy_real）

## 导出

`play.py` 会自动导出 Actor 网络：MLP 存为 `policy_1.pt`，RNN 存为 `policy_lstm_1.pt`。也可以自行导出 TorchScript / ONNX 供 ROS 2 节点加载。

```bash
python legged_gym/scripts/play.py --task=g1
```

## MuJoCo 验证

```bash
python deploy/deploy_mujoco/deploy_mujoco.py g1.yaml
```

把 YAML 里的 `policy_path` 换成自己的策略即可。

## 退化排查：三层定位法

Isaac Gym 里能走、MuJoCo 里摔倒时，我按这个顺序查：

| 层 | 检查项 | 典型症状 |
|---|---|---|
| 观测层 | 观测向量顺序是否逐位一致 | 策略表现像随机动作 |
| 控制层 | PD 增益单位与量纲是否统一 | 动作幅度整体偏大或偏小 |
| 模型层 | 阻尼、初始位置、执行器建模 | 真机使不上劲 |

我的做法是写脚本逐位比对两个引擎的观测值，而不是靠肉眼看行为判断。观测顺序错位的表现不是报错，而是机器人一脸无辜地摔倒——这是它最难查的原因。

## URDF / MJCF 对表清单

- [ ] 关节限位、速度上限逐条核对
- [ ] 连杆惯量与质心参数
- [ ] 碰撞模型与连杆尺寸偏差
- [ ] 阻尼 / 初始位置 / 执行器建模
- [ ] mimic 从动关节与主动自由度一致

更完整的排查手册见我的另一个仓库：**sim2real-playbook**

## 控制周期参考值

| 项 | 参考值 | 来源 |
|---|---|---|
| 策略控制周期 | 20 ms（50 Hz） | `unitree_rl_gym` C++ 部署控制器 `Controller.cpp` |
| 通信方式 | DDS（Unitree SDK2） | 同上 |
| 观测维度 / 动作维度 | 47 / 12 | `g1_config.py` |

> 这两组数字是我在排查"机器人发抖"时的对比基线：策略侧 50 Hz，底层总线是 1000 Hz，中间的插值没做好就会抖。排查过程见 sim2real-playbook 的《频率与延迟》。

## 真机部署

安全顺序不可跳步：**零力矩 → 调试模式 → 悬挂测试 → 落地行走**

```bash
python deploy/deploy_real/deploy_real.py <网卡名> g1.yaml
```

真机结果待补：是否跑通、失败模式是什么、怎么处理的，我会在做完完整测试后补上。
