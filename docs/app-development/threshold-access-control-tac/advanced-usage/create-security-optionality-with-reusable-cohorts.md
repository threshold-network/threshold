# Create Security Optionality With Reusable Cohorts

Providing configurable security to end-users is easy with _Reusable Cohorts_. Below we define thre cohorts; small, medium, and large.&#x20;

`smallCohort`, `mediumCohort`, and `largeCohort` can then be reused by multiple Strategies.

```javascript
const smallConfig = {
  threshold: 3,
  shares: 5,
  porterUri: 'https://porter.nucypher.community',
};
const mediumConfig = {
  threshold: 11,
  shares: 20,
  porterUri: 'https://porter.nucypher.community',
};
const largeConfig = {
  threshold: 51,
  shares: 100,
  porterUri: 'https://porter.nucypher.community',
};
const smallCohort = await Cohort.create(smallConfig);
const mediumCohort = await Cohort.create(mediumConfig);
const largeCohort = await Cohort.create(largeConfig);
```