# Project 3: CS61CPU

## 提示与指南

- 不要使用 Soda 实验室电脑上预装的 Logisim 版本，那会弄乱你的电路！请使用你项目仓库中提供的 `logisim-evolution.jar`。
- 在本项目的所有部分中，你只能使用 Logisim 内置的以下库中的模块：Wiring（**除 Transistor、Transmission Gate、POR、Pull Resistor、Power、Ground、POR、Do not connect 外**）、Gates、Plexers、Arithmetic（**除 Divider 外**）、Memory（**除 RAM、Random Generator 外**）。注意，允许使用 **ROM**。
- 经常保存、经常提交！尽量每 5 分钟左右就在 Logisim 中保存一次代码，并且每产出一个新功能就提交一次，哪怕它很小。
- 大多数项目任务的 sanity 测试都包含在项目起始代码中。更多信息见每个任务的 Testing 部分。
- 观看[这个视频](https://inst.eecs.berkeley.edu/~cs61c/fa20/lectures/videos?ytvid=qFIPWt9Pu7I)，了解自定义测试与调试的演示视频。
- 不要移动电路中的输入和输出引脚，因为这可能导致引脚错位（换句话说，autograder 测试会失败）！请检查 harness 电路以及 `cpu.circ`，确保你的电路能适配测试 harness。
- 你可以创建新的子电路，但它们必须位于起始代码所给的文件夹内。你不能新建 `.circ` 文件；如果这么做，autograder 会让你不及格！
- 在所有 `.circ` 文件中，每个子电路必须使用唯一的名称。否则会导致 autograder 扣分。你不能更改起始文件中所提供的任何电路的名称。
- `.circ` 文件使用 XML 格式，这使得 Git 难以自动合并。我们建议你一次只在一台电脑上工作；如果使用多台电脑，请确保在切换设备之前已经 push 和 pull 了你的代码。
- 你的提交必须具有合理的效率：运行单个测试不应花费很长时间。
- 我们建议在开始项目 Part A 之前完成 Lab 05，在开始 Part B 之前完成 Lab 06。这两个实验都涵盖了许多对项目相应部分有用的 Logisim 基础知识。
- 在 Logisim 中，可通过 `Help->Library Reference` 找到 Logisim 库参考。
- 一些常见的 Logisim 错误来源，供你调试时参考：

## Part A：开始

对于本项目，你使用的电脑需要能够运行 Java 和 Python 3，这些应该在 Project 2 中已经设置好了。本项目规范将使用 `python3` 作为示例命令；根据你的系统，你可能需要使用 `python` 或 `py`。我们将使用 Logisim Evolution，这是一个基于 Java 的 GUI 程序，因此我们强烈建议你在安装了 Java 并**直接连接了显示器**的笔记本电脑或台式机上工作。不建议通过 Hive SSH 会话进行窗口转发，这可能导致不稳定和各种麻烦。

请按照这个 Google 表单中的说明获取仓库：[https://docs.google.com/forms/d/e/1FAIpQLSf6m3CjVF1IxwM6l1dTRjeAtTqwk_5974pOFvhdwCr0ZYrqDQ/viewform?usp=sf_link](https://docs.google.com/forms/d/e/1FAIpQLSf6m3CjVF1IxwM6l1dTRjeAtTqwk_5974pOFvhdwCr0ZYrqDQ/viewform?usp=sf_link)。填写完表单后，克隆你的 GitHub Classroom 仓库，并将起始代码仓库添加为远程仓库：

```bash
$ git clone YOUR_REPO_URL
$ cd YOUR_REPO_NAME
$ git remote add starter https://github.com/61c-teach/fa20-proj3-starter.git
$ git pull starter master
```

如果我们对起始代码做了修改，你可以通过 `git pull starter master` 来更新你的仓库。

---

## 任务 1：算术逻辑单元（ALU）

你的第一个任务是创建一个 ALU，它支持我们 ISA（将在下一节详细介绍）中所有指令所需的操作。请注意，我们对待溢出的方式与 RISC-V 处理无符号指令时一样，也就是说，我们忽略溢出。

我们在 `alu.circ` 中为你提供了一个 ALU 的骨架。它有三个输入：

| 输入名称 | 位宽 | 描述 |
| --- | --- | --- |
| A | 32 | ALU 运算中输入 A 的数据 |
| B | 32 | ALU 运算中输入 B 的数据 |
| ALUSel | 4 | 选择 ALU 应执行的操作（见下方操作及其对应开关值列表） |

……以及一个输出：

| 输出名称 | 位宽 | 描述 |
| --- | --- | --- |
| Result | 32 | ALU 运算的结果 |

下面是需要你实现的 ALU 操作列表，以及它们对应的 ALUSel 值。所有操作都是必需的。允许并鼓励你使用 Logisim 内置模块来实现算术运算。**请注意，由于 Logisim 中新增了带符号乘法器，使得实现 mulh 变得和 mulhu 一样简单，因此 mulh 不再是额外加分的项目了。**

| 开关值 | 指令 |
| --- | --- |
| 0 | add：`Result = A + B` |
| 1 | and：`Result = A & B` |
| 2 | or：`Result = A | B` |
| 3 | xor：`Result = A ^ B` |
| 4 | srl：`Result = (unsigned) A >> B` |
| 5 | sra：`Result = (signed) A >> B` |
| 6 | sll：`Result = A << B` |
| 7 | slt：`Result = (A < B (signed)) ? 1 : 0` |
| 8 | 未使用 |
| 9 | 未使用 |
| 10 | mul：`Result = (signed) (A * B)[31:0]` |
| 11 | mulhu：`Result = (A * B)[63:32]` |
| 12 | sub：`Result = A - B` |
| 13 | bsel：`Result = B` |
| 14 | mulh：`Result = (signed) (A * B)[63:32]` |

在实现 `mul` 和 `mulh` 时，注意乘法模块有一个 "Carry Out" 输出（加法器模块也有这个，但你不需要用到它）。

你可以对 `alu.circ` 做任何修改，但输出**必须**遵循上述规定的行为。如果你创建了额外的子电路，它们也必须放在 `alu.circ` 中（你不能新建 `.circ` 文件）。此外，你的 ALU **必须**能够放入提供的测试装置 `alu_harness.circ` 中。这意味着你应该注意不要移动输入或输出。要验证你的改动没有破坏任何东西，可以打开 `alu_harness.circ`，确保没有错误且电路运行正常。

**提示：**

- `add` 已经为你做好了；在实现其他模块时，可以放心地使用类似的结构。
- 你可以将光标悬停在模块的输入/输出上，获取关于该输入/输出的更详细信息。
- 如果你想了解每个模块的更多细节，请前往 Help->Library Reference 获取关于该模块及其输入输出的更多信息。
- 在实现 `sra` 和 `srl` 时，你可能会发现位拆分器（bit splitters）或扩展器（extenders）很有用。
- 使用隧道（tunnels）！它们能让你的布线更整洁、更易读，并减少遇到交叉线或意外错误的机会。
- 在决定要输出哪个模块的输出时，多路选择器（MUX）可能会很有用。换句话说，可以考虑简单地在所有模块中处理输入，然后输出你选择的那一个。

---

### ALU 测试

我们在 `tests/part_a/alu` 目录中提供了一组 ALU sanity 测试。运行该组的测试运行器将执行 ALU 测试，并在 `tests/part_a/alu/student_output` 目录中生成输出。提醒一下，你可以在[这个视频](https://inst.eecs.berkeley.edu/~cs61c/fa20/lectures/videos?ytvid=qFIPWt9Pu7I)中找到调试演示。

```bash
$ python3 test_runner.py part_a alu
```

我们还提供了 `binary_to_hex_alu.py`，它可以为你将这些输出解释成可读的格式。使用方法如下：

```bash
$ cd tests/part_a/alu
$ python3 binary_to_hex_alu.py PATH_TO_OUTPUT_FILE
```

例如，要以可读格式查看 `reference_output/alu-add-ref.out`，你可以这样做：

```bash
$ cd tests/part_a/alu
$ python3 binary_to_hex_alu.py reference_output/alu-add-ref.out
```

如果你想查看你的输出与参考解之间的差异，请将可读输出放入新的 `.out` 文件并对它们执行 `diff`。例如，对于 `alu-add` 测试，你可以这样做：

```bash
$ cd tests/part_a/alu
$ python3 binary_to_hex_alu.py reference_output/alu-add-ref.out > reference.out
$ python3 binary_to_hex_alu.py student_output/alu-add-student.out > student.out
$ diff reference.out student.out
```

---

## 任务 2：寄存器文件（RegFile）

正如你在课上学到的，RISC-V 架构有 32 个寄存器。对于本项目，**我们将实现全部 32 个寄存器**。为了便于调试，**我们编写了 regfile 使其暴露下面指定的 9 个端口**。请确保这些寄存器的值连接到正确的输出上。

你的 RegFile 应该能够在不影响任何其他寄存器的情况下，对给定 RISC-V 指令中指定的寄存器进行写入或读取。有一个值得注意的例外：即使某条指令试图写入 x0，你的 RegFile 也**不应**写入 x0。请记住，零寄存器（zero register）的值应**始终**为 0x0。你不应在 RegFile 中的任何位置对时钟进行门控（gate）：时钟信号应**始终**直接连接到寄存器的时钟输入端，而不经过**任何**组合逻辑。

暴露的寄存器及其对应编号如下表所示。

| 寄存器编号 | 寄存器名称 |
| --- | --- |
| x0 | x0 |
| x1 | ra |
| x2 | sp |
| x5 | t0 |
| x6 | t1 |
| x7 | t2 |
| x8 | s0 |
| x9 | s1 |
| x10 | a0 |

我们在 `regfile.circ` 中为你提供了寄存器文件的骨架。寄存器文件电路有六个输入：

| 输入名称 | 位宽 | 描述 |
| --- | --- | --- |
| Clock | 1 | 提供时钟的输入。该信号可以传入子电路，或直接连接到 Logisim 中存储单元的时钟输入端，但除此之外不应被门控（即不要对其取反，不要将其与任何东西做 "与" 运算等）。 |
| RegWEn | 1 | 决定在下一个时钟上升沿是否将数据写入寄存器文件。 |
| Read Register 1 (rs1) | 5 | 决定哪个寄存器的值被送到 Read Data 1 输出，见下文。 |
| Read Register 2 (rs2) | 5 | 决定哪个寄存器的值被送到 Read Data 2 输出，见下文。 |
| Write Register (rd) | 5 | 决定在下一个时钟上升沿将哪个寄存器设置为 Write Data 的值（假设 RegWEn 为 1）。 |
| Write Data (wb) | 32 | 决定在下一个时钟上升沿（假设 RegWEn 为 1）将什么数据写入由 Write Register 输入所标识的寄存器。 |

寄存器文件还有以下输出：

| 输出名称 | 位宽 | 描述 |
| --- | --- | --- |
| rs1 | 32 | 由 Read Register 1 输入所标识的寄存器的值驱动。 |
| rs2 | 32 | 由 Read Register 2 输入所标识的寄存器的值驱动。 |
| `ra` Value | 32 | 始终由 `ra` 的值驱动（这是一个调试/测试输出）。 |
| `sp` Value | 32 | 始终由 `sp` 的值驱动（这是一个调试/测试输出）。 |
| `t0` Value | 32 | 始终由 `t0` 的值驱动（这是一个调试/测试输出）。 |
| `t1` Value | 32 | 始终由 `t1` 的值驱动（这是一个调试/测试输出）。 |
| `t2` Value | 32 | 始终由 `t2` 的值驱动（这是一个调试/测试输出）。 |
| `s0` Value | 32 | 始终由 `s0` 的值驱动（这是一个调试/测试输出）。 |
| `s1` Value | 32 | 始终由 `s1` 的值驱动（这是一个调试/测试输出）。 |
| `a0` Value | 32 | 始终由 `a0` 的值驱动（这是一个调试/测试输出）。 |

你 `regfile.circ` 文件顶部的测试输出是为了测试和调试而设置的。如果你是在实现一个真正的寄存器文件，你会省略那些输出。但在我们的情况下，请确保它们被正确包含——如果没有，你将无法通过。

你可以对 `regfile.circ` 做任何修改，但输出必须遵循上述规定的行为。如果你创建了额外的子电路，它们也必须放在 `regfile.circ` 中（你不能新建 `.circ` 文件）。此外，你提交的 `regfile.circ` **必须**能够放入我们提供的 `regfile_harness.circ` 文件中。这意味着你应该注意不要移动输入或输出。要验证你的改动没有破坏任何东西，可以打开 `regfile_harness.circ`，确保没有错误且电路运行正常。

**提示：**

- 充分利用复制粘贴！最好先完整地做好一个寄存器，然后把它作为模板来复制其他寄存器，以避免重复劳动。
- 我建议你不要使用 MUX 上的 enable 输入。实际上，你可以关闭这个功能。我还建议你把 "three-state?" 也关闭。看看 Logisim 寄存器的所有输入，了解它们各自的作用。
- 再次强调，MUX 是你的朋友，但 DeMUX 也是。
- 想想一条指令执行完毕后寄存器文件中发生了什么。哪些值改变了？哪些值保持不变？寄存器是时钟触发的——这意味着什么？
- 请记住，寄存器除了时钟输入外，还有一个可用的 "enable" 输入。
- `x0` 的值是什么？

---

### RegFile 测试

我们在 `tests/part_a/regfile` 目录中提供了一组 RegFile sanity 测试。运行该组的测试运行器将执行 ALU 测试，并在 `tests/part_a/regfile/student_output` 目录中生成输出。

```bash
$ python3 test_runner.py part_a regfile
```

我们还提供了 `binary_to_hex_regfile.py`，它的用法与任务 1 中的 `binary_to_hex_alu.py` 类似。