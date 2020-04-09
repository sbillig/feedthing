A "thing" is a crdt, edits to which are stored as entries in an append-only log.
Other people can make their own "forks" of existing things, which creates a new append-only log.

Elements in a thing could be static (content addressed data),
or dynamic (feed id of another thing)
```rust
let mut chomsky = things::Map::new(); // new feed
let mut books = things::OrderedSet::new(); // new feed
books.add(things::new_static(BookMeta{ title: "Manufacturing Consent" }, read_file("manucon.pdf")));
chomsky.set("books", books);
books.add(things::new_static(BookMeta { title: "Profit_over_people" }, read_file("pop.epub")));
```

Everyone has their own subjective view of a thing, and can opt to
merge in other peoples' changes, or ignore them.

Amanda:
```rust
// - makes a new thing, which creates a new feed (append-only log)
let mut cool_songs = things::OrderedSet::new();
println!(cool_songs.id); // id is the public key of the feed

// - modifies the thing; this creates an 'add' entry on the feed.
//   a song is static, so the feed contains the hash of the song file (and metadata?)
cool_songs.add(baba_oriley);
```

Barbara: 
```rust
// - forks amanda's list of cool songs, to add some of her own.
//   this creates a new feed.
let mut coolio = things::fork(amandas_cool_songs); // fork references feed sequence number
coolio.add(wish_you_were_here);
```

Amanda:
```rust
// - adds another song
cool_songs.add(rocket_man);
// - likes barb's changes, and merges them in
cool_songs.merge(barbs_fork); // merge as of a specified sequence number
```

Barbara: 
```rust
// - likes amanda's latest edits, so merges them in:
coolio.merge(amandas_cool_songs);
```

### authorship
- Each author could have an identity feed, which claims authorship of individual thing feeds.
  - Thing feed also references author feed.
  - This makes it easy to discover new content created by a friend.
- Author feed could act as a keychain, storing encrypted copies of the private keys of the individual thing feeds.


### ux
- I'm imagining special-purpose distributed apps using this as a backbone. Eg
  - playlist sharing
  - knowledge graph


### questions
- How do people discover new forks of a known thing?
- Cherry-picking edits?
- build on hypercore?
