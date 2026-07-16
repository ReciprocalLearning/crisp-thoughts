# Beyond Infinity: Why Your Cloud Strategy Needs a "Second-Source" VM SKU Catalog
Author: Manish Vyas

## Introduction

I have had the privilege of being associated with cloud computing since its early days, watching public cloud providers grow, mature, and evolve over time. I have seen the transition from simple virtualized hosting to massive, global multi-cloud ecosystems.

Throughout this evolution, we have all been sold a beautiful, comforting lie: **that the cloud is infinite**.

For a long time, that lie felt close enough to the truth. But like any other physical supply chain, cloud providers are not exempt from hardware bottlenecks, resource constraints, and physical limitations. Over the past couple of years, datacenters globally have run headfirst into massive capacity walls as providers struggle to build out physical space in proportion to staggering enterprise demands.

This capacity crunch has only intensified with the massive wave of AI adoption. Hyperscale focus has unevenly shifted toward supporting AI-driven markets, diverting hardware and energy resources, all while trying to address existing capacity constraints in standard compute. New datacenters are constantly in the making, but physical supply chains move much slower than digital demands.

---

## The Illusion of the Click-to-Scale Button

We are taught to believe that as long as our credit card is valid, we can scale to thousands of cores at the click of a button. But anyone running enterprise-scale workloads in public clouds knows the harsh reality. Between localized hardware shortages, physical datacenter power limits, and massive regional demand spikes, "infinite scale" frequently hits a wall known as the allocation failure.

Most cloud professionals are intimately familiar with these frustrating errors: `SkuNotAvailable` or `QuotaNotAvailable`.

You run your deployment pipeline, and instead of a green build, everything grinds to a sudden halt. When this happens, engineering teams scramble to find an alternative VM size, only to realize that nobody has tested the application on that alternate SKU. Suddenly, you are hit with a barrage of critical, unanswered questions:

* **Architecture & Compiling:** *Does our container compile and run on ARM?*

* **Microarchitecture Quirks:** *Will our high-throughput database choke on that AMD chipset's memory architecture?*

* **Regional Trade-offs:** *What happens to workload performance and SLAs if we are forced to choose another region?*

* **Policy & Compliance:** *We can't force our customers to choose another region—our strict data residency policies won't allow it. What now?*


---

## The "Single-Source" Compute Trap

It is time to stop treating compute capacity as a generic, guaranteed commodity. We need to shift our architectural mindset: we must treat VM SKUs as a core supply chain dependency and proactively introduce a **"Second Source" VM SKU Catalog**.

If you do not, you are stuck in the **Single-Source Compute Trap**.

Think of it this way: if Apple relied on only one supplier for a critical microchip, their global supply chain would collapse during a shortage. Yet, in the cloud, we do exactly that. We "single-source" our compute by hardcoding specific VM sizes into our infrastructure-as-code (IaC) templates, locking ourselves into a single chip family without even realizing it.

```text
Today's Brittle Pipeline:
[ Your Application ] ➔ Bound tightly to ➔ [ Standard_D8s_v5 (Intel) Only ]

```

### The Consequences of the Status Quo

* **The Single-Source Trap:** A single localized hardware shortage in your primary region completely blocks your deployments.


* **No Dynamic Fallbacks:** Deployment scripts fail instantly because there is no pre-tested, alternative SKU to fall back on dynamically.



To build true resilience, we must decouple the workload from a single physical SKU, treating the underlying chipsets—**Intel, AMD, and ARM**—as interchangeable raw materials.

---

## The Solution: A Two-Pronged Pipeline Approach

To escape the compute trap, organizations should adopt a modernized pipeline strategy built on two core principles:

### 1. Define a Workload Portability Score

Pre-test and tag your workloads based on how easily they can run across different silicon.

* **Gold (Fully Portable):** The workload can run seamlessly on Intel, AMD, and ARM with zero code changes. Build pipelines automatically compile and package multi-architecture container images.


* **Silver (x86 Only):** The workload runs on Intel or AMD but cannot run on ARM due to compiled binary dependencies, legacy libraries, or specialized driver requirements.


* **Red (SKU-Locked):** The workload is strictly bound to a specific SKU family or physical hardware layout (e.g., nested virtualization requirements or specific Nvidia GPU architectures).



### 2. Decouple Logical and Physical Compute

Stop defining your deployment pipelines by a single, hardcoded physical SKU. Instead, define your infrastructure requirements by a **"Compute Class"** that can dynamically evaluate and fall back to pre-tested alternative SKUs.

If your primary Intel SKU hits a capacity limit, your deployment scripts shouldn't fail—they should automatically and safely provision the secondary AMD or tertiary ARM SKU. **Your business keeps moving**.

---

## The Workload-to-SKU Compatibility Catalog

By auditing and classifying your workloads, you can construct an organizational catalog that acts as an insurance policy against cloud capacity failures:

| Workload ID | Primary SKU (Target) | Secondary SKU (Fallback 1) | Tertiary SKU (Fallback 2) | Portability Status | Blockers / Requirements |
| --- | --- | --- | --- | --- | --- |
| **App-01 (API Service)** | Azure `D8s_v5` (Intel) | Azure `D8as_v5` (AMD) | Azure `D8ps_v5` (ARM) | **Gold** (Fully Portable) | None. Multi-arch container builds automated in CI/CD.
| **DB-03 (High-I/O DB)** | Azure `E16s_v5` (Intel) | Azure `E16as_v5` (AMD) | *Unsupported*<br> | **Silver** (x86 Only) | Requires specific Intel/AMD memory bus speeds; no ARM build available.
| **ML-02 (Inference)** | Azure `NC6s_v3` (Nvidia) | *Unsupported*<br> | *Unsupported*<br> | **Red** (SKU Locked) | Hard-coded dependency on specific CUDA libraries.

---

## Conclusion: Compute is No Longer a Given

The cloud has matured, and with that maturity comes the reality of physical resource limitations. Designing for high availability is no longer just about deploying across multiple availability zones; it is about designing for **hardware flexibility**.

By building a Workload-to-VM SKU Catalog, you transform your infrastructure from a brittle, single-sourced chain into a highly resilient, adaptive grid. When the next capacity constraint hits, your workloads will have already failed over to an alternative chipset, serving customers without missing a single beat.

*In my next article, I will write a step-by-step technical guide on how to programmatically implement this VM SKU portability strategy in your IaC pipelines. Stay tuned, and happy deploying!*
