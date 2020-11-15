---
title: 深入理解CMA
date: YYYY-MM-DD HH:mm:ss
categories: 
- Linux内存管理
tags:
- 架构一体化
- 特殊的内存需求
- 结语
---
原文链接：http://lwn.net/Articles/486301/
连续的内存分配器（或CMA），在LWN上从2011年6月以来，已经发展到允许大并且物理上连续的内存块的分配。原理简单，它已经成长相当复杂，需要多个子系统之间的合作。依据个人观点说，CMA也有工作要做，并获得关注。在这篇文章中，我将介绍如何使用CMA以及如何将其与给定的平台集成。

从设备驱动程序的作者的角度看，不用做任何修改。CMA集成到DMA子系统内部，所以对DMA普通的API调用（如dma_alloc_coherent()）是可以照常工作的。事实上，设备驱动程序应该不需要直接调用CMA的API接口，因为CMA没有对总线地址通过它的页面和页面帧号（PFNs）进行内核的映射，也没有提供机制来保证高速缓存的一致性。

想了解更多信息，请看[Documentation/DMA-API.txt](http://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/tree/Documentation/DMA-API.txt?id=805a6af8dba5dfdd35ec35dc52ec0122400b2610)和[ Documentation/DMA-API-HOWTO.txt](http://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/tree/Documentation/DMA-API-HOWTO.txt?id=805a6af8dba5dfdd35ec35dc52ec0122400b2610)。这两份文件描述了DMA提供的接口，以及应用实例。

# 架构一体化

当然，有人把CMA与DMA子系统集成。这只要简单的几步很少的修改。

CMA的工作原理是在系统启动时的早期预留内存。这段内存，称作CMA区域或CMA上下文，稍后归还给buddy系统分配器，以便它可以通过常规的应用使用到。怎么样来预留呢？通过调用如下接口：

```
void dma_contiguous_reserve(phys_addr_t limit);
```

一旦完成底层“memblock”分配器的初始化，但在buddy分配器设置前。memblock分配器初始化，例如：在ARM上，它被称为arm_memblock_init()，而在x86是memblock的设置是在刚过setup_arch()的时候.

该limit参数指定在哪个物理地址以上无法设置CMA内存。其意图是CMA上下文限制到DMA能够处理的地址。在ARM里，limit为arm_dma_limit和arm_lowmem_limit中较小的一个。传递0将允许CMA分配其上下文任意想要的高地址。唯一的限制是，预留内存必须属于同一区Zone。

预留内存的数量取决于几个的Kconfig选项和CMA内核参数。我在下面的文章对他们进一步描述。

所述dma_contiguous_reserve()函数会预留内存，并且为CMA准备好。在某些架构上(例如ARM)还有一些体系结构相关的初始化工作。可以调用如下接口：

```
void dma_contiguous_early_fixup(phys_addr_t base, unsigned long size);
```

这是体系结构的职责：提供它的实现连同它在asm/dma-contiguous.h头文件里的声明。如果给定的架构不需要任何特殊的处理，它应该提供一个空函数的定义。

这将被很早就调用到，因此一些子系统调用（如用kmalloc()）将不可用。此外，它可以被多次调用（因为，如下所述，可能存在多个CMA上下文）。

要做的第二件事是改变架构的DMA实现使用整个DMA机制。要分配CMA内存可以使用：

```
struct page *dma_alloc_from_contiguous(struct device *dev, int count, unsigned int align);
```

第一个参数是代表执行分配的设备。
第二个参数指定分配页数（而不是字节或顺序）。
第三个参数是表示2^align个页面对齐。
它使得分配缓冲区的物理地址以2^align对齐。为了避免碎片，如果参数传的是0。值得注意的是，有一个的Kconfig选项（CONFIG_CMA_ALIGNMENT它指定由函数接受最大对齐）。它的默认值是8的含义256页的对齐方式。

返回值是count个已分配页面的第一个页的指针。

要释放分配的缓冲区，需要调用：

```
bool dma_release_from_contiguous(struct device *dev, struct page *pages, int count);
```

dev和count参数都和上面的一样，而pages是dma_alloc_from_contiguous()的返回值。如果传递给函数的区域并不是从CMA分配的，该函数将返回false。否则，它将返回true。这消除了需要更高级别的功能来跟踪内存是从CMA分配的还是其他方法分配的。

要注意的是dma_alloc_from_contiguous()可能无法从原子上下文被调用。它执行一些“重”的操作，如页面迁移，直接回收等，这可能需要一段时间。正因为如此，使dma_alloc_coherent()和相关接口不能出现在原子上下文，该架构需要在原子上下文使用不同的分配内存方法。

最简单的解决办法是在开机启动阶段划出一块内存和从这块内存里面执行原子分配。这其实是ARM正在做的。现有的架构很可能已经拥有原子分配一个特殊的路径。

# 特殊的内存需求

在这一点上，大部分车手都应该“只要能工作”。他们使用DMA API，它调用CMA。生活是美好的。除了某些设备可能有特殊的内存需求。例如，三星的S5P多格式编解码器需要缓冲可以位于不同的存储体（其允许读取它们通过两个存储器通道，从而增加存储器带宽）。此外，有人可能想单独的和一些设备的分配分离开，以防止CMA区域的碎片化。

CMA的环境工作。设备使用一个全局区默认，但私有上下文可作为良好。之间有一个多到一的映射结构装置 S和 结构CMA（即CMA上下文）。这意味着单个设备驱动器需要具有单独的结构体装置 的对象到使用一个以上的CMA上下文，而在同一时间的几个结构设备的对象可能指向同一CMA上下文。

要指定一个CMA环境到设备，所有的人需要做的就是调用：

```
int dma_declare_contiguous(struct device *dev, unsigned long size,
			       phys_addr_t base, phys_addr_t limit);
```

正如 dma_contiguous_reserve（） ，这需要memblock初始化之后，但太多的内存会从它抓起之前调用。对于ARM平台，将调用此功能方便的地方是在机器的储备（）回调。这将不会对自动探测装置或那些加载模块的工作，因此，如果这些类型的设备的需要的CMA的上下文，将需要一些其它机制。

的函数的第一个参数是新的上下文将被分配给该设备。第二个指定字节大小（不在页）保留的区域。第三个是区域或零的物理地址。最后一个具有相同的含义 dma_contiguous_reserve（）的限制的说法。返回值是零或负的错误代码。

有许多“私人”领域怎么可以声明，即限制CONFIG_CMA_AREAS。其缺省值是7，但它可以在需要时被安全地增加。

事情变得有点复杂，如果同一非默认CMA方面需要通过两个或多个设备使用。目前API没有提供一个简单的方法来做到这一点。有什么可以做的是使用dev_get_cma_area（）找出CMA区域一台设备正在使用，并dev_set_cma_area（）来设置相同的情况下到另一个设备。此顺序必须不早叫比postcore_initcall（） 。这里是如何看起来：

```
static int __init foo_set_up_cma_areas(void)
{
	struct cma *cma;

	cma = dev_get_cma_area(device1);
	dev_set_cma_area(device2, cma);
	return 0;
}
postcore_initcall(foo_set_up_cma_areas);
```

作为事实上，没有什么特别之处是由创建的默认情况下 dma_contiguous_reserve（）函数。它绝不是必需的，该系统将没有它工作。如果没有默认情况下， dma_alloc_from_contiguous（）将返回 NULL对于没有指定区域的设备。 dev_get_cma_area（）可以用于这种情况，并分配失败来区分。

dma_contiguous_reserve（）不采取大小作为参数，所以它是如何知道多少内存应该保留？这方面有两种信息来源：

有一组的Kconfig选项，指定保留的默认大小。所有这些选项位于下的菜单的Kconfig“设备驱动程序”»“通用驱动程序选项”»“连续内存分配器”。它们允许从四种可能性中选择：大小可以以兆字节的绝对值，总存储器的百分比，这两个较小的，或者是两者中较大的。默认值是分配16的MIB。

还有一个CMA =内核命令行选项。它让之一指定的区域在启动时的大小，而不需要重新编译内核。此选项指定字节大小，并接受通常的后缀。

那么它是怎样工作的？

要了解CMA是如何工作的，我们需要了解一些迁移类型和pageblocks。

当从好友分配器请求存储器，一个提供一个gfp_mask。除其他事项外，它指定请求的页面（S）的“迁移类型”。其中的迁移类型是MIGRATE_MOVABLE。其背后的想法是，从可移动的页数据可以被迁移（或移 动，因此而得名），这对于磁盘高速缓存，过程页等效果很好

为了保持与同类型的迁移一起页面，好友分配器组页面为“pageblocks，”每一个都具有迁移类型分配给它。分配器然后试图从pageblocks与对应于所述请求的类型分配的页面。如果这是不可能的，但是，它 会采取不同pageblocks页面，甚至可以改变一个pageblock的迁移类型。这意味着，一个不可移动的页可以从被分配MIGRATE_MOVABLE pageblock这也可导致在该pageblock改变其迁移类型。这是不可取的CMA，所以它引入了一个MIGRATE_CMA其中有一个重要的属性类型：只有移动网页可以从分配 MIGRATE_CMA pageblock。

所以，在开机的时候，当dma_contiguous_reserve（）和/或 dma_declare_contiguous（）函数被调用，CMA会谈memblock保留RAM的一部分，只是为了与底层pageblock的迁移类型集还给好友系统以后到MIGRATE_CMA。最终的结果是，所有的保留页最终回到在好友分配器，因此它们可用于满足移动页分配。

在CMA分配，dma_alloc_from_contiguous（） 选择页面范围，并呼吁：

```
int alloc_contig_range(unsigned long start, unsigned long end,
	                    unsigned migratetype);
```

在开始和结束参数指定的页面帧号（或PFN目标存储器范围）。最后一个参数， migratetype，表示基本pageblocks的迁移类型; 在CMA的情况下，这是 MIGRATE_CMA。这个函数做的第一件事是标记包含内pageblocks [起点，终点）范围内 MIGRATE_ISOLATE。好友分配器绝不会碰pageblock与迁移类型。更改迁移类型不会奇迹般地免费的网页，虽然，这就是为什么__alloc_conting_migrate_range（）一次调用。它扫描PFN范围，并寻找可迁移离开页面。

迁移是复制页面到系统内存的其他一些部分和更新任何引用它的过程。前者是直截了当，后者是由存储器管理子系统处理。其数据迁移后，旧的网页给它回哥们分配器中被释放。这就是为什么包含pageblocks不得不被标记为MIGRATE_ISOLATE预先。如果他们被赋予了不同的迁移类型，哥们分配器不会三思利用它们来满足其他分配请求。

现在所有的页面alloc_contig_range（）在乎的是（希望）免费。该函数接受他们的好友系统走，然后更改pageblock的迁移类型回到 MIGRATE_CMA。然后这些网页被返回给调用者。

释放内存是简单得多的过程。 dma_release_from_contiguous（）代表大部分工作于：

```
void free_contig_range(unsigned long pfn, unsigned nr_pages);
```

它简单地遍历所有的页面，并把他们回到伙伴系统。

# 结语

连续内存分配器patch set已经经历了很久的开发，从第一版（以及其前身甚至更长的时间 - 物理内存管理发布三年前）。在开发过程中，它失去了一些功能，但到现在已经变得更好了。在复杂的平台上，CMA很可能是不会单独使用的，但它将与ION和dmabuf组合使用。

即使是第23版，CMA还不够完善，并一如既往，还是有很多可以改进的地方。虽然如此，我还是希望，它会合入内核mm代码树，更多开发者可以在此基础上做开发，以造福所有人。