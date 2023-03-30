# 硬件描述语言

在芯片设计时，根据不同模块的功能特点，通常会分为模拟电路模块和数字电路模块

模拟电路还是像早期的半导体电路那样，处理的是连续变化的模拟信号，所以只能用传统的电路设计方法

数字电路处理的是已经量化的数字信号，往往用来实现特定的逻辑功能，更容易被抽象化，所以就产生了专门用于设计数字电路的硬件描述语言。现在业界的 IEEE 标准主要有两种硬件描述语言

- VHDL
- Verilog HDL

在高层次数字系统设计领域，大部分公司都采用 Verilog HDL 完成设计

## Verilog

Verilog 代码和 C 语言、Java 等这些计算机编程语言有本质的不同，高级编程语言通常只要功能实现就行，而在可综合（可理解为代码编译）的 Verilog 代码里，基本所有写出来的东西都对应着实际的电路。

在用 Verilog 的时候，必须理解每条语句实质上对应着什么电路，并且要从电路的角度来思考它为何要这样设计。

```c
// 定义一个模块以关键字module开始，endmodule结束
module counter(  // counter为模块名
  /*
    端口/接口定义
    input 输入信号
    output 输出信号
    inout 双向信号
  */
  input                   reset_n,  //复位端，低有效
  input                   clk,       //输入时钟
  output [3:0]            cnt,      //计数输出
  output                  cout     //溢出位
);  
  
reg [3:0]               cnt_r ;      // 定义了位宽为4bit的计数器寄存器 reg类型信号，信号名称为cnt_r
  
always@(posedge clk or negedge reset_n) begin
  if(!reset_n) begin                  //复位时，计时归0
      cnt_r        <= 4'b0000 ;  // 给寄存器cnt_r赋以4’b0000的值
    end
    else if (cnt_r==4'd9) begin      //计时10个cycle时，计时归0
      cnt_r        <=4'b0000;
    end
  else begin                      
      cnt_r        <= cnt_r + 1'b1 ; //计时加1
  end
end
  
  assign  cout = (cnt_r==4'd9) ;       //输出周期位
  assign  cnt  = cnt_r ;               //输出实时计时器
  
endmodule
```

## 仿真器

芯片前端设计工程师写 Verilog 代码的目的，就是把一份电路用代码的形式表示出来，然后由计算机把代码转换为所对应的逻辑电路。

就像我们开发软件，需要代码编译器和模拟器一样，Verilog 这种硬件语言的代码，也需要 EDA(Electronic Design Automation 电子设计仿真器) 运行验证。

- 付费：`VCS—verilog`、`NC-verilog`
- 开源：`Iverilog`、`GTKWave`

```shell
# 安装 Iverilog
sudo apt-get install iverilog
# 安装 GTKWave
sudo apt-get install gtkwave

# 检查安装是否成功
which iverilog 
which vvp 
which gtkwave
```

通过 `Iverilog` 对 verilog 执行编译，再对生成的文件通过 `vvp` 命令执行仿真，配合 `GTKWave` 即可显示和查看图形化的波形。

有了软件和 Verilog 代码。在运行仿真前，我们还需要设计一个重要的文件，即仿真激励文件，也就是 `TestBench`。在仿真时，要把 TestBench 放在所设计模块的顶层，以便对模块进行系统性的例化调用。

### 逻辑综合

通过逻辑综合，我们就能完成从 Verilog 代码到门级电路的转换。而逻辑综合的结果，就是把设计的 Verilog 代码，翻译成门级网表 Netlist。

![20230204010208](http://image.zuoright.com/20230204010208.png)

逻辑综合需要基于特定的综合库，不同的库中，门电路基本标准单元（Standard Cell）的面积、时序参数是不一样的。所以，选用的综合库不一样，综合出来的电路在时序、面积上也不同。因此，哪怕采用同样的设计，选用台湾的台积电（TSMC）工艺和上海的中芯国际（SMIC）的工艺，最后生产出来的芯片性能也是有差异的。

通常，工业界使用的逻辑综合工具有

- Synopsys 的 `Design Compiler`（DC）
- Cadence 的 `RTL Compiler`
- Synplicity 的 `Synplify`

然而，这些 EDA 工具都被国外垄断了，且需要收取高昂的授权费用。

为了降低学习门槛和费用，这里我们选择：`Yosys`

它是一个轻量级开源综合工具。虽然功能上还达不到工业级的 EDA 工具，但是对于我们这门课的学习已经完全够用了。

```shell
sudo add-apt-repository ppa:saltmakrell/ppasudo 
apt-get updatesudo 
apt-get install yosys
```
