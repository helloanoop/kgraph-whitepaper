## kgraph whitepaper
kgraph - a decentralised knowledge graph network

### Motivation
Its fascinating to imagine a world where you personal notes live on a decentralised network that can never be taken down. Decentralised peer-to-peer file systems such as IPFS make it possible to have your data uploaded to an immutable store which can be never be censored. Think about it, your blog could live forever. And you don't have to pay hosting fees or worry that services like Github might go out of service. Or worse, have a service make a change to what you had written. This was never possible before. Today, ethereum (a decentralized, open-source blockchain with smart contract functionality) along with other blockchain technologies make it possible.

### Knowledge Graph
A knowledge graph is essentially a knowledge base that uses a graph-structured data model or topology to integrate data. The ux for bi directional links and block references that has been pioneered by Roam Research has enabled users to create knowledge graphs that are interlinked and allow users to query theirs graphs to gain insight. The feature of bi directional links and block references are now a first class citizen in all of the new note taking apps that have arrived in the recent years.

kgraph aims to be a decentralized hub for knowledge graphs.

### Decentralised Tech Stack
The first questions that pops is are we going to store the notes in a blockchain. That would not scale, even for a single user. We are talking about storage aspect of having your notes on a blockchain. You could ask the same question for different features needed for a decentralised knowledge network. How do we handle Identity and Access Management, Discovery, Search, Storage, Collaboration (like git), Subscriptions (similar to newsletter subscriptions), End to end encryption and so on.

Instead of building all parts of the stack, we choose to decentralise and allow the user to pick what they want. When it comes to storage, some might want to use IPFS, some might want to have their graphs on a github repository, some on any other cloud provider for hosting the graph.

kgraph aims to define the protocol for accessing the graph and define the structure of the graph, and leave the rest for the user to choose.

### Graph Metadata Vs Graph Data Structure
These two terms are used often in the sections below, and I want define them before we move forward.

**Graph Metadata** - This is the data that lives in the smart contract and can only be updated by the person who created it (using the private key)

**Graph Data Structure** - This is the structure of the knowledge graph, each user can have multiple knowledge graphs. The knowledge graph can be stored in any location as per the users preference.

### Scope
It is crucial to get the design right for *graph metadata* that is written on the blockchain. The format of the knowledge graph itself is versioned allowing us to make improvements in the future without having to fork the blockchain.

For the first version of the knowledge graph, it is recommended to keep the *graph data structure* simple enough to fit in a blog and allow bi directional links. Features such as multiplayer, collaboration, subscriptions can be tackled in future versions of the graph.

### Graph Metadata
When talking about graph metadata, we are talking about the data that lives in the blockchain. Hence it is very important to keep the data footprint small. It's crucial to get the design right for the graph metadata, since we will not be able to change the smart contract once deployed on the mainnet (except forking)

Here is how the graph metadata would look like.

```javascript
[{
  user: "anoop",
  address: "0xf39Fd6e51aad88F6F4ce6aB8827279cffFb92266",
  graphs: [{
    name: "musings"
    location: "https://kgraph.notebase.com/anoop/musings",
    topic: ["blog", "musings"],
    stars: ["0x70997970C51812dc3A010C7d01b50e0d17dc79C8"],
    likes: [{
      uid: "L9jk9XR8ht67ddi6B-Y78",
      address: "0x70997970C51812dc3A010C7d01b50e0d17dc79C8"
    }]
  }]
}]
```

| Key      | Description |
| ----------- | ----------- |
| user      | The name of the user. (unique)       |
| address   | The eth account address of the user        |
| graphs   | The graphs belongining to the user        |
| graphs.name   | The name of the graph (unique under user)       |
| graphs.location   | Where the graph is hosted (can be Github / IPFS / any cloud provider)        |
| graphs.topic   | An array of topics of the graph       |
| graphs.stars   | Array of eth addresses of the users who have starred the graph     |
| graphs.likes   | An array containing likes of the pages in the graph    |
| graphs.likes.uid   | uid of the page being liked under the graph     |
| graphs.likes.address   | eth address of the user who liked the page in the graph    |

### Graph Data Structure
Each graph has a set of pages, and each page has a title with a set of blocks, and each block has a content and contain more blocks. The graph has a version attribute. This helps us to bring improvements in the graph in the future.

```javascript
{
  version: 1,
  name: "The Kgraph Specification",
  description: "Decentralised knowledge graphs",
  topics: ["ethereum", "blockchain", "pkm"],
  pages: [{
    uid: "L9jk9XR8ht67ddi6B-Y78"
    title: 'The Soverign Individual',
    icon: null,
    cover: null,
    outline: false,
    created_at: '2021-12-12 10:10:60',
    updated_at: '2021-12-12 10:10:60',
    transaction_id: 1,
    blocks: [{
      uid: "V1StGXR8_Z5jdHi6B-myT"
      content: 'I love ethereum',
      collapse: false,
      blocks: [{
        uid: "_HY65XR8abht98HH65jj9",
        content: "This is a nested content",
        collapse: false
      }]
    }]
  }]
}
```

