# Pillow

Python内置的图像处理库：PIL(Python Imaging Library)，仅支持2.x，而Pillow是在其基础上开发的，支持3.x的版本，且增加了很多新特性

安装：`pip install pillow`

## 画布

```python
from PIL import Image

# 创建一个画布
im = Image.open(path)  # 从文件读取
im = Image.new("RGB", (w, h), color)  # 新建
im = Image.alpha_composite(im1, im2)  # 合并两张图，必须均为RGBA模式，且尺寸相同

# 属性
"""
im.size 图像尺寸
im.width 宽度
im.height 高度

im.mode 模式，比如RGB

im.format 来源，新建的图片来源为None
im.format_description 更详细的来源
"""

# 方法
"""
im.copy() 复制
im.paste(logo, 坐标) 粘贴
im.paste(logo, 坐标，logo) 粘贴，logo外的部分透明
im.crop() 裁剪一块区域

im.resize() 调整大小
im.rotate(45) 逆时针旋转，旋转后得到一个新的对象，原始对象不变
im.transpose() 镜像翻转
im.transpose(Image.FLIP_LEFT_RIGHT) 水平翻转
im.transpose(Image.FLIP_TOP_BOTTOM) 垂直翻转

im.getpixel(坐标) 获取单像素的RGBA值
im.putpixel(坐标，颜色) 设置单像素颜色

im.save() 保存
im.show() 使用默认程序打开图像
"""
```

## 画笔

```python
from PIL import ImageDraw

# 创建一个画笔
draw = ImageDraw.Draw(im, mode=None))

"""
xy 指起点和终点坐标，可以表示为[(x0, y0), (x1, y1)]，或者[x0, y0, x1, y1]
fill 指填充颜色
width 指线宽，像素为单位
joint 类型
"""
draw.point(xy, fill=None)  # 画点
draw.line(xy, fill=None, width=0, joint=None)  # 画线
draw.rectangle(xy, fill, outline)  # 矩形
draw.polygon(xy, fill, outline)  # 多边形
draw.ellipse((x-r, y-r, x+r, y+r))  # 椭圆，r为半径
draw.arc()  # 圆弧

# 绘制文本
from PIL import ImageFont

draw.text(xy, text, fill=None, font=None)
font = ImageFont.truetype("statics/font/NotoSans-Medium.ttf", text_size)  # 思源黑体，开源，不支持中文（支持中文的字体包比较大）
"""
xy 文本左上角
text 要绘制的文本
fill 文本颜色
font ImageFont实例
direction 文字方向，ltr（从左到右），rtl（从右到左），ttb（从上到下）
"""
```

## 保存为base64格式

```python
def generate_data_url(im, img_format):
    output_buffer = io.BytesIO()
    im.save(output_buffer, "JPEG")
    bytes_data = output_buffer.getvalue()
    # 把字节流编码为base64字符串
    base64_str = base64.b64encode(bytes_data)
    # 再把base64字符串解码为base64字节流
    base64_bytes = base64_str.decode('ascii')
    # 拼接data url
    data_url = "data:image/{};base64,".format(img_format) + base64_bytes

    return data_url
```
