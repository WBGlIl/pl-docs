﻿# 关于语法

## 引言

　　有人说语法很重要。这点我实际上一直不否认，但一定程度上（除了[这篇文章](what-is-syntax.md)）并没有表达过一个整体上为什么有意无意地在语言设计和分析中避免“优化语法设计”这样的工作的理由，所以有必要概括一下观点。

　　因为上下文是编程语言，所以“语法”指的是 syntax ——这在自然语言中是“句法”，但编程语言不存在严格对应的“句”的概念；自然语言意义上的语法则是更一般的文法(grammar) 。

## 重要

　　语法很重要。考虑到使用频率，语法就像呼吸一样。也因此我不能忍受复杂到在日常使用时需要刻意注意的构造，并且有意远离不得不使用这些构造的场合。要是一个人时不时[潮式呼吸(en-US)](https://en.wikipedia.org/wiki/Cheyne–Stokes_respiration) 的话，那么大概是快要挂了。

## 变化

　　另一方面，既然众口难调是现实，通用目的语言中呈现的最终的语法就应该允许让用户定制。这更容易使之被排除出语义规则之外单独设计——如 Racket 这样的基于传统 Lisp 的 read macro 基础上扩展出来的方案。

　　这样的现实需求也体现语言设计中分离语法和语义规则（而不是铁板一块的混沌文法）的普遍必要性——考虑到应对需求而变更不成熟的语法设计的代价仍然不会小到哪去（未必困难，但对会设计并更感兴趣使用语义解决实际问题的语言设计者来说，不用普遍规则概括的细节几乎总是烦的），只有极端不在意语法的“一次性”的小规模[领域特定语言](https://zh.wikipedia.org/zh-cn/领域特定语言)设计中，才可能漠视这种模式而不造成麻烦。

## 复杂

　　需要刻意注意的构造通常来自语法规则的复杂性。这种复杂性是抽象的，但并不是很难体现。对设计者以外的用户，最主要负担这种复杂性的场合体现在语法分析(parse) 上——不论是对人还是对机器的实现而言。

　　解析复杂的语法需要更多的上下文切换(context switch) 。用“上下文切换”这种特定机器实现气息浓厚的说法，是因为这不仅是人脑应对复杂性的比喻，而是普遍事实。就是抽象的机器实现，在[抽象机(abstract machine)](http://www.ccs.neu.edu/home/matthias/redex-workshop/wed-aft.html) 操作语义的底层这恰恰是带来无法避免的开销的本质的麻烦之一——如函数调用和 `eval` 操作的 E(environment) 这样的上下文（虽然对应操作语义中（元语言）的真正的 context （所谓 hole ）中这仅仅是其中的一种）。

　　举例来说，解析 `1 + 2 + 3` 这样的表达式，不管是哪个方向，遇到不同的记号(token) 时总需要至少切换一次上下文并记忆状态；相比之下，用 `(+ 1 2 3)` 这样的同义表达式，在减小记号数量同时减少上下文切换数量上的效果立刻高下立判。

　　当然，机器的实现跟人脑相比通常并不计较运行时的开销，但实际情况当然远远例子中的规模，而且处处存在。就算机器快到运行时无所谓多余的开销，设计、实现和测试语法分析器(parser) 的普遍代价也不是零。

　　作为日常工具的语法需要有效，因此解析技术的复杂在工程上只能是妥协，而非适合通过复杂度来展现意义的话题。一旦复杂，那么已经说明某种失败。而以实现技术本身以外的进展在更大的背景（例如某个具体项目，或者作为评判某个语言设计话题的依据）下用复杂的分析器炫技，则多是无知（不知轻重）或无耻（有意搅混水）至少其一的行径。

## 意义

　　隐藏在复杂问题下的更一般的麻烦问题是，损失普遍性，并妨碍抽象的目的。

### 计算目的

　　对不方便使用而不适用 [AOT 编译(en-US)](https://en.wikipedia.org/wiki/Ahead-of-time_compilation) 的场合——包括人脑——解析语法构成了求值算法(evaluation algorithm) 的算法复杂性的基础，也构成了计算性上的(computational) ——即为了通过操作语义规则得到计算结果的——需要克服的复杂性基础。因此复杂的语法和语义一样会直接影响计算的效率。如果语法不是直觉上特别有目的的行为，语法不应该承担比语义更显著的角色。

　　一个语法上起到重要作用的构造的例子是[结合律](https://zh.wikipedia.org/zh-cn/结合律)。结合律用中缀风格的语法来表达时显得简洁易记，但使用前缀或后缀的写法就比较晦涩了。这说明中缀语法的优越吗？不，普遍来看恰恰相反，结合律实际上并不应该有计算上的普遍作用。这是一个“以辞害意”的例子。

### 本质问题

　　强调并不自觉依赖结合律这样的语法风格，在语法以外本质上是“二元操作中心主义”。这体现在二元操作的原生特殊支持——而扩展二元操作往往用[fold(en-US)](https://en.wikipedia.org/wiki/Fold_%28higher-order_function%29)实现。但是，若要反方向归纳到二元以下的操作上，又需要有特殊的规则。这和 Lisp 风格用 `(+)` 、 `(+ 1)` 、 `(+ 1 1)` ……这样的序列通吃任意有限参数个数的操作而归纳出一致性相比，显得并不自然。

　　当然，以二元操作中心的方法论用于抽象目的上并非全然是无效的。得益于[数学归纳法](https://zh.wikipedia.org/zh-cn/数学归纳法)和[递归](https://zh.wikipedia.org/zh-cn/递归)的表达形式，像 [cons pair](https://en.wikipedia.org/wiki/Cons) 这样的还原主义方法就体现出了单一的二元操作(cons) 的简洁而普遍的威力。这但是，体现这种适用性的目的是*建模(modeling)* ——为了得到抽象的描述和理解对象本身的构造。一旦目的改为通过变换不同粒度的对象以解决一般的问题——*计算(computing)* ，劣势就体现来了：直接使用二元操作解构运算的步骤来替代计算上的操作，会有复杂度上的困难。

　　这里的本质原因是，要解决的问题本身通常并不在乎二元操作这种具体的实现细节，而二元操作隐含的归纳法本质依赖的局域性可能就只能作为可选的实现细节才不至于阻碍问题的解决。实际是否形成阻碍，取决于算法是否仅依赖局域特性。例如，当对一个序列(sequence) 进行搜索时，元素访问操作是局域的，而比较操作是不限制局域性的，因此使用 cons pair 这样的方式来表达序列的构造并不阻碍只依赖局域操作的最基本的顺序迭代算法；但这样的构造并不能利用超出相邻元素的局域的性质——当这个序列全局有序时，使用 cons pair 的构造无法利用全局性而不能直接支持[降低算法复杂度的有效算法](https://zh.wikipedia.org/zh-cn/二分搜索算法)。再如，cons pair 的实现方式蕴含了序列中非预期存在的构造顺序这一细节，而暴露了构造、访问和析构操作可能的和顺序相关的[副作用](https://zh.wikipedia.org/zh-cn/函数副作用)，使结构上的实现细节（非预期地）泄漏到普遍行为上，阻碍[信息隐藏(en-US)](https://en.wikipedia.org/wiki/Information_hiding) 和抽象目的的准确表达。

　　另一个次要的困难表达本身在计算上的冗余——罗嗦。上述问题共同导致许多常用的抽象脱离使用二元操作构造表示的模型而存在独立的意义。不少时候，通过二元操作或其它还原论方法构造的模型仅仅起到模型论意义上的作用——例如，大多数时候人们不使用[集合论编码](https://zh.wikipedia.org/zh-cn/自然数的集合论定义)来使用自然数，即便是不使用加法之类套用模型会有算法复杂度问题的操作的场合。

### 一转攻势

　　着力于在两个现有数学体系之间建立联系（通过[态射](https://zh.wikipedia.org/wiki/态射)）的[范畴论](https://zh.wikipedia.org/zh-cn/范畴论)而使用本来并没有太多的槽点，不过其中一些不良语法习惯被某些语言奉为“函数式”正统设计以至于把一些用户迷得神魂颠倒就颇有些奇怪了。

　　讽刺的是，在这个背景下的最基础的[复合](https://ncatlab.org/nlab/show/composition)，传统语法也有[歧义](https://ncatlab.org/nlab/show/composition#Notation)，以至于要强调所谓的 diagrammic order 。然而注意到作为结合律的适用的典例，复合操作具有上面提及的所有问题。这种强调 diagram 这种先天被局域作用限制的语法已经在一定程度上误导了局部结构的在一般问题的解中所占的比重乃至歪曲了表达解时本应注意到的抽象。

## 解决

　　避免麻烦的语法和相关问题的大部分套路并不难：

* 除非必要，避免新造 DSL 。
  * 使用成熟的设计。
  * 使用通用目的语言构造 DSL ，而不是从头造轮子。
* 避免没有实际目的的中缀语法。
  * 尤其避免设计生造的新的中缀语法。
  * 使用类似 `x . y . z` 到 `. x y z` 这样的变换消除没有明确依赖结合律等二元操作没有明显特异作用的构造。

　　不过，不擅于独立思考以及观念上先入为主的偏见，可能仍然是难以纠正的。
