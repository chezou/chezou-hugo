+++
date = "2016-08-23T13:46:03+09:00"
draft = false
title = "Building predictive model with Ibis and scikit-learn"

+++

# tl;dr
- visualising [MovieLens](http://grouplens.org/datasets/movielens/) 20M data (famous movie rating data) with [Ibis](http://www.ibis-project.org/)
- build predictive model for movie favor with scikit-learn
- [repo](https://github.com/chezou/ibis-demo) / [notebook](https://github.com/chezou/ibis-demo/blob/master/ibis_demo_en.ipynb)

# What is Ibis?

Ibis is a bridge between Python and Big Data. Ibis enables pandas handling Big Data.

![](/blog/ibis-overview1.png)

For more detail, see Wes's presentation.

<iframe src="//www.slideshare.net/slideshow/embed_code/key/MGvd2c7LUgy9et" width="595" height="485" frameborder="0" marginwidth="0" marginheight="0" scrolling="no" style="border:1px solid #CCC; border-width:1px; margin-bottom:5px; max-width: 100%;" allowfullscreen> </iframe> <div style="margin-bottom:5px"> <strong> <a href="//www.slideshare.net/wesm/ibis-scaling-python-analytics-on-hadoop-and-impala" title="Ibis: Scaling Python Analytics on Hadoop and Impala" target="_blank">Ibis: Scaling Python Analytics on Hadoop and Impala</a> </strong> from <strong><a href="//www.slideshare.net/wesm" target="_blank">Wes McKinney</a></strong> </div>


As you know, pandas is known as a killer application for data analysis. In my previous job, which is known as a developer of [world largest monolithic Ruby on Rails application](https://speakerdeck.com/a_matsuda/the-recipe-for-the-worlds-largest-rails-monolith), many Rails developer attracted with pandas and Jupyter notebook for sharing analysis result.

# Why Ibis?

pandas loads data on memory, so we have to filter with some SQL before analyzing. But we actualy want to get insight and handle without SQL.

# Preparation

## Impala cluster

* CDH 5.7 with Cloudera Director 2.1
* required port
  * impalad node's 21050 port
  * NN's 50070 port
* table is created with parquet on S3

## Ibis
* Python 3.5
* using wheel and virtualenv, I didn't use conda

# Notebook


<script src="https://gist.github.com/chezou/90950ab7a17df6d8dda972752787a246.js"></script>

Full notebook repo is [here](https://github.com/chezou/ibis-demo/).
I also executed same code for Redshift, but several dialects prevent exection...

https://github.com/chezou/ibis-demo/blob/master/ibis_demo_redshift.ipynb

# FAQ

## What is the difference between PySpark?
- Easy to setup. It is just like connecting DB
- Fast x10. So that we can x10 experiences. It makes us innovations!
- We can rapid prototyping with Ibis.

## Which is prefer to build model Ibis + scikit-learn or Spark + MLlib?
- It depends on data size.
- [Netflix uses Spark and R for building predictive models](https://www.infoq.com/news/2016/07/meson-framework-netflix). Netflix uses R in order to model filtered data such as specific country, and they use Spark for global model.