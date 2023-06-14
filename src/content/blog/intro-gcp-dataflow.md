---
title: Intro to Google Dataflow (Hosted Apache Beam)
author: Richard Chien
pubDatetime: 2022-02-06
postSlug: intro-dataflow-gcp
featured: false
draft: false
tags:
  - data
  - dataflow
  - gcp
description: "Introduction to Dataflow architecture and features (with videos)"
---

---

author: Sat Naing
pubDatetime: 2022-09-23T15:22:00Z
title: Adding new posts in AstroPaper theme
postSlug: adding-new-posts-in-astropaper-theme
featured: true
draft: false
tags:

- docs
  ogImage: ""
  description:
  Some rules & recommendations for creating or adding new posts using AstroPaper
  theme.

---

<aside>
✨ I hosted a study group on intro to Google’s Dataflow Architecture using miro (which, BTW is a great way to spice up presentation with doses of interactivity). Below are key points taken from slides with talking points and links. Hope this helps people who are starting on their Google Dataflow journey 🙂

</aside>

# Background

Google’s Dataflow is a runner for Apache Beam. Apache beam is a unified and portable batch and streaming data processing framework. Dataflow is one of the popular beam runner. FlinkRunner and SparkRunner are also popular choices in community. Here’s a [capability matrix](https://beam.apache.org/documentation/runners/capability-matrix/) for reading pleasure.

# Why Dataflow

- Not available from other cloud vendors
- Born from Google technologies. Active involvement from Google engineers

- Dataflow specific features
  - Shuffle Service, Streaming Engine
  - FlexRS
  - GPU Support
  - IAM, Monitoring, Logging built-in
- Optimizations
  - Graph optimization
    - Fusion, combine
  - Auto scaling and sharding
    - [GroupIntoBatches](https://www.youtube.com/watch?v=jses0W4Zalc&list=PL4dEBWmGSIU8vLWF56shrSuTsLXvO6Ex3)
- New features
  - Prime - VPA (similar to k8 VPA), better monitoring and scaling

# Overall Architecture

![overall architecture](/public/blog/intro-gcp-dataflow/overall-architecture.png)

# V2 portable runner

[https://www.youtube.com/watch?v=tXdnPKPnY3E&list=PLjYq1UNvv2UcrfapfgKrnLXtYpkvHmpIh&t=396s](https://www.youtube.com/watch?v=tXdnPKPnY3E&list=PLjYq1UNvv2UcrfapfgKrnLXtYpkvHmpIh&t=396s)

# From code to dataflow job

[https://www.youtube.com/watch?v=udKgN1_eThs&t=250s](https://www.youtube.com/watch?v=udKgN1_eThs&t=250s)

<aside>
💡 Note that one crucial point not mentioned is data transfer cost between different regions. Also make sure to disable external IP (which is enabled by default) to avoid unnecessary networking charges.

</aside>

## [Read](https://cloud.google.com/dataflow/docs/guides/deploying-a-pipeline#pipeline-lifecycle:-from-pipeline-code-to-dataflow-job)

## Summary

To summarize, below are the diffrent stages of manifest

1. Code
2. Graph (graph construction time)
   1. Traverse from main entry point to generate nodes of graph
   2. Executes locally on the machine that runs the pipeline (2b differs depending on use of classic vs flex template)
   3. Validates all resources (GCS, PubSub, etc)
   4. Check other errors
3. Job (job creation time)
   1. Translated to JSON format and sent it to Dataflow service endpoint
   2. Validates JSON and replies with jobId
   3. Becomes a job on the Dataflow Service
4. Job Execution Time
   1. The Dataflow service starts provisioning worker VMs. Serialized processing functions from the execution graph and required libraries are downloaded to the worker VMs and the Dataflow service starts distributing the data bundles to be processed on these VMs.

## Sample execution graph (WordCount)

![sample execution graph](/public/blog/intro-gcp-dataflow/sample-execution-graph.png)

# Unique services to dataflow

## Shuffle Service

[https://youtu.be/udKgN1_eThs?t=519](https://youtu.be/udKgN1_eThs?t=519)

## Streaming Engine

[https://youtu.be/tXdnPKPnY3E?list=PLjYq1UNvv2UcrfapfgKrnLXtYpkvHmpIh&t=396](https://youtu.be/tXdnPKPnY3E?list=PLjYq1UNvv2UcrfapfgKrnLXtYpkvHmpIh&t=396)

## [FlexRS](https://youtu.be/tXdnPKPnY3E?list=PLjYq1UNvv2UcrfapfgKrnLXtYpkvHmpIh&t=396)

[https://youtu.be/udKgN1_eThs?t=659](https://youtu.be/udKgN1_eThs?t=659)

# Dataflow graph optimizations

[https://youtu.be/udKgN1_eThs?t=74](https://youtu.be/udKgN1_eThs?t=74)

# Auto scaling and sharding

These are two different concepts. Personally I consider

Auto-sharding = dynamically adjust partition size
Auto-scaling = dynamically adjust worker size

## Auto sharding and scaling

[https://youtu.be/tXdnPKPnY3E?list=PLjYq1UNvv2UcrfapfgKrnLXtYpkvHmpIh&t=985](https://youtu.be/tXdnPKPnY3E?list=PLjYq1UNvv2UcrfapfgKrnLXtYpkvHmpIh&t=985)

## Auto-sharding

[https://www.youtube.com/watch?v=udKgN1_eThs&t=461s](https://www.youtube.com/watch?v=udKgN1_eThs&t=461s)

<aside>
💡 Highly recommend watching [GroupIntoBatches](https://www.youtube.com/watch?v=jses0W4Zalc&list=PL4dEBWmGSIU8vLWF56shrSuTsLXvO6Ex3)

</aside>

# New features

Dataflow Prime

[https://youtu.be/udKgN1_eThs?t=813](https://youtu.be/udKgN1_eThs?t=813)

# Resources

- [https://cloud.google.com/dataflow/docs/guides/deploying-a-pipeline#pipeline-lifecycle:-from-pipeline-code-to-dataflow-job](https://cloud.google.com/dataflow/docs/guides/deploying-a-pipeline#pipeline-lifecycle:-from-pipeline-code-to-dataflow-job)
- [https://cloud.google.com/blog/products/data-analytics/introducing-cloud-dataflows-new-streaming-engine](https://cloud.google.com/blog/products/data-analytics/introducing-cloud-dataflows-new-streaming-engine)
- [https://cloud.google.com/blog/topics/developers-practitioners/why-you-should-be-using-flex-templates-your-dataflow-deployments](https://cloud.google.com/blog/topics/developers-practitioners/why-you-should-be-using-flex-templates-your-dataflow-deployments)
- [Best Practices](https://cloud.google.com/architecture/building-production-ready-data-pipelines-using-dataflow-overview)
