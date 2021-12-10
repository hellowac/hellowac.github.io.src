---
title: python-etl工具介绍
date: 2021-12-11 00:00:46
tags:
---

原文：<https://blog.karatos.in/a?ID=2837049d-fa78-4217-8c0d-f7cc7c9410a6>

## ETL之Python数据转换工具详解（译文）

### **关于ETL**

As a data warehouse system, ETL is a key part. If it is big, ETL is a data integration solution, if it is small, it is a tool to pour data. Recalling all these years of work, there is really a lot of work dealing with data migration and conversion. But those jobs are basically one-time jobs or a small amount of data, which can be done by using access, DTS, or compiling a small program yourself. However, in the data warehouse system, ETL has risen to a certain theoretical height, which is different from the original tools used for small troubles. What is the difference? You can see from the name that people have divided the data dumping process into three steps. E, T, and L stand for extraction, conversion, and loading, respectively.

作为数据仓库系统，ETL是关键部分。 如果系统很大的话，ETL是数据整合方案，如果系统很小的话，就是倒数据的工具。 回顾这些年的工作，确实有很多处理数据迁移和转换的工作。 但是那些作业基本上都是一次性作业或者少量的数据，可以通过access、DTS或者自己编译一个小程序来完成。 但是，在数据仓库系统中，ETL上升到了一定的理论高度，区别于原来用于小麻烦的工具。 有什么不同？ 从名字上就可以看出人们将数据转储过程分为三个步骤。 E、T 和 L 分别代表提取、转换和加载。

In fact, the ETL process is the process of data flow, from different data sources to different target data. But in the data warehouse, ETL has several characteristics.

* One is data synchronization. It is not a one-time dump of data and then pulls it out. It is a regular activity and runs in a fixed cycle. Even now, some people have proposed real-time ETL. concept.
* The second is the amount of data, which is generally huge. It is worth you to split the process of data flow into E, T, and L.

事实上, ETL过程就是处理数据流的过程，从不同的数据源到不同的目标数据。但是在数据仓库中，ETL 有几个特点。

* 一是数据同步。它不是一次性转储数据然后将其取出。这是一项定期活动，并以固定周期运行。即使是现在，也有人提出了实时 ETL概念。
* 二是数据量，一般都是巨大的。它值得你把数据处理流程拆分成E、T、L。

There are many mature tools that provide ETL functions, such as datastage, powermart, etc., not to mention their quality. From an application point of view, the ETL process is actually not very complicated. These tools bring great convenience to data warehouse engineering, especially the convenience of development and maintenance. On the other hand, developers can easily get lost in these tools.

提供ETL功能的成熟工具有很多，比如`datastage`、`powermart`等，更不用说它们的质量了。从应用的角度来看，ETL的过程其实并不是很复杂。这些工具给数据仓库工程带来了极大的便利，尤其是开发和维护的便利。另一方面，开发人员很容易迷失在这些工具中。

For example, VB is a very simple language and a very easy-to-use programming tool. It is very fast to learn, but how many real VB masters are? Microsoft’s products usually have a principle of “treat users as fools”. Under this principle, Microsoft’s things are really easy to use, but for developers, if you treat yourself as a fool, then it’s true. Silly.

例如，VB 是一种非常简单的语言，也是一种非常易于使用的编程工具。学起来很快，但是真正的VB高手有多少呢？微软的产品通常都有一个“把用户当傻子”的原则。在这个原则下，微软的东西确实好用，但是对于开发者来说，如果你把自己当傻子，那就是真的了。真愚蠢。

