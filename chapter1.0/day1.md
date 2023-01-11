## Chapter 1.0 Day 1 Quests

1. **Write a transaction to save a @Record.Collection to the signer's account, making sure to link the appropriate interfaces to the public path.**
```cadence
import NonFungibleToken from 0x01
import Record from 0x02
import Artist from 0x03

transaction {
  
  prepare(acct: AuthAccount) {
  
   if acct.borrow<&Record.Collection>(from: Record.CollectionStoragePath) == nil {
        acct.save(<- Record.createEmptyCollection(), to: Record.CollectionStoragePath)
        acct.link<&Record.Collection{NonFungibleToken.Receiver, NonFungibleToken.CollectionPublic, Record.CollectionPublic}>
                (Record.CollectionPublicPath, target: Record.CollectionStoragePath)
    }
  }

  execute {
    log("Collection setup complete.")
  }
}
```

2. **Write a transaction to mint some @Record.NFTs to the user's @Record.Collection**


3. **Write a script to return an array of all the user's &Record.NFT? in their @Record.Collection**


4. **Write a transaction to save a @Artist.Profile to the signer's account, making sure to link it to the public so we can read it**


5. **Write a script to fetch a user's &Artist.Profile, borrow their recordCollection, and return an array of all the user's &Record.NFT? in their @Record.Collection from the recordCollection**


6. **Write a transaction to unlink a user's @Record.Collection from the public path**


7. **Explain why the recordCollection inside the user's @Artist.Profile is now invalid**


8. **Write a script that proves why your answer to #7 is true by trying to borrow a user's recordCollection from their &Artist.Profile**
