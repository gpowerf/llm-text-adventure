# 8‑Bit Island 

**starting room**: `bedroom`
**win condition**: player has `freeze64` **or** `warrior_chicken`
**losing conditions**: health <= 0

## Global state variables
dollars: 0
has_bus_ticket: false
has_wallet: false
has_video_rental_card: false
clothes_changed: false
wallet_opened: false
has_baguette: false
has_milk_bottle: false
has_bent_key: false
has_trading_cards: false
has_rocks: false
has_betamax_tape: false
has_slingshot: false
has_coffee: false
has_toy: false
has_warrior_chicken: false
has_freezee64: false
has_burger: false
has_radio: false
sculpture_made: false
sculpture_sold: false
coffee_power: false
knock_count: 0
baker_threw_baguette: false
seen_wallet_pantry: false
seen_bus_ticket_fridge: false
seen_bottle_convymart: false
seen_key_door: false
seen_slingshot_pawn: false
seen_radio_junk: false

## Player initial state
health: 10
inventory: []
flags: []

## Rooms

### bedroom
*Your bedroom. Closet, bed, bedside table, window.*
- exits: east (kitchen)
- objects: closet, bed, table (bedside table), window, note
- script: note says: "hi darling, your magazine didn't arrive. love, mom ♥♥♥" (cannot be taken)

