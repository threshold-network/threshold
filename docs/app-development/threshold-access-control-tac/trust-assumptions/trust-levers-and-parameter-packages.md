# Trust levers & parameter packages

In future versions, adopting developers will be able to freely pull a range of 'trust levers' in order to satisfy and assuage the trust, risk, cost, redundancy, and latency preferences of their end-users. This includes tweaking, on the fly, some of the network parameters introduced in the [Trust Model Foundation](mainnet-trust-model-foundation.md) section, such as the _Cohort_ _threshold_, the _frequency of node replacement_, and the _minimum stake required to serve in a given Cohort._ \
\
However, correctly selecting individual parameters across each and every trust/risk dimension requires a deep understanding of the underlying mechanisms and cryptology. Moreover, these parameters must be combined into a coherent bundle that, as a whole, aligns with the risk aversion and trust-minimization desiderata of end-users. To avoid burdening developers with this onerous analysis, particularly for those new to the service, the Threshold team has constructed a set of pre-configured 'parameter packages'. In the genesis version of TACo (v.7.0), there are only two packages to choose from, and only one difference between them:\
\
**Package I** \
`m` : 16, `n` : 30 (16-of-30)&#x20;

**Package II** \
`m` : 8 `n` : 15 (8-of-15)&#x20;

Both parameter packages will be subject to identical Sampling-related and Population-related trust assumptions, but come with slightly different Cohort-related trust assumptions. \
\
