# PyHealth学习笔记

## data

这个模块定义了pyhealth中最基础的数据结构：Event和Patient

### data.Event

表示一个单一的临床事件，是data中的一个类，有如下参数：

event_type：str 表示这个临床事件的类型，如：'DIAGNOSES_ICD','PROCEDURES_ICD','MEDICINE'等，这个参数是必须的

timestamp：datetime，是可选的，表示event发生的时间，如果没有用当前时间

attr_dict：任何额外的关键数据都可以放在这个字典当中，以键值对的方式存储比如：code：str 是event的一个代号（428代表heart failure）；table：str，指示这个时间被记录在哪个表格中（'DIAGNOSES_ICD'就是一个表的名字）；vocabulary：str，表示疾病的编码表（ICD9CM等等）；visit_id：访问记录的唯一id；patient_id：病人的唯一id

![image-20250928153437459](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250928153437459.png)

### data.Patient

也是一个类，表示一个病人以及和他相关的临床数据的总合，作为一个容器存储和某一patient相关的events和visits，有如下参数：

patient_id：str  必须要有，独一无二

data_source：polars.DataFrame   包含和这个patient相关的全部事件的一个dataframe，通过这个类构建的一个数据对象才能作为Patient类的参数输入，这个数据中包含：patient_id、event_type、timestamp等等有用的字段，表示找这个patient的具体哪些信息，如在哪个表格里，id是多少这种

![image-20250928153455549](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250928153455549.png)

get_events：patient类中的常用方法，通过参数来选择具体的events，其中参数有：event_type：str数组  指定找那种类型的event；start：datetime数组  限定开始时间；end：datetime数组  限定结束时间；fileters：元组构成的列表，可以在这里做更多的限定

![image-20250928154134758](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250928154134758.png)

## datasets

这个模块用来把原始没有统一结构的数据处理成结构一致的数据集对象，具体参数如下：

root：指向具体的原始数据文件夹（如"mimiciii/1.4/"）\

tables：一个表格名称的列表，这些名称来自原始的数据库，这个参数指明具体哪些数据被用来构建你的数据集

code_mapping：默认值是None  这个参数指示code怎么变，如{"NDC": ("ATC", {"target_kwargs": {"level": 3}})}意味着pyhealth会自动把疾病编码从NDC格式转成ATC-3格式

dev：默认值是False，表示要不要启动dev模式，这种模式下只使用很小一部分的数据

refresh_cache：是否要更新缓存重新处理一遍数据

### datasets.BaseDataset

更为具体的一个数据集对象，有如下功能和参数：

root：同上

tables：同上

dataset_name：str  具体的数据集的名称

config：dict  内容从配置文件产生而来

global_event_df：ploars.LazyFrame  全部events的dataframe

dev：同上

collected_global_event_df：返回ploars.DataFrame数据类型  返回选中的global eventdataframe

load_data()：返回ploars.LazyFrame数据类型  从指定的table中加载数据

load_table(table_name)：返回pl.LazyFrame数据类型  加载一个表格中的数据并处理指定的链接

unique_patient_ids：List[str]  返回一个patiens的ID的列表

get_patient(patient_id)：返回Patient数据类型  根据id得到指定的patient的信息

iter_patients(df=None)：返回Iterator[Patient]数据类型  产生对于所有数据集中的patient的迭代指针

stats()：输出数据集的统计信息

default_task：返回BaseTask的数组

set_task(task=None,num_workers=1,cache_dir=None,cache_format='parquet')：返回SampleDataset数据类型   参数分别代表：设定一下这个数据集用于什么任务；用哪个CPU；缓存的处理过的样例的路径；缓存的格式（后缀）

### datasets.SampleDataset

一个具体的数据对象，有人如下参数：

samples：List[Dict]  表示数据采样的列表

input_schema：Dict[str,Union[str,Type[FeatureProcessor]]]  输入数据的范式

output_schema：输出数据的范式

dataset_name：str数组  数据集的名称

task_name：str数组  用于的任务名称

validate()：验证采样后的数据是不是符合输入输出范式 return None

build()：建立输入和输出的处理器，这个处理器能基于范式处理他们  return None

### datasets.MIMIC3Dataset

这是一个具体的数据集类，专门用来处理MIMIC-Ⅲ这个数据集的，pyhealth中还有很多这样的类，都是根据特定的数据集进行命名的类，用来处理各自对应的数据集，MIMIC3这个类继承了BaseDataset，有如下参数：

