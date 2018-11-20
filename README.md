# WeRateDogs-data-wrangling
# 清洗 WeRateDogs 推特数据

背景
----------------
​    在本项目中要整理 (以及分析和可视化) 的数据集是推特用户 [@dog_rates](https://twitter.com/dog_rates) 的档案, 推特昵称为 [WeRateDogs](https://en.wikipedia.org/wiki/WeRateDogs)。WeRateDogs 是一个推特主，他以诙谐幽默的方式对人们的宠物狗评分。这些评分通常以10作为分母。但是分子则一般大于 10：11/10、12/10、13/10 等等。为什么会有这样的评分？因为 "[They're good dogs Brent.](http://knowyourmeme.com/memes/theyre-good-dogs-brent)" 。WeRateDogs 拥有四百多万关注者，曾受到国际媒体的报道。

目的
--------
清洗 WeRateDogs 推特数据，并创建有趣可靠的分析和可视化。

项目数据的收集、评估和清洗
--------
### 一、收集数据

收集下面描述的三份数据：

#### 1.WeRateDogs 推特档案

本项目提供了csv文件twitter-archive-enhanced，将其直接读取到Jupyter Notebook，并命名为twitter。

#### 2.图像预测文件

​    通过一个可以对狗狗种类进行分类的[神经网络](https://www.youtube.com/watch?v=2-Ol7ZB0MmU)，运行这份推特档案中的所有图像，对出现在每个推特中狗的品种（或其他物体、动物等）进行预测的结果。获取的结果为一份图像预测结果表格，其中包含了预测结果的前三名，推特 ID，图像 url 以及最可信的预测结果对应的图像编号（由于推特最多包含 4 个图片，所以编号为 1 到 4）。

   使用 Python 的 Requests 库和提供的 URL 来进行编程下载，然后读取到Jupyter Notebook，命名为image_predictions。下载用的 URL：<https://raw.githubusercontent.com/udacity/new-dand-advanced-china/master/%E6%95%B0%E6%8D%AE%E6%B8%85%E6%B4%97/WeRateDogs%E9%A1%B9%E7%9B%AE/image-predictions.tsv> 。

#### 3.通过推特 API 获取附加数据

​    回到基础的推特档案：转发数（retweet count）和喜爱数（favorite count）是两个遗漏的列。幸运的是，从推特 API 中，任何人都可以收集到这些数据。其实，"任何人" 只是能获取最多 3000 条的最近推特数据。但是因为本项目拥有 WeRateDogs 推特档案和其中的推特 ID，因此可以收集到这其中所有的 5000 多条推特。我将查询推特 API 来收集这些有价值的数据。

​    使用 WeRateDogs 推特档案中的推特 ID，使用 Python [Tweepy 库](http://www.tweepy.org/)查询 API 中每个推特的 JSON 数据，把所有 JSON 数据存储到一个名为 `tweet_json.txt` 的文件中。每个推特的 JSON 数据写入单独一行，将这个 .txt 文件逐行读入一个 pandas DataFrame 中，然后获取数据tweet_id，retweet_count，favorite_count，并命名为tweet_extra。

### 二、数据评估

通过目测评估和编程评估发现：

#### 1.质量问题

##### twitter 表格：

- 存在转发的推特需要删除；
- in_reply_to_status_id、in_reply_to_user_id、retweeted_status_id、retweeted_status_user_id、retweeted_status_timestamp类型不对；

- in_reply_to_status_id、in_reply_to_user_id、expanded_urls信息缺省；
- timestamp类型错误；
- name有的条目提取错误，且有缺省值；
- doggo、floofer、pupper、puppo有缺省值,有的条目并列存在；
- rating_denominator有的不是10；
- rating_numerator存在异常值；

- 无图片的twitter需要删除。

##### image_predictions表格：

- p1,p2,p3中狗名字格式不一致，首字母要大写。

##### tweet_extra表格：

- 缺少记录，暂时无法修改

#### 2.清洁度问题

- twitter 表格中的doggo、floofer、pupper、puppo应该用狗的地位stage来表示；
- tweet_extra 表格中的favorite_count和retweet_count加入twitter表格中；
- image_predictions 表也应该合并到 twitter 表格中，并且 twitter 表格去掉没有image_predictions的部分。

### 三、数据清洗

数据清洗首先处理缺失数据，然后清理整洁度问题，最后清理质量问题。

首先将三份原始的数据copy，保存原始数据的副本。

#### 1.处理缺失数据 

- twitter：name有的条目提取错误，且有缺省值。

处理方法：用str.extract()和正则表达式从text中重新提取name。

#### 2.清洁度问题 

- l  twitter：doggo、floofer、pupper、puppo应该用狗的地位stage来表示，doggo、floofer、pupper、puppo有缺省值，有的条目并列存在。

处理方法：doggo、floofer、pupper、puppo应该用狗的地位stage来表示，删除这四列，并用str.findall函数和正则表达式重新提取“stage”信息，再用apply函数针对stage这一列做出修改，将未提取出来的修改为 NaN，有多重地位的将这些地位信息用逗号连接。

- twitter 表格中应该去掉retweeted_status_id不为空的行。

处理方法：我们分析的是原始数据，即不包含转发的数据，因此在twitter的retweeted_status_id不为空（转发条目）的都要删除。

- tweet_extra 表格中的favorite_count和retweet_count加入twitte表格中。

处理方法：用merge函数将tweet_extra_clean表中的favorite_count和retweet_count加入twitter_clean表格。

- image_predictions 表也应该合并到 twitter 表格中，去掉twitter 表格中没有image_predictions的部分。

处理方法：用merge函数将image_predictions_clean合并到twitter_clean表格，how为inner。

#### 3.质量问题 

- in_reply_to_status_id、in_reply_to_user_id、retweeted_status_id、retweeted_status_user_id、retweeted_status_timestamp类型不对。

- in_reply_to_status_id、in_reply_to_user_id、expanded_urls信息缺省

处理方法：考虑到在分析数据时不会用到这几项，此处用drop函数直接删除。

- timestamp类型错误

处理方法：使用pd.to_datetime将timestamp转为datetime数据类型。

- rating_denominator有的不是10

- rating_numerator存在异常值

处理方法：使用正则表达式和extract函数重新提取两项评分，再次检查异常值，进一步分析和处理。无法确定原始值的，虑用众数替换。

- p1,p2,p3中狗的名字格式不一致，首字母要大写

处理方法：使用title函数，将p1,p2,p3中名字的每个字的首字母大写。

#### 4.保存数据 

将清洗完成后的数据保存在twitter_archive_master.csv。

数据的分析、可视化与结论
--------
#### 1. favorite_count与retweet_count比较

- 分析favorite_count和retweet_count。

![分析favorite_count和retweet_count](https://github.com/ArtemisLiu/We-Rate-Dogs--Python/blob/master/结论图表/分析favorite_count和retweet_count.png)

结论：分析favorite_count和retweet_count可知favorite_count的平均值为8923，retweet_count的平均值为2770，可知相较于评论，人们更喜欢点赞。

- 可视化favorite_count和retweet_count随时间的变化。

将时间分组，可视化favorite_count和retweet_count随时间的变化。

![可视化favorite_count和retweet_count随时间的变化](https://github.com/ArtemisLiu/We-Rate-Dogs--Python/blob/master/结论图表/可视化favorite_count和retweet_count随时间的变化.png)

结论：根据favorite_count和retweet_count随时间的变化的趋势图，可知随着时间的变化，人们点赞和评论都有一定的波动，点赞的波动大于评论的波动，总体来说，随着时间的变化，点赞数高于评论数。

#### 2. 图像预测算法的可信度比较

- 分析p1_conf，p2_conf和p3_conf。

![分析p1_conf，p2_conf和p3_conf](https://github.com/ArtemisLiu/We-Rate-Dogs--Python/blob/master/结论图表/分析p1_conf，p2_conf和p3_conf.png)

- p1_conf直方图：

![p1_conf直方图](https://github.com/ArtemisLiu/We-Rate-Dogs--Python/blob/master/结论图表/p1_conf直方图.png)

- P2_conf直方图：

![p2_conf直方图](https://github.com/ArtemisLiu/We-Rate-Dogs--Python/blob/master/结论图表/p2_conf直方图.png)

- P3_conf直方图：

![p3_conf直方图](https://github.com/ArtemisLiu/We-Rate-Dogs--Python/blob/master/结论图表/p3_conf直方图.png)

结论：分析p1_conf，p2_conf，p3_conf可知1号预测的可信度 > 2号预测的可信度 > 3号预测的可信度，2号预测和3号预测的可信度均小于0.5，1号预测可信度的均值为0.59，可信度相对来说还是不高，因此图形预测算法还有很大的提升空间。

#### 3. 狗狗的评价地位分析

- 可视化狗狗评价地位，按出现的次数顺序绘制柱状图。

![狗狗的评价地位分析](https://github.com/ArtemisLiu/We-Rate-Dogs--Python/blob/master/结论图表/狗狗的评价地位分析.png)

结论：由上图可知狗狗的评价地位使用最多的是pupper,其次是doggo，再次是puppo，查阅狗狗字典可知，大多数参与评价的狗狗年纪较轻。