The same is true for ETL tools. These tools provide us with a graphical interface, allowing us to focus on the rules in order to improve development efficiency. In terms of usage effects, it is true that these tools can be used to build a job very quickly to process certain data, but from an overall point of view, it does not necessarily mean that his overall efficiency will be much higher. The problem is not mainly in the tools, but in the design and developers. They were lost in the tools and did not explore the essence of ETL.

 ETL 工具也是如此。这些工具为我们提供了图形界面，让我们可以专注于规则以提高开发效率。就使用效果而言，这些工具确实可以非常快速地构建作业来处理某些数据，但从整体来看，并不一定意味着他的整体效率会高很多。问题主要不在于工具，而在于设计和开发人员。他们迷失在工具中，没有探索 ETL 的本质。

It can be said that these tools have been applied for so long and used in so many projects and environments. It must have its success, and it must reflect the essence of ETL. If we don't look at the ideas behind them through the simple use of these tools on the surface, what we finally make will be independent jobs. Integrating them will still have a huge workload. Everyone knows that "the combination of theory and practice",**if you surpass in a field, you must reach a certain height on the theoretical level.**

可以说，这些工具应用了这么久，用在了这么多的项目和环境中。 一定有它的成功，一定要体现ETL的精髓。 如果我们不通过表面上这些工具的简单使用来审视它们背后的想法，我们最终做出来的将是独立的工作。 整合它们仍然会有巨大的工作量。 大家都知道“理论与实践相结合”，**如果你在一个领域超越，你必须在理论层面上达到一定的高度。**

Let's take a look at the Python data conversion tool for ETL. The specific content is as follows:

我们来看看 ETL 的 Python 数据转换工具。 具体内容如下：

A few days ago, I went to Reddit to ask if Python should be used for ETL-related conversions, and the overwhelming answer was "yes".

前几天，我去Reddit问Python是否应该用于ETL相关的转换，压倒性的答案是“是”。

However, although my Redditor colleagues enthusiastically support the use of Python, they recommend studying libraries other than Pandas-because of concerns about the performance of Pandas on large data sets.

经过研究，我发现了很多用于数据转换的Python库：有的提高了Pandas的性能，有的则提供了自己的解决方案。

I can't find a complete list of these tools, so I think I can use the research I've done to compile a tool-if I missed something or got it wrong, please let me know!

我找不到这些工具的完整列表，所以我想我可以利用我所做的研究来编写一个工具——如果我遗漏了什么或弄错了，请告诉我！

### **Pandas**

Website: <https://pandas.pydata.org/>

#### 概览

Pandas当然不需要介绍，但我还是给它介绍一下。

Pandas 在 Python 中加入了 DataFrame 的概念，被广泛应用于数据科学界，用于分析和清理数据集。 它作为 ETL 转换工具非常有用，因为它使操作数据变得非常简单和直观。

#### **优点**

* 广泛用于数据处理
* 简单直观的语法
* 与其他 Python 工具（包括可视化库）的良好集成
* 支持常用数据格式（从SQL数据库、CSV文件等读取）

#### **缺点**

* 由于它将所有数据加载到内存中，因此无法缩放，对于非常大（大于内存）的数据集可能是错误的选择

#### 相关阅读

* 10 minutes Pandas
* Data Processing for Pandas Machine Learning

### **Dask**

Website: <https://dask.org/>

#### **概览**

根据他们的网站，“Dask 是一个灵活的 Python 并行计算库。”

本质上，Dask 扩展了 Pandas 等通用接口以用于分布式环境——例如 Dask DataFrame 模仿它。

#### **优点**

* 可扩展性-Dask可以在本地计算机上运行并扩展到集群
* 能够处理低内存数据集
* 即使在相同的硬件上，使用相同的功能也可以提高性能（由于并行计算）
* 以最少的代码更改从 Pandas 切换
* 旨在与其他 Python 库集成

#### **缺点**

* 除了并行之外，还有其他方法可以提高 Pandas 的性能（通常更显着）
* 如果你做少量的计算，没有任何好处
* Dask DataFrame 中未实现某些功能

#### 相关阅读

* Dask document
* Why every data scientist should use Dask

### **Modin**

Website: <https://github.com/modin-project/modin>

#### 概览

