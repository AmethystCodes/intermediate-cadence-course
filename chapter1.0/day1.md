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
```cadence
import NonFungibleToken from 0x01
import Record from 0x02
import Artist from 0x03

transaction(recipient: Address, songName: String) {
  
  prepare(acct: AuthAccount) {
  
    let recipientCollection = getAccount(recipient).getCapability(/public/RecordCollection)
                                .borrow<&Record.Collection{Record.CollectionPublic}>() 
                                ?? panic("User does not have a collection setup.")

    let nft <- Record.createRecord(songName: songName)

    recipientCollection.deposit(token: <- nft)
  }

  execute {
    log("NFT was minted")
  }
}
```


3. **Write a script to return an array of all the user's &Record.NFT? in their @Record.Collection**
```cadence
import Record from 0x02

pub fun main(account: Address): [UInt64]  {
  let publicCollection = getAccount(account).getCapability(Record.CollectionPublicPath)
                          .borrow<&Record.Collection{Record.CollectionPublic}>()!
  
  return publicCollection.getIDs()
}
```

4. **Write a transaction to save a @Artist.Profile to the signer's account, making sure to link it to the public so we can read it**
```cadence
import NonFungibleToken from 0x01
import Record from 0x02
import Artist from 0x03

transaction(name: String) {
  
  prepare(signer: AuthAccount) {

    let recordCollection = signer.getCapability<&Record.Collection{Record.CollectionPublic}>(Record.CollectionPublicPath)

    if signer.borrow<&Artist.Profile>(from: /storage/Profile) == nil {
        signer.save(<- Artist.createProfile(name: name, recordCollection: recordCollection), to: /storage/Profile)
        signer.link<&Artist.Profile>(/public/Profile, target: /storage/Profile)
    }

  }

  execute {
    log("Your Profile has been setup.")
  }
}
```

5. **Write a script to fetch a user's &Artist.Profile, borrow their recordCollection, and return an array of all the user's &Record.NFT? in their @Record.Collection from the recordCollection**
```cadence
import Record from 0x02
import Artist from 0x03

pub fun main(user: Address): [UInt64] {
   
    let profile: &Artist.Profile = getAccount(user).getCapability(/public/Profile)
                    .borrow<&Artist.Profile>()!

    let recordCollection: &Record.Collection{Record.CollectionPublic} = profile.recordCollection.borrow()!

    return recordCollection.getIDs()
}
```


6. **Write a transaction to unlink a user's @Record.Collection from the public path**
```cadence
import Record from 0x02
import Artist from 0x03

transaction() {

    prepare(signer: AuthAccount) {
    
        signer.unlink(Record.CollectionPublicPath)
    }

    execute {
        log("Collection unlinked")
    }

}
```


7. **Explain why the recordCollection inside the user's @Artist.Profile is now invalid**
- The `recordCollection` is now invalid because when a user unlinks a path, private or public, the capability is no longer accessible. Making it basically non existent to anyone outside of the users account.  


8. **Write a script that proves why your answer to #7 is true by trying to borrow a user's recordCollection from their &Artist.Profile**
```cadence
import Record from 0x02
import Artist from 0x03

pub fun main(user: Address): [UInt64] {
   
    let profile: &Artist.Profile = getAccount(user).getCapability(/public/Profile)
                    .borrow<&Artist.Profile>()!

    let recordCollection: &Record.Collection{Record.CollectionPublic} = profile.recordCollection.borrow()!

    return recordCollection.getIDs()
}
```
