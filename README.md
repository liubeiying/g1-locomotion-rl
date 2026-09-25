# g1-locomotion-rl

> ## ⚠️ 性质声明（请务必保留）
> 本仓库是**复现与工程记录**，不是原创算法仓库。
> - 训练框架、机器人模型、PPO 实现均来自上游开源项目，版权归原作者所有
> - 我做的事情是：**把上游项目在自己的环境下跑通、调通、记下每一步的参数与踩坑**
> - 仓库里所有标注 `TODO` 的位置都是需要本人填写的实测数据，**未填即代表未实测**

**上游来源（算法与代码非本人原创）**
| 内容 | 上游仓库 |
|---|---|
| 训练框架 | https://github.com/leggedrobotics/legged_gym |
| RL 算法（PPO） | https://github.com/leggedrobotics/rsl_rl |
| G1 机器人配置与部署链路 | https://github.com/unitreerobotics/unitree_rl_gym |
| 模型资源 | https://github.com/google-deepmind/mujoco_menagerie |

---

## 一句话说明

在 Isaac Gym 中训练 Unitree G1 人形机器人的行走策略，经 MuJoCo 跨引擎验证后，通过 ROS 2 下发到真机。**完整链路：Train → Play → Sim2Sim → Sim2Real。**

---

## 环境

| 项 | 版本 | 备注 |
|---|---|---|
| OS | Ubuntu 22.04 | TODO: 填你的实际版本 |
| GPU | TODO: 型号 / 显存 | 并行环境数由显存决定 |
| Python | TODO | legged_gym 对版本敏感 |
| Isaac Gym | Preview 4 | 需自行到 NVIDIA 下载 |
| PyTorch | TODO: 版本 + CUDA | 与驱动版本强绑定 |

> TODO: 这一栏必须按你的真实环境填。面试官第一个问题大概率是"你什么配置跑的"。

---

## 跑通流程（我实际执行的命令）

```bash
# 1. 安装（顺序不能乱，Isaac Gym 必须先装）
#    TODO: 补上你实际执行的安装命令与耗时

# 2. 训练（headless 模式，不开渲染）
python legged_gym/scripts/train.py --task=g1 --headless --num_envs=4096

# 3. 回放验证 + 导出 Actor 网络
python legged_gym/scripts/play.py --task=g1

# 4. MuJoCo 跨引擎验证（sim2sim）
python deploy/deploy_mujoco/deploy_mujoco.py g1.yaml

# 5. 真机部署（调试模式下）
python deploy/deploy_real/deploy_real.py <网卡名> g1.yaml
```

**我踩到的坑**：详见 [`docs/02-踩坑记录.md`](docs/02-踩坑记录.md)

---

## 我在这件事上真正做了什么

| 环节 | 我做的 | 产出 |
|---|---|---|
| 环境搭建 | 装 Isaac Gym + rsl_rl + legged_gym，解决版本冲突 | 可复现安装文档 |
| 模型校验 | 逐条核对 URDF 关节限位、速度、惯量、碰撞体 | 校验清单 |
| 奖励设计 | 针对**步态抖动 / 足底打滑**迭代奖励项 | 奖励对照表 |
| 课程学习 | 地形难度随训练进度爬升 | 课程配置 |
| 训练调参 | 侧向推力、阶跃地形抗扰测试，多组参数消融 | 调参结论 |
| 跨引擎验证 | MuJoCo 跑同一策略，定位 sim2sim 退化原因 | 排查记录 |
| 真机下发 | ROS 2 + CycloneDDS 下发，处理延迟与标定误差 | 部署记录 |

---

## 结果（TODO：必须填真实数据，禁止估算）

- 训练：TODO 迭代次数 / 耗时 / 最终奖励值
- sim2sim：TODO 两个引擎下的行为差异描述
- 真机：TODO 是否成功行走、失败模式是什么

> 这一栏是面试官必看的。**没有实测数据就如实写"未做完整测试"**，比编一个数字安全一百倍。

---

## 证据（TODO：贴真实素材）

`docs/assets/` 放：
- 训练曲线截图（reward / value loss）
- Isaac Gym 训练画面录屏
- MuJoCo sim2sim 录屏
- 真机行走录屏（有就放，没有就别放别人的）

---

## 目录

```
docs/
  01-环境搭建.md
  02-踩坑记录.md
  03-奖励与课程设计.md
  04-sim2sim与导出.md
```
