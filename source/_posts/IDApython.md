---
title: IDApython
date: 2022-03-04 07:32:02
categories:
 - 技术研究
---

[TOC]



# IDApython

## IDApython简介


在过去，IDAPython主要由三个独立的模块组成。
第一个是idc。它是一个兼容模块，用于包装IDA的IDC功能。
第二个模块是idautils。它是一个面向IDA的高级实用功能。
第三个模块是idaapi。它允许访问更多的低级数据。
随着6.95的发布，IDA开始包含更多模块，这些模块涵盖了过去idapi所涵盖的功能。
这些新模块的命名约定为ida_*.py
本书中引用了一些模块。
ida_kernwin.py就是这样一个模块。
一旦读者读完这本书，我建议你自己去探索这些模块。
它们在IDADIR\python\ id_ *.py中

## 旧版本与新版本

2017年9月，IDA 7.0发布。这个版本是对HexRays的重大更新，因为IDA是从x86移植到x86_64二进制文件的

这个版本的一个副作用是旧的插件需要重新编译

从6.95到7.0的向后兼容性是由于兼容性层存在于IDADIR\python\idc_bc695.py中

下面的代码是兼容层代码的示例

```
def MakeName(ea, name): return set_name(ea, name, SN_CHECK)
```

原来的IDAPython函数MakeName已被重命名为set_name

对于熟悉旧命名约定的IDAPython用户，并不是所有的API名称都更改了。有些API名称无法重新定义，因此保持不变



对于许多用户来说，通常使用hex函数来打印地址。升级到IDA 7+后，使用十六进制打印地址的用户将不再有可点击的地址。地址类型现在是long而不是int。如果您需要打印的地址是可点击的，请使用字符串格式。下面的第一个打印地址很长，不能点击。使用字符串格式打印的地址是可打印的

```python
Python>ea = idc.get_screen_ea() # get address of cursor
Python>print(hex(ea)) # print unclickable address
0x407e3bL
Python>print("0x%x" % ea) # print clickable address
0x407e3b
```

## IDApython基础

在深入研究之前，我们应该定义一些关键字并研究IDA的拆卸输出的结构。这在使用IDA-View窗口的GUI中很常见。我们可以使用下面的代码作为示例。

```
.text:00401570 lea eax, [ebp+arg_0]
```

.text 是节名，地址是00401570。显示的地址为不带0x前缀的十六进制格式。指令lea被称为助记符。助记符之后是eax的第一个操作数，第二个操作数是[ebp+arg_0]。在使用IDAPython api时，最常见的传递变量是地址。

在IDAPython文档中，地址被引用为ea

一个地址可以通过多种功能手动访问。最常用的函数是idc.get_screen_ea()或here()这些函数返回一个光标所在地址的整数值。如果我们想要获得IDB中存在的最小地址，我们可以使用idc.get_inf_attr(INF_MIN_EA)，或者使用idc.get_inf_attr(INF_MAX_EA)获得最大地址。



反汇编输出中所描述的每个元素都可以通过IDAPython中的函数访问。下面是一个如何访问每个元素的示例。请回忆一下，我们之前将地址存储在ea中

```
Python>idc.get_segm_name(ea) # get text
.text
Python>idc.generate_disasm_line(ea, 0) # get disassembly
lea eax, [ebp+arg_0]
Python>idc.print_insn_mnem(ea) # get mnemonic
7
lea
Python>idc.print_operand(ea,0) # get first operand
eax
Python>idc.print_operand(ea,1) # get second operand
[ebp+arg_0]
```

为了获得段名的字符串表示，我们使用idc. get_seg_name (ea)， ea是段中的地址

打印一串的反汇编可以用idc.generate_disasm_line (ea, 0)

参数是存储在ea中的地址和标志0。标志0返回IDA在分析过程中发现的显示的反汇编。当0标志被传递时，ea可以是指令偏移范围内的任何地址。要分解一个精确的偏移量并忽略IDA的分析，将使用一个标记1



为了获取我们将调用的助记符或指令名。为了得到助记符的操作数，我们调用idc.print_operand(ea, long n)。第一个参数是地址，第二个长n是操作数的索引。第一个操作数是0，第二个操作数是1，对于n，后面的每个操作数都加1



在某些情况下，验证地址的存在是很重要的。idaapi.BADADDR, idc.BADADDR或BADADDR可以用来检查有效的地址。

```
Python>if BADADDR != here(): print("valid address")
valid address
```

## 段

打印单行并不是特别有用。IDAPython的强大功能来自于遍历所有指令、交叉引用地址和搜索代码或数据。后两个将在后面的章节中详细描述。也就是说，遍历所有部分是一个很好的开始。

```
Python>for seg in idautils.Segments():

  print("%s, 0x%x, 0x%x" % (idc.get_segm_name(seg), idc.get_segm_start(seg),
idc.get_segm_end(seg)))

Python>
.textbss, 0x401000, 0x411000
.text, 0x411000, 0x418000
.rdata, 0x418000, 0x41b000
.data, 0x41b000, 0x41c000
.idata, 0x41c000, 0x41c228
.00cfg, 0x41d000, 0x41e000
```

如果报错   name 'idautils' is not defined

引用 import idautils就可以了

```
Python>import idautils
```

idautils.Segments()返回一个迭代器类型对象。我们可以使用for循环遍历对象。列表中的每一项都是段的起始地址。

如果将地址作为参数传递给idc. get_seg_name (ea)，则可以使用它来获取段名。段的开始和结束可以通过调用idc. get_seg_start (ea)或idc. get_seg_end (ea)来找到。地址或ea需要在段的开始或结束范围内。

如果我们不想遍历所有的段，但是想从偏移量中找到下一个段，我们可以使用idc.get_next_seg(ea)



传递的地址可以是段范围内的任何地址，我们希望找到下一个段。如果偶然我们想通过名称获得一个段的起始地址，我们可以使用

idc.get_segm_by_sel (idc.selector_by_name (str_SectionName))。函数idc.selector_by_name(segname)返回段选择器，并传递一个段名称的字符串参数。段选择器是一个整数值，从1开始，并在可执行文件中的每个段(又名section)中递增。将段选择器传递给idc. get_seg_by_sel (int)并返回段的起始地址。

## 函数

既然我们已经知道了如何遍历所有片段，那么我们应该回顾一下如何遍历所有已知函数。

```
Python>for func in idautils.Functions():
print("0x%x, %s" % (func, idc.get_func_name(func)))
Python>
0x401000, sub_401000
0x401006, w_vfprintf
0x401034, _main
…removed…
0x401c4d, terminate
0x401c53, IsProcessorFeaturePresent
```

函数()返回已知函数列表。这个列表包含了每个函数的起始地址。函数()可以传入参数在一个范围内进行搜索。

如果我们想要这样做，就要通过起始地址和结束地址的idautils。函数(start_addr end_addr)。要获取函数名，我们使用idc.get_func_name(ea)。

ea可以是函数边界内的任何地址。IDAPython包含大量用于处理函数的api。让我们从一个简单的函数开始。这个函数的语义并不重要，但是我们应该在头脑中创建地址记录。

```
.text:0045C7C3 sub_45C7C3 proc near
.text:0045C7C3 mov eax, [ebp-60h]
.text:0045C7C6 push eax ; void *
.text:0045C7C7 call w_delete
.text:0045C7CC retn
.text:0045C7CC sub_45C7C3 endp

```

为了得到边界，我们可以使用idaapi.get_func(ea)

```
Python>func = idaapi.get_func(ea)
Python>type(func)
<class 'ida_funcs.func_t'>
Python>print("Start: 0x%x, End: 0x%x" % (func.start_ea, func.end_ea))
Start: 0x45c7c3, End: 0x45c7cd
```

idaapi.get_func(ea)返回一个ida_funcs.func_t。有时，如何使用函数调用返回的类并不总是很明显。在Python中研究类的一个有用命令是dir(class)函数。                                                                                                                            

```
python>dir(func)
['__class__', '__delattr__', '__dict__', '__dir__', '__doc__', '__eq__',
'__format__', '__ge__', '__get_points__', '__get_regvars__', '__get_tails__',
'__getattribute__', '__gt__', '__hash__', '__init__', '__init_subclass__',
'__le__', '__lt__', '__module__', '__ne__', '__new__', '__reduce__',
'__reduce_ex__', '__repr__', '__setattr__', '__sizeof__', '__str__',
'__subclasshook__', '__swig_destroy__', '__weakref__', '_print', 'analyzed_sp',
'argsize', 'clear', 'color', 'compare', 'contains', 'does_return', 'empty',
'endEA', 'end_ea', 'extend', 'flags', 'fpd', 'frame', 'frregs', 'frsize',
'intersect', 'is_far', 'llabelqty', 'llabels', 'need_prolog_analysis', 'overlaps',
'owner', 'pntqty', 'points', 'referers', 'refqty', 'regargqty', 'regargs',
'regvarqty', 'regvars', 'size', 'startEA', 'start_ea', 'tailqty', 'tails', 'this',
'thisown']
```

从输出中我们可以看到函数start_ea和end_ea。它们用于访问函数的开始和结束。

结束地址不是最后一条指令中的最后一个地址，而是指令后面的一个字节。这些属性只适用于当前函数。如果要访问周围的函数，可以使用idc.get_next_func(ea)和idc.get_prev_func(ea)。

枚举函数需要注意的是，它只在IDA已标识的情况下有效

作为函数的代码块



在代码块被标记为函数之前，它将在函数枚举过程中被跳过。未标记为函数的代码在图例中被标记为红色

(在IDA的GUI顶部的彩色条)。可以使用函数idc.create_insn(ea)手动修复或自动修复这些问题。

DAPython有很多不同的方法来访问相同的数据。访问函数内边界的常用方法是使用idc.get_func_attr(ea,FUNCATTR_START)和idc.FUNCATTR_END get_func_attr (ea)。

idc.get_func_attr(ea, attr)用于获取函数的开始和结束。然后使用idc打印当前地址和拆解。generate_disasm_line (ea, 0)

我们使用idc.next_head(eax)来获得下一条指令的开始，并继续执行，直到到达这个函数的末尾。这种方法的一个缺陷是它依赖于包含在函数开始和结束边界内的指令。



这种方法的一个缺陷是它依赖于包含在函数开始和结束边界内的指令。如果跳转到比函数末尾更高的地址，循环将提前退出。这些类型的跳转在代码转换等混淆技术中非常常见。由于边界可能是不可靠的，因此最佳实践是调用idautils.FuncItems(ea)来循环函数中的地址。我们将在下一节中详细介绍这种方法。



相似于idc.get_func_attr(ea,attr)。另一个用于收集函数信息的有用参数是idc.FUNCATTR_FLAGS get_func_attr (ea)。FUNCATTR_FLAGS可以用于检索关于函数的信息，比如它是库代码还是函数没有返回值。一个函数有九种可能的标志。如果我们想要枚举所有函数的所有标志，我们可以使用下面的代码。

```
Python>import idautils
Python>for func in idautils.Functions():
flags = idc.get_func_attr(func,FUNCATTR_FLAGS)
if flags & FUNC_NORET:
print("0x%x FUNC_NORET" % func)
if flags & FUNC_FAR:
print("0x%x FUNC_FAR" % func)
if flags & FUNC_LIB:
print("0x%x FUNC_LIB" % func)
if flags & FUNC_STATIC:
print("0x%x FUNC_STATIC" % func)
if flags & FUNC_FRAME:
print("0x%x FUNC_FRAME" % func)
if flags & FUNC_USERFAR:
print("0x%x FUNC_USERFAR" % func)
if flags & FUNC_HIDDEN:
print("0x%x FUNC_HIDDEN" % func)
if flags & FUNC_THUNK:
print("0x%x FUNC_THUNK" % func)
if flags & FUNC_LIB:
print("0x%x FUNC_BOTTOMBP" % func)
Python>
0x401006 FUNC_FRAME
0x40107c FUNC_LIB
0x40107c FUNC_STATIC

```

们使用idautils.Functions()获得所有已知函数地址的列表，然后使用idc.get_func_attr(ea, FUNCATTR_FLAGS)获取标记。我们通过对返回值使用逻辑和(&)操作来检查该值。

例如，为了检查函数是否没有返回值，如果flags & FUNC_NORET，我们将使用下面的比较。现在让我们回顾一下所有的函数标记。有些旗帜很常见，而另一些则很罕见

1. FUNC_NORET

此标志用于标识不执行return指令的函数。它在内部表示为等于1。下面是一个不返回值的函数示例

```
CODE:004028F8 sub_4028F8 proc near
CODE:004028F8
CODE:004028F8 and eax, 7Fh
CODE:004028FB mov edx, [esp+0]
CODE:004028FE jmp sub_4028AC
CODE:004028FE sub_4028F8 endp
```

注意，ret或leave并不是最后的指示

2. FUNC_FAR

这个标志很少看到，除非逆向软件使用分段内存。它在内部表示为整数2。

3. FUNC_USERFAR

这个标志很少见到，也没有什么文档。Hex-Rays将该标志描述为“用户指定了功能的距离”。它的内部值是32

4. FUNC_LIB

此标志用于查找库代码。识别库代码非常有用，因为在进行分析时，这些代码通常可以被忽略。它在内部表示为整数值4。下面是它的用法和功能的一个示例。

```
Python>for func in idautils.Functions():
flags = idc.get_func_attr(func, FUNCATTR_FLAGS)
if flags & FUNC_LIB:
print("0x%x FUNC_LIB %s" % (func,idc.get_func_name(func)))
Python>
0x40107c FUNC_LIB ?pre_c_initialization@@YAHXZ
0x40113a FUNC_LIB ?__scrt_common_main_seh@@YAHXZ
0x4012b2 FUNC_LIB start
0x4012bc FUNC_LIB ?find_pe_section@@YAPAU_IMAGE_SECTION_HEADER@@QAEI@Z
0x401300 FUNC_LIB ___scrt_acquire_startup_lock
0x401332 FUNC_LIB ___scrt_initialize_crt
```

5. FUNC_STATIC

这个标志是用来用一个静态的基于ebp的帧来识别库函数

6. FUNC_FRAME

这个标志表示该函数使用帧指针ebp。使用帧指针的函数通常从用于设置堆栈帧的标准函数序言开始

```
.text:1A716697 push ebp
.text:1A716698 mov ebp, esp
13
.text:1A71669A sub esp, 5Ch
```

7. FUNC_BOTTOMBP

与FUNC_FRAM一样，这个标志用于跟踪帧指针。它标识基指针指向堆栈指针的函数。

8. FUNC_HIDDEN

带有FUNC_HIDDEN标志的函数表示它们是隐藏的，需要展开以查看。如果我们要进入一个被标记为隐藏的函数地址，它将自动展开

9. FUNC_THUNK

这个标志标识了是thunk函数的函数。它们是跳转到另一个函数的简单函数。

```
text:1A710606 Process32Next proc near
.text:1A710606 jmp ds:__imp_Process32Next
.text:1A710606 Process32Next endp
```

应该注意的是，一个函数可以由多个标志组成。下面是一个带有多个标志的函数示例。

```
0x1a716697 FUNC_LIB
0x1a716697 FUNC_FRAME
0x1a716697 FUNC_HIDDEN
0x1a716697 FUNC_BOTTOMBP
```

有时需要将一段代码或数据定义为函数。例如，以下代码在分析阶段没有被定义为函数，或者没有交叉引用。

```
.text:00407DC1
.text:00407DC1 mov ebp, esp
.text:00407DC3 sub esp, 48h
.text:00407DC6 push ebx
```

为了定义一个函数，我们可以使用idc.add_func(start、end)。

```
Python>idc.add_func(0x00407DC1, 0x00407E90
```

idc.add_func(start, end)的第一个参数是函数的起始地址，第二个是函数的结束地址

在许多情况下，不需要函数结束地址和IDA自动识别函数的结束。

下面的程序集是执行上述代码的输出

```
text:00407DC1 sub_407DC1 proc near
.text:00407DC1
.text:00407DC1 SystemInfo= _SYSTEM_INFO ptr -48h
.text:00407DC1 Buffer = _MEMORY_BASIC_INFORMATION ptr -24h
.text:00407DC1 flOldProtect= dword ptr -8
.text:00407DC1 dwSize = dword ptr -4
.text:00407DC1
.text:00407DC1 mov ebp, esp
.text:00407DC3 sub esp, 48h
.text:00407DC6 push ebx
```

#### Extracting Function Arguments

在IDAPython中提取函数参数并不总是一项简单的任务

在许多情况下，需要确定函数的调用约定，并且必须使用反向跟踪或类似的技术手动解析参数

由于大量的调用约定，所以实现泛型这并不总是可行的。IDAPython确实包含一个名为idaapi.get_arg_addrs(ea)的函数，如果IDA能够识别被调用函数的原型，则可以使用该函数获取参数的地址

