+++
title = "OpenAI o3: Carbon Footprint"
date = "2024-12-23T13:12:20+01:00"

tags = []
+++

OpenAI recently [previewed](https://openai.com/12-days/) **o3**, the most powerful large language model up to date.

While the performance capabilities (and their implications) are understandably getting most of the attention, what stood out to me was the cost of running the benchmarks. To evaluate the model, OpenAI partnered with the ARC Prize Foundation to test its ability to learn and apply new skills on the fly, rather than recalling knowledge from its training data - a common issue in today's model benchmarking.

Using the data published in the [benchmark results](https://arcprize.org/blog/oai-o3-pub-breakthrough) we can estimate the energy consumed by running the model on a task and, therefore, infer the carbon footprint of a single run.


### TL;DR
**Running a task on the o3 model emits as much CO~2~ as driving a car for thousands of kilometers.**

---

## Computing the footprint

### Variables
* H100 power draw: 700W (assuming 100% TDP) [^2]
* GPU usage rate: $1.50 per H100 GPU/hr (low end assumed) [^3]
* Data center PUE: 1.18 (Microsoft, Arizona) [^4]
* Emission factor: 0.37kg CO~2~/kWh [^5]


### Converting the available information to GPU Hours

ARC Prize report[^1] on perfomance does not directly mention the cost of running the high-compute model:

> o3 high-compute costs not available as pricing and feature availability is still TBD. The amount of compute was roughly 172x the low-compute configuration.

However, since we know the "efficient" model took $20 per task, high-performance model draws 172 times of that, $3.440.

\[
\text{GPU hours}
= \frac{\text{Task cost}}{\text{Cost per GPU-hour}}
= \frac{3440}{1.50}
\approx 2293.33 \,\text{hours},
\]

i.e. ARC Prize benchmark task required approximately 2293.33 hours of H100 GPU time.

### Power consumed by running H100 for 2293.33 hours

\[
0.70 \,\text{kW}
\times 2293.33 \,\text{h}
= 1605.33 \,\text{kWh},
\]

including data center overhead this adds up to approximately:

\[
1605.33 \,\text{kWh}
\times 1.18
\approx 1795 \,\text{kWh}.
\]

So the total electricity consumption at the data‑center level is about 1795 kWh per task for the high-performance deployment.

### CO~2~ emissions

With an emission factor of 0.37kg CO~2~/kWh:

\[
1795 \,\text{kWh}
\times 0.37 \,\text{kg $\text{CO}_2$/kWh}
\approx 665 \,\text{kg $\text{CO}_2$}.
\]

Rounding a bit, **one task produces about 665kg of CO~2~**.

## Real‑World Comparisons

To put 665kg of CO~2~ into perspective, here are some everyday carbon release examples:

1. **Running a new car for over than 6 000 kilometers**[^6];
2. **42% of average European's home electricity consumption**[^7];
3. **Flying from London to Miami**[^8];
4. **The amount 30 mature trees can capture over a year**[^9].

## Discussion
This clearly demonstrates the emerging issue of environmental impact of running state-of-the-art LLMs. It is worth mentioning that the estimates here only focus on inference GPU footprint, the overall emissions produced per inference task will be higher as running a model involves other hardware components, overhead from software and personnel, or manufacturing, transportation, and disposal of hardware.

Once o3 reaches general availability, it is reasonable to expect that the costs of running the model (and, by extension, the associated CO~2~ emissions) will be passed on to users. Given the costs now, it is expected that the model will be drastically optimized in order to reduce the costs of running inference. When released in March 2023, the cost of inference on GPT-4 was $60 per 1 million output tokens and $30 per 1 million input tokens. Today, GPT-4o mini, which surpasses GPT-4 in performance benchmarks, costs $0.15 per 1 million input tokens and $0.075 per 1 million input tokens, making it 400 times cheaper.

[^1]: [OpenAI o3 ARC-AGI Results](https://arcprize.org/blog/oai-o3-pub-breakthrough)
[^2]: [Max Thermal Design Power (TDP): Up to 700W](https://www.nvidia.com/en-us/data-center/h100/)
[^3]: [$1,080/month per one VM with 1 × H100 GPU](https://nebius.com/explorer-tier)
[^4]: [Microsoft's Sustainability Targets: PUE, Arizona](https://datacenters.microsoft.com/sustainability/efficiency/)
[^5]: [U.S. net generation resulted in ... 0.81 pounds of CO~2~ emissions per kWh.](https://www.eia.gov/tools/faqs/faq.php?id=74&t=11)
[^6]: [EU CO~2~ emissions from new passenger cars is approximately 108 grams of CO~2~ per kilometer.](https://www.eea.europa.eu/en/newsroom/news/average-emissions-from-new-cars-and-vans)
[^7]: [Electricity consumption per capita in the household sector in the EU in 2022 was 1 584 kWh per capita](https://ec.europa.eu/eurostat/statistics-explained/index.php?title=Electricity_and_heat_statistics)
[^8]: [Flying from Miami to London Heathrow and back generates about 1,373 kg CO~2~](https://www.theguardian.com/environment/ng-interactive/2019/jul/19/carbon-calculator-how-taking-one-flight-emits-as-much-as-many-people-do-in-a-year)
[^9]: [In one year, a mature live tree can absorb more than 48 pounds of carbon dioxide](https://www.fs.usda.gov/about-agency/features/trees-are-climate-change-carbon-storage-heroe)