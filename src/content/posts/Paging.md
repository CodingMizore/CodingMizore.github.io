---
title: "Operating System: Paging"
description: ""
pubDatetime: 2026-08-22T19:37:52+08:00
tags:
  - computerScience
  - operatingSystem
---
### 分页的引入

我们希望虚拟内存-物理内存映射(Virtual Address-Physical Address Mapping)有这两个特点：
* 灵活性：一个虚拟地址可以映射到任何一个物理地址；
* low overhead: 我们不希望用太长的时间、太多的空间维护VA-PA mapping. 

为了达成以上两个要求，我们引入分页(Paging)机制。Paging机制可以视为以上两个要求的折中。如果希望达成映射的**绝对**任意，那么就必然要为每个VA都维护对应的PA，这对于虚拟地址空间巨大的现代系统来说，这笔空间开销不可接受。因此，我们做出一个妥协：每一个Virtual Page可以映射到任意一个Physical Page Frame, 但每一页中的地址都是按顺序一一对应的。这样，既保持了相当大的灵活性，同时也减少了维护映射的开销（需要维护的映射数目缩小到了原来的$\frac{1}{\text{pageNumberSize}}$）. 

现在的我们的目标就是维护VPN(Virtual Page Number)到PFN(Page Frame Number)的映射。维护这个映射的数据结构被称为page table(页表)。
### 多级页表的引入

page table的一个朴素的实现方式是，维护一个线性的page table, 直接用VPN索引一个虚拟页对应的页表项(page table entry, PTE). 这种实现的问题是开销过大。对于4KB(12bits) page, 64bit虚拟地址空间，我们共有$2^{64-12}=2^{52}$个虚拟页，对应$2^{52}$个页表项，这明显是不可接受的内存开销。

注意到，通常的程序根本不会使用到如此大量级数量的虚拟页，因此我们没有必要像上述朴素方法一样为每一个VPN都维护映射。换句话说，我们想要：

> [!Idea]
> 设计一种仅为实际使用的虚拟页维护VPN-PFN Mapping的Page Table.
> 

这就引出了**多级页表**（Multi-level Page Table）. 回忆之前的连续线性页表，这样一个大页表会占据很多页的内存，并且其中大部分页的所有PTE都很可能不包含任何**被使用**的虚拟页的映射。对于这样的页，我们可以直接把它们丢掉，并在**某个结构**中将这**一整页**所有的PTE都标记为`invalid`，这样就节省出了大量的内存。原来我们需要一整页来**逐个**将这些PTE标注为`invalid`, 现在我们只需要一项即可. 

这里说到的**某个结构**，被称为**Page Directory**. 注意这里的一整页PTE存储的VA都会具有相同的高位（因为它们是连续的一整块），所以我们将这些高位作为page directory的索引。在进行地址翻译时，就可以取出VA的高位，在page directory中索引page directory entry(PDE)，如果发现`invalid`标记，就可以直接异常了。

这样，我们就只剩下少量包含**实际被使用的**VA-PA映射的页需要处理。我们将这些页像刚刚一样放入Page Directory, 但是标记为`valid`, 同时记录下这一页的PA. 这样，在进行地址翻译时，同样取出VA的高位，用它在page directory中索引PDE，如果发现`valid`标记，就可以取出记录该VA的翻译的PTE的PA，并去那里拿到PTE，完成翻译。

这就是一个二级页表。可以看到，它很好地完成了我们的设计目标：
* 如果一整串虚拟页都没有被使用，那么它们的PTE不会被维护，在朴素方案中带来的一整页的内存开销就被优化为`sizeof(PDE)`;
* 如果一串虚拟页中有被使用的页，这时它们的PTE才会被维护，它们的PTE所在的PFN会被记录到page directory中。

注意这种页表结构为页表本身的存储引入了灵活性：第二级可以存储在内存的任何一个区域，而不用像之前一样存储在一片连续区域中，因为每一个second level page的PFN都在page directory中有记录。

当虚拟内存空间很大时，就连page directory本身也可能变得很大。但这并不是困难的问题，只需要沿用二级页表的设计思路，为page directory自身也建立directory结构即可。这样就引入了三级/更高级的页表。
### TLB的引入

上述多级页表的方案确实极大地缓解了维护页表的空间开销，但同时引入了显著的时间开销（_No Silver Bullet!_）. 在最开始的朴素页表实现中，每次访存只需要额外一次查表；但是在二级页表中，就需要额外两次查表（第一次查page directory获得PDE，找到目标PTE所在的PFN； 第二次访存才能找到翻译所依赖的PTE！）。多级页表的额外开销就更多。

这个问题的解决也有一个简单而自然的思路。对于时间开销大但是频繁进行的操作，computer architects的解决方案出奇地一致，引入一层cache：这就是TLB(Translation Lookaside Buffer). TLB的机制和其他cache也并无不同：
* 进行地址翻译时，首先拿着VA去TLB中找，TLB中有这条VA的记录，就可以直接从TLB中拿到翻译；
* 如果TLB中没有，那就只好进行通常的translation procedure, 然后将得到的翻译写到TLB中。

> [!Notice]
> Work In Progress.







