# 8‑Bit Island

**starting room**: `bedroom`
**win condition**: player has `freeze64` **or** `warrior_chicken` (that is, you have successfully recovered the magazine *or* you have bought the new game that replaces it)
**losing conditions**: `health <= 0` (bad endings occur when you die or fail to recover the magazine)

> *Premise*: Your copy of FREEZE64 magazine didn’t arrive. Rumour says someone snatched it. Explore Bit Island, earn a few dollars, buy gear, and decide how this day ends. There are multiple endings—survive, triumph, or die.

## Global state variables
| Variable | Description |
|----------|-------------|
| `dollars` | Money you have. |
| `ticket_num` | How many bus tickets you have (0 = none, 1+ = you can ride). |
| `has_changed` | Whether you have changed clothes (helps in some shops). |
| `has_talked_convymart` | Whether you have talked to the clerk at Convy Mart. |
| `has_talked_postwoman` | Whether you have talked to the postal clerk. |
| `has_prayed_church` | Whether you have prayed at the church. |

## Player initial state
health: 10
dollars: 5
ticket_num: 0
has_changed: false
has_talked_convymart: false
has_talked_postwoman: false
has_prayed_church: false
inventory: []
flags: []

## Rooms

### bedroom (id: bedroom)
*You are in your bedroom. You see your closet, bed, bedside table, and a window overlooking the sea.*
- exits: south (living_room)
- objects: closet, bed, table, window

### living_room (id: living_room)
*A small living room with video game posters on the walls. You can see your 14‑inch TV, a comfy sofa, and under the TV a Commodore 64 and an Atari 2600.*
- exits: north (bedroom), south (kitchen), west (bus_stop_home)
- objects: tv, sofa, c64, atari2600

### kitchen (id: kitchen)
*Your kitchen. There’s a fridge, a cooker, and an open pantry here.*
- exits: north (living_room)
- objects: fridge, cooker, pantry

### bus_stop_home (id: bus_stop_home)
*You are at the bus stop near your house. The road to the Convy Mart lies to the south. To board the city bus, walk east.*
- exits: east (mall_bus_departing), south (convy_mart)
- objects: none

### mall_bus_departing (id: mall_bus_departing)
*You are on the bus heading to the mall. A sign says “WELCOME TO BIT ISLAND MALL”. Another sign reminds you to “HAVE YOUR TICKET READY”.*
- exits: east (mall_entrance)
- objects: bus_driver
- script: `if ticket_num > 0 → “You show your ticket and the driver lets you pass.” else → “The driver stops you. You need a bus ticket!”`

### mall_entrance (id: mall_entrance)
*You are standing in front of your local mall, your usual hangout. On the ground you see some rocks.*
- exits: west (mall_bus_departing), north (mall_interior)
- objects: rocks

### mall_interior (id: mall_interior)
*Inside the mall. You can see a video rental shop, a food court, and a strange stall selling “MAGIC GOODS”.*
- exits: south (mall_entrance)
- objects: video_shop, food_court, magic_stall

### convy_mart (id: convy_mart)
*Convy Mart – a small convenience store. A clerk stands behind the counter.*
- exits: north (bus_stop_home)
- objects: clerk, counter, fridge_with_milk, shelf_with_baguettes
- script: `on_enter: if has_talked_convymart == false → “The clerk nods at you.”`

### postal_office (id: postal_office)
*You are in the postal service office. A woman stands behind the counter sorting parcels.*
- exits: ? (to be discovered)
- objects: postwoman
- script: `on_enter: if has_talked_postwoman == false → “The woman looks up: ‘Can I help you?’”`

### beach (id: beach)
*The beach. This is surfer territory. A surfer is sitting on a large rock – but is he a troll?*
- exits: ? (to be discovered)
- objects: surfer

### church (id: church)
*A quiet church. A faint smell of incense hangs in the air.*
- exits: ? (to be discovered)
- objects: altar, priest
- script: `use` altar → sets flag `prayed` and heals health by 1 (max 10)

### bakery (id: bakery)
*You are inside the bakery. The baker has an axe and is dressed like a clown.*
- exits: none (bad ending room – you are trapped)
- objects: baker
- script: `on_enter → print “The baker laughs maniacally.” ; trigger bad_ending`

## Objects

### closet (id: closet)
- description: “A wooden closet.”
- takeable: false
- verbs: `open` → if `has_changed == false`, prints “You open the closet and find some clothes. You get changed.” and sets `has_changed = true`. If `has_changed == true`, prints “The closet is open.”

### bed (id: bed)
- description: “A comfortable single bed.”

### table (id: table)
- description: “A small wooden bedside table with a note on it.”
- examine: prints, “The note says: ‘Hi darling, I’ll be back late. Don’t forget to feed the cat.’ – Mum”

