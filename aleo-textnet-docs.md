Local Testing with Network
To test with the network, developers need to set up a local development network (devnet), which provides an isolated environment to deploy programs and execute transactions that modify program states. With this local network running, developers can validate state transitions within their programs, ensuring that record creation and consumption are correctly verified according to program logic, thereby preventing issues such as double spending.

Here are the steps to start running a local devnet:

To run a local devnet with the script, start by installing tmux.
macOS
To install tmux on macOS, you can use the Homebrew package manager. If you haven't installed Homebrew yet, you can find instructions at their website.

# Once Homebrew is installed, run:
brew install tmux

Ubuntu
Windows
Clone snarkOS repository to your local machine if you haven't done so.
Run ./devnet.sh from the snarkOS repository that you just cloned.
tip
As an alternative to ./devnet.sh, you can also use the drop-in replacement amareleo-chain which provides similar functionality but lighter and faster for setting up a local devnet. Built by the Aleo community.

When asked for the total number of validators and clients, press enter to use default values.
Pick a network ID or press enter to use the default network.
When asked to run build the binary, enter y if this is the first time running, or n to skip if this is not the first time and nothing has changed in the snarkOS repo.
When asked to clear the existing ledger history, enter y if you want to restart a clean environment, or n to continue running the last network environment.
Once the nodes are started, you can to switch to the next node in tmux with:
Ctrl+b n

or to the previous node with:

Ctrl+b p

To enable scrolling in tmux, run:
Ctrl+b [

tip
Press q to quit scrolling mode.

To kill session, press Ctrl+b and type :kill-session.

Look for the validator node 0 and take note of its private key that is shown during startup of the node. It should look something like below: TestnetValidator
The private key will always stay the same for every node that runs with devnet.sh script so it only needs to remember once.
The reason for taking note of validator node 0's private key is because it is automatically funded with test Aleo Credits (ACs) with both public mapping balance and private records.
To interact with the local devnet:
Replace all API calls URL with http://localhost:3030.
When using Leo CLI commands, you can set the endpoint in two ways:
a. Use the --endpoint flag for each command. For example:
leo deploy --endpoint http://localhost:3030 --broadcast
leo execute --endpoint http://localhost:3030 --broadcast

b. Set the endpoint in the .env file at the root of your project:
ENDPOINT=http://localhost:3030

This way, you don't need to specify the endpoint for each command.
Refer here for all the default available REST API endpoints from a validator node.

Once you have a local devnet running in the background, you can start deploying a program to the devnet using leo deploy --broadcast and execute transactions using leo execute --broadcast to start testing any logic in the async functions and correctness of the state transitions of your programs. If you haven't set the endpoint in the .env file, remember to include the --endpoint http://localhost:3030 --broadcast flags when interacting with your local devnet.

The --broadcast flag is crucial as it instructs the Leo CLI to send the deployment or execution transaction to the network, ensuring that your actions are actually performed on the devnet rather than just simulated locally, allowing you to test the full lifecycle of your program in a realistic environment.

Querying the Local Devnet
Once your local devnet is running, you can use various leo query commands to interact with and retrieve information from the network. To query the local devnet, ensure you're using the correct endpoint by either including the --endpoint http://localhost:3030 flag with your commands or setting ENDPOINT=http://localhost:3030 in your .env file.

The leo query command supports querying different types of data from your local network, including block information, transaction details, program data, and mapping values. These queries allow you to inspect the current state of your local network, verify transactions, and debug your Aleo programs effectively, providing valuable insights into the behavior and performance of your applications on the devnet.