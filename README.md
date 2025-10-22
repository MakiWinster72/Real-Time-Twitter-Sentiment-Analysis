## 大数据项目：基于 Kafka、Spark（MLLib & Streaming）、MongoDB 与 Django 的实时推文情感分析

### 概述

本项目旨在对 **Twitter 推文进行实时情感分析（情绪分类）**，利用多种大数据技术实现从数据采集、流式处理、分析到可视化展示的全流程。

---

### 项目架构

该项目由以下组件构成：

* **Apache Kafka**：从推文数据集（Twitter DataSet）中进行实时数据采集。
* **Spark Streaming**：从 Kafka 获取数据流并执行情感分析。
* **MongoDB**：存储分析后的情感数据。
* **Django**：构建实时可视化仪表盘（dashboard），展示情感分析结果。
* **chart.js** 与 **matplotlib**：用于图表绘制与可视化。

项目结构示意图：
![项目流程图](imgs/flow.png)

---

### 功能特性

* **实时数据采集**：通过 Kafka 从推文数据集中收集推文数据。
* **流式处理**：使用 Spark Streaming 实时处理与分析数据流。
* **情感分析**：基于自然语言处理（NLP）将推文分类为正面、负面或中性。
* **数据存储**：分析结果持久化保存至 MongoDB。
* **可视化展示**：通过 Django 构建实时情感趋势可视化仪表盘。

---

### 数据说明

项目使用的数据集包括 `twitter_training.csv` 与 `twitter_validation.csv`，用于训练 PySpark 模型并生成 Kafka 实时推文流。

* 数据集 `twitter_training.csv` 包含约 **74,682 条推文记录**。
* 每条记录的字段包括：

  * Tweet ID：整数
  * Entity：字符串
  * Sentiment（目标标签）：字符串
  * Tweet Content：字符串

验证集 `twitter_validation.csv` 含 **998 条推文**，字段结构相同。

数据来源：
[https://www.kaggle.com/datasets/jp797498e/twitter-entity-sentiment-analysis](https://www.kaggle.com/datasets/jp797498e/twitter-entity-sentiment-analysis)

---

### 仓库结构

* **Django-Dashboard**：Django 实时仪表盘应用
* **Kafka-PySpark**：包含 Kafka producer 与 PySpark consumer（流处理）
* **ML PySpark Model**：训练好的模型、Jupyter Notebook 与数据集
* **zk-single-kafka-single.yml**：Kafka 的 Docker 部署配置文件
* **bigdataproject rapport**：项目报告（法语）

---

### 环境准备

#### 前置要求

运行本项目需要安装以下环境：

* Docker（用于运行 Kafka）
* Python 3.x
* Apache Kafka
* Apache Spark（PySpark）
* MongoDB
* Django

---

### 安装步骤

1. **克隆项目仓库**

   ```bash
   git clone https://github.com/drisskhattabi6/Real-Time-Twitter-Sentiment-Analysis.git
   cd Real-Time-Twitter-Sentiment-Analysis
   ```

2. **安装 Docker Desktop**

3. **设置 Kafka**

   ```bash
   docker-compose -f zk-single-kafka-single.yml up -d
   ```

4. **安装 MongoDB**
   建议同时安装 **MongoDB Compass**，便于数据可视化。

5. **安装 Python 依赖(建议先虚拟化)**

   ```bash
   pip install -r requirements.txt
   ```

---

### 运行项目

运行前请确保 MongoDB 已启动，否则 Kafka 与 Spark 无法写入数据。

#### 启动 MongoDB

```bash
sudo systemctl start mongodb
```

或使用 MongoDB Compass。

---

#### 运行 Kafka 与 Spark Streaming

1. 切换目录：

   ```bash
   cd Kafka-PySpark
   ```

2. 启动 Kafka 容器：

   ```bash
   docker exec -it <kafka-container-id> /bin/bash
   ```
> kafka-container-id 可用 docker container ls 查看

3. 创建 Kafka 主题：

   ```bash
   kafka-topics --create --topic twitter --bootstrap-server localhost:9092
   kafka-topics --describe --topic twitter --bootstrap-server localhost:9092
   ```

4. 启动生产者（Producer）：

   ```bash
   python producer-validation-tweets.py
   ```

5. 启动 PySpark 消费者（Consumer）：

   ```bash
   python consumer-pyspark.py
   ```

运行效果：
![运行Kafka与Spark流处理](imgs/img6.png)
运行后，MongoDB Compass 中将可看到分析结果：
![MongoDBCompass 数据展示](imgs/img4.png)

---

#### 启动 Django 仪表盘

1. 切换目录：

   ```bash
   cd Django-Dashboard
   ```

2. 创建静态资源目录：

   ```bash
   python manage.py collectstatic
   ```

3. 启动 Django 服务：

   ```bash
   python manage.py runserver
   ```
> 如果卡在下载数据集，前往[nltk.org](https://www.nltk.org/nltk_data/)下载punkt_tab(或项目根目录(文件日期2510222)),解压到~/nltk_data/

4. 打开浏览器访问：

   ```
   http://127.0.0.1:8000
   ```

   查看实时情感分析仪表盘。

![仪表盘界面](imgs/img2.png)
![仪表盘运行截图](imgs/img3.png)

---

### 其他说明

* Django 仪表盘的数据来自 MongoDB。
* 用户可在 `http://127.0.0.1:8000/classify` 页面输入文本并进行实时情感分类。
* 仪表盘展示包括：

  * 推文情感标签表
  * 标签比例统计图
  * 饼图与柱状图展示情感趋势

---
## 项目来源：

摩洛哥塔恩吉尔大学（Abdelmalek Essaadi University）

* 专业：人工智能与数据科学硕士
* 课程模块：大数据

