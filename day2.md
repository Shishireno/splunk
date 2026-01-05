# Splunk Learning Journey – Day 2: Searching & Analysis

## Overview
This report documents **Day 2** of my Splunk learning journey. The objective of this session was to move from basic navigation into **practical searching, aggregation, and analysis using SPL (Search Processing Language)**. All work was conducted on a local Splunk Enterprise instance using the `_internal` index.

The focus was on developing **SOC-style analytical thinking**, not just executing commands.

---

## Environment
- Platform: Splunk Enterprise (Local instance)
- Access URL: http://127.0.0.1:8000
- Index Used: `_internal`
- Time Range: Last 24 hours

---

## Task 1: Sourcetype Volume Analysis

### SPL Query
    index=_internal | stats count by sourcetype | sort - count

### Purpose
To identify which sourcetypes generate the highest volume of log events. This establishes a baseline of normal activity and highlights dominant data sources.

### Observations
- `splunkd` generated the highest number of events, which is expected as it represents the core Splunk daemon.
- `splunkd_access` and `splunkd_ui_access` reflect active interaction with the Splunk web interface.
- Node and MongoDB-related sourcetypes represent internal supporting services.

---

## Task 2: Host Activity Analysis

### SPL Query
    index=_internal | stats count by host | sort - count

### Purpose
To determine which hosts are generating log events and whether activity is distributed or concentrated.

### Observations
- All events originate from a single host (`LAPTOP-EI909QSD`).
- This is expected in a standalone lab environment.
- In a production SOC environment, a single host dominating logs could indicate misconfiguration or abnormal behaviour.

---

## Task 3: Component Noise Analysis

### SPL Query
    index=_internal | stats count by component | sort - count

### Purpose
To identify which internal Splunk components generate the most log noise and distinguish normal background activity from anomalies.

### Observations
- `Metrics` and `PeriodicHealthReporter` generate the majority of events.
- These components are responsible for system health checks and internal metrics collection.
- No unexpected or suspicious components were identified.

---

## Task 4: Error Trend Analysis

### SPL Query
    index=_internal error | timechart count

### Purpose
To analyse how error events occur over time and determine whether errors appear steadily or in spikes.

### Observations
- Error events are low in volume.
- Errors occur sporadically rather than in sustained spikes.
- This behaviour indicates normal operational conditions rather than an active incident.

---

## Task 5: Error Source Identification

### SPL Query
    index=_internal error | stats count by sourcetype | sort - count

### Purpose
To determine which sourcetypes are responsible for generating error events.

### Observations
- Errors primarily originate from `splunkd_ui_access`, `node:sidecar`, and `node:supervisor`.
- Very few errors originate from `splunkd`, indicating stable core services.

---

## Analyst Reflection

Day 2 marked a transition from simply running searches to **interpreting log data in context**. The exercises reinforced the importance of:

- Converting raw log events into meaningful summaries using `stats`
- Establishing baselines to understand what “normal” looks like
- Using time-based analysis to identify potential anomalies
- Treating Splunk queries as investigative questions rather than isolated commands

These skills directly align with **SOC Tier 1 responsibilities**, including log triage, baseline analysis, and early-stage incident investigation.

---

Author: Shishir Rai  
Learning Track: Splunk + Cybersecurity Skills Development  
Day: 2
