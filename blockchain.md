```py
class Blockchain(object):
    # Define a new class named Blockchain.
    # Classes are blueprints for creating objects with properties and methods.
    
    def __init__(self):
        # Define a special method called __init__ which serves as a constructor.
        # This method is automatically called when a new object of this class is created.
        
        # Initialize an empty list to hold the blocks of the blockchain.
        self.chain = []
        
        # Initialize an empty list to store transactions that have not yet been added to a block.
        self.current_transactions = []

    @staticmethod
    def hash(block):
        # A static method that hashes a block.
        # Static methods are bound to the class and don't depend on specific instances.
        # This method is used to generate a cryptographic hash for a given block.
        pass
    
    @property
    def last_block(self):
        # A property method that returns the last Block in the chain.
        # This method allows access to the last block without directly exposing the chain attribute.
        pass




# block = {
#     'index': 1,
#     'timestamp': 1506057125.900785,
#     'transactions': [
#         {
#             'sender': "8527147fe1f5426f9dd545de4b27ee00",
#             'recipient': "a77f5cdfa2934df3954a5c7c7da5df1f",
#             'amount': 5,
#         }
#     ],
#     'proof': 324984774000,
#     'previous_hash': "2cf24dba5fb0a30e26e83b2ac5b9e29e1b161e5c1fa7425e73043362938b9824"
# }

```

 Each new block contains within itself, the hash of the previous Block.
This is crucial because it is what gives blockchains immutability: If an attacker corrupted an earlier Block in the chain then all subsequent blcoks will contain incorrect hashes,


```

# Adding Transactions to a Block

    def new_transaction(self, sender, recipient, amount):
        """
        Create a new transaction to go into next minded Block
        :param sender: <str> Address of Sender
        :param recipient: <str> Address of the Recipient
        :param amount: <int> Amount
        :return <int> The index of the Block that will hold this transaction

        self.current_transactions.append(
            {
                'sender': sender,
                'recipient': recipient,
                'amount': amount,
            }
        )

        return self.last_block['index'] + 1
        
```
After new_transaction() adds a transaction to the list, it returns the index of the block which the transaction will be added to—the next one to be mined. 
This  will be useful later on, to the user submitting the transaction.

Creating new Blocks

When our Blockchain is instantiated we’ll need to seed it with a genesis block—a block with no predecessors. We’ll also need to add a “proof” to our genesis block which is the result of mining (or proof of work). We’ll talk more about mining later.

In addition to creating the genesis block in our constructor, we’ll also flesh out the methods for
new_block(), new_transaction() and hash().


When a new block is created, the self.current_transactions list contains all the transactions that have been created since the last block was mined. These 
transactions are added to the new block's transactions field. Once the new block is added to the blockchain, the self.current_transactions list is cleared to
allow for new transactions to be collected for the next block. This ensures that each transaction is only included in one block and prevents duplication.

```
import hashlib
import json
from time import time

class Blockchain(object):
    def __init__(self):
        # Initialize the blockchain and the current list of transactions
        self.current_transactions = []  # This will hold transactions until they are added to a block
        self.chain = []  # This will store the chain of blocks

        # Create the genesis block (the first block in the blockchain)
        self.new_block(previous_hash=1, proof=100)

    def new_block(self, proof, previous_hash=None):
        """
        Create a new Block in the Blockchain
        :param proof: <int> The proof given by the Proof of Work algorithm
        :param previous_hash: (Optional) <str> Hash of previous Block
        :return: <dict> New Block
        """
        
        # Create a new block with the necessary fields
        block = {
            'index': len(self.chain) + 1,  # The index of the new block (next block in the chain)
            'timestamp': time(),  # Current timestamp when the block is created
            'transactions': self.current_transactions,  # List of transactions to be included in the block
            'proof': proof,  # The proof of work number (nonce)
            'previous_hash': previous_hash or self.hash(self.chain[-1]),  # Hash of the previous block or a default value
        }

        # Reset the current list of transactions since they are now added to the new block
        self.current_transactions = []

        # Append the new block to the chain
        self.chain.append(block)
        return block

    def new_transaction(self, sender, recipient, amount):
        """
        Creates a new transaction to go into the next mined Block
        :param sender: <str> Address of the Sender
        :param recipient: <str> Address of the Recipient
        :param amount: <int> Amount
        :return: <int> The index of the Block that will hold this transaction
        """
        
        # Append a new transaction to the current list of transactions
        self.current_transactions.append({
            'sender': sender,  # The sender's address
            'recipient': recipient,  # The recipient's address
            'amount': amount,  # The amount to be transferred
        })

        # Return the index of the next block to which the transaction will be added
        return self.last_block['index'] + 1

    @property
    def last_block(self):
        # Return the last block in the chain
        return self.chain[-1]

    @staticmethod
    def hash(block):
        """
        Creates a SHA-256 hash of a Block
        :param block: <dict> Block
        :return: <str> The block's hash
        """
        
        # Convert the block into a string and then encode it to bytes
        block_string = json.dumps(block, sort_keys=True).encode()
        # Return the SHA-256 hash of the encoded block string
        return hashlib.sha256(block_string).hexdigest()

```

Understanding Proof of Work

A Proof of Work algorithm (PoW) is how new Blocks are created or mined on the blockchain. The goal of PoW is to discover a number which solves a problem. The number must be difficult to find but easy to verify—computationally speaking—by anyone on the network. This is the core idea behind Proof of Work.

```

# We’ll look at a very simple example to help this sink in.

# Let’s decide that the hash of some integer x multiplied by another y must end in 0. So,

hash(x * y) = ac23dc...0

# And for this simplified example, let’s fix

x = 5
```

 Implementing this in Python:

```

from hashlib import sha256
x = 5
y = 0  # We don't know what y should be yet...
while sha256(f'{x*y}'.encode()).hexdigest()[-1] != "0":
    y += 1
print(f'The solution is y = {y}')

```

The solution here is
y = 21
Since, the produced hash ends in 0: hash(5 * 21) = 1253e9373e...5e3600155e860

In Bitcoin, the Proof of Work algorithm is called Hashcash. And it’s not too different from our basic example above. It’s the algorithm that miners race to solve in order to create a new block. In general, the difficulty is determined by the number of characters searched for in a string. The miners are then rewarded for their solution by receiving a coin—in a transaction.

The network is able to easily verify their solution.


Implementing basic Proof of Work

Let’s implement a similar algorithm for our blockchain. Our rule will be similar to the example above:

Find a number p that when hashed with the previous block’s solution a hash with 4 leading 0s is produced.






```
import hashlib
import json

from time import time
from uuid import uuid4


class Blockchain(object):
    ...
        
    def proof_of_work(self, last_proof):
        """
        Simple Proof of Work Algorithm:
         - Find a number p' such that hash(pp') contains leading 4 zeroes, where p is the previous p'
         - p is the previous proof, and p' is the new proof
        :param last_proof: <int>
        :return: <int>
        """

        proof = 0
        while self.valid_proof(last_proof, proof) is False:
            proof += 1

        return proof

    @staticmethod
    def valid_proof(last_proof, proof):
        """
        Validates the Proof: Does hash(last_proof, proof) contain 4 leading zeroes?
        :param last_proof: <int> Previous Proof
        :param proof: <int> Current Proof
        :return: <bool> True if correct, False if not.
        """

        guess = f'{last_proof}{proof}'.encode()
        guess_hash = hashlib.sha256(guess).hexdigest()
        return guess_hash[:4] == "0000"
```
