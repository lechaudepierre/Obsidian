# INFO-H420 — Fairness (Lecture 9) Summary

## Overview

Fairness in data science concerns building and deploying AI/ML systems that do not discriminate against individuals or groups, while remaining transparent, accountable, and aligned with ethical and legal principles.

## Public Perception and Motivation

- Many users feel AI systems can be biased, untrustworthy, and non‑moral.
    
- Incidents like the COMPAS recidivism tool highlight how model errors can differentially harm protected groups (e.g., higher false positives for some groups).
    

## Responsible AI Foundations

- Responsible AI emphasizes ethical, transparent, and human‑centered practices.
    
- Common principles (recurring across guidelines): transparency, justice/fairness, non‑maleficence, responsibility, privacy, and trust.
    

## What Is Fairness?

- Political philosophy: justice as fairness (equality of outcome vs equality of opportunity vs social welfare).
    
- Legal framing: non‑discrimination.
    
    - **Disparate treatment:** intentional discrimination against protected classes.
        
    - **Disparate impact:** neutral policies with disproportionate negative effects on protected classes.
        
    - Affirmative action: measures to counter historical disadvantage.
        

## Algorithmic Fairness

- Algorithms make decisions with real impact (loans, bail, hiring, content ranking, recommendations).
    
- Fairness aims to ensure **no unjust discrimination** across individuals or groups.
    

## Group vs Individual Fairness

- **Group fairness:** outcomes should be comparable across protected vs non‑protected groups (e.g., parity of positive rates or error rates).
    
- **Individual fairness:** similar individuals should receive similar outcomes.
    

## Core Concepts

- **Protected attributes:** sensitive variables (e.g., race, gender) defining protected vs non‑protected groups.
    
- **Color‑blindness (fairness through unawareness):** ignoring protected attributes; often insufficient because proxies can reintroduce bias.
    
- **Thresholding in classification:** score thresholds determine positive/negative outcomes; group‑specific distributions can create unfairness.
    

## Fairness Criteria in Classification

- **Demographic parity (statistical parity):** equal positive outcome rates across groups.
    
- **Equalized odds:** equal true positive rates and false positive rates across groups.
    
- **Equal opportunity:** equal true positive rates (sensitivity/recall) across groups.
    
- Calibration and other consistency measures may conflict with parity constraints; trade‑offs are common.
    

## Fairness in Rankings

- **Exposure:** attention/opportunity (e.g., top positions) should be fairly distributed across groups.
    
- **Group parity in rankings:** group‑level exposure/visibility should align with targets (e.g., proportional to qualified items).
    

## Fairness in Recommendations

- Dual stakeholder view:
    
    - **Consumers:** avoid biased personalization that disadvantages certain users.
        
    - **Producers/items:** ensure fair exposure/opportunity for items from protected groups.
        
- Balance utility (relevance) with exposure parity to reduce systemic disadvantages.
    

## Fairness Interventions

- **Pre‑processing:** modify data to reduce bias (reweighting, debiasing features/labels).
    
- **In‑processing:** add constraints/regularizers during training to enforce fairness criteria.
    
- **Post‑processing:** adjust outputs/scores/thresholds after training to satisfy fairness (e.g., group‑specific thresholds, ranking exposure correction).
    
    - Dedicated post‑processing exists to improve group parity in rankings (e.g., re‑ranking with exposure constraints).
        

## Continuous Protected Attributes and Gerrymandering

- Protected attributes may be continuous (e.g., age, income).
    
- **Gerrymandering:** satisfying fairness for coarse groups while creating many subgroups with unfair outcomes; guard against by auditing multiple slices and continuous ranges.
    

## Accountability, Transparency, and Governance

- **Transparency:** document data sources, modeling choices, limitations, and fairness goals.
    
- **Accountability:** assign ownership for fairness monitoring, incident response, and remediation.
    
- **Human‑in‑the‑loop:** preserve oversight and recourse for affected individuals.
    

## Practical Workflow Checklist

1. **Define fairness goals:** pick criteria (e.g., equalized odds, exposure parity) aligned with context and legal constraints.
    
2. **Identify protected attributes:** direct or proxy; consider continuous variants and multiple slices.
    
3. **Measure baseline disparities:** compare outcome rates, error rates, calibration, exposure across groups.
    
4. **Choose interventions:** pre‑, in‑, post‑processing; for rankings/recs include exposure‑aware methods.
    
5. **Evaluate trade‑offs:** utility (accuracy, relevance) vs fairness; monitor stability and calibration.
    
6. **Monitor and audit:** ongoing slice‑based audits; watch for gerrymandering and drift over time.
    
7. **Document and govern:** transparency reports, accountability roles, incident remediation pathways.
    

## Key Takeaways

- Fairness spans ethical, legal, and technical dimensions; no single metric suffices in all contexts.
    
- Group and individual fairness can conflict; selecting the right criterion is application‑specific.
    
- Effective fairness requires continuous measurement, targeted interventions, and governance.