### kitchen
*Your kitchen. Fridge, cooker, pantry. A back door leads outside? No, exits are west and south.*
- exits: west (bedroom), south (living_room)
- objects: fridge, cooker, pantry
- script: 
  - **look fridge** (if hasn't taken bus ticket): reveals bus ticket under magnet → seen_bus_ticket_fridge = true
  - **look pantry** (if hasn't taken wallet): reveals wallet → seen_wallet_pantry = true
  - **take bus ticket** (if seen): has_bus_ticket = true
  - **take wallet** (if seen): has_wallet = true

### living_room
*Living room with TV, sofa, C64, Atari 2600, posters on wall.*
- exits: north (kitchen), south (road)
- objects: tv, sofa, c64, atari2600, posters
- script: 
  - **use c64** (only if has warrior_chicken): triggers ending 8/8 (gamer ending, win)
  - **use c64** (without game): "maybe later after you buy warrior chicken."

### road
*Outside at the side of the road. Your house is north, convy mart south, bus stop east, postal service west.*
- exits: north (living_room), south (convy_mart), west (postal_exterior), east (bus_stop_home)
- objects: none (but can see house to north, convy mart to south)

### postal_exterior
*Standing in front of the bit island postal service. To enter go south.*
- exits: south (postal_office), west (bakery_exterior), east (road)
- objects: none (building itself described)

### postal_office
*Postal service office. A woman stands behind the counter.*
- exits: north (postal_exterior)
- objects: postwoman (woman), counter
- script:
  - **talk** (if clothes_changed): she says they delivered all packages including your magazine, suggests it may have been stolen.
  - **talk** (if not changed): doesn't talk about delivery.

### bakery_exterior
*Standing in front of a bakery. The door is closed.*
- exits: east (postal_exterior)
- objects: door, bakery (sign), key (in lock, if not taken)
- script:
  - **look door** (if bent_key not taken): reveals key in lock → seen_key_door = true
  - **take key** (if seen): get bent_key (has_bent_key = true) – key bends when pulled.
  - **knock door**: baker leans out, dialogue based on knock_count. After 4 knocks, baker throws baguette → baker_threw_baguette = true, a baguette appears on ground (baguette object). After 15+ knocks, you are pulled inside → move to bakery_interior (instant death).
  - **look baguette** (if baker threw it): appears on ground, can be taken (has_baguette = true).
  - **open door**: fails, bakery closed.

### bakery_interior
*Dead end – bad ending.*
- exits: none
- objects: baker (clown with axe)
- script: on_enter → print "The baker laughs. He kills you.", health = 0 (lose).

### convy_mart
*Convy Mart convenience store. Magazines, sweets, trading cards. A man behind the counter.*
- exits: north (road)
- objects: clerk (man), counter, magazines, sweets, trading_cards (packs), bubblegum (sold out), milk_bottle (on ground, if not taken and seen)
- script:
  - **look convy mart** (if bottle not taken): mentions a bottle on ground → seen_bottle_convymart = true
  - **take bottle** (if seen): has_milk_bottle = true
  - **buy trading cards** ($3): get pack of trading cards (has_trading_cards = true)
  - **buy sweets/gum**: man refuses (mother said no / out of gum)
  - **talk** (if clothes_changed): man says what they sell and that they are out of bubble gum.
  - **talk** (if not changed): mocks you for being in PJs.

### bus_stop_home
*Bus stop near your house. Bus to the mall is east (board by walking east).*
- exits: west (road), east (bus_stop_mall) – east requires has_bus_ticket, otherwise “You cannot travel without a bus ticket.”
- objects: bus_driver (implied)
- script: 
  - **east** (with ticket): plays bus ride animation, moves to bus_stop_mall.
  - **east** (without ticket): blocked.

### bus_stop_mall
*Bus stop near the mall. Bus back home is east (walk east).*
- exits: south (mall_entrance), west (video_exterior), east (bus_stop_home) – east requires ticket, same rule as above.
- objects: bus
- script: same as bus_stop_home but reversed.

### video_exterior
*Standing in front of the video rental store (Betacine). To enter, walk south.*
- exits: south (video_rental), west (pawn_exterior), east (bus_stop_mall)
- objects: none

### video_rental
*Inside video rental store. Walls full of Betamax tapes (only "Games of War"). A man behind counter.*
- exits: north (video_exterior)
- objects: man, tapes (wall of betamax), counter
- script:
  - **talk**: man asks if you want to rent Games of War (Y/N). If yes and you have video rental card and not already rented, gives betamax_tape (has_betamax_tape = true). If no card, refuses. If already has tape, refuses.
  - **look tapes**: all the same movie.

### pawn_exterior
*Standing in front of the local pawn shop. Entrance south.*
- exits: south (pawn_shop), east (video_exterior)
- objects: none

### pawn_shop
*Inside pawn shop. Junk pile, guns, slingshot (maybe), radio (maybe). Big bald man behind counter.*
- exits: north (pawn_exterior)
- objects: man, junk, guns, slingshot (if not bought), radio (if not bought), counter
- script:
  - **talk**: dialogue varies. Man won’t sell guns but will sell other items or buy from you. Shows price list.
  - **buy slingshot** ($7): if seen and available, get slingshot (has_slingshot = true).
  - **buy radio** ($15): if seen in junk, get radio (has_radio = true).
  - **sell toy** ($2): can sell toy.
  - **sell bent_key** ($4): can sell key.
  - **sell milk_bottle**: man won’t buy (belongs to milkman).
  - **sell sculpture** (bottle+key combined): sell for $200, set sculpture_sold = true, remove key and bottle.
  - **look junk** (if radio not taken): reveals a radio → seen_radio_junk = true
  - **look guns** (if slingshot not taken): reveals slingshot → seen_slingshot_pawn = true

### mall_entrance
*Outside the mall. Rocks on ground.*
- exits: north (bus_stop_mall), south (mega_cafe)
- objects: rocks
- script:
  - **take rocks**: get rocks (has_rocks = true), can only take some.
  - **look rocks**: huge pile, can’t take all.

### mega_cafe
*Mega Cafe. Extra strong coffee sold here.*
- exits: north (mall_entrance), south (barron_burger)
- objects: coffee, counter (menu)
- script:
  - **buy coffee** ($2): get coffee (has_coffee = true), can only carry one.
  - **use coffee**: drink, gives coffee_power = true (super speed? affects troll fight), coffee removed.

### barron_burger
*Barron Burger. Fat burgers and kiddy meals.*
- exits: north (mega_cafe), south (super_micro_mart)
- objects: burger, kiddy_meal, counter
- script:
  - **buy burger** ($4): get burger (has_burger = true), only one.
  - **buy kiddy meal** ($4): get burger and toy (has_burger = true, has_toy = true), only one each.
  - **use burger**: eat, removes burger.

### super_micro_mart
*Super Micro Mart. Computers and games. Micro Bill behind counter.*
- exits: north (barron_burger), south (beach)
- objects: bill (micro bill), computers (c64s, spectrums, apples, ataris), games, warrior_chicken (on shelf)
- script:
  - **talk**: Bill says they have plenty of Warrior Chicken, $25.
  - **buy warrior chicken** ($25): get the game (has_warrior_chicken = true).
  - **look games**: you see Warrior Chicken.

### beach
*The beach. Surfer territory. A huge troll/surfer sits on a rock.*
- exits: north (super_micro_mart)
- objects: surfer (troll)
- script:
  - **kill troll** (bare hands): instant death (health=0).
  - **kill troll with slingshot** (has slingshot, no rocks): throws slingshot, dies.
  - **kill troll with slingshot** (has slingshot, rocks, no warrior chicken, no coffee power): hit troll but you die (ending 3/8).
  - **kill troll with slingshot** (has slingshot, rocks, has warrior chicken): hit troll, you drop game, troll takes it, you survive but lose game (ending 4/8).
  - **kill troll with slingshot** (has slingshot, rocks, coffee_power, no warrior chicken): you barely escape (ending 7/8).
  - **kill troll with baguette** (has baguette, no warrior chicken): troll drops freeze64, you grab it and run home → get freeze64, good ending 5/8.
  - **kill troll with baguette** (has baguette, has warrior chicken): same but also have game → best ending 6/8, get freeze64.
  - Talking to troll: insults you.
