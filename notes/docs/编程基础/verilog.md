# Verilog

在芯片设计时，根据不同模块的功能特点，通常会分为模拟电路模块和数字电路模块

模拟电路还是像早期的半导体电路那样，处理的是连续变化的模拟信号，所以只能用传统的电路设计方法

数字电路处理的是已经量化的数字信号，往往用来实现特定的逻辑功能，更容易被抽象化，所以就产生了专门用于设计数字电路的硬件描述语言

现在业界的 IEEE 标准主要有 VHDL 和 Verilog HDL 这两种硬件描述语言。在高层次数字系统设计领域，大部分公司都采用 Verilog HDL 完成设计

把一份电路用代码的形式表示出来，然后由计算机把代码转换为所对应的逻辑电路

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
