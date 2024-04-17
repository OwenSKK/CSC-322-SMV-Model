# CSC-322-SMV-Model-The-Dining-Philosipher

Halle Koyanagi V00913278

Wenzhe Shi V00968344

Vyom Shah V00951024

The Dining Philosopher is a common thought problem illustrating algorithm design for resource allocation, synchronization, and concurrency. Classically, this problem refers to forks and spaghetti with regards to an eating philosopher. In this project for clarity, we will refer to these objects as chopsticks and rice instead. Following, we outline two SMV source files that solve this problem – one sequential and one concurrent.

## Execution

The two files can be executed using NuSMV as follows:

```smv
NuSMV phil.smv
NuSMV phil.extended.smv
```

both output the passed SPECs to stdout

## Basic Task

The SMV source file called phil.smv is originally obtained as a prewritten faulty solution to be modified to assert and satisfy safety and liveness properties and to include fairness constraints. This solution will be sequential.

### Fairness

Two fairness constraints are added into the model:

```
FAIRNESS running;
FAIRNESS state=hungry;
```

The first constraint gets every philosopher process scheduled evenly. Without it, there can be only the main process being scheduled, leaving all philosophers in hunger. The second constraint prevents philosophers from thinking forever and let them get hungry infinitely often.

If we added a fairness constraint `FAIRNESS state=eating;`, the problem would be solved immediately, as all paths where philosophers cannot be eating infinitely often are eliminated, which the email points out that we should not do.

### Safety and Liveness Specification

The following SPECs are added to the model to satisfy safety and liveness properties

```
SPEC AG ((state = eating) -> gotleft & gotright)
SPEC AG ((state = hungry) -> AF (state = eating))
```

The safety SPEC asserts that a philosopher must have both chopsticks to eat. The liveness SPEC asserts that if a philosopher is hungry, they must eventually eat.

### Token

The basic solution creates a token passing along in philosophers. If a philosopher is thinking and a token is passed to him, he just passes the token to the next philosopher. A philosopher will only pick up chopsticks if he is hungry and holding the token. After he finishes eating, he will then pass the token to the next philosopher.

## Extended Task

The SMV source file called phil.extended.smv contains the concurrent solution to the dining philosophers problem.

### Concurrency Specifications

In addition to the SPECs from phil.smv, more SPECs were added to assert concurrent eating among philosophers. The following SPECs assert no strict sequencing for philosopher 0, ensuring philosopher 0 (p0) can eat before philosophers 1, 2, 3, and 4.

```
SPEC EF (p0.state = eating & E [ p0.state = eating U (!(p0.state = eating) & E [ !(p1.state = eating) U p0.state = eating ])]) 
SPEC EF (p0.state = eating & E [ p0.state = eating U (!(p0.state = eating) & E [ !(p2.state = eating) U p0.state = eating ])]) 
SPEC EF (p0.state = eating & E [ p0.state = eating U (!(p0.state = eating) & E [ !(p3.state = eating) U p0.state = eating ])]) 
SPEC EF (p0.state = eating & E [ p0.state = eating U (!(p0.state = eating) & E [ !(p4.state = eating) U p0.state = eating ])])
```

The same SPECs were added for each philosopher.
As well, SPECs were added to assert concurrent eating among philosophers. The following asserts that philosophers who are not sitting beside each other must at some point eat at the same time.

```
SPEC EF ((p0.state=eating)&(p2.state=eating))
SPEC EF ((p0.state=eating)&(p3.state=eating))
SPEC EF ((p1.state=eating)&(p3.state=eating))
SPEC EF ((p1.state=eating)&(p4.state=eating))
SPEC EF ((p2.state=eating)&(p4.state=eating))
```

### Modified Token

The token solution for this problem is having the philosophers in positions two left or two right of the token also eat. This is referenced using (i+3) mod 5 and (i+2) mod 5 where i is the current position of the token. The philosophers will then only pick up chopsticks if they are in this positon