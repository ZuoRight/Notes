# 测试

```shell
brownie test [--network xxx] [--pdb]  # 加pdb可以进入调试模式（连接Infura节点时不支持），quit()退出
```

## Fixture

Brownie 集成了 Pytest 作为测试框架，内置了一些 Fixture 方便与智能合约交互

```python
"""
比如调用accounts时，直接将同名参数作为Fixture传入即可使用
不需要再from brownie import accounts
"""
def test_transfer(Token, accounts):
    pass
```

## 隔离测试

可以使用以下 Fixture 实现隔离测试

- `module_isolation` 运行测试模块前后重置本地链
- `fn_isolation` 运行测试前快照当前状态，测试结束后恢复

```python
import pytest
from brownie import accounts


# 可以自定义公共Fixture减少重复事务
@pytest.fixture(scope="module")
def token(Token):
    yield Token.deploy("Test Token", "TST", 18, 1e20, {'from': accounts[0]})


def test_transferFrom(fn_isolation, token):
    token.approve(accounts[1], 6e18, {'from': accounts[0]})
    token.transferFrom(accounts[0], accounts[2], 5e18, {'from': accounts[1]})

    assert token.balanceOf(accounts[2]) == 5e18
    assert token.balanceOf(accounts[0]) == 9.5e19
    assert token.allowance(accounts[0], accounts[1]) == 1e18


def test_balance_allowance(fn_isolation, token):
    assert token.balanceOf(accounts[0]) == 1e20
    assert token.allowance(accounts[0], accounts[1]) == 0
```
