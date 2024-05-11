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



# Each new block contains within itself, the hash of the previous Block.
# This is crucial because it is what gives blockchains immutability: If an attacker corrupted an earlier Block in the chain then all subsequent blcoks will contain incorrect hashes,





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