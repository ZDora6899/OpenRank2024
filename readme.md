这是openrank杯2024比赛，This is AI的仓库。

我们目前主要针对数据集进行处理

这是一个简单的数据集可视化工具，使用的数据集是Seaborn自带的tips，后面可以直接更换：
# 数据分析与可视化（pandas、matplotlib、seaborn、plotly、bokeh）
## 环境要求
- Python 3.x
- pandas
- seaborn
- matplotlib
- plotly
- bokeh
- Jupyter Notebook（可选，但推荐用于交互式分析）
## 代码示例
```python
import pandas as pd
import seaborn as sns
import matplotlib.pyplot as plt
from bokeh.io import output_notebook, show
from bokeh.plotting import figure
import plotly.express as px

# 在Jupyter中启用内联显示
%matplotlib inline
output_notebook()

# 1. 数据加载与初步查看（使用seaborn自带tips示例数据）
tips = sns.load_dataset("tips")
print("数据集前五行：")
display(tips.head())

print("\n数据基本信息：")
display(tips.info())

# 基本统计描述
print("\n数据描述统计：")
display(tips.describe())

# 2. 数据清洗与特征处理
categorical_cols = ['sex', 'smoker', 'day', 'time']
for col in categorical_cols:
    tips[col] = tips[col].astype('category')

# 新增特征列：tip_rate = tip/total_bill（小费率）
tips['tip_rate'] = tips['tip'] / tips['total_bill']

# 3. 使用matplotlib进行基础可视化 - 账单金额分布直方图
plt.figure(figsize=(8, 6))
plt.hist(tips['total_bill'], bins=20, color='skyblue', edgecolor='black')
plt.title('Distribution of Total Bill')
plt.xlabel('Total Bill')
plt.ylabel('Frequency')
plt.grid(True, alpha=0.3)
plt.show()

# 4. 使用seaborn进行可视化

# 4.1 箱线图：不同日期下的消费金额分布
plt.figure(figsize=(8,6))
sns.boxplot(x='day', y='total_bill', data=tips, palette='Set3')
plt.title('Total Bill Distribution Across Days')
plt.show()

# 4.2 散点图 + 回归线：小费率与账单金额的关系
plt.figure(figsize=(8,6))
sns.regplot(x='total_bill', y='tip_rate', data=tips, scatter_kws={'alpha':0.5}, line_kws={'color':'red'})
plt.title('Tip Rate vs Total Bill')
plt.show()

# 4.3 热力图：变量间相关性
corr = tips[['total_bill','tip','tip_rate','size']].corr()
plt.figure(figsize=(6,5))
sns.heatmap(corr, annot=True, cmap='coolwarm', vmin=-1, vmax=1)
plt.title('Correlation Heatmap')
plt.show()

# 5. 使用Plotly实现交互式可视化
fig = px.scatter(
    tips, 
    x='total_bill', y='tip', 
    color='smoker', 
    symbol='sex',
    size='size', 
    title='Interactive Scatter Plot (Plotly): Total Bill vs Tip',
    hover_data=['day','time']
)
fig.show()

# 6. 使用Bokeh进行交互式可视化
p = figure(
    title="Bokeh Interactive Plot: Tip Rate vs Total Bill",
    x_axis_label='Total Bill',
    y_axis_label='Tip Rate',
    plot_width=600,
    plot_height=400,
    tools="pan,wheel_zoom,box_zoom,reset,hover"
)
p.circle(
    tips['total_bill'], 
    tips['tip_rate'], 
    size=7, 
    color="navy", 
    alpha=0.5
)
show(p)


除了以上自己的尝试，还可以直接使用当前比较流行的分析工具：
**引用网络构建与分析工具**
1.网络分析
NetworkX (Python)：对学术引用网络进行建模和分析，提取节点（论文）、边（引用关系）、作者节点等。
igraph (Python/R)：高效处理大规模图结构，并进行社区检测、路径长度统计、模块度计算等高级分析。
2.大规模数据存储与处理（可选）
Elasticsearch / Solr：如果需要对大量文献数据进行全文搜索或索引，以快速查找引用关系或文本特征。
Neo4j：图数据库，对引用网络进行存储和查询（Cypher查询语言可方便地发现模式和关系）。
**时间序列与长期使用模式分析工具**
1。时间序列分析
Prophet (Facebook开源)：预测未来的引用趋势或使用模式变化趋势。
2.统计分析与可视化
SciPy / statsmodels (Python)：进行基础统计检验、回归分析和显著性分析。

**进一步的分析思路**
可以做以下尝试：
1.长期使用模式评估指标设计：
除了简单的年度引用次数外，还可设计更丰富的评价指标：
a.引用半衰期：数据集论文在发布后多久引用率开始下降，从而量化其学术“寿命”。
b.引用增长率：分析引用次数随时间的增速或减速情况，以识别该数据集是否在某些年份开始重新获得关注（例如随着领域热点变化）。
c.研究主题演化：通过提取引用论文的关键词或研究领域分类，分析这些引用论文所在的领域有没有从原始应用领域扩散到其他领域。
2结合其他数据源与行为数据：
虽然原始代码仓库行为数据缺失，但可考虑：
a.利用搜索引擎或数据集下载量信息（如从DataHub、Zenodo等数据托管平台的API）补充数据集使用的间接证据。
b.使用GitHub API查看其他项目中对该数据集的二次引用（如README中提到的数据集）。
3对比分析：
将目标数据集对应论文的引用模式与其他同类数据集的引用模式进行对比，寻找影响长期使用的共性特征（如数据集领域、数据类型、社区支持力度）。


**案例构思**
构思了以下案例：
1.多维度指标整合：
除了引用次数趋势，还可结合作者地理位置（如果数据可得）或主题领域标签，对引用网络分层分析，利用分组统计与可视化（如Seaborn的分面图）揭示不同领域对数据集的采用程度。
2.热点演化分析：
借助时间序列分析工具和Plotly的交互式可视化，将引用网络按年份分层，对比初期和近期引用者群体的研究方向变化，用动态可视化（如动画的时间步进）展现该数据集在学术圈的“足迹”。
3.对比分析与参考基线：
使用同样的工具与流程，对其它已知在学术界有不同使用模式的数据集进行类似分析，对比年度引用趋势和网络拓扑特征，提炼出可能影响长期使用的关键因素（如论文质量、数据集类型、后续维护水平）。

**深度分析思路**
1.定性与定量相结合的分析路径：
可考虑将量化的指标与定性的观察相结合：
a.定性观察：对某些关键年份中引用量突增的节点（论文）和团体（作者群体）进行定性分析，手动查看该年度引用的论文内容（如摘要、标题或关键词）以判断增长的原因（领域新热点、相关工具链成熟等）。
b.定量指标：对引用时间序列的结构（如突变点、平台期、下滑期）进行统计检测（如Change Point Detection），进一步量化数据集影响力的生命周期特征。
2.多维特征整合分析：
获得更丰富的元数据（除了引用时间、引用论文标题、作者等，还包括论文关键词、领域分类、作者所属机构和地理分布），则可以进行多维度整合分析：
a.地理与机构分布分析：利用作者的机构信息（如果从Semantic Scholar或其他数据源中获取）统计每年不同区域、不同机构对该数据集引用的增长或下降情况。可通过地理热力图（Plotly或Folium库）展示全球学术共同体对数据集应用的分布与演化。
b.领域拓扑分析：从引用论文的关键词或领域标签中抽取特征（如NLP、CV、Robotics等学科标签），统计该数据集最初在特定领域中的引用情况，并观察其是否在随时间扩散到新的研究领域。可利用Seaborn的facetgrid()或Altair的交互式图表分面化展示领域的演化。
3.文本挖掘与语义分析：
a.当获取引用论文的摘要或关键词后，可以利用自然语言处理（NLP）和文本挖掘技术获得数据集使用模式的语义线索：
b.关键词提取与聚类：利用NLTK、spaCy或scikit-learn中的TF-IDF、LDA（Latent Dirichlet Allocation）主题模型，对引用论文摘要和标题进行主题建模。
c.分析不同年份出现的主要主题，通过主题的兴衰更好地理解数据集在学术研究上的功能迁移（例如从初期在图像分割任务上的引用延展到后期在医学影像分析领域的使用）。
4.情感与立场分析（可能无明显价值，但可探索）：检查论文中引用该数据集时是否对其有一定评价（如性能对比、数据集质量评价）。尽管学术论文普遍中性，但也有可能在相关工作中对数据集的局限性或优势有简述。

现在对方法论框架做进一步补充说明：
数据抓取与预处理：数据抓取是整个分析过程的基础。将通过 Paperswithcode 和 Semantic Scholar API 获取数据集对应的学术论文及其引用信息。数据预处理将帮助清洗和整合这些信息，为后续的分析和建模做准备。
关键步骤：
1.API数据抓取：使用requests或asyncio抓取论文数据。抓取内容包括论文标题、DOI、引用信息、关键词、出版年份等。
2.数据清洗：利用pandas处理数据缺失、重复项、异常值等，确保数据整洁一致。
3.时间字段转换：引用数据按时间顺序分析时，需要确保年份等时间字段的格式一致。

现在对引用网络进行构建与分析：引用网络是分析数据集使用模式的核心。每篇学术论文都与其他论文通过引用关系相连，通过这些引用关系，可以构建一个引用网络。
关键步骤：
1.构建引用图：利用NetworkX或igraph库，将引用数据构建成有向图。每个节点表示一篇论文，边表示引用关系。
2.社区检测：通过Louvain或Girvan-Newman等算法，识别引用网络中的学术社群或研究领域。分析哪些领域或学者群体推动了数据集的使用。
3.中心性分析：通过度中心性、PageRank等指标，找出引用网络中的核心论文和关键作者。了解数据集的学术影响力是如何通过这些核心节点扩展到其他领域的。

**现在对应用场景与实际意义进行展开论述**
帮助研究人员选择数据集：
研究人员在选择开源数据集时，常常面临选择困难，尤其是面对已经多年未更新的数据集时。通过分析学术引用网络与使用模式，研究者可以获得基于数据的选择依据，从而更科学地选择适合自己研究领域的数据集。

补充两点具体意义：
1.推动开源数据集管理与评估
研究结果将为开源数据集的管理提供支持，尤其是在缺乏传统更新和行为数据的情况下，引用数据可以作为衡量数据集活跃度和影响力的一个有效指标。这可以帮助开源平台、学术机构等制定更好的数据集发布与管理策略。
2.影响政策与产业应用
对于工业界的应用者，了解数据集在学术界的长期影响力，能够帮助判断一个数据集是否具有长期使用的价值，并为未来的技术开发提供数据支持。

关于如何量化数据集的活跃度与影响力的思考：
**引用数据作为关键指标：**
1.引用次数：数据集对应的学术论文被引用的总次数可以直接反映其在学术界的影响力。引用次数越多，表明数据集被更多研究者使用和认可。
2.引用动态：分析引用数量随时间的变化趋势，识别数据集的生命周期阶段（发布、初期使用、持续使用、停用/过时）。例如，引用量的持续增长表明数据集在长期使用中保持活跃，而引用量的下降可能预示其逐渐被新数据集取代。

关于开发标准化的评价体系的思考：
**构建多维度评价指标：**
1.引用数量与增长率：不仅关注总引用次数，还关注引用增长率，以评估数据集的持续影响力。
2.跨领域引用：衡量数据集在多个学科领域中的引用情况，反映其跨领域应用的广泛性。
3.核心引用网络：分析引用网络中的核心节点（如高引用论文），评估数据集在学术网络中的关键地位。
**标准化流程：**
1.数据收集：通过Paperswithcode和Semantic Scholar API收集数据集对应的引用数据。
2.指标计算：根据预定义的评价指标计算每个数据集的评分。
3.评分体系：建立一个评分体系，将不同指标进行权重组合，生成综合评分，便于不同数据集之间的比较。

那么如何构建多维度评价指标？
一个全面的评价体系应涵盖多个维度，以全面反映数据集的使用情况和影响力。以下是三个关键的评价指标：
a.引用数量与增长率
b.跨领域引用
c.核心引用网络
引用数量与增长率
**定义与重要性：**
引用数量：指数据集相关论文被引用的总次数，是衡量数据集学术影响力的基本指标。
引用增长率：指在特定时间段内引用数量的增长速度，反映数据集的持续影响力和未来潜力。
代码示例：
import pandas as pd
import matplotlib.pyplot as plt
# 假设 df_citations 包含 'year' 和 'citation_count' 列
df_citations = pd.DataFrame({
    'year': range(2010, 2024),
    'citation_count': [50, 60, 70, 80, 90, 100, 110, 120, 130, 140, 150, 160, 170, 180]
})
# 计算总引用次数
total_citations = df_citations['citation_count'].sum()
# 计算年度增长率
df_citations['growth_rate'] = df_citations['citation_count'].pct_change() * 100
# 计算平均增长率（复合年增长率）
cagr = ((df_citations['citation_count'].iloc[-1] / df_citations['citation_count'].iloc[0]) ** (1/(df_citations.shape[0]-1)) - 1) * 100
print(f"总引用次数: {total_citations}")
print(f"平均增长率 (CAGR): {cagr:.2f}%")
# 可视化引用趋势
plt.figure(figsize=(10, 6))
plt.plot(df_citations['year'], df_citations['citation_count'], marker='o', label='引用次数')
plt.title('年度引用次数变化趋势')
plt.xlabel('年份')
plt.ylabel('引用次数')
plt.legend()
plt.grid(True)
plt.show()

如何进行跨领域引用？
1.定义与重要性：
跨领域引用：指数据集相关论文在不同学科领域的引用情况，反映数据集的跨学科应用广泛性。
重要性：跨领域引用量高的数据集具有更广泛的适用性和更高的学术价值，能够推动多学科的研究进展。
2.计算方法：
领域分类：
自动分类：使用NLP技术和机器学习算法对引用论文进行自动领域分类。
手动标注：对自动分类结果进行人工验证和修正，确保分类的准确性。
跨领域引用量（Cross-domain Citations）：
![image](https://github.com/user-attachments/assets/fe1b9835-7498-493e-bcef-e150d0b410af)
代码示例如下：
import pandas as pd
from sklearn.feature_extraction.text import TfidfVectorizer
from sklearn.naive_bayes import MultinomialNB
from sklearn.pipeline import make_pipeline
import matplotlib.pyplot as plt
import seaborn as sns
# 假设 df_papers 包含 'title', 'abstract', 'keywords', 'field' 列
df_papers = pd.DataFrame({
    'title': ['Paper A', 'Paper B', 'Paper C', 'Paper D'],
    'abstract': ['Abstract A', 'Abstract B', 'Abstract C', 'Abstract D'],
    'keywords': ['Keyword1, Keyword2', 'Keyword3, Keyword4', 'Keyword1, Keyword3', 'Keyword2, Keyword4'],
    'field': ['Computer Vision', 'Natural Language Processing', 'Biomedical', 'Robotics']
})
# 自动领域分类（示例使用预先标注的训练数据）
# 在实际应用中，需要使用更多的训练数据和更复杂的模型
vectorizer = TfidfVectorizer(stop_words='english')
clf = MultinomialNB()
model = make_pipeline(vectorizer, clf)
# 假设已有训练数据
train_data = pd.DataFrame({
    'text': ['Image recognition in computer vision', 'Language models in NLP', 'Biomedical data analysis', 'Robotics and automation'],
    'field': ['Computer Vision', 'Natural Language Processing', 'Biomedical', 'Robotics']
})
model.fit(train_data['text'], train_data['field'])
# 预测领域
df_papers['predicted_field'] = model.predict(df_papers['abstract'])
# 统计跨领域引用量
cross_domain_citations = df_papers['predicted_field'].value_counts()
# 可视化
plt.figure(figsize=(10, 6))
sns.barplot(x=cross_domain_citations.index, y=cross_domain_citations.values, palette='viridis')
plt.title('跨领域引用分布')
plt.xlabel('学科领域')
plt.ylabel('引用次数')
plt.xticks(rotation=45)
plt.show()

下面对跨领域引用进行展开解释：
跨领域引用通常是指同一数据集所对应的学术论文，被不同学科领域或研究方向的文献所引用的情况。
它体现了数据集在多个学科交叉点上的影响力和应用价值。如果一个数据集能在多个领域中被广泛引用，通常意味着该数据集具有较强的通用性和较高的学术价值，可以推动多个学科的研究进展。
1.多学科交叉：跨领域引用可以帮助研究者了解数据集与其他学科的衔接点，挖掘新的研究方向或应用场景。
2.价值评估：跨学科引用越多，通常说明数据集更具潜在推广价值和学术影响力，可引导研究人员在项目立项与经费分配时将其纳入重点考虑范围。

接下来给出计算跨领域引用的方法：
1. 领域分类
1）自动化分类
利用自然语言处理（NLP）和机器学习算法，对论文的标题、摘要或关键词等文本内容进行分析，将其自动划分到预定义领域（如计算机视觉、自然语言处理、医学影像等）。
机器学习模型（如朴素贝叶斯、SVM、随机森林等）或深度学习模型（如BERT等）都可用于文本分类。
根据数据规模和准确度需求，可结合向量化方法（TF-IDF、Word2Vec、BERT Embeddings等）进行特征提取，提升分类性能。
2）手动标注
对自动分类结果进行抽样检查和人工验证，主要用于修正模型的误判和领域边界不清的问题。
若有不符合预期的文本，可进行人工重分类或在训练集中添加更多示例，以迭代提升模型准确性。
2. 跨领域引用量统计
跨领域引用量：可以通过对文献所属领域的分布进行统计来衡量。例如，“计算机视觉”领域中引用该数据集的论文数量为多少，“自然语言处理”领域有多少，等等。若一篇论文预测为多个领域，则可为其进行加权处理，或记为“多学科交叉”。
不同领域的引用占比：除了单纯计算各领域的引用数量，也可以对比各领域引用占总引用量的百分比，帮助直观感受数据集在每个领域的影响程度。
趋势分析（可选）：若能获取不同时期的引用数据，还可对各领域引用量在时间维度上进行分析，了解数据集的“跨领域扩散”进程。

这里给出自动化分类的代码示例：
from sklearn.feature_extraction.text import TfidfVectorizer
from sklearn.naive_bayes import MultinomialNB
from sklearn.pipeline import make_pipeline
# 将标题和摘要合并，构建文本特征（示例做法，也可单独使用摘要或标题）
train_texts = train_data['title'] + ' ' + train_data['abstract']
train_labels = train_data['field']
# 构建TF-IDF + 朴素贝叶斯的Pipeline
model_pipeline = make_pipeline(
    TfidfVectorizer(stop_words='english'),
    MultinomialNB()
)
# 训练模型
model_pipeline.fit(train_texts, train_labels)
# 对测试集（待分类论文）的文本进行预测
test_texts = test_data['title'] + ' ' + test_data['abstract']
predicted_fields = model_pipeline.predict(test_texts)
# 将预测结果合并到 test_data 中
test_data['predicted_field'] = predicted_fields
print("=== 预测结果 ===")
print(test_data[['title', 'predicted_field']])


这里给出趋势分析的代码示例：
import matplotlib.pyplot as plt
# 假设 test_data 中还包含一列 'year' 表示论文年份
# 为了演示，这里先随意生成一些年份
import random
years = [2020, 2021, 2022]
test_data['year'] = [random.choice(years) for _ in range(len(test_data))]
# 按年份和预测领域进行统计
year_field_counts = test_data.groupby(['year', 'predicted_field']).size().reset_index(name='count')
# 进一步可在这里选择用pivot_table来转成矩阵形式，便于可视化
pivot_df = year_field_counts.pivot_table(index='year', columns='predicted_field', values='count', fill_value=0)
# 绘制堆叠柱状图
pivot_df.plot(kind='bar', stacked=True, figsize=(8, 5))
plt.title('按年份统计跨领域引用量')
plt.xlabel('年份')
plt.ylabel('引用数')
plt.legend(title='领域')
plt.show()


给出手工标注中抽样检查与人工验证的代码片段：
import pandas as pd

# 假设 test_data 是待分类的论文数据集，已包含 'predicted_field' 列
# 示例数据
test_data = pd.DataFrame({
    'title': [
        'A study on image segmentation with transformers',
        'Dialogue generation using GPT-based models',
        'An analysis of CT scans in diagnosing lung diseases'
    ],
    'abstract': [
        'We investigate image segmentation performance using transformer models in computer vision...',
        'This research focuses on dialogue generation with GPT-based models in NLP context...',
        'CT scans are crucial in diagnosing lung diseases, providing new insights in medical imaging...'
    ],
    'predicted_field': [
        'Computer Vision',
        'Natural Language Processing',
        'Medical Imaging'
    ],
    'year': [2020, 2021, 2022]
})

# 抽取10%的样本进行人工审核
sample_size = int(0.1 * len(test_data))  # 根据数据量调整抽样比例
sample_data = test_data.sample(n=sample_size, random_state=42).copy()

print("=== 抽样检查样本 ===")
print(sample_data)


给出跨领域引用分析整体示例代码：
import pandas as pd
from sklearn.feature_extraction.text import TfidfVectorizer
from sklearn.naive_bayes import MultinomialNB
from sklearn.pipeline import make_pipeline
import matplotlib.pyplot as plt
import seaborn as sns

# --------------------------
# 1. 数据准备
# --------------------------

# 原始训练数据
train_data = pd.DataFrame({
    'title': [
        'Deep learning for image recognition',
        'A novel NLP model for text generation',
        'Advanced MRI techniques in medical imaging',
        'Speech recognition with CNN and RNN',
        'Transformer-based BERT model for question answering',
        'COVID-19 detection in chest X-ray images'
    ],
    'abstract': [
        'This paper explores deep learning for image classification in computer vision...',
        'We propose a new approach for text generation using advanced NLP techniques...',
        'MRI scans in medical imaging have shown potential for early disease detection...',
        'Exploring CNN and RNN architectures for automatic speech recognition tasks...',
        'This paper introduces a Transformer-based method for QA in NLP tasks...',
        'Using deep CNN for detecting COVID-19 in chest X-ray images in the medical domain...'
    ],
    'field': [
        'Computer Vision', 
        'Natural Language Processing', 
        'Medical Imaging', 
        'Speech Processing', 
        'Natural Language Processing', 
        'Medical Imaging'
    ]
})

# 待分类的测试数据
test_data = pd.DataFrame({
    'title': [
        'A study on image segmentation with transformers',
        'Dialogue generation using GPT-based models',
        'An analysis of CT scans in diagnosing lung diseases'
    ],
    'abstract': [
        'We investigate image segmentation performance using transformer models in computer vision...',
        'This research focuses on dialogue generation with GPT-based models in NLP context...',
        'CT scans are crucial in diagnosing lung diseases, providing new insights in medical imaging...'
    ],
    'year': [2020, 2021, 2022]
})

# --------------------------
# 2. 自动化分类
# --------------------------

# 合并标题和摘要作为文本特征
train_texts = train_data['title'] + ' ' + train_data['abstract']
train_labels = train_data['field']

test_texts = test_data['title'] + ' ' + test_data['abstract']

# 构建TF-IDF + 朴素贝叶斯的Pipeline
model_pipeline = make_pipeline(
    TfidfVectorizer(stop_words='english'),
    MultinomialNB()
)

# 训练模型
model_pipeline.fit(train_texts, train_labels)

# 对测试集进行预测
predicted_fields = model_pipeline.predict(test_texts)
test_data['predicted_field'] = predicted_fields

print("=== 自动分类预测结果 ===")
print(test_data[['title', 'predicted_field']])

# --------------------------
# 3. 手动标注
# --------------------------

# 抽取10%的样本进行人工审核
sample_size = int(0.1 * len(test_data))  # 根据数据量调整抽样比例
sample_data = test_data.sample(n=sample_size, random_state=42).copy()

print("\n=== 抽样检查样本 ===")
print(sample_data)

# 模拟人工修正（实际应用中应由人工完成）
# 例如，将第一个样本的预测领域修正为 'Computer Vision'
if not sample_data.empty:
    sample_index = sample_data.index[0]
    test_data.loc[sample_index, 'predicted_field'] = 'Computer Vision'

print("\n=== 修正后的样本 ===")
print(test_data.loc[sample_index])

# 将修正后的样本添加到训练集中
# 这里只添加标题、摘要和修正后的领域标签
corrected_sample = test_data.loc[sample_index].copy()
corrected_sample = corrected_sample.rename({'predicted_field': 'field'})
updated_train_data = pd.concat([train_data, corrected_sample[['title', 'abstract', 'field']]], ignore_index=True)

# --------------------------
# 4. 重新训练分类模型
# --------------------------

# 构建新的文本特征
updated_train_texts = updated_train_data['title'] + ' ' + updated_train_data['abstract']
updated_train_labels = updated_train_data['field']

# 重新构建Pipeline并训练
updated_model_pipeline = make_pipeline(
    TfidfVectorizer(stop_words='english'),
    MultinomialNB()
)

updated_model_pipeline.fit(updated_train_texts, updated_train_labels)

# 对测试集进行重新预测
updated_predicted_fields = updated_model_pipeline.predict(test_texts)
test_data['predicted_field'] = updated_predicted_fields

print("\n=== 重新训练后的预测结果 ===")
print(test_data[['title', 'predicted_field']])

# --------------------------
# 5. 跨领域引用量统计
# --------------------------

# 统计各领域的引用数量
field_counts = test_data['predicted_field'].value_counts()

print("\n=== 各领域引用数量 ===")
print(field_counts)

# 计算各领域引用占比
total_citations = field_counts.sum()
field_percentages = (field_counts / total_citations) * 100

print("\n=== 各领域引用占比 (%) ===")
print(field_percentages)

# 绘制柱状图
sns.set(style="whitegrid")
plt.figure(figsize=(10, 6))
sns.barplot(x=field_counts.index, y=field_counts.values, palette='viridis')
plt.title('跨领域引用分布')
plt.xlabel('学科领域')
plt.ylabel('引用数量')
plt.xticks(rotation=45)
plt.show()

# 绘制饼图
plt.figure(figsize=(8, 8))
plt.pie(field_counts.values, labels=field_counts.index, autopct='%1.1f%%', startangle=140, colors=sns.color_palette('viridis', len(field_counts)))
plt.title('各领域引用占比')
plt.axis('equal')  # 保证饼图为圆形
plt.show()

# --------------------------
# 6. （可选）趋势分析
# --------------------------

# 假设 test_data 中包含 'year' 列
# 这里展示如何进行时间趋势分析

# 按年份和预测领域进行统计
year_field_counts = test_data.groupby(['year', 'predicted_field']).size().reset_index(name='count')

print("\n=== 按年份和领域的引用统计 ===")
print(year_field_counts)

# 使用pivot_table转换为矩阵形式，便于绘图
pivot_df = year_field_counts.pivot_table(index='year', columns='predicted_field', values='count', fill_value=0)

# 绘制堆叠柱状图
pivot_df.plot(kind='bar', stacked=True, figsize=(12, 7), colormap='viridis')
plt.title('按年份统计跨领域引用量')
plt.xlabel('年份')
plt.ylabel('引用数')
plt.legend(title='学科领域')
plt.xticks(rotation=0)
plt.show()

# 绘制折线图（每个领域一条线）
plt.figure(figsize=(12, 7))
for field in pivot_df.columns:
    plt.plot(pivot_df.index, pivot_df[field], marker='o', label=field)
plt.title('各领域引用量的时间趋势')
plt.xlabel('年份')
plt.ylabel('引用数')
plt.legend(title='学科领域')
plt.grid(True)
plt.show()

给出上述代码的总结：
1.数据准备
构建带领域标签的 train_data 用于模型训练；
构建不带领域标签（或需要预测的）test_data 用于模型预测。
2.模型训练
使用 TF-IDF 将文本向量化；
使用 朴素贝叶斯（或其他分类器）进行领域分类训练；
调用 model_pipeline.predict() 对新论文进行领域预测。
3.手动校验与修正（可选）
对预测结果进行抽样检查，如有误判则修正；
修正结果可纳入新的训练集中，迭代提升模型性能。
4.跨领域引用统计
依据 predicted_field 统计各领域文献数，进而计算占比；
若需要多标签处理或加权统计，则需改写计数逻辑。
5.趋势分析（可选）
结合时间信息（如发表年份、引用年份），按年度和领域统计文献分布；
通过折线图或堆叠柱状图呈现领域引用随时间的变化。
该流程能够帮助在处理大规模文献数据、需要自动化识别不同领域文献的场景下快速上手，并为后续的跨领域引用分析、学术趋势洞察奠定基础。
根据项目规模与精度需求，你可替换所用的模型或特征提取方法（如 BERT、GPT 等），并对手动标注与持续迭代策略进行深入设计，以实现更高的分类准确率与更精细的跨领域分析。


构建带领域标签的 train_data
建议：
1.数据多样性与代表性：
多来源数据：确保训练数据来自多个学术数据库（如PubMed、IEEE Xplore、ACM Digital Library等），涵盖不同领域和出版物类型，以提高模型的泛化能力。
平衡各领域数据：避免某些领域的数据过多而其他领域数据稀少，导致模型对频繁领域过拟合。可采用过采样（如SMOTE）或欠采样技术平衡数据。
2.丰富的元数据：
扩展特征：除了标题和摘要，还可以包括关键词、作者信息、发表年份、期刊名称等元数据，作为辅助特征，提高分类模型的表现。
领域细分：根据需要，将大领域进一步细分为更具体的子领域（如将“计算机视觉”细分为“图像识别”、“目标检测”等），以实现更精细的分类。
3.高质量标签：
准确标注：确保训练数据的领域标签准确无误，可以通过专家审核或使用高可信度的数据源（如期刊分类）来获取标签。
一致性检查：制定明确的标签标准和分类指南，确保标签的一致性，减少主观偏差。
4.数据清洗与预处理：
去重与纠错：移除重复的论文记录，修正拼写错误和格式问题，确保数据的一致性和准确性。
处理缺失值：对缺失的关键信息（如标题、摘要）进行填补或删除，避免影响模型训练。

构建不带领域标签的 test_data
1.确保数据一致性：
与训练数据格式一致：确保测试数据的格式和结构与训练数据一致，包括字段名称、文本编码等，以避免预处理和模型预测过程中的兼容性问题。
2.数据多样性：
涵盖多领域：测试数据应涵盖多个领域，以全面评估模型的分类性能。
包含不同文本长度：确保测试数据包含不同长度的摘要和标题，以测试模型在处理长文本和短文本时的表现。
3.元数据补充：
时间信息：如果进行趋势分析，确保测试数据包含发表年份或引用年份等时间信息。
引用关系：如有可能，获取引用关系（即哪些论文引用了哪些其他论文），以便进行更深入的网络分析。
4.数据量充足：
大规模数据：尽量获取足够数量的测试样本，以确保统计分析的可靠性和可视化结果的代表性。

