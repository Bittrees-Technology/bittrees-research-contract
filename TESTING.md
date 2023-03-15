# Testing steps

-   Upload IPFS image in ./ipfs, then update metadata with that image CID, then upload ./ipfs folder via Pinata
-   Make sure contract ready to go. Update uri from new folder CID from IPFS upload
-   Make sure deploy script has proper contract name
-   If first time deploy, blow away `~/contract/.openzeppelin` files.
-   Deploy and verify contract to Goerli and following testing below. Try on testnets.opensea.io
-   Check gas prices prior to deploying to mainnet -- view at the top of etherscan.io
-   Deploy and verify on mainnet. Try on opensea.io
-   Add team to collection on OpenSea so they can edit it
    -   Jonathan: raging.eth -- 0xE5350D96FC3161BF5c385843ec5ee24E8B465B2f
    -   Jennica: 0x41Be6bDB81695c44631162e906Ba19e5233D3144

## GOERLI

Deployer wallet public key: 0x7435e7f3e6B5c656c33889a3d5EaFE1e17C033CD

OpenZeppelin Proxy deployed to 0xf62075e5bf74bf2f37f02368e40949298f07da4b
CONTRACT: 0x9CDfb5B74eD4c001F53628750c41c5841551c600

Set Mint Price a bit cheaper to 0.001 ETH: 1000000000000000

Mint Membership (Token 1) with 0.001 ETH to address: 0x7435e7f3e6B5c656c33889a3d5EaFE1e17C033CD

Check balance with `balanceOf` token 1

Test Expiration

-   Check Token 1 expiration via `expirationTimestamps` to retrieve token 1's current timestamp -- and it should be 360 days in the future when run in brower console: `new Date(timestamp * 1000)`. `isExpired` should also return false.

-   Try changing expiration timestamp for Token 1 -- set it to nearby future (3 min from now) -- e.g. find future timestamp by running this in browser console: `Math.floor(Date.now()/1000) + 180` and query `isExpired`. Then try `balanceOf` to see owner no longer has that token since expired.

-   Test setting a longer expiration, then minting, then double-check date

Test URI

-   try `setURI` to `ipfs://cid/{id}`
-   try `uri` to see results come back

Permissions

-   check `hasRole` using role of `0x0` and address of `0x7435e7f3e6B5c656c33889a3d5EaFE1e17C033CD`
-   verify non-owner wallet can't run setters
-   verify adding a new wallet with role

Withdraw

-   Try withdraw

Test Minting website.

## MAINNET

### IPFS

Upload files to IPFS and test.

### DEPLOY

Deploy to `mainnet`

        npx hardhat run --network mainnet scripts/deploy.js

Deployer wallet public key: 0x7435e7f3e6B5c656c33889a3d5EaFE1e17C033CD
OpenZeppelin Proxy deployed to 0xc8121E650bd797D8b9DAd00227a9A77EF603A84A

Implementation contract: 0x7d85cb11d2cb805bd941fccdd2ec65e49df77c9b
ProxyAdmin: https://etherscan.io/address/0xe4249d51593d590fb5120f48e2dabc36a550f698

        npx hardhat verify --network mainnet 0x7d85cb11d2cb805bd941fccdd2ec65e49df77c9b

NOTE: ProxyAdmin is same one as BAG Deploy: https://etherscan.io/address/0xe4249d51593d590fb5120f48e2dabc36a550f698 and is owned by raging.eth (https://etherscan.io/address/0x41Be6bDB81695c44631162e906Ba19e5233D3144)

### Setup Defender for contract Upgrades

-   Setup Defender: https://docs.openzeppelin.com/defender/guide-upgrades
-   Make sure `.env` has proper DEFENDER API keys
-   Transfer ProxyAdmin contract (`0xe4249d51593d590fb5120f48e2dabc36a550f698`) to Multisig Safe address of `eth:0x41Be6bDB81695c44631162e906Ba19e5233D3144` by updating `transfer-ownership.js` script to have proper Safe address, then run `npx hardhat run --network mainnet scripts/transfer-ownership.js`
