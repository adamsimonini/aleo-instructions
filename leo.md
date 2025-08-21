# LEO LANGUAGE EXPLAINED

### Links
https://docs.leo-lang.org/cli/overview

[Transition function](https://docs.leo-lang.org/language/programs#transition-function)
Transition functions in Leo are declared as transition {name}() {}. Transition functions can be called directly when running a Leo program (via leo run). Transition functions contain expressions and statements that can compute values. Transition functions must be in a program's current scope to be called. Transition functions that call async functions to execute code on-chain must be declared as async transition.

A transition normally runs on chain (though in Aloe's case it runs within Aleo's zkVM). It introduces a state change to on-chain data. It must follow the rules of the circuit, no randomness unless source is approved. 

Aleo doesn’t just “run” your transition like JavaScript runs a function — it compiles it into an arithmetic circuit that must be provable in zero knowledge. That means your code is transformed into a set of mathematical constraints, and everything you write has to be something the zkVM can represent and verify.

Can read and write program state (public or private records).
Generates a cryptographic proof that this state change is valid.
Side effects are recorded on the Aleo ledger after proof verification.

In JavaScript, your function is just code running on a CPU, and the CPU can handle whatever.
In Leo, your transition is a mathematical proof generator, so it’s constrained to what the zk-SNARK proving system can encode and verify. That’s what I meant by rules of the circuit.

In the following transition exmaple, `owner` and `amount` are primative values that aren't consumed by the transition, just like input parameters in "normal" functions. They do not persist past the transitions execution, and technically records don't either. However, the source of a primative input value can exist past the transition's execution.

`birdge_auth` and `total_supply` on the other hand are records, which are first-class objects in Aleo that are consumed by transitions.
```bash
    transition mint(
        public owner: address,
        public amount: u64,
        bridge_auth: BridgeAuth,
        total_supply: TotalSupply,
    ) -> (balance.aleo/Balance, TotalSupply) {
        # transition function logic 
    }
```

## Step-by-step reality of an Aleo transaction

Local execution & proof generation

You run the transition on your own machine.

Your machine computes the result and generates a zk-SNARK proof that says, “I followed the rules of the program, here’s the valid outcome” — without revealing the inputs, intermediate steps, or even (in some cases) the outputs.

Submitting the proof

You send this proof (and any public outputs) to the Aleo network.

Validators check the proof mathematically — they don’t re-run your code.

On-chain state update

If the proof is valid, the state change is applied: a record is created, a balance is updated, a token is transferred, etc.

### Privacy granularity

You can choose to keep private:

Inputs — e.g., the sender’s address, amount, or secret parameters.

Outputs — e.g., the receiver’s address or resulting balances.

Intermediate computations — the “how” of the transaction.

Metadata — sometimes even the type of transaction can be hidden if you design the program that way.

#### You cannot hide everything

The network still sees that a proof was submitted and that some program was executed.

If your program outputs any public state (like updating a public counter or minting a public token), that part is visible.

### Record
A record defines a type schema for a piece of sthat that could be stored on-chain. It is a self-contained `state object` on the blockchain that has an owner. It lives in Aleo's global state tree. When a record is consumed, it is spent or disolved, and that usually results in creating new records as outputs.

### Transaction
A transition in Aleo is the unit of execution that actually runs on-chain (or provably off-chain, then verified on-chain). Only within a transition are you producing or consuming records and generating a zero-knowledge proof that enforces correctness.

A transaction is a function that takes in records as inputs, applies some logic, and produces new records or public outputs. Each transiction muist be a self-contained state transformation - it's similar to ACID (atomic, consistant, isolated, and durable). But blockchains are not "rolled back" so cryptographic proofs enofce ACID before a validator even sees the transaction. 

Only records are consumed by transitions. If a transition is passed in primative inputs, they are not consumed.

### Assert
Assert encodes a constraint into the zero-knowledge proof circuit for that transition.

Assert takes a boolean condition, and either evaluates `true` or `false`. Assert is only meaningful within a transition. If true, execution of the transition continues. If false, the entire transition aborts, and the transaction fails.