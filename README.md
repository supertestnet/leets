# Leets

Lightning escrowed electronic tokens. A system for issuing and transferring tokens using the lightning network and escrows.

Suppose an escrow maintains a publicly viewable database of signed transactions. The signed transactions involve pubkeys, token ids, and quantities. Users can issue tokens by signing and sending an issuance transaction to the escrow for publication in its database. An issuance transaction must list a pubkey and a statement in this form: "pubkey 1ef...50a received 100 tokens from an issuance transaction, the token id is 5d2...9a4." The recipient pubkey must sign issuance transactions.

Users can transfer tokens by signing and sending a transfer transaction to the escrow for publication in its database. A transfer transaction must list the sender's pubkey and a statement in this form: "pubkey 4a1...990 received 50 tokens from pubkey 1ef...50a, the token id is 5d2...9a4." The sender's pubkey must sign transfer transactions. Clients only see them as valid if the sender sent tokens they previously received either via issuance transactions or through other transfers.

To avoid getting sybilled, the escrow may charge a lightning fee for each transaction. Assuming the escrow doesn't censor transactions but rather adds them to its publicly viewable database, all transactions can be publicly validated by all clients. Moreover, if the escrow hashes all state updates, signs the hash, and publishes the signed hash, anyone can create proofs of fairness by downloading a copy of the database and checking that each state update was properly hashed and signed. If anything in the database's history ever gets altered, the fraud can be publicly disclosed because there will be more than one signed state for the same database entry.

# Eleets: encrypted leets

Before sending any transaction to an escrow, the transaction can be partially encrypted by the transaction creator such that only three things are public viewable: the sender's pubkey, a hash of a decryption key, and a bunch of indecipherable, encrypted text. By this means, the amount and the recipient's pubkey are hidden, even from the escrow. If the escrow adds such a transaction to its database, everyone will be able to see that the sender has perhaps sent some tokens somewhere but they won't be able to identify the recipient or the quantity of the transaction.

Even if the sender sends an eleet transaction multiple times, anyone can see how many times the sender has potentially sent funds somewhere because each valid transaction from their pubkey -- even an eleet one -- needs to be publicly viewable, though its contents may be encrypted (if it is an eleet transaction). Consequently, any user can see how many transactions each pubkey in the database has potentially created, though not necessarily how many payments it has received. If they know it has received a payment -- such as through an unencrypted leet transaction -- and they also know it never created any transactions -- such as because there are no transactions in the public database signed by that pubkey -- then they also know a lower bound on how much money it has. Moreover, if they see that a pubkey has received some money from an unencrypted leet transaction and that it has also sent some leet transactions but not any eleet transactions, they can also know a lower bound on how much money it has by subtracting the amounts it has spent from the amounts they know it received.

What's more, if a pubkey has received leet transactions and sent eleet transactions, that pubkey can prove to others if it has some tokens left by giving them all of the decryption keys necessary in order to decrypt all of its outgoing transactions, because the user they are proving it to can then subtract the amounts it ever has spent from the amounts they know it received. If anything is left over, it must have a balance. Even if a pubkey has only ever received eleet transactions, that pubkey can still prove to others if it has a balance by giving them all of the decryption keys necessary in order to decrypt all of its outgoing transactions as well as some number of incoming transactions which gave it a balance of tokens greater than the amount it ever spent.

# Censorship resistance through privacy

These properties, combined with pubkey reuse avoidance (i.e. send all your change to a new pubkey every time you make a transaction), create a relatively private way to issue, send, and receive tokens using an escrow service. Anyone who wants to receive tokens using this method could use a client which provides a receipt for any transaction where the sender first provides proof of a positive balance and then sends some of that balance to one of the client's pubkeys via the escrow. If the escrow quickly adds transactions to its database upon receipt of a small lightning fee, this is effectively a way to do fast and cheap token transfers via lightning.

By ensuring that transactions can be encrypted, the reasons an escrow might have for censoring a transaction are reduced. It can't feasibly censor eleet transactions based on their amount or based on the recipient's pubkey because they are not disclosed to it. If it is asked to add transactions to its database involving pubkeys it hasn't seen before, it has no grounds for refusing to do so as long as the transaction creator pays the fees desired by the escrow. It also has no feasible way to link these unknown pubkeys to any blacklisted user unless someone leaks decrypted information to the escrow. If the escrow decides to blacklist a certain pubkey and censor all pubkeys that it ever sent money to in the past or endeavors to send money to in the future, it could only succeed in that endeavor if someone leaks decrypted information to it. Moreover, if it removed some information from its database history in an effort to censor that history, users who previously downloaded the database could use the escrow's signatures to prove its fraudulent behavior and then create a new fork of the system with a new escrow.
