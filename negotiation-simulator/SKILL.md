---
name: negotiation-simulator
description: >
  Simulate multi-round negotiations between a buyer and a seller bot,
  each with configurable objectives, personalities, and strategies.
  Test which prompts and tactics close the best deals.
---

# Negotiation Simulator

Run automated buyer-vs-seller negotiation rounds to evaluate which
prompting strategies yield better outcomes. Based on
[evmckinney9/negotiator-bots](https://github.com/evmckinney9/negotiator-bots),
a framework for pitting LLM-powered buying and selling bots against each
other in deal-making simulations.

## Provenance

- **Skill URL**: https://github.com/evmckinney9/negotiator-bots
- **Source URL**: https://github.com/evmckinney9/negotiator-bots
- **Original author**: evmckinney9
- **Language**: English

## User Job

You want to see how two AI negotiators perform against each other so you
can refine your own negotiation prompts, strategies, or personality
settings before using them in a real scenario.

## Inputs

The user provides a **negotiation scenario** as a message or an uploaded file:

| Field | Required | Description |
|---|---|---|
| `item` | yes | The good or service being negotiated (e.g. "berries", "SaaS contract", "used car") |
| `buyer_personality` | no | Buyer disposition: aggressive, cooperative, analytical, etc. Default: aggressive |
| `buyer_strategy` | no | Buyer tactic: bargaining, anchoring, BATNA-based, etc. Default: bargaining |
| `seller_personality` | no | Seller disposition: timid, firm, friendly, etc. Default: timid |
| `seller_strategy` | no | Seller tactic: flattery, value-framing, bundling, etc. Default: flattery |
| `rounds` | no | Number of back-and-forth exchanges. Default: 5 |
| `buyer_target_price` | no | Buyer's ideal price point |
| `seller_target_price` | no | Seller's ideal price point |

If the user uploads a file, treat it as a scenario brief containing the
item description, context, and any constraints for both sides.

## Workflow

1. **Parse the scenario.** Extract the item, personalities, strategies,
   target prices, and round count from the user's message or uploaded file.

2. **Construct buyer prompt.** Format:
   > You are a negotiator. Your objective is to negotiate the price of
   > {item} you are purchasing. You are {buyer_personality}. You are
   > using the {buyer_strategy} strategy. As the buying bot, begin by
   > presenting your offer which will be given to the seller bot.

3. **Construct seller prompt.** Format:
   > You are a negotiator. Your objective is to negotiate the price of
   > {item} you are selling. You are {seller_personality}. You are using
   > the {seller_strategy} strategy. As the selling bot, begin by
   > waiting to receive the first offer.

4. **Run the negotiation.** Alternate messages between buyer and seller
   for the specified number of rounds. Each side sees only the other
   side's last response (not its own previous output). Maintain separate
   conversation histories per agent to prevent role confusion. The buyer
   always opens with the first offer.

5. **Produce the outcome report.** After all rounds, summarize:
   - Final agreed price (or note if no deal was reached)
   - Number of rounds to agreement
   - Key turning points in the negotiation
   - Which strategies and personality traits appeared most effective
   - Suggested prompt tweaks for better outcomes

## Output Contract

Return a structured summary:

```
## Negotiation Result

**Item**: {item}
**Rounds**: {rounds_used} / {rounds_max}
**Final Price**: {price} (or "No deal reached")

### Transcript
{Round-by-round exchange with BUYER: and SELLER: labels}

### Analysis
- Turning points: ...
- Strategy effectiveness: ...
- Suggested improvements: ...
```

## Tips

- Try varying one parameter at a time (e.g. change only the buyer's
  strategy) to isolate what drives better deals.
- Upload a detailed scenario brief for complex negotiations (real estate,
  B2B contracts) to get more realistic simulations.
- Compare aggressive vs. cooperative personalities to see which closes
  faster vs. which gets a better price.
- Watch for price anchoring effects by varying the opening offer.
