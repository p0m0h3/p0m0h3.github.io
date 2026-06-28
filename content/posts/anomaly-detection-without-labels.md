---
title: "Anomaly Detection Without Labels"
date: 2025-09-03
tags: ["machine learning", "ids", "unsupervised"]
---

The standard pitch for ML-based intrusion detection goes something like this: train a classifier on labeled network traffic, deploy it, watch it catch attacks. It sounds reasonable until you ask where the labels come from.

Labeled network intrusion datasets are scarce, expensive to produce, and age poorly. Attack patterns from a dataset collected in 2018 look nothing like what hits a modern network. And the rarest, most dangerous attacks — zero-days, novel APT techniques — have no labels at all because nobody has seen them before.

This is why unsupervised anomaly detection is worth taking seriously, even though it is considerably messier.

## The Setup

Instead of learning "this traffic pattern is malicious," you learn "this traffic pattern is unusual given everything I have seen." The model is trained entirely on normal traffic and learns to assign a low anomaly score to patterns it recognizes, and a high score to everything else.

Two families of approaches dominate:

**Reconstruction-based.** An autoencoder compresses input into a low-dimensional representation and reconstructs it. Normal traffic reconstructs well; anomalies produce high reconstruction error. Simple to train, interpretable loss, but sensitive to how you choose the bottleneck dimension.

**Density-based.** Models like Isolation Forest or SVDD learn a boundary around normal data. Points outside the boundary are anomalies. These work well on tabular flow features but struggle with sequential structure in packet-level data.

## What Makes Network Traffic Hard

Raw packets are not like image pixels or word tokens. The feature space is heterogeneous — port numbers, byte counts, flag combinations, inter-arrival times — and the relevant signal is often relational: this packet is anomalous not in isolation, but because of what preceded it in the flow.

Flattening a flow into a fixed-size feature vector throws away most of that structure. Keeping it means dealing with variable-length sequences, which pushes you toward LSTMs or Transformers, which are harder to train and interpret.

There is also the base rate problem. Even in an actively targeted network, malicious traffic is a tiny fraction of total volume. A model that flags 0.1% of traffic as anomalous will have a false positive rate that overwhelms any security team trying to triage alerts.

## The Threshold Problem

Every anomaly detector has a threshold: above it, raise an alert; below it, ignore. Setting this threshold is where most real-world deployments struggle.

Set it too low and you flood the SOC with noise. Set it too high and you miss attacks. The right threshold depends on the base rate of actual attacks, the cost of false positives, and the cost of missed detections — all of which vary by environment and are hard to quantify.

One underused approach: rather than a binary threshold, output a ranked list of the top-N most anomalous flows per hour. Let analysts triage the list rather than react to binary alerts. This shifts the problem from "what threshold is correct" to "how many flows can your team review" — a much more tractable question.

## Where This Is Going

The most promising recent direction is self-supervised pre-training on large unlabeled traffic corpora, followed by fine-tuning with the small number of labels that do exist. This is essentially the same playbook that transformed NLP — and early results on network data are encouraging.

The unsolved problem is concept drift. Normal traffic changes constantly as services are updated, usage patterns shift, and the network topology evolves. A model trained six months ago is already partially stale. Online learning and drift detection are active research areas, but nothing has become standard practice yet.
