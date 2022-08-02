## 代码说明

### 环境配置

Python 版本：3.7.6
CUDA 版本：10.1

所需环境在 `requirements.txt` 中定义。

### 数据

* 使用大赛提供的有标注数据（10万）和无标注数据（100万）。
* 未使用任何额外数据。

### 代码结构

```
./
├── README.md
├── requirements.txt      		# Python包依赖文件 
├── init.sh               		# 初始化脚本，用于准备环境
├── train.sh              		# 模型训练脚本
├── inference.sh          		# 模型测试脚本 
├── src/                  		# 核心代码
│   ├── category_id_map.py		# 结果类别映射代码
│   ├── requirements.txt		# 预测时环境配置（与预训练环境相同）
│   ├── config.py				# 预测时的配置
│   ├── data_helper.py			# 数据处理代码
│   ├── inference.py			# 预测代码
│   ├── job1					# 第一个模型
│   │   ├── category_id_map.py  # 结果类别映射代码
│   │   ├── config.py			# 第一个模型训练时的配置
│   │   ├── data_helper.py		# 数据处理代码
│   │   ├── evaluate.py			# 模型评估代码
│   │   ├── main.py				# 模型训练代码
│   │   ├── model.py			# 模型代码
│   │   ├── requirements.txt	# 第一个模型训练时的环境配置（与预训练环境相同）
│   │   ├── swa_from_ema.py		#生成swa模型代码
│   │   └── util.py				# 工具函数代码
│   ├── job2					# 第二个模型
│   │   ├── category_id_map.py	# 结果类别映射代码
│   │   ├── config.py			# 第一个模型训练时的配置
│   │   ├── data_helper.py		# 数据处理代码
│   │   ├── evaluate.py			# 模型评估代码
│   │   ├── main.py				# 模型训练代码
│   │   ├── model.py			# 模型代码
│   │   ├── requirements.txt	# 第二个模型训练时的环境配置
│   │   └── util.py				# 工具函数代码
│   ├── model.py				# 模型代码
│   ├── pre_train				# 预训练
│   │   └── requirements.txt	# 预训练时的环境配置
│   └── third_party				# 第三方开源代码
│       └── qq_unibert
│           ├── model
│           │   └── model_uni.py # 模型代码
│           └── pre_train        # 预训练
│               ├── category_id_map.py
│               ├── config.json
│               ├── config.py
│               ├── create_optimizer.py
│               ├── data_cfg.py
│               ├── masklm.py
│               ├── model_cfg.py
│               ├── pretrain.py
│               ├── pretrain_cfg.py
│               ├── qq_dataset.py
│               ├── qq_uni_model.py
│               ├── requirements.txt
│               └── utils.py
├── data/				  # 预训练模型与预测所需模型均在该目录下	
│   ├── annotations/      # 数据集标注（仅作示意，无需上传）
│   ├── zip_feats/        # 数据集视觉特征（仅作示意，无需上传）



```



### 预训练模型

* 使用了 huggingface 上提供的 `hfl/chinese-roberta-wwm-ext` 模型。链接为： https://huggingface.co/hfl/chinese-roberta-wwm-ext

### 算法描述

* 模型参考2021_QQ_AIAC_Tack1_1st，链接为：https://github.com/zr2021/2021_QQ_AIAC_Tack1_1st
* 算法流程
  * 首先将文本（title,asr,ocr）拼接起来，然后过一个embedding layer得到文本的embedding。
  * 视频特征经过一个线性层+激活函数+embedding layer得到视频的embedding。
  * 将文本和视频的embedding拼接起来，过一个BertEncoder。最后做一下mean pooling再经过一个线性层做200分类。
* 预训练采用了  Image-TextMatching, Mask language model, Mask frame model 三个任务
* 采用投票方式进行多模型融合



### 性能

离线测试性能：0.6983
B榜测试性能：0.693455

### 运行配置与耗时

配置：4核32G + 1 V100

预训练：约110小时

模型训练：约35小时

预测 ：约1.25小时

### 运行流程说明

依次执行init.sh, train.sh, inference.sh这三个脚本 

由于训练过程中涉及到两种不同的环境

所以安装各阶段环境的代码将在 train.sh 与 inference.py中进行