这种标识并不总是存在，但在对api的调用或64位代码中经常可以看到。例如，在下面的程序集中，我们可以看到API SendMessage有四个传递给它的参数

```
.text:000000014001B5FF js loc_14001B72B
.text:000000014001B605 mov rcx, cs:qword_14002D368 ; hWnd
.text:000000014001B60C xor r9d, r9d ; lParam
.text:000000014001B60F xor r8d, r8d ; wParam
.text:000000014001B612 mov edx, 0BDh ; '½' ; Msg
.text:000000014001B617 call cs:SendMessageW
.text:000000014001B61D xor esi, esi
```

通过使用以ea为API地址的idaapi.get_arg_addrs(ea)，我们可以检索传递参数的地址列表。

```
Python>ea = 0x00014001B617
Python>idaapi.get_arg_addrs(ea)
[0x14001b605, 0x14001b612, 0x14001b60f, 0x14001b60c]
```

## 指令

既然我们知道了如何使用函数，现在是时候回顾一下如何访问函数中的指令了。如果我们有一个函数的地址，我们可以使用idautils.FuncItems(ea)来获得所有地址的列表。

```
Python>dism_addr = list(idautils.FuncItems(here()))
Python>type(dism_addr)
<type 'list'>
Python>print(dism_addr)
[4573123, 4573126, 4573127, 4573132]
Python>for line in dism_addr: print("0x%x %s" % (line,
idc.generate_disasm_line(line, 0)))
0x45c7c3 mov eax, [ebp-60h]
0x45c7c6 push eax ; void *
0x45c7c7 call w_delete
0x45c7cc retn
```

idautils.FuncItems(ea)返回一个迭代器类型，但被转换为一个列表。这个列表按连续的顺序包含了每条指令的起始地址



现在我们已经有了一个很好的遍历段、函数和指令的知识库;让我们展示一个有用的例子。有时在逆向 packed code时，只知道动态调用发生在哪里是有用的。动态调用可以是对寄存器(如call eax或jmp edi)的操作数的调用或跳转

```
Python>
for func in idautils.Functions():
flags = idc.get_func_attr(func, FUNCATTR_FLAGS)
if flags & FUNC_LIB or flags & FUNC_THUNK:
continue
dism_addr = list(idautils.FuncItems(func))
for line in dism_addr:
m = idc.print_insn_mnem(line)
if m == 'call' or m == 'jmp':
op = idc.get_operand_type(line, 0)
if op == o_reg:
print("0x%x %s" % (line, idc.generate_disasm_line(line, 0)))
Python>
0x43ebde call eax ; VirtualProtect
```

我们调用idautils.Functions()来获得所有已知函数的列表。

对于每个函数，我们通过调用idc来检索函数标志。FUNCATTR_FLAGS get_func_attr (ea)

如果函数是库代码或thunk函数函数被传递。接下来，我们调用idautils.FuncItems(ea)来获取函数内的所有地址

我们使用for循环遍历列表。因为我们只对call和jmp指令感兴趣，所以我们需要通过调用idc.print_insn_mnem(ea)来获取助记符

然后我们使用一个简单的字符串比较来检查助记符。如果助记符是一个跳转或调用，则通过调用idc来获取操作数类型。idc.get_operand_type (ea,n)

这个函数返回一个在内部被称为op_t的整数。类型。这个值可以用来确定操作数是否为寄存器、内存引用等。然后检查op_t。类型是一个寄存器。如果是，则打印该行。将idautils.FuncItems(ea)的返回值转换为列表是很有用的，因为迭代器没有像len()这样的对象。通过将其转换为列表，我们可以很容易地获得函数中的行数或指令数。

```
Python>ea = here()
Python>len(idautils.FuncItems(ea))
Traceback (most recent call last):
File "<string>", line 1, in <module>
TypeError: object of type 'generator' has no len()
Python>len(list(idautils.FuncItems(ea)))
39
```

在前面的示例中，我们使用了一个包含函数中所有地址的列表

我们循环遍历每个实体以访问下一条指令

如果我们只有一个地址，并且想要得到下一条指令，那该怎么办? 为了移动到下一个指令地址，我们可以使用idc.next_head(ea)，为了获得前一个指令地址，我们可以使用idc.prev_head(ea)。



这些函数得到的是下一条指令的开始，而不是下一个地址。为了获得下一个地址，我们使用idc.next_addr(ea)，为了获得前一个地址，我们使用idc.prev_head(ea)。

```
Python>ea = here()
Python>print("0x%x %s" % (ea, idc.generate_disasm_line(ea, 0)))
0x10004f24 call sub_10004F32
Python>next_instr = idc.next_head(ea)
Python>print("0x%x %s" % (ea, idc.generate_disasm_line(next_instr, 0)))
0x10004f29 mov [esi], eax
Python>prev_instr = idc.prev_head(ea)
Python>print("0x%x %s" % (ea, idc.generate_disasm_line(prev_instr, 0)))
0x10004f1e mov [esi+98h], eax
Python>print("0x%x" % idc.next_addr(ea))
0x10004f25
Python>print("0x%x" % idc.prev_head(ea))
0x10004f23
```

在动态调用示例中，IDAPython代码依赖于使用jmp和call的字符串比较。除了使用字符串比较，我们还可以使用idaapi.decode_insn(insn_t, ea)解码指令。

第一个参数是ida_ua中的insn_t类，它是通过调用ida_ua.insn_t()创建的,一旦idaapi.decode_insn被调用，这个类就会被属性填充

第二个参数是要分析的地址

对指令进行解码是有好处的，因为使用指令的整数表示可以更快，更少出错。不幸的是，整数表示是特定于IDA的，不能容易地移植到其他拆卸工具上。下面是相同的示例，但是使用idaapi.decode_insn(insn_t, ea)并比较整数表示。

```
Python>JMPS = [idaapi.NN_jmp, idaapi.NN_jmpfi, idaapi.NN_jmpni]
Python>CALLS = [idaapi.NN_call, idaapi.NN_callfi, idaapi.NN_callni]
Python>for func in idautils.Functions():
flags = idc.get_func_attr(func, FUNCATTR_FLAGS)
if flags & FUNC_LIB or flags & FUNC_THUNK:
continue
dism_addr = list(idautils.FuncItems(func))
for line in dism_addr:
ins = ida_ua.insn_t()
idaapi.decode_insn(ins, line)
if ins.itype in CALLS or ins.itype in JMPS:
if ins.Op1.type == o_reg:
print("0x%x %s" % (line, idc.generate_disasm_line(line, 0))
Python>
0x43ebde call eax ; VirtualProtect
```

输出与前面的示例相同。前两行将jmp和call的常量放入两个列表中。因为我们不使用助记符的字符串表示，所以我们需要认识到一个助记符(如call或jmp)可以有多个值。

例如，jmp可以用idaapi.NN_jmp跳转，idapi.NN_jmpfi用于间接远跳或idapi.NN_jmpni用于间接的近跳



X86和X64指令类型都以NN开头。为了探索所有1700多种指令类型，我们可以在命令行中执行[dir中的名称对应名称(idapi)，如果名称中有“NN”]，或者在IDA的SDK文件allins.hpp中查看它们。

一旦我们在列表中有了指令，我们就使用idautils.Functions()和get_func_attr(ea, FUNCATTR_FLAGS)的组合来获得所有适用的函数，同时忽略库和thunks。 



我们通过调用idautils.FuncItems(ea)来获取函数中的每条指令。这里调用了新引入的函数idaapi.decode_insn(ins, ea)。这个函数获取我们想要解码的指令的地址。解码后，可以通过访问变量ins中的insn_t类来访问指令的不同属性

```
Python>dir(ins)
['Op1', 'Op2', 'Op3', 'Op4', 'Op5', 'Op6', 'Operands', '__class__', '__del__',
'__delattr__', '__dict__', '__doc__', '__format__', '__get_auxpref__',
'__get_operand__', '__get_ops__', '__getattribute__', '__getitem__', '__hash__',
'__init__', '__iter__', '__module__', '__new__', '__reduce__', '__reduce_ex__',
'__repr__', '__set_auxpref__', '__setattr__', '__sizeof__', '__str__',
'__subclasshook__', '__swig_destroy__', '__weakref__', 'add_cref', 'add_dref',
'add_off_drefs', 'assign', 'auxpref', 'create_op_data', 'create_stkvar', 'cs',
'ea', 'flags', 'get_canon_feature', 'get_canon_mnem', 'get_next_byte',
'get_next_dword', 'get_next_qword', 'get_next_word', 'insnpref', 'ip', 'is_64bit',
'is_canon_insn', 'is_macro', 'itype', 'ops', 'segpref', 'size', 'this', 'thisown']
```

正如我们从dir()命令中看到的，ins有很多属性。操作数类型是通过使用ins.Op1.type访问的.请注意，操作数索引从1开始，而不是从0开始，这与idc不同

## 操作数

操作数类型是常用的类型，因此遍历所有类型是有利的。

如前所述，我们可以使用idc.get_operand_type(ea,n)来获取操作数类型。ea是地址，n是索引。有八种不同的操作数类型

ea是地址，n是索引。有八种不同的操作数类型。

#### o_void

如果一条指令没有任何操作数，则返回0

```
Python>print("0x%x %s" % (ea, idc.generate_disasm_line(ea, 0)))
0xa09166 retn
Python>print(idc.get_operand_type(ea,0))
0
```

#### o_reg

如果一个操作数是通用寄存器，它返回这种类型。这个值在内部表示为1。

```
Python>print("0x%x %s" % (ea, idc.generate_disasm_line(ea, 0)))
0xa09163 pop edi
Python>print(idc.get_operand_type(ea,0))
1
```

#### o_mem

如果操作数是直接内存引用，则返回该类型。这个值在内部表示为2。

这种类型对于查找对数据的引用很有用

```
Python>print("0x%x %s" % (ea, idc.generate_disasm_line(ea, 0)))
0xa05d86 cmp ds:dword_A152B8, 0
Python>print(idc.get_operand_type(ea,0)) 
2
```



#### o_phrase

如果操作数由基寄存器和/或索引寄存器组成，则返回此操作数。这个值在内部表示为3。

```
Python>print("0x%x %s" % (ea, idc.generate_disasm_line(ea, 0)))
0x1000b8c2 mov [edi+ecx], eax
Python>print(idc.get_operand_type(ea,0))
3
```

#### o_displ

如果操作数由寄存器和位移值组成，则返回该操作数。位移是一个整数值，例如0x18。这在指令访问结构中的值时很常见。在内部，它表示为值4。

```
Python>print("0x%x %s" % (ea, idc.generate_disasm_line(ea, 0)))
0xa05dc1 mov eax, [edi+18h]
Python>print(idc.get_operand_type(ea,1))
4
```

#### o_imm

作为值的操作数(例如整数0xC)属于这种类型。在内部，它表示为5

```
Python>print("0x%x %s" % (ea, idc.generate_disasm_line(ea, 0)))
0xa05da1 add esp, 0Ch
Python>print(idc.get_operand_type(ea,1))
5
```

#### o_far

当逆向x86或x86_64时，这个操作数不常见。它用于查找访问直接远地址的操作数。它在内部表示为6

#### o_near

当逆向x86或x86_64时，这个操作数不常见。它用于查找访问最近地址的操作数。它在内部表示为7

### 例子

有时，当逆向可执行文件的内存转储时，操作数不能被识别为偏移量

```
seg000:00BC1388 push 0Ch
seg000:00BC138A push 0BC10B8h
seg000:00BC138F push [esp+10h+arg_0]
seg000:00BC1393 call ds:_strnicmp
```

第二个被推入的值是内存偏移量。如果我们右键点击它并将其更改为数据类型;我们将看到字符串的偏移量。这样做一两次是可以的，但是在那之后，我们也可以自动化这个过程

```python
min = idc.get_inf_attr(INF_MIN_EA)
max = idc.get_inf_attr(INF_MAX_EA)

for each known function

for func in idautils.Functions():
flags = idc.get_func_attr(func, FUNCATTR_FLAGS)

#skip library & thunk functions

if flags & FUNC_LIB or flags & FUNC_THUNK:
continue
dism_addr = list(idautils.FuncItems(func))
for curr_addr in dism_addr:
if idc.get_operand_type(curr_addr, 0) == 5 and \
(min < idc.get_operand_value(curr_addr, 0) < max):
idc.OpOff(curr_addr, 0, 0)
if idc.get_operand_type(curr_addr, 1) == 5 and \
(min < idc.get_operand_value(curr_addr, 1) < max):
idc.op_plain_offset(curr_addr, 1, 0)
```

在运行了上面的代码之后，我们现在可以看到字符串了。

```
seg000:00BC1388 push 0Ch
seg000:00BC138A push offset aNtoskrnl_exe ; "ntoskrnl.exe"
seg000:00BC138F push [esp+10h+arg_0]
seg000:00BC1393 call ds:_strnicmp
```

在开始时，我们通过调用idc.get_inf_attr(INF_MIN_EA)获得最小和最大地址，并循环遍历所有地址功能和使用说明

对于每条指令，我们检查操作数类型是否为o_imm，并且在内部表示为数字5。o_imm类型是整数或偏移量之类的值。旦找到一个值，我们就通过调用idc.get_operand_value(ea,n)来读取该值。

然后检查该值是否在最小和最大地址的范围内。如果是，我们使用idc.op_plain_offset(ea, n, base)将操作数转换为偏移量。第一个参数ea是地址，n是操作数的索引，base是基址。我们的例子只需要底数为0

## 基本代码块

基本代码块是没有分支的直线代码序列，由一个单入口点和一个单出口点组成。基本块在分析程序的控制流时是有用的。在使用函数的图形反汇编视图时，通常可以观察到rda对基本块的表示。一些使用基本块进行分析的著名例子是用于识别循环或控制流混淆。当一个基本块将控制权转移到另一个块时，下一个块称为后继块，前一个块称为前一个块。下面流程图是一个用单字节异或解密字符串的函数。

由于图像很难看到代码和地址，所以可以在下面找到汇编输出。该函数包含三个块，基本块前导位于偏移量

0x0401034、0x040104A和0x0040105e。

XOR循环从0x040104A开始，索引在偏移量0x0401059处计算，并在XOR循环完成后继续到0x040105E。

```
.text:00401034 push esi
.text:00401035 push edi
.text:00401036 push 0Ah ; Size
.text:00401038 call ds:malloc
.text:0040103E mov esi, eax
.text:00401040 mov edi, offset str_encrypted
.text:00401045 xor eax, eax ; eax = 0
.text:00401047 sub edi, esi
.text:00401049 pop ecx
.text:0040104A
.text:0040104A loop: ; CODE XREF: _main+28↓j
.text:0040104A lea edx, [eax+esi]
.text:0040104D mov cl, [edi+edx]
.text:00401050 xor cl, ds:b_key ; cl = 0
.text:00401056 inc eax
.text:00401057 mov [edx], cl
.text:00401059 cmp eax, 9 ; index
.text:0040105C jb short loop
.text:0040105E push esi
.text:0040105F push offset str_format
.text:00401064 mov byte ptr [esi+9], 0
.text:00401068 call w_vfprintf
.text:0040106D push esi ; Memory
.text:0040106E call ds:free
.text:00401074 add esp, 0Ch
.text:00401077 xor eax, eax ; eax = 0
.text:00401079 pop edi
.text:0040107A pop esi
.text:0040107B retn
.text:0040107B _main endp
```

如果在0x0401050处提取单字节XOR加密的偏移量，则使用以下代码获取发生XOR的基本块的开始和结束，并获取后继和前继基本块。

```
ea = 0x0401050
f = idaapi.get_func(ea)
fc = idaapi.FlowChart(f, flags=idaapi.FC_PREDS)
for block in fc:
 print("ID: %i Start: 0x%x End: 0x%x" %         (block.id, block.start_ea,
block.end_ea))
if block.start_ea <= ea < block.end_ea:
  print(" Basic Block selected")
successor = block.succs()
for addr in successor:
  print(" Successor: 0x%x" % addr.start_ea)
pre = block.preds()
for addr in pre:
 print(" Predecessor: 0x%x" % addr.end_ea)
if ida_gdl.is_ret_block(block.type):
 print(" Return Block")
```

第一条指令将单个字节的异或偏移量赋给变量ea

函数idaapi.FlowChart(f=None, bounds=None, flags=0)要求传递一个func_t类作为第一个参数

为了得到这个类,我们调用idaapi.get_func (ea)

参数bounds可以传递一个元组，其中第一项是开始地址，第二项是结束地址边界=(start, end)。

在IDA 7.4中，第三个参数标志必须设置为idapi.FC_PREDS，如果要计算前任。变量fc包含一个ida_gdl.FlowChart，可循环通过该对象来遍历所有块。每个块包含以下属性。

1. 函数中的每个基本块都有一个唯一的索引。第一个块的id为0。

