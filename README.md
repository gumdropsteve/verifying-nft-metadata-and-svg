# Verifying NFT Metadata + SVG
This is a simple repo showing how to use the `cast` command in [Foundry](https://getfoundry.sh/) to download & verify the metadata of an NFT and extract the SVG image from that metadata with [an applied example](#applied-example) on NFT #2797 from the [Loot (for Adventurers)](https://opensea.io/collection/lootproject) NFT collection.

Note: This repo assumes you are working from a repo where Foundry has been deployed and your `foundry.toml` has `chain_id = 1` (Ethereum Mainnet is chain ID 1). If you want to use this for another chain's mainnet or testnet, make sure you adjust the `chain_id` to point to that chain. For example, `chain_id = 10143` for Monad Testnet (chainlist.org is a site where [a collection of chain ids](https://chainlist.org/) can be found).

---

## 1. Call `tokenURI(tokenId)`

Use `cast` to call the contract and get the metadata URI:

```bash
cast call <PropertyNFT_address> "tokenURI(uint256)" <id> --rpc-url <rpc>
```

The output will look something like this
```bash
0x0000000000000...75248566a526b5130493055464e4a6...00000000000000000000000
```

Your `cast call` output is ABI-encoded. You can extract the human-readable string directly: 
```bash
cast --to-ascii 0x0000000000000...
```

This will return something like
```bash
data:application/json;base64,eyJuYW1lIjogIlByb3BlcnR5ICMwIiwiZGVzY3JpcHRpb...24iOiAiVG9rZW5pemVkIFJlYWwgRXN0YXRlIFByb3BlcnR5Li4uIn0=
```


## 2. Decode the Base64 JSON
Strip the prefix data:application/json;base64, and decode the JSON:

```bash
echo '<BASE64_JSON>' | base64 -d > metadata.json
cat metadata.json
```

Example output:
```bash
{
  "name": "Property #0",
  "description": "Tokenized Real Estate Property with on-chain verification...",
  "image": "data:image/svg+xml;base64,PHN2ZyB4bWxucz0..."
}
```

## 3. Decode the Embedded SVG
Take the value of the image field (after data:image/svg+xml;base64,) and decode it:

```bash 
echo '<BASE64_SVG>' | base64 -d > file.svg
```

## 4. View the SVG
Open file.svg in a browser or image viewer to confirm the onchain rendering.

```bash
open file.svg
```

# Applied Example
Here is an applied example with the [Loot (for Adventurers)](https://opensea.io/collection/lootproject) NFT collection. This came together because I needed to verify that the SVG for an NFT collection in development on Testnet was being created as expected before applying some SVG metadata functions to other NFT contracts within the broader system being developed. Normally I would have tested this by finding the NFT collection on OpenSea or Magic Eden and seeing the image displayed, but OpenSea no longer supports testnet and the SVG images for the collection were not displaying on Magic Eden. Make sure `chain_id = 1` is set in your `foundry.toml` file or this won't work.

## Copy + Paste This Instance
```bash
cast call 0xff9c1b15b16263c61d017ee9f65c50e4ae0113d7 "tokenURI(uint256)" 2797 --rpc-url https://ethereum.public.blockpi.network/v1/rpc/public
```

The output should look something like this (full output can be found [in this gist](https://gist.github.com/gumdropsteve/aaca33017da9476e33d287f2e3363bd3)).
```bash
0x000000...35a6b523159305a4551304f4752485...0000000000000
```

Your `cast call` output is ABI-encoded. You can extract the human-readable string directly: 
```bash
cast --to-ascii 0x00000...
```

The output should look something like this (full output can be found [in this gist](https://gist.github.com/gumdropsteve/17491f78acc0b7e06e6b0da0b80fc001)).
```bash
udata:application/json;base64,eyJuYW1lIb3Bl...255In1dfQ==
```

### 2. Decode the json
Extract the metadata and save it locally as `metadata.json`.
```bash
echo 'eyJuYW1lIjogIkJhZyAjMjc5NyIsICJkZXNjcmlwdGlvbiI6ICJMb290IGlzIHJhbmRvbWl6ZWQgYWR2ZW50dXJlciBnZWFyIGdlbmVyYXRlZCBhbmQgc3RvcmVkIG9uIGNoYWluLiBTdGF0cywgaW1hZ2VzLCBhbmQgb3RoZXIgZnVuY3Rpb25hbGl0eSBhcmUgaW50ZW50aW9uYWxseSBvbWl0dGVkIGZvciBvdGhlcnMgdG8gaW50ZXJwcmV0LiBGZWVsIGZyZWUgdG8gdXNlIExvb3QgaW4gYW55IHdheSB5b3Ugd2FudC4iLCAiaW1hZ2UiOiAiZGF0YTppbWFnZS9zdmcreG1sO2Jhc2U2NCxQSE4yWnlCNGJXeHVjejBpYUhSMGNEb3ZMM2QzZHk1M015NXZjbWN2TWpBd01DOXpkbWNpSUhCeVpYTmxjblpsUVhOd1pXTjBVbUYwYVc4OUluaE5hVzVaVFdsdUlHMWxaWFFpSUhacFpYZENiM2c5SWpBZ01DQXpOVEFnTXpVd0lqNDhjM1I1YkdVK0xtSmhjMlVnZXlCbWFXeHNPaUIzYUdsMFpUc2dabTl1ZEMxbVlXMXBiSGs2SUhObGNtbG1PeUJtYjI1MExYTnBlbVU2SURFMGNIZzdJSDA4TDNOMGVXeGxQanh5WldOMElIZHBaSFJvUFNJeE1EQWxJaUJvWldsbmFIUTlJakV3TUNVaUlHWnBiR3c5SW1Kc1lXTnJJaUF2UGp4MFpYaDBJSGc5SWpFd0lpQjVQU0l5TUNJZ1kyeGhjM005SW1KaGMyVWlQazFoWTJVZ2IyWWdVMnRwYkd3OEwzUmxlSFErUEhSbGVIUWdlRDBpTVRBaUlIazlJalF3SWlCamJHRnpjejBpWW1GelpTSStJazFoWld4emRISnZiU0JYYUdsemNHVnlJaUJUZEhWa1pHVmtJRXhsWVhSb1pYSWdRWEp0YjNJZ2IyWWdSVzVzYVdkb2RHVnViV1Z1ZER3dmRHVjRkRDQ4ZEdWNGRDQjRQU0l4TUNJZ2VUMGlOakFpSUdOc1lYTnpQU0ppWVhObElqNUVhWFpwYm1VZ1NHOXZaQ0J2WmlCQmJtZGxjand2ZEdWNGRENDhkR1Y0ZENCNFBTSXhNQ0lnZVQwaU9EQWlJR05zWVhOelBTSmlZWE5sSWo1UWJHRjBaV1FnUW1Wc2RDQnZaaUJIYVdGdWRITThMM1JsZUhRK1BIUmxlSFFnZUQwaU1UQWlJSGs5SWpFd01DSWdZMnhoYzNNOUltSmhjMlVpUGlKRllXZHNaU0JDYVhSbElpQklZWEprSUV4bFlYUm9aWElnUW05dmRITWdiMllnUlc1c2FXZG9kR1Z1YldWdWRDQXJNVHd2ZEdWNGRENDhkR1Y0ZENCNFBTSXhNQ0lnZVQwaU1USXdJaUJqYkdGemN6MGlZbUZ6WlNJK0lrVmhaMnhsSUVkeVlYTndJaUJJWVhKa0lFeGxZWFJvWlhJZ1IyeHZkbVZ6SUc5bUlGSmhaMlVnS3pFOEwzUmxlSFErUEhSbGVIUWdlRDBpTVRBaUlIazlJakUwTUNJZ1kyeGhjM005SW1KaGMyVWlQazVsWTJ0c1lXTmxQQzkwWlhoMFBqeDBaWGgwSUhnOUlqRXdJaUI1UFNJeE5qQWlJR05zWVhOelBTSmlZWE5sSWo0aVIyeHZiMjBnVUdWaGF5SWdSMjlzWkNCU2FXNW5JRzltSUVWdWJHbG5hSFJsYm0xbGJuUWdLekU4TDNSbGVIUStQQzl6ZG1jKyJ9' | base64 -d > metadata.json
```

Show the contents of the json file:
```bash
cat metadata.json
```

The output from those commands should be saved locally as `metadata.json` and look like this:
```bash
{"name": "Property #0", "description": "Tokenized Real Estate Property with on-chain verification and Loot-inspired attributes.", "image": "data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMj...jbGFzcz0iYmFzZSI+VHlwZTogUmVzaWRlbnRpYWw8L3RleHQ+PC9zdmc+"}
```

The full output for this example can be found [in this gist](https://gist.github.com/gumdropsteve/0f2ff7803b6a829af50605046d582007).

### 3. Decode the Embedded SVG
Inside `metadata.json` (the output above), find the `"image": "data:image/svg+xml;base64,PHN2Zy..."`.
Copy only the part after `data:image/svg+xml;base64,`.

What you copy should look like this:
```bash
PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMj...BjbGFzcz0iYmFzZSI+VHlwZTogUmVzaWRlbnRpYWw8L3RleHQ+PC9zdmc+
```

Now you need to run the command:
```bash
echo 'PHN2ZyB4bWxucz0...' | base64 -d > loot2797.svg
```

For our Loot example, that command looks like:
```bash
echo 'PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHByZXNlcnZlQXNwZWN0UmF0aW89InhNaW5ZTWluIG1lZXQiIHZpZXdCb3g9IjAgMCAzNTAgMzUwIj48c3R5bGU+LmJhc2UgeyBmaWxsOiB3aGl0ZTsgZm9udC1mYW1pbHk6IHNlcmlmOyBmb250LXNpemU6IDE0cHg7IH08L3N0eWxlPjxyZWN0IHdpZHRoPSIxMDAlIiBoZWlnaHQ9IjEwMCUiIGZpbGw9ImJsYWNrIiAvPjx0ZXh0IHg9IjEwIiB5PSIyMCIgY2xhc3M9ImJhc2UiPk1hY2Ugb2YgU2tpbGw8L3RleHQ+PHRleHQgeD0iMTAiIHk9IjQwIiBjbGFzcz0iYmFzZSI+Ik1hZWxzdHJvbSBXaGlzcGVyIiBTdHVkZGVkIExlYXRoZXIgQXJtb3Igb2YgRW5saWdodGVubWVudDwvdGV4dD48dGV4dCB4PSIxMCIgeT0iNjAiIGNsYXNzPSJiYXNlIj5EaXZpbmUgSG9vZCBvZiBBbmdlcjwvdGV4dD48dGV4dCB4PSIxMCIgeT0iODAiIGNsYXNzPSJiYXNlIj5QbGF0ZWQgQmVsdCBvZiBHaWFudHM8L3RleHQ+PHRleHQgeD0iMTAiIHk9IjEwMCIgY2xhc3M9ImJhc2UiPiJFYWdsZSBCaXRlIiBIYXJkIExlYXRoZXIgQm9vdHMgb2YgRW5saWdodGVubWVudCArMTwvdGV4dD48dGV4dCB4PSIxMCIgeT0iMTIwIiBjbGFzcz0iYmFzZSI+IkVhZ2xlIEdyYXNwIiBIYXJkIExlYXRoZXIgR2xvdmVzIG9mIFJhZ2UgKzE8L3RleHQ+PHRleHQgeD0iMTAiIHk9IjE0MCIgY2xhc3M9ImJhc2UiPk5lY2tsYWNlPC90ZXh0Pjx0ZXh0IHg9IjEwIiB5PSIxNjAiIGNsYXNzPSJiYXNlIj4iR2xvb20gUGVhayIgR29sZCBSaW5nIG9mIEVubGlnaHRlbm1lbnQgKzE8L3RleHQ+PC9zdmc+' | base64 -d > loot2797.svg
```

### 4. View the SVG
Open `loot2797.svg` in a browser or image viewer to confirm the on-chain rendering.

```bash
open file.svg
```

## Review
The full set of terminal commands ran for this example, and the final image you should extract, can be found [in this gist](https://gist.github.com/gumdropsteve/4e838a8962d2dbec0b3e294f8377f076).
