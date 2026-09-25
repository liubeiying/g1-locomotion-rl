# 策略导出与 sim2sim 验证

> 上游：unitree_rl_gym（deploy/deploy_mujoco、deploy/deploy_real）

## 导出

- `play.py` 会自动导出 Actor 网络
- MLP → `policy_1.pt`；RNN → `policy_lstm_1.pt`
- 也可自行导出 TorchScript / ONNX 供 ROS 2 节点加载

```bash
python legged_gym/scripts/play.py --task=g1
# TODO: 贴你实际的导出命令与产物路径
```

## sim2sim（MuJoCo）

```bash
python deploy/deploy_mujoco/deploy_mujoco.py g1.yaml
```

修改 `deploy/pre_train/{robot}/motion.pt` 或 YAML 里的 `policy_path` 换成自己的策略。

## 退化定位三层法（我的排查顺序）

| 层 | 检查项 | 典型症状 |
|---|---|---|
| 观测层 | 观测向量顺序是否逐位一致 | 策略表现像随机动作 |
| 控制层 | PD 增益单位与量纲是否统一 | 动作幅度整体偏大/偏小 |
| 模型层 | 阻尼、初始位置、执行器建模 | 真机"使不上劲" |

**我的做法**：写脚本逐位比对两个引擎的观测值，**不靠肉眼看行为判断**。

## URDF / MJCF 对表清单

- [ ] 关节限位、速度上限逐条核对
- [ ] 连杆惯量与质心参数
- [ ] 碰撞模型与连杆尺寸偏差
- [ ] 阻尼 / 初始位置 / 执行器建模
- [ ] mimic 从动关节与主动自由度一致

> 更完整的排查手册见同账号仓库：**sim2real-playbook**

## 真机部署（TODO：填你的真实流程与结果）

```bash
python deploy/deploy_real/deploy_real.py <网卡名> g1.yaml
```

安全顺序（**不可跳步**）：零力矩 → 调试模式 → 悬挂测试 → 落地行走

TODO: 填真机是否跑通、失败模式、处理方式