2. type用以下类型描述基本块

  fcb_normal表示一个正常块，其内部值为0。                           fcb_indjump是一个以间接跳转结束的块，其内部值为1。



fcb_ret是一个返回块，其内部值为2

ida_gld .is_ret_block(block.type)也可以用来确定块是否为fcb_ret类型



fcb_cndret是一个条件返回区，值为3 

 fcb_noret是一块没有回报,有一个内部的价值4 

fcb_enoret是一块没有返回,不属于一个函数和一个内部5 

 fcb_extern外部没有价值

fcb_error是一个将执行传递到函数end之后的块，其内部值为7

3. start_ea是基本块的起始地址
4. end_ea是基本块的结束地址。基本块的结束地址不是最后一个指令地址，而是它后面的偏移量。
5. preds是一个函数，它返回一个包含所有前一个地址的生成器
6. succs是一个函数，它返回一个包含所有后续地址的生成器

在 idaapi.FlowChart被调用之后，就会遍历每个基本块

打印id、起始地址和结束地址。

要定位ea所在的块，

通过比较block.start_ea是否大于或等于基本块的开始部分并且block.start_ea小于block.end_ea。

变量名块是任意选择的

为了获得所有后续偏移量的生成器，我们调用block.succs()

sucs发生器中的每一项都循环通过并打印出来。要获得包含所有前任偏移量的生成器，可以调用block.preds()



preds生成器中的每个项都被循环遍历并打印出来。最后一个if语句调用ida_gld .is_ret_block(btype)来确定块是否为返回类型。

脚本的输出如下所示。

```
ID: 0 Start: 0x401034 End: 0x40104a
Successor: 0x40104a
ID: 1 Start: 0x40104a End: 0x40105e
Basic Block selected
Successor: 0x40105e
Successor: 0x40104a
Predecessor: 0x40104a
Predecessor: 0x40105e
ID: 2 Start: 0x40105e End: 0x40107c
Predecessor: 0x40105e
Return Block
```

ID为1的基本块是一个循环，这就是为什么它会发生多个继承和前身。

## 结构体

在编译过程中从代码中删除结构布局、结构名称和类型。重构结构并正确标记成员名可以极大地帮助逆向过程。

下面是x86 shellcode中常见的汇编代码片段。

在线程环境块(TEB)和进程环境块(PEB)中查找kernel32.dll的基址的完整代码遍历结构

```
seg000:00000000 xor ecx, ecx
seg000:00000002 mov eax, fs:[ecx+30h]
seg000:00000006 mov eax, [eax+0Ch]
seg000:00000009 mov eax, [eax+14h]
```

通常观察到的下一步是遍历可移植的可执行文件格式到查找窗口apis。

在解析所有不同的结构时，很容易丢失，除非标记出结构偏移量。从下面的代码中可以看出，即使是一些标记的结构也会很有帮助。

```
seg000:00000000 xor ecx, ecx
seg000:00000002 mov eax, fs:[ecx+_TEB.ProcessEnvironmentBlock]
seg000:00000006 mov eax, [eax+PEB.Ldr]
seg000:00000009 mov eax, [eax+PEB_LDR_DATA.InMemoryOrderModuleList.Flink]
seg000:0000000C mov eax, [eax+ecx]
```

我们可以使用下面的代码将偏移量标记为相应的结构名称

```
status = idc.add_default_til("ntapi")
if status:
idc.import_type(-1, "_TEB")
idc.import_type(-1, "PEB")
idc.import_type(-1, "PEB_LDR_DATA")
ea = 2
teb_id = idc.get_struc_id("_TEB")
idc.op_stroff(ea, 1, teb_id, 0)
ea = idc.next_head(ea)
peb_ldr_id = idc.get_struc_id("PEB_LDR_DATA")
idc.op_stroff(ea, 1, peb_ldr_id, 0)
ea = idc.next_head(ea)
idc.op_stroff(ea, 1, peb_ldr_id, 0)
```

第一行是通过调用idc.add_default_til(name)来加载类型库(TIL)

对于不熟悉TIL的人来说，它们是IDA自己的C/ c++头文件格式。它们包含结构、枚举、联合和其他数据类型的定义可以通过打开类型库窗口(SHIFT+F11)手动探索不同的TILs。

idc.add_default_til(name)返回库是否可以加载的状态。如果可以加载TIL，则返回1 (True)或0 (False)(如果库是否加载)。将这种检查添加到代码中是一个好习惯。

ida并不总是识别导入TIL的编译器，或者忘记我们手动加载了TIL。

在加载TIL之后，需要将来自TIL的单个定义导入IDB。要导入个人定义，我们称之为idc.import_type (idx type_name)第一个参数是idx，它是类型的索引

每种类型都有一个索引和id

idx为-1表示该类型应该添加到IDA的导入类型列表的末尾。一个类型的索引可以改变，因此依赖索引并不总是可靠的。idx -1是最常用的参数。上述代码中添加到IDB的三种类型是_TEB、PEB和PEB_LDR_DATA

变量ea被赋值为2。赋值之后，我们通过调用idc.get_struc_id(string_name)来获得导入类型的id。

字符串“_TEB”被传递给idc.get_struct_id，它以整数形式返回struct ID结构id被分配给teb_id

要将成员名“ProcessEnvironmentBlock”应用于结构偏移量(0x30)，我们可以使用idc.op_stroff(ea, n, strid, delta)。op_stroff接受4个参数。

第一个参数是包含将要被标记的偏移量的指令的地址(ea)。

第二个参数n是操作数。在我们的示例中，因为我们想要更改mov eax, fs:[ecx+30h]中的标签0x30，所以需要为第二个操作数传递一个值1

第三个参数是类型id，它需要用来将偏移量转换为结构体

最后一个参数是基结构和进入该结构的指针之间的增量。这个的值通常为0。idc.op_stroff用于将结构名添加到偏移量中。然后，代码调用idc.next_head(ea)获取下一个指令地址，然后使用前面描述的相同进程标记另外两个结构。

除了使用IDA内置的TIL访问结构之外，我们还可以创建自己的结构

对于本例，我们将假定IDA没有PEB_LDR_DATA的类型定义与使用IDA不同，我们必须使用winddbg命令dt nt!_PEB_LDR_DATA。该命令的输出如下所示。

```
0:000> dt nt!_PEB_LDR_DATA
ntdll!_PEB_LDR_DATA
+0x000 Length : Uint4B
+0x004 Initialized : UChar
+0x008 SsHandle : Ptr64 Void
+0x010 InLoadOrderModuleList : _LIST_ENTRY
+0x020 InMemoryOrderModuleList : _LIST_ENTRY
+0x030 InInitializationOrderModuleList : _LIST_ENTRY
+0x040 EntryInProgress : Ptr64 Void
+0x048 ShutdownInProgress : UChar
+0x050 ShutdownThreadId : Ptr64 Void
```

注意:这些字段在您的机器上应该是静态的，但是如果它们不同也不用担心随着微软增加新的字段，这种情况会随着时间的推移而改变。查看输出，我们可以看到偏移量、名称和类型。这些信息足以创建我们自己的类型。下面的代码检查是否存在名为my_peb_ldr_data的结构体。如果该结构存在，代码将删除该结构，创建一个新的结构，然后从nt!_PEB_LDR_DATA中添加结构成员字段

```
sid = idc.get_struc_id("my_peb_ldr_data")
if sid != idc.BADADDR:
idc.del_struc(sid)
sid = idc.add_struc(-1, "my_peb_ldr_data", 0)
idc.add_struc_member(sid, "length", 0, idc.FF_DWORD, -1, 4)
idc.add_struc_member(sid, "initialized", 4, idc.FF_DWORD, -1, 4)
idc.add_struc_member(sid, "ss_handle", -1, idc.FF_WORD, -1, 2)
idc.add_struc_member(sid, "in_load_order_module_list", -1, idc.FF_DATA, -1, 10)
idc.add_struc_member(sid, "in_memory_order_module_list", -1, idc.FF_QWORD +
idc.FF_WORD, -1, 10)
idc.add_struc_member(sid, "in_initialization_order_module_list", -1, idc.FF_QWORD +
idc.FF_WORD, -1, 10)
idc.add_struc_member(sid, "entry_in_progress", -1, idc.FF_QWORD, -1, 8)
idc.add_struc_member(sid, "shutdown_in_progress", -1, idc.FF_WORD, -1, 2)
idc.add_struc_member(sid, "shutdown_thread_id", -1, idc.FF_QWORD, -1, 8)
```

代码中的第一步，调用idc.get_struc_id(struct_name)以按名称返回struct的id

如果有一个结构体的名称不是“my_peb_ldr_data”，idc.get_struct_id返回idc.BADADDR

如果struct id不是idc.BADADDR，然后我们就知道了一个名为“my_peb_ldr_data”已经存在。

在这个例子中，我们通过调用idc.del_struc(sid)来删除这个结构体。

它接受结构id的一个参数。要创建一个结构，代码调用idc。add_struc(指数、名称、is_union)

第一个参数是新结构的索引

idc.import_type，最好的做法是传递-1的值。这指定IDA应该为id使用下一个最大的索引。

第二个参数传递给idc.add_struc是结构体的名称。

is_union的第三个参数是一个bool值，它定义新创建的结构体是否是一个union

在上面的代码中，我们传递一个值0来指定它不是一个联合。可以通过调用idc来标记该结构的成员。add_struc_member(sid, name, offset, flag, typeid, nbytes)。

注意:idc.add_struc_member有更多的参数，但由于它们用于更复杂的定义，我们将不讨论它们。

如果您对如何创建更复杂的定义感兴趣，我建议稍后深入研究IDAPython源代码。第一个参数是先前分配给变量sid的结构id

第二个参数是成员名的字符串。第三个参数是偏移量。偏移量可以是-1加到结构的末尾，也可以是一个整数值来指定偏移量。第四个参数是标志。标志指定数据类型(字、浮点数等)。可用的标志数据类型如下所示。

```
FF_BYTE 0x00000000 // byte
FF_WORD 0x10000000 // word
FF_DWORD 0x20000000 // dword
FF_QWORD 0x30000000 // qword
FF_TBYTE 0x40000000 // tbyte
FF_STRLIT 0x50000000 // ASCII ?
FF_STRUCT 0x60000000 // Struct ?
FF_OWORD 0x70000000 // octaword (16 bytes/128 bits)
FF_FLOAT 0x80000000 // float
FF_DOUBLE 0x90000000 // double
FF_PACKREAL 0xA0000000 // packed decimal real
FF_ALIGN 0xB0000000 // alignment directive
FF_CUSTOM 0xD0000000 // custom data type
FF_YWORD 0xE0000000 // ymm word (32 bytes/256 bits)
FF_ZWORD 0xF0000000 // zmm word (64 bytes/512 bits)
FF_DATA 0x400  // data
```

第五个参数是typeid，用于更复杂的定义。在我们的例子中，它的值为-1。最后一个参数是要分配的字节数(nbyte)。重要的是标志和nbytes的大小相等。

如果一个双字带有idc标志。如果使用FF_DWORD，则必须指定大小为4。如果没有，则IDA不会创建该成员。这可能是一个棘手的bug，因为IDA不会抛出任何警告。

可以使用标志的组合。例如idc.FF_QWORD + idc.FF_WORD用于在建“in_memory_order_module_list”时指定大小为10个成员

如果标志为idc。FF_DATA被传递，而不需要组合和添加其他标志，任何大小都可以使用。如果我们查看IDA中新创建的结构，就会看到以下情况

结构窗口。

```
00000000 my_peb_ldr_data struc ; (sizeof=0x3A, mappedto_139)
00000000 length dd ?
00000004 initialized dd ?
00000008 ss_handle dw ?
0000000A in_load_order_module_list db 10 dup(?)
00000014 in_memory_order_module_list dt ?
0000001E in_initialization_order_module_list dt ?
00000028 entry_in_progress dq ?
00000030 shutdown_in_progress dw ?
```

## 枚举

枚举类型的简化描述：这是一种使用符号常量来表示有意义的名称的方法，在调用系统api时，枚举类型(又名枚举)是很常见的。在Windows上调用CreateFileA时，对GENERIC_READ的期望访问表示为常量0x80000000

不幸的是，在编译过程中这些名称会被删除。

用有意义的名称重新填充常量有助于逆向工程过程。

当逆向工程恶意软件时，经常会看到代表API名称散列的常量。这种技术用于混淆来自静态分析的API调用。下面的代码是该技术的一个示例。

```
seg000:00000018 push 0CA2BD06Bh ; ROR 13 hash of CreateThread
seg000:0000001D push dword ptr [ebp-4]
seg000:00000020 call lookup_hash
seg000:00000025 push 0
seg000:00000027 push 0
seg000:00000029 push 0
seg000:0000002B push 4C30D0h ; StartAddress
seg000:00000030 push 0
seg000:00000032 push 0
seg000:00000034 call eax ; CreateThread
```

0xCA2BD06B是CreateThread的散列值。

他哈希是通过循环每个字符、使用ROR将字节的位移动13位并存储结果来创建哈希的组合来创建的

这种技术通常被称为z0mbie哈希或ROR-13。因为哈希在某种程度上是“CreateThread”的符号名称，所以它是一个何时使用枚举的实际例子。

因为我们已经知道散列0xCA2BD06B是字符串“CreateThread”，我们可以只创建enum，如果我们不知道哈希表示的API名称呢?我们需要一些方法来散列某些Windows DLL中导出的所有符号名称。

为了简单起见，我们先认定DLL是kernel32.dll。要导出kernel32.dll中的符号名，我们可以使用pefile。

有关使用pefile最常见的用例的简短示例，请参阅附录。

然后我们需要一种复制哈希算法的方法。对于下面的代码，我们将使用Rolf Rolles的修改版本(参见下一节)z0mbie散列和pefile的实现。该代码被设计为便于读者修改以匹配任何哈希或添加所有哈希

```
import pefile
def ror32(val, amt):
return ((val >> amt) & 0xffffffff) | ((val << (32 - amt)) & 0xffffffff)
def add32(val, amt):
return (val + amt) & 0xffffffff
def z0mbie_hash(name):
hash = 0
for char in name:
hash = add32(ror32(hash, 13), ord(char) & 0xff)
return hash
def get_name_from_hash(file_name, hash):
pe = pefile.PE(file_name)
for exp in pe.DIRECTORY_ENTRY_EXPORT.symbols:
if z0mbie_hash(exp.name) == hash:
return exp.name
api_name = get_name_from_hash("kernel32.dll", 0xCA2BD06B)
if api_name:
id = idc.add_enum(-1, "z0mbie_hashes", idaapi.hexflag())
idc.add_enum_member(id, api_name, 0xCA2BD06B, -1)
```

第一行将pefile导入IDA。

两个函数ror32和add32负责复制ROR指令。函数z0mbie_hash(name)接受要散列的字符串的一个参数，并返回散列。最后一个函数get_name_from_hash(file_path, hash)接受两个参数。第一个参数是要对符号进行哈希处理的DLL的文件路径。第二个参数是我们的哈希值，函数返回字符串名称

这个函数的第一行调用pefile.PE(file_path)来加载和解析kernel32.dll，PE文件实例被保存到变量PE中。

通过循环pe.DIRECTORY_ENTRY_EXPORT中的每一项来遍历DLL中的每个符号。

此字段包含DLL中每个导出符号的名称、地址和其他属性。通过调用z0mbie_hash(exp.name)对符号名称进行散列处理，然后进行比较。

如果匹配，则返回符号名并分配给api_name

在代码的这一点上，就是创建和添加enum完成的时候。添加enum的第一步是创建enum id。这是通过调用idc来完成的。add_enum(idx, name, flag)。第一个参数是新enum的idx或序列号。值为-1将分配下一个可用id。

第二个参数是enum的名称。最后一个参数是标志idaapi.hexflag()。

在执行代码后，如果我们按下快捷键M，同时在IDA中突出显示值0xCA2BD06B，我们将看到字符串“CreateThread”作为一个符号常量选项

下面的代码是我们之前看到的哈希现在是一个符号常量的代码。

```
seg000:00000015 mov [ebp-4], ebx
seg000:00000018 push CreateThread ; ROR 13 hash of CreateThread
seg000:0000001D push dword ptr [ebp-4]
```

## 交叉引用

能够定位数据或代码的交叉引用(即xref)是一项常见的分析任务

定位xref很重要，因为它们提供了使用特定数据或调用函数的位置

例如，如果我们想要定位所有的地址

WriteFile被调用。通过使用Xrefs，我们所需要做的就是定位的地址

按名称写文件，然后查找到它的所有xrefs。

```
Python>wf_addr = idc.get_name_ea_simple("WriteFile")
Python>print("0x%x %s" % (wf_addr, idc.generate_disasm_line(wf_addr, 0)))
0x1000e1b8 extrn WriteFile:dword
Python>for addr in idautils.CodeRefsTo(wf_addr, 0):\
print("0x%x %s" % (addr, idc.generate_disasm_line(addr, 0)))
0x10004932 call ds:WriteFile
0x10005c38 call ds:WriteFile
0x10007458 call ds:WriteFile
```

