---
title: "Scaling Data-Intensive Workloads on Azure Kubernetes Service (AKS) with Assurance"
description: "Sample - Add your description"
date: 2050-08-24 # date is important. future dates will not be published
author: Anson Qian # must match the authors.yml in the _data folder
categories: general # general, operations, networking, security, developer topics, add-ons
---

## Background

In the fast-paced digital landscape, efficiently managing and processing large volumes of data is not just a requirement but a necessity for businesses in various industries. The exponential growth in data generation necessitates scalable solutions that can handle this demand without compromising on reliability. The recent advancements in Large Language Models (LLMs) and generative AI have further underscored the importance of scalable infrastructures like AKS for machine learning training and inferencing, making it an indispensable tool for businesses looking to stay ahead in the technology curve. For instance, data scientists and machine learning engineers can easily configure and utilize a Jupyter notebook on top of Azure Machine Learning (AML) with [AKS serving as the compute engine](https://learn.microsoft.com/en-us/azure/machine-learning/how-to-attach-kubernetes-anywhere?view=azureml-api-2).

The challenges of handling increased data volume, velocity, and variety are compounded by the need for diverse and dynamic infrastructure requirements, especially for companies with nimble teams tasked with managing cloud infrastructure. Azure Kubernetes Service (AKS) offers a solution that not only addresses the scalability and reliability concerns but does so in a way that is manageable for smaller, agile teams. It simplifies the management of clusters and workloads, making it easier for businesses to process and analyze large datasets efficiently.

This blog will explore the strategic approaches to scaling data-intensive workloads on AKS, with a particular focus on ensuring scalability and reliability during infrastructure operation. We will dive into architectural considerations, best practices, and technical details that enable businesses, especially those with limited cloud infrastructure engineering resources, to optimize their data-intensive applications on AKS.

## Architecture Consideration

Prefer horizontal scale to vertical scale, treat single cluster as cow instead of kitten, auto manage clusters with fleet or capz, consider adding another layer of ML workloads queuing and dispatching for ultra scale


## Operation Consideration

### Avoid overload ETCD
1. Don’t store a hundred fields like environment variables on CRD
2. Be mindful of custom events publish & sub patten
3. Have a data retention policy for jobs, pods and pv/pvcs, use ttlSeconds, use delete


### Avoid overload API Server
1. Avoid daemon with list-and-watch pattern at all cost - fluent-bit, cilium agent, consider using a cache service like Datadog Cluster Agent or istiod
2. Follow best practices to implement API service client list calls, e.g. not like Keda, and keep them stable, e.g. Prometheus server
3. Fine tune APF to throttle sub-optimal call pattern more aggressively, introduce your own flow control


### Avoid Excessive Autoscaling
1. Use resource quota to control the upper bound
2. Use over-provision to control the lower bound
3. Don’t scale up/down too much and too often


### Avoid Resources Overcommitting
1. Resource Allocation and Management: Configure Resource Requests and Limits and QoS classes: Properly set CPU and memory (RAM) requests and limits for your ML pods to ensure they have enough resources to run efficiently without over-allocating resources, which could lead to waste. Kubernetes allows you to specify these parameters in the pod specifications. Utilizing Vertical Pod Autoscaler (VPA) can help adjust these values dynamically based on usage. Leverage Quality of Service (QoS) Classes: Kubernetes offers QoS classes (Guaranteed, Burstable, and BestEffort) that determine the scheduling and eviction priorities of pods. For critical ML workloads, ensure they are classified as Guaranteed by setting equal CPU and memory requests and limits, ensuring they have the highest priority and are last to be evicted under resource pressure.

2. Use Node Pools: Create dedicated node pools for your ML workloads. This allows you to select the appropriate machine types that match your workload requirements, such as GPU-enabled nodes for deep learning tasks. Assigning workloads to the right node pool can significantly improve performance and cost efficiency.

3. Optimizing Networking & Storage for Distributed ML Workloads:: Use Zone redundant Azure file based PV to access data., Consider image cache and use p2p instead of single image registry for fast application boot time, Use premium storage and local ssd

## Summary

