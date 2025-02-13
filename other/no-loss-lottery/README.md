# No-loss Lottery example

How to test:

## Prepare
### Basics
1. `resim reset`
1. `export xrd=030000000000000000000000000000000000000000000000000004`

### Main account
1. `resim new-account` -> save address and key into $admin and $admin_pub 


### Players
1. `resim new-account` -> save into $player1 and $p_pub1
1. `resim new-account` -> save into $player2 and $p_pub2
1. `resim new-account` -> save into $player3 and $p_pub3

## Build Staking component
Use specific package
1. `export staking_pkg=01d1f50010e4102d88aacc347711491f852c515134a9ecf67ba17c`
1. `cd staking`
1. `resim publish . --address $staking_pkg`
1. `resim call-function $staking_pkg Staking new 1000,$xrd` -> save into $staking, save staking token def into $staking_token 


## Build Lottery component
1. `cd ../lottery`
1. `resim publish .` -> save into $lottery_pkg
1. `resim call-function $lottery_pkg Lottery new $staking` -> save Lottery component into $lottery, save admin def into $admin_badge, lottery ticket token def into $lottery_ticket

## Start new lottery
Specify lottery name, ending epoch and price. Also use the admin badge for the auth
1. `resim call-method $lottery start_lottery "Lottery#1" 1000 100 1,$admin_badge` -> save lottery ID (will be zero for the first generated lottery) into the $lottery_id

## Buy tickets for each user
For each user setup default account, purchase one ticket using the $lottery_id and required xrd
`resim set-default-account $player1 $p_pub1`
`resim call-method $lottery buy_ticket $lottery_id 100,$xrd` 

`resim set-default-account $player2 $p_pub2`
`resim call-method $lottery buy_ticket $lottery_id 100,$xrd` 

`resim set-default-account $player3 $p_pub3`
`resim call-method $lottery buy_ticket $lottery_id 100,$xrd`


## End current lottery
1. `resim set-default-account $admin $admin_pub`
1. `resim set-current-epoch 1001`
1. `resim call-method $lottery end_lottery $lottery_id 1,$admin_badge`

## Withdraw staking and check rewards
For each user setup default account again, withdraw staking + reward using $lottery ID and the ticket resource def as auth.

`resim set-default-account $player1 $p_pub1`
`resim call-method $lottery withdraw $lottery_id 1,$lottery_ticket`

`resim set-default-account $player2 $p_pub2`
`resim call-method $lottery withdraw $lottery_id 1,$lottery_ticket`

`resim set-default-account $player3 $p_pub3`
`resim call-method $lottery withdraw $lottery_id 1,$lottery_ticket`