在第一行中，我们使用idc.get_name_ea_simple(str)获取API WriteFile的地址。

这个函数返回API的地址，我们打印出WriteFile的地址及其字符串表示形式，然后通过调用idautils.CodeRefsTo (ea,flow)循环遍历所有的代码交叉引用。它返回一个可循环的迭代器。ea是我们想要交叉引用的地址,参数flow是bool类型。它用于指定是否遵循正常的代码流。然后显示对该地址的每个交叉引用。

一个快速注释idc.get_name_ea_simple(str)的使用

IDB中所有重命名的函数和api都可以通过调用idautils.Names()来访问，这个函数返回一个迭代器对象，可以通过循环来打印或访问名称。每个命名项都是(ea， str_name)

```
Python>[x for x in Names()]
[(268439552, 'SetEventCreateThread'), (268439615, 'StartAddress'), (268441102,
'SetSleepClose'),....]
```

如果我们想要得到代码被引用的地方，我们将使用idautisl.CodeRefsFrom(ea,flow)。例如，让我们获取引用0x10004932的地址。

```
Python>ea = 0x10004932
Python>print("0x%x %s" % (ea, idc.generate_disasm_line(ea, 0)))
0x10004932 call ds:WriteFile
Python>for addr in idautils.CodeRefsFrom(ea, 0):\
print("0x%x %s" % (addr, idc.generate_disasm_line(addr, 0)))
Python>
0x1000e1b8 extrn WriteFile:dword
```

如果我们回顾一下这些文字。CodeRefsTo(ea, flow)示例中我们看到地址0x10004932是一个要写文件的地址

idautils.CodeRefsTo (ea,flow)和idautils.CodeRefsFrom(ea, flow)用于搜索代码之间的交叉引用

一个使用idautils.CodeRefsTo(ea, flow)的限制是动态导入的api

然后手动重命名，不显示为代码交叉引用。

假设我们使用 idc.set_name(ea, name, SN_CHECK) 手动重命名dword地址为“RtlCompareMemory”

```
Python>print("0x%x" % (ea)
0xa26c78
Python>idc.set_name(ea, "RtlCompareMemory", SN_CHECK)
True
Python>for addr in idautils.CodeRefsTo(ea, 0):\
print("0x%x %s" % (addr, idc.generate_disasm_line(addr, 0)))
```

ida没有将这些api标记为代码交叉引用，稍后我们将描述一种获取所有交叉引用的通用技术,如果我们想要搜索对数据和从数据的交叉引用，我们可以使用idautils.DataRefsTo(ea)或idautils.DataRefsFrom(ea)。

```
Python>print("0x%x %s" % (ea, idc.generate_disasm_line(ea, 0)))
0x1000e3ec db 'vnc32',0
Python>for addr in idautils.DataRefsTo(ea):\
print("0x%x %s" % (addr, idc.generate_disasm_line(addr, 0)))
0x100038ac push offset aVnc32 ; "vnc32"
```

idautils.Datarefsto (ea)接受地址的参数，并返回与数据交叉引用的所有地址的迭代器。

```
Python>print("0x%x %s" % (ea, idc.generate_disasm_line(ea, 0)))
0x100038ac push offset aVnc32 ; "vnc32"
Python>for addr in idautils.DataRefsFrom(ea):\
print("0x%x %s" % (addr, idc.generate_disasm_line(addr, 0)))
0x1000e3ec db 'vnc32',0
```

要做相反的事情，并显示地址，我们调用idautils.DataRefsFrom(ea)的，将地址作为参数传递。

它返回一个迭代器，包含所有交叉引用回数据的地址。代码和数据的不同用法可能会有点令人困惑。让我们来描述一种更通用的技术。

这种方法可以通过调用一个函数来获得对一个地址的所有交叉引用。我们可以用idautils.XrefsTo(ea, flags=0)得到所有地址的交叉引用。并通过调用idautils.XrefsFrom (ea,flag= 0)从一个地址获取所有的交叉引用。

```
Python>print("0x%x %s" % (ea, idc.generate_disasm_line(ea, 0)))
0x1000eee0 unicode 0, <Path>,0
Python>for xref in idautils.XrefsTo(ea, 1):
print("%i %s 0x%x 0x%x %i" % (xref.type, idautils.XrefTypeName(xref.type),
xref.frm, xref.to, xref.iscode))
Python>
1 Data_Offset 0x1000ac0d 0x1000eee0 0
Python>>print("0x%x %s" % (xref.frm, idc.generate_disasm_line(xref.frm, 0))
0x1000ac0d push offset KeyName ; "Path"
```

第一行显示了我们的地址和一个名为“Path”的字符串。

我们使用idautils.XrefsTo(ea, 1)获取对字符串的所有交叉引用

然后我们使用 xref.type打印xrefs类型值的类型。

idautils.XrefTypeName(xref.type)用于打印该类型的字符串表示。有12种不同的文档引用类型值。该值可以在左边看到，其对应的名称可以在下面看到。

```
0 = 'Data_Unknown'
1 = 'Data_Offset'
2 = 'Data_Write'
3 = 'Data_Read'
4 = 'Data_Text'
5 = 'Data_Informational'
16 = 'Code_Far_Call'
17 = 'Code_Near_Call'
18 = 'Code_Far_Jump'
19 = 'Code_Near_Jump'
20 = 'Code_User'
21 = 'Ordinary_Flow'
```

xfrm打印出from地址并且xref.to打印出两个地址

  xref.iscode打印xref是否在代码段中。在之前的例子中，我们将idautils.XrefsTo(ea, 1)的flag设置为1。如果标志为零，则显示任何交叉引用。我们可以使用下面的汇编块来说明这一点。

```
.text:1000AAF6 jnb short loc_1000AB02 ; XREF
.text:1000AAF8 mov eax, [ebx+0Ch]
.text:1000AAFB mov ecx, [esi]
.text:1000AAFD sub eax, edi
.text:1000AAFF mov [edi+ecx], eax
.text:1000AB02
.text:1000AB02 loc_1000AB02: ; ea is here()
.text:1000AB02 mov byte ptr [ebx], 1
```

我们的光标在0x1000AB02。这个地址有一个来自0x1000AAF6的交叉引用，但它也有第二个来自0x1000AAF6的交叉引用。

```
Python>print("0x%x %s" % (ea, idc.generate_disasm_line(ea, 0)))
0x1000ab02 mov byte ptr [ebx], 1
Python>for xref in idautils.XrefsTo(ea, 1):
print("%i %s 0x%x 0x%x %i" % (xref.type, idautils.XrefTypeName(xref.type),
xref.frm, xref.to, xref.iscode))
Python>
19 Code_Near_Jump 0x1000aaf6 0x1000ab02 1
Python>for xref in idautils.XrefsTo(ea, 0):
print("%i %s 0x%x 0x%x %i" % (xref.type, idautils.XrefTypeName(xref.type),
xref.frm, xref.to, xref.iscode))
Python>
21 Ordinary_Flow 0x1000aaff 0x1000ab02 1
19 Code_Near_Jump 0x1000aaf6 0x1000ab02 1
```

第二个交叉引用是从0x1000AAFF到0x1000AB02交叉引用不一定由分支指令引起。

它们也可能由普通代码流引起。如果将该标志设置为1，则不会添加普通流引用类型。

现在回到前面的RtlCompareMemory示例。

我们可以使用 idautils.XrefsTo(ea, flow)获取所有交叉引用

```
Python>print("0x%x" % ea)
0xa26c78
Python>idc.set_name(ea, "RtlCompareMemory", SN_CHECK)
True
Python>for xref in idautils.XrefsTo(ea, 1):
print("%i %s 0x%x 0x%x %i" % (xref.type, idautils.XrefTypeName(xref.type),
xref.frm, xref.to, xref.iscode))
Python>
3 Data_Read 0xa142a3 0xa26c78 0
3 Data_Read 0xa143e8 0xa26c78 0
3 Data_Read 0xa162da 0xa26c78 0
```

获取所有的交叉引用有时会有点冗长。

```
Python>print("0x%x %s" % (ea, idc.generate_disasm_line(ea, 0)))
0xa21138 extrn GetProcessHeap:dword
Python> for xref in idautils.XrefsTo(ea, 1):
print("%i %s 0x%x 0x%x %i" % (xref.type, idautils.XrefTypeName(xref.type),
xref.frm, xref.to, xref.iscode))
Python>
17 Code_Near_Call 0xa143b0 0xa21138 1
17 Code_Near_Call 0xa1bb1b 0xa21138 1
3 Data_Read 0xa143b0 0xa21138 0
3 Data_Read 0xa1bb1b 0xa21138 0
Python>print(idc.generate_disasm_line(0xa143b0, 0))
call ds:GetProcessHeap
```

冗长来自于添加到xrefs中的Data_Read和Code_Near

获取所有地址并将它们添加到一个集合对精简所有地址非常有用

```python
def get_to_xrefs(ea):
  xref_set = set([])
   for xref in idautils.XrefsTo(ea, 1):
    xref_set.add(xref.frm)
   return xref_set
def get_frm_xrefs(ea):
 xref_set = set([])
 for xref in idautils.XrefsFrom(ea, 1):
  xref_set.add(xref.to)
 return xref_set
```

缩减函数的示例GetProcessHeap示例

```
Python>print("0x%x %s" % (ea, idc.generate_disasm_line(ea, 0)))
0xa21138 extrn GetProcessHeap:dword
Python>get_to_xrefs(ea)
set([10568624, 10599195])
Python>[("0x%x" % x) for x in get_to_xrefs(ea)]
['0xa143b0', '0xa1bb1b']
```

## 搜索

我们已经通过遍历所有已知的函数或指令来讨论了一些基本的搜索。

这很有用，但有时我们需要搜索特定的字节，如0x55 0x8B 0xEC

这个字节模式是经典的函数prologue push ebp, mov ebp, esp

要搜索字节或二进制模式，可以使用ida_search。find_binary(start, end, searchstr, radiux, sflag)。

start和end定义了要搜索的范围，searchstr是我们要搜索的模式。这个radiux是当处理器模块时使用的基数。

这个题目超出了这本书的范围。

现在，基数字段的值是16。sflag是方向或状态。有几种不同类型的标志。名称和值如下所示。

```
SEARCH_UP = 0
SEARCH_DOWN = 1
SEARCH_NEXT = 2
SEARCH_CASE = 4
SEARCH_REGEX = 8
SEARCH_NOBRK = 16
SEARCH_NOSHOW = 32
SEARCH_IDENT = 128
SEARCH_BRK = 256
```

并非所有这些标记都值得一读，但我们可以接触到最常用的标记。

SEARCH_UP和SEARCH_DOWN是用来选择我们希望搜索遵循的方向。

•SEARCH_NEXT用于获取下一个找到的对象。

•SEARCH_CASE用于指定区分大小写。

•SEARCH_NOSHOW不显示搜索进度

以前的IDA版本包含一个SEARCH_UNICODE标记，用于搜索Unicode字符串

在搜索字符时不再需要此标志，因为IDA同时搜索ASCII和

Unicode默认情况下。让我们快速浏览一下查找前面提到的函数序言字节模式。

```
Python>pattern = '55 8B EC'
addr = idc.get_inf_attr(INF_MIN_EA)
pattern = '55 8B EC'
addr = idc.get_inf_attr(INF_MIN_EA)
for x in range(0, 5):
addr = ida_search.find_binary(addr, idc.BADADDR, pattern,
16,ida_search.SEARCH_DOWN)
if addr != idc.BADADDR:
print("0x%x %s" % (addr, idc.generate_disasm_line(addr, 0)))
Python>
0x401000 push ebp
0x401000 push ebp
0x401000 push ebp
0x401000 push ebp
0x401000 push ebp
```

第一行定义了我们的搜索模式。搜索模式可以是以0x55 0x8B 0xEC中的0x开头的十六进制格式，也可以是IDA的十六进制视图55 8B EC中出现的字节格式。

这个格式\x55\x8B\xEC不能使用，除非我们使用ida_search.(ea, y, x, searchstr, sflag)。idc.get_inf_attr(INF_MIN_EA)用于获取可执行文件中的第一个地址

然后我们分配使用ida_search.find_binary(start, end, searchstr, radiux, sflag)的返回值。存储到addr变量

在搜索时，验证搜索是否找到了模式是很重要的

这是通过比较addr和idc.BADADDR来测试的然后我们打印地址和拆解

注意到地址没有增加吗?这是因为我们没有传递SEARCH_NEXT标志。如果没有传递此标志，则使用当前地址来搜索模式。如果最后一个地址包含我们的字节模式，搜索将永远不会递增传递给它。下面是修正后的版本，在SEARCH_DOWN之前添加了SEARCH_NEXT标志。

```
Python> pattern = '55 8B EC'
addr = idc.get_inf_attr(INF_MIN_EA)
pattern = '55 8B EC'
addr = idc.get_inf_attr(INF_MIN_EA)
for x in range(0, 5):
addr = ida_search.find_binary(addr, idc.BADADDR, pattern, 16,
ida_search.SEARCH_NEXT|ida_search.SEARCH_DOWN)
if addr != idc.BADADDR:
print("0x%x %s" % (addr, idc.generate_disasm_line(addr, 0))
Python>
0x401000 push ebp
0x401040 push ebp
0x401070 push ebp
0x4010e0 push ebp
0x401150 push ebp
```

搜索字节模式是有用的，但有时我们可能想要搜索诸如

“chrome.dll”。我们可以使用[hex(y) for y in bytearray("chrome.dll")]将字符串转换为十六进制字节，但这有点难看。此外，如果字符串是Unicode，我们就必须考虑这种编码。最简单的方法是使用ida_search.find_text(ea, y, x, searchstr, sflag)。这些字段中的大多数看起来应该很熟悉，因为它们与ida_search.find_binary相同

ea为起始地址。y是ea处的直线数x是直线上的坐标。

字段y和x通常被分配为0。searchstr是搜索的模式，sflag定义了搜索的方向和类型。例如，我们可以搜索所有出现的字符串“Accept”。字符串窗口shift+F12中的任何字符串都可以用于这个示例搜索。

```
Python>cur_addr = idc.get_inf_attr(INF_MIN_EA)
for x in range(0, 5):
cur_addr = ida_search.find_text(cur_addr, 0, 0, "Accept",
ida_search.SEARCH_DOWN)
if addr == idc.BADADDR:
break
print("0x%x %s" % (cur_addr, idc.generate_disasm_line(cur_addr, 0)))
cur_addr = idc.next_head(cur_addr)
Python>
0x40da72 push offset aAcceptEncoding; "Accept-Encoding:\n"
0x40face push offset aHttp1_1Accept; " HTTP/1.1\r\nAccept: */* \r\n "
0x40fadf push offset aAcceptLanguage; "Accept-Language: ru \r\n"
...
0x423c00 db 'Accept',0
0x423c14 db 'Accept-Language',0
0x423c24 db 'Accept-Encoding',0
0x423ca4 db 'Accept-Ranges',0
```

我们使用idc.get_inf_attr(INF_MIN_EA)获取最小地址，并将其分配给名为cur_addr的变量

通过调用idc.get_inf_attr(INF_MAX_EA)并将返回值分配给名为the end的变量，对最大地址再次进行类似的操作

由于我们不知道该字符串出现了多少次，我们需要检查搜索是否继续下去，并且小于最大地址。然后，我们分配ida_search的返回值。查找当前地址

因为我们在手动递增地址通过调用idc.next_head (ea)

我们不需要SEARCH_NEXT标志。我们手动将当前地址增加到下一行的原因是，一个字符串可以在一行中出现多次。这使得获取下一个字符串的地址变得很棘手。

除了前面描述的模式搜索之外，还有两个函数可以用来查找其他类型。

find api的命名约定使得很容易推断其整体功能。
在讨论寻找不同类型之前，我们先看看通过地址识别类型。
有一个以“is”开头的api子集可以用来确定地址的类型。
api返回一个布尔值True或False

idc.is_code(f)

如果IDA将地址标记为code则返回True。
idc.is_data(f)

如果IDA将地址标记为数据则返回True。
idc.is_tail(f)

如果IDA将地址标记为tail则返回True。
idc.is_unknown(f)

如果IDA将地址标记为未知，则返回True。当IDA没有识别地址是代码还是数据时，就使用这种类型。
idc.is_head(f)

如果IDA将地址标记为head则返回True。
f 对我们来说是新鲜的。
而不是传递一个地址，我们首先需要获得内部标志表示，然后将它传递给我们的idc。
is_*函数集。
为了获得内部标志，我们使用idc.get_full_flags(ea)。
现在我们已经了解了如何使用函数和不同类型的基本知识，让我们做一个简单的示例

