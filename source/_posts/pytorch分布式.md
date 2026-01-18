---
title: pytorch 分布式多卡并行训练模型
date: 2020-03-24 22:13:34
tags: [deeplearning, pytorch, note]
categories: [pytorch]
---

## pytorch 单机多卡训练模型

### 修改代码

现在大部分github支持分布式训练，如果不支持需要做一下更改

需要加入参数--local_rank

```python
        parser.add_argument("--local_rank", type=int, default=0)
```

使用local_rank初始化设备

```python
        device = torch.device('cuda:{}'.format(args.local_rank))
        torch.cuda.set_device(args.local_rank)
        torch.distributed.init_process_group(backend="nccl", init_method="env://")
```

创建数据加载器

```python
        train_sampler = DistributedSampler(train_dataset)
        trainloader = torch.utils.data.DataLoader(train_dataset,batch_size=config.TRAIN.BATCH_SIZE_PER_GPU, shuffle=config.TRAIN.SHUFFLE and train_sampler is None,num_workers=config.WORKERS, sampler=train_sampler)
```

创建模型

```python
        model = nn.SyncBatchNorm.convert_sync_batchnorm(model)
        model = model.to(device)
        model = nn.parallel.DistributedDataParallel(
        model, device_ids=[args.local_rank], output_device=args.local_rank)
```

网上有很多教程，这里只做粗略介绍。

### 启动方式

```shell
        python -m torch.distributed.launch --nproc_per_node=2 train.py 参数
```
nproc_per_node 表示GPU的个数

## pytorch 多机多卡训练模型

### 环境配置

通过实验，发现需要多台机器之前相互可以ping通，才可以进行多机多卡训练。比如
* A主机与B主机连着同一个路由器，这样A主机与B主机是可以相互连通的。
* 如果在A主机下的容器主机CA，和B主机下的容器主机CB，他们之前是相互独立的，CA ping不通 CB，同样CB ping不通CA。所以需要借助一个插件weave，把A主机的docker与B主机的docker相互连通，这样CA与CB通过虚拟网关相互连通。

#### weave插件的使用

假如现在有3台主机A、B、C。把A当作主节点，B、C当作工作节点，要求B和C可以与A在同一局域网。

在A上运行
```shell
        weave launch --host A的ip地址 --ipalloc-range 10.32.0.0/12
```
在B、C上运行

```shell
        weave launch --host A的ip地址
```

#### 容器创建

在创建容器时，需要指定网络weave，这样才可以让容器之前相互连通。
```shell
    nvidia-docker run -itd --net=weave --shm-size=60G --name 容器名 镜像名 /bin/bash
```
如果需要挂载数据卷，可以使用 参数-v
```shell
    nvidia-docker run -itd -v /data/g9:/data --net=weave --shm-size=60G --name 容器名 镜像名 /bin/bash
```

进入容器，通过*ifconfig*可以查看网络。

### pytorch运行方式

```shell
    python -m torch.distributed.launch --nproc_per_node=2  --nnodes=2 --node_rank=0 --master_addr="10.44.0.0"   --master_port=6666 train.py 参数
```
