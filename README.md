# CSC-322-SMV-Model

Halle Koyanagi V00913278

## Basic Part

### Fairness

Two fairness constraints are added into the model:

```
FAIRNESS running;
FAIRNESS state=hungry;
```

The first constraint gets every philosopher process scheduled evenly. Without it, there can be only the main process being scheduled, leaving all philosophers in hunger. The second constraint prevents philosophers from thinking forever and let them get hungry infinitely often.

If we added a fairness constraint `FAIRNESS state=eating;`, the problem would be solved immediately, as all paths where philosophers cannot be eating infinitely often are eliminated, which the email points out that we should not do.

### Token

The basic solution creates a token passing along in philosophers. If a philosopher is thinking and a token is passed to him, he just passes the token to the next philosopher. A philosopher will only pick up chopsticks if he is hungry and holding the token. After he finishes eating, he will then pass the token to the next philosopher.