```
Python>print("0x%x %s" % (ea, idc.generate_disasm_line(ea, 0)))
0x10001000 push ebp
Python>idc.is_code(idc.get_full_flags(ea))
True
```

ida_search.find_code(ea, flag)*：*

它用于查找下一个标记为代码的地址。如果我们想要找到数据块的末尾，这是很有用的。如果ea是一个已经标记为代码的地址，它将返回下一个地址。该标志如前面在ida_search.find_text中描述的那样使用。

```
Python>print("0x%x %s" % (ea, idc.generate_disasm_line(ea, 0)))
0x4140e8 dd offset dword_4140EC
Python>addr = ida_search.find_code(ea, SEARCH_DOWN|SEARCH_NEXT)
Python>print("0x%x %s" % (addr, idc.generate_disasm_line(addr, 0)))
0x41410c push ebx
```

我们可以看到ea是一些数据的地址0x4140e8。我们指定ida_search的返回值。查找代码(ea, SEARCH_DOWN|SEARCH_NEXT)到addr。

然后我们打印addr和它的反汇编。通过调用这个函数，我们跳过了36个字节的数据，以获得标记为代码的部分的开始

ida_search.find_data(ea, flag)

它完全被用作ida_search。find_code的不同之处在于它返回一个标记为数据块的下一个地址的开始。如果我们反转前面的场景，从代码的地址开始并搜索以找到数据的开始。

```
Python>print("0x%x %s" % (ea, idc.generate_disasm_line(ea, 0)))
0x41410c push ebx
Python>addr = ida_search.find_data(ea, SEARCH_UP|SEARCH_NEXT)
Python>print("0x%x %s" % (addr, idc.generate_disasm_line(addr, 0)))
0x4140ec dd 49540E0Eh, 746E6564h, 4570614Dh, 7972746Eh, 8, 1, 4010BCh
```

唯一与前面的例子稍有不同的是方向

SEARCH_UP|SEARCH_NEXT和搜索数据

*ida_search.find_unknown(ea, flag)*

此函数用于查找IDA没有标识为代码或数据的字节的地址。未知的类型需要进一步的手动分析，或者通过视觉分析，或者通过脚本分析

```
Python>print("0x%x %s" % (ea, idc.generate_disasm_line(ea, 0)))
0x406a05 jge short loc_406A3A
Python>addr = ida_search.find_unknown(ea, SEARCH_DOWN)
Python>print("0x%x %s" % (addr, idc.generate_disasm_line(addr, 0))))
0x41b004 db 0DFh ; ?
```

*ida_search.find_defined(ea, flag)*

它用于查找IDA标识为代码或数据的地址。

```
0x41b900 db ? ;
Python>addr = ida_search.find_defined(ea, SEARCH_UP)
Python>print("0x%x %s" % (addr, idc.generate_disasm_line(addr, 0))))
0x41b5f4 dd ?
```

这可能看起来没有任何真正的价值，但如果我们要打印addr的交叉引用，我们将看到它正在被使用

```
Python>for xref in idautils.XrefsTo(addr, 1):
print("0x%x %s" % (xref.frm, idc.generate_disasm_line(addr, 0))))
Python>
0x4069c3 mov eax, dword_41B5F4[ecx*4]
```

ida_search.find_imm(ea, flag, value)



我们可能希望搜索特定的值，而不是搜索类型。例如，我们有一种感觉，代码调用rand来生成一个随机数，但我们找不到代码

如果我们知道rand使用值0x343FD作为种子，我们可以通过ida_search.find_imm(get_inf_attr(INF_MIN_EA)，SEARCH_DOWN, 0x343FD)来搜索这个数字。

```
Python>addr = ida_search.find_imm(get_inf_attr(INF_MIN_EA), SEARCH_DOWN, 0x343FD )
Python>addr
[268453092, 0]
Python>print("0x%x %s %x" % (addr[0], idc.generate_disasm_line(addr[0], 0),
addr[1]))
0x100044e4 imul eax, 343FDh 0
```

在第一行中，我们通过get_inf_attr(INF_MIN_EA)传递最小地址，向下搜索，然后搜索值0x343FD,而不是像前面的Find api ida_search那样返回一个地址。find_imm返回元组。元组中的第一项是地址，第二项是操作数。比如idc的回归。第一个操作数从0开始。当我们打印地址和反汇编时，我们可以看到这个值是第二个操作数。如果我们想搜索一个即时值的所有用途，我们可以执行以下操作。

```
Python>addr = idc.get_inf_attr(INF_MIN_EA)
while True:
addr, operand = ida_search.find_imm(addr, SEARCH_DOWN | SEARCH_NEXT, 4)
if addr == BADADDR:
break
print("0x%x %s Operand %i" % (addr, idc.generate_disasm_line(addr, 0),
operand))
Python>
0x402434 dd 9, 0FF0Bh, 0Ch, 0FF0Dh, 0Dh, 0FF13h, 13h, 0FF1Bh, 1Bh Operand 0
0x40acee cmp eax, 7Ah Operand 1
0x40b943 push 7Ah Operand 0
0x424a91 cmp eax, 7Ah Operand 1
0x424b3d cmp eax, 7Ah Operand 1
0x425507 cmp eax, 7Ah Operand 1
```

大部分代码看起来应该很熟悉，但是因为我们要搜索多个值，所以它使用了一个while循环和SEARCH_DOWN|SEARCH_NEXT标志。

使用ida_search进行搜索时存在一些情况。find_*可能会有点慢。Yara可以用来加速在IDA中的搜索。请参阅Yara章节，

## 数据选取

我们并不总是需要搜索代码或数据。在某些情况下，我们已经知道代码或数据的位置，但是我们想要选择它进行分析。在这种情况下，我们可能只想突出显示代码并开始在IDAPython中使用它。为了获得所选数据的边界，我们可以使用idc.read_selection_start()来获得开始，使用idc.read_selection_end()来获得结束。假设我们选择了下面的代码。

```
.text:00408E46 push ebp
.text:00408E47 mov ebp, esp
.text:00408E49 mov al, byte ptr dword_42A508
.text:00408E4E sub esp, 78h
.text:00408E51 test al, 10h
.text:00408E53 jz short loc_408E78
.text:00408E55 lea eax, [ebp+Data]
```

我们可以使用下面的代码来打印地址

```
Python>start = idc.read_selection_start()
Python>print("0x%x" % start)
0x408e46
Python>end = idc.read_selection_end()
Python>print("0x%x" % end)
0x408e58
```

我们将idc.read_selection_start()的返回值指定为start。这是第一个选择的地址的地址。然后使用idc.read_selection_end()的返回值，并将其赋值给end

需要注意的一点是end不是最后一个选择的地址，而是下一个地址的开始。如果我们希望只进行一次API调用，我们可以使用idaapi.read_selection()

## 注释和重命名

添加注释、重命名函数以及与程序集交互是理解代码的最佳方式之一。随着时间的推移，一些互动变得多余。在这种情况下，自动化流程是很有用的

在我们回顾一些例子之前，我们应该首先讨论注释和重命名的基础知识，两种类型的评论。第一个是常规注释，第二个是可重复注释。一个常规注释作为文本常规注释出现在地址0x041136B。

一个可重复的注释可以在地址0x0411372, 0x0411386和0x0411392看到。只有最后注释是手动输入的注释。其他注释在指令出现时出现引用包含可重复注释的地址(如分支条件)

```
00411365 mov [ebp+var_214], eax
0041136B cmp [ebp+var_214], 0 ; regular comment
00411372 jnz short loc_411392 ; repeatable comment
00411374 push offset sub_4110E0
00411379 call sub_40D060
0041137E add esp, 4
00411381 movzx edx, al
00411384 test edx, edx
00411386 jz short loc_411392 ; repeatable comment
00411388 mov dword_436B80, 1
00411392
00411392 loc_411392:
00411392
00411392 mov dword_436B88, 1 ; repeatable comment
0041139C push offset sub_4112C0
```

为了添加注释，我们使用idc.set_cmt(ea, comment,0)，对于可重复的comment，我们使用idc.set_cmt(ea, comment, 1)。ea是地址，comment是我们想要添加的字符串，0指定注释是不可重复的，1表示注释是可重复的。下面的代码在每次指令用异或将寄存器或值置零时添加注释

```
for func in idautils.Functions():
flags = idc.get_func_attr(func, FUNCATTR_FLAGS)
# skip library & thunk functions
if flags & FUNC_LIB or flags & FUNC_THUNK:
continue
dism_addr = list(idautils.FuncItems(func))
for ea in dism_addr:
 if idc.print_insn_mnem(ea) == "xor":
if idc.print_operand(ea, 0) == idc.print_operand(ea, 1):
comment = "%s = 0" % (idc.print_operand(ea, 0))
idc.set_cmt(ea, comment, 0)
```

如前所述，我们通过调用idautils.Functions()来循环遍历所有函数，并通过调用list(idautils.FuncItems(func))来循环遍历所有指令

我们使用idc.print_insn_mnem(ea)读取助记符，并检查它是否等于xor。

如果是，我们验证操作数与idc.print_operand (ea,n)。如果相等，则创建一个带有操作数的字符串，然后添加一个不可重复的注释。

```
0040B0F7 xor al, al ; al = 0
0040B0F9 jmp short loc_40B163
```

为了增加一个可重复的注释，我们用idc.set_cmt(ea, comment, 0) 替换idc.set_cmt (ea,comment,1)

这可能更有用一些，因为我们将看到对分支的引用，这些分支将值归零并可能返回0。要得到comment，我们只需使用idc.get_cmt (ea)。ea是包含注释的地址，并且repeatable的bool值为True(1)或False(0)。

```
Python>print("0x%x %s" % (ea, idc.generate_disasm_line(ea, 0)))
0x40b0f7 xor al, al ; al = 0
Python>idc.get_cmt(ea, False)
al = 0
```

如果该comment是可重复的，我们用idc.get_cmt(ea, False)替换idc.get_cmt (ea,ture)

为了添加函数注释，我们使用idc.set_func_cmt(ea, cmt, repeatable)，为了得到一个函数注释，我们调用idc.get_func_cmt (ea、repeatable)

ea可以是函数开始和结束边界内的任何地址。cmt是我们想要添加的字符串注释，可重复是一个布尔值，它标记注释是否可重复。

如果注释是不可重复的，则表示为0或False;如果注释是可重复的，则表示为1或True。使函数comment为可重复的，可以在函数被交叉引用、调用或在IDA的GUI中查看时添加注释

```
Python>print("0x%x %s" % (ea, idc.generate_disasm_line(ea, 0)))
0x401040 push ebp
Python>idc.get_func_name(ea)
sub_401040

Python>idc.set_func_cmt(ea, "check out later", 1)
True
```

我们在头几行中打印地址、反汇编和函数名。然后我们使用idc。set_func_cmt(ea，注释，repeatable)设置“稍后检出”的可重复注释。如果我们看一下函数的开头，就会看到我们的注释

```
00401040 ; check out later
00401040 ; Attributes: bp-based frame
00401040
00401040 sub_401040 proc near
00401040 .
00401040 var_4 = dword ptr -4
00401040 arg_0 = dword ptr 8
00401040
00401040 push ebp
00401041 mov ebp, esp
00401043 push ecx
00401044 push 723EB0D5h
```

由于注释是可重复的，所以只要查看函数就会显示注释。这是添加关于某个函数的提醒或注释的好地方。

```
00401C07 push ecx
00401C08 call sub_401040 ; check out later
00401C0D add esp, 4
```

重命名函数和地址是一项常见的自动化任务，特别是在处理位置独立代码(PIC)、封装器或包装函数时。这在PIC或未打包代码中很常见的原因是导入表可能不在转储中。在包装器函数的情况下，完整函数只是调用一个API。

```
10005B3E sub_10005B3E proc near
10005B3E
10005B3E dwBytes = dword ptr 8
10005B3E
10005B3E push ebp
10005B3F mov ebp, esp
10005B41 push [ebp+dwBytes] ; dwBytes
10005B44 push 8 ; dwFlags
45
10005B46 push hHeap ; hHeap
10005B4C call ds:HeapAlloc
10005B52 pop ebp
10005B53 retn
10005B53 sub_10005B3E endp
```

在上面的代码中，这个函数可以被称为“w_HeapAlloc”。w_是包装器的缩写。要重命名一个地址，我们可以使用idc功能。(ea, name, SN_CHECK)。ea是地址，name是字符串名称，比如“w_HeapAlloc”。

要重命名一个函数，ea需要是该函数的第一个地址。要重命名我们的HeapAlloc包装器的函数，我们将使用以下代码

```
Python>print("0x%x %s" % (ea, idc.generate_disasm_line(ea, 0)))
0x10005b3e push ebp
Python>idc.set_name(ea, "w_HeapAlloc", SN_CHECK)
True
```

ea是函数中的第一个地址，名称是“w_HeapAlloc”。

```
10005B3E w_HeapAlloc proc near
10005B3E
10005B3E dwBytes = dword ptr 8
10005B3E
10005B3E push ebp
10005B3F mov ebp, esp
10005B41 push [ebp+dwBytes] ; dwBytes
10005B44 push 8 ; dwFlags
10005B46 push hHeap ; hHeap
10005B4C call ds:HeapAlloc
10005B52 pop ebp
10005B53 retn
10005B53 w_HeapAlloc endp
```

在上面我们可以看到函数已被重命名。要确认它已被重命名，可以使用idc.get_func_name(ea)打印新函数的名称。

```
Python>idc.get_func_name(ea)
w_HeapAlloc
```

要重命名一个操作数，我们首先需要获取它的地址。在地址0x04047B0，我们有一个要重命名的dword。

```
text:004047AD lea ecx, [ecx+0]
.text:004047B0 mov eax, dword_41400C
.text:004047B6 mov ecx, [edi+4BCh]
```

为了得到操作数的值，我们可以使用idc.get_operand_value (ea, n)

```
Python>print("0x%x %s" % (ea, idc.generate_disasm_line(ea, 0)))
0x4047b0 mov eax, dword_41400C
Python>op = idc.get_operand_value(ea, 1)
Python>print("0x%x %s" % (ea, idc.generate_disasm_line(ea, 0)))
0x41400c dd 2
Python>idc.set_name(op, "BETA", SN_CHECK)
True
Python>print("0x%x %s" % (ea, idc.generate_disasm_line(ea, 0)))
0x4047b0 mov eax, BETA[esi]
```

在第一行中，我们打印当前工作地址。调用idc将第二个操作数值dword_41400C赋值给op通过调用idc.get_operand_value (ea)。我们将操作数的地址传递给idc.set_name(ea, name, SN_CHECK)然后打印新重命名的操作数。

既然我们已经有了很好的知识基础，我们就可以使用到目前为止所学的知识来自动化包装器函数的命名。请查看内联注释以了解其逻辑

```
import idautils
def rename_wrapper(name, func_addr):
if idc.set_name(func_addr, name, SN_NOWARN):
print("Function at 0x%x renamed %s" % (func_addr, idc.get_func_name(func)))
else:
print("Rename at 0x%x failed. Function %s is being used." % (func_addr,
name))
return
def check_for_wrapper(func):
flags = idc.get_func_attr(func, FUNCATTR_FLAGS)
# skip library & thunk functions
if flags & FUNC_LIB or flags & FUNC_THUNK:
47
return
dism_addr = list(idautils.FuncItems(func))
# get length of the function
func_length = len(dism_addr)
# if over 32 lines of instruction return
if func_length > 0x20:
return
func_call = 0
instr_cmp = 0
op = None
op_addr = None
op_type = None
# for each instruction in the function
for ea in dism_addr:
m = idc.print_insn_mnem(ea)
if m == 'call' or m == 'jmp':
if m == 'jmp':
temp = idc.get_operand_value(ea, 0)
# ignore jump conditions within the function boundaries
if temp in dism_addr:
continue
func_call += 1
# wrappers should not contain multiple function calls
if func_call == 2:
return
op_addr = idc.get_operand_value(ea, 0)
op_type = idc.get_operand_type(ea, 0)
elif m == 'cmp' or m == 'test':
# wrappers functions should not contain much logic.
instr_cmp += 1
if instr_cmp == 3:
return
else:
continue
# all instructions in the function have been analyzed
if op_addr == None:
return
name = idc.get_name(op_addr, ida_name.GN_VISIBLE)
# skip mangled function names
if "[" in name or "$" in name or "?" in name or "@" in name or name == "":
return
name = "w_" + name
if op_type == 7:
if idc.get_func_attr(op_addr, FUNCATTR_FLAGS) & FUNC_THUNK:
rename_wrapper(name, func)
return
if op_type == 2 or op_type == 6:
rename_wrapper(name, func)
return
for func in idautils.Functions():
check_for_wrapper(func)
```

Example Output

