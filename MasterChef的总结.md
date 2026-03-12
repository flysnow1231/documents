
## MasterChef核心设计思想
accRewardPerShare是池子的历史累计奖励指数，表示每 1 个质押单位到当前时刻累计应得的奖励。
当某个用户的 stAmount 发生变化时，必须在当前 accRewardPerShare 上建立一个记账基线，也就是更新 rewardDebt，把新增仓位与历史奖励切割开，否则新增仓位会错误地享受进入之前的历史累计奖励。
之后用户的可领取奖励通过
pending = amount * accRewardPerShare - rewardDebt来计算。
由于每次操作只更新当前池子和当前用户，不需要遍历所有用户，所以时间复杂度是 O(1)。

用户stAmount变更时，计算步骤详细如下：

第一步：先结算旧奖励
新增奖励 = 当前 amount * 当前 acc - finishedReward
把它加到 pending

第二步：再修改 amount
amount += depositAmount
amount -= withdrawAmount

第三步：重建 finishedReward
finishedReward = **新**amount * **当前** acc  
### 【先把旧仓位到当前时刻的收益清出来，再用新仓位按当前指数重新建账。】###
###  【 rewardDebt 是用户在 accRewardPerShare 上的记账切割线】###