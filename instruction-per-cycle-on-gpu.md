## Instruction per cycle of GPU

### understanding about GPU architectures.

#### GPU\(图形处理器\)

ARCHITECTURE的变迁史

Written by JP

上面我们已经了解了CPU和GPU之间的中转是由graphics

driver

software来承担的，接下来我们来了解一下GPU硬件本身的构造。

著名的游戏引擎虚幻引擎\(Unreal

Engine\)Epic

Games的Unreal

window用PC游戏是在1998年公布的。当时因为还是软件渲染时代，坐标转换矩阵计算是在CPU中进行的。

1999年NVIDIA发布了Geforce

256显卡，因为硬件是T

&

L\(transform

&

lighting\)，矩阵计算和Multiply

accumulate

operation这些固定功能是由硬件来实现的，从这个时候开始，3D

PC游戏的坐标转换将在GPU中实现。

单机游戏机中，在2000年发布的索尼

[Playstation](http://endic.naver.com/enkrEntry.nhn?entryId=4ad2dca8761e412fa517cbad29f3bac8)

2中CPU

Emotion

Engine的向量运算单位已经实现了几何处理，2001年发售的

[任天堂GameCube](http://cndic.naver.com/zh/entry?entryID=c_35f4b0e7ae0f7d)

在GPU

Flipper上实现了硬件T

&

L。

2000年微软发布了window用图形API

DirectX

8。

DirectX

8不是固定功能，而是搭载了用户可以自定义编写的软件管线的硬件Shader，前提得是Programmable

shader架构的GPU。

Shader分为两种，处理几何的Vertex

Shader和处理Pixel的Pixel

Shader，各自内部分别实现了个别硬件Block。

在OpenGL/OpenGL-ES中，把在DirectX中叫做Pixel

shader的词汇称之为Fragment

shader。

2001年发布了支持DiectX

8的操作系统windows

XP和Nvidia

GPU

Geforce

3。

同年，发售的微软单机游戏机

'XBOX'搭载了支持自定义的Geforce

3，并支持Programmable

shader。

渲染就是为了表现出3D立体感，给物件赋予由Shading这种模型现象而产生的阴影和灯光效果。

灯光、阴影处理等影响渲染结果的处理有很多，所以Shading这个词汇作为现代用语，指的是使用GPU来渲染的一般表现。

把在GPU硬件Shader中运行的程序叫做Shader

Program，有时候干脆就叫做Shader。

在这之后，在2005年发售的微软XBOX

360中采用的ATI

GPU

Xenos，采用了Unified

shader，这是一个很大的突破。

Unified

shader作为同一硬件，在Shader

process

中，同时处理vertex

shader和Pixel

shader两边，会根据各自处理阶段和每个游戏特性的不同会更换适合各个处理负荷的资源分配，在Shader

process

方面能保持road平衡。

这边Unified

shader

process是以stream

processing

unit来实现的，具有5要素的VLIW\(Very

Long

Instruction

Word,超长命令\)

ISA。

5要素就是为了Vector4和Matrix4x4运算的

XYZW的4unit，超越函数和为了形态变换处理的T

unit合在一起叫做VLIW5。

在支持Unified

shader以后，作为大规模并行计算机\(Massively

parallel

computer\)的概念也渐渐明确下来，分支预测也逐渐加强，GPGPU处理为了不一直停留于图形处理，逐渐开始使用GPU。

这种趋势一直持续到现在为止，手机用的GPU，每种最新的型号都采用了Unified

shader。现在比较有代表性的GPU架构（开发公司）如下：

-

Mali/Midgard\(ARM\)

-

Adreno

-

Tegra

-

PowerVR

最新型号都支持OpenGL

ES

3.1。其中Mali的最新型号不是VLIW5，采用的是省略了T

unit的VLIW4，把使用的并不是很多的T

unit给省略了，把这部分又分配给了其它地方，处理速度就提高了。而且，Adreno,

Tegra,

PowerVR的最新型号用的也不是VLIW，采用的是接近于一般CPU

ISA的

Scalar

processor

。

Scalar

processor

因为不是向量计算用

SoA形式，所以处理一般AoS形式的数据也是很快的。

随着Shader

program变得越来越复杂，这么做就是为了应对除了向量运算以外增加的处理。

虽然它是GPU

core，但是实际搭载在手机上的不仅仅是GPU，而是把和CPU通信的通信调制解调器等相关部件都一并搭载在一个芯片上的

SoC\(System

on

Chip\)。apple

A8，高通骁龙，Nvidia

Tegra

等公司从ARM拿到许可，把CPU芯片也包含在了SoC上。

手机GPU的Die-Size（半导体芯片面积）因为手机

form

factor的尺寸很小，比PC用台式GPU要小很多，性能也要差很多。但是，现在手机

GPU的Die-Size也有逐渐变大的趋势，以FLOPS来表示的浮动小数点运算性能来看，

iPhone

6的GPU比iPhone

5的GPU的运算性能要高4倍，性能提高的非常快。正如以哪个硬件为对象，优化的内容就完全不一样，开发手机软件的时候，作为我们发布对象的市场是哪个，需要慎重考虑。

系统内存空间虽然也会越来越大，但是正如GPU性能的提升和画面分辨率的提升并没有形成正比，所以内存空间依旧是稀有资源。

而且，和在SoC外面的又远又慢的系统内存的access是通过公交（路径，Bus）实现的，它和CPU,GPU消耗的电力和产生的热量是类似的，根据电池消耗和Soc温度调节功能的不同，可能会成为导致性能低下的原因，所以尽可能减少对系统内存的access。

我们看下PowerVR

Series7XT的内部结构，心脏部位多数是Unified

shading

cluster,USC\)的集合。USC即是scalar运算引擎，又是一般使用的浮点型单精度浮动小数点\(FP16\)，也支持double型二倍精度浮动小数点\(FP32\)的运算。GPU整体作为

ALU\(arithmetic

logic

unit,运算装置\)搭载的处理器核数量最大已经提高到了512个。

作为手机GPU，PowerVR的传统特征是所谓的TBDR\(Tile

based

deffered

rendering\)方式。

Viewport变换结束后，把画面分成小的tile\(bin\)，把属于1

tile内的临近区域的Geometry集合相关的Pointer转送到搭载于GPU

Chip上的小的、快的专用内存，像素处理好之后，把结果记录于系统内存的Frame

buffer。此时，先计算物件的前后关系，根据Over-load把不需要的Pixel

shader处理功能都删掉，运行Deffered

rendering（延迟渲染）。

并没有采用PC用台式GPU的Tile分配，而是采用了简单的

IMR\(Immediate

mode

rendering\)方式。

内存带域策略来看，利用硬件支持的压缩格式虽然效果也挺好的，PowerVR支持自己的贴图压缩格式PVRTC。

安卓设备主流Mali或者Adreno的GPU仅实现了除了

[deferred](http://endic.naver.com/enkrEntry.nhn?entryId=c7f07f4066d24e5098159f7d08ffb898)

rendering的TBR\(Tile

based

rendering\)。

现在，手机设备1080P以上也成了标准，分辨率和PC环境也没啥区别，Pixel

shader处理比重也升高了。

分tile来渲染的方式，也有缺点，因为随着顶点数的增加，Tile

overhead会增加，所以要控制顶点数量，以防顶点处理成为瓶颈。而且，在渲染中，如果要试图关于Frame

buffer的accessh和复数渲染目标的连接的话，因为中间状态要存储到系统内存，会导致严重性能问题。

Low

Level

Graphics

API.

\(

低阶图形应用编程界面\)

安卓因为采用了各种厂商生产的GPU，如果想依靠特定GPU机型的特性或者扩展功能来进行优化的话，根据GPU型号，需要单独的对策。而且，随着GPU的不同，存在特定命令慢或者快等各种特性，仅仅依靠对应最小公分母\(lowest

common

denominator\)是不够的，要仔细的在大多数的机型上做实际测试，游戏跑起来对性能上有没有影响等，如果发现问题的话，得个别处理。

作为标准API，在安卓5.0中，提供了Android

Extention

pack\(AEP\)，OpenGL

ES

3.2中也能使用相关的功能。

OpenGL

ES

3.2支持安卓6.0以上。

IOS的话，苹果设备因为只使用PowerVR

GPU，如果不考虑旧机型的话，可以期待接近于单机游戏机开发用特定的硬件。

举例，在Xcode的

[profiler](http://endic.naver.com/enkrEntry.nhn?entryId=854a1122e74a42218287532e95858f1d)

Instruments中，在一个叫做Tiler

Utilization的项目中能知道TBDR的使用状况。

在Unity中激活

IL2CPP，以IOS版本Build的时候，以生成的Xcode为对象，使用Instuments的话，根据IL2CPP，函数的符号名被翻译为C++代码并应用，如果在Xcode项目选项中把dysm符号文件生成选项激活的话，使用Instruments，可以实施一定程度的

[profiling](http://endic.naver.com/enkrEntry.nhn?entryId=7a40b4369d9d4d9d87a649ae7fcd8a08)

。

而且，IOS，从iPhone

5S之后，往OpenGL

ES

3.0新增了支持独立低阶图形API

Metal。

OpenGL

ES设备的运行，在APP中虽然无法控制，Metal追求的是效率化以及图形处理的高速化。

-现在，图形驱动所做的一些处理在APP中一定程度已经能控制了，使一些不需要的处理在APP中能自主判断并省略。

-把Command

Que以Multi-Thread来对应，提高Multi

core

CPU运转率，缓解了CPU的瓶颈。

而且，作为记录Shader

Program的Shader语言，用的是基于C++

11的Metal

shading

Language。

在Unity中，Build

Settings的Player

Settings中，可以在IOS设置中选择Metal

API，和iPhone

5S相比，在之前的机型中是无法使用的。所以，开发app的时候，就没有必要移植使用Metal的代码了。

安卓也是，像Metal一样，提供低阶图形API。计划在手机设备上实现低阶图形API标准化的

Khronos

Group

发表了要推出基于独立

AMD低阶图形API

Mantle的Vulcan

API，安卓也是，会像OpenGL

ES一样，以标准规格采用Vulcan。

Heterogeneous

System

Architectur

\(异构系统架构\)

把具有相同性质的芯片整合到一个处理器（多核心处理器CUP或者GPU）上的叫做Homogeneous，相反，把两种性质完全不同的芯片整合到一个处理器上的叫做Heterogeneous。

这里所说的Homogeneous整合是多核心处理，Heterogeneous整合是HSA。

HSA会把Heterogeneous的CPU和GPU看作是一个运算体来生成一个抽象的阶层，这样CPU和GPU之间就没有必要进行数据往来了。即，HSA就是把CPU和GPU整合到一个芯片上，在两者之间追求紧密的联系。

随着GPU的不断发展，就是想把它用作为其它的新的运算装置，从那时开始就是不断发现问题，解决问题的过程。简单地说，这种架构让GPGPU比原来更方便利用了。

现在我们整体来看下包含CPU和GPU的系统架构，参考的是用于Sony

Xperia

Z4等机型的SOC

高通骁龙810内部数据流。

64位多核心CPU和Adreno

GPU通信，结构是引用两边的系统内存上的几何，贴图和shader。

虽然，PC上CPU和GPU有各自专用的内存，但是，单机游戏机和手机设备的话，一般都是单一系统内存，CPU和GPU都能访问的一个整合的内存结构\(Unified

memory

architecture,UMA\)。

内存Bus如果单一的话，不管对耗电还是对内存芯片配线面积都是有利的。

UMA的优点是不管内存芯片多小，内存空间都能有效利用。

当CPU内存使用的少的时候，GPU相对可以使用更多的内存。同时，UMA的缺点是，CPU和GPU相互会抢内存带域，可能相互会干涉处理。

而且，不是UMA的时候，当CPU和GPU使用不同类型的内存芯片的时候，CPU使用低延迟的内存芯片，GPU使用的是宽带宽的内存芯片，按照处理器别，选用适合自己的work load，虽然能提高处理效率，但是像UMA这种情况又是无法妥协的。举个例子，以带宽优先，构成UMA，为了隐藏相对增长的Latency，要提高CPU的并行处理能力或者搭载更多的Cache.

虽然有同样的缺点，UMA在掌机中也利用的是除了减少内存芯片的种类，降低成本的优点外，因为是用所谓的Heterogeneous

System

Architectur，由CPU和GPU构成的异构处理器来编程控制的，和为了处理仅仅依靠CPU无法处理的并列

[computing](http://endic.naver.com/enkrEntry.nhn?entryId=1ace9163d7de4dfe80b2fdbac4e24b8c)

workload的系统构成很适合。

关于通用并列处理，专门为Heterogeneous

Computing发布了叫做OpenCL的API。

以游戏来说，原来在CPU上发生的物理运算和AI处理，现在在GPU上也能进行，并且CPU和CPU可以渐渐的相互通信，相互共享信息，Heterogeneous

Architecture的优势也渐渐的突显出来了。手机设备上也同样，不仅仅局限于游戏，一般的软件也是同样，对图片处理和音频处理，物理处理这种高度的数据处理需求也在越来越大，往后用Heterogeneous

Computing来优化应该是趋势。

