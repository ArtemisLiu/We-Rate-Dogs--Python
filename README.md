# Project-data-wrangling
清洗 WeRateDogs 推特数据

项目说明
================
在这一项目中，运用python去清洗 WeRateDogs 推特数据[@dog_rates](https://twitter.com/dog_rates)，创建有趣可靠的分析和可视化。

背景
--------
你的目标：清洗 WeRateDogs 推特数据，创建有趣可靠的分析和可视化。推特档案很大，但是只包括基本的推特信息。对 "Wow!" 进行收集、评估和清洗，是分析和可视化应该做的。

关于数据
--------
#### 完善推特档案

WeRateDogs 推特档案包括基本的推特信息，如 5000 多条推特，但并不包括所有数据。不过档案中有一列包括每个推特文本，我可以用来提取评级、狗的名字和 "地位" (即 doggo、floofer、pupper 和 puppo)。

Twitter API 的附加数据

回到推特档案的基础信息：转发用户和喜爱用户是两个遗漏的列。幸运的是，从推特 API 中，任何人都可以收集到附加数据。其实，"任何人" 都能获取至少最近的 3000 条推特数据。但是因为你拥有 WeRateDogs 推特档案和专门的推特 ID，你可以收集到所有的 5000 多条推特。你将会查询推特的 API 收集这个重要数据

#### 图像预测文件

一件更酷的事情：我通过一个[神经网络](https://www.youtube.com/watch?v=2-Ol7ZB0MmU) 运行 WeRateDogs 推特档案中的所有图片，这个神经网络可以对狗的品种分类。结果：对图片预测 (只含前三名) 的表格包括每个推特 ID、图片 URL 和最自信预测对应的图片编号 (由于推特最多包含 4 个图片，所以编号为 1 到 4)。
其中有：
* p1 是对推特中图片算法 #1 的预测 
* p1_conf 是 #1 预测中算法的可信度
* p1_dog 是 #1 预测是否是狗的品种
* p2 是算法的第二个最有可能的预测
* p2_conf 是 #2 预测中算法的可信度
* p2_dog 是 #2 预测是否是狗的品种


#### 关键要点
清洗这个项目的数据时要牢记几个要点：

我们只需要含有图片的原始评级 (不包括转发)。
充分评估和清洗整个数据集需要巨大努力，所以只有一些问题 (至少 8 个质量问题和 2 个清洁度问题) 的子集需要进行评估和清洗。
根据 清洗数据 的规则，清洗包括合并数据的独立内容。
如果分子评级超过分母评级，不需要进行清洗。这个 特殊评级系统 是 WeRateDogs 人气度较高的主要原因。

任务说明
--------
#### 收集项目数据

收集下面描述的三份数据，在 Jupyter Notebook 命名为 wrangle_act.ipynb ：

* WeRateDogs 推特档案。这个数据都可以从[Github repo](https://github.com/udacity/new-dand-advanced-china/tree/master/%E6%95%B0%E6%8D%AE%E6%B8%85%E6%B4%97/WeRateDogs%E9%A1%B9%E7%9B%AE)中下载到。
* 推特图片预测，即根据神经网络，出现在每个推特中狗的品种 (或其他物体、动物等)。这个文件目前也在Github repo中，你需要使用Python 的 Requests 和对应的URL (https://raw.githubusercontent.com/udacity/new-dand-advanced-china/master/%E6%95%B0%E6%8D%AE%E6%B8%85%E6%B4%97/WeRateDogs%E9%A1%B9%E7%9B%AE/image-predictions.tsv) 来进行编程下载。
* 每条推特的数据，至少要包含转发数（retweet_count）和喜欢数（favorite_count），以及任何你觉得有趣的额外数据。在 WeRateDog 推特档案中的推特 ID 中，使用 Python Tweepy 库查询 API 中每个 JSON 数据，把每个推特的 JSON 数据的完整集合存储到一个名为 tweet_json.txt 的文件中。每个推特的 JSON 数据应当写入单独一行。然后将这个 .txt 文件逐行读入一个 pandas DataFrame 中，（至少）包含 tweet ID、retweet_count 和 favorite_count 字段。注释：不要包含你项目提交的推特 API 密钥和访问令牌。

#### 评估项目数据

收集上述数据的每个内容后，从视觉上和程序上，对质量和清洁度进行数据评估。在你的 wrangle_act.ipynb Jupyter Notebook 中查找和记录至少 8 个质量问题 和 2 个清洁度问题。为了符合规范，必须评估符合项目动机的问题 (参见上一页的 关键要点 标题)。

#### 清洗项目数据

评估时清洗你记录的每个问题。在 wrangle_act.ipynb 完成清洗。结果应该为优质干净的主要 pandas DataFrame (如有，或为多个 DataFrame)。必须评估符合项目动机的问题。

#### 存储、分析和可视化项目数据

在 CSV 文件中存储洁净的数据，命名为 twitter_archive_master.csv。如果因为清洁需要多个表格，存在附加文件，要给这些文件合理命名。另外，你可以把清洗后的数据存储在 SQLite 数据库中 (如有需要也可以提交)。

在 wrangle_act.ipynb Jupyter Notebook 中对清洗后的数据进行分析和可视化。必须生成至少 3 个见解和 1 个可视化。

#### 项目汇报

创建一个 300-600 字书面报告 命名为 wrangle_report.pdf，可以简要描述你的清洗过程。这可以作为内部文档。
创建一个 250 字以上的书面报告 命名为 act_report.pdf，可以沟通观点，展示你清洗过数据后生成的可视化内容。这可作为外部文档，如博客帖子或杂志文章。
