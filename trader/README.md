# Trader

A basic Bitcoin trading bot example.

Let's try an example that uses data from a CSV file to train.  We will be training a model that uses historical Bitcoin prices to learn when to trade effectively.

First, ensure this `samples` repository is cloned.

```bash
git clone https://github.com/spiceai/samples.git
```

Move to the `trader` directory and start the Spice runtime.

```bash
cd samples
cd trader
spice run
```

In another terminal, add the Trader sample:

```bash
spice pod add samples/Trader
```

In the Spice runtime terminal, you will observe the runtime loading the CSV from `data/btcusd.csv` and starting to train!

Now, let's update the starting balances for the portfolio so the app has more capital to trade with.

*Change*

```yaml
fields:
    - name: usd_balance
    initializer: 10 # update with the starting balance to train with
    - name: btc_balance
    initializer: 0 # update with the starting balance to train with
```

*to*

```yaml
fields:
      - name: usd_balance
        initializer: 1000
      - name: btc_balance
        initializer: 10
```

Feel free to try whatever amounts you'd like!

Now, save the file.  You should observe a new training run begin in the Spice runtime. If you'd like to start training manually, use this command.

```bash
spice pod train trader
```

## Inference

Now try fetching a recommendation from the newly trained pod.

```bash
curl http://localhost:8000/api/v0.1/pods/trader/inference
```

You'll see a result you can take action on immediately:

```json
{
  "action": "buy",
  "confidence": 0.90,
  "end": "Mon Jul 19 12:34:00 2021",
  "start": "Mon Jul 19 12:29:00 2021",
  "tag": "latest"
}
```

## View Observation Data

Try fetching observation data with:

```bash
curl http://localhost:8000/api/v0.1/pods/trader/observations
```

## Integrate with your app

You can easily use your newly trained model in your app.  Spice AI works with any language using a simple REST API.  Here is a simple example that will fetch inferences for a Javascript app:

```js
const fetch = require("node-fetch");

console.log("Trader - A Spice app");

setInterval(async () => {
  let response = await fetch(
    "http://localhost:8000/api/v0.1/pods/trader/inference"
  );
  let data = await response.json();

  console.log(`${new Date().toISOString()}: ${data.action.toUpperCase()}!!!`);
}, 3000);
```