```
Function at 0xa14040 renamed w_HeapFree
Function at 0xa14060 renamed w_HeapAlloc
Function at 0xa14300 renamed w_HeapReAlloc
Rename at 0xa14330 failed. Function w_HeapAlloc is being used.
Rename at 0xa14360 failed. Function w_HeapFree is being used.
Function at 0xa1b040 renamed w_RtlZeroMemory
```

大多数代码应该是熟悉的。一个显著的区别是使用idc.
set_name(ea, name, flag) 和 rename_wrapper。的差异
我们使用这个函数是因为idc.set_name如果函数名已经被使用，将抛出警告对话框。通过传递标志值SN_NOWARN或256，我们避免了对话框。我们可以应用一些逻辑将函数重命名为w_HeapFree_1，但为了简洁起见，我们将省略这一点。

要确定一个函数是否已被重命名，可以使用地址标志。下面的代码是一个已重命名的函数。

```
text:000000014001FF90 func_example proc near ; CODE XREF: sub_140020B52+3A↓p
.text:000000014001FF90 ; sub_140020BEF+3A↓p ...
.text:000000014001FF90
.text:000000014001FF90 var_18 = qword ptr -18h
.text:000000014001FF90 var_10 = dword ptr -10h
.text:000000014001FF90
.text:000000014001FF90 sub rsp, 38h
.text:000000014001FF94 mov eax, cs:dword_1400268D4
49
.text:000000014001FF9A mov r9, cs:DelayLoadFailureHook
```

为了检索标记，我们调用ida_bytes.get_flags(ea)。它接受我们想要检索标志的地址的单个参数。返回的是地址标志，然后将其传递给idc.hasUserName(flags)，以确定该地址是否已被用户重命名。

```
Python>here()
0x14001ff90
Python>ida_bytes.get_flags(here())
0x51005600
Python>idc.hasUserName(ida_bytes.get_flags(here()))
True
```

## 颜色

向IDA的输出添加一点颜色是加快分析过程的一种简单方法

颜色可以用来在视觉上为指令、块或片段添加上下文。当浏览大型函数时，很容易错过一个调用指令，从而错过功能

如果我们给包含call指令的所有行着色，那么快速识别对子函数的调用就会容易得多

为了改变IDB中显示的颜色，我们使用了idc.set_color(ea，what，color)。第一个参数ea是地址。第二个论点是什么。它是用来指明它应该是什么颜色的。它可以是用于着色指令的CIC_ITEM，用于着色函数块的CIC_FUNC，以及用于着色段的CIC_SEGM。color参数接受十六进制颜色代码的整数值。IDA使用的是BGR (0xBBGGRR)的十六进制颜色码格式，而不是RGB (0xRRGGBB)。后一种十六进制颜色代码更普遍，因为它被用于HTML、CSS或SVG中。要用十六进制颜色代码0xDFD9F3给调用指令着色，我们可以使用下面的代码。

```
for func in idautils.Functions():
flags = idc.get_func_attr(func, FUNCATTR_FLAGS)
# skip library & thunk functions
if flags & FUNC_LIB or flags & FUNC_THUNK:
continue
dism_addr = list(idautils.FuncItems(func))
for ea in dism_addr:
if idc.print_insn_mnem(ea) == "call":
idc.set_color(ea, CIC_ITEM ,0xDFD9F3)
```

除了最后一行之外，前面已经描述了所有的代码。

代码循环遍历所有函数和所有指令。如果一条指令包含助记符调用指令，它将改变地址的颜色。

最后一行调用函数idc.set_color使用当前地址作为第一个参数

因为我们只对识别单个指令感兴趣，所以我们将what参数(第二个)定义为CIC_ITEM

最后一个参数是BGR十六进制编码的颜色代码。如果我们要查看一个已经执行了颜色调用脚本的IBD，那么下面的行0x0401469和0x0401473将改变它们的颜色。

```
.text:00401468 push ecx ; int
.text:00401469 call __setmode ; color coded
.text:0040146E lea edx, [esp+40B8h+var_405C]
.text:00401472 push edx
.text:00401473 call constants ; color coded
.text:00401478 push esi ; FILE *
```

为了检索一个地址的十六进制颜色代码，我们使用功能idc.get_color(ea,what)。

第一个参数ea是地址。第二个参数是我们想要为之获取颜色的项的类型。它使用与前面描述的相同的项(CIC_ITEM、CIC_FUNC和CIC_SEGM)。

下面的代码获取指令、函数和段地址的十六进制颜色代码

0 x0401469。

```
Python>"0x%x" % (idc.get_color(0x0401469, CIC_ITEM))
0xdfd9f3
Python>"0x%x" % (idc.get_color(0x0401469, CIC_FUNC))
0xffffffff
Python>"0x%x" % (idc.get_color(0x0401469, CIC_SEGM))
0xffffffff
```

十六进制颜色代码0xffffffff是IDA使用的默认颜色代码。如果您对更改IDA的颜色主题感兴趣，我建议您查看IDASkins 项目。

## 访问原始数据

在逆向工程中，能够访问原始数据是至关重要的。原始数据是代码或数据的二进制表示。我们可以在地址后面的左边看到原始数据或指令字节。

```
00A14380 8B 0D 0C 6D A2 00 mov ecx, hHeap
00A14386 50 push eax
00A14387 6A 08 push 8
00A14389 51 push ecx
00A1438A FF 15 30 11 A2 00 call ds:HeapAlloc
00A14390 C3 retn
```

要访问数据，我们首先需要确定单元大小。用于访问数据的api的命名约定是单元大小。要访问一个字节，可以调用idc.get_wide_byte(ea);要访问一个字，可以调用idc.get_wide_word(ea)，等等

• idc.get_wide_byte(ea)
• idc.get_wide_word(ea)

• idc.get_wide_dword(ea)
• idc.get_qword(ea)
• idc.GetFloat(ea)
• idc.GetDouble(ea)

如果游标在上面的程序集中的0x0A14380处，我们将得到以下输出。

```
Python>print("0x%x %s" % (ea, idc.generate_disasm_line(ea, 0)))
0xa14380 mov ecx, hHeap
Python>"0x%x" % idc.get_wide_byte(ea)
0x8b
Python>"0x%x" % idc.get_wide_word(ea)
0xd8b
Python>"0x%x" % idc.get_wide_dword(ea)
0x6d0c0d8b
Python>"0x%x" % idc.get_qword(ea)
0x6a5000a26d0c0d8bL
Python>idc.GetFloat(ea) # Example not a float value
2.70901711372e+27
Python>idc.GetDouble(ea)
1.25430839165e+204
```

在编写解码器时，获取单个字节或读取dword并不总是有用的，而是读取原始数据块。要在一个地址上读取指定大小的字节，我们可以使用idc.get_bytes(ea, size, use_dbg=False)。最后一个参数是可选的，只有在需要调试器内存时才需要它。

```
Python>for byte in idc.get_bytes(ea, 6):
print("0x%X" % byte),
0x8B 0xD 0xC 0x6D 0xA2 0x0
```

## 补丁

有时当逆向恶意软件时，样本包含被编码的字符串。这样做是为了减慢分析过程，并阻止使用字符串查看器来恢复指示器。在这种情况下，修补IDB是很有用的。我们可以重命名地址，但重命名是有限的。

这是由于命名约定的限制。要用一个值来修补一个地址，我们可以使用以下函数

• idc.patch_byte(ea, value)
• idc.patch_word(ea, value)
• idc.patch_dword(ea, value)

ea是地址，值是我们想要修补IDB的整数值。值的大小需要与我们选择的函数名指定的大小匹配。我们找到了以下编码字符串的一个例子。

```
.data:1001ED3C aGcquEUdg_bUfuD db 'gcqu^E]~UDG_B[uFU^DC',0
.data:1001ED51 align 8
.data:1001ED58 aGcqs_cuufuD db 'gcqs\_CUuFU^D',0
.data:1001ED66 align 4
.data:1001ED68 aWud@uubQU db 'WUD@UUB^Q]U',0
.data:1001ED74 align 8
```

在我们的分析过程中，我们能够识别解码器的功能。

```
100012A0 push esi
100012A1 mov esi, [esp+4+_size]
100012A5 xor eax, eax
100012A7 test esi, esi
100012A9 jle short _ret
100012AB mov dl, [esp+4+_key] ; assign key
100012AF mov ecx, [esp+4+_string]
100012B3 push ebx
100012B4
100012B4 _loop: ;
100012B4 mov bl, [eax+ecx]
100012B7 xor bl, dl ; data ^ key
100012B9 mov [eax+ecx], bl ; save off byte
100012BC inc eax ; index/count
100012BD cmp eax, esi
100012BF jl short _loop
100012C1 pop ebx
100012C2
100012C2 _ret: ;
100012C2 pop esi
100012C3 retn
```

该函数是一个标准的异或解码器函数，参数为size、key和译码缓冲区。

```
Python>start = idc.read_selection_start()
Python>end = idc.read_selection_end()
Python>print hex(start)
0x1001ed3c
Python>print hex(end)
0x1001ed50
Python>def xor(size, key, buff):
for index in range(0, size):
cur_addr = buff + index
temp = idc.get_wide_byte(cur_addr ) ^ key
idc.patch_byte(cur_addr, temp)
Python>
Python>xor(end - start, 0x30, start)
Python>idc.get_strlit_contents(start)
WSAEnumNetworkEvents
```

我们使用idc.read_selection_start()和idc.read_selection_end()选择突出显示的数据地址起始和结束

然后我们有一个函数，它通过调用idc.get_wide_byte(ea)读取字节，XOR将键传递给函数，然后通过调用idc修补字节。patch_byte (ea,值)

## 输出与输入

当我们不知道文件路径或不知道用户想把数据保存在哪里时，将文件导入和导出到IDAPython可能会很有用

要按名称导入或保存文件，我们使用ida_kernwin.ask_file(forsave, mask, prompt)。

如果我们想打开一个对话框，forsave的值可以是0，如果我们想打开一个保存对话框，forsave的值可以是1

mask是文件扩展名或模式。如果我们只想打开.dll文件，我们将使用“*.dll”的掩码并且prompt 是窗口的标题。输入、输出和选择数据的一个很好的例子是下面的IO_DATA类。

```
import sys
import idaapi
class IO_DATA():
def __init__(self):
self.start = idc.read_selection_start()
self.end = idc.read_selection_end()
self.buffer = ''
self.ogLen = None
self.status = True
self.run()
def checkBounds(self):
if self.start is BADADDR or self.end is BADADDR:
self.status = False
def getData(self):
"""get data between start and end put them into object.buffer"""
self.ogLen = self.end - self.start
self.buffer = b''
try:
self.buffer = idc.get_bytes(self.start, self.ogLen)
except:
self.status = False
return
def run(self):
"""basically main"""
self.checkBounds()
if self.status == False:
sys.stdout.write('ERROR: Please select valid data\n')
return
self.getData()
def patch(self, temp=None):
"""patch idb with data in object.buffer"""
if temp != None:
self.buffer = temp
for index, byte in enumerate(self.buffer):
idc.patch_byte(self.start + index, ord(byte))
def importb(self):
'''import file to save to buffer'''
fileName = ida_kernwin.ask_file(0, "*.*", 'Import File')
try:
self.buffer = open(fileName, 'rb').read()
except:
sys.stdout.write('ERROR: Cannot access file')
def export(self):
'''save the selected buffer to a file'''
exportFile = ida_kernwin.ask_file(1, "*.*", 'Export Buffer')
f = open(exportFile, 'wb')
f.write(self.buffer)
f.close()
def stats(self):
print("start: 0x%x" % self.start)
print("end: 0x%x" % self.end)
print("len: 0x%x" % len(self.buffer))
```

有了这个类，数据可以选择保存到一个缓冲区，然后存储到一个文件。这对于IDB中经过编码或加密的数据很有用。我们可以使用IO_DATA来选择解码缓冲区的数据

Python，然后修补IDB。如何使用IO_DATA类的示例。

```
Python>f = IO_DATA()
Python>f.stats()
start: 0x401528
end: 0x401549
len: 0x21
```

与其解释每一行代码，还不如让读者一个一个地浏览这些函数，看看它们是如何工作的

下面的要点解释了每个变量和函数的作用。obj是我们赋给类的任何变量。f是obj在f = IO_DATA()

obj.start

包含所选偏移量的起始地址

•。obj.end 

包含所选偏移量的结束地址。

•obj.buffer

包含二进制数据。

•obj.ogLen o包含缓冲区的大小。

•obj. getdata ()

 在obj.start和obj.end之间复制二进制数据到bj.buffer

•obj.run() 

选择的数据以二进制格式拷贝到缓冲区中

•obj.patch() 

在obj上对IDB进行补丁。从obj中的数据开始。缓冲区。

•obj.patch(d) 

 patch IDB在obj。从参数数据开始。

•obj.importb()

 打开并保存文件

• obj.export()
exports the data in  obj.buffer to a save as file.
• obj.stats()
print hex of  obj.start ,  obj.end and  obj.buffer length.

## PyQt

本书中记录的与IDAPython的大部分交互都是通过命令行进行的在某些情况下，使用图形用户界面(通常在IDAPython文档中称为表单)与代码交互可能很有用

 IDA的图形用户界面是在跨平台Qt GUI框架中编写的

要与这个框架交互，我们可以使用Qt的Python绑定称为PyQt 

PyQt的深入概述超出了本书的范围。本章提供的是一个简单的框架代码片段，可以很容易地修改和构建用于编写表单

代码创建了两个小部件，第一个小部件创建了一个表，第二个小部件是一个按钮。单击按钮时，当前地址和助记符将添加到表中的一行。如果单击该行，IDA将跳转到disassembly视图中.

该行中的地址可以把这段代码看作是简单的地址簿标记。下图是通过点击“添加地址”按钮，然后双击第一行，将三个地址添加到表单后的表单。





并不是下面代码中的所有api都会涉及到。

之所以如此简洁，是因为PyQt的API名称具有描述性。

例如，函数setColumnCount设置列数

如果一个API没有意义，请搜索API的名称。Qt和PyQt有非常好的文档记录。一旦我们理解了下面代码的基础知识，就很容易把一些东西拼凑在一起。在查看下面的代码时，了解PyQt的关键概念是了解PyQt是一个面向对象的框架。

```
from idaapi import PluginForm
from PyQt5 import QtCore, QtGui, QtWidgets
class MyPluginFormClass(PluginForm):
def OnCreate(self, form):
# Get parent widget
self.parent = self.FormToPyQtWidget(form) # IDAPython
self.PopulateForm()
def PopulateForm(self):
# Create layout
layout = QtWidgets.QVBoxLayout()
# Create Table Widget
self.example_row = QtWidgets.QTableWidget()
column_names = ["Address", "Mnemonic"]
self.example_row.setColumnCount(len(column_names))
self.example_row.setRowCount(0)
self.example_row.setHorizontalHeaderLabels(column_names)
self.example_row.doubleClicked.connect(self.JumpSearch)
layout.addWidget(self.example_row)
# Create Button
self.addbtn = QtWidgets.QPushButton("Add Address")
self.addbtn.clicked.connect(self.AddAddress)
layout.addWidget(self.addbtn)
# make our created layout the dialogs layout
self.parent.setLayout(layout)
def AddAddress(self):
ea = here() # IDAPython
index = self.example_row.rowCount()
self.example_row.setRowCount(index + 1)
h = "0x%x" % ea
item = QtWidgets.QTableWidgetItem(h)
item.setFlags(item.flags() ^ QtCore.Qt.ItemIsEditable)
self.example_row.setItem(index, 0, item)
self.example_row.setItem(index, 1,
QtWidgets.QTableWidgetItem(idc.print_insn_mnem(ea))) # IDAPython
self.example_row.update()
def JumpSearch(self, item):
tt = self.example_row.item(item.row(), 0)
ea = int(tt.text(), 16)
idaapi.jumpto(ea) # IDAPython
plg = MyPluginFormClass()
plg.Show("Jump Around")
```

前两行包含导入所需模块。为了创建表单，在idapi中继承PluginForm来创建一个类



在MyPluginFormClass类中有一个被调用的方法OnCreate。这个方法在创建插件窗体时被调用

OnClose是相反的，当插件关闭时被调用。函数self. formtopyqtwidget (form)创建了必要的父实例，用于填充我们的小部件，该实例存储在self.parent

方法PopulateForm(self)是所有小部件设计和创建的地方

在这个方法中，重要的核心三个步骤是为布局(layout =

QtWidgets.QVBoxLayout())，创建(self.example_row = QtWidgets.QTableWidget())并添加小部件(layout. addwidget (self.example_row))，然后设置layout

