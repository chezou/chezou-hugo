+++
date = "2016-08-24T16:07:02+09:00"
draft = true
title = "livy & Jupyter notebook & sparkmagic = Powerful & Easy notebook for Data Scientist"

+++

livy is a REST server of Spark. You can see [the talk of the Spark Summit 2016](https://spark-summit.org/2016/events/livy-a-rest-web-service-for-apache-spark/),  [Microsoft uses livy for HDInsight with Jupyter notebook and sparkmagic](https://azure.microsoft.com/en-us/documentation/articles/hdinsight-apache-spark-jupyter-notebook-kernels/). [Jupyter notebook](http://jupyter.org/) is one of the most popular notebook OSS within data scientists. Using sparkmagic + Jupyter notebook, data scientists can execute ad-hoc Spark job easily.

# Why livy is good?

According to [the official document](http://livy.io/overview.html), livy has features like:

- Have long running SparkContexts that can be used for multiple Spark jobs, by multiple clients
- Share cached RDDs or Dataframes across multiple jobs and clients
- Multiple SparkContexts can be managed simultaneously, and they run on the cluster (YARN/Mesos) instead of the Livy Server for good fault tolerance and concurrency
- Jobs can be submitted as precompiled jars, snippets of code, or via Java/Scala client API
- Ensure security via secure authenticated communication
- Apache License, 100% open source

# Why livy + sparkmagic?

[sparkmagic](https://github.com/jupyter-incubator/sparkmagic) is a client of livy using with Jupyter notebook. When we write Spark code at our local Jupyter client, then sparkmagic runs the Spark job through livy. Using sparkmagic + Jupyter notebook, data scientists can use Spark from their own Jupyter notebook, which is running on their localhost. We don't need any Spark configuration getting from the CDH cluster. So we can execute Spark job in cluster like running on a local machine.

![](blog/sparkmagic_diagram.png)

diagram from https://github.com/jupyter-incubator/sparkmagic/raw/master/screenshots/diagram.png

# Requirements

- Spark Cluster
    - Cloudera Director is nice to prepare
    - Install git and maven
    - I tried CDH 5.7 with CentOS 7
- Local jupyter client
    - virtualenv and virtualenvwrapper is awesome

# Preparation

In order to use livy with sparkmagic, we should install livy into the Spark gateway server and sparkmagic into local machine.

- [cloudera/livy: Livy is an open source REST interface for interacting with Apache Spark from anywhere](https://github.com/cloudera/livy)
- [jupyter-incubator/sparkmagic: Jupyter magics and kernels for working with remote Spark clusters](https://github.com/jupyter-incubator/sparkmagic)

## Install R

```sh
$ sudo yum install -y epel-release
$ sudo yum install -y R
```

## Build livy

```sh
$ git clone git@github.com:cloudera/livy.git
$ cd livy
$ mvn -Dspark.version=1.6.0 -DskipTests clean package
```

Because of failing test at that time, I added `-DskipTests` to build.

## Run livy

Set environment variables as following:

```sh
$ export SPARK_HOME=/opt/cloudera/parcels/CDH-5.7.1-1.cdh5.7.1.p0.11/lib/spark
$ export HADOOP_CONF_DIR=/etc/hadoop/conf
```

Add following configuration into livy.conf:

```sh
livy.server.session.factory = yarn
```

Let's run livy server

```sh
$ ./bin/livy-server
```

Open another terminal and check the server

```sh
$ curl localhost:8998/sessions
{"from":0,"total":0,"sessions":[]}
```

As livy's Default port number is 8998, we should open or forward the port.

## Prepare sparkmagic in local machine

Install sparkmagic with [document](https://github.com/jupyter-incubator/sparkmagic):

```sh
$ pip install sparkmagic
$ jupyter nbextension enable --py --sys-prefix widgetsnbextension
```

Then install wapper kernel. Do pip show sparkmagic and you can see the Location info. In the following example, Location is /Users/ariga/.virtualenvs/ibis/lib/python3.5/site-packages.

```sh
$ pip show sparkmagic
---
Metadata-Version: 2.0
Name: sparkmagic
Version: 0.2.3
Summary: SparkMagic: Spark execution via Livy
Home-page: https://github.com/jupyter-incubator/sparkmagic/sparkmagic
Author: Jupyter Development Team
Author-email: jupyter@googlegroups.org
Installer: pip
License: BSD 3-clause
Location: /Users/ariga/.virtualenvs/ibis/lib/python3.5/site-packages
Requires: ipywidgets, pandas, ipython, requests, mock, autovizwidget, numpy, nose, ipykernel, notebook, hdijupyterutils
Classifiers:
  Development Status :: 4 - Beta
  Environment :: Console
  Intended Audience :: Science/Research
  License :: OSI Approved :: BSD License
  Natural Language :: English
  Programming Language :: Python :: 2.6
  Programming Language :: Python :: 2.7
  Programming Language :: Python :: 3.3
  Programming Language :: Python :: 3.4
$ cd /Users/ariga/.virtualenvs/ibis/lib/python3.5/site-packages
$ jupyter-kernelspec install sparkmagic/kernels/sparkkernel
$ jupyter-kernelspec install sparkmagic/kernels/pysparkkernel
```

Copy the [config.json](https://github.com/jupyter-incubator/sparkmagic/blob/master/sparkmagic/example_config.json) into ~/.sparkmagic/config.json and modify it.


### Run jupyter notebook

Before running jupyter, I recommend to check the connection from local machine to the livy server.

```
$ curl YOUR_HOSTNAME:8998/sessions
```

Launch jupyter notebook and create PySpark notebook (of course you can use Spark)

```
$ jupyter notebook
```

The example notebook is here

<iframe src="https://nbviewer.jupyter.org/gist/chezou/88568ce2bb620107cfdbdd20f0c966ae" width=800 height=1200> </iframe>

In the nbviewer, we can not see the result of SQL, but we can visualize the result of SQL with `%%sql` magic command. That's awesome :)

![](blog/example-sparkmagic.png)

If you use `%%local`, you can use local Python libraries such as scikit-learn, seaborn etc, with received results from PySpark.

# References

- [Livy, an Open Source REST Service for Apache Spark](http://livy.io/)
- [How to use the Livy Spark REST Job Server API for doing some interactive Spark with curl | Hue - Hadoop User Experience - The Apache Hadoop UI](http://gethue.com/how-to-use-the-livy-spark-rest-job-server-for-interactive-spark-2-2/)