Modin 与 Dask 类似，它试图通过使用并行性和启用分布式 DataFrames 来提高 Pandas 的效率。 与 Dask 不同，Modin 基于 Ray（任务并行执行框架）。

Modin 相对于 Dask 的主要优势在于 Modin 可以自动处理跨计算机内核的数据分布（无需配置）。

#### **优点**

* Scalability-Ray offers more than Modin
* 完全相同的功能（即使在相同的硬件上）可以提高性能
* 以最少的代码更改从 Pandas 切换（更改导入(import)语句）
* 提供所有 Pandas 功能——比 Dask 更“嵌入式”的解决方案

#### **缺点**

* 除了并行之外，还有其他方法可以提高 Pandas 的性能（通常更显着）
* 如果你做少量的计算，没有任何好处

#### 相关阅读

* Modin documentation
* What is the difference between Dask and Modin?

### Petl

Website: <https://petl.readthedocs.io/en/stable/>

#### 概览

petl包含了pandas的很多功能，但是是为ETL设计的，所以缺少额外的功能，比如分析功能。 petl 对 ETL 的所有三个部分都有工具，但本文只关注数据转换。

虽然petl提供了转换表的功能，但其他工具（如pandas）似乎更广泛用于转换和文档化文档，所以petl的吸引力不大。

#### **优点**

* 最大限度地减少系统内存的使用，使其能够扩展到数百万行
* 用于在 SQL 数据库之间迁移
* 轻巧高效

#### 缺点

* 通过大大减少系统内存的使用，petl的执行速度会变慢——不建议在性能很重要的应用中使用
* 较少使用此列表中的其他解决方案进行数据处理

#### 相关阅读

1. Use Petl to quickly understand data conversion and migration
2. petl conversion document PySpark

### Spark

Website: <http://spark.apache.org/>

#### 概览

Spark 专为处理和分析大数据而设计，并提供多种语言的 API。 使用 Spark 的主要优点是 Spark DataFrames 使用分布式内存并利用延迟执行的优势，因此它们可以使用集群来处理更大的数据集，而 Pandas 等工具则做不到这一点。

如果要处理的数据非常大，并且数据操作的速度和规模都很大，那么Spark是ETL的理想选择。

#### 优点

* 可扩展性和对更大数据集的支持
* 在语法方面，Spark DataFrames 与 Pandas 非常相似
* 通过 Spark SQL 使用 SQL 语法进行查询
* 兼容其他流行的 ETL 工具，包括 Pandas（您实际上可以将 Spark DataFrame 转换为 Pandas DataFrame，允许您使用各种其他库）
* 与 Jupyter 笔记本电脑兼容
* 对 SQL、流和图形处理的内置支持

#### 缺点

* 需要一个分布式文件系统，比如 S3
* 使用CSV等数据格式会限制执行延迟，数据需要转换为Parquet等其他格式
* 缺乏对数据可视化工具（如 Matplotlib 和 Seaborn）的直接支持，而 Pandas 对这两种工具都提供了很好的支持

#### 相关阅读

* Apache Spark in Python: A Beginner's Guide
* Introduction to PySpark
* PySpark documentation (especially the syntax) is worth mentioning

虽然我希望这是一个完整的列表，但我不希望这篇文章太长！

确实有很多很多用于数据转换的 Python 工具，所以我包含了这部分，至少对于我错过的其他项目（我可能会在本文的第二部分进一步探讨这些项目）。

* bonobo <https://www.bonobo-project.org/>
* bubbles <http://bubbles.databrewery.org/>
* pygrametl <http://chrthomsen.github.io/pygrametl/>
* Apache Beam <https://beam.apache.org/>

### **最后**

我希望这份清单至少可以帮助您了解 `Python` 必须提供哪些工具来进行数据转换。 在进行这项研究后，我相信 `Python` 是 `ETL` 的绝佳选择——这些工具及其开发人员使其成为一个了不起的平台。
