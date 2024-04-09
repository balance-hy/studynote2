# Gurobi

## 获得全部解

```py
from gurobipy import *
import numpy as np
import scipy.sparse as sp

import gurobipy as gp
from gurobipy import GRB

try:

    # Create a new model
    m = gp.Model("mip1")

    # Create variables
    x = m.addVar(vtype=GRB.BINARY, name="x")
    y = m.addVar(vtype=GRB.BINARY, name="y")
    z = m.addVar(vtype=GRB.BINARY, name="z")

    # Set objective

    # Add constraint: x + 2 y + 3 z <= 4
    m.addConstr(x + 2 * y + 3 * z <= 4, "c0")

    # Add constraint: x + y >= 1
    m.addConstr(x + y >= 1, "c1")

    # 限制收集的解决方案数量
    m.setParam(GRB.Param.PoolSolutions, 1024)
    # 通过为最差情况的解设置间隙来限制搜索空间。
    # m.setParam(GRB.Param.PoolGap, 0.10)
    # 系统地搜索 k 最佳解决方案
    m.setParam(GRB.Param.PoolSearchMode, 2) # 1会尝试找到更多解，但不保证是最好的解 2会进行更系统的搜索 实测1有问题

    # Optimize model
    m.optimize()

    for i in range(m.SolCount):
        m.setParam(GRB.Param.SolutionNumber, i) # 解的编号
        print(f"Solution {i}:") # 解的数量
        print("Obj_{} = {}".format(i, m.PoolObjVal)) # 输出目标函数取值
        print(m.Xn) # 输出变量取值
        print()

    print('找到解决方案数量: ' + str(m.SolCount))

except gp.GurobiError as e:
    print('Error code ' + str(e.errno) + ': ' + str(e))

except AttributeError:
    print('Encountered an attribute error')
```

## 固定变量，测试模型

```py
from gurobipy import *
import numpy as np
import scipy.sparse as sp

import gurobipy as gp
from gurobipy import GRB

try:
    for i in range(8):
        m = gp.Model("mip1")
        # 创建决策变量矩阵，数量为不等式个数，此处需要根据具体S盒生成不等式设置
        z = m.addMVar((3, 1), vtype=GRB.BINARY, name="z")

        # ----------------变量赋值--------------------------
        # 将 i 转换为 3 位二进制字符串
        binary_str = format(i, '03b')
        print("实验%d %s" % (i, binary_str))
        # 分别将二进制字符串的每一位赋值给变量 z
        for j, bit in enumerate(binary_str):
            z[j].lb = int(bit)
            z[j].ub = int(bit)

        # -----------------约束-------------------------
        # Add constraint: x + 2 y + 3 z <= 4
        expr = LinExpr()
        expr += z[0] + 2 * z[1] + 3 * z[2]
        m.addConstr(expr <= 4, "c0")

        # Add constraint: x + y >= 1
        expr = LinExpr()
        expr += z[0] + z[1]
        m.addConstr(expr >= 1, "c1")

        # -----------------优化及输出-------------------------
        # 去除不需要的输出
        m.setParam('OutputFlag', 0)
        # Optimize model
        m.optimize()
        if (m.status == 3):
            print("模型无解\n")
        else:
            for v in m.getVars():
                print('%s %g' % (v.varName, v.X))
            print("\n")
        m.write("dist/help.lp")

except gp.GurobiError as e:
    print('Error code ' + str(e.errno) + ': ' + str(e))

except AttributeError:
    print('Encountered an attribute error')
```

