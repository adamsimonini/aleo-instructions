[Aleo language instructions](https://developer.aleo.org/guides/aleo/language/)

## Commands


### Local Network Setup

For local development and testing:

```bash
cd snarkOS
./devnet.sh
```

### Deployment

Deploy to local network:

```bash
leo deploy --endpoint https://localhost:3030 --broadcast
```

Execute functions on local network:

```bash
leo execute --endpoint https://localhost:3030 --broadcast <function_name> <args>
```

```bash
leo build
```

<!-- SNARKOS -->
### SnarkOS
https://github.com/ProvableHQ/snarkOS