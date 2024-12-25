+++
title = "OpenAI o3: Carbon Footprint"
date = "2024-12-23T13:12:20+01:00"

tags = []
+++

OpenAI recently [previewed](https://openai.com/12-days/) **o3**, the most powerful large language model up to date.

While the performance capabilities (and their implications) are understandably getting most of the attention, what stood out to me was the cost of running the benchmarks. To evaluate the model, OpenAI partnered with the ARC Prize Foundation to assess its ability to efficiently acquire and apply new skills on the fly, rather than relying solely on recalling knowledge from its training data - a common limitation in today’s model benchmarking.

Using the data published in the [benchmark results](https://arcprize.org/blog/oai-o3-pub-breakthrough) we can estimate the energy consumed by running the model on a task and, therefore, infer the carbon footprint of a single run.


### TL;DR
**O3's single high-performance run: ~635 kg of CO₂. That's akin to driving 6,000 km in a new car, or one European's annual household energy usage.**

---

*EDIT 25 Dec: It appears that Boris Gamazaychikov has [explored](https://www.linkedin.com/posts/bgamazay_openai-has-announced-o3-which-appears-to-activity-7276250095019335680-sVbW/) something similar. While we have reached comparable conclusions, my approach takes a deeper dive into the details and methodology behind estimating the footprint.*

---

## Computing the footprint

### Variables
* Assuming compute leveraging SoTA nVidia H100 GPUs
    * Power draw per GPU: 700W, 90% TDP [^2]
    * Data center GPU lifespan: 2 years [^3]
    * Purchase cost per unit: $25,000 [^4]
* Data center PUE: 1.18 (Microsoft, Arizona) [^5]
* Data center electricity costs: $0.0733 per kWh [^6]
* Emission factor: 0.37kg CO~2~ per kWh [^7]


### OpenAI's GPU hour cost estimate

As part of the partnership with OpenAI, we can assume Microsoft provides the GPU infrastructure rather than OpenAI needing to lease it from a third party. Thus, we should compare the cost of purchasing and operating an H100 from the datacenter provider's perspective, not the consumer's.

Assuming 90% TDP, let's first compute H100's power draw adjusted for PUE:

\[
0.7 \text{ kW} \times 0.9 \times 1.18 \approx 0.7434 \text{ kW}
\]

With costs estimated at $0.0733 per kWh, the price for electricity to run the GPU for one hour is roughly $0.055.

To amortize the $25k capital expense over the GPU's 2-year lifespan (17,520 hours, assuming continuous use), the hourly hardware cost would be:

\[
\text{Hardware cost per hour}
= \frac{\$25000}{17520 \text{ hours}}
\approx \$1.43 \text{ per hour}
\]

Now we can conclude the estimated price per GPU hour is $1.49.

\[
\$0.055 + \$1.43
\approx \$1.49 \text{ per hour}
\]

The actual infrastructure cost might be slightly lower if the provider optimizes by running tasks in regions with cheaper electricity or lower cooling costs. That said, this estimate is entirely reasonable when compared to commercial GPU/hour rates.

### Converting the available information to GPU Hours

The [ARC Prize report](https://arcprize.org/blog/oai-o3-pub-breakthrough) on perfomance does not directly mention the cost of running the high-compute model:

> Note: o3 high-compute costs not available as pricing and feature availability is still TBD. The amount of compute was roughly 172x the low-compute configuration.

Given that the low--ompute model runs at $20 per task, we can multiply by 172 to reflect the high-compute variant, arriving at an estimated **$3,440 per task**.

\[
\text{GPU hours}
= \frac{\text{Task cost}}{\text{Cost per GPU-hour}}
= \frac{3440}{1.49}
\approx 2308.72 \,\text{hours},
\]

i.e. ARC Prize benchmark task required approximately **2,308.72 hours** of H100 GPU time.

### Power consumed by running H100 for 2,308 hours

Including data center overhead and 90% TDP this adds up to approximately:

\[
0.7434 \,\text{kW}
\times 2308.72 \,\text{h}
= 1716.30 \,\text{kWh},
\]

So the total electricity consumption at the data‑center level is about **1,716 kWh per task** for the high-performance deployment.

### CO~2~ emissions

With an emission factor of 0.37kg CO~2~/kWh:

\[
1716 \,\text{kWh}
\times 0.37 \,\text{kg $\text{CO}_2$/kWh}
\approx 635 \,\text{kg $\text{CO}_2$}.
\]

Rounding a bit, **one task produces about 635kg of CO~2~**.

## Real‑World Comparisons

We can now see energy inference requirements create substantial environmental impacts. Below, we compare the carbon footprint of an o3 run to everyday activities to help put the visualize the scale. 

A task run is akin to:
1. **Running a new car for almost 6,000 kilometers**[^8];
2. **108% of average European's annual home electricity consumption**[^9];
3. **Flying from London to Miami**[^10];
4. **The amount 30 mature trees can capture over a year**[^11].

Training also remains a major source of carbon emissions for advanced AI systems, and its impact should be not neglected. However, the closed nature of the model's training process makes it hard to estimate its carbon footprint.

## Conclusion
This clearly demonstrates the emerging issue of environmental impact of running state-of-the-art LLMs. It is worth mentioning that the estimates here only focus on inference GPU footprint, the overall emissions produced per inference task will be higher as running a model involves other hardware components, overhead from software and personnel, or manufacturing, transportation, and disposal of hardware. Mitigating these impacts can involve a combination several different strategies: optimizing model architectures, hardware efficiency, leveraging renewable energy or tighter regulation on model lifecycle process.

Once o3 reaches general availability, it is reasonable to expect that the costs of running the model (and, by extension, the associated CO~2~ emissions) will be passed on to users. Given the costs now, it is expected that the model will be drastically optimized in order to reduce the costs of running inference. When released in March 2023, the cost of inference on GPT-4 was $60 per 1 million output tokens and $30 per 1 million input tokens. Today, GPT-4o mini, which surpasses GPT-4 in performance benchmarks, costs $0.15 per 1 million input tokens and $0.075 per 1 million input tokens, making it 400 times cheaper.

As large language models become more ubiquitous, both AI developers and users must remain mindful of their environmental cost.

[^2]: [Max Thermal Design Power (TDP): Up to 700W](https://www.nvidia.com/en-us/data-center/h100/)
[^3]: [Datacenter GPU service life can be surprisingly short — only one to three years is expected](https://www.tomshardware.com/pc-components/gpus/datacenter-gpu-service-life-can-be-surprisingly-short-only-one-to-three-years-is-expected-according-to-unnamed-google-architect)
[^4]: [](https://docs.jarvislabs.ai/blog/h100-price)
[^5]: [Microsoft's Sustainability Targets: PUE, Arizona](https://datacenters.microsoft.com/sustainability/efficiency/)
[^6]: [The U.S. average rate is $.0733/kwh ...](https://info.siteselectiongroup.com/blog/power-in-the-data-center-and-its-costs-across-the-united-states)
[^7]: [U.S. net generation resulted in ... 0.81 pounds of CO~2~ emissions per kWh.](https://www.eia.gov/tools/faqs/faq.php?id=74&t=11)
[^8]: [EU CO~2~ emissions from new passenger cars is approximately 108 grams of CO~2~ per kilometer.](https://www.eea.europa.eu/en/newsroom/news/average-emissions-from-new-cars-and-vans)
[^9]: [Electricity consumption per capita in the household sector in the EU in 2022 was 1 584 kWh per capita](https://ec.europa.eu/eurostat/statistics-explained/index.php?title=Electricity_and_heat_statistics)
[^10]: [Flying from Miami to London Heathrow and back generates about 1,373 kg CO~2~](https://www.theguardian.com/environment/ng-interactive/2019/jul/19/carbon-calculator-how-taking-one-flight-emits-as-much-as-many-people-do-in-a-year)
[^11]: [In one year, a mature live tree can absorb more than 48 pounds of carbon dioxide](https://www.fs.usda.gov/about-agency/features/trees-are-climate-change-carbon-storage-heroe)