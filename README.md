# g1-locomotion-rl

Unitree G1 人形机器人行走策略的复现与工程记录。

## 关于这个仓库

这是**复现记录，不是原创算法**。训练框架、机器人模型、PPO 实现都来自上游开源项目：

| 内容 | 上游 |
|---|---|
| 训练框架 | legged_gym（leggedrobotics） |
| RL 算法 | rsl_rl（leggedrobotics） |
| G1 配置与部署链路 | unitree_rl_gym（unitreerobotics） |
| 模型资源 | mujoco_menagerie（google-deepmind） |

我做的是：在自己的机器上把这条链路跑通，记录每一步的参数选择、观察到的现象和问题排查过程。文档里标注「待补」的地方是我还没做完实测的部分，我会陆续补上。

---

## 做了什么

完整链路：**Train → Play → Sim2Sim → Sim2Real**

```bash
# 训练（headless，不开渲染）
python legged_gym/scripts/train.py --task=g1 --headless --num_envs=4096

# 回放验证，同时导出 Actor 网络
python legged_gym/scripts/play.py --task=g1

# MuJoCo 跨引擎验证
python deploy/deploy_mujoco/deploy_mujoco.py g1.yaml

# 真机部署（调试模式下）
python deploy/deploy_real/deploy_real.py <网卡名> g1.yaml
```

| 环节 | 内容 |
|---|---|
| 环境搭建 | 装 Isaac Gym + rsl_rl + legged_gym，处理版本冲突 |
| 模型校验 | 逐条核对 URDF 关节限位、速度、惯量、碰撞体 |
| 奖励设计 | 针对步态抖动、足底打滑迭代奖励项 |
| 课程学习 | 地形难度随训练进度爬升 |
| 抗扰评估 | 侧向推力、阶跃地形测试，多组参数消融对比 |
| 跨引擎验证 | MuJoCo 跑同一策略，定位 sim2sim 退化原因 |
| 真机下发 | ROS 2 + CycloneDDS，处理延迟与标定误差 |

---

## 环境

| 项 | 版本 |
|---|---|
| OS | Ubuntu 22.04 |
| GPU | 待补 |
| Python | 待补 |
| Isaac Gym | Preview 4 |
| PyTorch | 待补 |
| rsl_rl | 待补 |

> 环境版本对 legged_gym 影响很大，我踩的第一个坑就是版本不匹配，见 [docs/02-踩坑记录.md](docs/02-踩坑记录.md)。

---

## 结果

| 项 | 状态 |
|---|---|
| 训练收敛 | 待补 |
| sim2sim 一致性 | 待补 |
| 真机行走 | 待补 |

补实测数据时我会把测试条件一起写清楚——没有前提条件的数字没有意义。

---

## 目录

```
docs/
  01-环境搭建.md        安装顺序与版本对照
  02-踩坑记录.md        六个真实问题的排查过程
  03-奖励与课程设计.md   症状 → 奖励项 → 效果
  04-sim2sim与导出.md    策略导出与跨引擎验证
```