root：同Base

tables：同base

dataset_name：同base

config_path：str 数组  配置文件的名称

preprocess_noteevents(df)：df数据类型是ploars.LazyFrame  执行BaseDataset.load_table()这个方法的时候就会被调用  return进行时间处理过的dataframe

collected_global_event_df：收集并返回全局事件数据 return ploars.DataFrame

default_task：返回默认的训练任务

get_patient(patient_id)：根据id返回对应id的patient的数据

iter_patients(df=None)：依次产生每个patient的迭代指针

load_data()：从指定的表格中加载数据  return ploars.LazyFrame

load_table(table_name)：加载数据并进行连结操作   return ploars.LazyFrame

set_task：同base

stats()：同base

unique_patient_ids()：返回唯一的patient的id列表

![image-20250929105402237](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250929105402237.png)

## tasks

这个类用来定义要进行的训练任务，根据这个任务就知道如何处理每个patient的数据，把这些数据编程一个样本集供下游任务去学习，有各种任务，如：药物推荐；Readmission Prediction；死亡预测；存活时间预测；Sleep Staging等等，这个模块中有很多任务类，定义各种各样的任务，伴有各种方法。dataset中的哪些类，set_task这个参数用的就是这个模块下的这些东西

### tasks.base_task.BaseTask

定义任务的一个类，有如下参数：

task_name：str  任务的具体名字

input_schema：输入数据的格式

output_schema：输出数据的范式

pre_filter(df)：return LazyFrame

### tasks.readmission_30days_mimic4.Readmission30DaysMIMIC4

这个任务是为了预测patient未来30天复发入院的可能性，它的基类是BaseTask，参数和基类一样，实例如下

task：str='Readmission30DaysMIMIC4'

input_schema：Dict[str,str]={'conditions':'sequence','drugs':'sequence',procedures:'sequence'}