(self.parent.setLayout(layout)。

其余的代码是修改小部件或向小部件添加操作。一旦这样的操作调用方法self。如果一行被双击，跳转搜索。如果用户双击行，它将读取第一行，然后调用idaapi.jumpto(ea)将拆卸视图重定向到地址。设置好布局后，表单实例中的Show(str)方法将用于显示表单。Show(str)接受一个字符串参数

表单的标题(例如“跳跃”)。

## 批处理文件生成

有时，为一个目录中的所有文件创建IDBs或ASMs是很有用的。

他可以帮助节省时间，分析一组样本，这是同一家族的恶意软件。比起在一个大的集合上手工生成批处理文件，这要容易得多。要进行批处理分析，我们需要将-B参数传递给文本idat.exe。下面的代码可以复制到包含我们想要生成文件的所有文件的目录中

```
import os
import subprocess
import glob
paths = glob.glob("*")
ida_path = os.path.join(os.environ['PROGRAMFILES'], "IDA Pro 7.5", "idat.exe")
for file_path in paths:
if file_path.endswith(".py"):
continue
subprocess.call([ida_path, "-B", file_path])
```

我们使用glob.glob(“*”)来获得目录中所有文件的列表

如果只希望选择某个正则表达式模式或文件类型，则可以修改参数

如果我们只想获取扩展名为.exe的文件，我们可以使用globo .glob("*.exe").os.path.join(os.environ['PROGRAMFILES'], "IDA", "idat.exe")

用于获取idat.exe的路径。一些版本的IDA有一个带有版本号的文件夹名。如果是这种情况，则需要将参数“IDA”修改为文件夹名。

另外，如果我们选择为IDA使用非标准的安装位置，可能必须修改整个命令,现在，让我们假设IDA的安装路径是C:\Program Files\IDA。找到路径后，我们循环遍历目录中不包含.py扩展名的所有文件，然后将它们传递给IDA。

对于单个文件，它看起来像C:\Program Files\IDA\idat.exe -B bad_file.exe。

一旦运行，它将为文件生成一个ASM和IDB。所有文件都将写入工作目录。下面是一个输出示例

```
C:\injected>dir
0?/**/____ 09:30 AM <DIR> .
0?/**/____ 09:30 AM <DIR> ..
0?/**/____ 10:48 AM 167,936 bad_file.exe
0?/**/____ 09:29 AM 270 batch_analysis.py
0?/**/____ 06:55 PM 104,889 injected.dll
C:\injected>python batch_analysis.py
Thank you for using IDA. Have a nice day!
C:\injected>dir
0?/**/____ 09:30 AM <DIR> .
0?/**/____ 09:30 AM <DIR> ..
0?/**/____ 09:30 AM 506,142 bad_file.asm
0?/**/____ 10:48 AM 167,936 bad_file.exe
0?/**/____ 09:30 AM 1,884,601 bad_file.idb
0?/**/____ 09:29 AM 270 batch_analysis.py
0?/**/____ 09:30 AM 682,602 injected.asm
0?/**/____ 06:55 PM 104,889 injected.dll
0?/**/____ 09:30 AM 1,384,765 injected.idb
```

bad_file.asm, bad_file.idb,injected.asm和injected.idb是生成的文件。

## 可执行脚本

IDAPython脚本可以从命令行执行

我们可以使用下面的代码来计算IDB中的每条指令，然后将其写入名为instruments .txt的文件中

```
import idc
import idaapi
import idautils
idaapi.auto_wait()
count = 0
for func in idautils.Functions():
# Ignore Library Code
flags = idc.get_func_attr(func, FUNCATTR_FLAGS)
if flags & FUNC_LIB:
continue
for instru in idautils.FuncItems(func):
count += 1
f = open("instru_count.txt", 'w')
print_me = "Instruction Count is %d" % (count)
f.write(print_me)
f.close()
idc.qexit(0)
```

从命令行的角度来看，两个最重要的函数是idaapi.auto_wait()和idc.qexit(0)。当IDA打开一个文件时，等待分析完成是很重要的

这允许ida填充基于ida分析引擎的所有函数、结构或其他值。

们基于IDA的分析引擎。要等待分析完成，我们调用idaapi.auto_wait().它将等待/暂停直到IDA完成了它的分析。一旦分析完成，它就将控制权返回给脚本。

在调用任何依赖于完成分析的IDAPython函数之前，在脚本的开头执行这一点是很重要的。一旦我们的脚本执行完毕，我们需要调用idc.qexit(0)。

这将停止脚本的执行，关闭数据库并返回到脚本的调用者。否则，我们的IDB将无法正常关闭。

如果我们希望执行IDAPython来计算所有行数，那么在IDB中，我们将执行以下命令行

```
"C:\Program Files\IDA Pro 7.5\ida.exe" -Scount.py example.idb
```

-s 在IDB打开后，向IDA发出信号，让它在IDB上运行脚本。

在工作目录中，我们会看到一个名为instruc_count .txt的文件，其中包含所有指令的计数。

如果我们想在可执行文件上执行我们的脚本，我们需要通过传递-A让IDA以自动化运行

```
"C:\Program Files\IDA Pro 7.5\ida.exe" -A -Scount.py example.exe
```

## Yara

Yara 是一个基于规则的模式匹配软件和库，可以用于搜索文件

Yara的规则是使用基于字符串的模式定义的(" foo ")，字节数({66 6f 6f})，文件大小(filesize < 37)或文件的其他条件属性。

由于其强大和灵活的规则，Yara被正确地称为“模式匹配瑞士刀的恶意软件研究人员”从IDAPython的观点来看，Yara是一个很好的库，可以添加到工具包中，原因有几个

对于初学者来说，Yara比IDAPython的搜索速度快得多，它的规则可以用于自动化分析过程，而且还有大量公开可用的规则

我最喜欢的自动化分析过程的搜索示例之一是搜索加密函数使用的常量。通过搜索字节模式，我们可以交叉引用匹配，并推断引用字节的函数与加密算法有关。例如，搜索常量0x67452301可用于查找与哈希算法MD4、MD5和SHA1相关的函数。

使用Yara的第一步是创建规则

Yara规则遵循类似于C语言的简单语法。规则由名称、匹配模式(也就是Yara文档中的字符串定义)和条件组成。下面的文本是一个简单的Yara规则。它不是一个实用的Yara规则，但它对于演示Yara的规则语法很有用。

```
*
Example Yara Rule
*/
rule pe_md5_constant
{
strings:
$pe_header = "MZ"
$hex_constant = { 01 23 45 67 } // byte pattern
condition:

$pe_header at 0 and $hex_constant
}
```

前两行是一个多行注释。与C和其他语言一样，注释以/*开头，以*/结尾。

Yara规则以关键字rule开头，后面跟着名称(也就是规则标识符)。

规则后面是一个左花括号

{。左花括号后面是字符串定义，它以关键字字符串开头，后跟一个冒号:。字符串定义用于定义Yara匹配的规则

每个字符串都有一个标识符，以$字符开头，后跟组成字符串定义名称的字符和数字

字符串定义可以是字符(如MZ)或十六进制字符串(如

{01 23 45 67})。

字符串定义之后是Yara匹配的条件

条件以关键字condition开头，后跟冒号:。在上面的Yara规则示例中，匹配的条件为:如果字符串定义$pe_header位于偏移量0，并且文件包含在$hex_constant中定义的字节模式，那么Yara有一个匹配。因为$hex_constant没有定义偏移量，所以字节模式只需要在文件的任何地方存在才能匹配。

Yara支持广泛的关键字，可用于定义宽字符、入口点、大小和其他条件。建议阅读Yara文档中的Writing Yara规则，以了解所有不同的关键字、它们支持的选项以及扫描或匹配文件的不同方式。

通过执行pip install Yara -python命令，可以使用pip轻松安装Yara的python接口。以下步骤需要在Python中使用Yara扫描文件

1. Yara需要import

   import 	yara

2. Yara需要使用Yara .compile编译Yara规则

    rules = Yara .compile(source=signature)

3. 打开一个文件或在一个缓冲区中有数据Yara匹配o

   data = Open (scan_me， "rb").read()

4. 使用编译的Yara规则扫描文件 yara.match

   matchs= rules.match(data=self.mem_results)

5. 打印匹配或应用基于匹配的逻辑

当然，这是对所需步骤的简化描述

Yara包含一些方法和配置，可以用于更高级的扫描选项

这些功能的例子有函数回调，扫描运行的进程和大文件的超时

请参阅Yara的文档获取这些方法和配置的完整列表

在IDA中使用Yara的上下文中，扫描IDB中的二进制数据需要相同的步骤。

除了需要一个额外的步骤将Yara匹配文件偏移量转换为可执行的虚拟地址，这是IDA引用地址的方式。

如果Yara正在扫描一个可移植的可执行文件，并且它与文件偏移量为0x1000的模式匹配，则可以在ida里表示为虚拟地址

0 x0401000

下面的代码是一个类，它从IDB读取二进制数据，然后使用Yara扫描数据

```
import yara
import idautils
SEARCH_CASE = 4
SEARCH_REGEX = 8
SEARCH_NOBRK = 16
SEARCH_NOSHOW = 32
SEARCH_UNICODE = 64
SEARCH_IDENT = 128
SEARCH_BRK = 256
class YaraIDASearch:
def __init__(self):
self.mem_results = ""
self.mem_offsets = []
if not self.mem_results:
self._get_memory()
def _get_memory(self):
print("Status: Loading memory for Yara.")
result = b""
segments_starts = [ea for ea in idautils.Segments()]
offsets = []
start_len = 0
for start in segments_starts:
end = idc.get_segm_end(start)
result += idc.get_bytes(start, end - start)
offsets.append((start, start_len, len(result)))
start_len = len(result)
print("Status: Memory has been loaded.")
self.mem_results = result
self.mem_offsets = offsets
def _to_virtual_address(self, offset, segments):
va_offset = 0
for seg in segments:
if seg[1] <= offset < seg[2]:
va_offset = seg[0] + (offset - seg[1])
return va_offset
64
def _init_sig(self, sig_type, pattern, sflag):
if SEARCH_REGEX & sflag:
signature = "/%s/" % pattern
if SEARCH_CASE & sflag:
# ida is not case sensitive by default but yara is
pass
else:
signature += " nocase"
if SEARCH_UNICODE & sflag:
signature += " wide"
elif sig_type == "binary":
signature = "{ %s }" % pattern
elif sig_type == "text" and (SEARCH_REGEX & sflag) == False:
signature = '"%s"' % pattern
if SEARCH_CASE & sflag:
pass
else:
signature += " nocase"
signature += " wide ascii"
yara_rule = "rule foo : bar { strings: $a = %s condition: $a }" % signature
return yara_rule
def _compile_rule(self, signature):
try:
rules = yara.compile(source=signature)
except Exception as e:
print("ERROR: Cannot compile Yara rule %s" % e)
return False, None
return True, rules
def _search(self, signature):
status, rules = self._compile_rule(signature)
if not status:
return False, None
values = []
matches = rules.match(data=self.mem_results)
if not matches:
return False, None
65
for rule_match in matches:
for match in rule_match.strings:
match_offset = match[0]
values.append(self._to_virtual_address(match_offset,
self.mem_offsets))
return values
def find_binary(self, bin_str, sflag=0):
yara_sig = self._init_sig("binary", bin_str, sflag)
offset_matches = self._search(yara_sig)
return offset_matches
def find_text(self, q_str, sflag=0):
yara_sig = self._init_sig("text", q_str, sflag)
offset_matches = self._search(yara_sig)
return offset_matches
def find_sig(self, yara_rule):
offset_matches = self._search(yara_rule)
return offset_matches
def reload_scan_memory(self):
self._get_memory()
```

前面的代码中已经介绍了所有api

这个函数_to_virtual_address可以用来将Yara文件偏移匹配转换为正确地址中的IDA地址

下面是一个创建YaraIDASearch()实例的示例，该实例使用Yara签名扫描IDB，并返回规则匹配的偏移量

需要注意的是，该规则是由前一条规则修改而来的。ida并不总是将可移植可执行文件的MZ头文件作为一个段加载。

```
Python>ys = YaraIDASearch()
Status: Loading memory for Yara.
Status: Memory has been loaded.
Python>example_rule = """rule md5_constant
{
strings:
$hex_constant = { 01 23 45 67 } // byte pattern
condition:
$hex_constant
}"""
Python>
Python>ys.find_sig(example_rule)
[4199976L]

```

第一行创建了一个YaraIDASearch实例并将其赋值给ys

Yara规则保存为字符串，并分配给变量example_rule。

规则作为参数传递给方法ys.find_sig(yara_rule)。搜索方法返回Yara规则所匹配的所有偏移量的列表。如果要搜索二进制模式，可以使用ys.find_binary(bytes)

一个搜索的ys.find_binary(01 23 45 67)将返回与自定义Yara规则相同的结果。YaraIDASearch还支持使用sys .find_text(string)搜索字符串

## Unicorn engine

Unicorn Engine 是一个轻量级的多平台、多架构的CPU仿真器框架，构建在Qemu的修改版本之上

Unicorn是用C编写的，但包含了多种语言的绑定，包括Python

Unicorn是一个强大的工具，可以在逆向工程过程中提供帮助，因为它本质上允许在可配置、控制和特定状态下模拟代码

后面的形容词specific是unicorn engine的力量发挥作用的地方。

在没有真正理解程序集的情况下，执行代码并被告知特定的输出，可以节省时间，并有助于自动化分析过程。例如，使用Unicorn执行数百个带有不同位移位算法和/或异或密钥的内联字符串解密例程，然后将解密的密钥写成字符串作为注释，这就是它的一种用法。

为了更好地理解使用Unicorn引擎，我们需要了解一些核心概念以及如何使用Unicorn api实现它们

### 实例初始化unicorn

要初始化Unicorn类，使用API Uc(UC_ARCH, UC_MODE)

*译文：*
Uc定义了如何模拟代码的细节。例如，二进制数据是按MIPS-32执行还是按as执行x86 - 64

第一个参数是硬件体系结构类型，第二个参数是硬件模式类型和/或端序。以下是当前支持的架构类型

```
 UC_ARCH_ARM
o ARM architecture (including Thumb, Thumb-2)
•  UC_ARCH_ARM64
o ARM-64, also called AArch64
•  UC_ARCH_MIPS
o Mips architecture
•  UC_ARCH_X86
o X86 architecture (including x86 & x86-64)
•  UC_ARCH_PPC
o PowerPC architecture (currently unsupported)
•  UC_ARCH_SPARC
o Sparc architecture
•  UC_ARCH_M68K
o M68K architecture
```

以下是可用的硬件类型。这些评论来自unicorn.h

```
Endianness
•  UC_MODE_LITTLE_ENDIAN
o little-endian mode (default mode)
•  UC_MODE_BIG_ENDIAN
o big-endian mode
Arm
•  UC_MODE_ARM
o ARM mode
•  UC_MODE_THUMB
o THUMB mode (including Thumb-2)
Mips
•  UC_MODE_MIPS32
o Mips32 ISA
•  UC_MODE_MIPS64
o Mips64 ISA
x86 / x64
•  UC_MODE_16
o 16-bit mode
•  UC_MODE_32
o 32-bit mode
o
•  UC_MODE_64
o 64-bit mode
sparc
•  UC_MODE_SPARC32
o 32-bit mode
•  UC_MODE_SPARC64
o 64-bit mode
```

有许多不同的架构和硬件类型的组合，Unicode 引擎

Python bindings目录包含几个示例脚本。所有的例子都有样例_.*.py模式。

#### 读写寄存器

在对内存进行读写之前，需要对内存进行映射。

为了映射内存api uc.mem_map(address，size，perms=uc. uc_prot_all)和uc. uc_prot_all。使用了mem_map_ptr(地址、大小、perms、ptr)。以下内存保护是可用的

•  UC_PROT_NONE
•  UC_PROT_READ
•  UC_PROT_WRITE
•  UC_PROT_EXEC
•  UC_PROT_ALL

保护一个内存范围的API uc.mem_protect(address, size,
perms=uc.UC_PROT_ALL的使用

取消了内存映射api uc.mem_unmap(address, size)

一旦内存被映射，就可以通过调用 uc.mem_write(address,
data) 来写入

从分配的内存中读取 uc.mem_read(address, size)

### 读写寄存器

寄存器可以通过调用 uc.reg_read(reg_id, opt=None) 来读取

reg_ids是Python绑定目录中适当的体系结构常量Python文件中定义

ARM-64 in  arm64_const.py
•  ARM in  arm_const.py
•  M68K in  m68k_const.py
•  MIPS in  mips_const.py
•  SPARC in  sparc_const.py
•  X86 in  x86_const.py

要引用这些常量，必须首先导入它们

对于x86来说常量是通过调用from unicorn.x86_const import *  .写寄存器的内容使用uc.reg_write(reg_id, value)。

### 启动和停止模拟

启动Unicorn引擎来模拟API uc.emu_start(begin, until, timeout=0,count=0)

第一个函数开始是第一个被模拟的地址。第二个参数直到Unicorn引擎停止模仿的地址(或上面的地址)

这个参数timeout= 是定义Unicorn引擎在超时之前执行的毫秒数。

UC_SECOND_SCALE * n可以用来等待n秒。

最后一个参数count=可用于定义Unicorn引擎停止执行前执行的指令数量。如果count=为零或小于Unicorn引擎的计数被禁用。要停止模拟API，使用uc.emu_stop()

### 内存和钩子管理与用户定义的回调

Unicode引擎支持各种各样的hook。下面描述这些hook的一个子集

在调用之前插入钩子以启动仿真

要添加一个挂钩的API  uc.hook_add(UC_HOOK_*, callback, user_data, begin, end, ...) 

前两个参数是必须的。后三个选项是可选的，通常使用默认值None、1、0、UC_INS填充。要删除一个钩子，需要使用API emu.hook_del(hook)

要删除一个钩子，它必须赋值给一个变量。

例如，下面的代码片段是如何删除钩子

```
i = emu.hook_add(UC_HOOK_CODE, hook_code, None)
emu.hook_del(i)
```

钩子及其相应的回调允许插装模拟代码。在这些回调函数中，我们可以应用逻辑进行分析、修改代码或简单地输出值。

这些回调函数在调试错误或确保正确的初始值时非常有用。下面的一些例子来自Unicorn Engine的样本repo 

#### UC_HOOK_INTR

UC_HOOK_INTR用于挂钩所有中断和系统调用事件。回调hook_intr的第一个参数是Unicorn实例

该实例可用于调用前面描述的Unicorn API。第二个参数intno是中断号。第三个参数user_data是一个可以从钩子传递给回调函数的变量。下面的示例打印中断号(如果不等于0x80)，并通过调用uc.emu_stop()停止仿真

```
def hook_intr(uc, intno, user_data):
# only handle Linux syscall
if intno != 0x80:
print("got interrupt %x ???" %intno);
uc.emu_stop()
return
uc.hook_add(UC_HOOK_INTR, hook_intr)
```

#### UC_HOOK_INSN

UC_HOOK_INSN在执行x86指令IN、OUT或系统调用时添加钩子。

下面的代码片段添加了一个UC_HOOK_INSN，并在

UC_X86_INS_SYSCALL执行。回调读取RAX寄存器，如果RAX等于0x100，则用0x200修补，Unicorn引擎继续模拟代码

```
def hook_syscall(uc, user_data):

rax = uc.reg_read(UC_X86_REG_RAX)
if rax == 0x100:
uc.reg_write(UC_X86_REG_RAX, 0x200)
uc.hook_add(UC_HOOK_INSN, hook_syscall, None,1, 0, UC_X86_INS_SYSCALL)
```



#### UC_HOOK_CODE

UC_HOOK_CODE可以挂钩一个范围的代码。钩子在每条指令执行之前被调用。

回调hook_code包含四个参数。下面的代码片段实现

UC_HOOK_CODE钩子并打印被模拟的地址和大小。第一个参数uc是

Unicorn实例，address是要执行的代码的地址，size是模拟指令的大小，前面已经介绍过user_data。

```
def hook_code(uc, address, size, user_data):
print("Tracing instruction at 0x%x, instruction size = 0x%x" %(address, size))
uc.hook_add(UC_HOOK_CODE, hook_code)
```

#### UC_HOOK_BLOCK

UC_HOOK_BLOCK是一个钩子，它可以实现跟踪基本块的回调函数。

参数与UC_HOOK_CODE中描述的相同

```
def hook_block(uc, address, size, user_data):
print("Tracing basic block at 0x%x, block size = 0x%x" %(address, size)
uc.hook_add(UC_HOOK_BLOCK, hook_block
```

#### UC_HOOK_MEM_*

Unicorn引擎有几个钩子，专门用于读取、获取、写入和访问内存。它们都以UC_HOOK_MEM_*开头。它们的回调都有相同的参数，如下所示。

```
def hook_mem_example(uc, access, address, size, value, user_data):
pass
```

第一个参数是Unicorn实例，第二个参数是access。它们的值如下所示

```
UC_MEM_READ = 16
UC_MEM_WRITE = 17
UC_MEM_FETCH = 18
UC_MEM_READ_UNMAPPED = 19
UC_MEM_WRITE_UNMAPPED = 20
UC_MEM_FETCH_UNMAPPED = 21
UC_MEM_WRITE_PROT = 22
UC_MEM_READ_PROT = 23
UC_MEM_FETCH_PROT = 24
UC_MEM_READ_AFTER = 25
```

#### UC_HOOK_MEM_INVALID

UC_HOOK_MEM_INVALID的示例代码包含一个比较访问错误的示例。

当发生无效内存访问时，将执行回调hook_mem_invalid。

```
def hook_mem_invalid(uc, access, address, size, value, user_data):
eip = uc.reg_read(UC_X86_REG_EIP)
if access == UC_MEM_WRITE:
print("invalid WRITE of 0x%x at 0x%X, data size = %u, data value = 0x%x"
% (address, eip, size, value))
if access == UC_MEM_READ:
print("invalid READ of 0x%x at 0x%X, data size = %u" % (address, eip,
size))
if access == UC_MEM_FETCH:
print("UC_MEM_FETCH of 0x%x at 0x%X, data size = %u" % (address, eip,
size))
if access == UC_MEM_READ_UNMAPPED:
print("UC_MEM_READ_UNMAPPED of 0x%x at 0x%X, data size = %u" % (address,
eip, size))
if access == UC_MEM_WRITE_UNMAPPED:
print("UC_MEM_WRITE_UNMAPPED of 0x%x at 0x%X, data size = %u" %
(address, eip, size))
if access == UC_MEM_FETCH_UNMAPPED:
print("UC_MEM_FETCH_UNMAPPED of 0x%x at 0x%X, data size = %u" %
(address, eip, size))
if access == UC_MEM_WRITE_PROT:
print("UC_MEM_WRITE_PROT of 0x%x at 0x%X, data size = %u" % (address,
eip, size))
if access == UC_MEM_FETCH_PROT:
print("UC_MEM_FETCH_PROT of 0x%x at 0x%X, data size = %u" % (address,
eip, size))
if access == UC_MEM_FETCH_PROT:
print("UC_MEM_FETCH_PROT of 0x%x at 0x%X, data size = %u" % (address,
eip, size))
if access == UC_MEM_READ_AFTER:
print("UC_MEM_READ_AFTER of 0x%x at 0x%X, data size = %u" % (address,
eip, size))
return False
uc.hook_add(UC_HOOK_MEM_INVALID, hook_mem_invalid)
```

#### UC_HOOK_MEM_READ_UNMAPPED

UC_HOOK_MEM_READ_UNMAPPED 是一个钩子，当模拟代码试图读取未映射内存时执行回调。下面的代码片段是一个示例

```
def hook_mem_read_unmapped(uc, access, address, size, value, user_data):
pass
uc.hook_add(UC_HOOK_MEM_READ_UNMAPPED, hook_mem_read_unmapped, None)
```

下面是其他内存钩子的列表，具有最小的描述。可以修改前面的示例代码片段以使用下面的钩子。

```
• UC_HOOK_MEM_WRITE_UNMAPPED
o Executes a callback when an invalid memory write event occurs
• UC_HOOK_MEM_FETCH_UNMAPPED
o Executes a callback when an invalid memory fetch for execution event
• UC_HOOK_MEM_READ_PROT
o Executes a callback when a memory read on read-protected memory occurs
• UC_HOOK_MEM_WRITE_PROT
o Executes a callback when a memory write on write-protected memory occurs
• UC_HOOK_MEM_FETCH_PROT
o Executes a callback when a memory fetch on non-executable memory occurs
• UC_HOOK_MEM_READ
o Executes a callback when a memory read event occurs
• UC_HOOK_MEM_WRITE
o Executes a callback when a memory write events"
• UC_HOOK_MEM_FETCH
o Executes a callback when a memory fetch for execution event occurs
• UC_HOOK_MEM_READ_AFTER
o Executes a callback when a successful memory read event occurs.
```

现在我们了解了Unicorn引擎的工作原理，我们将其用于IDA的上下文。下面的程序集通过调用malloc来分配内存，复制加密字符串的偏移量，然后使用一个键对字符串的每个字节进行XORs，并将结果存储在分配的内存中

```
.text:00401034 push esi
.text:00401035 push edi
.text:00401036 push 0Ah ; Size
.text:00401038 call ds:malloc
.text:0040103E mov esi, eax
.text:00401040 mov edi, offset str_encrypted
.text:00401045 xor eax, eax ; eax = 0
.text:00401047 sub edi, esi
.text:00401049 pop ecx
text:0040104A
.text:0040104A loop: ; CODE XREF: _main+28↓j
.text:0040104A lea edx, [eax+esi]
.text:0040104D mov cl, [edi+edx]
.text:00401050 xor cl, ds:b_key
.text:00401056 inc eax
.text:00401057 mov [edx], cl
.text:00401059 cmp eax, 9 ; index
.text:0040105C jb short loop
.text:0040105E push esi
```

上面的代码很简单，但是它包含了一些在模拟代码时必须考虑到的细微差别。第一个问题是在偏移量0x0401038处调用malloc。Unicorn引擎像CPU处理器一样模拟指令，但又不像操作系统的模拟器。它不像窗口的加载器那样工作。它不为可执行文件初始化内存以便它可以执行。

Unicorn引擎不处理内存映射、动态链接库的加载或导入表的填充。它可以执行位置独立的代码，如果它是自包含的，因此不依赖于由操作系统填充的内存结构(如进程环境块)

如果这些是成功执行所需要的属性，那么这些属性需要手工创建和映射，或者通过钩子和手工处理回调

第二个问题是随着加密字符串偏移量的移动而产生的0x0401040偏移量。字符串的偏移量为虚拟偏移量0x0402108。

如果可执行文件中的代码被视为没有内存映射的原始数据，那么执行偏移量将是0x440，但是试图读取虚拟地址将返回无效的内存读取，因为内存没有映射。当一个可执行文件或IDA的IDB中的可执行数据被执行时，需要映射到正确的地址。最后一个问题是只执行XOR循环并忽略其他代码和异常。下面的代码假设用户从上面高亮显示程序集

0 0 x40105e x401034

```
from unicorn import *
from unicorn.x86_const import *
import idautils
import math
VIRT_MEM = 0x4000
def roundup(x):
return int(math.ceil(x / 1024.0)) * 1024
def hook_mem_invalid(uc, access, address, size, value, user_data):
if uc._arch == UC_ARCH_X86:
74
eip = uc.reg_read(UC_X86_REG_EIP)
else:
eip = uc.reg_read(UC_X86_REG_RIP)
bb = uc.mem_read(eip, 2)
if bb != b"\xFF\x15":
return
if idc.get_name(address) == "malloc":
uc.mem_map(VIRT_MEM, 8 * 1024)
if uc._arch == UC_ARCH_X86:
uc.reg_write(UC_X86_REG_EAX, VIRT_MEM)
cur_addr = uc.reg_read(UC_X86_REG_EIP)
uc.reg_write(UC_X86_REG_EIP, cur_addr + 6)
else:
cur_addr = uc.reg_read(UC_X86_REG_RIP)
uc.reg_write(UC_X86_REG_RIP, cur_addr + 6)
def hook_code(uc, address, size, user_data):
"""For Debugging Use Only"""
print('Tracing instruction at 0x%x, instruction size = 0x%x' % (address, size))
def emulate():
try:
# get segment start and end address
segments = []
for seg in idautils.Segments():
segments.append((idc.get_segm_start(seg), idc.get_segm_end(seg)))
# get base address
BASE_ADDRESS = idaapi.get_imagebase()
# get bit
info = idaapi.get_inf_structure()
if info.is_64bit():
mu = Uc(UC_ARCH_X86, UC_MODE_64)
elif info.is_32bit():
mu = Uc(UC_ARCH_X86, UC_MODE_32)
# map 8MB memory for this emulation
mu.mem_map(BASE_ADDRESS - 0x1000, 8 * 1024 * 1024)
75
# write segments to memory
for seg in segments:
temp_seg = idc.get_bytes(seg[0], seg[1] - seg[0])
mu.mem_write(seg[0], temp_seg)
# initialize stack
stack_size = 1024 * 1024
if info.is_64bit():
stack_base = roundup(seg[1])
mu.reg_write(UC_X86_REG_RSP, stack_base + stack_size - 0x1000)
mu.reg_write(UC_X86_REG_RBP, stack_base + stack_size)
elif info.is_32bit():
stack_base = roundup(seg[1])
mu.reg_write(UC_X86_REG_ESP, stack_base + stack_size - 0x1000)
mu.reg_write(UC_X86_REG_EBP, stack_base + stack_size)
# write null bytes to the stack
mu.mem_write(stack_base, b"\x00" * stack_size)
# get selected address range
start = idc.read_selection_start()
end = idc.read_selection_end()
if start == idc.BADADDR:
return
# add hook
mu.hook_add(UC_HOOK_MEM_READ, hook_mem_invalid)
mu.hook_add(UC_HOOK_CODE, hook_code)
mu.emu_start(start, end)
decoded = mu.mem_read(VIRT_MEM, 0x0A)
print(decoded)
except UcError as e:
print("ERROR: %s" % e)
return None
return mu
emulate()
```

从函数模拟开始，它通过调用idautils.Segments()遍历IDB中的所有段，并通过调用idc. get_seg_start (seg)提取段的起始地址，然后使用idc. get_seg_end (seg)获取结束地址

通过调用idaapi.get_imagebase()来检索基地址。检索基地址之后，我们调用idaapi.get_inf_structure()来获取idainfo结构的实例。使用存储在info中的idainfo结构，调用info.is_64bit()或info.is_32bit()来确定IDB的位

因为这是一个32位的可执行文件，所以调用Uc(UC_ARCH_X86, UC_MODE_32)。这将设置Unicorn实例以X86的32位模式执行代码，并将实例存储在变量mu中。8MB的内存通过调用mu.mem_map(BASE_ADDRESS - 0x1000, 8 * 1024 * 1024)在基地址减去1000处分配。在内存映射之后，就可以对其进行写入了。

如果试图对未映射的内存进行写、读或访问，将发生错误。段数据被写入相应的内存偏移量。写完这些段后，就分配堆栈内存

通过调用mu写入基指针和堆栈指针寄存器。(UC_X86_REG_ESP, stack_base + stack_size - 0x1000)和mu。(UC_X86_REG_EBP, stack_base + stack_size)。第一个参数是regid(例如UC_X86_REG_ESP)，第二个值是要写入寄存器的值

栈初始化为空字节(“\0x00”)，并提取所选的地址。钩子

钩子UC_HOOK_MEM_READ的回调函数为hook_mem_invalid，钩子UC_HOOK_CODE的回调函数为hook_code。通过调用mu来模拟代码。emu_start(start, end)， start和end用选定的偏移量填充。模拟完成后，它将读取

xor字符串并打印它

第一个被触发的钩子是UC_HOOK_MEM_READ，当Unicorn试图读取malloc应该映射到的地址时发生。一旦钩子出现，回调函数hook_mem_invalid就会被执行。

在这个回调中，我们通过分配内存，将偏移量写入EAX或RAX，然后返回来编写我们自己的自定义malloc。要确定是否应该写入EAX或RAX，我们可以通过比较uc来检索存储在Unicorn实例中的体系结构。UC_ARCH_X86。

另一个选项是将info.is_32bit()的结果作为user_data中的可选参数传递。EIP是通过使用UC_X86_REG_EIP参数调用uc.reg_read(reg_id)来读取的。接下来，我们通过调用uc读取两个字节。mem_read(int, size)，第一个参数是存储在EIP中的偏移量，第二个参数是要读取的数据的大小。将这两个字节与“\xFF\x15”进行比较，以确保异常发生在调用指令中

如果是调用指令，则使用idc.get_name(ea)检索地址的名称，并检查API地址的名称为malloc。如果是malloc，则使用uc映射内存。mem_map(地址，大小)，然后将地址写入寄存器EAX uc。reg_write (UC_X86_REG_EAX VIRT_MEM)。为了绕过内存异常，我们需要设置

EIP地址调用malloc (0x40103E

为了写入EIP，我们使用uc.reg_write(reg_id, value)，值为EIP + 6的地址。UC_HOOK_CODE的第二个钩子，带有一个hook_code回调函数，打印正在模拟的当前地址及其大小。下面是在IDA中模拟的独角兽的输出。最后一行包含解密的字符串。

```
Tracing instruction at 0x401034, instruction size = 0x1
Tracing instruction at 0x401035, instruction size = 0x1
77
Tracing instruction at 0x401036, instruction size = 0x2
..removed..
Tracing instruction at 0x401059, instruction size = 0x3
Tracing instruction at 0x40105c, instruction size = 0x2
Tracing instruction at 0x40105e, instruction size = 0x1
bytearray(b'test mess\x00')
```