### window (id: window)
- description: “You can see the sea from here – your house is on top of a cliff.”

### tv (id: tv)
- description: “A 14‑inch TV. Nothing interesting is on right now.”

### sofa (id: sofa)
- description: “A comfy, soft sofa.”

### c64 (id: c64)
- description: “A Commodore 64 computer with a joystick.”
- verbs: `use with warrior_chicken` → effect: winning condition

### atari2600 (id: atari2600)
- description: “An Atari 2600 console. No cartridge is inserted.”

### fridge (id: fridge)
- description: “Your kitchen fridge.”
- examine: prints “There’s never anything interesting in this fridge.”

### cooker (id: cooker)
- description: “An old gas cooker.”

### pantry (id: pantry)
- description: “A walk‑in pantry.”
- examine: prints “It’s full of cans and other things you don’t need. And your wallet is there too!” → effect: you receive a wallet containing 5 dollars (increase dollars by 5). This happens only once.

### rocks (id: rocks)
- description: “A pile of small rocks.”
- takeable: yes (they become a generic “rocks” object in inventory)
- verbs: `use with slingshot` → effect: you can shoot the troll

### bus_ticket (id: bus_ticket)
- description: “A bus ticket for the Bit Island bus service. Under the fridge magnet, remember?”
- takeable: yes, from the fridge magnet in your kitchen (this is how you get the ticket).
- use: when boarding the bus, if you have this, you ride.

### wallet (id: wallet)
- description: “A leather wallet with a few dollars inside.”
- takeable: yes (from pantry)
- verbs: `open` → effect: prints “You see 5 dollars.” and adds $5 to dollars (if not already taken).

### trading_cards (id: trading_cards)
- description: “A pack of vintage trading cards.”
- buy: available at the magic stall for 3 dollars.

### warrior_chicken (id: warrior_chicken)
- description: “The new video game everyone is talking about – Warrior Chicken for the Commodore 64.”
- buy: available at the magic stall (expensive – 10 dollars) OR recoverable from troll’s lair if you defeat the troll.

### freeze64 (id: freeze64)
- description: “Your favourite video game magazine! It has a feature on ‘Warrior Chicken’.”
- condition for victory: if you recover this (the original plot goal), you win immediately.

### milk_bottle (id: milk_bottle)
- description: “A half‑full bottle of milk.”
- use: drink it → restore 1 health.

### baguette (id: baguette)
- description: “A fresh baguette.”
- use: eat it → restore 2 health.

### slingshot (id: slingshot)
- description: “A powerful slingshot with a wooden handle.”
- obtain: buy from the magic stall (5 dollars).

## Custom Verbs

### `buy <object>`
- allowed in: `convy_mart`, `magic_stall`
- effect: If you have enough dollars, remove the dollars and add the object to inventory.
- message: “You buy a <object>. You now have X dollars left.”

### `sell <object>`
- allowed in: `convy_mart`
- effect: You receive dollars for the object (maybe 1 dollar for rocks, 2 for milk bottle, etc.).
- message: “You sell your <object> and receive Y dollars.”

### `knock`
- allowed on: any door or the post office counter.
- effect: If you knock at the post office, the woman responds and you can ask about your magazine.

### `talk <person>`
- allowed on: `clerk`, `postwoman`, `surfer`, `priest`
- effect: Triggers a brief dialogue that may give clues, advance the plot, or change the character’s behaviour.

### `pray`
- allowed only in: `church`
- effect: Sets flag `prayed`, heals 1 health (max 10).
- message: “You kneel and pray. A sense of calm washes over you.”

### `kill <target>`
- allowed target: `troll` (the surfer on the beach is revealed to be a troll)
- effect: Must have a weapon (e.g., slingshot + rocks, or a baguette if you are lucky). If you succeed, you recover the missing magazine/warrior chicken from the troll’s remains.
- messages:
  - “You attack the troll bare handed and die.” (bad ending)
  - “You use the slingshot with rocks and hit the troll right between the eyes! You recover the stolen copy of FREEZE64.” (win condition)
  - “You throw a baguette at the troll. It gets stuck on his head, enraging him. He kills you.” (bad ending)

## Scripts & Triggers

- `on_take` `freeze64` → **immediate victory**: prints “You found your magazine! You rush home to read it. Congratulations!” and ends game.
- `on_take` `warrior_chicken` from **magic stall** → also triggers victory (you bought the game instead of recovering the magazine).
- `on_use` `c64` with `warrior_chicken` → prints “You load Warrior Chicken. The game is amazing! Who needs FREEZE64? You win!”
- **bad ending** (e.g., entering the bakery, fighting the troll without a weapon, or reaching health ≤ 0) prints an ending number (e.g., “Ending 1/8 – the baker gets you.”) and stops.