output_schema: [Dict](https://docs.python.org/3/library/typing.html#typing.Dict)[[str](https://docs.python.org/3/library/stdtypes.html#str), [str](https://docs.python.org/3/library/stdtypes.html#str)]= {'readmission': 'binary'}

pre_filter(df)：同base

### tasks.drug_recommendation_mimic3_fn

类似这种fn函数很多，这种类型的方法比较原始，从tasks模块中引入这种fn之后，通过数据集的set_task方法直接把这里作为参数传入，就能够去实现相应的训练任务。这种fn的命名规则都是任务名_数据集名称\_fn。他们都有如下参数：

patient：一个Patient类实体对象

time_window：时间滑动窗口阈值

函数最后返回的是一个样本的list，每个样本都有对应的信息，如patient_id等等

## models

这个模块里同时集成了很基础的深度学习模型（RNN，CNN等）和一些论文提出的比较出名的关心健康方面的深度学习模型（RETAIN等），这个模块为每个模型提供了两个可调用的方法：Model和ModelLayer，可以直接通过包来引入

Model下的参数：

dataset：数据类型为pyhealth.dataset  所有的特殊处理都是基于这个

feature_keys：一个table名的list，指示什么tables会用到这个pipeline里面

label_key：只支持定义在task function中的 label

mode：multiclass/multilabel/binary

如果不想用dataset对象来初始化模型，我们也可以用ModelLayer来调用我们想用的模型，ModelLayer都有自己各自的参数，像input_size、dropout什么的

![image-20250929140225006](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250929140225006.png)

## trainer

这个模块下面的Trainer（大小写注意）就是用来训练ML和DL的，有如下参数：

model：数据类型为pyhealth.models

checkpoint_path：检查点的路径

metrics：决定记录哪个评估指标

device：运行的位置

enable_logging：是否记录日志

output_path：输出保存的路径

exp_name：实验或是任务的名字

调用Trainer中的train方法，就可以进行训练，train方法中有如下参数：

train_dataloader：训练数据的加载器

val_dataloader：验证数据的加载器

epochs：训练轮次

optimizer_class：优化器，如torch.optim.Adam

optimizer_params：优化器的参数，有lr（学习率）和weight_decay（权重衰减）

max_grad_norm：最大梯度范数

monitor：要监控的评估指标的名称，默认None

monitor_criterion：要监控的标准，默认max

load_best_model_at_last：是否要加载最好的模型在最后一次迭代中

还有其他方法：

inference(dataloader,additional_outputs=None,return_patient_ids=False)：用于模型推理

evaluate(dataloader)：评估函数

save_ckpt(ckpt_path)：保存模型训练参数的路径

load_ckpt(ckpt_path)：加载已保存的模型参数

模型从构建到训练完整过程：

```python
from pyhealth.tasks import readmission_prediction_mimic3_fn
from pyhealth.datasets import split_by_patient, get_dataloader

# set task
dataset = mimic3dataset.set_task(task_fn=readmission_prediction_mimic3_fn)

# dataset split
train_ds, val_ds, test_ds = split_by_patient(dataset, [0.8, 0.1, 0.1])

# obtain train/val/test dataloader, they are <torch.data.DataLoader> object
train_loader = get_dataloader(train_ds, batch_size=32, shuffle=True)
val_loader = get_dataloader(val_ds, batch_size=32, shuffle=False)
test_loader = get_dataloader(test_ds, batch_size=32, shuffle=False)
# use RETAIN model
from pyhealth.models import RETAIN

model = RETAIN(
    dataset=dataset,
    # look up what are available for "feature_keys" and "label_keys" in dataset.samples[0]
    feature_keys=["conditions", "procedures"],
    label_key="label",
    mode="binary",
)
from pyhealth.trainer import Trainer

trainer = Trainer(model=model)
trainer.train(
    train_dataloader=train_loader,
    val_dataloader=val_loader,
    epochs=50,
    monitor="roc_auc",
)
```

## metrics

这个模块下提供了multiclass、multilabel、binary（即多分类、多标签、二分类）这三个子模块，各个子模块下面有自己的设置评估指标的方法，命名方式类似于  分类方式_metrics_fn()，下面以multiclass来举例

pyhealth.metrics.multiclass.multiclass_metrics_fn(y_true,y_prob,metrics=None,y_predset=None)：y_true表示实际的标签，形状是n_samples\*1；y_prob表示预测的每个类的概率值，形状是n_samples\*n_class；metrics表示一个str的list，内容是想要得到的评估指标的值，比如roc_auc_macro_ovo、accuracy这种

```python
>>> from pyhealth.metrics import multiclass_metrics_fn
>>> y_true = np.array([0, 1, 2, 2])
>>> y_prob = np.array([[0.9,  0.05, 0.05],
...                    [0.05, 0.9,  0.05],
...                    [0.05, 0.05, 0.9],
...                    [0.6,  0.2,  0.2]])
>>> multiclass_metrics_fn(y_true, y_prob, metrics=["accuracy"])
{'accuracy': 0.75}
```

## tokenizer

这个模块把str根据词汇表转成数据表示形式，这个模块中提供了一维、二维、三维的转变

模块中还包含两个子类：

### tokenizer.Vocabulary

这个类用于做token和index之间的映射。初始化时有两个参数：tokens，special_tokens=None。分别表示要转变的token和要加进去的特殊的token

还有一个方法：add_token(token)：把额外的token再加进去

### tokenizer.Tokenizer

做token和index之间的转变，这个类会根据初始化时输入的tokens形成一个vocabulary并提供方法来做真正的从token到index的转变，也提供方法做一批的数据的编码，具体方法如下：

get_padding_index()：得到token的index

get_vocabulary_size()：得到vocabulary的size

conver_tokens_to_indices(tokens)：把一个tokens的list变成indices

conver_indices_to_tokens(indices)：把一个indices的list变成tokens

batch_encode_2d(batch,padding=True,truncation=True,max_length=512)：参数分别为[list[list[str]]]，就是一个二维；填充，是否要填充tokens到最长长度；是否要要把过长的内容裁剪到最大长度；tokens的最大长度

batch_decode_2d(batch,padding=False)：从indices转回tokens

batch_encode_3d(batch,padding=(True,True),truncation=(True,True),max_length=(10,512))：batch就是list[list[list[str]]]，padding变成了两个逻辑变量，后者用来决定是否填充到最大长度，前者用来决定是否要填充到批次中的最大样本数量；truncation和padding一样；max_length的两个值前者是批次中最大样本数量，后者是一个样本中最长的tokens数量

batch_decode_3d：和2d的一样

## medcode

医疗领域的编码方式，pyhealth中集成了很多，比如ICD9CM、ICD10CM、CCSCM这种，这个模块下的子模块有：InnerMap、CrossMap以及各种公开的诊断编码方式

### medcode.InnerMap

这个类用来做在一个编码方式中，数据和病症之间的转换，初始化时有vocabulary和refresh_cache两个参数：前者是个str，指明具体的编码方式即可，如ICD9CM，后者就是是否要更新缓存，这个类中还有很多方法，这个类被所有具体的编码系统对应的类所继承：

load(vocabulary,refresh_cache=False)：这个就是它的初始化函数

available_attributes：返回一个list[str]数据对象，指示都有哪些可用属性

stat()：打印编码系统的统计信息

静态方法  standardize(code)：标准化一个给定的编码，由子类来重写

静态方法  convert(code,**kwargs)：转换一个给定的编码，由子类来重写

lookup(code,attribute='name')：查看一下这个code/index，看看它对应的病症是什么

get_ancestors(code)：找到这个编码的祖先

get_descendants(code)：找到这个编码的后代

### medcode.CrossMap

这个类用来做不同编码系统之间的转换，有如下方法：

load(source_vocabulary,target_vocabulary,refresh_cache=False)：和innermap一样，这个函数用来构造crossmap，参数分别表示：原始的编码系统；想要变成的编码系统；是否更新缓存

map(source_code,source_kwargs=None,target_kwargs=None)：把原始的编码变成目标编码的实操

## 实验一

```python
#Step1 创建数据对象
from pyhealth.datasets import MIMIC3Dataset

mimic3_ds = MIMIC3Dataset(
#第一个参数表示这个数据的原始地址，第二个参数表示要拿这个数据的哪些表格，第三个参数表示要把原始数据的编码方式NDC改成ATC-3这种编码方式
    root="https://storage.googleapis.com/pyhealth/Synthetic_MIMIC-III/",
 tables=["DIAGNOSES_ICD", "PROCEDURES_ICD", "PRESCRIPTIONS"],
 code_mapping={"NDC": ("ATC", {"target_kwargs": {"level": 3}})},
        dev=True
)

mimic3_ds.stat()#展示数据对象的统计信息
mimic3_ds.info()#展示数据对象的具体信息

#可以通过下面的命令检查是否正确获得数据
# mimic3_ds.patients
# mimic3_ds.patients['947']
# mimic3_ds.patients['947'].visits
# mimic3_ds.patients['947'].visits['100999']
# mimic3_ds.patients['947'].visits['100999'].get_code_list('DIAGNOSES_ICD')

#Step2 定义任务并划分数据集
from pyhealth.tasks import drug_recommendation_mimic3_fn

mimic3_ds = mimic3_ds.set_task(task_fn=drug_recommendation_mimic3_fn)#这个参数就是pyhealth中的任务函数，目前看来这个任务函数就是专门用来做这里的参数的
# stats info
mimic3_ds.stat()

from pyhealth.datasets.splitter import split_by_patient
from pyhealth.datasets import split_by_patient, get_dataloader

# data split
train_dataset, val_dataset, test_dataset = split_by_patient(mimic3_ds, [0.8, 0.1, 0.1])

# create dataloaders (they are <torch.data.DataLoader> object)
train_loader = get_dataloader(train_dataset, batch_size=64, shuffle=True)
val_loader = get_dataloader(val_dataset, batch_size=64, shuffle=False)
test_loader = get_dataloader(test_dataset, batch_size=64, shuffle=False)

#Step3 定义模型
from pyhealth.models import Transformer
#参数分别是：数据集；要看的表格的名称；标签的名称；任务类型是多标签
model = Transformer(
    dataset=mimic3_ds,
    feature_keys=["conditions", "procedures"],
    label_key="drugs",
    mode="multilabel",
)

#Step4 模型训练
from pyhealth.trainer import Trainer

trainer = Trainer(model=model)
trainer.train(
    train_dataloader=train_loader,
    val_dataloader=val_loader,
    epochs=3,
    monitor="pr_auc_samples",#一个要监控的评估指标
)

#Step5 模型评估
# option 1: use our built-in evaluation metric
score = trainer.evaluate(test_loader)
print (score)

# option 2: use our pyhealth.metrics to evaluate
from pyhealth.metrics.multilabel import multilabel_metrics_fn
y_true, y_prob, loss = trainer.inference(test_loader)
multilabel_metrics_fn(y_true, y_prob, metrics=["pr_auc_samples"])
```