| Key      | Description |
| ----------- | ----------- |
| version      | The version of the kgraph data model       |
| description      | The kgraph description       |
| topics      | The kgraph topics      |
| pages      | An array of pages in the kgraph      |
| pages.uid      | The uid of the page (unique under the graph)      |
| pages.title      | The page title      |
| pages.icon      | The page icon (unicode emoji or url))      |
| pages.cover      | The page cover image url     |
| pages.outline      | whether the page is an outliner   |
| pages.created_at      | Creation time of the page     |
| pages.updated_at      | Last updated time of the page      |
| pages.transaction_id      | See caching section below on the use of transaction_id    |
| pages.blocks      | An array of blocks under the page      |
| pages.blocks.uid      | The uid of the block (unique under the graph)      |
| pages.blocks.content      | The content of the block  |
| pages.blocks.collapse      | whether the block is collapsed (useful in outliner pages) |
| pages.blocks.blocks      | Children blocks (This is recursive) |

### Smart Contract
The kgraph will be deployed as a smart contract on the polygon network (for scalability). Anyone can create their usernames and add knowledge graphs by executing the smart contract.

### User Actions
A user exploring a knowledge graph can perform 2 actions on the graph.
 - **Star the graph** - Starring a graph is very similar to how you star a repository in Github (or star a notebase).
 - **Like a Page in the Graph** - Liking a page is again similar to liking a tweet, or a post.

When these actions occur, KNOW tokens get minted and assigned both to the graph creator user as well as the user.

### Tokenomics
Its very important to incentivize graph creators, as well as the readers. For this I propose an ERC20 Token that gets minted for every user action (Starring a Graph or Liking a Page). I am naming the token as KNOW as in "Knowledge Token"

Also, in order for the network to gain traction, we also need to incentivize early adopters. Here is what I propose.

- For the first 1000 User Actions, the graph creator gets 100 KNOW Coins and the user gets 20 KNOW Coins.
- After this, until 10000 User Actions, the graph creator gets 50 KNOW Coins and the user gets 10 KNOW Coins
- After this, until 100000 User Actions, the graph creator gets 10 KNOW Coins and the user gets 2 KNOW Coins
- And then onwards, thegraph creator gets 5 KNOW Coins and the user gets 1 KNOW Coins

My hope is that, as the network grows, the Coins would become more valuable, which in turn would lead to result more people contributing to the decentralised knowledge graph network. Its evident that the max value of the coin will never be greater than the transaction fees on the polygon network.

Tokens also help in establishing social proof for the usefulness of the graph.

### Gas Fees
kgraph would not to scale directly on ethereum. The plan is to use a sidechain such as Polygon to deploy the smart contract. 

### Ads
I don't believe ads are inherently bad. Its a grey area. No ads would mean an end to the careers of youtubers, instagram influeners et all. Ads help small entrepreneurs to reach a global audience. On the other end, the ad business incentivizes companies to start hoarding user data and track and profile them, and then cambridge analytica happens.

In the web2 ecosystem, Brendon Eich (the creator of javascript) and the Brave Team are working on BAT (Basic Attention Token), which is an attempt to incentivize the 3 parties involved in an ad transaction. The Ad Publisher, The Creator (on whose blog the ad is being viewed), and the User (the one who is paying attention to the ad). The tracking and profiling happens within the browser and no data gets uploaded to cloud. This is a way better ad model than the ones that we have today.

I think kgraph (if the network takes off) and Brave would be a great match. Along with KNOW coins the creators could then conditionally enable ads, thus creating more value for everyone. Isn't that what capitalism is all about.

 I would imagine if we can provide a layout section where we could somehow access and display brave ads. This of course will be entirely opt-in for the kgraph creators. Only those who want to show ads in their kgraph can opt in. It would also be possible to allow the kgraph creators to specify which ads they specifically are willing to display. This could be based of a unique advertiser id which is maintained on the blockchain (writing a smart contract for decentralised ad publisher lookup registry should be pretty straightforward)
 
### Projects
Here are the list of projects that will be written for kgraph. We plan to open source a lot off existing code that we have used for Notebase.

- **Magellan** - This is the network explorer that will be hosted at kgraph.network and allow for quick search by usernames, graph names and graph topic, with ranking based on stars and likes, thus improving the quality of search. Since the data resides on the blockchain, the users could use any competing kgraph explorers.
- **Columbus** - This is the offline app that would support writing and publishing kgraphs. 
- **Ada** - This is the backend for Magellan. The service will be responsible for listening to smart contract events and mirroring the kgraph database on the blockchain.

### Graph Caching for Performance
Whenever the user reloads the graph on the kgraph explorer, we would not want to reload the entire graph again. Every request that goes out to fetch the graph must include a transaction_id and the graph server is only expected to respond with pages that have a transaction_id more than the provided one. This ensures that we only load the deltas needed.

### A note on Inline Code Execution and Plugins
*Inline Code execution*
kgraphs are public, and people will explore graphs on the browser, its a security threat to have the graphs execute arbitrary piece of code on your browser. But nothing stops the community from building apps that allow inline javascript execution. This is in alignment with the philosophy of kgraph having a small core, and allow the user to have the freedom to use the tools of their choice. kgraph aims to define the protocol for accessing the graph and define the structure of the graph, and leave the rest for the user to choose.

* Plugins*
Since the kgraph is going to be decentralised, tools/apps could be independently developed to visualise and gather more insights from the graphs. The kgraph explorer (magellan) is likely to remain simple and minimalist and very text heavy.

### Open Source
All projects will be open sourced under the MIT license.

### Request for comments.
This is an early draft. I look forward to comments and feedback and participation from the community. I hope to have a the smart contract deployed to the testnet early december.

Author: Anoop M D <anoop@notebase.com>


